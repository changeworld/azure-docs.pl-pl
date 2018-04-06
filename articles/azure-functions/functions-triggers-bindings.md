---
title: Wyzwalaczy i powiązań w usługi Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań w usługi Azure Functions nawiązać połączenia z wykonanie kodu zdarzenia w sieci i usług w chmurze.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: d008e94186dd3c5a18c92617ebddfffa966358d5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure funkcje wyzwalaczy i powiązań pojęcia

Ten artykuł zawiera omówienie wyzwalaczy i powiązań w funkcji platformy Azure. W tym miejscu opisano funkcje, które są wspólne dla wszystkich powiązań i wszystkich obsługiwanych językach.

## <a name="overview"></a>Przegląd

A *wyzwalacza* definiuje sposób wywoływania funkcji. Funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mieć skojarzone dane, co jest zazwyczaj ładunku, który wywołał funkcję.

Wejście i wyjście *powiązania* Podaj deklaratywne, aby nawiązać połączenie danych z poziomu kodu. Powiązania są opcjonalne i mieć wielu danych wejściowych i wyjściowych powiązania funkcji. 

Wyzwalaczy i powiązań pozwalają uniknąć hardcoding szczegółowe informacje o pracy z usługi. Funkcja odbiera dane (na przykład zawartość komunikatu w kolejce) w parametrów funkcji. Wysyłanie danych (na przykład można utworzyć komunikatu w kolejce) przy użyciu funkcji, wartość zwracana `out` parametru lub [obiekt moduł zbierający](functions-reference-csharp.md#writing-multiple-output-values).

Podczas opracowywania funkcji przy użyciu portalu Azure, wyzwalaczy i powiązań są konfigurowane w *function.json* pliku. Portal zawiera interfejsu użytkownika dla tej konfiguracji, ale plik można edytować bezpośrednio przez zmianę na **Zaawansowany edytor**.

Podczas opracowywania funkcji za pomocą programu Visual Studio do tworzenia biblioteki klas, skonfiguruj wyzwalaczy i powiązań przez dekoracji parametry z atrybutami i metod.

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które są w wersji zapoznawczej powiązań, lub są zatwierdzone do użycia w środowisku produkcyjnym, zobacz [obsługiwanych języków](supported-languages.md).

## <a name="register-binding-extensions"></a>Zarejestruj rozszerzenia powiązania

W wersji 2.x środowiska uruchomieniowego usługi Azure Functions, należy jawnie zarejestrować [powiązanie rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) używaną w funkcji aplikacji. 

Rozszerzenia są wyświetlane w postaci pakietów NuGet, których nazwa pakietu zwykle zaczyna się od [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  Sposób zainstalowania i zarejestrowania rozszerzenia powiązania zależy od tego, jak w przypadku tworzenia funkcji: 

+ [Lokalnie w języku C# za pomocą programu Visual Studio lub kodzie VS](#local-c-development-using-visual-studio-or-vs-code)
+ [Lokalnie za pomocą narzędzia podstawowych funkcji platformy Azure](#local-development-azure-functions-core-tools)
+ [W portalu Azure](#azure-portal-development) 

Brak podstawowego zestawu powiązania w wersji 2.x, które nie są dostarczane jako rozszerzenia. Nie trzeba rejestrować rozszerzeń dla następujących wyzwalaczy i powiązań: HTTP, zegara i magazynu Azure. 

Informacje dotyczące sposobu konfigurowania aplikacji funkcji w wersji 2.x środowisko uruchomieniowe Functions, zobacz [jak korzystać z wersji środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md). Wersja 2.x środowisko uruchomieniowe Functions jest obecnie w przeglądzie. 

Wersje pakietów wyświetlany w tej sekcji znajdują się tylko jako przykłady. Sprawdź [lokacji NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) do określenia, którą wersję danego rozszerzenia są wymagane przez inne zależności w aplikacji funkcji.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>C# wdrożenia lokalnego przy użyciu programu Visual Studio lub kodzie VS 

Jeśli korzystasz z programu Visual Studio lub Visual Studio Code lokalnie tworzenia funkcji w języku C#, należy po prostu Dodaj pakiet nuget służący do rozszerzenia. 

+ **Visual Studio**: Użyj narzędzia NuGet Package Manager. Następujące [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) polecenia instaluje rozszerzenie Azure DB rozwiązania Cosmos w konsoli Menedżera pakietów:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**: pakietów można zainstalować z wiersza polecenia przy użyciu [dotnet Dodaj pakiet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) poleceń w .NET interfejsu wiersza polecenia, w następujący sposób:

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Lokalne działania projektowe Azure funkcje podstawowe narzędzia

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Programowanie portalu Azure

Podczas tworzenia funkcji lub dodać powiązanie do istniejącej funkcji, zostanie wyświetlony monit, gdy rozszerzenie wyzwalacza lub powiązanie dodawany wymaga rejestracji.   

Po dla określonego rozszerzenia, instalowana pojawi się ostrzeżenie, kliknij przycisk **zainstalować** do zarejestrowania rozszerzenia. Każdego rozszerzenia, należy zainstalować jeden raz dla danej funkcji aplikacji. 

>[!Note] 
>Plan przez proces instalacji w portalu może zająć do 10 minut.

## <a name="example-trigger-and-binding"></a>Przykład wyzwalacza i powiązania

Załóżmy, że chcesz zapisać nowy wiersz do magazynu tabel Azure przy każdym wyświetleniu nowego komunikatu w magazynie kolejek platformy Azure. W tym scenariuszu można implementować przy użyciu kolejek Azure wyzwalacza z magazynu i magazynu tabel Azure powiązania wyjściowego. 

Oto *function.json* pliku dla tego scenariusza. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Pierwszym elementem w `bindings` tablica ma wyzwalacz magazynu kolejki. `type` i `direction` właściwości zidentyfikować wyzwalacza. `name` Właściwość identyfikuje parametru funkcji, który odbiera zawartość komunikatu w kolejce. Nazwa kolejki, aby monitorować jest `queueName`, i parametry połączenia są identyfikowane przez ustawienie aplikacji `connection`.

Drugi element w `bindings` tablica jest magazyn tabel Azure powiązania wyjściowego. `type` i `direction` właściwości identyfikacji powiązania. `name` Właściwość określa, jak funkcja zapewnia nowy wiersz w tabeli, w tym przypadku za pomocą funkcji zwracają wartość. Nazwa tabeli jest `tableName`, i parametry połączenia są identyfikowane przez ustawienie aplikacji `connection`.

Aby wyświetlić i edytować zawartość *function.json* w portalu Azure kliknij **Zaawansowany edytor** opcja **integracji** kartę funkcji.

> [!NOTE]
> Wartość `connection` jest nazwą ustawienia aplikacji, które zawiera parametry połączenia, a nie parametry połączenia się. Powiązania połączenia ciągi przechowywane w ustawieniach aplikacji w celu wymuszenia najlepszych rozwiązań, które *function.json* nie zawiera kluczy tajnych usługi.

Oto C# kodu skryptu, który współpracuje z tego wyzwalacza i powiązania. Należy zauważyć, że nazwa parametru, który udostępnia zawartość komunikatu w kolejce jest `order`; ta nazwa jest wymagana, ponieważ `name` wartości właściwości w *function.json* jest `order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Tego samego pliku function.json można użyć z funkcją JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

W bibliotece klas, taki sam wyzwalacz i informacje o powiązaniu &mdash; nazwy kolejki i tabeli kont magazynu funkcji parametry wejściowe i wyjściowe &mdash; są dostarczane przez atrybuty zamiast pliku function.json. Oto przykład:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Kierunek powiązania

Wszystkich wyzwalaczy i powiązań ma `direction` właściwości w *function.json* pliku:

- Wyzwalacze kierunek jest zawsze `in`
- Użyj powiązań wejściowych i wyjściowych `in` i `out`
- Niektóre powiązania obsługuje specjalne kierunku `inout`. Jeśli używasz `inout`, tylko **Zaawansowany edytor** jest dostępna w **integracji** kartę.

Jeśli używasz [atrybutów w bibliotece klas](functions-dotnet-class-library.md) skonfigurować wyzwalaczy i powiązań, kierunek dostarczony w Konstruktorze atrybutu lub wywnioskować typu parametru.

## <a name="using-the-function-return-value"></a>Przy użyciu wartości zwracanej — funkcja

W językach, które mają wartość zwracaną wartość zwracaną można powiązać powiązania danych wyjściowych:

* W języku C# biblioteki klas zastosuj atrybut wiązania danych wyjściowych do zwracana wartość metody.
* W innych językach, należy ustawić `name` właściwości w *function.json* do `$return`.

Jeśli trzeba zapisać więcej niż jeden element, użyj [obiekt moduł zbierający](functions-reference-csharp.md#writing-multiple-output-values) zamiast wartości zwracanej. Jeśli istnieje wiele powiązań danych wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Przykład C#

W tym C# kodu korzystającego z wartością zwracaną dla powiązania danych wyjściowych, następuje przykład asynchronicznych:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

Oto powiązania danych wyjściowych *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod C# skrypt, następuje przykład async:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Przykład F #

Oto powiązania danych wyjściowych *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod F #:

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Przykład JavaScript

Oto powiązania danych wyjściowych *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript, zwracana wartość przechodzi w parametrze drugi `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>Właściwość dataType powiązania

W środowisku .NET należy użyć typu parametru do definiowania typu danych dla danych wejściowych. Na przykład użyć `string` powiązać tekstu wyzwalacza kolejki, tablica bajtów do odczytu jako binarny i niestandardowego typu do deserializacji do obiektu POCO.

Dla języków, które są dynamicznie wpisane takich jak JavaScript, użyj `dataType` właściwości w *function.json* pliku. Na przykład można odczytać treści żądania HTTP w formacie binarnym, należy ustawić `dataType` do `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Inne opcje `dataType` są `stream` i `string`.

## <a name="binding-expressions-and-patterns"></a>Wyrażenia wiązania i wzorce

Jedną z najbardziej zaawansowanych funkcji, wyzwalaczy i powiązań jest *wyrażenia powiązania*. W *function.json* plik, a w funkcji parametry i kod, można użyć wyrażenia, które rozpoznają wartości z różnych źródeł.

Większość wyrażenia są identyfikowane przez zawijania je w nawiasach klamrowych. Na przykład w funkcji wyzwalacza kolejki `{queueTrigger}` jest rozpoznawana jako tekst wiadomości w kolejce. Jeśli `path` właściwości dla obiektu blob danych wyjściowych jest powiązanie `container/{queueTrigger}` i funkcji jest wyzwalany przez komunikatu w kolejce `HelloWorld`, obiektu blob o nazwie `HelloWorld` jest tworzony.

Typy wyrażeń powiązania

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#binding-expressions---trigger-file-name)
* [Wyzwalacz metadanych](#binding-expressions---trigger-metadata)
* [Ładunek JSON](#binding-expressions---json-payloads)
* [Nowy identyfikator GUID](#binding-expressions---create-guids)
* [Bieżąca data i godzina](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Wyrażenia wiązania — ustawienia aplikacji

Najlepszym rozwiązaniem kluczy tajnych i parametry połączenia mają być zarządzane przy użyciu ustawienia aplikacji, a nie plików konfiguracyjnych. To ogranicza dostęp do tych kluczy tajnych i pozwala bezpiecznie przechowywać pliki takie jak *function.json* w repozytoria kontroli źródła publicznego.

Ustawienia aplikacji są przydatne także w przypadku, gdy chcesz zmienić konfigurację na podstawie środowiska. Na przykład w środowisku testowym można monitorować różne kontenera magazynu kolejek i obiektów blob.

Wyrażenia wiązania ustawienia aplikacji są identyfikowane zależy od innych wyrażenia wiązania: one są ujęte w znaki procentu, a nie w nawiasy klamrowe. Na przykład, jeśli ścieżka powiązania wyjściowego obiektu blob jest `%Environment%/newblob.txt` i `Environment` wartość ustawienia aplikacji to `Development`, obiektu blob zostaną utworzone w `Development` kontenera.

Gdy funkcja działa lokalnie, wartość ustawienia aplikacji pochodzą z *local.settings.json* pliku.

Należy pamiętać, że `connection` wyzwalaczy i powiązań jest szczególnych przypadkach i automatycznie rozpoznaje wartości zgodnie z ustawieniami aplikacji, bez procentu. 

Poniższy przykład jest magazyn kolejek Azure wyzwalacz, który używa ustawienia aplikacji `%input-queue-name%` do definiowania wyzwalane w kolejce.

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

Można użyć tej samej metody w bibliotekach klas:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Wyrażenia wiązania - nazwa pliku wyzwalacza

`path` Dla obiekt Blob wyzwalacz może być wzorzec, który umożliwia odwoływać się do nazwy obiektu blob wyzwalającą w pozostałych powiązaniach i działania kodu. Wzorzec mogą również obejmować kryteria filtrowania, które określają, które obiekty BLOB może wyzwolić wywołania funkcji.

Na przykład w następujących wyzwalacza obiektu Blob powiązanie `path` wzorzec jest `sample-images/{filename}`, co powoduje wyrażenia powiązania o nazwie `filename`:

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

Wyrażenie `filename` można w powiązaniu danych wyjściowych do określenia nazwy obiektu blob tworzona:

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
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Tej samej możliwość używania wyrażeń powiązania i wzorce ma zastosowanie do atrybutów w bibliotekach klas. W poniższym przykładzie parametrami konstruktora atrybutu są takie same `path` wartości, jak poprzedni *function.json* przykłady: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

Można również utworzyć wyrażenia dla elementów, takich jak rozszerzenie nazwy pliku. Aby uzyskać więcej informacji o sposobie używania wyrażeń i wzorce w ciągu ścieżki obiektu Blob, zobacz [odwołanie do magazynu obiektów blob powiązania](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Wyrażenia wiązania - wyzwalacza metadanych

Oprócz ładunku danych dostarczonych przez wyzwalacz (na przykład zawartość komunikat z kolejki, który wywołał funkcję) wiele wyzwalaczy, podaj wartości dodatkowe metadane. Te wartości może służyć jako parametry wejściowe w języku C# i F # lub właściwości na `context.bindings` obiektu w języku JavaScript. 

Na przykład wyzwalacz magazynu kolejek Azure obsługuje następujące właściwości:

* QueueTrigger - wyzwalania zawartość komunikatu, jeśli prawidłowy ciąg
* DequeueCount
* ExpirationTime
* Identyfikator
* InsertionTime
* NextVisibleTime
* PopReceipt

Te wartości metadanych są dostępne w *function.json* właściwości pliku. Na przykład użyć wyzwalacza kolejki i kolejki wiadomości zawiera nazwę obiektu blob, który chcesz odczytać. W *function.json* plików, można użyć `queueTrigger` metadanych właściwości w obiekcie blob `path` właściwości, jak pokazano w poniższym przykładzie:

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

Szczegółowe informacje o właściwości metadanych dla każdego wyzwalacza są opisane w odpowiedniej artykule. Na przykład zobacz [kolejki wyzwalacza metadanych](functions-bindings-storage-queue.md#trigger---message-metadata). Dokumentacja jest również dostępna w **integracji** kartę portalu w **dokumentacji** sekcji poniżej obszar konfiguracji powiązania.  

### <a name="binding-expressions---json-payloads"></a>Wyrażenia wiązania - ładunek JSON

Gdy ładunek wyzwalacza jest JSON, mogą odwoływać się do jego właściwości w konfiguracji na innych powiązania w tej samej funkcji i funkcji kodu.

W poniższym przykładzie przedstawiono *function.json* pliku dla elementu webhook funkcję, która odbiera nazwa obiektu blob w formacie JSON: `{"BlobName":"HelloWorld.txt"}`. Powiązania wejściowego obiektu Blob odczytuje obiektu blob i HTTP output powiązanie zwraca zawartość obiektu blob w odpowiedzi HTTP. Zwróć uwagę, że powiązanie wejściowych obiektu Blob pobiera nazwę obiektu blob, odnosząc się bezpośrednio do `BlobName` właściwości (`"path": "strings/{BlobName}"`)

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Aby to zrobić w języku C# i F # należy klasa, która definiuje pola do deserializacji, jak w poniższym przykładzie:

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

W języku JavaScript deserializacji JSON jest wykonywane automatycznie.

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

#### <a name="dot-notation"></a>Kropkowego

Niektóre z właściwości w Twojej ładunek JSON są obiekty z właściwościami, mogą odwoływać się do tych bezpośrednio za pomocą kropkowego. Na przykład załóżmy, że Twoje JSON wygląda następująco:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Można odwoływać się bezpośrednio do `FileName` jako `BlobName.FileName`. W formacie JSON, Oto co `path` właściwości w poprzednim przykładzie powinien wyglądać tak:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

W języku C# będzie potrzebny dwie klasy:

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

### <a name="binding-expressions---create-guids"></a>Wyrażenia wiązania — tworzenie identyfikatorów GUID

`{rand-guid}` Powiązanie wyrażenia tworzy identyfikator GUID. Następująca ścieżka obiektu blob w `function.json` plik tworzy obiektu blob o nazwie, jak *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Wyrażenia wiązania — bieżący czas

Wyrażenie powiązania `DateTime` jest rozpoznawana jako `DateTime.UtcNow`. Następująca ścieżka obiektu blob w `function.json` plik tworzy obiektu blob o nazwie, jak *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych języków .NET, można użyć wzorca wiązania konieczne, w przeciwieństwie do deklaratywne powiązania w *function.json* i atrybutów. Powiązanie konieczne jest przydatne, gdy Parametry wiążące muszą ma zostać obliczony w czasie środowiska uruchomieniowego zamiast projektu. Aby dowiedzieć się więcej, zobacz [dokumentacja dla deweloperów języka C#](functions-dotnet-class-library.md#binding-at-runtime) lub [dokumentacja dla deweloperów skryptu C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Schemat pliku Function.JSON

*Function.json* schemat pliku znajduje się w temacie [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Obsługa błędów powiązań

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Łącza do wszystkich odpowiednich błąd tematów dotyczących różnych usług obsługiwanych przez funkcje, zobacz [powiązanie kody błędów](functions-bindings-error-pages.md#binding-error-codes) sekcji [obsługi błędów usługi Azure Functions](functions-bindings-error-pages.md) temat.  

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na określone powiązanie zobacz następujące artykuły:

- [HTTP i elementy webhook](functions-bindings-http-webhook.md)
- [Czasomierz](functions-bindings-timer.md)
- [Queue Storage](functions-bindings-storage-queue.md)
- [Blob Storage](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [Centrum zdarzeń](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Plik zewnętrzny](functions-bindings-external-file.md)
