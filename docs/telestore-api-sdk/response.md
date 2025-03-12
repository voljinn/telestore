---
sidebar_position: 5
---

# Response Interface

### JavaScript SDK
Each return type from the SDK functions is wrapped in an ApiResponse object:
```javascript
export interface ApiResponse<T> {
    error?: ErrorObject | null;
    result?: T | null;
}

export interface ErrorObject {
    code: number; // Error code can be used to catch specific error codes
    readonly message: string | null; // Error description     
}
```
### API
**[ADD]**