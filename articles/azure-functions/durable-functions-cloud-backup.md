---
title: Fan-wyjściowego/fan-w scenariuszach w funkcjach trwałe - Azure
description: Dowiedz się, jak wdrożyć scenariusz dla fan-wyjściowego wentylator — ruch przychodzący w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e7b7b6af1f41eb0077d8a8605eb2a553c251f8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763852"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-wyjściowego/fan-w scenariuszu w funkcjach trwałe — przykład kopii zapasowej chmury

*Fan-wyjściowego/fan-w* odwołuje się do struktury wykonywane jednocześnie wiele funkcji, a następnie wykonuje niektóre agregacji na wyniki. W tym artykule opisano przykładu korzystającego z [trwałe funkcje](durable-functions-overview.md) do zaimplementowania scenariusza fan-w/fan-wyjściowego. Próbka jest trwałe funkcję, która tworzy kopię zapasową wszystkich lub niektórych zawartości aplikacji do magazynu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* [Instalowanie funkcji trwałe](durable-functions-install.md).
* Zakończenie [Hello sekwencji](durable-functions-sequence.md) wskazówki.

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie funkcji przekazywania wszystkich plików w katalogu rekursywnie określony katalog do magazynu obiektów blob. One również liczba całkowita liczba bajtów, które zostały przekazane.

Istnieje możliwość zapisu pojedynczą funkcję, która zajmuje się wszystko. Główny problem, należy uruchomić w jest **skalowalność**. Wykonanie jednej funkcji można uruchomić tylko na jednej maszynie Wirtualnej, więc ogranicza przepływność przepływność tej jednej maszyny Wirtualnej. Inny problem jest **niezawodności**. W przypadku połowie awarii, za pośrednictwem lub cały proces wymaga więcej niż 5 minut, kopia zapasowa może nie działać w stanie częściowo ukończone. Następnie może zaistnieć ponownego uruchomienia.

Bardziej niezawodne rozwiązanie będzie można zapisać dwie funkcje regularnych: jeden czy wyliczania plików i Dodaj nazwy plików do kolejki, a inny może odczytywać z kolejki i przekazać pliki do magazynu obiektów blob. Jest to lepszą wydajność i niezawodność, ale użytkownik musi obsługiwać i zarządzać nim kolejki. Co ważniejsze, wprowadzono znaczące złożoności w zakresie **stanu zarządzania** i **koordynacji** Chcąc żadne czynności, jak raport całkowita liczba bajtów przekazany.

Podejście trwałe funkcji daje wszystkich wymienionych korzyści z bardzo niskie obciążenie.

## <a name="the-functions"></a>Funkcje

W tym artykule opisano następujące funkcje w przykładowej aplikacji:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

W poniższych sekcjach opisano konfigurację i kodu, które są używane dla języka C# skryptów. Kod dla tworzenia Visual Studio jest wyświetlany na końcu tego artykułu.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestration kopii zapasowych w chmurze (Visual Studio Code i Azure portal przykładowy kod)

`E2_BackupSiteContent` Funkcja korzysta ze standardu *function.json* dla funkcji programu orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Oto kod, który implementuje funkcję programu orchestrator:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ta funkcja orchestrator zasadniczo wykonuje następujące czynności:

1. Trwa `rootDirectory` wartość jako parametr wejściowy.
2. Wywołania funkcji, aby uzyskać listę plików w obszarze cykliczne `rootDirectory`.
3. Wielu wywołań funkcji równoległych do przekazania każdego pliku do magazynu obiektów Blob Azure.
4. Czeka na wszystkich przekazywania zakończyć.
5. Zwraca sumę całkowitą liczbę bajtów, które zostały przekazane do magazynu obiektów Blob Azure.

