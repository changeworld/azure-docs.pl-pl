---
title: Wyzwalacze i powiązania w usłudze Azure Functions
description: Dowiedz się, jak za pomocą wyzwalaczy i powiązań w usłudze Azure Functions połączyć wykonywanie kodu z wydarzenia online i usług w chmurze.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/24/2018
ms.author: cshoe
ms.openlocfilehash: b071bfe83ba9ef653db2d6d1debad4e3dfa02580
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845930"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia powiązania i Wyzwalacze usługi Azure Functions

Ten artykuł stanowi omówienie pojęć dotyczących wyzwalaczy i powiązań w usłudze Azure Functions. Funkcje, które są wspólne dla wszystkich powiązań i wszystkie obsługiwane języki zostały opisane w tym miejscu.

## <a name="overview"></a>Przegląd

A *wyzwalacza* definiuje sposób wywoływania funkcji. Funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mają skojarzone dane będące zwykle ładunkiem, który wyzwolił funkcję.

Dane wejściowe i wyjściowe *powiązania* zapewniają deklaratywną metodę, aby nawiązać połączenie danych z poziomu kodu. Powiązania są opcjonalne i funkcji mogą mieć wiele danych wejściowych i wyjściowych powiązania. 

Wyzwalacze i powiązania pozwalają uniknąć hardcoding szczegółowe informacje o pracy z usługi. Funkcja odbiera dane (na przykład zawartość komunikatu w kolejce) w parametrach funkcji. Możesz wysłać danych (na przykład w celu utworzenia komunikatu w kolejce) przy użyciu wartość zwracaną przez funkcję. W języku C# i skrypt języka C#, są alternatywne sposoby wysyłania danych `out` parametrów i [obiektów modułu zbierającego](functions-reference-csharp.md#writing-multiple-output-values).

Podczas tworzenia funkcji przy użyciu witryny Azure portal, wyzwalaczy i powiązań są konfigurowane w *function.json* pliku. Portal udostępnia interfejs wielokrotnego użytku tej konfiguracji, ale plik można edytować bezpośrednio, zmieniając **edytor zaawansowany**.

Podczas tworzenia funkcji w programie Visual Studio do tworzenia biblioteki klas, skonfiguruj wyzwalaczy i powiązań przez urządzanie metody i parametrów za pomocą atrybutów.

## <a name="example-trigger-and-binding"></a>Przykładowy wyzwalacz i powiązania

Załóżmy, że chcesz napisać nowy wiersz do usługi Azure Table storage, zawsze wtedy, gdy nowy komunikat pojawi się w usłudze Azure Queue storage. W tym scenariuszu można zaimplementować przy użyciu usługi Azure Queue wyzwalacz usługi storage i Azure Table storage powiązania danych wyjściowych. 

Oto *function.json* pliku, w tym scenariuszu. 

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

Pierwszy element w `bindings` tablica jest wyzwalacz magazynu kolejki. `type` i `direction` właściwości określenie wyzwalacza. `name` Właściwość identyfikuje parametru funkcji, który odbiera zawartość komunikatu w kolejce. Nazwa kolejki do monitorowania jest `queueName`, oraz parametry połączenia są identyfikowane za pomocą ustawienia aplikacji `connection`.

Drugi element `bindings` tablica jest usługa Azure Table Storage powiązania danych wyjściowych. `type` i `direction` właściwości identyfikacji powiązania. `name` Właściwość określa, jak funkcja zapewnia nowy wiersz w tabeli, w tym przypadku za pomocą funkcji zwraca wartość. Nazwa tabeli jest `tableName`, oraz parametry połączenia są identyfikowane za pomocą ustawienia aplikacji `connection`.

Możesz wyświetlać i edytować zawartość *function.json* w witrynie Azure portal kliknij pozycję **edytor zaawansowany** opcja **integracja** kartę funkcji.

