---
sidebar_position: 3
---

# Аутентификация

## Получение новой сессии
```javascript
// Первый параметр — ваш User Key с https://web.tele.store
// Второй параметр — режим разработки, true или false
const teleStoreClient = new TeleStoreClient(\<YOUR_USER_KEY>\, true);

    const isConnected: boolean = await teleStoreClient.Connect();
```