Powiadomienie `await Task.WhenAll(tasks);` (C#) i `yield context.df.Task.all(tasks);` wiersza (Javascript). Wszystkie wywołania do `E2_CopyFileToBlob` zostały funkcja *nie* oczekiwane. Jest to zamierzone, aby umożliwić ich równolegle. Jeśli przekazywana tej tablicy zadań w celu `Task.WhenAll`, uzyskujemy ponownie zadanie, które nie zakończyć *ukończenie wszystkich operacji kopiowania ma*. Jeśli wiesz z zadań równoległych biblioteki (TPL) w środowisku .NET, a następnie nie jest to nowe dla Ciebie. Różnica polega na te zadania mogą być wykonywane na wiele maszyn wirtualnych jednocześnie, czy rozszerzenie funkcji trwałe zapewnia, że wykonanie end-to-end jest odporność na odtwarzanie procesów.

Zadania są bardzo podobne do koncepcji JavaScript ze zobowiązania. Jednak `Promise.all` ma pewne różnice z `Task.WhenAll`. Pojęcie `Task.WhenAll` systemie w ramach `durable-functions` modułu JavaScript i jest na wyłączność do niego.

Po oczekiwanie na z `Task.WhenAll` (lub reaguje z `context.df.Task.all`), wiemy, że wszystkie wywołania funkcji zakończył pracę i zwrócić wartości z powrotem do nas. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę bajtów przekazać dzięki obliczaniu sum liczba całkowita liczba bajtów jest dodanie wszystkich tych wartości zwracane razem.

## <a name="helper-activity-functions"></a>Funkcje działalności pomocy

Funkcje działalności pomocy, podobnie jak w przypadku innych próbek są po prostu regularne funkcje programu wykorzystujące `activityTrigger` wyzwolenia powiązania. Na przykład *function.json* plik `E2_GetFileList` wygląda podobnie do następującego:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A Oto implementacji:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Implementacja JavaScript `E2_GetFileList` używa `readdirp` moduł rekursywnie odczytać struktury katalogów.

> [!NOTE]
> Użytkownik może się zastanawiać, dlaczego właśnie nie umieść ten kod bezpośrednio do funkcji programu orchestrator. Było to możliwe, ale spowoduje to przerwanie jeden z podstawowych reguł funkcji programu orchestrator, które jest, że nigdy nie powinni zrobić we/wy, takie jak pliku lokalnego dostępu do systemu.

*Function.json* plik `E2_CopyFileToBlob` jest podobnie prosty:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Implementacja C# również jest dość proste. Zdarza się, aby korzystać z niektórych zaawansowanych funkcji usługi Azure Functions powiązań (oznacza to, że użycie `Binder` parametru), ale nie trzeba martwić się o te szczegóły na potrzeby tego przewodnika.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

Implementacja JavaScript nie ma dostępu do `Binder` funkcji usługi Azure functions, więc [zestawu SDK usługi Magazyn Azure dla węzła](https://github.com/Azure/azure-storage-node) odbywa się jej. Należy pamiętać, że zestaw SDK wymaga `AZURE_STORAGE_CONNECTION_STRING` ustawienia aplikacji.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementacja ładuje plik z dysku i asynchronicznie prześle strumieniowo zawartość do obiektu blob o takiej samej nazwie w kontenerze "kopie zapasowe". Wartość zwracana jest liczba bajtów skopiowane do magazynu, który jest następnie używany przez funkcję programu orchestrator do obliczenia agregacji sum.

> [!NOTE]
> To jest przykład doskonałe przenoszenia operacji We/Wy do `activityTrigger` funkcji. Nie tylko mogą pracy być rozproszone na wielu różnych maszyn wirtualnych, ale można również uzyskać korzyści wynikające z użycia punktów kontrolnych postęp. Jeśli proces hosta pobiera zakończone jakiejkolwiek przyczyny, wiadomo, przekazywania, które zostały już wykonane.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Orchestration można uruchomić poprzez wysłanie następujące żądania HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funkcja, która jest działa tylko w przypadku zawartości w formacie JSON. Z tego powodu `Content-Type: application/json` nagłówka jest wymagany i ścieżkę katalogu jest zakodowany jako ciąg w formacie JSON.

HTTP tego żądania wyzwalaczy `E2_BackupSiteContent` orchestrator i przekazuje ciąg `D:\home\LogFiles` jako parametr. Odpowiedź zawiera łącze do pobrania stanu operacji tworzenia kopii zapasowej:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W zależności od liczby plików dziennika w aplikacji funkcji tej operacji może potrwać kilka minut. Można pobrać najnowszy stan badając adres URL w `Location` nagłówka HTTP 202 poprzedniej odpowiedzi.

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

W takim przypadku funkcja nadal działa. Jest możliwe wyświetlić dane wejściowe, który został zapisany w stanie orchestrator i godzina ostatniej aktualizacji. Można nadal używać `Location` wartości nagłówka do sondowania ukończenia. Gdy jest w stanie "ukończone", zostanie wyświetlony wartość odpowiedzi HTTP podobny do następującego:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Teraz widać, że orchestration została zakończona i zajęło około ile czasu do ukończenia. Zobacz też wartość `output` pola, co oznacza, że około 450 KB Dzienniki zostały przekazane.

## <a name="visual-studio-sample-code"></a>Visual Studio przykładowy kod

Oto aranżacji jako pojedynczy plik C# w projektu programu Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Kolejne kroki

Ten przykład pokazuje, jak implementują wzorzec fan-wyjściowego/fan-w. Następna próbka pokazuje, jak wdrożyć przy użyciu wzorca monitor [trwałe czasomierze](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Uruchom monitor próbki](durable-functions-monitor.md)
