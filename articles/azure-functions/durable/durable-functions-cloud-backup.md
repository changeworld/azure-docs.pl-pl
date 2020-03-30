---
title: Scenariusze typu "fan-out/fan-in" w funkcji trwałych — Azure
description: Dowiedz się, jak zaimplementować scenariusz typu fan-out-fan-in w rozszerzeniu Funkcje trwałe dla usług Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562194"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scenariusz wyjścia/wentylatora w funkcji trwałe — przykład kopii zapasowej w chmurze

*Fan-out/fan-in* odnosi się do wzorca wykonywania wielu funkcji jednocześnie, a następnie wykonywania niektórych agregacji na wyniki. W tym artykule wyjaśniono przykład, który używa [funkcji trwałe](durable-functions-overview.md) do zaimplementowania scenariusza fan-in/fan-out. Przykład jest trwałą funkcją, która łączy w sobie wszystkie lub niektóre z zawartości witryny aplikacji w usłudze Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie funkcje przekazać wszystkie pliki w ramach określonego katalogu rekursywnie do magazynu obiektów blob. Liczą również całkowitą liczbę bajtów, które zostały przekazane.

Można napisać jedną funkcję, która dba o wszystko. Głównym problemem, na który można napotkać, jest **skalowalność**. Wykonanie pojedynczej funkcji można uruchomić tylko na jednej maszynie wirtualnej, więc przepływność będzie ograniczona przez przepływność tej pojedynczej maszyny Wirtualnej. Innym problemem jest **niezawodność**. Jeśli wystąpi błąd w połowie lub jeśli cały proces trwa dłużej niż 5 minut, kopia zapasowa może zakończyć się niepowodzeniem w stanie częściowo ukończone. Następnie należy go ponownie uruchomić.

Bardziej niezawodne podejście byłoby napisać dwie regularne funkcje: jeden będzie wyliczyć pliki i dodać nazwy plików do kolejki, a inny będzie odczytywać z kolejki i przekazać pliki do magazynu obiektów blob. Takie podejście jest lepsze pod względem przepływności i niezawodności, ale wymaga aprowizji i zarządzania kolejką. Co ważniejsze, znaczna złożoność jest wprowadzana pod względem **zarządzania i** **koordynacji** stanu, jeśli chcesz zrobić coś więcej, na przykład zgłosić całkowitą liczbę przesłanych bajtów.

Podejście Trwałe funkcje zapewnia wszystkie wymienione korzyści przy bardzo niskim obciążeniu.

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E2_BackupSiteContent`: [Funkcja](durable-functions-bindings.md#orchestration-trigger) koordynatora, `E2_GetFileList` która wywołuje, aby uzyskać listę `E2_CopyFileToBlob` plików do utworzenia kopii zapasowej, a następnie wywołuje do utworzenia kopii zapasowej każdego pliku.
* `E2_GetFileList`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) zwracana lista plików w katalogu.
* `E2_CopyFileToBlob`: Funkcja działania, która kopii zapasowej pojedynczego pliku do usługi Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funkcja orkiestry

Ta funkcja koordynatora zasadniczo wykonuje następujące czynności:

1. Przyjmuje `rootDirectory` wartość jako parametr wejściowy.
2. Wywołuje funkcję, aby uzyskać cykliczną listę `rootDirectory`plików w obszarze .
3. Wykonuje wiele wywołań funkcji równoległych do przekazywania każdego pliku do usługi Azure Blob Storage.
4. Czeka na zakończenie wszystkich przesłanych.
5. Zwraca sumę bajtów, które zostały przekazane do usługi Azure Blob Storage.

# <a name="c"></a>[C #](#tab/csharp)

Oto kod, który implementuje funkcję koordynatora:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Zwróć `await Task.WhenAll(tasks);` uwagę na linię. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji *nie* były oczekiwane, co pozwala na ich uruchamianie równolegle. Po przejściu tej tablicy zadań do `Task.WhenAll`, możemy odzyskać zadanie, które nie zostanie *wykonane, dopóki wszystkie operacje kopiowania nie zostaną zakończone.* Jeśli znasz bibliotekę równoległą zadań (TPL) w .NET, nie jest to dla Ciebie nowość. Różnica polega na tym, że te zadania mogą być uruchomione na wielu maszynach wirtualnych jednocześnie, a rozszerzenie Funkcje trwałe zapewnia, że wykonanie end-to-end jest odporne na przetwarzanie procesu.

Po oczekiwaniu `Task.WhenAll`od , wiemy, że wszystkie wywołania funkcji zostały zakończone i zwróciły wartości z powrotem do nas. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę bajtów przekazanych, więc obliczanie sumy całkowita liczba bajtów jest kwestią dodawania wszystkich tych wartości zwracanych razem.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkcja używa standardowego *function.json* dla funkcji orkiestratora.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Oto kod, który implementuje funkcję koordynatora:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Zwróć `yield context.df.Task.all(tasks);` uwagę na linię. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji *nie* zostały uzyskane, co pozwala im działać równolegle. Po przejściu tej tablicy zadań do `context.df.Task.all`, możemy odzyskać zadanie, które nie zostanie *wykonane, dopóki wszystkie operacje kopiowania nie zostaną zakończone.* Jeśli znasz [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) się na JavaScript, to nie jest dla Ciebie nowy. Różnica polega na tym, że te zadania mogą być uruchomione na wielu maszynach wirtualnych jednocześnie, a rozszerzenie Funkcje trwałe zapewnia, że wykonanie end-to-end jest odporne na przetwarzanie procesu.

> [!NOTE]
> Chociaż zadania są koncepcyjnie podobne do `context.df.Task.all` obietnic `context.df.Task.any` JavaScript, funkcje programu orchestrator powinny używać i zamiast `Promise.all` i `Promise.race` zarządzać równoległości zadań.

Po ustąpieniu `context.df.Task.all`z , wiemy, że wszystkie wywołania funkcji zostały zakończone i zwróciły wartości z powrotem do nas. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę bajtów przekazanych, więc obliczanie sumy całkowita liczba bajtów jest kwestią dodawania wszystkich tych wartości zwracanych razem.

---

### <a name="helper-activity-functions"></a>Funkcje aktywności pomocnika

Funkcje działania pomocnika, podobnie jak w przypadku innych przykładów, są tylko regularne funkcje, które używają powiązania `activityTrigger` wyzwalacza.

#### <a name="e2_getfilelist-activity-function"></a>funkcja aktywności E2_GetFileList

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Plik *function.json* `E2_GetFileList` dla wygląda następująco:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

A oto realizacja:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funkcja używa modułu `readdirp` (wersja 2.x) do rekursywnego odczytu struktury katalogów.

---

> [!NOTE]
> Możesz się zastanawiać, dlaczego nie można po prostu umieścić ten kod bezpośrednio do funkcji koordynatora. Można, ale to złamać jedną z podstawowych zasad funkcji koordynatora, który jest, że nigdy nie należy wykonywać we/wy, w tym dostępu do lokalnego systemu plików. Aby uzyskać więcej informacji, zobacz [Ograniczenia kodu funkcji programu Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob funkcja aktywności

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Należy zainstalować pakiet `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet, aby uruchomić przykładowy kod.

