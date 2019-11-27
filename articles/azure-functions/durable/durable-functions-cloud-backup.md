---
title: Scenariusze dotyczące wentylatorów/wentylatorów w Durable Functions na platformie Azure
description: Dowiedz się, jak wdrożyć w Durable Functions rozszerzenia wentylator-out-wentylator w Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: a87a4edd544c2f7d8ff9c6415df2f2dda125f2bf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233001"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scenariusz na zewnątrz/wentylator w Durable Functions — przykład kopii zapasowej w chmurze

Wentylator */wentylator — w* odniesieniu do wzorca wykonywania wielu funkcji współbieżnie, a następnie wykonywania pewnej agregacji wyników. W tym artykule opisano przykład, który używa [Durable Functions](durable-functions-overview.md) do implementowania scenariusza wentylator-in/out. Przykładem jest funkcja trwała, która tworzy kopię zapasową całej lub pewnej zawartości witryny w usłudze Azure Storage.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie funkcja przekaże wszystkie pliki w określonym katalogu cyklicznie do magazynu obiektów BLOB. Liczą również łączną liczbę przekazanych bajtów.

Istnieje możliwość napisania pojedynczej funkcji, która zajmuje się wszystkimi elementami. Główną przyczyną problemu jest **skalowalność**. Pojedyncze wykonanie funkcji można uruchomić tylko na jednej maszynie wirtualnej, dzięki czemu przepływność będzie ograniczać przepływność tej pojedynczej maszyny wirtualnej. Innym problemem jest **niezawodność**. Jeśli wystąpi awaria w połowie, lub jeśli cały proces trwa dłużej niż 5 minut, tworzenie kopii zapasowej może zakończyć się niepowodzeniem w stanie częściowo zakończonym. Następnie należy ponownie uruchomić.

Bardziej niezawodną metodą jest zapisanie dwóch funkcji regularnych: jedna z nich spowoduje Wyliczenie plików i dodanie nazw plików do kolejki, a druga odczytana z kolejki i przekazanie plików do usługi BLOB Storage. Ta metoda jest lepsza pod względem przepływności i niezawodności, ale wymaga aprowizacji i zarządzania kolejką. Co ważniejsze, znacząca złożoność jest wprowadzana w zakresie **zarządzania Stanami** i **koordynacji** , jeśli chcesz zrobić coś więcej, na przykład Zgłoś łączną liczbę przekazanych bajtów.

Podejście Durable Functions zapewnia wszystkie wymienione korzyści z bardzo niskimi kosztami.

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

W poniższych sekcjach opisano konfigurację i kod, który jest używany C# do obsługi skryptów. Kod dla projektowania programu Visual Studio znajduje się na końcu artykułu.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Aranżacja kopii zapasowej w chmurze (Visual Studio Code i Azure Portal przykładowego kodu)

Funkcja `E2_BackupSiteContent` używa standardowego pliku *Function. JSON* dla funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Oto kod implementujący funkcję programu Orchestrator:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ta funkcja programu Orchestrator wykonuje następujące czynności:

1. Przyjmuje `rootDirectory` wartość jako parametr wejściowy.
2. Wywołuje funkcję, aby uzyskać cykliczną listę plików w obszarze `rootDirectory`.
3. Wykonuje wiele wywołań funkcji równoległych, aby przekazać każdy plik do Blob Storage platformy Azure.
4. Czeka na zakończenie wszystkich operacji przekazywania.
5. Zwraca łączną sumę bajtów przesłanych do usługi Azure Blob Storage.

Zwróć uwagę na wierszeC#`await Task.WhenAll(tasks);` () i `yield context.df.Task.all(tasks);` (JavaScript). Wszystkie indywidualne wywołania funkcji `E2_CopyFileToBlob` *nie* zostały oczekiwane, co umożliwia ich równoległe uruchamianie. Po przejściu tej tablicy zadań do `Task.WhenAll` (C#) lub `context.df.Task.all` (JavaScript) otrzymamy zadanie, które nie zostanie ukończone, *dopóki nie zostaną ukończone wszystkie operacje kopiowania*. Jeśli znasz bibliotekę zadań równoległych (TPL) w programie .NET lub [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) w języku JavaScript, to nie jest to nowe rozwiązanie. Różnica polega na tym, że te zadania mogą być uruchamiane jednocześnie na wielu maszynach wirtualnych, a rozszerzenie Durable Functions zapewnia odporność na zakończenie procesu odtwarzania.

> [!NOTE]
> Chociaż zadania są koncepcyjnie podobne do języka JavaScript niesie obietnice zwiększenia, funkcje programu Orchestrator powinny używać `context.df.Task.all` i `context.df.Task.any` zamiast `Promise.all` i `Promise.race` do zarządzania przetwarzanie równoległe zadań.

