---
title: Kwerenda o zdarzenia klastra przy użyciu interfejsów API eventstore
description: Dowiedz się, jak używać interfejsów API usługi Azure Service Fabric EventStore do wykonywania zapytań o zdarzenia platformy
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 48350caef6bdaafda9aff7ac776d67b314aeaf8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614404"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Kwerendy eventstore interfejsów API dla zdarzeń klastra

W tym artykule opisano, jak zbadać interfejsy API EventStore, które są dostępne w sieci szkieletowej usług w wersji 6.2 i nowszej — jeśli chcesz dowiedzieć się więcej o usłudze EventStore, zobacz [omówienie usługi EventStore](service-fabric-diagnostics-eventstore.md). Obecnie usługa EventStore może uzyskiwać dostęp tylko do danych z ostatnich 7 dni (jest to oparte na zasadach przechowywania danych diagnostycznych klastra).

>[!NOTE]
>Interfejsy API eventstore są ga w wersji szkieletowej usług w wersji 6.4 tylko dla klastrów systemu Windows uruchomionych na platformie Azure.

Interfejsy API EventStore są dostępne bezpośrednio za pośrednictwem punktu końcowego REST lub programowo. W zależności od zapytania istnieje kilka parametrów, które są wymagane do zbierania odpowiednich danych. Parametry te zazwyczaj obejmują:
* `api-version`: wersja interfejsów API eventstore, których używasz
* `StartTimeUtc`: określa początek okresu, który chcesz
* `EndTimeUtc`: koniec okresu

Oprócz tych parametrów dostępne są również parametry opcjonalne, takie jak:
* `timeout`: zastąpienie domyślnego limitu czasu 60 sekund dla wykonania operacji żądania
* `eventstypesfilter`: daje możliwość filtrowania dla określonych typów zdarzeń
* `ExcludeAnalysisEvents`: nie zwracaj zdarzeń "Analiza". Domyślnie kwerendy EventStore będą zwracać ze zdarzeniami "analizy", jeśli to możliwe. Zdarzenia analizy są bogatsze zdarzenia kanału operacyjnego, które zawierają dodatkowe kontekst lub informacje poza zwykłym zdarzeniem sieci szkieletowej usług i zapewniają więcej głębi.
* `SkipCorrelationLookup`: nie należy szukać potencjalnych skorelowanych zdarzeń w klastrze. Domyślnie EventStore będzie próbował skorelować zdarzenia w klastrze i połączyć zdarzenia ze sobą, gdy jest to możliwe. 

Każda jednostka w klastrze może być kwerendy dla zdarzeń. Można również kwerendy dla zdarzeń dla wszystkich jednostek typu. Na przykład można wysyłać kwerendy w poszukiwaniu zdarzeń dla określonego węzła lub dla wszystkich węzłów w klastrze. Bieżący zestaw jednostek, dla których można wysyłać zapytania dotyczące zdarzeń, to (z zachowaniem struktury kwerendy):
* Klastra:`/EventsStore/Cluster/Events`
* Węzłów:`/EventsStore/Nodes/Events`
* Węzła:`/EventsStore/Nodes/<NodeName>/$/Events`
* Aplikacji:`/EventsStore/Applications/Events`
* Aplikacji:`/EventsStore/Applications/<AppName>/$/Events`
* Usług:`/EventsStore/Services/Events`
* Usługi:`/EventsStore/Services/<ServiceName>/$/Events`
* Partycji:`/EventsStore/Partitions/Events`
* Partycji:`/EventsStore/Partitions/<PartitionID>/$/Events`
* Repliki:`/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* Repliki:`/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Podczas odwoływania się do nazwy aplikacji lub usługi kwerenda nie musi zawierać "fabric:/" Prefiks. Ponadto jeśli nazwy aplikacji lub usługi mają "/" w nich, przełącz go na "~", aby zachować działanie kwerendy. Na przykład jeśli aplikacja jest wyświetlana jako "fabric:/App1/FrontendApp", zapytania specyficzne `/EventsStore/Applications/App1~FrontendApp/$/Events`dla aplikacji będą miały strukturę .
>Ponadto raporty kondycji dla usług są obecnie wyświetlane w odpowiedniej `DeployedServiceHealthReportCreated` aplikacji, więc należy zbadać zdarzenia dla odpowiedniej jednostki aplikacji. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Zapytanie do eventstore za pośrednictwem punktów końcowych interfejsu API REST

EventStore można wysyłać zapytania bezpośrednio za pośrednictwem `GET` punktu końcowego REST, wysyłając żądania do: `<your cluster address>/EventsStore/<entity>/Events/`.

Na przykład w celu wykonywania zapytań `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`o wszystkie zdarzenia klastra między i , żądanie będzie wyglądać następująco:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

