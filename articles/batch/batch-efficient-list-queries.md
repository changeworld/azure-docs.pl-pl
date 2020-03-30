---
title: Projektowanie wydajnych zapytań o listę — usługa Azure Batch | Dokumenty firmy Microsoft
description: Zwiększ wydajność, filtrując zapytania podczas żądania informacji o zasobach wsadowych, takich jak pule, zadania, zadania i węzły obliczeniowe.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022243"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Efektywne tworzenie kwerend w celu efektywnego wyświetlenia zasobów usługi Batch

W tym miejscu dowiesz się, jak zwiększyć wydajność aplikacji usługi Azure Batch, zmniejszając ilość danych zwracanych przez usługę podczas wykonywania zapytań o zadania, zadania, węzły obliczeniowe i inne zasoby za pomocą biblioteki [Batch .NET.][api_net]

Prawie wszystkie aplikacje batch należy wykonać pewnego rodzaju monitorowania lub innej operacji, która wysyła zapytania usługi Batch, często w regularnych odstępach czasu. Na przykład, aby ustalić, czy w zadaniu pozostają jakieś zadania w kolejce, należy uzyskać dane dotyczące każdego zadania w zadaniu. Aby określić stan węzłów w puli, należy uzyskać dane na każdy węzeł w puli. W tym artykule wyjaśniono, jak wykonać takie zapytania w najbardziej efektywny sposób.

