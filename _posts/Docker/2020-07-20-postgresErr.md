---
title : "postgresql 설정"
categories :
    - Docker
tag :
    - Docker
    - postgresql
---

### docker-compose 설정

docker-compose.yml 파일

```yaml
version: '3.7'
services:
  postgres:
    image: postgres:10
    container_name: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_USER=user
      - POSTGRES_DB=db
    ports:
      - '5432:5432'
volumes:
  pgdata:
    driver: local

```

```bash
docker-compose up
```

```bash
docker exec -it [이름] bash
```

`psql -U postgres`의 경우 default설정으로 `role "postgres" does not exist`에러를 만난다. 
envrionment에 명시한 user, db명으로 접속이 가능하다.
  
```bash
psql --username user --dbname db
```

### 접속 실패

`PostgreSQL Database directory appears to contain a database; Skipping initialization`라는 메세지를 보내고
설정한 유저, DB, 비밀번호가 동작하지 않는 경우가 발생했다.

docker ps 종료, docker image 삭제, docker volume 삭제 후 다시 compose하여 해결했다.

```shell script
docker ps -a
docker rm [아이디]

docker volume ls
docker volume rm [아이디]
```
