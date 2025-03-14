---
sidebar_position: 3
---

# Authorisation

## Obtaining a New Session
```javascript
// First parameter — your User Key from https://web.tele.store
// Second parameter — development mode, true or false
const teleStoreClient = new TeleStoreClient(`<YOUR_USER_KEY>`, true);

const isConnected: boolean = await teleStoreClient.Connect();
```
