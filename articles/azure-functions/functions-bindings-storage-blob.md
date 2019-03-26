---
title: Usługa Azure powiązania magazynu obiektów Blob dla usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Blob storage wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: e18a63892f000eff0f72656082d5e6e1f0ca159b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437479"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Usługa Azure powiązania magazynu obiektów Blob dla usługi Azure Functions

W tym artykule wyjaśniono, jak pracować z powiązania magazynu obiektów Blob platformy Azure w usłudze Azure Functions. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązania dla obiektów blob. Artykuł zawiera sekcja dla każdego powiązania:

* [Obiekt blob wyzwalacza](#trigger)
* [Powiązania danych wejściowych obiektów blob](#input)
* [Obiekt blob powiązania danych wyjściowych](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Na użytek wyzwalacza usługi Event Grid zamiast wyzwalaczy magazynu obiektów Blob tylko do obiektów blob konta magazynu, na dużą skalę lub w celu zmniejszenia opóźnienia. Aby uzyskać więcej informacji, zobacz [wyzwalacza](#trigger) sekcji.

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu obiektów Blob znajdują się w [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania magazynu obiektów Blob znajdują się w [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz usługi Blob storage uruchamia funkcję po wykryciu nowych lub zaktualizowanych obiektów blob. Zawartość obiektu blob są dostarczane jako dane wejściowe funkcji.

[Wyzwalacza usługi Event Grid](functions-bindings-event-grid.md) ma wbudowaną obsługę [zdarzenia usługi blob](../storage/blobs/storage-blob-event-overview.md) i można również uruchomić funkcję o wykryciu nowych lub zaktualizowanych obiektów blob. Aby uzyskać przykład, zobacz [— zmiana rozmiaru obrazu za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) samouczka.

Użyj usługi Event Grid zamiast wyzwalacz usługi Blob storage w następujących scenariuszach:

* Konta usługi Blob Storage
* Wysoka skalowalność
* Minimalizuje opóźnienie

### <a name="blob-storage-accounts"></a>Konta usługi Blob Storage

[Konta usługi blob storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) są obsługiwane dla obiektu blob danych wejściowych i wyjściowych powiązania, ale nie dla wyzwalacze obiektów blob. Wyzwalaczy magazynu obiektów blob wymagają konta magazynu ogólnego przeznaczenia.

### <a name="high-scale"></a>Wysoka skalowalność

Zapewnia wysoką skalowalność, które mogą być swobodnie definiowane jako kontenery zawierające ponad 100 000 obiektów blob w nich lub kont magazynu, który ma więcej niż 100 aktualizacji obiektu blob na sekundę.

### <a name="latency-issues"></a>Problemów z opóźnieniem

W przypadku aplikacji funkcji w planie zużycie, może istnieć maksymalnie 10 minut opóźnienia w przetwarzaniu nowe obiekty BLOB, jeśli aplikacja funkcji przeszedł bezczynności. Aby uniknąć ten czas oczekiwania, możesz przełączyć do planu usługi App Service przy użyciu zawsze włączonej włączone. Można również użyć [wyzwalacza usługi Event Grid](functions-bindings-event-grid.md) przy użyciu konta usługi Blob storage. Aby uzyskać przykład, zobacz [samouczka usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Wyzwalacz kolejki usługi storage

Oprócz usługi Event Grid alternatywą dla przetwarzania obiektów blob jest wyzwalacz kolejki magazynu, ale go nie ma wbudowanej obsługi zdarzeń obiektu blob. Czy trzeba tworzyć wiadomości w kolejce podczas tworzenia lub aktualizowania obiektów blob. Aby uzyskać przykład, który przyjęto założenie, że zostały wykonane, zobacz [przykładowe powiązania danych wejściowych obiektów blob w dalszej części tego artykułu](#input---example).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , zapisuje dziennik, gdy obiekt blob jest dodane lub zaktualizowane w `samples-workitems` kontenera.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce wyzwalacz obiektu blob `samples-workitems/{name}` tworzy [powiązania wyrażeń](./functions-bindings-expressions-patterns.md) , można użyć w kodzie funkcji dostęp do nazwy pliku obiektu blob wyzwalania. Aby uzyskać więcej informacji, zobacz [Blob wzorce nazw](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat `BlobTrigger` atrybutów, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano obiekt blob wyzwalacza powiązanie w *function.json* pliku i [kodu w języku Python](functions-reference-python.md) powiązania, który używa. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `samples-workitems` [kontenera](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto powiązanie danych w *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacz obiektu blob `samples-workitems/{name}` tworzy [powiązania wyrażeń](./functions-bindings-expressions-patterns.md) , można użyć w kodzie funkcji dostęp do nazwy pliku obiektu blob wyzwalania. Aby uzyskać więcej informacji, zobacz [Blob wzorce nazw](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat *function.json* właściwości pliku, zobacz [konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto C# kod skryptu, który tworzy powiązanie z `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Oto C# kod skryptu, który tworzy powiązanie z `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano obiekt blob wyzwalacza powiązanie w *function.json* pliku i [kod JavaScript](functions-reference-node.md) powiązania, który używa. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `samples-workitems` kontenera.

Oto *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacz obiektu blob `samples-workitems/{name}` tworzy [powiązania wyrażeń](./functions-bindings-expressions-patterns.md) , można użyć w kodzie funkcji dostęp do nazwy pliku obiektu blob wyzwalania. Aby uzyskać więcej informacji, zobacz [Blob wzorce nazw](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat *function.json* właściwości pliku, zobacz [konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Wyzwalacz — przykładem w języku Python

W poniższym przykładzie pokazano obiekt blob wyzwalacza powiązanie w *function.json* pliku i [kodu w języku Python](functions-reference-python.md) powiązania, który używa. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `samples-workitems` [kontenera](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto *function.json* pliku:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacz obiektu blob `samples-workitems/{name}` tworzy [powiązania wyrażeń](./functions-bindings-expressions-patterns.md) , można użyć w kodzie funkcji dostęp do nazwy pliku obiektu blob wyzwalania. Aby uzyskać więcej informacji, zobacz [Blob wzorce nazw](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat *function.json* właściwości pliku, zobacz [konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie pokazano obiekt blob wyzwalacza powiązanie w *function.json* pliku i [kodu Java](functions-reference-java.md) powiązania, który używa. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `myblob` kontenera.

Oto *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Oto kodu Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyć następujących atrybutów, aby skonfigurować wyzwalacz obiektu blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu ma ciąg ścieżki, która wskazuje kontenera, aby obejrzeć i opcjonalnie [wzorzec nazwy obiektu blob](#trigger---blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Udostępnia inny sposób, aby określić konto magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji zawierającego parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Konto magazynu do użycia, jest określana w następującej kolejności:

* `BlobTrigger` Atrybutu `Connection` właściwości.
* `StorageAccount` Zastosowany do tego samego parametru jako `BlobTrigger` atrybutu.
* `StorageAccount` Zastosowany do funkcji.
* `StorageAccount` Zastosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `BlobTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `blobTrigger`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. Wyjątki są zaznaczone w [użycia](#trigger---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt blob w kodzie funkcji. |
|**path** | **BlobPath** |[Kontenera](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) do monitorowania.  Może być [wzorzec nazwy obiektu blob](#trigger---blob-name-patterns). |
|**połączenia** | **Połączenie** | Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.<br><br>Parametry połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu obiektów Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W języku C# i skrypt języka C# można użyć następujących typów parametru wyzwalająca obiektu blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO możliwy do serializacji w formacie JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać do jednego z typów z zestawu SDK usługi Storage, a komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string`, `Byte[]`, lub POCO jest zalecane tylko, jeśli rozmiar obiektu blob jest mała, jako blob całej zawartości są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używać `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżności](#trigger---concurrency-and-memory-usage) w dalszej części tego artykułu.

W języku JavaScript, dostęp do danych wejściowych obiektów blob za pomocą `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Wyzwalacz — wzorce nazw obiektów blob

Można określić wzorzec nazw obiektów blob w `path` właściwość *function.json* lub `BlobTrigger` atrybut konstruktora. Wzorzec nazwy mogą być [wyrażenia filtru lub powiązania](./functions-bindings-expressions-patterns.md). Przykłady można znaleźć w poniższych sekcjach.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę i rozszerzenie pliku

Poniższy przykład pokazuje, jak można powiązać nazwy pliku obiektu blob i rozszerzenia oddzielnie:

```json
"path": "input/{blobname}.{blobextension}",
```
Jeśli obiekt blob ma nazwę *Blob1.txt oryginalny*, wartości `blobname` i `blobextension` zmiennych w kodzie funkcji są *azureblob1 oryginalny* i *txt*.

### <a name="filter-on-blob-name"></a>Filtrowanie według nazwy obiektu blob

Następujących wyzwalaczy przykładzie obiekty BLOB, na `input` kontener, który rozpoczyna się od ciągu "oryginalny-":

```json
"path": "input/original-{name}",
```

Jeśli nazwa obiektu blob jest *Blob1.txt oryginalny*, wartość `name` zmiennej w kodzie funkcji jest `Blob1`.

### <a name="filter-on-file-type"></a>Filtrowanie według typu pliku

Poniższy przykład wyzwala wyłącznie w systemach *.png* plików:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrowanie według nawiasów klamrowych w nazwach plików

Aby wyszukać nawiasów klamrowych w nazwach plików, ucieczki nawiasy klamrowe przy użyciu dwóch nawiasów klamrowych. Następujące filtry przykładzie obiekty BLOB, mających nawiasów klamrowych w nazwie:

```json
"path": "images/{{20140101}}-{name}",
```

Jeśli obiekt blob ma nazwę  *{20140101}-soundfile.mp3*, `name` jest wartość zmiennej w kodzie funkcji *soundfile.mp3*.

## <a name="trigger---metadata"></a>Wyzwalacz - metadanych

Wyzwalacz obiektu blob zawiera kilka właściwości metadanych. Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Te wartości mogą być tą samą semantyką jako [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typu.

|Właściwość  |Typ  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalania obiektu blob.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu blob dla lokalizacji głównej.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Właściwości systemu obiektu blob. |
|`Metadata` |`IDictionary<string,string>`|Metadane zdefiniowane przez użytkownika dla obiektu blob.|

Na przykład poniższy skrypt języka C# i przykładów kodu JavaScript dziennika ścieżki do wyzwalania obiektu blob, w tym kontenerze:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Wyzwalacz — potwierdzenia obiektów blob

Środowisko uruchomieniowe usługi Azure Functions zapewnia, że żadna funkcja wyzwalacz obiektu blob jest wywoływana więcej niż jeden raz dla tego samego obiektu blob nowych lub zaktualizowanych. Aby określić wersję danego obiektu blob został przetworzony, przechowuje *blob potwierdzenia*.

Usługa Azure Functions magazynów obiektów blob potwierdzenia w kontenerze o nazwie *azure webjobs hostów* koncie magazynu platformy Azure dla aplikacji funkcji (zdefiniowany przez ustawienie aplikacji `AzureWebJobsStorage`). Odbieranie obiektów blob zawiera następujące informacje:

* Funkcja wyzwalana ("*&lt;nazwę aplikacji funkcji >*. Funkcje.  *&lt;nazwa funkcji >*", na przykład: "MyFunctionApp.Functions.CopyBlob")
* Nazwa kontenera
* Typ obiektu blob ("BlockBlob" lub "PageBlob")
* Nazwa obiektu blob
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetworzenie obiektu blob, należy usunąć potwierdzenia obiektów blob, dla tego obiektu blob z *azure webjobs hostów* kontenera ręcznie.

## <a name="trigger---poison-blobs"></a>Wyzwalacz - skażone obiektów blob

Jeśli obiekt blob funkcji wyzwalacza nie powiodło się dla danego obiektu blob usługi Azure Functions ponownych prób, które działają 5 razy w sumie domyślnie.

W przypadku awarii wszystkich prób 5, usługi Azure Functions dodaje komunikat do kolejki magazynu o nazwie *webjobs-blobtrigger-poison*. Komunikat w kolejce dla obiektów blob poison to obiekt JSON, który zawiera następujące właściwości:

* FunctionId (w formacie  *&lt;nazwę aplikacji funkcji >*. Funkcje.  *&lt;nazwa funkcji >*)
* BlobType ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* BlobName
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Wyzwalacz — zużycie pamięci i współbieżności

Obiekt blob wyzwalacza używa wewnętrznie, kolejki, dzięki czemu steruje maksymalną liczbę wywołań funkcji współbieżnych [konfiguracji kolejki w host.json](functions-host-json.md#queues). Ustawienia domyślne ograniczenie współbieżności 24 wywołań. Ten limit dotyczy osobno każdy funkcję, która używa wyzwalacz obiektu blob.

[Plan zużycie](functions-scale.md#how-the-consumption-plan-works) ogranicza aplikację funkcji na jednej maszynie wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez każdego współbieżnie wykonywanego wystąpienia funkcji i samo środowisko uruchomieniowe funkcji. Jeśli funkcji wyzwalanej przez obiekt blob ładuje cały obiekt blob do pamięci, maksymalny rozmiar pamięci używane przez tę funkcję tylko dla obiektów BLOB to 24 * rozmiar maksymalny obiektu blob. Na przykład aplikacji funkcji przy użyciu trzech funkcji wyzwalanej przez obiekt blob i domyślne ustawienia miałby VM maksymalną wartość współbieżności to 3 * 24 = 72 wywołania funkcji.

Funkcje języka JavaScript i Java załadować cały obiekt blob do pamięci, a C# funkcje to zrobić, jeśli powiązana z `string`, `Byte[]`, lub POCO.

## <a name="trigger---polling"></a>Wyzwalanie - sondowania

Jeśli kontener obiektów blob, które są monitorowane zawiera ponad 10 000 obiektów blob (we wszystkich kontenerów), funkcje skanowania środowiska uruchomieniowego plików dziennika do obserwacji nowych lub zmienionych obiektów blob. Ten proces może powodować opóźnienia. Funkcja może nie wyzwalane do kilku minut lub dłużej po utworzeniu obiektu blob. Ponadto [dzienników magazynu są tworzone na "starań"](/rest/api/storageservices/About-Storage-Analytics-Logging) podstawy. Nie ma żadnej gwarancji, że wszystkie zdarzenia są przechwytywane. W niektórych warunkach Dzienniki mogą zostać pominięci.

Jeśli konieczne jest przetworzenie szybsze i bardziej niezawodne obiektów blob, należy rozważyć utworzenie [komunikatu w kolejce](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu blob. Następnie użyj [wyzwalacz kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacz obiektu blob do przetwarzania obiektu blob. Innym rozwiązaniem jest użycie usługi Event Grid; Zapoznaj się z samouczkiem [automatyzacja zmiany rozmiaru przekazanych obrazów, które przy użyciu usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych magazynu obiektów Blob do odczytu obiektów blob.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#input---c-example)
* [Skryptu C# (csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Dane wejściowe — przykład w języku C#

Poniższy przykład jest [funkcja języka C#](functions-dotnet-class-library.md) używającej wyzwalacz kolejki i powiązania wejściowego obiektu blob. Komunikat w kolejce zawiera nazwy obiektu blob, a funkcja rejestruje rozmiar obiektu blob.

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

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [skrypt języka C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy kopię tekstu obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [kod JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Dane wejściowe - przykładem w języku Python

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [kodu w języku Python](functions-reference-python.md) powiązania, który używa. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#input---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Dane wejściowe - przykładów w języku Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, nazwa obiektu blob z ciągu zapytania wyszukiwania](#http-trigger-look-up-blob-name-from-query-string-java)
* [Wyzwalacz kolejki, odbierać nazwa obiektu blob z komunikatu w kolejce](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Wyzwalacz HTTP, wyszukiwanie nazwy obiektu blob z ciągu zapytania (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```HttpTrigger``` adnotacji, aby otrzymać parametr zawierający nazwę pliku w kontenerze magazynu obiektów blob. ```BlobInput``` Adnotacji, a następnie odczytuje plik i przekazuje jego zawartość, aby działały jak ```byte[]```.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Wyzwalacz kolejki, odbierać nazwa obiektu blob z komunikatu w kolejce (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```QueueTrigger``` adnotacji, aby otrzymać komunikat zawierający nazwę pliku w kontenerze magazynu obiektów blob. ```BlobInput``` Adnotacji, a następnie odczytuje plik i przekazuje jego zawartość, aby działały jak ```byte[]```.

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

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@BlobInput` adnotacji w parametrach przybyły, którego wartość z obiektu blob.  Ta adnotacja mogą być używane z typami natywnymi Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu `Optional<T>`.

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Ten atrybut Konstruktor przyjmuje ścieżkę do obiektu blob i `FileAccess` parametr wskazujący odczytu lub zapisu, jak pokazano w poniższym przykładzie:

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

Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

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

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `blob`. |
|**direction** | Nie dotyczy | Musi być równa `in`. Wyjątki są zaznaczone w [użycia](#input---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt blob w kodzie funkcji.|
|**path** |**BlobPath** | Ścieżka do obiektu blob. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji, która zawiera [parametry połączenia magazynu](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.<br><br>Parametry połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu tylko dla obiektów blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będzie można Odczyt lub zapis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# i skrypt języka C# można użyć następujących typów parametrów dla powiązania danych wejściowych obiektów blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać do jednego z typów z zestawu SDK usługi Storage, a komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko jeśli rozmiar obiektu blob jest mała, ponieważ zawartość całego obiektu blob są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używać `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżności](#trigger---concurrency-and-memory-usage) we wcześniejszej części tego artykułu.

W języku JavaScript, uzyskiwać dostęp za pomocą danych obiektu blob `context.bindings.<name from function.json>`.

## <a name="output"></a>Dane wyjściowe

Używanie powiązania danych wyjściowych magazynu obiektów Blob do napisania obiektów blob.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

Poniższy przykład jest [funkcja języka C#](functions-dotnet-class-library.md) wyzwalacz obiektu blob, który używa i dwa powiązania obiektu blob w danych wyjściowych. Funkcja jest wyzwalana przez utworzenie obiektu blob obrazu w *przykładowe obrazy* kontenera. Tworzy kopie małych i średnich obiekt blob obrazu.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [skrypt języka C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy kopię tekstu obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [kod JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Dane wyjściowe — przykładem w języku Python

<!--Same example for input and output. -->

W poniższym przykładzie pokazano obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [kodu w języku Python](functions-reference-python.md) powiązania, który używa. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalana przez komunikatu w kolejce, który zawiera nazwę obiektu blob do skopiowania. Nowy obiekt blob o nazwie *{originalblobname} — kopia*.

W *function.json* pliku `queueTrigger` właściwość metadanych służy do określania nazwy obiektu blob w `path` właściwości:

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

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Dane wyjściowe — przykładów w języku Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, za pomocą OutputBinding](#http-trigger-using-outputbinding-java)
* [Wyzwalacz kolejki, przy użyciu wartości zwracanej funkcji](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Wyzwalacz HTTP, za pomocą OutputBinding (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```HttpTrigger``` adnotacji, aby otrzymać parametr zawierający nazwę pliku w kontenerze magazynu obiektów blob. ```BlobInput``` Adnotacji, a następnie odczytuje plik i przekazuje jego zawartość, aby działały jak ```byte[]```. ```BlobOutput``` Wiąże adnotacji ```OutputBinding outputItem```, który jest następnie używany przez funkcję do zapisywania zawartości wejściowego obiektu blob do kontenera magazynu skonfigurowanych.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Wyzwalacz kolejki, przy użyciu wartości zwracanej funkcji (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```QueueTrigger``` adnotacji, aby otrzymać komunikat zawierający nazwę pliku w kontenerze magazynu obiektów blob. ```BlobInput``` Adnotacji, a następnie odczytuje plik i przekazuje jego zawartość, aby działały jak ```byte[]```. ```BlobOutput``` Adnotacji wiąże zwracanej wartości funkcji, która jest następnie używany przez środowisko uruchomieniowe można zapisać zawartości wejściowego obiektu blob do kontenera magazynu skonfigurowanych.

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

 W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@BlobOutput` adnotacja dla parametrów funkcji, której wartość będzie zapisany do obiektu w usłudze blob storage.  Typ parametru musi być `OutputBinding<T>`, gdzie T jest dowolnego typu natywnego języka Java lub obiektu typu POJO.

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Ten atrybut Konstruktor przyjmuje ścieżkę do obiektu blob i `FileAccess` parametr wskazujący odczytu lub zapisu, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `blob`. |
|**direction** | Nie dotyczy | Musi być równa `out` dla powiązania danych wyjściowych. Wyjątki są zaznaczone w [użycia](#output---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt blob w kodzie funkcji.  Ustaw `$return` odwoływać się do wartości zwracanej funkcji.|
|**path** |**BlobPath** | Ścieżka do blobco. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.<br><br>Parametry połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu tylko dla obiektów blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będzie można Odczyt lub zapis. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skrypt języka C# można powiązać następujących typów do zapisywania obiektów blob:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> wymaga "powiązania in" `direction` w *function.json* lub `FileAccess.Read` w bibliotece klas języka C#. Jednak można użyć obiektu kontenera, zapewniająca środowisko uruchomieniowe do zapisu operacji, takich jak przekazywanie obiektów blob do kontenera.

<sup>2</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas języka C#.

Jeśli próbujesz powiązać do jednego z typów z zestawu SDK usługi Storage, a komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

W funkcji asynchronicznej, użyj wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

Powiązanie z `string` lub `Byte[]` jest zalecane tylko jeśli rozmiar obiektu blob jest mała, ponieważ zawartość całego obiektu blob są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używać `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżności](#trigger---concurrency-and-memory-usage) we wcześniejszej części tego artykułu.


W języku JavaScript, uzyskiwać dostęp za pomocą danych obiektu blob `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Informacje ogólne |
|---|---|
| Obiekt blob | [Kody błędów obiektu blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt blob, tabela, kolejka |  [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
