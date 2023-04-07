# InnoTaxi

 Все создаваемые репозитории должны быть приватными. Они попадают под подписанный NDA (делать публичными не допускается никогда). 

InnoTaxi - приложение для заказа такси. 
В приложении должны быть реализованы:

3 роли пользователей: 
1) User;
2) Driver;
3) Analyst.

2 типа кошелька:
Personal
Family

3 типа такси:
1) Economy;
2) Comfort;
3) Business.

Приложение должно быть построено на основе микросервисной архитектуры, с использованием подхода чистой архитектуры. Необходимо создать 4 микросервиса:
1) User Service;
2) Driver Service;
3) Order Service;
4) Analytic Service.
Подробные требования к микросервисам описаны ниже.

Функциональные требования:

User Service:
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
User опционально может оставить комментарий к поездке.
6) User может просмотреть свои поездки (taxi type, driver, from, to).
7) User может получить свой профиль (name, phone number, email, rating).
8) User может изменить поля в профиле (name, phone number, email).
9) User может удалить свой профиль (из бд не удаляется, помечается как удаленный).
10) User должен иметь счет, также должна быть возможность создать “семейный” счет (для нескольких User), семейный счет должен быть привязан к персональному счету пользователя, пользователь может добавлять других User к семейному счету по номеру телефона. 
11) Если у User привязано несколько счетов, он может выбирать с какого будут списываться средства.
12) User может пополнять свои счета. Family счет может пополнять только владелец.
13) User может просматривать все транзакции по счету. Если это семейный счет, то только владелец счета имеет доступ к списку транзакций.
14) User не может заказать такси, если цена превышает кол-во денег на счете.

Driver Service:
1) Driver может зарегистрироваться. 
    - Поля для регистрации: name, phone number, email, password, taxi type.
2) Driver может войти в систему.
    - Поля для входа: phone number, password.
3) Driver может сменить свой статус с busy на free, когда поездка закончена. При этом статус order'a меняется на finished.
4) Driver может оценить поездку. Оценить можно только последнюю поездку, если с нее прошло менее некоторого(настраиваемого) времени. Оценка от 1 до 5 включительно.
5) Driver может узнать свой рейтинг. Рейтинг формируется на основе последних 20 оценок от юзеров.
6) Driver может просмотреть свои поездки (taxi type, user, from to).
7) У Driver могут быть статусы busy и free.

Order Service: 
1) Order Service выступает в качестве оркестратора для User и Driver сервисов для создания заказов. 
2) Можно получить список заказов. По опциональным параметрам (может быть любое поле заказа). 
3) Поля Order: user, driver, from, to, taxi type, date, status, comment.
4) У Order могут быть статусы in progress и finished.
5) Analyst имеет возможность поиска по списку заказов. 
6) Order Service хранит в себе цены для каждого из taxi type. 


Analytic Service:
1) Analyst может смотреть статистику заказов(количество по дням, месяцам).
2) Analyst может смотреть рейтинг всех водителей.
3) Analyst может смотреть рейтинг всех пользователей.
4) Analyst имеет заранее созданный в системе аккаунт, логин происходит по username, password.
5) Все регистрации и выполненные заказы должны записываться в аналитическую базу. 



Схема приложения: 

<img src="Design-diagram-v3.png" width="600" height="500" /> 

Допускается добавление новых сервисов, уменьшать количество сервисов не допускается. При изменении схемы(добавление новых сервисов) приложения необходимо дополнить диаграмму и выложить себе в репозиторий в .png и .drawio форматах.

Нефункциональные требования:

1) Общее:
  - Работа по GitHub Flow.
  - Две основных ветки (main, dev).
  - Main выступает в качестве релизной ветки. Сюда должны заливаться только стабильные версии (с реализованным функционалом).
  - При добавление новой фичи, рефакторинге или фиксинге багов необходимо отбренчиваться от Dev ветки и создавать новую.
  - Делаем работу в созданных ветках и создаем Pull Request в Dev-ветку.
  - Когда сделали pull request, пишем ментору, приступаем к новой задаче не дожидаясь ревью. Работаем по Jira флоу, описанному в Onboarding тикете.  
  - Если ментор оставит комментарии на pr, исправляем и заливаем изменения
  - Если ментор аппрувит pr то мерджим его в dev. Перед мерджем в Dev необходимо сделать Squash коммитов.

