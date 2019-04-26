---
title: Zapytanie dla zdarzenia klastrowania w klastrach usługi Azure Service Fabric przy użyciu interfejsów API bazy danych EventStore | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Service Fabric bazy danych EventStore interfejsów API usługi Azure do wykonywania zapytań dla zdarzenia platformy
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: facbcd6def7451ca83bdf00fe9b7c7cac2c74945
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392878"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Wykonanie kwerendy interfejsów API bazy danych EventStore zdarzenia klastra

W tym artykule opisano sposób tworzenia zapytań względem bazy danych EventStore interfejsów API, które są dostępne w usłudze Service Fabric w wersji 6.2 i nowsze wersje — Jeśli chcesz dowiedzieć się więcej o usłudze bazy danych EventStore, zobacz [Omówienie usługi bazy danych EventStore](service-fabric-diagnostics-eventstore.md). Obecnie usługa bazy danych EventStore dostęp tylko do danych w ciągu ostatnich 7 dni (oparte na zasady przechowywania danych diagnostycznych z klastra).

>[!NOTE]
>Interfejsy API bazy danych EventStore są ogólnie dostępne począwszy od usługi Service Fabric w wersji 6.4 tylko klastrów Windows działających na platformie Azure.

Są dostępne interfejsy API bazy danych EventStore bezpośrednio za pośrednictwem punktu końcowego REST lub programowo. Zależnie od zapytania istnieje kilka parametrów, które są wymagane do zbierania odpowiednie dane. Parametry te zwykle obejmują:
* `api-version`: wersja są przy użyciu interfejsów API bazy danych EventStore
* `StartTimeUtc`: Określa początek okresu interesuje Cię spojrzenie na
* `EndTimeUtc`: koniec okresu czasu

Oprócz tych parametrów dostępne są następujące parametry opcjonalne, takie jak:
* `timeout`: Przesłoń domyślną 60 drugi limitu czasu dla operacji żądania
* `eventstypesfilter`: umożliwia filtrowanie dla określonych typów zdarzeń
* `ExcludeAnalysisEvents`: zwraca zdarzeń "Analizy". Domyślnie zapytań w bazie danych EventStore zwróci ze zdarzeniami "Analiza", jeśli jest to możliwe. Zdarzenia analizy są bardziej rozbudowane zdarzeń kanał operacyjny, które zawiera dodatkowy kontekst lub informacji po przekroczeniu regularne zdarzeń usługi Service Fabric i zapewnia bardziej szczegółowe informacje.
* `SkipCorrelationLookup`: nie szukać potencjalnych zdarzenia skorelowane w klastrze. Domyślnie bazy danych EventStore podejmie próbę korelowanie zdarzeń w klastrze, a następnie połącz zdarzeń, gdy jest to możliwe. 

Każda jednostka w klastrze może być zapytań dotyczących zdarzenia. Możesz także zbadać zdarzenia dla wszystkich obiektów tego typu. Można na przykład wyszukać zdarzenia dla określonego węzła lub dla wszystkich węzłów w klastrze. Bieżący zestaw jednostek, dla których można wyszukiwać zdarzenia jest (jak zapytanie będzie wyglądać struktura):
* Klaster: `/EventsStore/Cluster/Events`
* Węzły: `/EventsStore/Nodes/Events`
* Węzeł: `/EventsStore/Nodes/<NodeName>/$/Events`
* Aplikacje: `/EventsStore/Applications/Events`
* Aplikacja: `/EventsStore/Applications/<AppName>/$/Events`
* Usługi: `/EventsStore/Services/Events`
* Usługa: `/EventsStore/Services/<ServiceName>/$/Events`
* Partycje: `/EventsStore/Partitions/Events`
* Partycja: `/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliki: `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Replika jest: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Podczas odwoływania się do aplikacji lub nazwę usługi, zapytanie nie musi zawierać "Service fabric: /" prefiks. Ponadto, jeśli Twoja aplikacja lub usługa nazwy "/" w nich, przełącz go do "~" Aby kontynuować pracę zapytania. Na przykład, jeśli aplikacja jest wyświetlany jako "Service fabric: / App1/FrontendApp", określonego zapytania aplikacji będzie postacią `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Ponadto raportów o kondycji usług już dziś pojawiają się w odpowiadającej jej aplikacji, dzięki czemu będzie szukać `DeployedServiceHealthReportCreated` zdarzenia dla jednostki prawo aplikacji. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Zapytanie bazy danych EventStore za pośrednictwem punktów końcowych interfejsu API REST

Można tworzyć zapytania bazy danych EventStore bezpośrednio za pośrednictwem punktu końcowego REST, wprowadzając `GET` żądania: `<your cluster address>/EventsStore/<entity>/Events/`.

Na przykład w celu wysłania zapytania o wszystkie zdarzenia klastra między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, żądania powinien wyglądać tak:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

To ustawienie może zwrócić albo, żadnych zdarzeń lub listę zdarzeń zwracane w formacie json:

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

W tym miejscu można widzimy, że między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, ten klaster pomyślnie ukończył jego najpierw uaktualnić po najpierw został miały, z `"CurrentClusterVersion": "0.0.0.0:"` do `"TargetClusterVersion": "6.2:1.0"`w `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Programowe zapytania bazy danych EventStore

