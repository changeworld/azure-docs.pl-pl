---
title: Azure Functions powiązania wyrażeń i wzorców
description: Dowiedz się utworzyć inną wyrażenia wiązania usługi Azure Functions, oparte na typowych wzorców.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437875"
---
# <a name="azure-functions-binding-expression-patterns"></a>Usługa Azure Functions powiązania wzorców wyrażeń

Jedną z najbardziej zaawansowanych funkcji [wyzwalaczy i powiązań](./functions-triggers-bindings.md) jest *powiązania wyrażeń*. W *function.json* plików i parametrów funkcji i kodu, można użyć wyrażenia, które nawiązują do wartości z różnych źródeł.

Większości wyrażeń są identyfikowane przez opakowywanie je w nawiasach klamrowych. Na przykład w funkcji wyzwalacza przez kolejkę `{queueTrigger}` jest rozpoznawana jako tekst komunikatu w kolejce. Jeśli `path` właściwości dla obiektu blob danych wyjściowych jest powiązanie `container/{queueTrigger}` i funkcja jest wyzwalana przez komunikatu w kolejce `HelloWorld`, obiektu blob o nazwie `HelloWorld` zostanie utworzony.

Typy wyrażeń powiązania

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#trigger-file-name)
* [Metadane wyzwalacza](#trigger-metadata)
* [Ładunki JSON](#json-payloads)
* [Nowy identyfikator GUID](#create-guids)
* [Bieżąca data i godzina](#current-time)

## <a name="binding-expressions---app-settings"></a>Wyrażenia wiązania — ustawienia aplikacji

Najlepszym rozwiązaniem wpisów tajnych i parametry połączenia powinny być zarządzane przy użyciu ustawień aplikacji, a nie plików konfiguracyjnych. To ogranicza dostęp do tych kluczy tajnych i sprawia, że można bezpiecznie przechowywać pliki, takie jak *function.json* w poziomie repozytoriów kontroli źródła publicznych.

Ustawienia aplikacji są także przydatne zawsze wtedy, gdy chcesz zmienić konfiguracji opartych na środowisku. Na przykład w środowisku testowym, można monitorować różne kolejki lub obiektu blob kontenera magazynu.

Wyrażenia wiązania ustawienie aplikacji są identyfikowane w różny sposób w innych wyrażeniach wiązania: one są opakowane w znaki procentu zamiast nawiasów klamrowych. Na przykład jeśli ścieżka powiązania danych wyjściowych obiektu blob jest `%Environment%/newblob.txt` i `Environment` wartość ustawienia aplikacji to `Development`, obiekt blob zostanie utworzony w `Development` kontenera.

Gdy funkcja działa lokalnie, wartości ustawień aplikacji pochodzą *local.settings.json* pliku.

Należy pamiętać, że `connection` właściwości wyzwalaczy i powiązań stanowią specjalny przypadek i automatycznie rozpoznaje wartości jako ustawienia aplikacji, bez znaków procentu. 

Poniższy przykład jest wyzwalacza usługi Azure Queue Storage, który używa ustawienia aplikacji `%input-queue-name%` do definiowania kolejki uruchomienie wyzwalacza.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

W bibliotekach klas, można użyć tej samej metody:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nazwa pliku wyzwalacza

`path` Dla obiektu Blob wyzwalacza może być wzorzec, którego można odwoływać się do nazwy wyzwalająca obiektu blob w pozostałych powiązaniach i funkcji kodu. Wzorzec może również obejmować kryteria filtrowania, które określają, które obiekty BLOB mogą wyzwalać wywołania funkcji.

Na przykład w następujących wyzwalacz obiektu Blob powiązania `path` wzorzec jest `sample-images/{filename}`, co powoduje utworzenie wyrażenia wiązania o nazwie `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Wyrażenie `filename` można następnie używane w powiązania danych wyjściowych, aby określić nazwę obiektu blob, tworzona:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kod funkcji ma dostęp do tej samej wartości przy użyciu `filename` jako nazwę parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Tym samym możliwość korzystania z wyrażenia wiązania i wzorców jest stosowana do atrybutów w bibliotekach klas. W poniższym przykładzie parametry konstruktora atrybutu są takie same `path` wartości co w poprzedni *function.json* przykłady: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Można również utworzyć wyrażeń dla części nazwy plików, takich jak rozszerzenie. Aby uzyskać więcej informacji o sposobie używania wyrażeń i wzorców w ciąg ścieżki obiektu Blob, zobacz [odwołania do powiązania obiektu blob magazynu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadane wyzwalacza

Oprócz ładunek danych dostarczone przez wyzwalacza (na przykład zawartość komunikatu w kolejce, który wywołał funkcję) wiele wyzwalaczy, podaj wartości dodatkowe metadane. Te wartości mogą służyć jako parametrów wejściowych w C# i F# lub we właściwościach `context.bindings` obiektu w języku JavaScript. 

Na przykład wyzwalacz usługi Azure Queue storage obsługuje następujące właściwości:

* QueueTrigger — wyzwalanie treści wiadomości, jeśli prawidłowy ciąg
* DequeueCount
* ExpirationTime
* Identyfikator
* InsertionTime
* NextVisibleTime
* PopReceipt

Wartości te metadane są dostępne w *function.json* właściwości pliku. Załóżmy na przykład, można użyć wyzwalacza kolejki i komunikatu w kolejce zawiera nazwę obiektu blob, który chcesz odczytać. W *function.json* pliku, można użyć `queueTrigger` metadanych właściwości w obiekcie blob `path` właściwości, jak pokazano w poniższym przykładzie:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Szczegółów właściwości metadanych dla każdego wyzwalacza są opisane w artykule odpowiednie odwołania. Aby uzyskać przykład, zobacz [metadane wyzwalacz kolejki](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentacja jest również dostępna w **integracja** karty portalu w **dokumentacji** sekcji poniżej obszar konfiguracji powiązania.  

## <a name="json-payloads"></a>Ładunki JSON

Ładunek wyzwalacza jest JSON, można znaleźć jego właściwości w konfiguracji w przypadku innych powiązań w tej samej funkcji i kodu funkcji.

W poniższym przykładzie przedstawiono *function.json* pliku dla funkcji elementu webhook, który odbiera nazwa obiektu blob w formacie JSON: `{"BlobName":"HelloWorld.txt"}`. Powiązania danych wejściowych obiektów Blob odczytuje obiekt blob i HTTP, dane wyjściowe powiązania zwraca zawartość obiektu blob w odpowiedzi HTTP. Należy zauważyć, że powiązania danych wejściowych obiektów Blob pobiera nazwy obiektu blob, odwołując się bezpośrednio do `BlobName` właściwości (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby to zrobić C# i F#, potrzebujesz klasę, która definiuje pola, które mają zostać przeprowadzona, jak w poniższym przykładzie:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

W języku JavaScript deserializacji JSON jest wykonywana automatycznie.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Notacji z kropką

Niektóre właściwości w ładunek w formacie JSON, obiekty są przy użyciu właściwości mogą odwoływać się do tych, które bezpośrednio przy użyciu notacji z kropką. Na przykład załóżmy, że kod JSON wygląda następująco:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Można się odwoływać bezpośrednio do `FileName` jako `BlobName.FileName`. W formacie JSON, Oto, co `path` będzie wyglądać właściwość w poprzednim przykładzie:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

W języku C# będziesz potrzebować dwóch klas:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Utwórz GUID

`{rand-guid}` Powiązania wyrażeń tworzy identyfikator GUID. Następująca ścieżka obiektu blob w `function.json` plik tworzy obiekt blob o nazwie, takich jak *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Bieżący czas

Wyrażenia wiązania `DateTime` jest rozpoznawana jako `DateTime.UtcNow`. Następująca ścieżka obiektu blob w `function.json` plik tworzy obiekt blob o nazwie, takich jak *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Powiązania w czasie wykonywania

W języku C# i innych językach .NET, można użyć wzorca wiązania imperatywnego, w przeciwieństwie do deklaratywne powiązania w *function.json* i atrybutów. Imperatywne powiązania jest przydatne, gdy Parametry wiążące muszą być obliczane w czasie wykonywania, a nie projekt. Aby dowiedzieć się więcej, zobacz [dokumentacja dla deweloperów języka C#](functions-dotnet-class-library.md#binding-at-runtime) lub [dokumentacja dla deweloperów skryptów języka C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Przy użyciu wartości zwracanej funkcji platformy Azure](./functions-bindings-return-value.md)

<!-- Update_Description: link update -->