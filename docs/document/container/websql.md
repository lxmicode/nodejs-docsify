# websql web数据库管理工具

## docker部署
```docker
# docker compose
version: '3'
services:
  websql:
    image: cgycms/websql:latest
    container_name: websql
    ports:
      - "13306:80"
    volumes:
      - ${pwd}/data:/websql/data
    stdin_open: true
    tty: true
    restart: unless-stopped
```
