---
title: Powiązania magazynu obiektów blob platformy Azure dla Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań usługi Azure Blob Storage w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 84e4cc69d173fd37ecd15f537feadf19c275ef34
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086211"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Powiązania magazynu obiektów blob platformy Azure dla Azure Functions

W tym artykule opisano sposób pracy z powiązaniami usługi Azure Blob Storage w Azure Functions. Azure Functions obsługuje powiązania wyzwalacza, danych wejściowych i wyjściowych dla obiektów BLOB. Artykuł zawiera sekcję dla każdego powiązania:

* [Wyzwalacz obiektów BLOB](#trigger)
* [Powiązanie danych wejściowych obiektu BLOB](#input)
* [Powiązanie danych wyjściowych obiektu BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użyj wyzwalacza Event Grid zamiast wyzwalacza usługi BLOB Storage dla kont magazynu obsługującego tylko obiekty blob, w przypadku dużej skali lub w celu zmniejszenia opóźnień. Aby uzyskać więcej informacji, zobacz [](#trigger) sekcję wyzwalacza.

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu obiektów BLOB są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania magazynu obiektów BLOB są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz magazynu obiektów BLOB uruchamia funkcję po wykryciu nowego lub zaktualizowanego obiektu BLOB. Zawartość obiektu BLOB jest dostarczana jako dane wejściowe do funkcji.

[Wyzwalacz Event Grid](functions-bindings-event-grid.md) ma wbudowaną obsługę [zdarzeń obiektów BLOB](../storage/blobs/storage-blob-event-overview.md) i może być również używany do uruchamiania funkcji w przypadku wykrycia nowego lub zaktualizowanego obiektu BLOB. Aby zapoznać się z przykładem, zapoznaj się z samouczkiem [Zmienianie rozmiaru obrazu za pomocą Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) .

Użyj Event Grid zamiast wyzwalacza usługi BLOB Storage dla następujących scenariuszy:

* Konta usługi Blob Storage
* Wysoka Skala
* Minimalizacja opóźnień

### <a name="blob-storage-accounts"></a>Konta usługi Blob Storage

[Konta usługi BLOB Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts) są obsługiwane przez powiązania danych wejściowych i wyjściowych obiektów blob, ale nie dla wyzwalaczy obiektów BLOB. Wyzwalacze magazynu obiektów BLOB wymagają konta magazynu ogólnego przeznaczenia.

### <a name="high-scale"></a>Wysoka Skala

Wysoką skalę można swobodnie zdefiniować jako kontenery, które mają więcej niż 100 000 obiektów BLOB w ramach kont magazynu, które mają więcej niż 100 aktualizacji obiektów BLOB na sekundę.

### <a name="latency-issues"></a>Problemy z opóźnieniami

Jeśli aplikacja funkcji jest w planie zużycia, może wystąpić maksymalnie 10 minut opóźnienia w przetwarzaniu nowych obiektów blob, jeśli aplikacja funkcji przestała być bezczynna. Aby uniknąć tego opóźnienia, możesz przejść do planu App Service z opcją zawsze włączone. Możesz również użyć wyzwalacza [Event Grid](functions-bindings-event-grid.md) z kontem magazynu obiektów BLOB. Aby zapoznać się z przykładem, zobacz [samouczek Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Wyzwalacz magazynu kolejki

Oprócz Event Grid, kolejną alternatywą dla przetwarzania obiektów BLOB jest wyzwalacz magazynu kolejki, ale nie ma wbudowanej obsługi dla zdarzeń obiektów BLOB. Podczas tworzenia lub aktualizowania obiektów BLOB konieczne będzie utworzenie komunikatów w kolejce. Aby zapoznać się z przykładem przyjętym przez Ciebie, zobacz [przykład powiązania danych wejściowych obiektu BLOB w dalszej części tego artykułu](#input---example).

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która zapisuje dziennik w przypadku dodania lub `samples-workitems` zaktualizowania obiektu BLOB w kontenerze.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu BLOB tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na `BlobTrigger` temat atrybutu, zobacz [wyzwalacz-Attributes](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

Poniższy przykład przedstawia powiązanie wyzwalacza obiektów BLOB w pliku *Function. JSON* i [kodu Python](functions-reference-python.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu BLOB tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#trigger---configuration) objaśnia te właściwości.

Kod skryptu C# , który jest powiązany z `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Kod skryptu C# , który jest powiązany z `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

Poniższy przykład przedstawia powiązanie wyzwalacza obiektów BLOB w pliku *Function. JSON* i [kodu JavaScript](functions-reference-node.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` kontenerze.

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu BLOB tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#trigger---configuration) objaśnia te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Wyzwalacz — przykład w języku Python

Poniższy przykład przedstawia powiązanie wyzwalacza obiektów BLOB w pliku *Function. JSON* i [kodu Python](functions-reference-python.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu BLOB tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#trigger---blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#trigger---configuration) objaśnia te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

Poniższy przykład przedstawia powiązanie wyzwalacza obiektu BLOB w pliku *Function. JSON* i [kodzie Java](functions-reference-java.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `myblob` kontenerze.

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

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz obiektu BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje ciąg ścieżki wskazujący, że kontener ma być obserwowany i opcjonalnie [wzorzec nazwy obiektu BLOB](#trigger---blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

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

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

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

Konto magazynu do użycia jest określane w następującej kolejności:

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
|**direction** | Nie dotyczy | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. Wyjątki są zanotowane w sekcji [użycie](#trigger---usage) . |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji. |
|**Ścieżka** | **BlobPath** |[Kontener](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) do monitorowania.  Może być [wzorcem nazw obiektów BLOB](#trigger---blob-name-patterns). |
|**połączenia** | **połączenia** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem usługi BLOB Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W C# skryptach i C# można użyć następujących typów parametrów dla wyzwalanego obiektu BLOB:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* POCO możliwy do serializacji jako kod JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece C# klas.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string`, `Byte[]`lub poco jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` typu lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](#trigger---concurrency-and-memory-usage) w dalszej części tego artykułu.

W języku JavaScript dostęp do danych wejściowych obiektów BLOB `context.bindings.<name from function.json>`za pomocą polecenia.

## <a name="trigger---blob-name-patterns"></a>Wyzwalacz — wzorce nazw obiektów BLOB

Można określić wzorzec nazwy obiektu BLOB we `path` właściwości w *funkcji Function. JSON* lub w `BlobTrigger` konstruktorze atrybutu. Wzorzec nazwy może być wyrażeniem [filtru lub powiązania](./functions-bindings-expressions-patterns.md). W poniższych sekcjach przedstawiono przykłady.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę i rozszerzenie pliku

Poniższy przykład pokazuje, jak powiązać z nazwą pliku i rozszerzeniem obiektu BLOB osobno:

```json
"path": "input/{blobname}.{blobextension}",
```
Jeśli obiekt BLOB ma nazwę *Original-Blob1. txt*, wartości `blobname` zmiennych `blobextension` i w kodzie funkcji są *oryginalne-Blob1* i *txt*.

### <a name="filter-on-blob-name"></a>Filtruj według nazwy obiektu BLOB

Poniższy przykład wyzwala wyzwalacze tylko w obiektach `input` BLOB w kontenerze, które zaczynają się od ciągu "Original-":

```json
"path": "input/original-{name}",
```

Jeśli obiekt BLOB ma nazwę *Original-Blob1. txt*, wartość `name` zmiennej w kodzie funkcji to `Blob1`.

### <a name="filter-on-file-type"></a>Filtruj według typu pliku

Poniższy przykład wyzwala tylko na plikach *PNG* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtruj dla nawiasów klamrowych w nazwach plików

Aby wyszukać nawiasy klamrowe w nazwach plików, należy wprowadzić nawiasy klamrowe przy użyciu dwóch nawiasów klamrowych. Poniższe przykładowe filtry dla obiektów blob, które mają nawiasy klamrowe w nazwie:

```json
"path": "images/{{20140101}}-{name}",
```

Jeśli obiekt BLOB ma `name` nazwę  *{20140101}-soundfile. mp3*, wartość zmiennej w kodzie funkcji to *soundfile. mp3*.

## <a name="trigger---metadata"></a>Wyzwalacz — metadane

Wyzwalacz obiektów BLOB zawiera kilka właściwości metadanych. Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Te wartości mają tę samą semantykę co typ [polecenia cloudblob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Właściwość  |Type  |Opis  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Ścieżka do wyzwalającego obiektu BLOB.|
|`Uri`|`System.Uri`|Identyfikator URI obiektu BLOB dla lokalizacji podstawowej.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Właściwości systemu obiektu BLOB. |
|`Metadata` |`IDictionary<string,string>`|Metadane zdefiniowane przez użytkownika dla obiektu BLOB.|

Na przykład poniższy C# skrypt i przykłady JavaScript rejestrują ścieżkę do obiektu BLOB wyzwalającego, łącznie z kontenerem:

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

## <a name="trigger---blob-receipts"></a>Wyzwalacze — potwierdzenia obiektów BLOB

Środowisko uruchomieniowe Azure Functions zapewnia, że żadna funkcja wyzwalacza obiektu BLOB nie zostanie wywołana więcej niż raz dla tego samego nowego lub zaktualizowanego obiektu BLOB. Aby określić, czy dana wersja obiektu BLOB została przetworzona, przechowuje *potwierdzenia obiektów BLOB*.

Azure Functions przechowuje potwierdzenia obiektów BLOB w kontenerze o nazwie *Azure-WebJobs-hosty* na koncie usługi Azure Storage dla aplikacji funkcji (zdefiniowanej przez ustawienie `AzureWebJobsStorage`aplikacji). Potwierdzenie obiektu BLOB zawiera następujące informacje:

* Funkcja wyzwalana (" *&lt;nazwa aplikacji funkcji >* . Obowiązki. Nazwa funkcji > ", na przykład:  *&lt;* "MyFunctionApp.Functions.CopyBlob")
* Nazwa kontenera
* Typ obiektu BLOB ("BlockBlob" lub "PageBlob")
* Nazwa obiektu BLOB
* Element ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetwarzanie obiektu BLOB, Usuń potwierdzenie obiektu BLOB dla tego obiektu BLOB z kontenera *usługi Azure-WebJobs-hosts* ręcznie. Proces ponownego przetwarzania może nie nastąpić natychmiast, dlatego ma miejsce w późniejszym czasie.

## <a name="trigger---poison-blobs"></a>Wyzwalanie trujących obiektów BLOB

Gdy funkcja wyzwalacza obiektu BLOB kończy się niepowodzeniem dla danego obiektu BLOB, Azure Functions ponownych prób, które domyślnie działają łącznie 5 razy.

W przypadku niepowodzenia wszystkich 5 prób Azure Functions dodaje komunikat do kolejki magazynu o nazwie *WebJobs-blobtrigger-trujące*. Komunikat w kolejce dla trujących obiektów BLOB jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w formacie  *&lt;nazwa aplikacji funkcji >* . Obowiązki. Nazwa funkcji >)  *&lt;*
* Blobtype ("BlockBlob" lub "PageBlob")
* ContainerName
* BlobName
* ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Wyzwalacz-współbieżność i użycie pamięci

Wyzwalacz obiektów BLOB używa kolejki wewnętrznie, więc Maksymalna liczba współbieżnych wywołań funkcji jest kontrolowana przez [konfigurację kolejek w pliku host. JSON](functions-host-json.md#queues). Ustawienia domyślne ograniczają współbieżność do 24 wywołań. Ten limit dotyczy osobno każdej funkcji, która używa wyzwalacza obiektu BLOB.

[Plan zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work) ogranicza aplikację funkcji na jednej maszynie wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez wszystkie jednocześnie wykonywane wystąpienia funkcji i przez sam czas wykonywania funkcji. Jeśli funkcja wyzwalana przez obiekt BLOB ładuje cały obiekt BLOB do pamięci, maksymalna ilość pamięci używana przez tę funkcję tylko dla obiektów BLOB to 24 * maksymalny rozmiar obiektu BLOB. Na przykład aplikacja funkcji mająca trzy funkcje wyzwalane przez obiekt BLOB i ustawienia domyślne byłyby w maksymalnym zakresie współbieżności maszyny wirtualnej równym 3 * 24 = 72 wywołań funkcji.

Funkcje języka JavaScript i języka Java ładują cały obiekt BLOB do C# pamięci i działają tak, jak w `string`przypadku `Byte[]`powiązania z, lub poco.

## <a name="trigger---polling"></a>Wyzwalanie sondowania

Jeśli monitorowany kontener obiektów BLOB zawiera więcej niż 10 000 obiektów BLOB (między wszystkimi kontenerami), środowisko uruchomieniowe funkcji skanuje pliki dziennika, aby obejrzeć nowe lub zmienione obiekty blob. Ten proces może spowodować opóźnienia. Funkcja może nie zostać wyzwolona do kilku minut lub dłużej po utworzeniu obiektu BLOB.

> [!WARNING]
> Ponadto [dzienniki magazynu są tworzone na podstawie najlepszego nakładu pracy](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nie ma gwarancji, że wszystkie zdarzenia są przechwytywane. W pewnych warunkach dzienniki mogą być pominięte.
> 
> Jeśli potrzebujesz szybszego lub bardziej niezawodnego przetwarzania obiektów blob, rozważ utworzenie [komunikatu w kolejce](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu BLOB. Następnie przetworzyć obiekt BLOB przy użyciu [wyzwalacza kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacza obiektu BLOB. Innym rozwiązaniem jest użycie Event Grid; Zapoznaj się z samouczkiem [Automatyzowanie zmiany rozmiarów przekazanych obrazów przy użyciu Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych magazynu obiektów blob, aby odczytać obiekty blob.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#input---c-example)
* [Skryptu C# (csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Dane wejściowe C# — przykład

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

### <a name="input---c-script-example"></a>Przykładowy C# skrypt wejściowy

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [ C# kodzie skryptu (. CSX)](functions-reference-csharp.md) , który używa powiązań. Funkcja tworzy kopię tekstu obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

### <a name="input---javascript-example"></a>Przykład Input-JavaScript

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie JavaScript](functions-reference-node.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

### <a name="input---python-example"></a>Przykład Input-Python

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie Python](functions-reference-python.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Input — przykłady dla środowiska Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania](#http-trigger-look-up-blob-name-from-query-string-java)
* [Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```HttpTrigger``` adnotacji do otrzymywania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji ```byte[]```jako. ```BlobInput```

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki (Java)

 Poniższy przykład pokazuje funkcję języka Java, która używa ```QueueTrigger``` adnotacji do odbierania komunikatu zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji ```byte[]```jako. ```BlobInput```

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

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@BlobInput` adnotacji w parametrach, których wartość pochodzi z obiektu BLOB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null `Optional<T>`przy użyciu.

## <a name="input---attributes"></a>Dane wejściowe — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i `FileAccess` parametr wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

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

Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

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
|**direction** | Nie dotyczy | Musi być równa `in`. Wyjątki są zanotowane w sekcji [użycie](#input---usage) . |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.|
|**Ścieżka** |**BlobPath** | Ścieżka do obiektu BLOB. |
|**połączenia** |**połączenia**| Nazwa ustawienia aplikacji, które zawiera [Parametry połączenia magazynu](../storage/common/storage-configure-connection-string.md) , które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

W C# skryptach i C# można użyć następujących typów parametrów dla powiązania danych wejściowych obiektów blob:

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

<sup>1</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece C# klas.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` typu lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](#trigger---concurrency-and-memory-usage) wcześniej w tym artykule.

W języku JavaScript Uzyskuj dostęp do danych obiektów `context.bindings.<name from function.json>`BLOB za pomocą polecenia.

## <a name="output"></a>Output

Używaj powiązań wyjściowych magazynu obiektów BLOB do zapisywania obiektów BLOB.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

Poniższy przykład to [ C# funkcja](functions-dotnet-class-library.md) , która używa wyzwalacza obiektu BLOB i dwóch wychodzących powiązań obiektów BLOB. Funkcja jest wyzwalana przez utworzenie obiektu BLOB obrazu w kontenerze *przykładowych obrazów* . Tworzy on małe i średnie kopie obiektu BLOB obrazu.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [ C# kodzie skryptu (. CSX)](functions-reference-csharp.md) , który używa powiązań. Funkcja tworzy kopię tekstu obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie JavaScript](functions-reference-node.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

### <a name="output---python-example"></a>Dane wyjściowe — przykład w języku Python

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w pliku *Function. JSON* i [kodzie Python](functions-reference-python.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W `queueTrigger` pliku *Function. JSON* Właściwość metadanych służy do określania nazwy `path` obiektu BLOB we właściwościach:

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

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Dane wyjściowe — przykłady dla środowiska Java

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz http, przy użyciubinding](#http-trigger-using-outputbinding-java)
* [Wyzwalacz kolejki, używanie wartości zwracanej funkcji](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Wyzwalacz http przy użyciubinding (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa ```HttpTrigger``` adnotacji do otrzymywania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji ```byte[]```jako. ```BlobInput``` Adnotacja jest powiązana ```OutputBinding outputItem```z, który jest następnie używany przez funkcję do zapisywania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu. ```BlobOutput```

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

 Poniższy przykład pokazuje funkcję języka Java, która używa ```QueueTrigger``` adnotacji do odbierania komunikatu zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji ```byte[]```jako. ```BlobInput``` ```BlobOutput``` Adnotacja jest powiązana z wartością zwracaną funkcji, która następnie jest używana przez środowisko uruchomieniowe do zapisywania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu.

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

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@BlobOutput` adnotacji w parametrach funkcji, których wartość zostałaby zapisywana w obiekcie w magazynie obiektów BLOB.  Typem parametru powinien być `OutputBinding<T>`, gdzie T jest dowolnym natywnym typem Java lub Pojo.

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i `FileAccess` parametr wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

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
|**direction** | Nie dotyczy | Musi być ustawiona na `out` dla powiązania danych wyjściowych. Wyjątki są zanotowane w sekcji [użycie](#output---usage) . |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.  Ustaw, `$return` aby odwoływać się do zwracanej wartości funkcji.|
|**Ścieżka** |**BlobPath** | Ścieżka do kontenera obiektów BLOB. |
|**połączenia** |**połączenia**| Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Nie dotyczy | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W C# programie C# i skrypcie można powiązać z następującymi typami w celu pisania obiektów blob:

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

<sup>1</sup> wymaga powiązania `direction` "in" w *funkcji Function. JSON* lub `FileAccess.Read` w bibliotece C# klas. Można jednak użyć obiektu kontenera, który środowisko uruchomieniowe zapewnia do wykonywania operacji zapisu, takich jak przekazywanie obiektów BLOB do kontenera.

<sup>2</sup> wymaga powiązania `direction` "Inout" w *funkcji Function. JSON* lub `FileAccess.ReadWrite` w bibliotece C# klas.

Jeśli próbujesz powiązać z jednym z typów zestawu SDK magazynu i otrzymasz komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

W funkcji asynchronicznej, użyj wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

Powiązanie z `string` lub `Byte[]` jest zalecane tylko wtedy, gdy rozmiar obiektu BLOB jest mały, ponieważ cała zawartość obiektu BLOB jest załadowana do pamięci. Ogólnie rzecz biorąc, zaleca się użycie `Stream` typu lub. `CloudBlockBlob` Aby uzyskać więcej informacji, zobacz [użycie współbieżności i pamięci](#trigger---concurrency-and-memory-usage) wcześniej w tym artykule.


W języku JavaScript Uzyskuj dostęp do danych obiektów `context.bindings.<name from function.json>`BLOB za pomocą polecenia.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Tematy pomocy |
|---|---|
| Blob | [Kody błędów obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
