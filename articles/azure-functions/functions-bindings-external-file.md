---
title: Zewnętrznego powiązania w pliku dla usługi Azure Functions (wersja eksperymentalna)
description: W usłudze Azure Functions przy użyciu powiązania pliku zewnętrznego
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: cshoe
ms.openlocfilehash: 39036f17b8ceafebbe3660f2074e2a6c84c4df03
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248697"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Usługa Azure powiązania pliku zewnętrznego Functions (wersja eksperymentalna)
W tym artykule pokazano, jak do manipulowania plikami z różnych dostawców SaaS (np. usługi Dropbox lub dysk Google) w usłudze Azure Functions. Usługi Azure Functions obsługuje wyzwalanie, dane wejściowe i wyjściowe powiązania dla plików zewnętrznych. Te powiązania utworzyć połączenia interfejsu API z dostawcami SaaS lub użyć istniejącego połączenia interfejsu API z poziomu grupy zasobów aplikacji funkcji.

> [!IMPORTANT]
> Powiązania pliku zewnętrznego są eksperymentalne i nigdy nie może osiągnąć stan jest ogólnie dostępna (GA). Są one uwzględnione tylko na platformie Azure funkcji 1.x i nie ma żadnych planów, aby dodać je do usługi Azure Functions 2.x. W przypadku scenariuszy, które wymagają dostępu do danych w modelu SaaS dostawców należy wziąć pod uwagę przy użyciu [aplikacje logiki, które wywołują funkcje](functions-twitter-email.md). Zobacz [łącznika systemu plików usługi Logic Apps](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Połączenia plików

|Łącznik|Wyzwalacz|Dane wejściowe|Dane wyjściowe|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[Usługi OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive dla Firm](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Dysk Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Połączenia zewnętrzne pliku można również w [usługi Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Wyzwalacz

Wyzwalacza zewnętrznego pliku umożliwia monitorowanie folderu zdalnego i uruchomić kod funkcji, gdy zostaną wykryte zmiany.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacz pliku zewnętrznego powiązania w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja rejestruje zawartość każdego pliku, który zostanie dodany do folderu monitorowane.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz pliku zewnętrznego powiązania w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja rejestruje zawartość każdego pliku, który zostanie dodany do folderu monitorowane.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**type** | Musi być równa `apiHubFileTrigger`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracja interfejsu użytkownika w witrynie Azure portal.|
|**Ścieżka** | Folder do monitorowania i opcjonalnie wzorzec nazwy.|

### <a name="name-patterns"></a>Wzorce nazw

Można określić wzorzec nazwy pliku, w `path` właściwości. Folder, do których odwołuje się musi istnieć w dostawcy SaaS.

Przykłady:

```json
"path": "input/original-{name}",
```

Ta ścieżka może znaleźć pliku o nazwie *więc Plik1.txt oryginalny* w *wejściowych* folder, a wartość `name` będzie zmiennej w kodzie funkcji `File1.txt`.

Inny przykład:

```json
"path": "input/{filename}.{fileextension}",
```

Ta ścieżka może również znaleźć w pliku o nazwie *więc Plik1.txt oryginalny*i wartość `filename` i `fileextension` będzie zmiennych w kodzie funkcji *plik1 oryginalny* i *txt* .

Typ pliku plików, można ograniczyć za pomocą wartości stałej dla rozszerzenia pliku. Na przykład:

```json
"path": "samples/{name}.png",
```

W tym przypadku tylko *.png* pliki *przykłady* folderu spowoduje wyzwolenia funkcji.

Nawiasy klamrowe są znaki specjalne w wzorce nazw. Do określenia nazw plików, które mają nawiasów klamrowych w nazwie, dwukrotnie nawiasów klamrowych.
Na przykład:

```json
"path": "images/{{20140101}}-{name}",
```

Ta ścieżka może znaleźć pliku o nazwie  *{20140101}-soundfile.mp3* w *obrazów* folderu i `name` będzie wartość zmiennej w kodzie funkcji *soundfile.mp3*.

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W funkcji języka C#, można powiązać z danymi pliku wejściowego przy użyciu nazwany parametr w podpisie funkcji, takie jak `<T> <name>`.
Gdzie `T` jest typ danych, który chcesz wykonać deserializacji danych, a `paramName` jest nazwa określona w [JSON wyzwalacza](#trigger). W funkcji Node.js, uzyskujesz dostęp do danych plików wejściowych przy użyciu `context.bindings.<name>`.

Plik może zostać przeprowadzona deserializacja dowolne z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku danych pliku serializacji JSON.
  Jeśli zadeklarujesz niestandardowy typ danych wejściowych (np. `FooType`), usługi Azure Functions próbuje deserializowanie danych JSON do określonego typu.
* Parametry - przydatne w przypadku danych z plików tekstowych.

Możesz również powiązać dowolny z następujących typów funkcji języka C# i środowisko uruchomieniowe usługi Functions próbuje wykonać deserializacji danych plików przy użyciu tego typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Wyzwalacz - skażone plików

W przypadku awarii funkcję wyzwalacza zewnętrznego pliku usługi Azure Functions ponawia próbę tej funkcji do 5 razy domyślnie (łącznie z pierwszym) dla danego pliku.
W przypadku awarii wszystkich prób 5, funkcje dodaje komunikat do kolejki magazynu o nazwie *webjobs-apihubtrigger-poison*. Komunikat w kolejce dla plików poison to obiekt JSON, który zawiera następujące właściwości:

* FunctionId (w formacie  *&lt;nazwę aplikacji funkcji >*. Funkcje.  *&lt;nazwa funkcji >*)
* Typ pliku
* Nazwa folderu
* Nazwa pliku
* Element ETag (identyfikator wersji pliku, na przykład: "0x8D1DC6E70A277EF")

## <a name="input"></a>Dane wejściowe

Powiązania danych wejściowych plików zewnętrznych platformy Azure umożliwia użycie pliku z folderu zewnętrzne w funkcji.

## <a name="input---example"></a>Dane wejściowe — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano pliku zewnętrznego powiązania danych wejściowych i wyjściowych w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja kopiuje plik wejściowy do pliku wyjściowego.

Oto powiązanie danych w *function.json* pliku:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

W poniższym przykładzie pokazano pliku zewnętrznego powiązania danych wejściowych i wyjściowych w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja kopiuje plik wejściowy do pliku wyjściowego.

Oto powiązanie danych w *function.json* pliku:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**type** | Musi być równa `apiHubFile`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracja interfejsu użytkownika w witrynie Azure portal.|
|**Ścieżka** | Musi zawierać nazwę folderu i nazwę pliku. Na przykład, jeśli masz [wyzwalacz kolejki](functions-bindings-storage-queue.md) w funkcji, można użyć `"path": "samples-workitems/{queueTrigger}"` wskaż plik w `samples-workitems` folder o nazwie, która jest zgodna z nazwą pliku określony w komunikacie wyzwalacza.   

## <a name="input---usage"></a>Dane wejściowe — użycie

W funkcji języka C#, można powiązać z danymi pliku wejściowego przy użyciu nazwany parametr w podpisie funkcji, takie jak `<T> <name>`. `T` to typ danych, który chcesz wykonać deserializacji danych, a `name` określono powiązania danych wejściowych. W funkcji Node.js, uzyskujesz dostęp do danych plików wejściowych przy użyciu `context.bindings.<name>`.

Plik może zostać przeprowadzona deserializacja dowolne z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku danych pliku serializacji JSON.
  Jeśli zadeklarujesz niestandardowy typ danych wejściowych (np. `InputType`), usługi Azure Functions próbuje deserializowanie danych JSON do określonego typu.
* Parametry - przydatne w przypadku danych z plików tekstowych.

Możesz również powiązać dowolny z następujących typów funkcji języka C# i środowisko uruchomieniowe usługi Functions próbuje wykonać deserializacji danych plików przy użyciu tego typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Dane wyjściowe

Azure pliku zewnętrznego powiązania danych wyjściowych pozwala zapisywać pliki do folderu zewnętrzne w funkcji.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz [przykładowe powiązania danych wejściowych](#input---example).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**type** | Musi być równa `apiHubFile`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Musi być równa `out`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracja interfejsu użytkownika w witrynie Azure portal.|
|**Ścieżka** | Musi zawierać nazwę folderu i nazwę pliku. Na przykład, jeśli masz [wyzwalacz kolejki](functions-bindings-storage-queue.md) w funkcji, można użyć `"path": "samples-workitems/{queueTrigger}"` wskaż plik w `samples-workitems` folder o nazwie, która jest zgodna z nazwą pliku określony w komunikacie wyzwalacza.   

## <a name="output---usage"></a>Dane wyjściowe — użycie

W funkcji języka C#, możesz powiązać plik wyjściowy przy użyciu nazwanego `out` parametru w swój podpis funkcji, takich jak `out <T> <name>`, gdzie `T` jest typ danych, który chcesz serializowania danych, a `name` jest nazwa określona w powiązania danych wyjściowych. W przypadku funkcji Node.js dostęp przy użyciu pliku wyjściowego `context.bindings.<name>`.

Można napisać do pliku wyjściowego przy użyciu dowolnej z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku serializacji JSON.
  Jeśli deklaracja typu danych wyjściowych niestandardowego (np. `out OutputType paramName`), usługi Azure Functions próby serializacji obiektu do postaci JSON. Jeśli parametr wyjściowy ma wartość null, jeśli funkcja kończy działanie, środowisko uruchomieniowe funkcji utworzy plik jako obiekt o wartości null.
* Ciąg — (`out string paramName`) przydatne w przypadku danych z plików tekstowych. środowisko uruchomieniowe funkcji utworzy plik tylko wtedy, gdy parametr ciągu jest różna od null, jeśli funkcja kończy działanie.

W funkcji języka C# można również danych wyjściowych do dowolnego z następujących typów:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
