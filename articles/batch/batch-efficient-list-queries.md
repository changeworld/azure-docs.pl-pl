---
title: Projektowanie zapytania wydajne listy — partii zadań Azure | Dokumentacja firmy Microsoft
description: Zwiększyć wydajność przez filtrowanie zapytania, gdy żąda informacji na temat zasobów usługi partia zadań, takich jak pule, zadań, zadań i węzły obliczeniowe.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950e422b3076e5abd5db6dd0ac452fa1c2d500d0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129272"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Wydajnie tworzyć zapytania do listy zasobów usługi partia zadań

Tutaj dowiesz się, jak zwiększyć wydajność aplikacji partii zadań Azure dzięki zmniejszeniu ilości danych zwracanych przez usługę kwerend zadania, zadania i obliczeniowe węzłów o [partiami platformy .NET] [ api_net]biblioteki.

Prawie wszystkie aplikacje partii muszą wykonywać niektórych monitorowania lub innych operacji, który odpytuje usługa partia zadań, często w regularnych odstępach czasu. Na przykład aby ustalić, czy istnieją wszystkie zadania w kolejce pozostały w ramach zadania, należy uzyskać danych dla każdego zadania w zadaniu. Aby określić stan węzłów w puli, należy uzyskać dane na każdym węźle w puli. W tym artykule opisano sposób wykonywania takich kwerend w najbardziej wydajny sposób.

> [!NOTE]
> Usługa partia zadań zapewnia obsługę interfejsu API specjalne typowy scenariusz inwentaryzacji zadań w ramach zadania. Zamiast korzystać z zapytania listy tych, można wywołać [pobrać liczby zadań] [ rest_get_task_counts] operacji. Liczby zadań Get wskazuje, ile zadań oczekujących, uruchomiona lub Zakończ i ile zadań ma powodzeniem lub niepowodzeniem. Liczba zadań Get jest bardziej efektywne niż zapytanie listy. Aby uzyskać więcej informacji, zobacz [liczba zadań dla zadania według stanu (wersja zapoznawcza)](batch-get-task-counts.md). 
>
> Operacji Get liczby zadań nie jest starszy niż 2017-06-01.5.1 dostępne w wersjach usługi partii. Jeśli używasz starszej wersji usługi użyć kwerendy listy, aby zamiast tego liczba zadań w ramach zadania.
>
> 

## <a name="meet-the-detaillevel"></a>Atrybut DetailLevel spełnia
W środowisku produkcyjnym aplikacji partii jednostek, takich jak zadania, zadania i węzły obliczeniowe można numerów w tysięcy. Gdy użytkownik żąda informacji na temat tych zasobów, potencjalnie dużą ilość danych musi "przechodzą przewodowo" z usługi partia zadań do aplikacji w każdym zapytaniu. Poprzez ograniczenie liczby elementów i typu danych zwracane przez zapytanie, może zwiększyć szybkość zapytań i w związku z tym wydajność aplikacji.

To [partiami platformy .NET] [ api_net] list fragment kodu interfejsu API *co* zadanie, które jest skojarzone z zadaniem, wraz z *wszystkie* właściwości każdego zadania:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Jednak bardziej efektywnego zapytanie listy, można wykonać przez zastosowanie "poziom szczegółowości" do zapytania. Aby to zrobić, podając [ODATADetailLevel] [ odata] do obiektu [JobOperations.ListTasks] [ net_list_tasks] metody. Ta Wstawka kodu zwraca tylko identyfikator, wiersz polecenia i właściwości informacji węzła obliczeń zakończonych zadań:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

