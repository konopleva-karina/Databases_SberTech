# Домашняя работа №3. Redis

1. Установим и запустим Redis-server. Для этого выполним
```shell
docker pull redis
docker run --name redis-server -d redis
docker exec -it redis-server redis-cli
```
