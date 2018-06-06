---
title: Azure powiązania magazynu obiektów Blob dla usługi Azure Functions
description: Zrozumienie, jak używać magazynu obiektów Blob platformy Azure Usługa wyzwalaczy i powiązań w usługi Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: 85cdce312e141bee9da3b633c45dc770e503abfe
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724802"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure powiązania magazynu obiektów Blob dla usługi Azure Functions

W tym artykule opisano sposób pracy z usługi Azure Functions powiązania magazynu obiektów Blob platformy Azure. Usługi Azure Functions obsługuje wyzwolenia, danych wejściowych i wyjściowych powiązań dla obiektów blob. Artykuł zawiera sekcja dla każdego powiązania:

* [Wyzwalacz obiektów blob](#trigger)
* [Powiązania wejściowego obiektu blob](#input)
* [Powiązania wyjściowego obiektu blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użyj wyzwalacza zdarzenia siatki zamiast wyzwalacza magazynu obiektów Blob dla kont magazynu tylko do obiektów blob, wysokiej skali lub w celu uniknięcia zimnego opóźnień. Aby uzyskać więcej informacji, zobacz [wyzwalacza](#trigger) sekcji. 

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu obiektów Blob są udostępniane w [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania magazynu obiektów Blob są udostępniane w [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Blob) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz magazynu obiektów Blob uruchamia funkcję po wykryciu nowych lub zaktualizowanych obiektów blob. Zawartość obiektu blob są dostarczane jako dane wejściowe do funkcji.

[Wyzwalacz zdarzenia siatki](functions-bindings-event-grid.md) ma wbudowaną obsługę [obiektu blob zdarzenia](../storage/blobs/storage-blob-event-overview.md) i można również uruchomić funkcję o wykryciu nowych lub zaktualizowanych obiektów blob. Na przykład zobacz [siatki zdarzenie zmiany rozmiaru obrazu](../event-grid/resize-images-on-storage-blob-upload-event.md) samouczka.

Użyj siatki zdarzeń zamiast wyzwalacza magazynu obiektów Blob w następujących scenariuszach:

* Konta magazynu tylko do obiektów blob
* Wysoka skalowalność
* Opóźnienie zimnego

### <a name="blob-only-storage-accounts"></a>Konta magazynu tylko do obiektów blob

[Konta magazynu tylko do obiektów blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts) są obsługiwane dla obiekt blob danych wejściowych i wyjściowych powiązania, ale nie dla obiekt blob wyzwalaczy. Wyzwalacze magazynu obiektów blob wymagają konta magazynu ogólnego przeznaczenia.

### <a name="high-scale"></a>Wysoka skalowalność

Wysoka skalowalność można słabo zdefiniować jako kontenery zawierające ponad 100 000 obiektów blob w nich lub kont magazynu ma więcej niż 100 aktualizacji obiektu blob na sekundę.

### <a name="cold-start-delay"></a>Opóźnienie zimnego

Jeśli funkcja aplikacji znajduje się na plan zużycia, może istnieć maksymalnie 10-minutowych opóźnienia w przetwarzaniu nowe obiekty BLOB, jeśli aplikacja funkcji przeszedł bezczynności. Aby uniknąć tego opóźnienia zimnego, przełącz się do planu usługi App Service z na zawsze włączone lub użyj typu różnych wyzwalacza.

### <a name="queue-storage-trigger"></a>Kolejki magazynu wyzwalacza

Oprócz siatki zdarzeń alternatywą dla przetwarzania obiektów blob jest wyzwalacz kolejki magazynu, ale nie nie wbudowaną obsługę zdarzeń obiektu blob. Czy trzeba tworzyć wiadomości w kolejce podczas tworzenia lub aktualizowania obiektów blob. Na przykład, w którym przyjęto założenie, możesz to, zobacz [przykład powiązania wejściowego obiektu blob w dalszej części tego artykułu](#input---example).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który zapisuje dziennik obiektu blob jest dodane lub zaktualizowane w `samples-workitems` kontenera.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu blob `samples-workitems/{name}` tworzy [powiązanie wyrażenie](functions-triggers-bindings.md#binding-expressions-and-patterns) czy można użyć w kodzie funkcji na dostęp do nazwy pliku wyzwalająca obiektu blob. Aby uzyskać więcej informacji, zobacz [obiektu Blob wzorce nazw](#trigger---blob-name-patterns) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat `BlobTrigger` atrybutów, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacza obiektu blob powiązanie w *function.json* pliku i [skryptu C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `samples-workitems` kontenera.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Ciąg `{name}` w ścieżce wyzwalacza obiektu blob `samples-workitems/{name}` tworzy [powiązanie wyrażenie](functions-triggers-bindings.md#binding-expressions-and-patterns) czy można użyć w kodzie funkcji na dostęp do nazwy pliku wyzwalająca obiektu blob. Aby uzyskać więcej informacji, zobacz [obiektu Blob wzorce nazw](#trigger---blob-name-patterns) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat *function.json* właściwości pliku, zobacz [konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto C# kodu skryptu, który jest powiązany z `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Oto C# kodu skryptu, który jest powiązany z `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacza obiektu blob powiązanie w *function.json* pliku i [kod JavaScript](functions-reference-node.md) używającą powiązania. Jeśli obiekt blob jest dodane lub zaktualizowane w funkcji zapisuje dziennik `samples-workitems` kontenera.

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

Ciąg `{name}` w ścieżce wyzwalacza obiektu blob `samples-workitems/{name}` tworzy [powiązanie wyrażenie](functions-triggers-bindings.md#binding-expressions-and-patterns) czy można użyć w kodzie funkcji na dostęp do nazwy pliku wyzwalająca obiektu blob. Aby uzyskać więcej informacji, zobacz [obiektu Blob wzorce nazw](#trigger---blob-name-patterns) dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat *function.json* właściwości pliku, zobacz [konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), umożliwia skonfigurowanie wyzwalacza obiektu blob następujące atrybuty:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu ma ciąg ścieżki, który wskazuje kontenera, aby obejrzeć i opcjonalnie [wzorzec nazwy obiektu blob](#trigger---blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Pełny przykład, zobacz [wyzwalacza — przykład C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Udostępnia innym sposobem określania konta magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji, która zawiera parametry połączenia magazynu. Ten atrybut można stosować na poziomie klasy parametrów, metody lub. W poniższym przykładzie przedstawiono poziom klasy i metody:

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

Konta magazynu do użycia jest określany w następującej kolejności:

* `BlobTrigger` Atrybutu `Connection` właściwości.
* `StorageAccount` Atrybut zastosowany do tego samego parametru jako `BlobTrigger` atrybutu.
* `StorageAccount` Atrybut zastosowany do funkcji.
* `StorageAccount` Atrybut zastosowany do klasy.
* Domyślne konto magazynu dla funkcji aplikacji (ustawienie aplikacji "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `BlobTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | należy wybrać opcję `blobTrigger`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**direction** | Nie dotyczy | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. Wyjątki w [użycia](#trigger---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiektu blob w kodzie funkcji. | 
|**Ścieżka** | **BlobPath** |Kontener do monitorowania.  Może być [wzorzec nazwy obiektu blob](#trigger-blob-name-patterns). | 
|**Połączenia** | **Połączenia** | Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Ciąg połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu tylko do obiektów blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W języku C# i skryptu C# można użyć następujących typów parametru obiektu blob wyzwalająca:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO do serializacji w formacie JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednego z typów zestawu SDK usługi Magazyn i komunikat o błędzie, upewnij się, że odwołanie do [poprawną wersję zestawu SDK usługi Magazyn](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string`, `Byte[]`, lub POCO zaleca się tylko wtedy, jeśli rozmiar obiektu blob jest mały, jako blob całej zawartości są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używania `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżność](#trigger---concurrency-and-memory-usage) dalszej części tego artykułu.

W języku JavaScript, dostęp do danych wejściowych obiektu blob przy użyciu `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Wyzwalacz - wzorce nazw obiektów blob

Można określić wzorzec nazwy obiektów blob w `path` właściwości w *function.json* lub `BlobTrigger` atrybut konstruktora. Wzorzec nazwy mogą być [wyrażenie filtru lub powiązanie](functions-triggers-bindings.md#binding-expressions-and-patterns). Przykłady można znaleźć w poniższych sekcjach.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę i rozszerzenie pliku

Poniższy przykład przedstawia sposób powiązania osobno nazwę pliku obiektu blob i rozszerzenie:

```json
"path": "input/{blobname}.{blobextension}",
```
Jeśli nosi nazwę obiektu blob *Blob1.txt oryginalne*, wartości `blobname` i `blobextension` zmienne w kodzie funkcja *Blob1 oryginalne* i *txt*.

### <a name="filter-on-blob-name"></a>Filtrowanie według nazwy obiektu blob

Tylko dla obiektów blob w następujących wyzwalaczy przykład `input` kontenera, w którym rozpoczyna się od ciągu "oryginalny —":

```json
"path": "input/original-{name}",
```
 
Jeśli nazwa obiektu blob jest *Blob1.txt oryginalne*, wartość `name` zmienna w funkcji kodu jest `Blob1`.

### <a name="filter-on-file-type"></a>Filtrowanie według typu pliku

Poniższy przykład wyzwala tylko na *.png* plików:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrowanie według nawiasy klamrowe w nazwach plików

Aby wyszukać nawiasy klamrowe w nazwach plików, poza nawiasy klamrowe przy użyciu dwa nawiasy klamrowe. Następujące przykładowe filtry dla obiektów blob, które mają nawiasy klamrowe w nazwie:

```json
"path": "images/{{20140101}}-{name}",
```

Jeśli w nazwie obiektu blob  *{20140101}-soundfile.mp3*, `name` jest wartość zmiennej w kodzie funkcja *soundfile.mp3*. 

## <a name="trigger---metadata"></a>Wyzwalacz - metadanych

Wyzwalacz obiektów blob udostępnia kilka właściwości metadanych. Te właściwości mogą służyć jako część wyrażenia powiązania w pozostałych powiązaniach lub parametrów w kodzie. Te wartości mogą być tej samej semantyki jako [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) typu.

|Właściwość  |Typ  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalania obiektu blob.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu blob dla lokalizacji głównej.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Właściwości systemu obiektu blob. |
|`Metadata` |`IDictionary<string,string>`|Zdefiniowane przez użytkownika metadane obiektu blob.|

Na przykład poniższy skrypt języka C# i JavaScript przykłady dziennika ścieżki do obiektu blob wyzwalania, w tym kontenerze:

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Wyzwalacz — obiekt blob potwierdzenia

Środowisko uruchomieniowe usługi Azure Functions zapewnia, że żadna funkcja wyzwalacza obiektu blob jest wywoływana więcej niż raz dla tego samego obiektu blob nowe lub zaktualizowane. Aby określić wersję danego obiektu blob został przetworzony, przechowuje *obiektu blob potwierdzenia*.

Potwierdzenia w kontenerze o nazwie obiektu blob Azure magazynów funkcji *azure webjobs hostów* na koncie magazynu Azure dla aplikacji funkcji (zdefiniowane przez ustawienie aplikacji `AzureWebJobsStorage`). Potwierdzenie obiektu blob zawiera następujące informacje:

* Funkcja wyzwalana ("*&lt;funkcja Nazwa aplikacji >*. Funkcje.  *&lt;nazwy funkcji >*", na przykład:"MyFunctionApp.Functions.CopyBlob")
* Nazwa kontenera
* Typ obiektów blob ("BlockBlob" lub "PageBlob")
* Nazwa obiektu blob
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetworzenie obiektu blob, należy usunąć potwierdzenia obiektu blob dla tego obiektu blob z *azure webjobs hostów* kontenera ręcznie.

## <a name="trigger---poison-blobs"></a>Wyzwalacz - skażone obiektów blob

Jeśli funkcja wyzwalacza obiektu blob nie powiodło się dla danego obiektu blob usługi Azure Functions ponownych prób, które działać łącznie 5 razy domyślnie. 

Jeśli nie wszystkie próby 5, usługi Azure Functions dodaje komunikat do kolejki magazynu o nazwie *webjob blobtrigger-poison*. Komunikat z kolejki skażone obiektów blob jest obiekt JSON, który zawiera następujące właściwości:

* FunctionId (w formacie  *&lt;funkcja Nazwa aplikacji >*. Funkcje.  *&lt;nazwy funkcji >*)
* BlobType ("BlockBlob" lub "PageBlob")
* ContainerName
* BlobName
* Element ETag (identyfikator wersji obiektów blob, na przykład: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Wyzwalacz - współbieżności i użycie pamięci

Wyzwalacz obiektów blob używane wewnętrznie, kolejki, więc maksymalną liczbę wywołań funkcji współbieżnych jest kontrolowany przez [konfiguracji kolejek w host.json](functions-host-json.md#queues). Ustawienia domyślne ograniczenia współbieżności 24 wywołań. Ten limit dotyczy oddzielnie każdej funkcji, która używa wyzwalacza obiektu blob.

[Plan zużycie](functions-scale.md#how-the-consumption-plan-works) ogranicza aplikacji funkcji w jednej maszyny wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez każdego współbieżnie wykonywanego wystąpienia funkcji i funkcje wykonawcze. Jeśli funkcja wyzwalana blob ładuje całego obiektu blob w pamięci, maksymalna ilość pamięci, używaną przez tą funkcję tylko do obiektów BLOB to 24 * rozmiar maksymalny obiektu blob. Na przykład aplikacja funkcji z trzech funkcji wyzwalanych przez obiekt blob i domyślne ustawienia byłyby wirtualna maksymalną wartość współbieżności to 3 * 24 = 72 wywołań funkcji.

Funkcje kodu JavaScript ładowanie całego obiektu blob do pamięci i funkcje C# to zrobić, jeśli można powiązać `string`, `Byte[]`, lub POCO.

## <a name="trigger---polling"></a>Wyzwalanie - sondowania

Jeśli kontenera obiektów blob monitorowanych zawiera więcej niż 10 000 obiektów blob, funkcje skanowania środowiska uruchomieniowego plików dziennika do wyszukiwać nowych lub zmienionych obiektów blob. Ten proces może spowodować opóźnienia. Funkcja może nie pobrać są uruchamiane kilka minut lub dłużej po utworzeniu obiektu blob. Ponadto [magazynu dzienniki są tworzone na "optymalnego"](/rest/api/storageservices/About-Storage-Analytics-Logging) podstawy. Nie ma żadnej gwarancji, że wszystkie zdarzenia są przechwytywane. W niektórych warunkach Dzienniki mogą zostać pominięci. Jeśli potrzebujesz przetwarzania szybsze i bardziej niezawodny obiektów blob, należy rozważyć utworzenie [komunikatu w kolejce](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu blob. Następnie użyj [wyzwalacza kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacza obiektu blob do przetworzenia w obiekcie blob. Inną opcją jest użycie siatki zdarzeń; Zobacz samouczek [zmiana rozmiaru Automatyzacja przekazać obrazów przy użyciu siatki zdarzeń](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Dane wejściowe

Odczytywanie obiektów blob przy użyciu powiązania wejściowego magazynu obiektów Blob.

## <a name="input---example"></a>Dane wejściowe — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#input---c-example)
* [Skryptu C# (csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Dane wejściowe — przykład C#

Poniżej przedstawiono przykład [C# funkcja](functions-dotnet-class-library.md) używającą wyzwalacz kolejki i powiązanie blob danych wejściowych. Komunikat z kolejki zawiera nazwę obiektu blob, a funkcja rejestruje rozmiar obiektu blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [skryptu C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy kopię obiektu blob tekstu. Funkcja jest wyzwalany przez komunikat z kolejki, zawierający nazwę można skopiować obiektu blob. Nosi nazwę nowego obiektu blob *{originalblobname}-kopiowania*.

W *function.json* pliku `queueTrigger` metadanych jest używana do określenia nazwy obiektów blob w `path` właściwości:

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

Oto kod skryptu C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [kod JavaScript](functions-reference-node.md) używającą powiązania. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalany przez komunikat z kolejki, zawierający nazwę można skopiować obiektu blob. Nosi nazwę nowego obiektu blob *{originalblobname}-kopiowania*.

W *function.json* pliku `queueTrigger` metadanych jest używana do określenia nazwy obiektów blob w `path` właściwości:

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

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Konstruktor atrybutu ma ścieżki do obiektu blob i `FileAccess` parametr wskazujący odczytu lub zapisu, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Można użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | należy wybrać opcję `blob`. |
|**direction** | Nie dotyczy | należy wybrać opcję `in`. Wyjątki w [użycia](#input---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiektu blob w kodzie funkcji.|
|**Ścieżka** |**BlobPath** | Ścieżka do obiektu blob. | 
|**Połączenia** |**Połączenia**| Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Ciąg połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu tylko do obiektów blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będzie można odczytu lub zapisu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# i skryptu C# można użyć następujących typów parametru dla powiązania wejściowego obiektu blob:

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

<sup>1</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednego z typów zestawu SDK usługi Magazyn i komunikat o błędzie, upewnij się, że odwołanie do [poprawną wersję zestawu SDK usługi Magazyn](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko jeśli rozmiar obiektu blob jest mały, ponieważ zawartość obiektu blob całego są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używania `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżność](#trigger---concurrency-and-memory-usage) we wcześniejszej części tego artykułu.

W języku JavaScript, uzyskiwać dostęp za pomocą danych obiektów blob `context.bindings.<name from function.json>`.

## <a name="output"></a>Dane wyjściowe

Powiązania danych wyjściowych magazynu obiektów Blob umożliwia zapis obiektów blob.

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

Poniżej przedstawiono przykład [C# funkcja](functions-dotnet-class-library.md) używającą wyzwalacz obiektów blob i output dwa powiązania obiektu blob. Funkcja jest wyzwalany przez utworzenie obiektu blob obrazu w *przykładowe obrazy* kontenera. Tworzy kopie małych i średnich obiekt blob obrazu. 

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

W poniższym przykładzie przedstawiono obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* pliku i [skryptu C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy kopię obiektu blob tekstu. Funkcja jest wyzwalany przez komunikat z kolejki, zawierający nazwę można skopiować obiektu blob. Nosi nazwę nowego obiektu blob *{originalblobname}-kopiowania*.

W *function.json* pliku `queueTrigger` metadanych jest używana do określenia nazwy obiektów blob w `path` właściwości:

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

Oto kod skryptu C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

<!--Same example for input and output. -->

W poniższym przykładzie przedstawiono obiektu blob danych wejściowych i wyjściowych powiązania w *function.json* plików i [kod JavaScript] (funkcje — odwołanie node.md), która używa powiązania. Funkcja tworzy kopię obiektu blob. Funkcja jest wyzwalany przez komunikat z kolejki, zawierający nazwę można skopiować obiektu blob. Nosi nazwę nowego obiektu blob *{originalblobname}-kopiowania*.

W *function.json* pliku `queueTrigger` metadanych jest używana do określenia nazwy obiektów blob w `path` właściwości:

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

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Konstruktor atrybutu ma ścieżki do obiektu blob i `FileAccess` parametr wskazujący odczytu lub zapisu, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

Można użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Blob` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | należy wybrać opcję `blob`. |
|**direction** | Nie dotyczy | Należy wybrać opcję `out` dla powiązania danych wyjściowych. Wyjątki w [użycia](#output---usage) sekcji. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiektu blob w kodzie funkcji.  Ustaw `$return` odwoływać się do wartości zwracane funkcji.|
|**Ścieżka** |**BlobPath** | Ścieżka do obiektu blob. | 
|**Połączenia** |**Połączenia**| Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Ciąg połączenia nie może być dla konta magazynu ogólnego przeznaczenia, [konta magazynu tylko do obiektów blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będzie można odczytu lub zapisu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skryptu C# można powiązać z następujących typów do zapisywania obiektów blob:

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

<sup>1</sup> wymaga "powiązania w" `direction` w *function.json* lub `FileAccess.Read` w bibliotece klas C#. Można jednak użyć obiektu kontenera, który udostępnia środowisko uruchomieniowe do zapisu operacji, takich jak przekazywanie obiekty BLOB do kontenera.

<sup>2</sup> wymaga powiązania "inout" `direction` w *function.json* lub `FileAccess.ReadWrite` w bibliotece klas C#.

Jeśli spróbujesz powiązać z jednego z typów zestawu SDK usługi Magazyn i komunikat o błędzie, upewnij się, że odwołanie do [poprawną wersję zestawu SDK usługi Magazyn](#azure-storage-sdk-version-in-functions-1x).

W funkcji asynchronicznych, użyj wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

Powiązanie z `string` lub `Byte[]` jest zalecane tylko jeśli rozmiar obiektu blob jest mały, ponieważ zawartość obiektu blob całego są ładowane do pamięci. Ogólnie rzecz biorąc, zaleca się używania `Stream` lub `CloudBlockBlob` typu. Aby uzyskać więcej informacji, zobacz [użycia pamięci i współbieżność](#trigger---concurrency-and-memory-usage) we wcześniejszej części tego artykułu.


W języku JavaScript, uzyskiwać dostęp za pomocą danych obiektów blob `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Informacje ogólne |
|---|---|
| Obiekt blob | [Kody błędów obiektów blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt blob, tabeli, kolejki |  [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabeli, kolejki |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, który używa wyzwalacz magazynu obiektów Blob](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
