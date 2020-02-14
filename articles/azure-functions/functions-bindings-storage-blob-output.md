---
title: Powiązanie danych wyjściowych usługi Azure Blob Storage dla Azure Functions
description: Dowiedz się, jak dostarczyć dane usługi Azure Blob Storage do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202140"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych usługi Azure Blob Storage dla Azure Functions

Powiązanie danych wyjściowych pozwala modyfikować i usuwać dane usługi BLOB Storage w funkcji platformy Azure.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Poniższy przykład to [ C# funkcja](functions-dotnet-class-library.md) , która używa wyzwalacza obiektu BLOB i dwóch wychodzących powiązań obiektów BLOB. Funkcja jest wyzwalana przez utworzenie obiektu BLOB obrazu w kontenerze *przykładowych obrazów* . Tworzy on małe i średnie kopie obiektu BLOB obrazu.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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
      "name": "outputblob",
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


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz http, przy użyciubinding](#http-trigger-using-outputbinding-java)
* [Wyzwalacz kolejki, używanie wartości zwracanej funkcji](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Wyzwalacz http przy użyciubinding (Java)

 Poniższy przykład pokazuje funkcję języka Java, która używa adnotacji `HttpTrigger`, aby otrzymać parametr zawierający nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]`. Adnotacja `BlobOutput` wiąże `OutputBinding outputItem`, który jest następnie używany przez funkcję do zapisywania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Wyzwalacz kolejki, używanie zwracanej wartości funkcji (Java)

 Poniższy przykład pokazuje funkcję języka Java, która używa adnotacji `QueueTrigger`, aby otrzymać komunikat zawierający nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]`. Adnotacja `BlobOutput` wiąże się z wartością zwracaną funkcji, która następnie jest używana przez środowisko uruchomieniowe do zapisania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@BlobOutput` w przypadku parametrów funkcji, których wartość zostałaby zapisywana w obiekcie w magazynie obiektów BLOB.  Typ parametru powinien być `OutputBinding<T>`, gdzie T jest dowolnym natywnym typem Java lub POJO.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i parametr `FileAccess` wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Możesz ustawić właściwość `Connection`, aby określić konto magazynu, które ma być używane, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobOutput` umożliwia dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw `dataType` na `binary`. Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

---

Aby uzyskać pełny przykład, zobacz [przykład danych wyjściowych](#example).

Można użyć atrybutu `StorageAccount`, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | musi być ustawiony na `blob`. |
|**direction** | Nie dotyczy | Musi być ustawiony na `out` dla powiązania danych wyjściowych. Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.  Ustaw `$return`, aby odwoływać się do zwracanej wartości funkcji.|
|**path** |**Blobpath ścieżką** | Ścieżka do kontenera obiektów BLOB. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

W języku JavaScript Uzyskuj dostęp do danych obiektów BLOB przy użyciu `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Parametry funkcji można zadeklarować jako następujące typy do zapisu w magazynie obiektów blob:

* Ciągi jako `func.Out(str)`
* Strumienie jako `func.Out(func.InputStream)`

Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobOutput` umożliwia dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw `dataType` na `binary`. Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Dokumentacja |
|---|---|
| Obiekt blob | [Kody błędów obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
