---
title: Projektować wydajne zapytania dotyczące list - usługi Azure Batch | Dokumentacja firmy Microsoft
description: Zwiększyć wydajność, filtrując zapytań podczas żądania informacji na temat zasobów usługi Batch, takie jak pule, zadania, zadań i węzłów obliczeniowych.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ff3e95a603b8f9a188c7839578cd12287935de90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778258"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Wydajny sposób tworzyć zapytania w celu wyświetlenia listy zasobów usługi Batch

Tutaj dowiesz się, jak zwiększyć wydajność aplikacji usługi Azure Batch przez zmniejszenie ilości danych zwracanych przez usługę, kiedy wykonujesz zapytanie o zadań, zadań, węzłów obliczeniowych i innych zasobów przy użyciu [platformy .NET usługi Batch] [ api_net] biblioteki.

Prawie wszystkie aplikacje usługi Batch należy przeprowadzić pewien rodzaj monitorowania lub innych operacji, która wysyła zapytanie usługi Batch, często w regularnych odstępach czasu. Na przykład aby ustalić, czy istnieją żadnych zadań podrzędnych w kolejce pozostałych w ramach zadania, należy uzyskać dane na każde zadanie podrzędne w zadaniu. Aby określić stan węzłów w puli, możesz uzyskiwać danych w każdym węźle w puli. W tym artykule wyjaśniono, jak wykonywanie takich zapytań w najbardziej efektywny sposób.

