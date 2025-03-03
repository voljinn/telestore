---
sidebar_position: 4
---

# Использование Кошелька

## Получение истории транзакций

### JavaScript SDK
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

### API
[ДОБАВИТЬ ИЗ SWAGGER]

## Пример создания счета для пользователя

### JavaScript SDK

```javascript
const getNewInvoiceLink = async (amount: number, partnerInfo: number, tag: string) => {
    const response = await teleStoreClient.CreateInvoice({
        amount: amount,
        currency: "TeleUSD",
        appId: <YOUR_APP_ID>,      // Ваш идентификатор приложения с https://web.tele.store
            partnerInfo: partnerInfo,  // ID пользователя из вашего приложения
            tag: tag                   // Информация о купленном товаре
            }, true); // По умолчанию, telestore перенаправит пользователя на ваше приложение после оплаты, если вы хотите этого избежать, установите false

            if (response.error || !response.result) {
                throw response.error || !response.result; // Обработка ошибок
            }

            return response.result.link;
            }

            // Пример результата
            // https://web.tele.store/shop?invoice=ISDW2JF3AFSTP&redirect=true
            const navigateUrl = await getNewInvoiceLink(20, \<YOUR_USER_ID>\, \<BOUGHT_ITEM_INFO>\);

                // Перенаправьте пользователя на ссылку из полученного объекта.const getNewInvoiceLink = async (amount: number, partnerInfo: number, tag: string) => {
                    const response = await teleStoreClient.CreateInvoice({
                    amount: amount,
                    currency: "TeleUSD",
                    appId: <YOUR_APP_ID>,      // Ваш идентификатор приложения с https://web.tele.store
                    partnerInfo: partnerInfo,  // ID пользователя из вашего приложения
                    tag: tag                   // Информация о купленном товаре
                }, true); // По умолчанию, telestore перенаправит пользователя на ваше приложение после оплаты, если вы хотите этого избежать, установите false

                if (response.error || !response.result) {
                    throw response.error || !response.result; // Обработка ошибок
                }

                return response.result.link;
                }

                // Пример результата
                // https://web.tele.store/shop?invoice=ISDW2JF3AFSTP&redirect=true
                const navigateUrl = await getNewInvoiceLink(20, \<YOUR_USER_ID>\, \<BOUGHT_ITEM_INFO>\);
```
Перенаправьте пользователя на **link** из полученного объекта.

### API
**[ДОБАВИТЬ ИЗ SWAGGER]**

## Пример мониторинга платежей по счетам (инвойсам)

### JavaScript SDK

```javascript
const handleInvoiceFunction = async (invoice: HistoryTransaction) => {
    console.log(invoice);
}

const lastProcessedId: string | null = null; /* Последний обработанный вашим приложением счет-фактура транзакции */

const isSuccess = teleStoreClient.StartMonitoring({
    handleInvoiceUpdate: handleInvoiceFunction,
}, lastProcessedId); // lastProcessedId необходим для обработки всех необработанных транзакций, прежде чем начнется мониторинг.
```
> ⚠️ *Создавайте новые инвойсы только после успешного запуска мониторинга сервиса.*

### API
**[ДОБАВИТЬ ИЗ SWAGGER]**

## Получение информации об успешном платеже

После успешной оплаты пользователь будет перенаправлен на страницу вашего приложения с параметром `successPayment=${transactionId}`, формат ссылки указан ниже:

`https://your-game.link/?successPayment=1734526117729582`