> [!NOTE]
> Wartość `connection` to nazwa ustawienia aplikacji zawierającego parametry połączenia, a nie parametry połączenia, sam. Powiązania używanie połączenia ciągów przechowywanych w ustawieniach aplikacji, aby wymusić najlepsze rozwiązaniem, które *function.json* nie zawiera wpisy tajne usługi.

Oto C# kod skryptu, który współdziała z tego wyzwalacza i powiązania. Należy zauważyć, że nazwa parametru, który udostępnia zawartość komunikatu w kolejce jest `order`; ta nazwa jest wymagana, ponieważ `name` wartości właściwości w *function.json* jest `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
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

Można użyć tego samego pliku function.json za pomocą funkcji języka JavaScript:

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

Biblioteka klas, jeden wyzwalacz i informacje o powiązaniu &mdash; nazwy kolejki i tabeli konta magazynu funkcji parametry wejściowe i wyjściowe &mdash; są dostarczane przez atrybuty zamiast pliku function.json. Oto przykład:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         ILogger log)
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

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które są w wersji zapoznawczej powiązań, lub są zatwierdzone do użycia w środowisku produkcyjnym, zobacz [obsługiwane języki](supported-languages.md).

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń powiązania

W niektórych środowiskach rozwojowych, trzeba jawnie *zarejestrować* powiązanie, które chcesz użyć. Rozszerzeń powiązania znajdują się w pakietach NuGet, a aby zarejestrować rozszerzenie, należy zainstalować pakiet. Poniższa tabela wskazuje, kiedy i jak należy zarejestrować rozszerzeń powiązania.

