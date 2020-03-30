---
title: Usługa Azure Functions powiązuje wyrażenia i wzorce
description: Dowiedz się, jak tworzyć różne wyrażenia wiązania usługi Azure Functions na podstawie typowych wzorców.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277650"
---
# <a name="azure-functions-binding-expression-patterns"></a>Wzorce wyrażeń wiązania usługi Azure Functions

Jedną z najpotężniejszych cech [wyzwalaczy i powiązań](./functions-triggers-bindings.md) są *wyrażenia wiążące*. W pliku *function.json* oraz w parametrach funkcji i kodzie można używać wyrażeń, które rozwiązują wartości z różnych źródeł.

Większość wyrażeń identyfikuje się przez umieszczenie ich w nawiasach klamrowych. Na przykład w funkcji wyzwalacza kolejki, `{queueTrigger}` rozwiązuje tekst wiadomości kolejki. Jeśli `path` właściwość dla powiązania wyjściowego obiektu blob jest `container/{queueTrigger}` i `HelloWorld`funkcja jest `HelloWorld` wyzwalana przez komunikat kolejki, tworzony jest obiekt blob o nazwie.

Typy wyrażeń powiązania

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#trigger-file-name)
* [Metadane wyzwalacza](#trigger-metadata)
* [Ładunki JSON](#json-payloads)
* [Nowy identyfikator GUID](#create-guids)
* [Bieżąca data i godzina](#current-time)

## <a name="binding-expressions---app-settings"></a>Wyrażenia wiązania — ustawienia aplikacji

Najlepszym rozwiązaniem jest, że wpisy tajne i parametry połączenia powinny być zarządzane przy użyciu ustawień aplikacji, a nie plików konfiguracyjnych. Ogranicza to dostęp do tych wpisów tajnych i umożliwia bezpieczne przechowywanie plików, takich jak *function.json* w repozytoriach kontroli źródła publicznego.

Ustawienia aplikacji są również przydatne, gdy chcesz zmienić konfigurację w zależności od środowiska. Na przykład w środowisku testowym można monitorować inną kolejkę lub kontener magazynu obiektów blob.

Wyrażenia wiązania wiązania ustawień aplikacji są identyfikowane inaczej niż inne wyrażenia wiązania: są one zawijane w znaki procentowe, a nie nawiasy klamrowe. Na przykład, jeśli ścieżka powiązania `%Environment%/newblob.txt` danych `Environment` wyjściowych `Development`obiektu blob jest i `Development` wartość ustawienia aplikacji jest , obiekt blob zostanie utworzony w kontenerze.

Gdy funkcja jest uruchomiona lokalnie, wartości ustawień aplikacji pochodzą z pliku *local.settings.json.*

Należy zauważyć, że `connection` właściwość wyzwalaczy i powiązań jest specjalnym przypadkiem i automatycznie rozpoznaje wartości jako ustawienia aplikacji, bez znaków procentu. 

Poniższy przykład jest wyzwalacz usługi Azure Queue `%input-queue-name%` Storage, który używa ustawienia aplikacji do definiowania kolejki do wyzwolenia.

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

Tego samego podejścia można użyć w bibliotekach klas:

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

Dla `path` wyzwalacza obiektu blob może być wzorzec, który pozwala odwołać się do nazwy wyzwalającego obiektu blob w innych powiązaniach i kod funkcji. Wzorzec może również zawierać kryteria filtrowania, które określają, które obiekty BLOB mogą wyzwalać wywołanie funkcji.

Na przykład w następującym wiązaniu `path` wyzwalacza obiektu Blob wzorzec jest `sample-images/{filename}`, który tworzy wyrażenie wiązania o nazwie: `filename`

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

Wyrażenie `filename` może być następnie użyte w powiązaniu danych wyjściowych, aby określić nazwę tworzonego obiektu blob:

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

Kod funkcji ma dostęp do `filename` tej samej wartości przy użyciu jako nazwa parametru:

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

Ta sama możliwość używania wyrażeń wiązania i wzorców ma zastosowanie do atrybutów w bibliotekach klas. W poniższym przykładzie parametry konstruktora `path` atrybutów są tymi samymi wartościami, co poprzednie przykłady *funkcji.json:* 

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

Można również tworzyć wyrażenia dla części nazwy pliku. W poniższym przykładzie funkcja jest wyzwalana tylko w nazwach plików, które pasują do wzorca:`anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Aby uzyskać więcej informacji na temat używania wyrażeń i wzorców w ciągu ścieżki obiektu Blob, zobacz [odwołanie do powiązania obiektu blob magazynu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadane wyzwalacza

Oprócz ładunku danych dostarczonego przez wyzwalacz (na przykład zawartość komunikatu kolejki, który wyzwolił funkcję), wiele wyzwalaczy dostarcza dodatkowych wartości metadanych. Wartości te mogą służyć jako parametry wejściowe w języku `context.bindings` C# i F# lub właściwości obiektu w języku JavaScript. 

Na przykład wyzwalacz magazynu kolejki platformy Azure obsługuje następujące właściwości:

* QueueTrigger - wyzwalanie zawartości wiadomości, jeśli prawidłowy ciąg
* DequeueCount (DequeueCount)
* ExpirationTime
* Identyfikator
* Czas wstawiania
* Następny Czas Widzialny
* PopReceipt (Polski)

Te wartości metadanych są dostępne we właściwościach pliku *function.json.* Załóżmy na przykład, że używasz wyzwalacza kolejki, a komunikat kolejki zawiera nazwę obiektu blob, który chcesz odczytać. W pliku *function.json* można `queueTrigger` użyć właściwości metadanych `path` we właściwości obiektu blob, jak pokazano w poniższym przykładzie:

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

Szczegóły właściwości metadanych dla każdego wyzwalacza są opisane w odpowiednim artykule referencyjnym. Na przykład zobacz [metadane wyzwalacza kolejki](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentacja jest również dostępna na karcie **Integruj** w portalu, w sekcji **Dokumentacja** poniżej obszaru konfiguracji powiązania.  

## <a name="json-payloads"></a>Ładunki JSON

Gdy ładunek wyzwalacza jest JSON, można odwołać się do jego właściwości w konfiguracji dla innych powiązań w tej samej funkcji i w kodzie funkcji.

W poniższym przykładzie pokazano plik *function.json* dla funkcji elementu webhook, która odbiera nazwę obiektu blob w JSON: `{"BlobName":"HelloWorld.txt"}`. Powiązanie wejściowe obiektu Blob odczytuje obiekt blob, a powiązanie wyjściowe HTTP zwraca zawartość obiektu blob w odpowiedzi HTTP. Należy zauważyć, że powiązanie wprowadzania obiektów Blob pobiera `BlobName` nazwę`"path": "strings/{BlobName}"`obiektu blob, odwołując się bezpośrednio do właściwości ( )

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

Aby to działało w językach C# i F#, potrzebna jest klasa definiusty pola, które mają zostać zdemonasze, tak jak w poniższym przykładzie:

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

W języku JavaScript deserializacja JSON jest wykonywana automatycznie.

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

### <a name="dot-notation"></a>Notacja kropki

Jeśli niektóre właściwości w ładunku JSON są obiekty z właściwości, można odwoływać się do tych bezpośrednio przy użyciu notacji kropka. Załóżmy na przykład, że json wygląda następująco:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Można odnosić się `FileName` `BlobName.FileName`bezpośrednio do jako . W tym formacie JSON, oto `path` jak będzie wyglądać właściwość w poprzednim przykładzie:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

W języku C#potrzebne są dwie klasy:

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

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

Wyrażenie `{rand-guid}` wiązania tworzy identyfikator GUID. Następująca ścieżka obiektu `function.json` blob w pliku tworzy obiekt blob o nazwie takiej jak *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Bieżący czas

Wyrażenie `DateTime` wiązania jest `DateTime.UtcNow`rozpoznawane na . Następująca ścieżka obiektu `function.json` blob w pliku tworzy obiekt blob o nazwie takiej jak *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych językach .NET można użyć wzorca wiązania imperatywu, w przeciwieństwie do deklaratywnych powiązań w *function.json* i atrybutów. Imperatywne powiązanie jest przydatne, gdy parametry wiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Aby dowiedzieć się więcej, zobacz [odwołanie do dewelopera języka C#](functions-dotnet-class-library.md#binding-at-runtime) lub [odwołanie do dewelopera skryptu C#.](functions-reference-csharp.md#binding-at-runtime)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Korzystanie z wartości zwracanej funkcji platformy Azure](./functions-bindings-return-value.md)
