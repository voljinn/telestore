---
sidebar_position: 4
---

# Wallet Usage

## Receiving transaction history

### JavaScript SDK
```javascript
const txHistory = await teleStoreClient.GetTransactionHistory({
    currencies: ["TeleUSD"],

    // Date format: YYYY-MM-DD
    start: "2025-05-10", // Start date (without time) of UTC search, if not specified — then 90 days from end.
    end: "2024-05-10", // The final date (without time) of the UTC sample, if not specified, then the current one.

    limit: 10, // Number of transactions in the output, by default 10, but not more than 100
    next_key: null, // After what identifier (deep into history) to continue the selection (for lazy loading)
    tx_types: [13, 14] // Transacion type
});
```
### API
**[ADD]**

## Creating invoice for user example

### JavaScript SDK
```javascript
const getNewInvoiceLink = async (amount: number, partnerInfo: number, tag: string) => {
    const response = await teleStoreClient.CreateInvoice({
        amount: amount,
        currency: "TeleUSD",
        appId: `<YOUR_APP_ID>`,     // Your application ID from https://web.tele.store 
        partnerInfo: partnerInfo,   // User ID from your app
        tag: tag                    // Info about bought item
    }, true);                       // By default, telestore will redirect user to your app after payment, if you want to avoid this, set to false

    if (response.error || !response.result) {
        throw response.error || !response.result; // Your error handling
    }

    return response.result.link;
}

// Result example
// https://web.tele.store/shop?invoice=ISDW2JF3AFSTP&redirect=true
const navigateUrl = await getNewInvoiceLink(20, `<YOUR_USER_ID>`, `<BOUGHT_ITEM_INFO>`);
```
Navigate user to **link** from received object.

### API
**[ADD]**

## Invoice payment monitoring example

### JavaScript SDK
```javascript
const handleInvoiceFunction = async (invoice: HistoryTransaction) => {
  console.log(invoice);
}

const lastProcessedId: string | null = null; /* Last proccessed by your app invoice transaction */

const isSuccess = teleStoreClient.StartMonitoring({
  handleInvoiceUpdate: handleInvoiceFunction,
}, lastProcessedId); // lastProcessedId is required to process all unprocessed transactions, before monitoring begins.
```
> ⚠️ *Create new invoices only after successfully started monitoring the service.*

### API
**[ADD]**

## Receiving info about success payment

After the user makes a successful payment, he will be redirected to your application page with the parameter `successPayment=${transactionId}`, the link format is given below:

`https://your-game.link/?successPayment=1734526117729582`