> [!NOTE]
> Usługa Batch zapewnia specjalną obsługę interfejsu API dla typowych scenariuszy zliczania zadań w zadaniu i zliczania węzłów obliczeniowych w puli usługi Batch. Zamiast używać kwerendy listy dla tych, można wywołać [Get Liczniki zadań][rest_get_task_counts] i [Listy puli liczy węzłów.][rest_get_node_counts] Te operacje są bardziej wydajne niż kwerendy listy, ale zwracają bardziej ograniczone informacje. Zobacz [Zliczanie zadań i węzły obliczeniowe według stanu](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Poznaj szczegółylevel
W aplikacji partii produkcyjnej jednostki, takie jak zadania, zadania i węzły obliczeniowe, mogą liczyć w tysiącach. Gdy żądasz informacji o tych zasobach, potencjalnie duża ilość danych musi "krzyżować się" z usługi Batch do aplikacji w każdej kwerendzie. Ograniczając liczbę elementów i typ informacji, który jest zwracany przez kwerendę, można zwiększyć szybkość zapytań, a tym samym wydajność aplikacji.

Ten fragment kodu interfejsu API [usługi Batch .NET][api_net] zawiera listę *wszystkich* zadań skojarzonych z zadaniem wraz *ze wszystkimi* właściwościami każdego zadania:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Można jednak wykonać znacznie bardziej wydajne zapytanie listy, stosując "poziom szczegółowości" do kwerendy. Można to zrobić, podając [ODATADetailLevel][odata] obiektu [do JobOperations.ListTasks][net_list_tasks] metody. Ten fragment kodu zwraca tylko właściwości informacji o identyfikatorze, wierszu polecenia i węźle obliczeniowym ukończonych zadań:

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

W tym przykładzie scenariusza, jeśli istnieją tysiące zadań w zadaniu, wyniki z drugiej kwerendy zazwyczaj będą zwracane znacznie szybciej niż pierwszy. Więcej informacji na temat używania ODATADetailLevel podczas listy elementów z interfejsem API usługi Batch .NET znajduje się [poniżej](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Zdecydowanie zaleca *się,* aby zawsze podawać ODATADetailLevel obiektu do wywołań listy interfejsu API platformy .NET, aby zapewnić maksymalną wydajność i wydajność aplikacji. Określając poziom szczegółowości, można zmniejszyć czas odpowiedzi usługi Batch, poprawić wykorzystanie sieci i zminimalizować użycie pamięci przez aplikacje klienckie.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrowanie, wybieranie i rozwijanie
Interfejsy API [batch .NET][api_net] i [Batch REST][api_rest] umożliwiają zmniejszenie zarówno liczby elementów zwracanych na liście, jak i ilości informacji zwracanych dla każdego z nich. Można to zrobić, określając **filtr**, **select**i **rozwijaj ciągi** podczas wykonywania kwerend listy.

### <a name="filter"></a>Filtr
Ciąg filtru jest wyrażeniem, które zmniejsza liczbę elementów, które są zwracane. Na przykład lista tylko uruchomione zadania dla zadania lub listy tylko węzłów obliczeniowych, które są gotowe do uruchamiania zadań.

* Ciąg filtru składa się z jednego lub więcej wyrażeń, z wyrażeniem, które składa się z nazwy właściwości, operatora i wartości. Właściwości, które można określić są specyficzne dla każdego typu jednostki, które są kwerendy, podobnie jak operatory, które są obsługiwane dla każdej właściwości.
* Wiele wyrażeń można łączyć `and` przy `or`użyciu operatorów logicznych i .
* W tym przykładzie ciąg filtru wyświetla `(state eq 'running') and startswith(id, 'renderTask')`tylko uruchomione zadania "renderowania": .

### <a name="select"></a>Wybierz pozycję
Ciąg wyboru ogranicza wartości właściwości, które są zwracane dla każdego elementu. Należy określić listę nazw właściwości i tylko te wartości właściwości są zwracane dla elementów w wynikach kwerendy.

* Ciąg wyboru składa się z listy nazw właściwości oddzielonych przecinkami. Można określić dowolną z właściwości typu jednostki, do której się zapytania należy wyceńować.
* W tym przykładzie wybierz ciąg określa, że dla `id, state, stateTransitionTime`każdego zadania powinny być zwracane tylko trzy wartości właściwości: .

### <a name="expand"></a>Rozwiń
Ciąg rozwijania zmniejsza liczbę wywołań interfejsu API, które są wymagane do uzyskania pewnych informacji. Korzystając z ciągu rozwijania, więcej informacji o każdym elemencie można uzyskać za pomocą jednego wywołania interfejsu API. Zamiast najpierw uzyskać listę jednostek, a następnie żądając informacji dla każdego elementu na liście, należy użyć ciągu rozwijania, aby uzyskać te same informacje w jednym wywołaniu interfejsu API. Mniej wywołań interfejsu API oznacza lepszą wydajność.

* Podobnie jak w przypadku ciągu wyboru, ciąg rozwijania określa, czy niektóre dane są uwzględniane w wynikach kwerendy listy.
* Ciąg rozwijania jest obsługiwany tylko wtedy, gdy jest używany w lista zadań, harmonogramów zadań, zadań i pul. Obecnie obsługuje tylko informacje statystyczne.
* Gdy wszystkie właściwości są wymagane i nie wybierz ciąg jest określony, ciąg *rozwijania musi* służyć do uzyskania informacji statystycznych. Jeśli ciąg wyboru jest używany do uzyskania podzbioru właściwości, a następnie `stats` można określić w ciągu wyboru i rozwijać ciąg nie musi być określony.
* W tym przykładzie rozwijania ciąg znaków określa, że informacje `stats`statystyczne powinny być zwracane dla każdego elementu na liście: .

> [!NOTE]
> Podczas konstruowania dowolnego z trzech typów ciągów kwerendy (filtr, wybierz i rozwiń), należy upewnić się, że nazwy właściwości i sprawy są zgodne z ich odpowiednikami elementu interfejsu API REST. Na przykład podczas pracy z .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) klasy, należy określić **stan** zamiast **Stanu**, mimo że .NET właściwość [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Zobacz tabele poniżej mapowania właściwości między .NET i REST INTERFEJSÓW API.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Reguły filtrowania, wybierania i rozwijania ciągów
* Nazwy właściwości w filtrze, zaznaczeniu i rozwinięciu ciągów powinny być wyświetlane tak samo, jak w interfejsie API [BATCH REST][api_rest] — nawet w przypadku użycia [usługi Batch .NET][api_net] lub jednego z innych skompuerów sdk partii.
* We wszystkich nazwach właściwości rozróżniana jest wielkość liter, ale w wartościach właściwości nie ma uwzględniania wielkości liter.
* Ciągi daty/godziny mogą być jednym z dwóch formatów i muszą być poprzedzone `DateTime`.
  
  * Przykład formatu W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Przykład formatu RFC 1123:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Ciągi logiczne są `true` `false`albo lub .
* Jeśli określono nieprawidłową właściwość `400 (Bad Request)` lub operator, spowoduje błąd.

## <a name="efficient-querying-in-batch-net"></a>Wydajne wykonywanie zapytań w uziemieniu wsadowym .NET
W interfejsie API [batch .NET][api_net] klasa [ODATADetailLevel][odata] służy do dostarczania filtru, wybierania i rozwijania ciągów do operacji listy. Klasa ODataDetailLevel ma trzy właściwości publicznego ciągu, które można określić w konstruktorze lub ustawić bezpośrednio na obiekcie. Następnie należy przekazać obiekt ODataDetailLevel jako parametr do różnych operacji listy, takich jak [ListPools][net_list_pools], [ListJobs][net_list_jobs]i [ListTasks][net_list_tasks].

* [ODATADetailPoziomuj][odata]. [FilterClause][odata_filter]: Ogranicz liczbę zwracanych elementów.
* [ODATADetailPoziomuj][odata]. [SelectClause][odata_select]: Określ, które wartości właściwości są zwracane z każdym towarem.
* [ODATADetailPoziomuj][odata]. [ExpandClause:][odata_expand]Pobieranie danych dla wszystkich elementów w jednym wywołaniu interfejsu API zamiast oddzielnych wywołań dla każdego elementu.

Poniższy fragment kodu używa interfejsu API usługi Batch .NET do skutecznego wykonywania zapytań w usłudze Batch w celu uzyskania statystyk określonego zestawu pul. W tym scenariuszu użytkownik usługi Batch ma zarówno pulę testów, jak i produkcji. Identyfikatory puli testów są poprzedzone "test", a identyfikatory puli produkcji są poprzedzone "prod". We urywek *myBatchClient* jest poprawnie zainicjowane wystąpienie [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) klasy.

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
> Wystąpienie [ODATADetailLevel,][odata] który jest skonfigurowany z Select i Expand klauzule mogą być również przekazywane do odpowiednich Metod Pobierz, takich jak [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), aby ograniczyć ilość danych, które są zwracane.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Mapowania interfejsu API REST wsadowe do sieci .NET
Nazwy właściwości w ciągach filtrowania, wybierania i rozwijania *muszą odzwierciedlać* ich odpowiedniki interfejsu API REST, zarówno w nazwie, jak i w przypadku. Poniższe tabele zawierają mapowania między odpowiednikami interfejsu API .NET i REST.

### <a name="mappings-for-filter-strings"></a>Mapowania ciągów filtrów
* **.NET list metody:** Każda z metod interfejsu API platformy .NET w tej kolumnie akceptuje [ODATADetailLevel][odata] obiektu jako parametr.
* **ŻĄDANIA LISTY REST:** Każda strona interfejsu API REST, do którą jest połączony w tej kolumnie, zawiera tabelę określającą właściwości i operacje dozwolone w ciągach *filtrów.* Użyjesz tych nazw właściwości i operacji podczas konstruowania ciągu [ODATADetailLevel.FilterClause.][odata_filter]

| Metody listy .NET | Żądania listy REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Wyświetlanie certyfikatów na koncie][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Wyświetlanie listy plików skojarzonych z zadaniem][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Wyświetlanie stanu zadań związanych z przygotowaniem zadania i zwolnieniem z pracy dla zadania][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Wyświetlanie listy zadań na koncie][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Wyświetlanie listy plików w węźle][rest_list_nodefiles] |
| [JobOperations.ListZadania][net_list_tasks] |[Wyświetlanie listy zadań skojarzonych z zadaniem][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Wyświetlanie harmonogramów zadań na koncie][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Wyświetlanie listy zadań skojarzonych z harmonogramem zadań][rest_list_schedule_jobs] |
| [Działanie w pliku PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Wyświetlanie listy węzłów obliczeniowych w puli][rest_list_compute_nodes] |
| [Puli operacji.lista operacji][net_list_pools] |[Wyświetlanie puli na koncie][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapowania dla ciągów wyboru
* **Typy wsadowe .NET:** Typy interfejsu API usługi Batch .NET.
* **Rest jednostek interfejsu API:** Każda strona w tej kolumnie zawiera jedną lub więcej tabel, które zawierają nazwy właściwości interfejsu API REST dla tego typu. Te nazwy właściwości są używane podczas konstruowania *wybierz* ciągi. Użyjesz tych samych nazw właściwości podczas konstruowania [ciągu ODATADetailLevel.SelectClause.][odata_select]

| Typy wsadowe .NET | Jednostki interfejsu API REST |
| --- | --- |
| [Certyfikat][net_cert] |[Uzyskaj informacje o certyfikacie][rest_get_cert] |
| [Chmura Chów][net_job] |[Uzyskaj informacje o zadaniu][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Uzyskaj informacje o harmonogramie zadań][rest_get_schedule] |
| [ComputeNode (Nie ma danych)][net_node] |[Uzyskaj informacje o węźle][rest_get_node] |
| [CloudPool (Basen w chmurze)][net_pool] |[Uzyskaj informacje o puli][rest_get_pool] |
| [CloudTask][net_task] |[Uzyskaj informacje o zadaniu][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Przykład: konstruowanie ciągu filtru
Podczas konstruowania ciągu filtru dla [ODATADetailLevel.FilterClause,][odata_filter]zapoznaj się z powyższą tabelą w obszarze "Mapowania ciągów filtrów", aby znaleźć stronę dokumentacji interfejsu API REST, która odpowiada operacji listy, którą chcesz wykonać. Właściwości filtrowania i ich obsługiwane operatory można znaleźć w pierwszej tabeli wielorowej na tej stronie. Jeśli chcesz pobrać wszystkie zadania, których kod zakończenia był niezerowy, na przykład ten wiersz na [liście zadań skojarzonych z zadaniem][rest_list_tasks] określa odpowiedni ciąg właściwości i dopuszczalne operatory:

| Właściwość | Dozwolone operacje | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

W związku z tym ciąg filtru do listy wszystkich zadań z kodem zakończenia niezerowego będzie:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Przykład: konstruowanie ciągu wyboru
Aby skonstruować [ODATADetailLevel.SelectClause][odata_select], zapoznaj się z powyższą tabelą w sekcji "Mapowania dla wybranych ciągów" i przejdź do strony interfejsu API REST, która odpowiada typowi jednostki, którą wystawiasz na aukcji. Właściwości do wyboru i ich obsługiwane operatory można znaleźć w pierwszej tabeli wielorowej na tej stronie. Jeśli chcesz pobrać tylko identyfikator i wiersz polecenia dla każdego zadania na liście, na przykład, znajdziesz te wiersze w odpowiedniej tabeli na [Pobierz informacje o zadaniu:][rest_get_task]

| Właściwość | Typ | Uwagi |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Ciąg wyboru dla uwzględnienia tylko identyfikatora i wiersza polecenia z każdym wymienionym zadaniem będzie wtedy:

`id, commandLine`

## <a name="code-samples"></a>Przykłady kodu
### <a name="efficient-list-queries-code-sample"></a>Przykładowy kod kwerend z listy Efficient list
Zapoznaj się [efficientlistqueries][efficient_query_sample] przykładowy projekt w usłudze GitHub, aby zobaczyć, jak wydajne kwerendy listy może mieć wpływ na wydajność w aplikacji. Ta aplikacja konsoli języka C# tworzy i dodaje dużą liczbę zadań do zadania. Następnie sprawia, że wiele wywołań [JobOperations.ListTasks][net_list_tasks] metody i przekazuje [ODATADetailLevel][odata] obiektów, które są skonfigurowane z różnymi wartościami właściwości, aby zmienić ilość danych do zwrotu. Produkuje wyjście podobne do następujących:

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

Jak pokazano w czasach, które upłynęło, można znacznie zmniejszyć czas odpowiedzi kwerendy, ograniczając właściwości i liczbę elementów, które są zwracane. Można znaleźć to i inne przykładowe projekty w repozytorium [przykładów azure w][github_samples] usłudze GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Przykład biblioteki i kodu BatchMetrics
Oprócz efficientlistqueries przykładowy kod powyżej, można znaleźć [BatchMetrics][batch_metrics] projektu w repozytorium GitHub [przykłady partii azure.][github_samples] Przykładowy projekt BatchMetrics pokazuje, jak skutecznie monitorować postęp zadania usługi Azure Batch przy użyciu interfejsu API usługi Batch.

[BatchMetrics][batch_metrics] przykład zawiera .NET projektu biblioteki klas, które można włączyć do własnych projektów i prosty program wiersza polecenia do wykonywania i demonstrować korzystanie z biblioteki.

Przykładowa aplikacja w ramach projektu pokazuje następujące operacje:

1. Wybieranie określonych atrybutów w celu pobrania tylko potrzebnych właściwości
2. Filtrowanie czasów przejścia stanu w celu pobrania tylko zmian od czasu ostatniej kwerendy

Na przykład w bibliotece BatchMetrics pojawia się następująca metoda. Zwraca ODATADetailLevel, który określa, `id` że `state` tylko właściwości i powinny być uzyskane dla jednostek, które są wyszukiwane. Określa również, że powinny być zwracane tylko `DateTime` jednostki, których stan uległ zmianie od czasu, gdy określony parametr powinien zostać zwrócony.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Następne kroki
### <a name="parallel-node-tasks"></a>Zadania węzła równoległego
[Maksymalizuj użycie zasobów obliczeniowych usługi Azure Batch za pomocą równoczesnych zadań węzłów](batch-parallel-node-tasks.md) to kolejny artykuł związany z wydajnością aplikacji batch. Niektóre typy obciążeń mogą korzystać z wykonywania równoległych zadań na większych — ale mniej — węzłów obliczeniowych. Zapoznaj się z [przykładowym scenariuszem](batch-parallel-node-tasks.md#example-scenario) w artykule, aby uzyskać szczegółowe informacje na temat takiego scenariusza.


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
