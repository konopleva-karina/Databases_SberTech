# Домашняя работа №1. MongoDB

1. Устанавливаем MongoDB и стартуем mongod. Подключаемся:

![Screenshot from 2022-03-08 17-09-46](https://user-images.githubusercontent.com/60742399/157254475-e1e8b5b1-5609-4e40-a071-7ed76f79fccd.png)

Можем проверить, что сервер активен: 

![Screenshot from 2022-03-08 13-51-11](https://user-images.githubusercontent.com/60742399/157253428-7999c24e-ed40-4b1b-af69-235dfdb4cea8.png)

2. Изначально база данных пуста: 

![Screenshot from 2022-03-08 16-33-46](https://user-images.githubusercontent.com/60742399/157253489-35da014f-6ded-410c-a7fb-4080b0ac90e3.png)

Скачаем [датасет о списком научных статей и их краткими описаниями](https://www.kaggle.com/Cornell-University/arxiv?select=arxiv-metadata-oai-snapshot.json). Заполним базу данными: 

![Screenshot from 2022-03-08 16-36-27](https://user-images.githubusercontent.com/60742399/157253997-708566b3-5fff-44b6-8120-6a9ac10fe305.png)

Теперь увидим, что в ней 166000 документов:

![Screenshot from 2022-03-08 16-36-55](https://user-images.githubusercontent.com/60742399/157254085-7e1c1936-ecd7-456b-b323-958a8fe196a2.png)

3. Посмотрим на книги из категории "math.NT": 

![Screenshot from 2022-03-08 16-38-26](https://user-images.githubusercontent.com/60742399/157254646-09aa5dcb-1e08-4842-9756-f74f759319b6.png)

Посмотрим на производительность этого запроса (без limit'a). Выполним данную команду несколько раз: 

![Screenshot from 2022-03-08 16-39-09](https://user-images.githubusercontent.com/60742399/157254767-2913166a-ba03-4b5e-9f3e-6386ac954bdb.png)
![Screenshot from 2022-03-08 16-45-34](https://user-images.githubusercontent.com/60742399/157256167-979eb8cf-56d1-4a5d-a40e-18896cba54ab.png)

Посмотрим на статьи авторов Christian Stahn и Dohoon Choi:
![Screenshot from 2022-03-08 16-40-05](https://user-images.githubusercontent.com/60742399/157254803-a0187ee9-eaec-4189-b2d8-79d80ebccacb.png)

Производительность запроса без индексов: 

![Screenshot from 2022-03-08 16-45-34](https://user-images.githubusercontent.com/60742399/157255907-cc748c31-3dd0-4622-b828-5c9e13688b92.png)

Также мы можем обновить уже существующие документы. В случае, если запросу не удовлетворяет ни один документ, создадим таковой:

![Screenshot from 2022-03-08 16-41-56](https://user-images.githubusercontent.com/60742399/157255674-9108f86a-8eac-4797-b9d9-756f27a8c682.png)

Можно удалить документ: 

![Screenshot from 2022-03-08 16-44-14](https://user-images.githubusercontent.com/60742399/157255751-222cd168-454b-4311-8ff5-81dd1a86fee5.png)

Проверим, что статьи с моим авторством больше нет в БД: 

![Screenshot from 2022-03-08 16-44-24](https://user-images.githubusercontent.com/60742399/157255852-c3cba181-1171-4cbc-a8a8-922118a7b474.png)

4. Создадим индесы по категориям и заново выполним запрос из пункта 2 по поиску определенной категории:

![Screenshot from 2022-03-08 16-48-51](https://user-images.githubusercontent.com/60742399/157256489-1145ed39-de56-4922-a3cd-3fec07f0b7ac.png)

Создадим индекс по авторам статьи:

![Screenshot from 2022-03-08 16-55-21](https://user-images.githubusercontent.com/60742399/157256655-69f77e88-248b-4f80-84de-de6a7265e43f.png)