Możesz także zbadać bazy danych EventStore programowo, za pośrednictwem [biblioteki klienckiej usługi Service Fabric](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Po utworzeniu, aby skonfigurować klienta usługi sieci szkieletowej, można wyszukiwać zdarzenia, uzyskując dostęp do bazy danych EventStore w następujący sposób: `sfhttpClient.EventStore.<request>`

Oto przykładowe żądanie, wszystkie zdarzenia dla klastra `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, za pośrednictwem `GetClusterEventListAsync` funkcji.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Oto inny przykład, który wysyła zapytanie o kondycji klastra i wszystkich zdarzeń węzła w września 2018 r i wyświetla je.

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>Przykładowe scenariusze i zapytań

Poniżej przedstawiono kilka przykładów, w jak wywołać interfejsy API REST Store zdarzeń do zrozumienia stanu klastra.

*Uaktualnianie klastra:*

Aby wyświetlić czas ostatniego klastra zostało pomyślnie lub podjęto próbę uaktualnienia w ostatnim tygodniu, można tworzyć zapytania interfejsów API do ostatnio wykonanych uaktualnień z klastrem przy użyciu zapytań dotyczących zdarzenia "ClusterUpgradeCompleted" w bazie danych EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemy z uaktualnieniem klastra:*

Podobnie jeśli wystąpiły problemy z uaktualnieniem klastra, wysłać zapytanie o wszystkie zdarzenia dla jednostki klastra. Zostaną wyświetlone różne zdarzenia, w tym rozpoczęcia uaktualnienia i każdy UD, dla którego uaktualnienia przedstawiana przez pomyślnie. Zobaczysz również zdarzenia do punktu, w którym wycofanie pracę i odpowiadających im zdarzenia dotyczące kondycji. Oto zapytanie, które są używane w tym: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zmiany stanu węzła:*

Aby wyświetlić Twój status węzła zmian w ciągu ostatnich kilku dni — gdy węzły błąd w górę lub w dół, lub zostały aktywowane lub dezaktywowane (przez platformę, usługa chaos lub z danych wejściowych użytkownika) — Użyj następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zdarzenia aplikacji:*

Można także śledzić najnowsze wdrożeń aplikacji i uaktualnień. Użyj następującego zapytania, aby wyświetlić wszystkie zdarzenia aplikacji w klastrze: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Historyczne kondycji dla aplikacji:*

Oprócz możliwości wyświetlania tylko zdarzenia cyklu życia aplikacji, można także wyświetlić dane historyczne dotyczące kondycji określonej aplikacji. Można to zrobić, określając nazwę aplikacji, dla której chcesz zbierać dane. Użyj tego zapytania, aby uzyskać wszystkie zdarzenia kondycji aplikacji: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. Jeśli chcesz uwzględnić zdarzenia dotyczące kondycji, które mogą wygasnąć (zniknął przekazywane czasu wygaśnięcia (TTL)) Dodaj `,ApplicationHealthReportExpired` na końcu zapytania, aby filtrować na dwa typy zdarzeń.

*Historyczne kondycji dla wszystkich usług w wartości "myApp":*

Obecnie zdarzenia raportów kondycji usługi są wyświetlane jako `DeployedServicePackageNewHealthReport` zdarzenia w obszarze odpowiednia jednostka aplikacji. Aby zobaczyć, sposobu usługi mają zostały działania dla komputera "App1", użyj następującego zapytania: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Ponowna konfiguracja partycji:*

Aby wyświetlić wszystkie przeniesień partycji, które wystąpiły w klastrze, wykonanie kwerendy `PartitionReconfigured` zdarzeń. Może to ułatwić ustalenie obciążenia uruchomione na który węzeł, w określonym czasie, podczas diagnozowania problemów w klastrze. Poniżej przedstawiono przykładowe zapytanie, tak, że: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Usługa chaos:*

Istnieje zdarzenie po Chaos usługa jest uruchomiona lub zatrzymana, oznacza to dostępne na poziomie klastra. Aby wyświetlić najnowsze użytkowania usługi chaosu, użyj następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