|Środowisko deweloperskie |Rejestracja<br/> w przypadku funkcji 1.x  |Rejestracja<br/> w przypadku funkcji 2.x  |
|---------|---------|---------|
|Azure Portal|Automatyczny|[Automatyczne z wiersza](#azure-portal-development)|
|Lokalne przy użyciu podstawowych narzędzi usługi Azure Functions|Automatyczny|[Użyj poleceń interfejsu wiersza polecenia podstawowych narzędzi](#local-development-azure-functions-core-tools)|
|Biblioteki klas C# za pomocą programu Visual Studio 2017|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2017)|[Użyj narzędzia NuGet](#c-class-library-with-visual-studio-2017)|
|Biblioteki klas C# za pomocą programu Visual Studio Code|ND|[Korzystanie z platformy .NET Core interfejsu wiersza polecenia](#c-class-library-with-visual-studio-code)|

Wyjątki, które nie wymagają jawna rejestracja, ponieważ są automatycznie rejestrowane we wszystkich wersjach i środowiska są następujące typy powiązania: HTTP oraz czasomierzem.

### <a name="azure-portal-development"></a>Programowanie na platformie Azure portal

Ta sekcja dotyczy tylko do funkcji 2.x. Rozszerzeń powiązania nie muszą być jawnie zarejestrowana w funkcji 1.x.

Podczas tworzenia funkcji lub dodać powiązanie, zostanie wyświetlony monit, gdy rozszerzenie dla wyzwalaczem lub powiązaniem wymaga rejestracji. Odpowiadanie do wiersza polecenia, klikając **zainstalować** do zarejestrowania rozszerzenia. Instalacja może trwać do 10 minut, w ramach planu zużycie.

Każdego rozszerzenia, należy zainstalować jeden raz dla danej funkcji aplikacji. Dla obsługiwanych powiązań, które nie są dostępne w portalu lub zaktualizować zainstalowanego rozszerzenia można także [ręczne instalowanie lub aktualizowanie usługi Azure Functions powiązania rozszerzeń z portalu](install-update-binding-extensions-manual.md).  

### <a name="local-development-azure-functions-core-tools"></a>Lokalne programowanie podstawowych narzędzi usługi Azure Functions

Ta sekcja dotyczy tylko do funkcji 2.x. Rozszerzeń powiązania nie muszą być jawnie zarejestrowana w funkcji 1.x.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
### <a name="c-class-library-with-visual-studio-2017"></a>Biblioteki klas C# za pomocą programu Visual Studio 2017

W **programu Visual Studio 2017**, możesz zainstalować pakiety z konsoli Menedżera pakietów przy użyciu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) polecenia, jak pokazano w poniższym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Nazwa pakietu do użycia dla danego powiązania znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<target_version>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

### <a name="c-class-library-with-visual-studio-code"></a>Biblioteki klas C# za pomocą programu Visual Studio Code

W **programu Visual Studio Code**, możesz zainstalować pakiety z wiersza polecenia przy użyciu [dotnet Dodaj pakiet](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) polecenia w .NET Core interfejsu wiersza polecenia, jak pokazano w poniższym przykładzie:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core interfejsu wiersza polecenia należy używać tylko dla usługi Azure Functions 2.x rozwoju.

Nazwa pakietu do użycia dla danego powiązania znajduje się w artykule dotyczącym struktury dla tego powiązania. Aby uzyskać przykład, zobacz [pakietów w dalszej części artykułu odwołanie powiązania usługi Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Zastąp `<target_version>` w przykładzie z określoną wersją pakietu, takich jak `3.0.0-beta5`. Prawidłowych wersji są wyświetlane na stronach poszczególnych pakietów w [NuGet.org](https://nuget.org). Wersji głównych, które odpowiadają środowisko uruchomieniowe 1.x i 2.x, które są określone w artykule odwołania dla wiązania.

## <a name="binding-direction"></a>Kierunek powiązania

Wszystkie wyzwalacze i powiązania ma `direction` właściwość *function.json* pliku:

- Wyzwalacze kierunku jest zawsze `in`
- Użyj powiązania danych wejściowych i wyjściowych `in` i `out`
- Niektóre wiązania obsługuje kierunku specjalne `inout`. Jeśli używasz `inout`, tylko **edytor zaawansowany** jest dostępna w **integracja** kartę.

Kiedy używasz [atrybutów w bibliotece klas](functions-dotnet-class-library.md) do skonfigurowania, wyzwalaczy i powiązań, kierunku jest podana w Konstruktorze atrybutu lub wywnioskowane na podstawie typu parametru.

## <a name="using-the-function-return-value"></a>Przy użyciu wartości zwracanej funkcji

W przypadku języków, które mają wartość zwracaną wartość zwracaną można powiązać powiązania danych wyjściowych:

* W języku C# biblioteki klas należy zastosować atrybut wiązania danych wyjściowych na wartość zwracaną metody.
* W innych językach, należy ustawić `name` właściwość *function.json* do `$return`.

W przypadku wielu powiązania danych wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

W języku C# i skrypt języka C#, są alternatywne sposoby wysyłania danych do powiązania danych wyjściowych `out` parametrów i [obiektów modułu zbierającego](functions-reference-csharp.md#writing-multiple-output-values).

Zobacz przykład specyficzny dla języka, przedstawiający użycie zwracanej wartości:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>Przykład w języku C#

W tym C# kod, który używa wartość zwracaną dla powiązania danych wyjściowych, następuje przykład async:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Przykładowy skrypt w języku C#

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Poniżej przedstawiono kod C# script, następuje przykład async:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>F#przykład

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto F# kodu:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Przykład JavaScript

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript, zwracana wartość znajduje się w drugi parametr dla `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

### <a name="python-example"></a>Przykładem w języku Python

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Poniżej przedstawiono kod języka Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="binding-datatype-property"></a>Właściwość dataType powiązania

Na platformie .NET należy użyć typu parametru zdefiniować typ danych dla danych wejściowych. Na przykład użyć `string` do powiązania w tekście wyzwalacz kolejki, tablica bajtów do odczytania pliku binarnego i niestandardowy typ do deserializacji do obiektów POCO.

W przypadku języków, które są dynamicznie wpisane, takich jak JavaScript, użyj `dataType` właściwość *function.json* pliku. Na przykład, aby odczytać zawartość żądania HTTP w formacie binarnym, należy ustawić `dataType` do `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Inne opcje `dataType` są `stream` i `string`.

## <a name="binding-expressions-and-patterns"></a>Wyrażenia wiązania i wzorców

Jednym z najbardziej zaawansowanych funkcji, wyzwalaczy i powiązań jest *powiązania wyrażeń*. W *function.json* plików i parametrów funkcji i kodu, można użyć wyrażenia, które nawiązują do wartości z różnych źródeł.

Większości wyrażeń są identyfikowane przez opakowywanie je w nawiasach klamrowych. Na przykład w funkcji wyzwalacza przez kolejkę `{queueTrigger}` jest rozpoznawana jako tekst komunikatu w kolejce. Jeśli `path` właściwości dla obiektu blob danych wyjściowych jest powiązanie `container/{queueTrigger}` i funkcja jest wyzwalana przez komunikatu w kolejce `HelloWorld`, obiektu blob o nazwie `HelloWorld` zostanie utworzony.

Typy wyrażeń powiązania

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#binding-expressions---trigger-file-name)
* [Metadane wyzwalacza](#binding-expressions---trigger-metadata)
* [Ładunki JSON](#binding-expressions---json-payloads)
* [Nowy identyfikator GUID](#binding-expressions---create-guids)
* [Bieżąca data i godzina](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Wyrażenia wiązania — ustawienia aplikacji

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

### <a name="binding-expressions---trigger-file-name"></a>Wyrażenia wiązania — nazwa pliku wyzwalacza

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
 
### <a name="binding-expressions---trigger-metadata"></a>Wyrażenia wiązania — wyzwalanie metadanych

Oprócz ładunek danych dostarczone przez wyzwalacza (na przykład zawartość komunikatu w kolejce, który wywołał funkcję) wiele wyzwalaczy, podaj wartości dodatkowe metadane. Te wartości mogą służyć jako parametrów wejściowych w C# i F# lub we właściwościach `context.bindings` obiektu w języku JavaScript. 

Na przykład wyzwalacz usługi Azure Queue storage obsługuje następujące właściwości:

* QueueTrigger — wyzwalanie treści wiadomości, jeśli prawidłowy ciąg
* DequeueCount
* czas wygaśnięcia
* Identyfikator
* InsertionTime
* NextVisibleTime
* Elementem PopReceipt

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

### <a name="binding-expressions---json-payloads"></a>Wyrażenia wiązania - ładunków JSON

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

#### <a name="dot-notation"></a>Notacji z kropką

Niektóre właściwości w ładunek w formacie JSON, obiekty są przy użyciu właściwości mogą odwoływać się do tych, które bezpośrednio przy użyciu notacji z kropką. Na przykład załóżmy, że kod JSON wygląda następująco:

```json
{"BlobName": {
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

### <a name="binding-expressions---create-guids"></a>Wyrażenia wiązania — Utwórz GUID

`{rand-guid}` Powiązania wyrażeń tworzy identyfikator GUID. Następująca ścieżka obiektu blob w `function.json` plik tworzy obiekt blob o nazwie, takich jak *50710cb5-84b9 - 4d 87 9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Wyrażenia wiązania — bieżący czas

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

## <a name="functionjson-file-schema"></a>Schemat pliku Function.JSON

*Function.json* schemat pliku znajduje się w temacie [ http://json.schemastore.org/function ](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Obsługa błędów powiązań

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Linki do wszystkich tematów błędów dla różnych usług, które są obsługiwane przez usługę Functions, zobacz [powiązanie kody błędów](functions-bindings-error-pages.md#binding-error-codes) części [obsługi błędów usługi Azure Functions](functions-bindings-error-pages.md) temat.  

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat określonego powiązania na ten temat można znaleźć w następujących artykułach:

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
