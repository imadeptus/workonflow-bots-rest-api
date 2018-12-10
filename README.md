## Workonflow bot rest api ##

### Введение

**Workonflow** - программный продукт для управления процессами и проектами  на предприятии, взаимодействия сотрудников внутри компании и общения с клиентами и партнерами.

***Основные элементы Workonflow:***

- **стрим** представляет из себя рабочее место для планирования бизнес-процесса, включает в себя различные этапы, которые проходит задача в рамках этого бизнес-процесса.

- **тред** нужен для постановки и описания задач, их обсуждения и совместной работы. Тред может содержать саб-треды - подзадачи, которые созданы для работы участников других бизнес-процессов.

- **комментарий** может быть как внутренним, предназначенным только для участников команды, так и внешним - это сообщения клиентам или партнерам.

- Среди каналов связи есть **email**, **телефония** и **live chat**.

**API** (программный интерфейс приложения, интерфейс прикладного программирования) — набор готовых классов, процедур, функций, структур и констант, предоставляемых приложением (библиотекой, сервисом) или операционной системой для использования во внешних программных продуктах. Используется программистами при написании всевозможных приложений.

**Bot Workonflow API** - это программный интерфейс для разработчиков, позволяющий создавать ботов, которые  взаимодействуют с программным продуктом [Workonflow](workonflow.com), выполняя внутри него определенные алгоритмы.

**Бот** - специальная программа, выполняющая автоматически и/или по заданному расписанию какие-либо действия через интерфейсы, предназначенные для людей.

Боты в Workonflow представляют из себя отдельные приложения, которые пользователи сервиса покупают в маркетплейсе botstore. У каждого бота есть цена месячной подписки, которую определяет разработчик. Боты предназначены для автоматизации рутинных процессов и позволяют кастомизировать Workonflow для каждой отдельной компании. После покупки бот попадает в команду компании и становится доступным для настройки и начала работы.

***Вот лишь небольшой перечень его возможных применений:***

- Создание и настройка бизнес процессов.
- Обновление настроек задач.
- Создание комментариев в потоке или задаче.
- Коммуникации с клиентами или партнерами извне.
- Настройки оповещений.
- Импорта данных из других програмных продуктов и систем для удобного перехода на Workonflow.
- Сбора и анализа данных о работе команды.
- Подключения и интеграции  внешних сервисов.

Обмен сообщениями идёт по обыкновенной HTTP  схеме «запрос-ответ».

**HTTP** — это протокол, не нуждающийся в представлении, ведь даже эта страница передана на ваш компьютер с помощью него ([Wikipedia](https://ru.wikipedia.org/wiki/HTTP)).

Протокол HTTP предполагает использование клиент-серверной структуры передачи данных. Клиентское приложение формирует запрос и отправляет его на сервер, после чего серверное программное обеспечение обрабатывает данный запрос, формирует ответ и передаёт его обратно клиенту.

Мы будем постоянно добавлять новые API-методы, чтобы покрыть все ваши потребности в автоматизации работы [Workonflow](workonflow.com). Если нужный функционал ещё не представлен в API, напишите нам. Наиболее востребованные методы будут реализованы в первую очередь.

---

### Начало работы

Каждый запрос к **API** должен быть авторизован.

Авторизация - это процес подтверждения подлинности запроса и разрешение на его выполнение.

Прежде всего для авторизации запроса требуется пара значений **botId**, **secretkey** и **signature**.

**botId** - это идентификатор для формирования сигнатуры запросов. Получить botId можно на странице создания ботов [console.workonflow.com](https://console.workonflow.com), в разделе настроек запросов. Он уникальный для каждого бота.

**secretkey** - это приватный ключ так-же необходимый для формирования сигнатуры запросов. Получить его можно на главной странице создания ботов [console.workonflow.com](https://console.workonflow.com)

**signature** - это подпись, закодированный параметр, который необходимо передать в заголовке "X-Signature"

**body** - передаваемые параметры запроса в формате [JSON](https://en.wikipedia.org/wiki/JSON).
Пример:
```json
{"threadId":"5c0e5996e43fb4001acfc596"}
```

Для того чтобы создать **signature** вам необходимо создать хэш sha256 в формате Hex, из **botId** + **secretkey** + **timestamp в секундах** + **body**

**Пример создания signature:**
```js
  sha256Hex(botId + secretkey + timestampInSeconds + body)
```

> **Важно!** При **каждом** запросе обязательно необходимо передавать следующие зоголовки:

|Название заголовка| Значение |
|-----|-----|
|X-Bot-Id| botId созданного бота |
|X-Signature| подпись, закодированная алгоритмом SHA256 |
|X-timestamp| таймстамп в секундах |

В случае если параметры не будут переданны или один из ключей не верный, ваш запрос будет неудачен, и вы получите ошибку HTTP 401.

### Общие правила запросов

События и ответы на запросы используют самый популярный формат - [json](https://en.wikipedia.org/wiki/JSON).

Если ваши запросы получают данные, которые можно кэшировать на некоторое время, вам следует их кэшировать, чтобы не запрашивать одни и те же данные многократно.

Чтобы отправить запрос, нужно использовать идентификаторы или [«URI»](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). URL любого API-запроса составляется следующим образом:

```https://botapi.workonflow.com/{teamId}/{URI}/{body}```

где
- *teamId* - идентификатор тимы
- *URI* - универсальный идентификатор ресурса
- *body* - тело запроса в JSON-формате

К примеру, что бы получить информацию по стриму, запрос должен быть следующим:

```https://api.workonflow.com/5b4484384e3e54001e267ed8/stream/read?body={"streamId":"7b4484384e3454001egh7847"}```

### Общие ответы для всех запросов
|Код|Сообщение|
|---|---|
|200|OK|
|400|No valid content. See documentaion or Incorrect teamId|
|401|Not authorized|
|500|Internal server error|

#### Методы передачи параметров запроса

|Метод|Назначение|
|--- |---|
|POST|Получение существующих и добавление новых данных|
-----

### Таблица событий и действий панели Workonflow

|Название| Возможные действия    |  Возможные события          |
|-----|:-----:|:------------:|
| **channel** | [actions](./request/channel.md) |              |
| **comment** | [actions](./request/comment.md) | [events](./events/comment.md)|
| **contact** | [actions](./request/contact.md) | [events](./events/contact.md)|             |
| **file**    | [actions](./request/file.md)    |                              |
| **stream**  | [actions](./request/stream.md)  | [events](./events/stream.md) |
| **team**    | [actions](./request/team.md)    | [events](./events/team.md)   |
| **thread**  | [actions](./request/thread.md)  | [events](./events/thread.md) |
| **call** (In developing)    | [actions](./request/call.md)    | [events](./events/call.md) |
