---
title : "postgresql 설정"
toc : true
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
