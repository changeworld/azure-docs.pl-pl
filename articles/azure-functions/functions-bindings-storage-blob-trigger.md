---
title: Wyzwalacz usługi Azure Blob Storage dla Azure Functions
description: Dowiedz się, jak uruchamiać funkcję platformy Azure jako zmiany danych usługi Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 0cdff3ac6eb2faed0c0b6b8796fdb3b6b0411018
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202088"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure Blob Storage dla Azure Functions

Wyzwalacz magazynu obiektów BLOB uruchamia funkcję po wykryciu nowego lub zaktualizowanego obiektu BLOB. Zawartość obiektu BLOB jest dostarczana jako [dane wejściowe do funkcji](./functions-bindings-storage-blob-input.md).

Wyzwalacz usługi Azure Blob Storage wymaga konta magazynu ogólnego przeznaczenia. Aby użyć konta typu obiekt BLOB lub jeśli aplikacja ma specjalne potrzeby, przejrzyj alternatywy do użycia tego wyzwalacza.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternatywy

### <a name="event-grid-trigger"></a>Wyzwalacz Event Grid

[Wyzwalacz Event Grid](functions-bindings-event-grid.md) również ma wbudowaną obsługę [zdarzeń obiektów BLOB](../storage/blobs/storage-blob-event-overview.md). Użyj Event Grid zamiast wyzwalacza usługi BLOB Storage dla następujących scenariuszy:

- **Konta magazynu tylko dla obiektów BLOB**: [konta magazynu](../storage/common/storage-account-overview.md#types-of-storage-accounts) obsługujące obiekty blob są obsługiwane przez powiązania danych wejściowych i wyjściowych obiektów blob, ale nie dla wyzwalaczy obiektów BLOB.

- **Wysoka Skala**: wysoka Skala można luźno definiować jako kontenery, które mają więcej niż 100 000 obiektów BLOB w ramach konta magazynu, które mają więcej niż 100 aktualizacji obiektów BLOB na sekundę.

- **Minimalizacja opóźnień**: Jeśli aplikacja funkcji jest w planie zużycia, może wystąpić maksymalnie 10 minut opóźnienia w przetwarzaniu nowych obiektów blob, jeśli aplikacja funkcji przestała być w stanie bezczynności. Aby uniknąć tego opóźnienia, możesz przejść do planu App Service z opcją zawsze włączone. Możesz również użyć [wyzwalacza Event Grid](functions-bindings-event-grid.md) z kontem magazynu obiektów BLOB. Aby zapoznać się z przykładem, zobacz [samouczek Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Zapoznaj się z przykładem [zmiany rozmiaru obrazu za pomocą Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) samouczka Event Grid.

### <a name="queue-storage-trigger"></a>Wyzwalacz magazynu kolejek

Innym podejściem do przetwarzania obiektów BLOB jest zapisanie komunikatów w kolejce, które odpowiadają tworzonym lub modyfikowanym obiektom BLOB, a następnie do rozpoczęcia przetwarzania przy użyciu [wyzwalacza magazynu kolejki](./functions-bindings-storage-queue.md) .

## <a name="example"></a>Przykład

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w kontenerze `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji o atrybucie `BlobTrigger`, zobacz [atrybuty i adnotacje](#attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza obiektu BLOB w pliku *Function. JSON* i kodzie, który używa tego powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Oto dane powiązania w pliku *Function. JSON* :

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

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Kod C# skryptu, który wiąże się z `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Kod C# skryptu, który wiąże się z `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza obiektów BLOB w pliku *Function. JSON* i [kodu JavaScript](functions-reference-node.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w kontenerze `samples-workitems`.

Oto plik *Function. JSON* :

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

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie wyzwalacza obiektów BLOB w pliku *Function. JSON* i [kodu Python](functions-reference-python.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Oto plik *Function. JSON* :

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

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *Function. JSON* , zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ta funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w kontenerze `myblob`.

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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz obiektu BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje ciąg ścieżki wskazujący, że kontener ma być obserwowany i opcjonalnie [wzorzec nazwy obiektu BLOB](#blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Możesz ustawić właściwość `Connection`, aby określić konto magazynu, które ma być używane, jak pokazano w następującym przykładzie:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Aby zapoznać się z kompletnym przykładem, zobacz [przykład wyzwalacza](#example).

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

* Właściwość `Connection` atrybutu `BlobTrigger`.
* Atrybut `StorageAccount` stosowany do tego samego parametru, który jest atrybutem `BlobTrigger`.
* Atrybut `StorageAccount` stosowany do funkcji.
* Atrybut `StorageAccount` stosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobTrigger` służy do zapewniania dostępu do obiektu BLOB, który wyzwolił funkcję. Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `BlobTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | musi być ustawiony na `blobTrigger`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | musi być ustawiony na `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji. |
|**path** | **Blobpath ścieżką** |[Kontener](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) do monitorowania.  Może być [wzorcem nazw obiektów BLOB](#blob-name-patterns). |
|**połączenia** | **Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Na przykład jeśli ustawisz `connection` na "WebStorage", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem usługi BLOB Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych obiektów BLOB przy użyciu `context.bindings.<NAME>`, gdzie `<NAME>` pasuje do wartości zdefiniowanej w *funkcji Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Dostęp do danych obiektów BLOB za pomocą parametru, który został określony jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

# <a name="javatabjava"></a>[Java](#tab/java)

Atrybut `@BlobTrigger` służy do zapewniania dostępu do obiektu BLOB, który wyzwolił funkcję. Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

---

## <a name="blob-name-patterns"></a>Wzorce nazw obiektów BLOB

Możesz określić wzorzec nazwy obiektu BLOB we właściwości `path` w *funkcji Function. JSON* lub w konstruktorze `BlobTrigger` atrybutu. Wzorzec nazwy może być [wyrażeniem filtru lub powiązania](./functions-bindings-expressions-patterns.md). W poniższych sekcjach przedstawiono przykłady.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę i rozszerzenie pliku

Poniższy przykład pokazuje, jak powiązać z nazwą pliku i rozszerzeniem obiektu BLOB osobno:

```json
"path": "input/{blobname}.{blobextension}",
```

Jeśli obiekt BLOB ma nazwę *Original-Blob1. txt*, wartości zmiennych `blobname` i `blobextension` w kodzie funkcji są *oryginalne-Blob1* i *txt*.

### <a name="filter-on-blob-name"></a>Filtruj według nazwy obiektu BLOB

Poniższy przykład wyzwala wyzwalacze tylko w obiektach Blob w kontenerze `input`, które zaczynają się od ciągu "Original-":

```json
"path": "input/original-{name}",
```

Jeśli obiekt BLOB ma nazwę *Original-Blob1. txt*, wartość zmiennej `name` w kodzie funkcji jest `Blob1`.

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

Jeśli obiekt BLOB ma nazwę *{20140101}-soundfile. mp3*, `name` wartość zmiennej w kodzie funkcji to *soundfile. mp3*.

## <a name="metadata"></a>Metadane

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Napisy](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Metadane nie są dostępne w języku Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Metadane nie są dostępne w języku Java.

---

## <a name="blob-receipts"></a>Potwierdzenia obiektów BLOB

Środowisko uruchomieniowe Azure Functions zapewnia, że żadna funkcja wyzwalacza obiektu BLOB nie zostanie wywołana więcej niż raz dla tego samego nowego lub zaktualizowanego obiektu BLOB. Aby określić, czy dana wersja obiektu BLOB została przetworzona, przechowuje *potwierdzenia obiektów BLOB*.

Azure Functions przechowuje potwierdzenia obiektów BLOB w kontenerze o nazwie *Azure-WebJobs-hosty* na koncie usługi Azure Storage dla aplikacji funkcji (zdefiniowanej przez ustawienie aplikacji `AzureWebJobsStorage`). Potwierdzenie obiektu BLOB zawiera następujące informacje:

* Funkcja wyzwalana (" *&lt;> nazwy aplikacji funkcji*. Obowiązki. *nazwa funkcji&lt;>* ", na przykład:" MyFunctionApp. Functions. CopyBlob ")
* Nazwa kontenera
* Typ obiektu BLOB ("BlockBlob" lub "PageBlob")
* Nazwa obiektu BLOB
* Element ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetwarzanie obiektu BLOB, Usuń potwierdzenie obiektu BLOB dla tego obiektu BLOB z kontenera *usługi Azure-WebJobs-hosts* ręcznie. Proces ponownego przetwarzania może nie nastąpić natychmiast, dlatego ma miejsce w późniejszym czasie.

## <a name="poison-blobs"></a>Trujące obiekty blob

Gdy funkcja wyzwalacza obiektu BLOB kończy się niepowodzeniem dla danego obiektu BLOB, Azure Functions ponownych prób, które domyślnie działają łącznie 5 razy.

W przypadku niepowodzenia wszystkich 5 prób Azure Functions dodaje komunikat do kolejki magazynu o nazwie *WebJobs-blobtrigger-trujące*. Komunikat w kolejce dla trujących obiektów BLOB jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w formacie *&lt;> Nazwa aplikacji funkcji*. Obowiązki. *nazwa funkcji&lt;>* )
* Blobtype ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* BlobName
* ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Użycie współbieżności i pamięci

Wyzwalacz obiektów BLOB używa kolejki wewnętrznie, więc Maksymalna liczba współbieżnych wywołań funkcji jest kontrolowana przez [konfigurację kolejek w pliku host. JSON](functions-host-json.md#queues). Ustawienia domyślne ograniczają współbieżność do 24 wywołań. Ten limit dotyczy osobno każdej funkcji, która używa wyzwalacza obiektu BLOB.

[Plan zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work) ogranicza aplikację funkcji na jednej maszynie wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez wszystkie jednocześnie wykonywane wystąpienia funkcji i przez sam czas wykonywania funkcji. Jeśli funkcja wyzwalana przez obiekt BLOB ładuje cały obiekt BLOB do pamięci, maksymalna ilość pamięci używana przez tę funkcję tylko dla obiektów BLOB to 24 * maksymalny rozmiar obiektu BLOB. Na przykład aplikacja funkcji mająca trzy funkcje wyzwalane przez obiekt BLOB i ustawienia domyślne byłyby w maksymalnym zakresie współbieżności maszyny wirtualnej równym 3 * 24 = 72 wywołań funkcji.

Funkcje języka JavaScript i języka Java ładują cały obiekt BLOB do C# pamięci i działają tak, jak w przypadku powiązania z `string`, `Byte[]`lub poco.

## <a name="polling"></a>Sondowania

Sondowanie działa jako hybrydowe między inspekcjami dzienników i uruchamiania okresowych skanowania kontenerów. Obiekty blob są skanowane w grupach 10 000 na raz z tokenem kontynuacji używanym między interwałami.

> [!WARNING]
> Ponadto [dzienniki magazynu są tworzone na podstawie najlepszego nakładu pracy](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nie ma gwarancji, że wszystkie zdarzenia są przechwytywane. W pewnych warunkach dzienniki mogą być pominięte.
> 
> Jeśli potrzebujesz szybszego lub bardziej niezawodnego przetwarzania obiektów blob, rozważ utworzenie [komunikatu w kolejce](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu BLOB. Następnie przetworzyć obiekt BLOB przy użyciu [wyzwalacza kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacza obiektu BLOB. Innym rozwiązaniem jest użycie Event Grid; Zapoznaj się z samouczkiem [Automatyzowanie zmiany rozmiarów przekazanych obrazów przy użyciu Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Następne kroki

- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)