Funkcja używa niektórych zaawansowanych funkcji powiązania usługi Azure Functions (czyli użycie [ `Binder` parametru),](../functions-dotnet-class-library.md#binding-at-runtime)ale nie trzeba się martwić o te szczegóły na potrzeby tego przewodnika.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Plik *function.json* `E2_CopyFileToBlob` dla jest podobnie proste:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Implementacja Języka JavaScript używa zestawu [SDK usługi Azure Storage dla węzła](https://github.com/Azure/azure-storage-node) do przekazywania plików do usługi Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Implementacja ładuje plik z dysku i asynchronicznie strumienie zawartości do obiektu blob o tej samej nazwie w kontenerze "kopie zapasowe". Zwracana wartość to liczba bajtów skopiowanych do magazynu, która jest następnie używana przez funkcję koordynatora do obliczania sumy zagregowanej.

> [!NOTE]
> Jest to doskonały przykład przenoszenia operacji `activityTrigger` we/wy do funkcji. Praca może być nie tylko rozproszona na wielu różnych komputerach, ale także korzyści z przechodzenia do punktu kontrolnego postępu. Jeśli proces hosta zostanie zakończony z jakiegokolwiek powodu, wiesz, które przesyłanie zostało już zakończone.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aranżację można uruchomić, wysyłając następujące żądanie HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Wywoływana `HttpStart` funkcja działa tylko z zawartością w formacie JSON. Z tego powodu `Content-Type: application/json` nagłówek jest wymagany, a ścieżka katalogu jest kodowana jako ciąg JSON. Ponadto fragment kodu HTTP zakłada, że w `host.json` pliku znajduje się wpis, który usuwa domyślny `api/` prefiks ze wszystkich adresów URL funkcji wyzwalania HTTP. Znaczniki dla tej konfiguracji można `host.json` znaleźć w pliku w przykładach.

To żądanie HTTP `E2_BackupSiteContent` wyzwala koordynatora i `D:\home\LogFiles` przekazuje ciąg jako parametr. Odpowiedź zawiera łącze, aby uzyskać stan operacji tworzenia kopii zapasowej:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W zależności od liczby plików dziennika, które masz w aplikacji funkcji, ta operacja może potrwać kilka minut. Możesz uzyskać najnowszy stan, odpytując `Location` adres URL w nagłówku poprzedniej odpowiedzi HTTP 202.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

W takim przypadku funkcja jest nadal uruchomiona. Można zobaczyć dane wejściowe, które zostały zapisane w stanie orkiestratora i ostatni zaktualizowany czas. Można nadal używać `Location` wartości nagłówka do sondowania do zakończenia. Gdy stan jest "Zakończone", zostanie wyświetlona wartość odpowiedzi HTTP podobna do następującej:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Teraz widać, że aranżacja jest kompletna i w przybliżeniu, ile czasu zajęło, aby zakończyć. Pojawi się również wartość `output` pola, która wskazuje, że około 450 KB dzienników zostało przekazanych.

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazał, jak wdrożyć wzór fan-out/fan-in. Następny przykład pokazuje, jak zaimplementować wzorzec monitora przy użyciu [trwałych czasomierzy](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Uruchamianie próbki monitora](durable-functions-monitor.md)