W tym przykładowym scenariuszu, jeśli istnieją tysiące zadania, wyniki z drugiego zapytania zwykle powróci znacznie szybciej niż pierwszy. Więcej informacji o używaniu ODATADetailLevel wśród elementów przy użyciu interfejsu API platformy .NET partii jest dołączony [poniżej](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Zdecydowanie zalecamy, aby użytkownik *zawsze* podać ODATADetailLevel obiekt do listy połączeń interfejs API .NET w celu zapewnienia maksymalnej wydajności i wydajność aplikacji. Umożliwia określenie poziomu szczegółowości, może pomóc zmniejszyć czasy odpowiedzi usługi partii, poprawić wykorzystanie sieci i zminimalizować użycie pamięci przez aplikacje klienckie.
> 
> 

## <a name="filter-select-and-expand"></a>Filtruj, wybierz i rozwiń węzeł
[Partiami platformy .NET] [ api_net] i [REST partii] [ api_rest] interfejsów API umożliwiają Zmniejsz liczbę elementów, które są zwracane w postaci listy, a także ilość informacji, która jest zwracana dla każdego. Możesz to zrobić, określając **filtru**, **wybierz**, i **rozwiń ciągów** podczas wykonywania zapytania z listy.

### <a name="filter"></a>Filtr
Ciąg filtru jest wyrażenie, które zmniejsza liczbę elementów, które są zwracane. Na przykład listy zadania uruchomione zadania lub listy tylko węzły obliczeniowe, które są gotowe do uruchamiania zadań.

* Ciąg filtru składa się z co najmniej jednego wyrażenia z wyrażeniem, które składa się z nazwy właściwości, operator i wartość. Właściwości, które można określić są specyficzne dla poszczególnych typów jednostek, które można zbadać, jak operatory, które są obsługiwane dla każdej właściwości.
* Wiele wyrażeń można łączyć przy użyciu operatorów logicznych `and` i `or`.
* W tym przykładzie filtrowanie list ciągów tylko zadania uruchamiania "renderowania": `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Wybierz pozycję
Wybierz parametry ogranicza wartości właściwości, które są zwracane dla każdego elementu. Określ listę nazw właściwości, a dla elementów w wynikach zapytania są zwracane tylko wartości tych właściwości.

* Wybierz parametry składa się z listy rozdzielanej przecinkami nazw właściwości. Można określić właściwości dla typu obiektu, który jest kwerenda.
* Ten ciąg wybierz przykład określa, że powinny być zwracane tylko trzy wartości właściwości dla każdego zadania: `id, state, stateTransitionTime`.

### <a name="expand"></a>Rozwiń
Ciąg rozszerzenia zmniejsza liczbę wywołań interfejsu API, które są wymagane do uzyskania określonych informacji. Korzystając z ciągu Rozwiń, można uzyskać więcej informacji o każdym elemencie przy użyciu jednego wywołania interfejsu API. Zamiast pierwszego uzyskiwania listy jednostek, następnie żąda informacji dla każdego elementu na liście ciągu rozwiń służy do uzyskania tych samych informacji w jednym wywołaniu interfejsu API. Mniej wywołań interfejsu API oznaczają lepszą wydajność.

* Podobnie jak wybierz ciąg, ciąg rozwiń Określa, czy niektóre dane są uwzględniane w wynikach zapytania listy.
* Ciąg rozwiń jest obsługiwana tylko w przypadku, gdy jest używany w listę zadań, harmonogramy zadań, zadań i pul. Obecnie obsługuje on tylko informacje statystyczne.
* Gdy wszystkie właściwości są wymagane i nie wybierz podano ciągu, ciąg rozwiń *musi* można użyć, aby uzyskać informacje statystyczne. Jeśli wybierz ciąg jest używany do uzyskania podzbiór właściwości, następnie `stats` można określić w ciągu wybierz i rozwiń ciągu nie muszą być określone.
* W tym przykładzie rozwiń ciąg Określa, że dla każdego elementu na liście powinny być zwracane informacje statystyczne: `stats`.

> [!NOTE]
> Podczas konstruowania dowolnego typu ciąg zapytania trzy (filtrowanie, wybierz i rozwiń), należy upewnić się, że nazwy właściwości i litery zgodną ich odpowiedniki element interfejsu API REST. Na przykład podczas pracy z platformą .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) klasy, należy określić **stanu** zamiast **stanu**, nawet jeśli jest właściwości platformy .NET [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Zobacz w poniższych tabelach mapowania właściwości między .NET i interfejsów API REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Reguły filtra wybierz i rozwiń ciągów
* Nazwy właściwości w filtrze, wybierz i rozwiń ciągi powinny być wyświetlane, tak jak w [REST partii] [ api_rest] interfejsu API — nawet wtedy, gdy używasz [partiami platformy .NET] [ api_net]lub jednego z innych zestawów SDK usługi partia zadań.
* Wszystkie nazwy właściwości jest rozróżniana wielkość liter, ale wartości właściwości są bez uwzględniania wielkości liter.
* Data i godzina ciągów może być jednym z dwóch formatów i musi być poprzedzony `DateTime`.
  
  * Przykładowy format W3C DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 Przykładowy format: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Logiczna ciągi są albo `true` lub `false`.
* Jeśli określono nieprawidłowe właściwości lub operatora `400 (Bad Request)` spowoduje błąd.

## <a name="efficient-querying-in-batch-net"></a>Efektywne wykonywania zapytania w partiami platformy .NET
W ramach [partiami platformy .NET] [ api_net] interfejsu API, [ODATADetailLevel] [ odata] klasa jest używana do podawania filtru, wybierz i rozwiń ciągów, aby wyświetlić listę operacje. Klasa ODataDetailLevel ma trzy właściwości publicznych ciągów, które może być określony w konstruktorze lub ustawić bezpośrednio dla obiektu. Można następnie przekazać obiekt ODataDetailLevel jako parametr do różnych operacji listy takich jak [ListPools][net_list_pools], [ListJobs][net_list_jobs], i [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: Ogranicz liczbę elementów, które są zwracane.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Określ wartości właściwości, które są zwracane z każdym elementem.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: pobieranie danych dla wszystkich elementów w jeden interfejs API Wywołaj zamiast wywołań dla każdego elementu.

Poniższy fragment kodu używa interfejsu API programu .NET partii w celu wydajnego wykonywania zapytań usługi partia zadań dla statystyk określony zbiór pule. W tym scenariuszu użytkownik partii ma pule zarówno testowego i produkcyjnego. Test puli identyfikatorów nazwy są poprzedzone ciągiem "test", a puli produkcji identyfikatory nazwy są poprzedzone ciągiem "produkcyjną". We fragmencie *myBatchClient* jest prawidłowo zainicjowany wystąpieniem [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) klasy.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Wystąpienie [ODATADetailLevel] [ odata] skonfigurowanego wybierz i klauzule rozwiń mogą również zostać przekazane do odpowiedniej metody Get, takich jak [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), Aby ograniczyć ilość danych, która jest zwracana.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Wsadowe REST do mapowania interfejs API .NET
Nazwy właściwości w filtrze, wybierz i rozwiń ciągów *musi* ich odpowiedniki interfejsu API REST, zarówno w nazwie i w przypadku uwzględnienia. W poniższych tabelach zawierają mapowania między partnerami .NET i interfejsu API REST.

### <a name="mappings-for-filter-strings"></a>Mapowania dla filtru ciągów
* **Metody listy .NET**: każdej z metod interfejsu API platformy .NET w tej kolumnie akceptuje [ODATADetailLevel] [ odata] obiektu jako parametr.
* **Żądania listy REST**: strona każdego interfejsu API REST powiązany z tej kolumny zawiera tabelę, która określa właściwości i operacje, które są dozwolone w *filtru* ciągów. Użyjesz tych nazw właściwości i operacji utworzenia [ODATADetailLevel.FilterClause] [ odata_filter] ciąg.

| Metody listy .NET | Żądania listy REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Lista certyfikatów dla konta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Lista plików skojarzonych z zadania][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Lista stanu przygotowanie zadania i wersji zadania dla zadania][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Wyświetlić zadania konta][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista plików w węźle][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Lista zadań skojarzonych z zadaniem][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista harmonogramów zadań w ramach konta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Listę zadań skojarzonych z harmonogramu zadań][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Listy węzłów obliczeniowych w puli][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Wyświetl listę pul konta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapowania dla wybierz ciągów
* **Typy .NET partii**: typy interfejsu API partii .NET.
* **Interfejs API REST jednostek**: każdej strony w tej kolumnie zawiera co najmniej jednej tabeli, które listę nazw właściwości interfejsu API REST dla typu. Te nazwy właściwości są używane podczas utworzymy *wybierz* ciągów. Utworzenia użyje tych samych nazw właściwości [ODATADetailLevel.SelectClause] [ odata_select] ciąg.

| Typy .NET partii | Jednostki interfejsu API REST |
| --- | --- |
| [Certyfikat][net_cert] |[Uzyskiwanie informacji o certyfikacie][rest_get_cert] |
| [CloudJob][net_job] |[Pobierz informacje o zadaniu][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Pobierz informacje o harmonogram zadań][rest_get_schedule] |
| [ComputeNode][net_node] |[Pobierz informacje o węźle][rest_get_node] |
| [CloudPool][net_pool] |[Pobierz informacje o puli][rest_get_pool] |
| [CloudTask][net_task] |[Pobierz informacje o zadaniu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Przykład: skonstruować ciąg filtru
Podczas konstruowania ciąg filtru dla [ODATADetailLevel.FilterClause][odata_filter], zapoznaj się z powyższej tabeli w obszarze "Mapowania dla filtru ciągów" na stronę dokumentacji Znajdź interfejsu API REST, który odpowiada Operacja listy, który chcesz wykonać. W pierwszej tabeli multirow na tej stronie znajdziesz filtrowanie właściwości i ich operatory obsługiwane. Jeśli chcesz pobrać wszystkie zadania, którego kod zakończenia: różną od zera, na przykład ten wiersz na [listy zadania skojarzone z zadaniem] [ rest_list_tasks] Określa dozwolone operatory i odpowiednie właściwości ciągu:

| Właściwość | Operacje dozwolone | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

W związku z tym będzie ciąg filtru, aby uzyskać listę wszystkich zadań z kodem zakończenia różną od zera:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Przykład: skonstruować wybierz ciąg
Aby utworzyć [ODATADetailLevel.SelectClause][odata_select], zapoznaj się z powyższej tabeli, w obszarze "Mapowania dla ciągów select" i przejdź do strony interfejsu API REST, który odpowiada typowi obiektu, który chcesz wyświetlić. W pierwszej tabeli multirow na tej stronie można znaleźć właściwości selectable i ich operatory obsługiwane. Jeśli chcesz pobrać tylko identyfikator i wiersz polecenia dla każdego zadania na liście, na przykład można znaleźć te wiersze w tabeli stosowanych w [uzyskać informacje o zadaniu][rest_get_task]:

| Właściwość | Typ | Uwagi |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Następnie będzie wybierz ciąg tylko identyfikator i wiersza polecenia do każdego zadania wymienione w tym:

`id, commandLine`

## <a name="code-samples"></a>Przykłady kodu
### <a name="efficient-list-queries-code-sample"></a>Przykładowy kod zapytania wydajne listy
Zapoznaj się z [EfficientListQueries] [ efficient_query_sample] przykładowy projekt w witrynie GitHub, aby zobaczyć, jak skuteczne zapytań listy może wpłynąć na wydajność w aplikacji. Tę aplikację konsolową C# tworzy i dodaje dużej liczby zadań do zadania. Następnie należy go wielu wywołań do [JobOperations.ListTasks] [ net_list_tasks] — metoda i przekazuje [ODATADetailLevel] [ odata] obiektów, które są skonfigurowano wartości różnych właściwości różnią się ilość danych do zwrócenia. Generuje dane wyjściowe podobne do następującego:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Jak pokazano na czas, można znacznie zmniejszyć czas odpowiedzi na zapytania, ograniczając właściwości oraz liczbę elementów, które są zwracane. Można znaleźć to i inne przykładowych projektach w [azure partii próbek] [ github_samples] repozytorium w witrynie GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics biblioteki i kod przykładowy
Oprócz powyższych przykładowy kod EfficientListQueries można znaleźć [BatchMetrics] [ batch_metrics] projektu w [azure partii próbek] [ github_samples]Repozytorium GitHub. Przykładowy projekt BatchMetrics pokazano, jak skutecznie monitorować postęp zadania partii zadań Azure za pomocą interfejsu API partii.

[BatchMetrics] [ batch_metrics] próbki obejmuje projektu biblioteki klas .NET, które można zastosować do własnych projektów i prosty program wiersza polecenia do wykonywania i przedstawiają sposób używania biblioteki.

Przykładową aplikację w projekcie przedstawiono następujące operacje:

1. Wybieranie określonych atrybutów w celu pobrania właściwości, które są potrzebne
2. Filtrowanie na czas przejścia stanu w celu pobrania tylko zmiany od czasu ostatniej kwerendy

Na przykład następująca metoda pojawia się w bibliotece BatchMetrics. Zwraca ODATADetailLevel, która określa, że tylko `id` i `state` właściwości mają być uzyskiwane dla jednostek, które będą pytani. On również określa, że tylko jednostki, w których stan zmienił się od określonego `DateTime` parametr ma zostać zwrócony.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Kolejne kroki
### <a name="parallel-node-tasks"></a>Zadania równoległych węzła
[Maksymalizowanie użycia zasobów obliczeniowych partii zadań Azure z węzła równoczesnych zadań](batch-parallel-node-tasks.md) inny artykuł dotyczy partii wydajność aplikacji. Niektóre rodzaje obciążeń mogą korzystać z wykonywanych zadań równoległych na większych — ale mniej — węzły obliczeniowe. Zapoznaj się z [przykładowy scenariusz](batch-parallel-node-tasks.md#example-scenario) w artykule, aby uzyskać szczegółowe informacje o takiej sytuacji.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job