To może albo zwrócić żadnych zdarzeń lub listy zdarzeń zwróconych w json:

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

Tutaj widzimy, `2018-04-03T18:00:00Z` że `2018-04-04T18:00:00Z`między i , ten klaster pomyślnie zakończył `"CurrentClusterVersion": "0.0.0.0:"` swoją `"TargetClusterVersion": "6.2:1.0"`pierwszą aktualizację, kiedy po raz pierwszy wstał, od do , w `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Programowo wysyłaj zapytania do magazynu eventstore

Można również wysyłać zapytania do EventStore programowo za pośrednictwem [biblioteki klienta sieci szkieletowej usług](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Po skonfigurowaniu klienta sieci szkieletowej usług można wyszukiwać zdarzenia, uzyskując dostęp do EventStore w ten sposób:`sfhttpClient.EventStore.<request>`

Oto przykładowe żądanie dla wszystkich `2018-04-03T18:00:00Z` `2018-04-04T18:00:00Z`zdarzeń klastra między i , za pośrednictwem `GetClusterEventListAsync` funkcji.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Oto kolejny przykład, że kwerendy dotyczące kondycji klastra i wszystkich zdarzeń węzła we wrześniu 2018 r. i drukuje je.

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

## <a name="sample-scenarios-and-queries"></a>Przykładowe scenariusze i zapytania

Oto kilka przykładów, w jaki sposób można wywołać interfejsy API REST magazynu zdarzeń, aby zrozumieć stan klastra.

*Uaktualnienia klastra:*

Aby zobaczyć, kiedy klaster został pomyślnie lub próbowano uaktualnić w zeszłym tygodniu, można zbadać interfejsy API dla ostatnio zakończonych uaktualnień do klastra, przeprowadzając kwerendę o zdarzenia "ClusterUpgradeCompleted" w magazynie zdarzeń:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*Problemy z uaktualnieniem klastra:*

Podobnie, jeśli wystąpiły problemy z niedawnym uaktualnieniem klastra, można zbadać wszystkie zdarzenia dla jednostki klastra. Zobaczysz różne zdarzenia, w tym inicjowanie uaktualnień i każdy UD, dla którego uaktualnienie zostało pomyślnie przerzucone. Zostaną również wyświetlne zdarzenia dla punktu, w którym rozpoczęto wycofywanie i odpowiednie zdarzenia kondycji. Oto zapytanie, którego można użyć do tego celu:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zmiany stanu węzła:*

Aby zobaczyć zmiany stanu węzła w ciągu ostatnich kilku dni - gdy węzły poszły w górę lub w dół, lub zostały aktywowane lub dezaktywowane (przez platformę, usługę chaosu lub z danych wejściowych użytkownika) - użyj następującej kwerendy:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Zdarzenia aplikacji:*

Można również śledzić ostatnie wdrożenia aplikacji i uaktualnienia. Użyj następującej kwerendy, aby wyświetlić wszystkie zdarzenia aplikacji w klastrze:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*Kondycja historyczna dla aplikacji:*

Oprócz wyświetlania tylko zdarzenia cyklu życia aplikacji, można również zobaczyć dane historyczne dotyczące kondycji określonej aplikacji. Można to zrobić, określając nazwę aplikacji, dla której chcesz zebrać dane. Użyj tej kwerendy, aby uzyskać `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`wszystkie zdarzenia kondycji aplikacji: . Jeśli chcesz uwzględnić zdarzenia kondycji, które mogły wygasnąć (przeminęło `,ApplicationHealthReportExpired` ich czas wygaśnięcia (TTL)), dodaj na końcu kwerendy, aby filtrować dwa typy zdarzeń.

*Historyczna kondycja wszystkich usług w "myApp":*

Obecnie zdarzenia raportu kondycji dla usług `DeployedServicePackageNewHealthReport` są wyświetlane jako zdarzenia w ramach odpowiedniej jednostki aplikacji. Aby zobaczyć, jak twoje usługi zostały robi dla "App1", użyj następującej kwerendy:`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*Ponowna konfiguracja partycji:*

Aby wyświetlić wszystkie ruchy partycji, które miały `PartitionReconfigured` miejsce w klastrze, kwerendy dla zdarzenia. Może to pomóc w znalezieniu, jakie obciążenia działały w którym węźle w określonych godzinach podczas diagnozowania problemów w klastrze. Oto przykładowa kwerenda, która to robi:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*Usługa Chaos:*

Istnieje zdarzenie, gdy usługa Chaos jest uruchamiana lub zatrzymywany, która jest narażona na poziomie klastra. Aby zobaczyć ostatnie korzystanie z usługi Chaos, użyj następującej kwerendy:`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

