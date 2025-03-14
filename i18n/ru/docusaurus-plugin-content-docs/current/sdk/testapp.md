---
sidebar_position: 2
---

# Тестовое приложение 

## Интро

Это приложение сделано для того, чтобы разработчикам было проще разобраться в работе SDK TeleStore на примерах, а также для ознакомления, как работать с SDK приложениям без серверной части. 

Приложение состоит из двух частей: клиентской и серверной. В вашем приложении может не быть серверной части, но некоторые процессы могут отличаться в зависимости от вашей реализации.

Документация также доступна на [GitHub на английском языке](https://github.com/telestore-rep/test-app).

## Клиентская часть

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

#### Валидация данных

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

