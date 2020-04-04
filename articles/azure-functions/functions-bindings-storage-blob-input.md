---
title: Powiązanie danych wejściowych magazynu obiektów Blob platformy Azure dla usług Azure Functions
description: Dowiedz się, jak dostarczać dane magazynu obiektów Blob platformy Azure do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 33db9a8d86e02db2076cdb85170d466697930b96
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633885"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Powiązanie danych wejściowych magazynu obiektów Blob platformy Azure dla usług Azure Functions

Powiązanie wejściowe umożliwia odczyt danych magazynu obiektów blob jako dane wejściowe do funkcji platformy Azure.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład jest [funkcją Języka C#,](functions-dotnet-class-library.md) która używa wyzwalacza kolejki i powiązania wejściowego obiektu blob. Komunikat kolejki zawiera nazwę obiektu blob, a funkcja rejestruje rozmiar obiektu blob.

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono powiązania wejściowe i wyjściowe obiektu blob w pliku *function.json* i kod [skryptu C# (csx),](functions-reference-csharp.md) który używa powiązań. Funkcja tworzy kopię tekstowego obiektu blob. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob nosi nazwę *{originalblobname}-Copy*.

W pliku *function.json* `queueTrigger` właściwość metadanych jest używana do `path` określania nazwy obiektu blob we właściwościach:

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono powiązania wejściowe i wyjściowe obiektu blob w pliku *function.json* i [kod JavaScript,](functions-reference-node.md) który używa powiązań. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob nosi nazwę *{originalblobname}-Copy*.

W pliku *function.json* `queueTrigger` właściwość metadanych jest używana do `path` określania nazwy obiektu blob we właściwościach:

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono powiązania wejściowe i wyjściowe obiektu blob w pliku *function.json* i [kod python,](functions-reference-python.md) który używa powiązań. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob nosi nazwę *{originalblobname}-Copy*.

W pliku *function.json* `queueTrigger` właściwość metadanych jest używana do `path` określania nazwy obiektu blob we właściwościach:

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
      "dataType": "binary",
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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukuj nazwę obiektu blob z ciągu kwerendy](#http-trigger-look-up-blob-name-from-query-string)
* [Wyzwalacz kolejki, odbieranie nazwy obiektu blob z wiadomości kolejki](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Wyzwalacz HTTP, wyszukuj nazwę obiektu blob z ciągu kwerendy

 W poniższym przykładzie przedstawiono `HttpTrigger` funkcję Java, która używa adnotacji do odbierania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów blob. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego `byte[]`zawartość do funkcji jako .

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wyzwalacz kolejki, odbieranie nazwy obiektu blob z wiadomości kolejki

 W poniższym przykładzie pokazano funkcję `QueueTrigger` Java, która używa adnotacji do odbierania wiadomości zawierającej nazwę pliku w kontenerze magazynu obiektów blob. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego `byte[]`zawartość do funkcji jako .

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

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@BlobInput` Języka Java użyj adnotacji na temat parametrów, których wartość będzie pochodzić z obiektu blob.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu `FileAccess` blob i parametr wskazujący odczyt lub zapis, jak pokazano w poniższym przykładzie:

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

Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

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

Za pomocą `StorageAccount` atrybutu można określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [Wyzwalacz - atrybuty i adnotacje](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobInput` daje dostęp do obiektu blob, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z `dataType` `binary`atrybutem, ustaw na . Szczegółowe informacje można znaleźć w [przykładzie wprowadzania.](#example)

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Blob` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `blob`ustawiona na . |
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Wyjątki są odnotowane w sekcji [użycia.](#usage) |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej obiekt blob w kodzie funkcji.|
|**Ścieżka** |**Ścieżka obiektu BlobPath** | Ścieżka do obiektu blob. |
|**Połączenia** |**Połączenia**| Nazwa ustawienia aplikacji, która zawiera [ciąg połączenia magazynu](../storage/common/storage-configure-connection-string.md) do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", funkcja środowiska wykonawczego szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .<br><br>Parametry połączenia muszą być dla konta magazynu ogólnego przeznaczenia, a nie konta magazynu tylko dla [obiektów blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będziesz czytać czy pisać. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych `context.bindings.<NAME>` `<NAME>` obiektów blob przy użyciu miejsca, w którym jest zgodny z wartością zdefiniowaną w *pliku function.json*.

# <a name="python"></a>[Python](#tab/python)

Dostęp do danych obiektów blob za pośrednictwem parametru wpisanego jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Szczegółowe informacje można znaleźć w [przykładzie wprowadzania.](#example)

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobInput` daje dostęp do obiektu blob, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z `dataType` `binary`atrybutem, ustaw na . Szczegółowe informacje można znaleźć w [przykładzie wprowadzania.](#example)

---

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji po zmianie danych magazynu obiektów blob](./functions-bindings-storage-blob-trigger.md)
- [Zapisywanie danych magazynu obiektów blob z funkcji](./functions-bindings-storage-blob-output.md)
