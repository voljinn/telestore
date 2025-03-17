---
sidebar_position: 3
---

# Тестовое приложение 

## Интро

Это приложение сделано для того, чтобы разработчикам было проще разобраться в работе SDK TeleStore на примерах, а также для ознакомления, как работать с SDK приложениям без серверной части. 

Приложение состоит из двух частей: клиентской (Client-Side) и серверной (Server-Side). В вашем приложении может не быть серверной части, также некоторые процессы могут отличаться в зависимости от вашей реализации.

Документация также доступна на [GitHub на английском языке](https://github.com/telestore-rep/test-app).

<a href="install"></a>
## Установка
```
sudo git clone https://github.com/telestore-rep/test-app telestore_test_app
```

Для запуска приложения используйте [Docker](https://www.docker.com).

## Клиентская сторона (Client-Side)

### Получение информации о пользователе без авторизации приложения

Чтобы получить информацию о пользователе, перенаправьте его по следующему URL-адресу TeleStore:
```
https://web.tele.store/redirect_ext_auth.html?get_user_info=${APP_URL_ID}
```

где `APP_URL_ID` — это уникальный идентификатор URL вашего приложения, зарегистрированный в TeleStore (Профиль ➡ Безопасность ➡ URL приложений).

[Пример перенаправления пользователя](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L152)

После получения данных TeleStore перенаправляет пользователя обратно в ваше приложение по следующему URL:
```
{YOUR_APP_URL}/?usr={USER_INFO}&check={CHECK_HASH}
```
Где:
- `YOUR_APP_URL` — URL вашего приложения
- `USER_INFO` — строка, закодированная в формате base64, содержащая объект с данными пользователя
- `CHECK_HASH` — хэш, используемый для валидации данных

Параметр `usr` содержит строку, закодированную в формате base64, с сериализованным объектом JSON:
```
usr=eyJpZCI6MTczMDIzMDQwMTgwOTIyOSwiZmlyc3RfbiI6IkZpcnN0bmFtZSIsInNlY29uZF9uIjoiTGFzdG5hbWUiLCJ1dGMiOiIyMDI1LTAyLTA0VDEzOjUyOjQ1LjYzNDk3WiJ9
```

```javascript
{
    "id": 1730230401809229, // ID клиента в TeleStore
    "first_n": "First Name", // имя
    "second_n": "Last Name", // фамилия
    "utc": "2025-02-04T13:52:45.63497Z" // метка времени
}
```

Параметр `check` содержит хеш HMACSHA256 с открытым ключом разработчика и данными пользователя в виде JSON-строки (кодировка UTF-8):
```javascript
check=ehcX3ApkjpyV42SaeFRALNQ17S7Q0ksitgcXSWRTd3A
```

### Валидация данных

Рекомендуется проверять подпись, чтобы гарантировать беспрепятственную связь. Валидацию можно выполнять как на серверной стороне, так и на клиентской (что не рекомендуется, так как вам придется хранить открытый ключ на клиентской стороне).

Для примера, вот фрагмент кода из JavaScript-реализации процесса на стороне TeleStore:
```javascript
/** Получить информацию о пользователе */
let userData = JSON.parse(atob(userEncoded));

/** Валидация информации о пользователе на серверной стороне */
const publicKeyBytes = Buffer.from(PUBLIC_USER_KEY, "hex");
const userDataUtf8Bytes = Buffer.from(JSON.stringify(userData), "utf-8");
const sha256Hash = crypto.createHash("sha256").update(publicKeyBytes).digest();
const hmacHash = crypto.createHmac("sha256", sha256Hash).update(userDataUtf8Bytes).digest();
const checkStrBase64 = toBase64Url(hmacHash);
```

Где `PUBLIC_USER_KEY` — это открытый ключ вашей учетной записи разработчика.

Если подпись верна и метка времени не слишком старая, значит, разработчик получил действительные данные пользователя TeleStore.

#### Валидация на серверной стороне

Вы можете выполнять валидацию полученных данных на своем сервере, отправляя данные из своего клиентского приложения и затем выполняя необходимые шаги по валидации.

[Пример валидации на серверной стороне](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/validate_usr_info/route.ts#L21)

#### Валидация на клиентской стороне

Мы не рекомендуем этот метод валидации, так как он требует хранения открытого ключа вашего приложения на клиентской стороне, что может снизить безопасность вашего приложения.

[Пример валидации на клиентской стороне](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L159)

### Авторизация пользователя через TeleStore (Клиентская сессия)

Если ваше приложение не имеет серверной части, вы можете авторизовать пользователя через TeleStore. Это позволит вам выполнять несколько авторизованных действий, таких как получение информации о пользователе, проверка их баланса, перевод на счет вашего приложения, сохранение или загрузка данных приложения.  

#### Авторизация через перенаправление (редирект)

Вы можете авторизовать пользователя, перенаправив его на следующий URL:  
```
https://web.tele.store/redirect_ext_auth.html?auth_app=${APP_URL_ID}
```

Где `APP_URL_ID` — это идентификатор URL вашего приложения, зарегистрированного в TeleStore (Профиль ➡ Безопасность ➡ URL приложений).

После успешной авторизации пользователь перенаправляется на следующий URL:  
```
{YOUR_APP_URL}/?auth_code={AUTH_CODE}
```


Впоследствии `auth_code` может быть использован для создания новой сессии.  

[Пример авторизации через редирект](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L200)

#### Авторизация через всплывающее окно

Вы можете авторизовать пользователя, открыв URL в окне всплывающего окна:  
```
https://web.tele.store/redirect_ext_auth.html?auth_app=${APP_URL_ID}&popup=true
```

Где: 
- `APP_URL_ID` — идентификатор URL вашего приложения, зарегистрированного в TeleStore (Профиль ➡ Безопасность ➡ URL приложений).
- `popup=true` — обязательный параметр для успешной аутентификации.  

[Пример авторизации через всплывающее окно](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L207)

### Получение информации о пользователе

Чтобы получить информацию о пользователе, отправьте авторизованный запрос на:  
```
GET https://web.tele.store/appauth/v1/get_teleuser_details
```

[Пример получения данных пользователя](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L125)

### Получение баланса пользователя

Чтобы получить баланс пользователя, отправьте авторизованный запрос на:  
```
GET https://web.tele.store/appauth/v1/get_balance
```

[Пример получения баланса пользователя](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L180)

### Перевод средств на счет разработчика

Чтобы перевести средства с аккаунта пользователя на ваш счет, отправьте авторизованный запрос на:  

```
POST https://web.tele.store/appauth/v1/make_payment  
BODY {  
    "amount": 10, // Сумма транзакции  
    "currency": "TeleUSD",  
    "tag": "Любое описание" // Описание транзакции  
}
```

[Пример перевода на счет разработчика](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L232)

### Сохранение данных приложения

Вы можете сохранить любую необходимую информацию приложения, используя авторизованный запрос на:  
```
POST https://web.tele.store/appauth/v1/save_app_user_data  
BODY {  
    <любые JSON-данные> 
}
```

[Пример сохранения данных приложения](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L261)  

### Получение данных приложения

Чтобы получить данные приложения, отправьте авторизованный запрос на:  
```
GET https://web.tele.store/appauth/v1/list_app_user_data
```

[Пример получения данных приложения](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/client/page.tsx#L281)

## Серверная сторона (Server-Side)

### TeleStore SDK

Для упрощения разработки мы рекомендуем использовать [официальный SDK TeleStore](https://github.com/telestore-rep/SDK). Он позволяет получать новые сессии, создавать инвойсы, извлекать историю транзакций и обновления SSE и многое другое.

### Создание новой сессии

[//]: # (Для создания новой сессии вам необходимо сгенерировать новый Ключ пользователя в вашем аккаунте разработчика TeleStore &#40;Профиль ➡ Безопасность ➡ Ключи пользователя&#41; &#40;подробнее см. в разделе [Получение Ключа пользователя]&#40;userkey.md&#41;&#41;)

[Пример создания сессии через SDK](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/sdk_connect/route.ts#L7)

### Получение информации об аккаунте

Чтобы получить информацию о аккаунте разработчика, отправьте авторизованный запрос на:
```
GET https://web.tele.store/api/v1/teleuser_details
```

[Пример получения информации об аккаунте](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/teleuser_detailts/route.ts#L8)

### Подписка на SSE

Чтобы подписаться на события SSE, создайте объект класса `EventSource` ([рекомендуемая библиотека](https://www.npmjs.com/package/eventsource)):

```javascript
let esLink = new EventSource("https://web.tele.store/auth/v1/subscribe", {
  fetch: (input, init) =>
    fetch(input, {
      ...init,
      headers: {
        ...init?.headers,
        Cookie: `sid=${sid}`
      }
    })
});

esLink.onmessage = async (event) => {
  console.log(event.data);
}

esLink.onerror = (error) => {
  console.error("Ошибка EventSource:", error);
}
```

[Пример подключения SSE](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/connect_sse/route.ts#L17)

### Получение вебхуков

Чтобы получать вебхуки, добавьте новый URL приложения (Профиль ➡ Безопасность ➡ URL приложений), указав флаг <span className="checkbox">✅ Использовать для webhooks</span> и URL метода API, который будет служить вебхуком. Пример URL вебхука:
```
POST https://test.tele.store/webhook
```

[Пример вебхука](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/webhook/route.ts#L10)

### Получение списка приложений разработчика

Чтобы получить список приложений разработчика, отправьте авторизованный запрос на:
```
GET https://web.tele.store/api/v1/dev_list_my_apps
```

[Пример получения списка приложений разработчика](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/dev_list_apps/route.ts#L8)

### Получение информации о балансе разработчика

Чтобы получить информацию о балансе разработчика, отправьте авторизованный запрос на:
```
GET https://web.tele.store/trex/v1/wallet/get_balance
PARAMS {
    currency: TeleUSD
}
```

[Пример получения информации о балансе разработчика](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/balance_info/route.ts#L8)  

### Создание перевода пользователю TeleStore

Чтобы создать перевод пользователю TeleStore, отправьте авторизованный запрос на:
```
POST https://web.tele.store/trex/v1/wallet/internal_transfer
BODY {
    "recipient": 123, // ID получателя
    "currency": "TeleUSD",
    "amount": 10, // сумма транзакции
    "tag": "" // описание транзакции
}
```

Вы получите ответ сервера:
```
BODY {
    "txId": 123, // ID транзакции
    "confirmCode": 123 // код подтверждения транзакции
}
```

Второй запрос подтверждает транзакцию:
```
POST https://web.tele.store/trex/v1/wallet/internal_transfer
PARAMS {
    "confirmationTimetick": 123, // ID транзакции
    "confirmationCode": 123 // код подтверждения транзакции
}
BODY {
    "recipient": 123, // ID получателя
    "currency": "TeleUSD",
    "amount": 10, // сумма транзакции
    "tag": "" // описание транзакции
}
```

[Пример создания перевода на пользователя TeleStore](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/server/page.tsx#L204) 

### Получение истории транзакций TeleStore

Чтобы получить историю транзакций, отправьте авторизованный запрос на:
```
GET https://web.tele.store/trex/v1/wallet/get_history_transactions
PARAMS {
    "currencies": ["TeleUSD"],
    "start": "2022-05-10", // Дата начала (без времени) UTC, по умолчанию 90 дней до конца, если не установлено
    "end": "2022-05-10", // Дата окончания (без времени) UTC, по умолчанию текущая дата, если не установлено
    "next_key": 123, // Последний идентификатор для ленивой загрузки
    "tx_types": [], // Фильтр типа операции
    "limit": 10 // Количество транзакций в выборке, мин - 10, макс - 100
}
```

[Пример получения истории транзакций](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/get_transactions/route.ts#L9)  

### Создание инвойса

[Пример создания инвойса через SDK](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/server/page.tsx#L175)  

### Получение списка инвойсов

Чтобы получить список инвойсов, отправьте авторизованный запрос на:
```
GET https://web.tele.store/trex/v1/list_tx_codes
PARAMS {
    "currency": "TeleUSD"
}
```

[Пример получения списка инвойсов](https://github.com/telestore-rep/test-app/blob/ff743c7db872b19f9ac69f32fabdf2c59dd6f737/src/app/api/get_invoices/route.ts#L8)  