Po oczekiwaniu na `Task.WhenAll` (lub uzyskaniu z `context.df.Task.all`) wiemy, że wszystkie wywołania funkcji zostały wykonane i zwracają wartości z powrotem do nas. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę przekazanych bajtów, więc obliczenie łącznej liczby bajtów sum jest kwestią dodawania wszystkich zwracanych wartości.

## <a name="helper-activity-functions"></a>Funkcje działania pomocnika

Działania pomocnika, podobnie jak inne przykłady, są tylko regularnymi funkcjami, które używają powiązań wyzwalacza `activityTrigger`. Na przykład plik *Function. JSON* dla `E2_GetFileList` wygląda następująco:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A oto implementacja:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Implementacja języka JavaScript `E2_GetFileList` używa modułu `readdirp`, aby rekursywnie odczytać strukturę katalogów.

> [!NOTE]
> Być może zastanawiasz się, dlaczego nie można bezpośrednio umieścić tego kodu w funkcji programu Orchestrator. Może to jednak spowodować uszkodzenie jednej z podstawowych reguł funkcji programu Orchestrator, co oznacza, że nigdy nie powinny wykonywać operacji we/wy, w tym dostępu do lokalnego systemu plików.

Plik *Function. JSON* dla `E2_CopyFileToBlob` jest podobny do prostego:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C# Implementacja jest również prosta. Ma ona zastosowanie do niektórych zaawansowanych funkcji powiązań Azure Functions (to jest użycie parametru `Binder`), ale nie trzeba martwić się o te szczegóły na potrzeby tego przewodnika.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

Implementacja języka JavaScript nie ma dostępu do `Binder` funkcji Azure Functions, więc [zestaw SDK usługi Azure Storage dla węzła](https://github.com/Azure/azure-storage-node) zajmie się jego miejscem.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementacja ładuje plik z dysku i asynchronicznie przesyła zawartość do obiektu BLOB o takiej samej nazwie w kontenerze "Backups". Wartość zwracana to liczba bajtów skopiowanych do magazynu, które są następnie używane przez funkcję programu Orchestrator do obliczania sumy zagregowanej.

> [!NOTE]
> Jest to idealny przykład przeniesień operacji we/wy na funkcję `activityTrigger`. Nie tylko można rozłożyć pracy na wiele różnych maszyn wirtualnych, ale można również uzyskać korzyści z przetworzenia punktów kontrolnych postępu. Jeśli proces hosta zostanie zakończony z dowolnego powodu, wiesz, które operacje przekazywania zostały już ukończone.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aranżację można uruchomić, wysyłając następujące żądanie HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Funkcja `HttpStart`, która jest wywoływana, działa tylko z zawartością sformatowaną w formacie JSON. Z tego powodu nagłówek `Content-Type: application/json` jest wymagany, a ścieżka katalogu jest zaszyfrowana jako ciąg JSON. Ponadto fragment kodu HTTP zakłada, że w pliku `host.json` znajduje się wpis, który usuwa domyślny prefiks `api/` ze wszystkich adresów URL funkcji wyzwalacza HTTP. Znaczniki tej konfiguracji można znaleźć w pliku `host.json` w przykładach.

To żądanie HTTP wyzwala `E2_BackupSiteContent` koordynator i przekazuje ciąg `D:\home\LogFiles` jako parametr. Odpowiedź zawiera link umożliwiający uzyskanie stanu operacji tworzenia kopii zapasowej:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W zależności od liczby plików dziennika w aplikacji funkcji ta operacja może potrwać kilka minut. Najnowszego stanu można uzyskać, przechodząc do adresu URL w nagłówku `Location` poprzedniej odpowiedzi HTTP 202.

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

W takim przypadku funkcja jest nadal uruchomiona. Możesz zobaczyć dane wejściowe, które zostały zapisane w stanie programu Orchestrator i czas ostatniej aktualizacji. Można nadal używać wartości nagłówka `Location` do sondowania w celu ukończenia. Gdy stan to "ukończono", zobaczysz wartość odpowiedzi HTTP podobną do następującej:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Teraz można zobaczyć, że aranżacja została ukończona i około chwilą jej ukończenia. Zostanie również wyświetlona wartość pola `output`, co oznacza, że przekazano około 450 KB dzienników.

## <a name="visual-studio-sample-code"></a>Przykładowy kod programu Visual Studio

Oto aranżacja jako pojedynczy C# plik w projekcie programu Visual Studio:

> [!NOTE]
> Musisz zainstalować pakiet NuGet `Microsoft.Azure.WebJobs.Extensions.Storage`, aby uruchomić przykładowy kod poniżej.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje, jak zaimplementować wzorzec wentylatorów/wentylatorów. Następny przykład pokazuje, jak zaimplementować wzorzec monitora przy użyciu [trwałych czasomierzy](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Uruchamianie przykładowego monitora](durable-functions-monitor.md)