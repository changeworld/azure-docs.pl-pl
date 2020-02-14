---
title: Powiązanie danych wejściowych magazynu obiektów blob platformy Azure dla Azure Functions
description: Dowiedz się, jak dostarczyć dane usługi Azure Blob Storage do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202153"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Powiązanie danych wejściowych magazynu obiektów blob platformy Azure dla Azure Functions

Powiązanie danych wejściowych umożliwia odczytywanie danych usługi BLOB Storage jako danych wejściowych do funkcji platformy Azure.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Poniższy przykład to [ C# funkcja](functions-dotnet-class-library.md) , która używa wyzwalacza kolejki i wejściowego powiązania obiektu BLOB. Komunikat kolejki zawiera nazwę obiektu BLOB, a funkcja rejestruje rozmiar obiektu BLOB.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [ C# kodzie skryptu (. CSX)](functions-reference-csharp.md) , który używa powiązań. Funkcja tworzy kopię tekstu obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W pliku *Function. JSON* Właściwość `queueTrigger` Metadata służy do określania nazwy obiektu BLOB we właściwościach `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie JavaScript](functions-reference-node.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W pliku *Function. JSON* Właściwość `queueTrigger` Metadata służy do określania nazwy obiektu BLOB we właściwościach `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie Python](functions-reference-python.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W pliku *Function. JSON* Właściwość `queueTrigger` Metadata służy do określania nazwy obiektu BLOB we właściwościach `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania](#http-trigger-look-up-blob-name-from-query-string)
* [Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania

 Poniższy przykład pokazuje funkcję języka Java, która używa adnotacji `HttpTrigger`, aby otrzymać parametr zawierający nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki

 Poniższy przykład pokazuje funkcję języka Java, która używa adnotacji `QueueTrigger`, aby otrzymać komunikat zawierający nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@BlobInput` w przypadku parametrów, których wartość pochodzi z obiektu BLOB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i parametr `FileAccess` wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Możesz ustawić właściwość `Connection`, aby określić konto magazynu, które ma być używane, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Można użyć atrybutu `StorageAccount`, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty i adnotacje](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobInput` umożliwia dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw `dataType` na `binary`. Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | musi być ustawiony na `blob`. |
|**direction** | Nie dotyczy | musi być ustawiony na `in`. Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.|
|**path** |**Blobpath ścieżką** | Ścieżka do obiektu BLOB. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji, które zawiera [Parametry połączenia magazynu](../storage/common/storage-configure-connection-string.md) , które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych obiektów BLOB przy użyciu `context.bindings.<NAME>`, gdzie `<NAME>` pasuje do wartości zdefiniowanej w *funkcji Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Dostęp do danych obiektów BLOB za pomocą parametru, który został określony jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobInput` umożliwia dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw `dataType` na `binary`. Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

---

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)
