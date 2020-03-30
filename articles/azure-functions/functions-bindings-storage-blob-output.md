---
title: Powiązanie danych wyjściowych magazynu obiektów Blob platformy Azure dla usług Azure Functions
description: Dowiedz się, jak dostarczać dane magazynu obiektów Blob platformy Azure do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277247"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych magazynu obiektów Blob platformy Azure dla usług Azure Functions

Powiązanie danych wyjściowych umożliwia modyfikowanie i usuwanie danych magazynu obiektów blob w funkcji platformy Azure.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład jest [funkcją Języka C#,](functions-dotnet-class-library.md) która używa wyzwalacza obiektu blob i dwóch wyjściowych powiązań obiektów blob. Funkcja jest wyzwalana przez utworzenie obiektu blob obrazu w kontenerze *obrazów przykładowych.* Tworzy małe i średnie kopie obiektu blob obrazu.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP przy użyciu funkcji OutputBinding](#http-trigger-using-outputbinding-java)
* [Wyzwalacz kolejki przy użyciu wartości zwracanej funkcji](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Wyzwalacz HTTP przy użyciu funkcji OutputBinding (Java)

 W poniższym przykładzie przedstawiono `HttpTrigger` funkcję Java, która używa adnotacji do odbierania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów blob. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego `byte[]`zawartość do funkcji jako . Adnotacja `BlobOutput` wiąże się `OutputBinding outputItem`z programem , który jest następnie używany przez funkcję do zapisu zawartości wejściowego obiektu blob w skonfigurowanym kontenerze magazynu.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Wyzwalacz kolejki przy użyciu wartości zwracanej funkcji (Java)

 W poniższym przykładzie pokazano funkcję `QueueTrigger` Java, która używa adnotacji do odbierania wiadomości zawierającej nazwę pliku w kontenerze magazynu obiektów blob. Adnotacja `BlobInput` następnie odczytuje plik i przekazuje jego `byte[]`zawartość do funkcji jako . Adnotacja `BlobOutput` wiąże się z wartością zwracaną funkcji, która jest następnie używana przez środowisko wykonawcze do zapisu zawartości wejściowego obiektu blob w skonfigurowanym kontenerze magazynu.

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

 W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@BlobOutput` Języka Java użyj adnotacji na temat parametrów funkcji, których wartość zostanie zapisana w obiekcie w magazynie obiektów blob.  Typ parametru `OutputBinding<T>`powinien być , gdzie T jest dowolnym natywnym typem Java lub POJO.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu `FileAccess` blob i parametr wskazujący odczyt lub zapis, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobOutput` daje dostęp do obiektu blob, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z `dataType` `binary`atrybutem, ustaw na . Szczegółowe informacje można znaleźć w [przykładzie danych wyjściowych.](#example)

---

Aby uzyskać pełny przykład, zobacz [przykład wyjścia](#example).

Za pomocą `StorageAccount` atrybutu można określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [Trigger - atrybuty](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Blob` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `blob`ustawiona na . |
|**Kierunku** | Nie dotyczy | Musi być `out` ustawiona na powiązanie danych wyjściowych. Wyjątki są odnotowane w sekcji [użycia.](#usage) |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej obiekt blob w kodzie funkcji.  Ustaw, `$return` aby odwoływać się do wartości zwracanej funkcji.|
|**Ścieżka** |**Ścieżka obiektu BlobPath** | Ścieżka do kontenera obiektów blob. |
|**Połączenia** |**Połączenia**| Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", funkcja środowiska wykonawczego szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .<br><br>Parametry połączenia muszą być dla konta magazynu ogólnego przeznaczenia, a nie konta magazynu tylko dla [obiektów blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będziesz czytać czy pisać. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

W języku JavaScript uzyskaj dostęp `context.bindings.<name from function.json>`do danych obiektu blob za pomocą programu .

# <a name="python"></a>[Python](#tab/python)

Można zadeklarować parametry funkcji jako następujące typy do zapisania do magazynu obiektów blob:

* Ciągi jako`func.Out(str)`
* Strumienie jako`func.Out(func.InputStream)`

Szczegółowe informacje można znaleźć w [przykładzie danych wyjściowych.](#example)

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobOutput` daje dostęp do obiektu blob, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z `dataType` `binary`atrybutem, ustaw na . Szczegółowe informacje można znaleźć w [przykładzie danych wyjściowych.](#example)

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie |  Tematy pomocy |
|---|---|
| Obiekt blob | [Kody błędów obiektów blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt Blob, Tabela, Kolejka |  [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt Blob, Tabela, Kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji po zmianie danych magazynu obiektów blob](./functions-bindings-storage-blob-trigger.md)
- [Odczytywanie danych magazynu obiektów blob po uruchomieniu funkcji](./functions-bindings-storage-blob-input.md)