2) User service:
  - PostgreSQL в качестве БД.
  - Таблицы Users, Trips, Wallets, Transactions. 
  - Wallets имеет MnM связь с Users. В Wallets есть колонка Type, которая является Enum. Может принимать значения Personal и Family.
  - В PostgreSQL должны храниться последние 20 заказов для каждого пользователя. При превышении количества > 20 записей неактуальные чистятся триггером.
  - На основе 20 оценок с последних заказов у пользователя формируется средний рейтинг.
  - Таблица со всеми транзакциями по списанию баланса. Статусы транзакции: create, blocked, success and canceled. При создании пользователем заказа ему присваивается статус **create**, если баланс пользователя >= стоимости поездки статус меняется на **blocked**, в ином случае на **canceled**. По завершению поездки статус переводится в **success**. 
  - Redis в качестве кеша. Реализовать хранение токена пользователя и logout (удаление токена).
  - Prometheus и Grafana для сбора метрик.
  - Swagger генерируется из кода приложения.
  - Front-end: Использовать Vue.js (3.0) composition API. Создать форму регистрации, авторизации, просмотр профиля, изменение полей в профиле, удаление профиля. Использовать компоненты, Pinia. 
  - VCS: GitHub; CI/CD: Github Actions.

3) Driver service:
  - Код транспортного слоя генерируется из Swagger описания. (Не пишется руками HTTP взаимодействие).
  - MongoDB в качестве БД.
  - В базе хранится: информация о Driver (name, phone number, email, password, taxi type), информация о последних 20 заказах, рейтинг водителя в виде массива, баланс водителя (заработок). Когда длина массива с заказами становится >20 необходимо удалять неактуальные.
  - Агрегация рейтинга драйвера на основе 20 последних оценок. 
  - Должны быть добавлены pprof хендлеры (и использованы). Или другой тип профилирования, если используется не GO.
  - Front-end: Использовать Angular (latest), создать форму регистрации, авторизации и просмотр профиля, изменение полей в профиле, удаление профиля.
  - VCS: Gitlab; CI/CD: Gitlab CI/CD;

4) Order service:
  - GraphQL в качестве транспортного слоя.
  - Эндпоинт для поиска по полям: DriverID, UserID, from, to, fromDate, toDate. Реализовать частичный поиск (когда некоторые поля не указаны - должны игнорироваться).
  - Добавить пагинацию для данного эндпоинта. Поля offset; limit.
  - Elasticsearch для order service (возможность поиска по from или to созданных поездок). Поиск по префиксу, full text search по комментариям к поездкам, добавить возможность поиска с транслитерацией и лексическими ошибками. 
- Для реализации ожидания юзером свободных водителей использовать многопоточное программирование. 
- Доп задание: реализовать возможность подбора водителей исходя из рейтинга пользователя.
 - Front-end: Использовать React + redux/redux toolkit. Создать main page (фронт к GQL эндпоинтам) с фильтрами (from - to; fromDate - toDate; User name; Driver name; taxi type etc.).
 - VCS: BitBucket; CI/CD: Bitbucket pipelines.

5) Analyst service:
  - ClickHouse в качестве БД.
  - Читает из кафки события и пишет в ClickHouse.
  - VCS: Github; CI/CD: Circle CI.

Technical requirements:
  - Kafka в качестве message broker.
  - Для RPC взаимодействия использовать GRPC.
  - Для каждого сервиса должен быть описан Dockerfile.
  - Поднятие всего приложения осуществлять через docker-compose. 
  - Все изменяемые переменные (подключение к бд, время ожидания юзером свободного водителя) должны устанавливаться через environment variables.
  - Каждый сервис должен иметь README (описано, что необходимо для запуска, environment variables) и swagger (подробно описаны все эндпоинты). 
  - Все сервисы должны быть покрыты интеграционными тестами.
  - Для каждого сервиса должен быть настроен CI/CD со степами: 1) тесты, 2) линтер, 3) линтер для протофайлов, 4) vulncheck, 5) билд образа и залив на свой dockerHub (в мастер ветке).  
  - Аутентификация с использованием JWT.

    Go:
  - UserService: Gin в качетсве HTTP-библиотеки. Analyst: Fiber.
  - Использовать golangci-lint
  - Для работы с Postgres допустимо использовать чистые запросы или sqlc, sqlx. Использование ORM не допускается.
  - Для реализации ожидания юзером свободных водителей использовать горутины и каналы, при необходимости пакеты sync, x/sync.
  - Для миграций использовать Goose или go-migrate.
  - Style guidlines [сюда](https://rakyll.org/style-packages/)
  - Для тестирования использовать табличные тесты, gomock, testify/suite, ginkgo, gomega.
