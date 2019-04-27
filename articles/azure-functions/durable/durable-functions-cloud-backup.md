---
title: Fan-wyjściowego/fan-w scenariuszach w funkcje trwałe - Azure
description: Dowiedz się, jak implementować scenariusza fan-wyjściowego fan w w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0bef5f1b64ec9f322070ba5c36cab138c7327da2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741277"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-wyjściowego/fan-w scenariuszu w funkcje trwałe — przykład tworzenia kopii zapasowych w chmurze

*Fan-wyjściowego/fan-w* odwołuje się do wzorca wykonywane jednocześnie wiele funkcji, a następnie wykonuje niektóre agregacji na wyniki. W tym artykule opisano przykładowy, który używa [funkcje trwałe](durable-functions-overview.md) do zaimplementowania fan-w/fan-wyjściowego scenariusza. Próbka jest trwałe funkcja, która tworzy kopię zapasową wszystkich lub niektórych zawartość witryny aplikacji do usługi Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie funkcji przekazywania wszystkich plików w obszarze rekursywnie określonego katalogu do magazynu obiektów blob. Liczone są także całkowita liczba bajtów, które zostały przekazane.

Istnieje możliwość zapisu jednej funkcji, która dba o wszystkie elementy. Główny problem, należy uruchomić następujące polecenie w polega na **skalowalność**. Pojedynczego wykonania funkcji można uruchamiać tylko na jednej maszynie Wirtualnej, więc przepływność jest ograniczona przez przepływność tego pojedynczej maszyny Wirtualnej. Jest innym problemem **niezawodność**. W przypadku awarii w połowie drogi za pośrednictwem lub cały proces wymaga więcej niż 5 minut, tworzenie kopii zapasowej może nie działać w stanie częściowo ukończone. Następnie będzie wymagać ponownego uruchomienia.

Będzie to bardziej niezawodne podejście do zapisania dwóch funkcji regularnych: jeden czy Wyliczanie plików i Dodaj nazwy plików do kolejki, a inny odczytany z kolejki i przekaż pliki do magazynu obiektów blob. Jest to lepszą przepustowość i niezawodność, ale wymaga ona obsługi administracyjnej i Zarządzanie kolejką. Co ważniejsze, wprowadzono znaczące złożoności w zakresie **stanu zarządzania** i **koordynacji** Jeśli chcesz wykonywać żadnych innych, jak raport całkowita liczba bajtów przekazany.

Trwałe funkcje podejście zapewnia wszystkich wymienionych korzyści z bardzo niskie obciążenie.

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

W poniższych sekcjach opisano konfigurację i kod, które są używane dla języka C# skryptów. Kod dla rozwoju programu Visual Studio jest wyświetlany na końcu tego artykułu.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Aranżacji kopii zapasowych w chmurze (kod przykładowy portal programu Visual Studio Code i platformy Azure)

`E2_BackupSiteContent` Funkcja używa standardowych *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Poniżej przedstawiono kod, który implementuje funkcję programu orchestrator:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ta funkcja programu orchestrator zasadniczo wykonuje następujące czynności:

1. Trwa `rootDirectory` wartość jako parametr wejściowy.
2. Wywołuje funkcję w celu uzyskania cyklicznego listę plików w obszarze `rootDirectory`.
3. Wielu wywołań funkcji równoległego do przekazywania wszystkich plików w usłudze Azure Blob Storage.
4. Czeka, aż wszystkie przekazywania zakończyć.
5. Zwraca sumę całkowitą liczbę bajtów, które zostały przekazane do usługi Azure Blob Storage.

