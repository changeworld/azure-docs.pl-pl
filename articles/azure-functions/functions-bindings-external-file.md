---
title: Zewnętrznego powiązania pliku dla usługi Azure Functions (eksperymentalne)
description: Przy użyciu powiązań zewnętrznych plików w usługi Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2018
ms.locfileid: "27607925"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure powiązania funkcji zewnętrzny plik (eksperymentalne)
W tym artykule przedstawiono sposób manipulowanie plikami od różnych dostawców SaaS (na przykład skrzynki lub dysk Google) w funkcji platformy Azure. Usługi Azure Functions obsługuje wyzwolenia, danych wejściowych i wyjściowych powiązań zewnętrznych plików. Tych powiązań utworzyć interfejsu API połączeń z dostawcami SaaS lub użyć istniejącego połączenia interfejsu API z grupy zasobów aplikacji funkcji.

> [!IMPORTANT]
> Powiązania zewnętrznego pliku są eksperymentalne i nigdy nie może osiągnąć stanu ogólnie dostępna (GA). Są one uwzględnione tylko na platformie Azure funkcje 1.x i nie ma żadnych planów, aby dodać je do usługi Azure Functions 2.x. W przypadku scenariuszy, które wymagają dostępu do danych w modelu SaaS dostawców, rozważ zastosowanie [logikę aplikacji, które wywołują funkcje](functions-twitter-email.md). Zobacz [łącznika systemu plików aplikacji logiki](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Połączenia dostępnych plików

|Łącznik|Wyzwalacz|Dane wejściowe|Dane wyjściowe|
|:-----|:---:|:---:|:---:|
|[Pole](https://www.box.com)|x|x|x
|[Skrzynki](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[Usługi OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive dla Firm](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Dysk Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Połączenia zewnętrzne pliku można również w [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Wyzwalacz

Wyzwalacza zewnętrznego pliku umożliwia monitorowanie folderu zdalnego i uruchomić kod funkcja, gdy zostaną wykryte zmiany.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz pliku zewnętrznego powiązania w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja rejestruje zawartość każdego pliku, który zostanie dodany do folderu monitorowane.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz pliku zewnętrznego powiązania w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja rejestruje zawartość każdego pliku, który zostanie dodany do folderu monitorowane.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**Typ** | należy wybrać opcję `apiHubFileTrigger`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**Kierunek** | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzeń w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracji interfejsu użytkownika w portalu Azure.|
|**Ścieżka** | Folder do monitorowania i opcjonalnie wzorzec nazwy.|

### <a name="name-patterns"></a>Wzorce nazw

Można określić wzorzec nazwy pliku, w `path` właściwości. Folder, do których odwołuje się musi istnieć w dostawcy SaaS.

Przykłady:

```json
"path": "input/original-{name}",
```

Ta ścieżka znajdował się plik o nazwie *więc Plik1.txt oryginalne* w *wejściowych* folderu, a wartość `name` byłoby zmiennej w kodzie funkcja `File1.txt`.

Inny przykład:

```json
"path": "input/{filename}.{fileextension}",
```

Ta ścieżka będzie również znaleźć w pliku o nazwie *więc Plik1.txt oryginalne*i wartość `filename` i `fileextension` będzie zmienne w kodzie funkcja *plik1 oryginalne* i *txt*.

Typ pliku plików można ograniczyć przy użyciu wartości stałej dla rozszerzenia pliku. Na przykład:

```json
"path": "samples/{name}.png",
```

W takim przypadku tylko *.png* plików *przykłady* folderu wyzwalanie funkcji.

Nawiasy klamrowe są znaki specjalne w wzorce nazw. Aby określić nazwy plików, które mają nawiasy klamrowe w nazwie, dwukrotnie nawiasów klamrowych.
Na przykład:

```json
"path": "images/{{20140101}}-{name}",
```

Ta ścieżka znajdował się plik o nazwie *{20140101}-soundfile.mp3* w *obrazów* folderu i `name` będzie wartość zmiennej w kodzie funkcja *soundfile.mp3*.

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W języku C# funkcji, można powiązać z danymi pliku wejściowego przy użyciu nazwanego parametru w podpisu funkcji tak samo, jak `<T> <name>`.
Gdzie `T` jest typ danych chcesz danych do deserializacji i `paramName` jest nazwa określona w [wyzwolenia JSON](#trigger). W przypadku funkcji Node.js dostęp przy użyciu danych pliku wejściowego `context.bindings.<name>`.

Plik może zostać przeprowadzona deserializacja żadnego z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku danych pliku serializacji JSON.
  W przypadku niestandardowy typ danych wejściowych (np. `FooType`), usługi Azure Functions próbuje deserializowanie danych JSON w sieci określonego typu.
* Parametry - przydatne w przypadku danych pliku tekstowego.

W języku C# funkcji można również wiązać z dowolnej z następujących typów, a środowisko uruchomieniowe Functions podejmuje próbę deserializacji danych plików za pomocą tego typu:

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

W przypadku awarii funkcja wyzwalacza zewnętrznego pliku usługi Azure Functions ponowi próbę tej funkcji do 5 razy domyślnie (w tym pierwszej próby) dla danego pliku.
Jeśli nie wszystkie próby 5, funkcje dodaje komunikat do kolejki magazynu o nazwie *webjob apihubtrigger-poison*. Komunikat z kolejki skażone plików jest obiekt JSON, który zawiera następujące właściwości:

* FunctionId (w formacie  *&lt;funkcja Nazwa aplikacji >*. Funkcje.  *&lt;nazwy funkcji >*)
* Typ pliku
* Nazwa folderu
* Nazwa pliku
* Element ETag (identyfikator wersji pliku, na przykład: "0x8D1DC6E70A277EF")

## <a name="input"></a>Dane wejściowe

Powiązania wejściowego pliku zewnętrznego Azure pozwala na użycie pliku z zewnętrznego folderu w funkcji.

## <a name="input---example"></a>Dane wejściowe — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [Skryptu C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Dane wejściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono pliku zewnętrznego powiązania wejściowe i wyjściowe *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja kopiuje plik wejściowy do pliku wyjściowego.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod skryptu C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Dane wejściowe — przykład JavaScript

W poniższym przykładzie przedstawiono pliku zewnętrznego powiązania wejściowe i wyjściowe *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja kopiuje plik wejściowy do pliku wyjściowego.

W tym miejscu jest powiązanie danych *function.json* pliku:

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

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Dane wejściowe — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**Typ** | należy wybrać opcję `apiHubFile`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**Kierunek** | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzeń w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracji interfejsu użytkownika w portalu Azure.|
|**Ścieżka** | Musi zawierać nazwę folderu i nazwę pliku. Na przykład, jeśli masz [wyzwalacza kolejki](functions-bindings-storage-queue.md) w funkcji, można użyć `"path": "samples-workitems/{queueTrigger}"` wskaż plik w `samples-workitems` folder o nazwie, która jest zgodna z nazwą pliku podane w komunikacie wyzwalacza.   

## <a name="input---usage"></a>Dane wejściowe — użycie

W języku C# funkcji, można powiązać z danymi pliku wejściowego przy użyciu nazwanego parametru w podpisu funkcji tak samo, jak `<T> <name>`. `T`jest typ danych chcesz danych do deserializacji i `name` jest nazwa określona w powiązania wejściowego. W przypadku funkcji Node.js dostęp przy użyciu danych pliku wejściowego `context.bindings.<name>`.

Plik może zostać przeprowadzona deserializacja żadnego z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku danych pliku serializacji JSON.
  W przypadku niestandardowy typ danych wejściowych (np. `InputType`), usługi Azure Functions próbuje deserializowanie danych JSON w sieci określonego typu.
* Parametry - przydatne w przypadku danych pliku tekstowego.

W języku C# funkcji można również wiązać z dowolnej z następujących typów, a środowisko uruchomieniowe Functions podejmuje próbę deserializacji danych plików za pomocą tego typu:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Dane wyjściowe

Azure pliku zewnętrznego powiązania danych wyjściowych umożliwia zapisu plików do zewnętrznego folderu w funkcji.

## <a name="output---example"></a>OUTPUT — przykład

Zobacz [przykład powiązania wejściowego](#input---example).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku.

|Właściwość Function.JSON | Opis|
|---------|---------|----------------------|
|**Typ** | należy wybrać opcję `apiHubFile`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**Kierunek** | należy wybrać opcję `out`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nazwa zmiennej, która reprezentuje element zdarzeń w kodzie funkcji. | 
|**połączenia**| Określa ustawienie aplikacji, które są przechowywane w parametrach połączenia. Ustawienia aplikacji jest tworzona automatycznie podczas dodawania połączenia w integracji interfejsu użytkownika w portalu Azure.|
|**Ścieżka** | Musi zawierać nazwę folderu i nazwę pliku. Na przykład, jeśli masz [wyzwalacza kolejki](functions-bindings-storage-queue.md) w funkcji, można użyć `"path": "samples-workitems/{queueTrigger}"` wskaż plik w `samples-workitems` folder o nazwie, która jest zgodna z nazwą pliku podane w komunikacie wyzwalacza.   

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# funkcji, możesz powiązać do pliku wyjściowego przy użyciu nazwanego `out` parametru w podpisu funkcji, takich jak `out <T> <name>`, gdzie `T` jest mają do serializowania danych do typów danych i `name` jest nazwa określona w Wiązanie danych wyjściowych. W przypadku funkcji Node.js dostęp przy użyciu pliku wyjściowego `context.bindings.<name>`.

Możesz zapisywać do pliku wyjściowego przy użyciu dowolnego z następujących typów:

* Wszelkie [obiektu](https://msdn.microsoft.com/library/system.object.aspx) — jest to przydatne w przypadku serializacji JSON.
  W przypadku typu danych wyjściowych niestandardowego (np. `out OutputType paramName`), usługi Azure Functions podejmuje próbę serializacji obiektu do postaci JSON. Jeśli parametr wyjściowy ma wartość null, gdy funkcja kończy, środowisko uruchomieniowe Functions tworzy plik jako obiekt null.
* Parametry - (`out string paramName`) przydatne w przypadku danych pliku tekstowego. środowisko uruchomieniowe Functions tworzy plik tylko wtedy, gdy parametr ciągu jest różna od null, gdy funkcja jest kończona.

W języku C# funkcji można również dane wyjściowe do żadnego z następujących typów:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
