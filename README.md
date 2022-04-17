# Домашняя работа №3. Redis


### Установка

Установим и запустим Redis-server. Для этого выполним
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

### Генерируем данные

Для генерации большого JSON'а мы сгенерируем большие строки (~100000 байт) и будем вставлять их большое число раз. А для начала напишем небольшой код на Python.

```python
import random
import string


# Генерирует длинную строку заданной длины и записывает её в text.txt. Будем использовать для SET.
def generate_long_string(length=100000):
    with open('text.txt', 'w') as f:
        letters = string.ascii_lowercase
        rand_string = ''.join(random.choice(letters) for i in range(length))
        f.write(rand_string)


# Генерирует строку '1 <слово длины 1> 2 <слово длины 2> 3 <слово длины 3> ...' и записывает её в filename, length - максимальная длина слова
def generate_data(filename, length):
    with open(filename, 'w') as f:
        letters = string.ascii_lowercase
        rand_string = ""
        for i in range(1, length):
            rand_string += str(i) + ' "' + ''.join(random.choice(letters) for _ in range(i)) + '" '
        f.write(rand_string)

# Строка для одной операции ZADD/HSET __key__ <data>
def generate_long_hset_data():
    generate_data('hset_data.txt', 450)


# Строка для одной операции LPUSH/RPUSH __key__ <data>
def generate_long_list_data():
    generate_data('list_data.txt', 100)


if __name__ == '__main__':
    generate_long_string()
    generate_long_hset_data()
    generate_long_list_data()
```

В итоге получим три файла
* [text.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502459/text.txt)
* [hset_data.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502460/hset_data.txt)
* [list_data.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502461/list_data.txt)

Они нужного нам размера

![Screenshot from 2022-04-18 00-15-49](https://user-images.githubusercontent.com/60742399/163732283-a505d8b3-7655-4a35-80eb-032cbb5742dd.png)

Тестировать скорость записи и чтения будем при помощи [memtier_benchmark](https://github.com/RedisLabs/memtier_benchmark). Устанавливаем его [по гайду](https://www.digitalocean.com/community/tutorials/how-to-perform-redis-benchmark-tests). Переносим в папку сгенерированные нами файлы со строками. Далее будем запускать memtier_benchmark c параметрами

* -s - Server address
* --key-minimum - Key ID minimum value
* --key-maximum - Key ID maximum value
* --requests=allkeys - Number of total requests per client
* --command - Command to send. "\_\_key\_\_" means using key generated from Key Options

### Результаты 
#### Запись
* __SET__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=10000  --requests=allkeys --command='set __key__ <содержимое text.txt>'
```

_Benchmark_ 

```1999800 ops,   11126 (avg:   11158) ops/sec, 1.04GB/sec (avg: 1.04GB/sec), 18.00 (avg: 17.92) msec```

_Full output_ [set_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502404/set_results.txt)

* __HSET__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=10000  --requests=allkeys --command='hset __key__ <содержимое hset_data.txt>'
```

_Benchmark_ 

```199800 ops,    2442 (avg:    2453) ops/sec, 252.70MB/sec (avg: 253.82MB/sec), 82.20 (avg: 81.51) msec latency```

_Full output_ [hset_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502410/hset_results.txt)

* __ZADD__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=10000  --requests=allkeys --command='hset __key__ <содержимое hset_data.txt>'
```

_Benchmark_ 

```199800 ops,    2179 (avg:    2204) ops/sec, 225.43MB/sec (avg: 228.07MB/sec), 92.39 (avg: 90.71) msec latency```

_Full output_ [zadd_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502411/zadd_results.txt)

_Замечание_ Мне не хватило оперативки, чтобы вставить с помощью `LPUSH` и `RPUSH` столько же пар ключ-значение, что и в `ZADD` и `HSET`, и сервер переставал отвечать. Поэтому операции `LPUSH`, `RPUSH`, `LRANGE` будут всего по 100 ключам.

* __LPUSH__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=100  --requests=allkeys --command='lpush __key__ <содержимое list_data.txt>'
```

_Benchmark_ 

```19800 ops,    1360 (avg:    1378) ops/sec, 172.68MB/sec (avg: 174.88MB/sec), 147.09 (avg: 145.11) msec latency```

_Full output_ [lpush_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502414/lpush_results.txt)

* __RPUSH__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=100  --requests=allkeys --command='rpush __key__ <содержимое list_data.txt>'
```

_Benchmark_ 

```19800 ops,    1511 (avg:    1567) ops/sec, 191.84MB/sec (avg: 198.85MB/sec), 130.92 (avg: 127.62) msec latency```

_Full output_ [rpush_requests.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502417/rpush_requests.txt)

#### Чтение
* __GET__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=1000  --requests=allkeys --command='get __key__'
```

_Benchmark_ 

```199800 ops,   14392 (avg:   14083) ops/sec, 1.34GB/sec (avg: 1.31GB/sec), 13.90 (avg: 14.19) msec latency```

_Full output_ [get_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502398/get_results.txt)

* __HGETALL__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=1000  --requests=allkeys --command='hgetall __key__'
```

_Benchmark_ 

```199800 ops,    3904 (avg:    3985) ops/sec, 403.94MB/sec (avg: 412.32MB/sec), 50.92 (avg: 50.17) msec latency```

_Full output_ [hgetall_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502393/hgetall_results.txt)

* __ZRANGEBYSCORE__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=1000  --requests=allkeys --command='ZRANGEBYSCORE __key__ 1 499'
```

_Benchmark_ 

```199800 ops,    6353 (avg:    6318) ops/sec, 633.58MB/sec (avg: 630.14MB/sec), 31.55 (avg: 31.65) msec latency```

_Full output_ [zrangbyscore_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502388/zrangbyscore_results.txt)

* __LRANGE__

_Command_ 

```bash
./memtier_benchmark -s 172.17.0.2 --key-minimum=1 --key-maximum=100  --requests=allkeys --command='lrange __key__ 1 499'
```

_Benchmark_ 

```199800 ops,   55328 (avg:   47859) ops/sec, 131.87MB/sec (avg: 146.42MB/sec),  3.62 (avg:  4.18) msec latency```

_Full output_ [lrange_results.txt](https://github.com/konopleva-karina/Databases_SberTech/files/8502380/lrange_results.txt)

### Выводы
Хуже всего при большом json'е показал себя `LPUSH`(avg 1378 ops/sec), `RPUSH` (avg 1567 ops/sec) немного производительнее `LPUSH'а`, но его тоже лучше не использовать в нашем случае. Самой быстрой оказалась работа со строками (для `SET` 11158 ops/sec avg). HSET (avg 2453 ops/sec) отработал несильно, но быстрее, чем ZADD (avg 2204 ops/sec).

Про чтение. Не будем учитывать `LRANGE`, потому что он читал очень мало данных. С учетом этого `GET` отработал быстрее всех. Чтение происходит быстрее, чем запись.
