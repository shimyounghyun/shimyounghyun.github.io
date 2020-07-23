---
title : "CI&CD 환경 구축"
categories :
    - CI&CD
tag :
    - CI&CD
---

### CI, CD 대략적인 과정

```text
ci  
push / pull-request -> github action job 실행 -> 테스트/리포팅 -> docker build & push

cd  
push -> github action job 실행 -> aws ssh 접속 & 이동 후 미리 작성된 shell script 실행
```

### github action 작성
```yaml
name: CI

on:
  push:
    branches:
      - master

jobs:
  ci:
    name: E2E Tests and Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Check out Git repository
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v1
        with:
          node-version: 12

      - name: Install & Tests
        run: |
          npm install
          npm test
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v1
        with:
          name: codecov-connected-server
          fail_ci_if_error: true
      - name: Docker build
        run: |
          docker build -t syh622/connected-42 .
      - name: Docker push
        run: |
          docker login -u $secrets.DOCKER_USER -p $secrets.DOCKER_PASSWORD
          docker tag syh622/connected-42 syh622/connected-42:latest
          docker tag syh622/connected-42 syh622/connected-42:$github.sha
          docker push syh622/connected-42:latest
          docker push syh622/connected-42:$github.sha
 cd:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    needs: ci

    steps:
      - name: SSH and Build Docker image and Push
        uses: appleboy/ssh-action@master
        with:
          host: $secrets.HOST
          username: $secrets.USERNAME
          key: $secrets.KEY
          port: $secrets.PORT
          script: |
            sh deploy.sh
```
latest로 최신 상태를 업데이트하고, 버전관리를 위해 github.sha 환경변수를 사용했다.
해당 repository에 버전이 2개이상 있을 경우 `docker pull 이미지`의 명령어를 사용할 경우 자동으로 tag에 latest가 붙는다. 
버전이 1개라면 tag를 명시해야한다.

### aws 환경 구성
AWS EC2 ubuntu를 사용했다. root계정을 사용하는 대신 ssh접속이 가능한 계정을 추가로 생성하고 접근 권한을 제한했다. 
해당 과정은 [이곳](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/managing-users.html)에 잘 나와있다.

docker설치 과정은 [이곳](https://velog.io/@wimes/AWS-EC2%EC%97%90-Docker-%EC%84%A4%EC%B9%98-%EB%B0%8F-Dockerfile%EB%A1%9C-%EC%9B%B9%EC%84%9C%EB%B2%84-%EA%B5%AC%EB%8F%99%EC%8B%9C%ED%82%A4%EA%B8%B0)을 참조했다.
추가로 docker-compose, nginx 패키지를 설치한다.

### shell script 작성
blue/green 배포 방식으로 이미 누군가 잘 작성해놓은 것을 조금 수정하여 사용했다.   
docker build완료 후 docker hub에 이미지가 최신화가 되었을 때 실행되는 deploy.sh파일
```shell script
#!/bin/bash

DOCKER_APP_NAME=server
DOCKER_DB_NAME=postgres

EXIST_BLUE=$(docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml ps | grep Up)
EXIST_DB=$(docker-compose -p ${DOCKER_DB_NAME} -f docker-compose.db.yml ps | grep Up)

if [ -z "$EXIST_DB" ]; then
    echo "DB setting"
    docker-compose -p ${DOCKER_DB_NAME} -f docker-compose.db.yml up -d
fi

if [ -z "$EXIST_BLUE" ]; then
    echo "blue up"
    docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml up -d

    sleep 10

    docker-compose -p ${DOCKER_APP_NAME}-green -f docker-compose.green.yml down
    docker image prune -f -a
else
    echo "green up"
    docker-compose -p ${DOCKER_APP_NAME}-green -f docker-compose.green.yml up -d

    sleep 10

    docker-compose -p ${DOCKER_APP_NAME}-blue -f docker-compose.blue.yml down
    docker image prune -f -a
fi
```
    
blue
```shell script
version: '3.7'
services:
  server:
    image: "syh622/connected-42"
    restart: always
    volumes:
      - appdata:/app
    ports:
      - '8081:8080'
    command:
      - bash
      - -c
      - |
        /wait-for-it.sh postgres:5432 -t 30
        yarn start
    networks:
      - app
networks:
  app:
    driver: bridge
volumes:
  appdata:
    driver: local
````
  
green  
```shell script
version: '3.7'
services:
  server:
    image: "syh622/connected-42"
    restart: always
    volumes:
      - appdata:/app
    ports:
      - '8080:8080'
    command:
      - bash
      - -c
      - |
        /wait-for-it.sh postgres:5432 -t 30
        yarn start
    networks:
      - app
networks:
  app:
    driver: bridge
volumes:
  appdata:
    driver: local
```
서로 다른 포트를 가지도록 8080, 8081를 각각 사용했고, db 컨테이너가 정상 동작이 완료된 상태에서 서버를 시작해야 하기때문에
[wait-for-it.sh](https://raw.githubusercontent.com/vishnubob/wait-for-it/master/wait-for-it.sh)를 사용했다.
  
db
```shell script
version: '3.7'

services:
  postgres:
    image: postgres:10
    container_name: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=${DB_ROOT_PASSWORD}
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_DB=${DB_NAME}
    ports:
      - '5432:5432'
    networks:
      - app
networks:
  app:
    driver: bridge
volumes:
  pgdata:
    driver: local
```

### nginx 설정
deploy.sh를 실행하면 *-blue, *-green이미지가 번갈아가며 실행되는것을 확인할 수 있다. 이때 실행되는 서버는 8080, 8081포트를 가지고있다.  
클라이언트가 브라우저에서 동일한 포트로 접근할 수 있도록 중계서버를 이용한다. nginx의 reverse proxy를 구성하면 가능하다.
해당 설명은 [이곳](https://medium.com/sjk5766/nginx-reverse-proxy-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-e11e18fcf843)에 자세히 나와있다.

nginx 설정 파일 /etc/nginx/sites-available/default
```text
upstream backend {
        server localhost:8081 weight=5 max_fails=3 fail_timeout=10s;
        server localhost:8080 weight=10 max_fails=3 fail_timeout=10s;
}

server {
        listen 80;
        server_name localhost;
        location / {
                proxy_pass http://backend;
        }
}
```

### 참조
https://smjeon.dev/web/deploy-with-jenkins-4/#nginxconf
https://github.com/JHyeok/jaebook-server