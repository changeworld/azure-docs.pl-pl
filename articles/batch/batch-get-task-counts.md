---
title: Monitorowanie postępu zadania przez zliczanie zadań według stanu - partii zadań Azure | Dokumentacja firmy Microsoft
description: Monitorować postęp zadania, wywołując operację Get liczby zadań, aby liczba zadań dla zadania. Możesz uzyskać liczby aktywnych, uruchomione i zakończonych zadań i zadań, które zakończyło się pomyślnie lub nie powiodło się.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: danlep
ms.openlocfilehash: bc112ed5b481560362962d6b550d336de6b3d9b4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Liczba zadań według stanu, aby monitorować postęp zadania (wersja zapoznawcza)

Partia zadań Azure umożliwia wydajny sposób, aby monitorować postęp zadania podczas jego wykonywania swoich zadań. Możesz wywołać [pobrać liczby zadań] [ rest_get_task_counts] operację, aby dowiedzieć się, jak wielu zadań, które są w stanie active, uruchomione lub zostało zakończone i ma liczbę powodzeniem lub niepowodzeniem. Przez liczenie zadania w każdym stanie, łatwiej można wyświetlić postęp zadania dla użytkownika lub wykrywania nieoczekiwane opóźnienia lub błędy, które mogą wpłynąć na zadania.

> [!IMPORTANT]
> Operacja Get liczby zadań jest obecnie w wersji zapoznawczej i nie jest jeszcze dostępna w Azure dla instytucji rządowych, chińskiej wersji platformy Azure i platformy Azure w Niemczech. 
>
>

## <a name="how-tasks-are-counted"></a>Jak zliczane są zadania

Operacja Get liczby zadań liczby zadań według stanu, w następujący sposób:

- Zadanie jest traktowane jako **active** gdy jest w kolejce i możliwość uruchamiania, ale nie jest aktualnie przypisany do węzła obliczeń. Zadanie również jest traktowane jako **active** Jeśli jest on zależny od zadaniem nadrzędnym, które nie zostało jeszcze zakończone. Aby uzyskać więcej informacji o zależnościach zadań, zobacz [utworzyć zależności zadań do wykonywania zadań, które są zależne od innych zadań](batch-task-dependencies.md). 
- Zadanie jest traktowane jako **systemem** po jego przypisany do węzła obliczeń, ale nie zostało jeszcze zakończone. Zadanie jest traktowane jako **systemem** po jej stan to `preparing` lub `running`, wskazywany przez [uzyskać informacje o zadaniu] [ rest_get_task] operacji.
- Zadanie jest traktowane jako **ukończone** gdy nie jest już mogą być uruchamiane. Zadania są liczone jako **ukończone** ma zazwyczaj albo zakończyło się pomyślnie, lub zakończy się niepowodzeniem i również Przekroczono limit ponownych prób. 

Operacja Get liczby zadań udostępnia również ile zadań ma powodzeniem lub niepowodzeniem. Partii określa, czy zadanie zakończyło się pomyślnie lub nie powiodło się, sprawdzając **wynik** właściwości [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] właściwości:

    - Zadanie jest traktowane jako **zakończyło się pomyślnie** Jeśli wynikiem wykonania zadania jest `success`.
    - Zadanie jest traktowane jako **nie powiodło się** Jeśli wynikiem wykonania zadania jest `failure`.

Aby uzyskać więcej informacji na temat stanów zadań, zobacz [uzyskać informacje o zadaniu][rest_get_task].

Poniższy przykład kodu .NET pokazano, jak można pobrać liczby zadań według stanu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Można użyć podobnego wzorca pozostałej i innych obsługiwanych języków można pobrać liczby zadań dla zadania. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Sprawdzanie liczby zadań spójności

Usługa partia zadań agreguje liczby zadań przez zbieranie danych z wielu części asynchroniczne Rozproszony system. Aby zapewnić, że liczby zadań są poprawne, partii udostępnia dodatkowe sprawdzanie poprawności stanu liczby, wykonując spójności sprawdza przed wiele składników systemu. Wsadowe wykonuje te sprawdzenia spójności tak długo, jak jest krótsza niż 200 000 zadań w zadaniu. W przypadku mało prawdopodobne, że sprawdzanie spójności znajdzie błędy partii poprawia wynik operacji Get liczby zadań, na podstawie wyników sprawdzania spójności. Kontrola spójności jest dodatkowy poziom, aby upewnić się, że klienci, którzy polegać na operację Get liczby zadań uzyskać właściwe informacje, które są im niezbędne do ich rozwiązania.

**Stan** właściwość w odpowiedzi wskazuje, czy partii jest wykonywane sprawdzanie spójności. Jeśli partii nie mógł sprawdzić stan wlicza się rzeczywista stany przechowywanych w systemie, a następnie **stan** właściwość jest ustawiona na `unvalidated`. Ze względu na wydajność wsadu nie będzie wykonywać sprawdzanie spójności Jeśli zadanie zawiera więcej niż 200 000 zadań, więc **stan** może mieć ustawioną właściwość `unvalidated` w takim przypadku. Jednak liczba zadań nie jest zawsze niewłaściwy w takim przypadku nawet utraty bardzo ograniczoną ilość danych jest wysoce nieprawdopodobne. 

Po zmianie stanu zadania potoku agregacji przetwarza zmiany w ciągu kilku sekund. Operacja Get liczby zadań odzwierciedla liczby zadań zaktualizowane w tym okresie. Jednak jeśli potoku agregacji chybień zmianę stanu zadań, następnie tej zmiany nie jest zarejestrowany do czasu następnego przebieg sprawdzania poprawności. W tym czasie liczby zadań mogą być nieco niedokładne z powodu brakujących zdarzeń, ale są one usunąć na następny przebieg sprawdzania poprawności.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Najlepsze rozwiązania dotyczące inwentaryzacji zadania podrzędne zadania

Podczas wywoływania operacji Get liczby zadań jest najbardziej wydajnym sposobem zwróciło podstawowe liczby zadania według stanu. Jeśli używasz wersji usługi partii 2017-06-01.5.1, zaleca się zapisywania lub aktualizowania kod, aby używał pobrać liczby zadań.

Operacji Get liczby zadań nie jest starszy niż 2017-06-01.5.1 dostępne w wersjach usługi partii. Jeśli używasz starszej wersji usługi użyć kwerendy listy, aby zamiast tego liczba zadań w ramach zadania. Aby uzyskać więcej informacji, zobacz [tworzenia zapytań do listy zasobów partii wydajnie](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). Artykuł zawiera omówienie głównej zasobów usługi partia zadań, takich jak pule, węzły obliczeniowe, zadań i zadań, a zawiera przegląd funkcji usługi.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](batch-dotnet-get-started.md) lub języka [Python](batch-python-tutorial.md). Te artykuły wprowadzające informacje pomocne przy działającą aplikację, która używa usługi partia zadań do wykonania na wielu węzłach obliczeniowych dla obciążenia.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
