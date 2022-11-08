# InnoTaxi

InnoTaxi - приложение для заказа такси. 


Функциональные требования: 

Taxi types:
1) Economy
2) Comfort
3) Business

Роли:
1) User
2) Driver
3) Analyst

User:
1) User может зарегистрироваться. 
    - Поля для регистрации: name, phone number, email, password.
2) User может войти в систему.
    - Поля для входа: phone number, password.
3) User может выйти из системы (выданный ему токен должен перестать приниматься системой, даже если все еще валиден по времени).
4) User может заказать такси. 
    - Поля заказа: taxi type, from, to.
    - При заказе такси ищется свободный (в статусе free) водитель и, если он найден, создается order со статусом in progress. Статус водителя меняется на busy.
    - Если свободного водителя не найдено, пользователь остается в состоянии поиска водителя в течении некоторого(настраиваемого) времени. Если за это время водитель не находится, пользователю возвращается ответ с отказом.
    - При отсутствии свободных водителей из ожидающих пользователей образуется очередь. Первый заказавший такси пользователей должен первым получить водителя или сообщение, что водитель не найден.
5) User может оценить поездку. Оценить можно только последнюю поездку, если с нее прошло менее некоторого(настраиваемого) времени. Оценка от 1 до 5 включительно.
6) User может просмотреть свои поездки (taxi type, driver, from, to).
7) User может получить свой профиль (name, phone number, email, rating).
8) User может изменить поля в профиле (name, phone number, email).
9) User может удалить свой профиль (из бд не удаляется, помечается как удаленный).

Driver:
1) Driver может зарегистрироваться. 
    - Поля для регистрации: name, phone number, email, password, taxi type.
2) Driver моджет войти в систему.
    - Поля для входа: phone number, password.
3) Driver может сменить свой статус с busy на free, когда поездка закончена. При этом статус order'a меняется на finished.
4) Driver может оценить поездку. Оценить можно только последнюю поездку, если с нее прошло менее некоторого(настраиваемого) времени. Оценка от 1 до 5 включительно.
5) Driver может узнать свой общий рейтинг.
6) Driver может просмотреть свои поездки (taxi type, user, from to).
7) У Driver могут быть статусы busy и free.

Analyst:
1) Analyst может смотреть статистику заказов(количество по дням, месяцам).
2) Analyst может смотреть рейтинг всех водителей.
3) Analyst может смотреть рейтинг всех пользователей.
4) Analyst имеет заранее созданный в системе аккаунт, логин происходит по username, password.

Order: 
1) У Order могут быть статусы in progress и finished.
2) Поля Order: user, driver, from, to, taxi type, date, status.

Схема приложения: 

<img src="Design diagram-v2.png" width="600" height="500" /> 


Сервис аналитики: все регистрации и выполненные заказы должны записываться в аналитическую базу. 

Допускается добавление новых сервисов, уменьшать количество сервисов не допускается. При изменении схемы(добавление новых сервисов) приложения необходимо дополнить диаграмму и выложить себе в репозиторий в .png и .drawio форматах.

Нефункциональные требования:

1) Общее:
  - При работе на windows пройти (с созданием задачи в jira) [курс](https://ru.hexlet.io/courses/cli-basics).
  - Все создаваемые репозитории должны быть приватными.
  - Работа по GitHub Flow
    - Две основных ветки (master, dev)
    - Ответвляемся всегда от dev в feature-ветки
    - Делаем работу в них и пушим
    - Делаем pull request в dev
    - Когда сделали pull request, пишем ментору, приступаем к новой задаче не дожидаясь ревью
    - Если ментор оставит комментарии на pr, исправляем и заливаем изменения
    - Если ментор аппрувит pr то заливаем его в dev
  
2) User service:
  - Gin в качестве HTTP фреймворка.
  - PostgreSQL в качестве БД.
  - MongoDB в качестве БД (сбор логов).
  - Redis в качестве кеша.
  - Prometheus и Grafana в качестве сбора метрик.
  - Должны быть добавлены pprof хендлеры.
  - Swagger генерируется из аннотаций в коде.

3) Driver service:
  - Сначала описывается swagger, потом из него генерируется код слоя хендлеров.
  - Cassandra в качестве БД.

4) Order service:
  - GraphQL для эндпоинтов вместо REST  
  - Elasctic search для order service (возможность поиска по from или to созданных поездок).

5) Analyst service:
  - Echo в качестве HTTP фреймворка.
  - ClickHouse в качестве БД.

6) General:
  - Kafka в качестве message broker.
  - Для RPC взаимодействия использовать GRPC.
  - Для каждого сервиса должен быть описан Dockerfile.
  - Поднятие всего приложения осуществлять через docker-compose. 
  - Все изменяемые переменные(подключение к бд, время ожидания юзером свободного водителя) должны устанавливаться через environment variables.
  - Каждый сервис должен иметь README(описано, что необходимо для запуска, environment variables) и swagger(подробно описаны все эндпоинты). 
  - Все сервисы должны быть покрыты интеграционными тестами.
  - Для каждого сервиса должен быть настроен CI/CD на GitHub actions со степами: 1) тесты, 2) линтер.
  - Аутентификация с использованием JWT.

    Go:
  - Для работы с Postgres допустимо использовать чистые запросы или sqlc, sqlx. Использование ORM не допускается.
  - Для реализации ожидания юзером свободных водителей использовать горутины и каналы, при необходимости пакеты sync, x/sync.
  - Для миграций использовать Goose или go-migrate.
  - По структуре проекта посматривать [сюда](https://github.com/golang-standards/project-layout).
  - Для тестирования использовать табличные тесты, gomock, testify/suite, ginkgo, gomega.

    Rust:
  - Rocket или Actix-web для работы с HTTP. 
  - Tokio для асинхронных операций.
  - Diesel для миграций и работы с БД.
