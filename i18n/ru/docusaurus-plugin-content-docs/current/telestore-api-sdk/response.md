---
sidebar_position: 5
---

# Интерфейс ответа

### JavaScript SDK
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
### API
**[ДОБАВИТЬ ИЗ SWAGGER]**