Zwróć uwagę `await Task.WhenAll(tasks);` (C#) i `yield context.df.Task.all(tasks);` (JavaScript), wiersze. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji zostały *nie* oczekiwane. Jest to zamierzone, aby umożliwić im do równoległego uruchamiania. Gdy przekazanie tej tablicy zadań `Task.WhenAll` (C#) lub `context.df.Task.all` (JavaScript), uzyskujemy ponownie zadanie, które nie będą ukończone *mają zakończenie wszystkich operacji kopiowania*. Jeśli znasz z Biblioteka zadań równoległych (TPL) na platformie .NET lub [ `Promise.all` ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) w JavaScript, następnie nie jest to dla Ciebie nowe. Różnica polega na że te zadania można równolegle będzie działać na wielu maszynach wirtualnych i rozszerzenia funkcji trwałych gwarantuje, że wykonanie end-to-end jest odporna na odtwarzanie procesów.

> [!NOTE]
> Chociaż zadania są koncepcyjnie podobne do obietnic JavaScript, za pomocą funkcji programu orchestrator `context.df.Task.all` i `context.df.Task.any` zamiast `Promise.all` i `Promise.race` sobie przetwarzanie równoległe zadań.

Po oczekujące na z `Task.WhenAll` (lub którego można z `context.df.Task.all`), gdy wiemy, że wszystkie wywołania funkcji zostały wykonane i zostały zwrócone wartości z powrotem do nas. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę bajtów przekazany, obliczanie liczba bajtów łączna suma jest kwestią Dodawanie zwrócić wszystkie te wartości razem.

## <a name="helper-activity-functions"></a>Pomocnik działania funkcji

Działanie funkcji pomocnika, podobnie jak w przypadku innych przykłady są funkcji po prostu regularnych, które używają `activityTrigger` wyzwolić powiązania. Na przykład *function.json* plik `E2_GetFileList` wygląda podobnie do następującego:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A Oto wdrożenia:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Implementację JavaScript `E2_GetFileList` używa `readdirp` moduł rekursywnie odczytać struktury katalogów.

> [!NOTE]
> Użytkownik może się zastanawiać, dlaczego po prostu nie można umieścić ten kod bezpośrednio do funkcji programu orchestrator. Można wykonać następujące akcje, ale spowoduje to przerwanie jedną z podstawowych reguł funkcje programu orchestrator, co stanowi, że nigdy nie powinni zrobić operacji We/Wy, takie jak dostęp do systemu plików lokalnych.

*Function.json* plik `E2_CopyFileToBlob` jest podobnie proste:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Implementacja języka C# jest również całkiem proste. Zdarza się, aby korzystać z niektórych zaawansowane funkcje usługi Azure Functions powiązania (czyli użytkowania `Binder` parametru), ale nie trzeba już martwić się o te informacje na potrzeby tego przewodnika.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

Implementacja języka JavaScript nie ma dostępu do `Binder` funkcji usługi Azure Functions, więc [zestawu SDK usługi Azure Storage dla węzła](https://github.com/Azure/azure-storage-node) odbywa się jej.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Wdrożenie ładuje plik z dysku i asynchronicznie strumieniowo zawartość do obiektu blob o takiej samej nazwie w kontenerze "kopie zapasowe". Zwracana wartość jest liczbą bajtów skopiowanych w magazynie, który jest następnie używany przez funkcję programu orchestrator do obliczenia sum agregacji.

> [!NOTE]
> To idealny przykład przenoszenia operacji We/Wy do `activityTrigger` funkcji. Nie tylko można rozpowszechniać prace na wielu różnych maszyn wirtualnych, ale możesz także uzyskać korzyści wynikające z tworzenia punktów kontrolnych postęp. Jeśli proces hosta pobiera zakończone jakiegokolwiek powodu, wiesz, przekazywania, które zostały już wykonane.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Możesz rozpocząć aranżacji, wysyłając następujące żądanie HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funkcja, która jest działa tylko z zawartości w formacie JSON. Z tego powodu `Content-Type: application/json` nagłówka jest wymagana, a ścieżka katalogu jest zakodowane jako ciąg JSON. Ponadto HTTP, fragment kodu zakłada, istnieje wpis w `host.json` pliku, co spowoduje usunięcie domyślnie `api/` prefiks z wszystkie adresy URL funkcji wyzwalacza HTTP. Znaczniki można znaleźć w tej konfiguracji w `host.json` pliku w przykładach.

Żądanie to HTTP wyzwalaczy `E2_BackupSiteContent` orchestrator i przekazuje ciąg `D:\home\LogFiles` jako parametr. Odpowiedź zawiera również link do pobrania stanu operacji tworzenia kopii zapasowej:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W zależności od liczby plików dziennika w aplikacji funkcji ta operacja może potrwać kilka minut, aby zakończyć. Można uzyskać najnowszy stan, badając adresu URL w `Location` nagłówka poprzedniej odpowiedzi HTTP 202.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

W takim przypadku funkcja jest nadal uruchomiona. Jesteś w stanie wyświetlić dane wejściowe, który został zapisany w stan programu orchestrator i godzina ostatniej aktualizacji. Będzie można kontynuować używanie `Location` wartości nagłówka do sondowania pod kątem ukończenia. Gdy stan to "ukończone", zobaczysz wartość odpowiedzi HTTP podobny do następującego:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Teraz widać, że aranżacji została ukończona, a około ile czasu zajęło do ukończenia. Zobacz też wartość `output` pola, co oznacza, że około 450 KB dzienników zostały przekazane.

## <a name="visual-studio-sample-code"></a>Kod przykładowy w usłudze Visual Studio

Oto aranżacji jako pojedynczy plik języka C# w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Storage` pakiet Nuget do uruchamiania kodu przykładu poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Kolejne kroki

Ten przykład pokazuje, jak zaimplementować wzorzec fan-wyjściowego/fan-w. Następny przykład pokazuje, jak zaimplementować za pomocą wzorca monitor [trwałe czasomierzy](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Uruchom aplikację przykładową monitora](durable-functions-monitor.md)
