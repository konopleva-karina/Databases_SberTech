# Домашняя работа №3. Redis

0. Установим и запустим Redis-server. Для этого выполним
```shell
docker pull redis
docker run --name redis-server -d redis
docker exec -it redis-server redis-cli
```
Мы увидим CLI

![Screenshot from 2022-04-17 14-43-58](https://user-images.githubusercontent.com/60742399/163713339-92ba785f-38c1-4495-b171-d68304cb0031.png)

Установим и запустим десктопный менеджер Redisinsight. Для этого на Ubuntu 21.04 выполним
```shell
wget https://download.redisinsight.redis.com/latest/RedisInsight-v2-linux-x86_64.AppImage
chmod a+x RedisInsight-v2-linux-x86_64.AppImage
./RedisInsight-v2-linux-x86_64.AppImage
```

Я подключилась к серверу по адресу 172.17.0.2:6379, назвала новую БД myDB. Это выглядит вот так

![Screenshot from 2022-04-17 14-54-02](https://user-images.githubusercontent.com/60742399/163713263-c6b62ec6-cc64-48f2-8cae-b911e1a15f85.png)

Команды set/get работают

![Screenshot from 2022-04-17 14-56-00](https://user-images.githubusercontent.com/60742399/163713311-6aa8f18c-691f-41b4-9ea3-5847e908fa2e.png)

1. 
