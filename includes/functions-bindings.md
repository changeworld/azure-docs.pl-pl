W poniższej tabeli przedstawiono powiązania, które są obsługiwane przez dwie wersje główne środowiska uruchomieniowego usługi Azure Functions.

| Typ | 1.x | 2.x | Wyzwalacz | Dane wejściowe | Dane wyjściowe |  
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔<sup>1</sup>|✔|✔|✔|  
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|  
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |  
| [Event Hubs](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|  
| [Plik zewnętrzny](../articles/azure-functions/functions-bindings-external-file.md)<sup>2</sup>    |✔|| |✔|✔|  
| [Tabela zewnętrzna](../articles/azure-functions/functions-bindings-external-table.md)<sup>2</sup>  |✔|| |✔|✔|  
| [HTTP](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔<sup>1</sup>|✔| |✔|
| [Program Microsoft Graph<br/>tabel w programie Excel](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Program Microsoft Graph<br/>pliki usługi OneDrive](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Program Microsoft Graph<br/>wiadomości e-mail programu Outlook](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Program Microsoft Graph<br/>zdarzenia](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Program Microsoft Graph<br/>tokenów uwierzytelniania](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔|✔| |✔|✔|  
| [Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔<sup>1</sup>|✔| |✔|  
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [Service Bus](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|  
| [Table Storage](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔<sup>1</sup>| |✔|✔|  
| [Czasomierz](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|
| [Elementy Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔||✔| |✔|
  
<sup>1</sup> w 2.x, należy zarejestrować wszystkie powiązania z wyjątkiem HTTP, Czasomierz i usługi Azure Storage. Zobacz [zarejestrować rozszerzeń powiązania](../articles/azure-functions/functions-triggers-bindings.md#register-binding-extensions).

<sup>2</sup> eksperymentalne &mdash; nie obsługiwane i może być porzucony w przyszłości.
