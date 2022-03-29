# Домашняя работа №2. CouchDB + PouchDB

1. Для моей версии Ubuntu нет установочных Apache CouchDB пакетов, поэтому установим CouchDB с помощью Docker Compose по [гайду](https://medevel.com/tutorial-install-couchdb-with-docker/).
2. Скачаем файл ДЗ_2.html.
3. Зайдем на `http://localhost:5984/_utils/#` и создадим БД с названием new-db через веб-интерфейс

![Screenshot from 2022-03-30 00-36-51](https://user-images.githubusercontent.com/60742399/160711538-4003704f-366b-4315-b25c-aacde788ec01.png)
4. Добавим в БД документ с полем «name», в которое запишем "Konopleva"

![Screenshot from 2022-03-30 00-42-20](https://user-images.githubusercontent.com/60742399/160712207-4435dcf5-7ba1-4531-b443-15db7925d122.png)

5. Пропишем в ДЗ_2.html путь к БД 

![Screenshot from 2022-03-30 00-40-08](https://user-images.githubusercontent.com/60742399/160712011-eb2cb88c-c3b5-4710-82ef-99781950460d.png)

6. У меня была проблема с CORS, поэтому нужно поменять настройки 

![Screenshot from 2022-03-30 00-46-16](https://user-images.githubusercontent.com/60742399/160712684-085bc9b7-18f1-4006-b149-cf873ec59f0f.png)

Нажмем sync и увидим "Konopleva". Результат сохранен в `answer.html`

![Screenshot from 2022-03-29 23-44-02](https://user-images.githubusercontent.com/60742399/160713257-f99257fb-fe42-4937-80e7-0d925e0d2bc2.png)

7. Остановим контейнер, обновим ДЗ_2.html, нажмем sync и все равно увидим мою фамилию