> [!NOTE]
> Usługa Batch obsługuje specjalne interfejsu API dla typowych scenariuszy zliczanie zadań w ramach zadania i liczenia węzłów obliczeniowych w puli usługi Batch. Zamiast używania zapytanie dotyczące listy dla tych, można wywołać [pobieranie liczby zadań] [ rest_get_task_counts] i [listy puli węzeł zlicza] [ rest_get_node_counts] operacji. Te operacje są bardziej efektywne niż zapytanie dotyczące listy, ale zwracany bardziej ograniczone informacje. Zobacz [liczba zadań obliczeniowych węzły według stanu i](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Atrybut DetailLevel spełniają
W środowisku produkcyjnym aplikacji usługi Batch jednostki, takie jak zadania, zadania i węzłów obliczeniowych może liczbę w tysiącach. W przypadku żądania informacji na temat tych zasobów, potencjalnie dużą ilość danych musi "przechodzą podczas transmisji" z usługi Batch do aplikacji dla każdej kwerendy. Poprzez ograniczenie liczby elementów oraz typu informacji zwracanych przez zapytanie, możesz zwiększyć szybkość zapytań i w związku z tym wydajność aplikacji.

To [platformy .NET usługi Batch] [ api_net] list fragment kodu interfejsu API *co* zadań, która jest skojarzona z zadaniem, wraz z *wszystkich* właściwości każdego zadania:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Jednak znacznie bardziej efektywne zapytanie dotyczące listy, można wykonać, stosując "poziom szczegółowości" do zapytania. Można to zrobić, podając [ODATADetailLevel] [ odata] obiekt [JobOperations.ListTasks] [ net_list_tasks] metody. Ten fragment kodu zwraca tylko identyfikator, wiersz polecenia i właściwości informacji węzła obliczeń wykonanych zadań:

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

W tym przykładowym scenariuszu jeśli istnieją tysiące podzadań w ramach zadania wyniki z drugiego zapytania będą zwykle zwracane znacznie szybciej niż pierwszy. Więcej informacji o używaniu ODATADetailLevel wśród elementów za pomocą interfejsu API .NET usługi Batch jest dołączony [poniżej](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Zdecydowanie zalecamy możesz *zawsze* podać obiekt ODATADetailLevel do wywołania interfejsu API platformy .NET listy Aby zapewnić maksymalną wydajność i wydajność aplikacji. Określając poziom szczegółowości, może pomóc zmniejszyć czas reakcji usługi Batch, poprawić wykorzystanie sieci i zminimalizować użycie pamięci przez aplikacje klienckie.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrowanie, wybierz i rozwiń węzeł
[Platformy .NET usługi Batch] [ api_net] i [interfejs REST usługi Batch] [ api_rest] interfejsów API zapewniają możliwość zmniejszenia liczby elementów, które są zwracane w postaci listy także ilość informacji, która jest zwracana dla każdego. Możesz to zrobić, określając **filtru**, **wybierz**, i **rozwiń ciągi** podczas wykonywania zapytania dotyczące list.

### <a name="filter"></a>Filtr
Ciąg filtru jest wyrażeniem, która zmniejsza liczbę elementów, które są zwracane. Na przykład listę zadania uruchomione w zadaniu lub listy tylko węzły obliczeniowe, które są gotowe do uruchamiania zadań.

* Ciąg filtru składa się z co najmniej jednego wyrażenia z wyrażeniem, które składa się z nazwy właściwości, operator i wartość. Właściwości, które można określić są specyficzne dla każdego typu jednostki, które można zbadać, ponieważ operatory, które są obsługiwane dla każdej właściwości.
* Wiele wyrażeń mogą być połączone za pomocą operatorów logicznych `and` i `or`.
* W tym przykładzie ciąg listy filtrów z tylko zadania uruchamiania "renderowanie": `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Wybierz pozycję
Wybierz opcję ciąg ogranicza wartości właściwości, które są zwracane dla każdego elementu. Określ listę nazw właściwości, a dla elementów w wynikach zapytania są zwracane tylko wartości tych właściwości.

* Wybierz opcję ciąg składa się lista rozdzielonych przecinkami nazw właściwości. Można określić właściwości dla typu jednostki, które jest wykonywane zapytanie.
* Ten ciąg wybierz przykład określa, czy ma zostać zwrócone tylko dla trzech wartości właściwości, dla każdego zadania: `id, state, stateTransitionTime`.

### <a name="expand"></a>Rozwiń
Ciąg rozwijania zmniejsza liczbę wywołań interfejsu API, które są wymagane do uzyskania określonych informacji. Korzystając z ciągiem rozwiń więcej informacji na temat każdego elementu można uzyskać za pomocą jednego wywołania interfejsu API. Aby uzyskać te same informacje w jednym wywołaniu interfejsu API, zamiast pierwszego uzyskiwania listę jednostek, następnie żądanie informacji dla każdego elementu na liście, użyj ciągu Rozwiń. Mniej wywołania interfejsu API oznaczają lepszą wydajność.

* Podobnie jak wybierz ciąg, ciąg rozwijania Określa, czy niektórych danych znajduje się w wynikach kwerendy listy.
* Ciąg rozwijania jest obsługiwana tylko w sytuacji, gdy jest używany w listę zadań, harmonogramów zadań, zadania i puli. Obecnie obsługuje on tylko informacje statystyczne.
* Gdy wymagane są wszystkie właściwości i nie wybierz ciągu jest określony, ciąg rozwijania *musi* można użyć do uzyskania statystyk. Jeśli wybierz opcję ciąg jest używany do uzyskiwania podzbiór właściwości, następnie `stats` można określić w Wybierz parametry, a ciąg rozwijania nie muszą być określone.
* W tym przykładzie rozwiń ciąg Określa, czy dla każdego elementu na liście ma zostać zwrócone informacje statystyczne: `stats`.

> [!NOTE]
> Podczas tworzenia dowolnego typu ciąg zapytania trzy (filtrowanie, wybierz i rozwiń węzeł), należy upewnić się, że nazwy i właściwości przypadku odpowiadają elementom element interfejsu API REST. Na przykład podczas pracy z platformą .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask) klasy, należy określić **stanu** zamiast **stanu**, nawet jeśli właściwość .NET jest [ CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask.state). Zobacz poniższe tabele zawierają mapowania właściwości między .NET i interfejsów API REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Reguły filtrowania, wybierz, a następnie rozwiń ciągów
* Nazwy właściwości w filtrze, wybierz i rozwiń ciągi powinien pojawić się, jak w [interfejs REST usługi Batch] [ api_rest] interfejsu API — nawet wtedy, gdy używasz [platformy .NET usługi Batch] [ api_net]lub jednego z innych zestawów SDK usługi Batch.
* Wszystkie nazwy właściwości jest rozróżniana wielkość liter, ale wartości właściwości jest rozróżniana wielkość liter.
* Data/Godzina ciągi mogą być jednym z dwóch formatów i musi być poprzedzony znakiem `DateTime`.
  
  * Przykładowy format W3C DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Przykład formacie RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Wartość logiczna, ciągi są `true` lub `false`.
* Jeśli określono nieprawidłowe właściwości lub operatora `400 (Bad Request)` spowoduje wystąpienie błędu.

## <a name="efficient-querying-in-batch-net"></a>Wydajne zapytania na platformie .NET usługi Batch
W ramach [platformy .NET usługi Batch] [ api_net] interfejsu API, [ODATADetailLevel] [ odata] klasa jest używana dla podanie filtr, wybierz i rozwiń ciągów, aby wyświetlić listę operacje. Klasa ODataDetailLevel ma trzy właściwości ciąg publicznych, które mogą być określony w konstruktorze lub ustawić bezpośrednio na obiekcie. Możesz następnie przekazać obiekt ODataDetailLevel jako parametr do różnych operacji listy takich jak [ListPools][net_list_pools], [ListJobs][net_list_jobs], i [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: Ogranicz liczbę elementów, które są zwracane.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: Określ wartości właściwości, które są zwracane z każdym elementem.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: Pobieranie danych dla wszystkich elementów w jednym wywołaniu interfejsu API, a nie oddzielnych wywołania dla każdego elementu.

Poniższy fragment kodu używa interfejsu API .NET usługi Batch do wydajnego przesyłania zapytań dotyczących usługi Batch dla statystyk określony zbiór pul. W tym scenariuszu użytkownik usługi Batch ma zestawów testowych i produkcyjnych. Test puli identyfikatorów mają prefiks "test", a puli produkcji identyfikatorów mają prefiks "prod". Ten fragment kodu *myBatchClient* jest prawidłowo zainicjowany wystąpieniem [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient#microsoft_azure_batch_batchclient) klasy.

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
> Wystąpienie [ODATADetailLevel] [ odata] skonfigurowany z wybranymi, rozwiń klauzule można również zostaną przekazane do odpowiedniej metody Get, takich jak [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), Aby ograniczyć ilość danych, która jest zwracana.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST do mapowania interfejsu API platformy .NET
Nazwy właściwości w filtrze, wybierz i rozwiń ciągi *musi* odzwierciedlają ich odpowiedników interfejsu API REST, zarówno nazwę i przypadek. Poniższe tabele zawierają mapowania między odpowiedników .NET i interfejsu API REST.

### <a name="mappings-for-filter-strings"></a>Mapowania w ciągach filtru
* **Metody listy .NET**: Każda z metod interfejsu API platformy .NET w tej kolumnie akceptuje [ODATADetailLevel] [ odata] obiektu jako parametr.
* **Żądania listy REST**: Każda strona interfejsu API REST, połączone z tej kolumny zawiera tabelę, która określa właściwości i operacje, które są dozwolone w *filtru* ciągów. Użyjesz tych nazw właściwości i operacje podczas konstruowania [ODATADetailLevel.FilterClause] [ odata_filter] ciągu.

| Metody listy .NET | Żądania listy REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Wyświetlanie listy certyfikatów w ramach konta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Lista plików skojarzonych z zadaniem][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Wyświetl stan zadania przygotowania i zwolnienia zadań dla zadania][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Wyświetlać listy zadań na konto][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista plików w węźle][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Lista zadań skojarzonych z zadaniem][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista harmonogramy zadań na koncie usługi][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista zadań skojarzonych z harmonogramem zadań][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Lista węzłów obliczeniowych w puli][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Lista pul w ramach konta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapowania wybierz ciągów
* **Typy .NET batch**: Typy interfejsu API .NET usługi Batch.
* **Jednostki interfejsu API REST**: Każda Strona ta kolumna zawiera co najmniej jedną tabelę, w których przedstawiono nazwy właściwości interfejsu API REST dla typu. Nazwy te właściwości są używane podczas konstruowania *wybierz* ciągów. Podczas konstruowania użyje tych samych nazw właściwości [ODATADetailLevel.SelectClause] [ odata_select] ciągu.

| Typy .NET usługi Batch | Jednostki interfejsu API REST |
| --- | --- |
| [Certyfikat][net_cert] |[Uzyskaj informacje o certyfikacie][rest_get_cert] |
| [CloudJob][net_job] |[Uzyskaj informacje o zadaniu][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Uzyskaj informacje o harmonogramie zadań][rest_get_schedule] |
| [ComputeNode][net_node] |[Uzyskać informacje na temat węzła][rest_get_node] |
| [CloudPool][net_pool] |[Pobierz informacje o puli][rest_get_pool] |
| [CloudTask][net_task] |[Uzyskaj informacje o zadaniu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Przykład: konstruowania ciągu filtru
Podczas konstruowania ciągu filtru dla [ODATADetailLevel.FilterClause][odata_filter], zapoznaj się z powyższej tabeli w obszarze "Mapowania w ciągach filtru" do strony dokumentacji Znajdź interfejsu API REST, który odpowiada Operacja wygenerowania listy, którą chcesz wykonać. W pierwszej tabeli multirow na tej stronie znajdziesz filtrowania właściwości i ich operatory obsługiwane. Jeśli chcesz pobrać wszystkie zadania, którego kod zakończenia: wartość różną od zera, na przykład ten wiersz na [listy zadań skojarzone z zadaniem] [ rest_list_tasks] Określa ciąg odpowiednie właściwości i operatory dopuszczalny rozmiar:

| Właściwość | Dozwolone operacje | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

W związku z tym będzie ciąg filtru do wyświetlania listy wszystkich zadań z kodem zakończenia różny od zera:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Przykład: skonstruować wybierz ciąg
Do konstruowania [ODATADetailLevel.SelectClause][odata_select], zapoznaj się z powyższej tabeli, w obszarze "Mapowań dla ciągów wybierz opcję" i przejdź do strony interfejsu API REST, który odpowiada typowi obiektu, który chcesz wyświetlić. W pierwszej tabeli multirow na tej stronie znajdziesz można wybrać właściwości i ich operatory obsługiwane. Jeśli chcesz pobrać tylko z Identyfikatorem i wiersza polecenia dla każdego zadania na liście, na przykład znajdziesz te wiersze w tabeli zastosowanie na [uzyskać informacje o zadaniu][rest_get_task]:

| Właściwość | Type | Uwagi |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Wybierz opcję ciąg, w tym z Identyfikatorem i wiersza polecenia za pomocą każdego zadania wymienione przestaną być:

`id, commandLine`

## <a name="code-samples"></a>Przykłady kodu
### <a name="efficient-list-queries-code-sample"></a>Wydajne zapytania dotyczące list przykładowego kodu
Zapoznaj się z [EfficientListQueries] [ efficient_query_sample] przykładowy projekt w witrynie GitHub, aby zobaczyć, jak wydajnych zapytań listy może wpłynąć na wydajność w aplikacji. Ta aplikacja konsolowa C# utworzy i zapisze dużą liczbę zadań do zadania. Następnie należy go wielu wywołań do [JobOperations.ListTasks] [ net_list_tasks] metody i przekazuje [ODATADetailLevel] [ odata] obiekty, które mają skonfigurowano wartości różnych właściwości różnią się ilość danych, które mają zostać zwrócone. Generuje dane wyjściowe podobne do następujących:

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

Jak pokazano na czas, można znacznie zmniejszyć czas odpowiedzi na zapytania, ograniczając właściwości i liczbę elementów, które są zwracane. Możesz znaleźć tego i innych przykładowych projektów w [azure-batch-samples] [ github_samples] repozytorium w witrynie GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics biblioteki i przykładowy kod
Oprócz powyższym przykładowym kodzie EfficientListQueries można znaleźć [BatchMetrics] [ batch_metrics] projektu w [azure-batch-samples] [ github_samples]Repozytorium GitHub. Przykładowy projekt BatchMetrics pokazuje, jak skutecznie Monitoruj postęp zadania usługi Azure Batch przy użyciu interfejsu API usługi Batch.

[BatchMetrics] [ batch_metrics] przykład obejmuje projektu biblioteki klas .NET, które można zastosować do własnych projektów i prosty program wiersza polecenia do wykonywania i pokazują użycie biblioteki.

Przykładowa aplikacja w projekcie pokazuje następujące operacje:

1. Wybieranie określonych atrybutów w celu pobrania właściwości, których potrzebujesz
2. Filtrowanie według czasów przejścia stanu w celu Pobierz tylko zmiany od ostatniej kwerendy

Na przykład poniższa metoda pojawia się w bibliotece BatchMetrics. Zwraca ODATADetailLevel, która określa, że tylko `id` i `state` właściwości mają być uzyskiwane dla jednostek, które są badane. Określa ona także, że tylko jednostki, w których stan zmienił się od określonego `DateTime` parametru powinna zostać zwrócona.

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
### <a name="parallel-node-tasks"></a>Zadania w węzłach równoległe
[Zmaksymalizuj użycie zasobów obliczeniowych usługi Azure Batch przy użyciu równoczesne zadania węzła](batch-parallel-node-tasks.md) inny artykuł dotyczy wydajności aplikacji usługi Batch. Niektóre rodzaje obciążeń mogą korzystać z wykonywania zadań równoległych w większych —, ale mniej — węzłów obliczeniowych. Zapoznaj się z [przykładowy scenariusz](batch-parallel-node-tasks.md#example-scenario) w artykule, aby uzyskać szczegółowe informacje na temat takiej sytuacji.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
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

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
