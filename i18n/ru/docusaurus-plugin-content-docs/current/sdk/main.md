---
sidebar_position: 3
---

# Основные функции SDK

## Аутентификация

### Получение новой сессии
```javascript
// Первый параметр — ваш User Key с https://web.tele.store
// Второй параметр — режим разработки, true или false
const teleStoreClient = new TeleStoreClient("YOUR_USER_KEY", true); 

const isConnected: boolean = await teleStoreClient.Connect();
```

## Использование кошелька

### Получение истории транзакций

```javascript
const txHistory = await teleStoreClient.GetTransactionHistory({
    currencies: ["TeleUSD"],

    // Формат даты: ГГГГ-ММ-ДД
    start: "2025-05-10", // Дата начала поиска по UTC, если не указана — то 90 дней от конца.
    end: "2024-05-10", // Конечная дата выборки по UTC, если не указана, то текущая.

    limit: 10, // Количество транзакций в выводе, по умолчанию 10, но не более 100
    next_key: null, // После какого идентификатора (глубже в историю) продолжить выборку (для ленивой загрузки)
    tx_types: [13, 14] // Тип транзакции
});
```

### Пример создания инвойса для пользователя

```javascript
const getNewInvoiceLink = async (amount: number, partnerInfo: number, tag: string) => {
    const response = await teleStoreClient.CreateInvoice({
        amount: amount,
        currency: "TeleUSD",
        appId: `<YOUR_APP_ID>`,     // Ваш идентификатор приложения с https://web.tele.store
        partnerInfo: partnerInfo,   // ID пользователя из вашего приложения
        tag: tag                    // Информация о купленном товаре
    }, true);                       // По умолчанию, telestore перенаправит пользователя на ваше приложение после оплаты, если вы хотите этого избежать, установите false

    if (response.error || !response.result) {
        throw response.error || !response.result; // Обработка ошибок
    }

    return response.result.link;
}

// Пример результата
// https://web.tele.store/shop?invoice=ISDW2JF3AFSTP&redirect=true
const navigateUrl = await getNewInvoiceLink(20, `"YOUR_USER_ID"`, `"BOUGHT_ITEM_INFO"`);
```
Перенаправьте пользователя на **link** из полученного объекта.

### Пример мониторинга платежей по инвойсам

```javascript
const handleInvoiceFunction = async (invoice: HistoryTransaction) => {
    console.log(invoice);
}

const lastProcessedId: string | null = null; /* Последний обработанный вашим приложением счет-фактура транзакции */

const isSuccess = teleStoreClient.StartMonitoring({
    handleInvoiceUpdate: handleInvoiceFunction,
}, lastProcessedId); // lastProcessedId необходим для обработки всех необработанных транзакций, прежде чем начнется мониторинг.
```

## Интерфейс ответа

Каждый тип возвращаемого значения из методов SDK обернут в объект ApiResponse:

```javascript
export interface ApiResponse<T> {
    error?: ErrorObject | null; // Ошибка, если она есть
    result?: T | null; // Результат, если он есть
}

export interface ErrorObject {
    code: number; // Код ошибки может быть использован для отлова конкретных кодов ошибок
    readonly message: string | null; // Описание ошибки     
}
```