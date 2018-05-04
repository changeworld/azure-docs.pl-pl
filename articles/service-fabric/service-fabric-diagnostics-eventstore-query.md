---
title: Zapytania do zdarzenia klastra przy użyciu interfejsów API EventStore w klastrach sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Dowiedz się jak używać usługi sieć szkieletowa EventStore interfejsów API usługi Azure do zapytania dla zdarzeń platformy
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: af97385981c61f32c4136921d3cf14a526fc6ddb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="query-eventstore-apis-for-cluster-events"></a>Interfejsy API EventStore zapytania do zdarzenia klastra

W tym artykule opisano sposób tworzenia zapytań EventStore interfejsów API, które są dostępne w sieci szkieletowej usług wersję 6.2 i później — Jeśli chcesz dowiedzieć się więcej o usłudze EventStore, zobacz [Omówienie usługi EventStore](service-fabric-diagnostics-eventstore.md). Obecnie usługa EventStore można tylko uzyskać dostępu do danych ostatnich 7 dni (oparte na zasady przechowywania danych diagnostycznych z klastra).

>[!NOTE]
>Począwszy od platformy Service Fabric wersję 6.2. interfejsy API EventStore są obecnie w przypadku klastrów systemu Windows tylko działających na platformie Azure w wersji zapoznawczej. Pracujemy nad eksportowanie tej funkcji do systemu Linux, a także naszych klastry autonomicznego.

Interfejsy API EventStore jest dostępny bezpośrednio przez punkt końcowy REST lub programowo. W zależności od tego zapytania istnieje kilka parametrów, które są wymagane do zbierania odpowiednich danych. Te zwykle obejmują:
* `api-version`: wersja API EventStore używasz
* `StartTimeUtc`: Określa początek okresu jest interesujące w patrzeć
* `EndTimeUtc`: koniec okresu czasu

Oprócz tych dostępne są następujące parametry opcjonalne również, takich jak:
* `timeout`: zastąpić domyślny limit czasu 60 sekund dla operacji żądania
* `eventstypesfilter`: daje możliwość filtrowania dla określonych typów zdarzeń
* `ExcludeAnalysisEvents`: nie zwracać zdarzenia "Analizy". Domyślnie EventStore kwerendy będą zwracać ze zdarzeniami "Analiza", jeśli jest to możliwe — analizy zdarzenia są bardziej rozbudowane zdarzenia operacyjne kanału, które zawiera dodatkowy kontekst lub informacji poza regularnego zdarzenia platformy Service Fabric i zapewnić bardziej szczegółowo.
* `SkipCorrelationLookup`: nie Wyszukaj potencjalne zdarzenia skorelowane w klastrze. Domyślnie EventStore będzie podejmować próby skorelować zdarzenia w klastrze, a łączenie zdarzeń, gdy jest to możliwe. 

Każdy obiekt w klastrze mogą być zapytania dotyczące zdarzeń. Można również wysyłać zapytania dotyczące zdarzeń dla wszystkich obiektów tego typu. Można na przykład wyszukać zdarzenia dla określonego węzła lub dla wszystkich węzłów w klastrze. Bieżący zestaw jednostek, dla których można wyszukać zdarzenia jest (z zapytanie może być struktury):
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
* Repliki: `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>Podczas odwoływania się do aplikacji lub nazwę usługi, zapytanie nie musi obejmować "fabric: /" prefiks. Ponadto jeśli aplikacji lub usługi nazwy "/" w nich, przełącz go do "~" do zachowania pracy zapytania. Na przykład, jeśli aplikacja jest wyświetlany jako "fabric: / App1/FrontendApp", zapytań określonych aplikacji czy strukturę jako `/EventsStore/Applications/App1~FrontendApp/$/Events`.
>Ponadto raportów o kondycji usług dzisiaj są wyświetlane w oknie odpowiednią aplikację, czy wyszukać `DeployedServiceHealthReportCreated` zdarzenia dla jednostki prawo aplikacji. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>Zapytanie EventStore przez punkty końcowe interfejsu API REST

Można zbadać EventStore bezpośrednio przez punkt końcowy REST, wprowadzając `GET` żądań: `<your cluster address>/EventsStore/<entity>/Events/`.

Na przykład, aby wyszukać wszystkie zdarzenia klastra między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, żądanie będzie wyglądała:

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.2-preview&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

Można to albo zwraca błąd, jeśli żadne zdarzenia nie są dostępne, lub jeśli zapytanie zakończyło się pomyślnie, zostanie wyświetlone zdarzenia zwracane w formacie json:

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

W tym miejscu zobaczysz, że między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, gdy najpierw została miały, ten klaster pomyślnie ukończył jego pierwszym uaktualnienia z `"CurrentClusterVersion": "0.0.0.0:"` do `"TargetClusterVersion": "6.2:1.0"`w `"OverallUpgradeElapsedTimeInMs": "120196.5212"`.

## <a name="query-the-eventstore-programmatically"></a>Zapytanie EventStore programowo

Możesz także zbadać EventStore programowo, za pomocą [biblioteki klienta usługi sieć szkieletowa](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library).

Po utworzeniu Konfigurowanie klienta usługi sieci szkieletowej może wyszukiwać zdarzenia po zalogowaniu się do EventStore następująco: ` sfhttpClient.EventStore.<request>`

Oto przykładowe żądanie klastra wszystkie zdarzenia między `2018-04-03T18:00:00Z` i `2018-04-04T18:00:00Z`, za pomocą `GetClusterEventListAsync` funkcji.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

## <a name="sample-scenarios-and-queries"></a>Przykładowe scenariusze i zapytań

Oto kilka przykładów w sposób należy wywołać interfejsów API REST magazynu zdarzeń do danych o stanie klastra.

1. Uaktualnienia klastra:

Aby wyświetlić czas ostatniej klastra została pomyślnie lub podjęto próbę uaktualnienia ostatniego tygodnia, można zbadać interfejsy API do ostatnio wykonanych uaktualnień do klastra, badając dla zdarzenia "ClusterUpgradeComplete" w EventStore: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeComplete`

1. Problemy z uaktualnieniem klastra:

Podobnie jeśli wystąpiły problemy z uaktualnieniem klastra, można zbadać dla wszystkich zdarzeń dla obiektu klastra. Zobaczysz różnych zdarzeń, w tym rozpoczęcia uaktualnienia i każdego UD, dla którego uaktualnienia wycofana za pośrednictwem pomyślnie. Widoczne będzie także zdarzeń dla punktu, w którym wycofywania uruchomiona i odpowiednie zdarzenia kondycji. Oto zapytania, które będzie używane w tym: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Zmiany stanu węzła:

Aby zobaczyć stan węzła zmienia się w ciągu ostatnich kilku dni — po węzłów poszło w górę i w dół lub zostały aktywowane lub dezaktywowane (przez platformę chaos service lub z danych wejściowych użytkownika) - Użyj następującego zapytania: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Zdarzenia aplikacji:

Można także śledzić Twoje bieżące wdrożenia aplikacji i uaktualnień. Użyj następującego zapytania, aby zobaczyć wszystkie aplikacji powiązanych zdarzeń w klastrze: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

1. Historyczne kondycji dla aplikacji:

Oprócz widoczne były tylko zdarzenia cyklu życia aplikacji, można także wyświetlić dane historyczne na kondycji określonej aplikacji. Można to zrobić, określając nazwę aplikacji, dla której chcesz zbierać dane. Użyj tego zapytania, aby pobrać wszystkie zdarzenia kondycji aplikacji: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.2-preview&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ProcessApplicationReport`. Jeśli chcesz uwzględnić zdarzenia kondycji, które mogą wygasnąć (przeniesiono przekazany czas wygaśnięcia (TTL)), Dodaj `,ExpiredDeployedApplicationEvent` na końcu kwerendy, aby odfiltrować dwa typy zdarzeń.

1. Historyczne kondycji dla wszystkich usług w "myApp":

Obecnie zdarzenia raportowania kondycji usługi wyświetlane jako `DeployedServiceHealthReportCreated` zdarzenia odpowiednia jednostka aplikacji. Aby zobaczyć, jak usługi ma zostały czynności dla "App1", użyj następującej kwerendy: `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServiceHealthReportCreated`

1. Ponowna konfiguracja partycji:

Aby wyświetlić wszystkie kwerendy przeniesień partycji, które wystąpiły w klastrze, `ReconfigurationCompleted` zdarzeń. Może to pomóc ustalić obciążenia uruchomione na wybór węzła, w określonym czasie, gdy diagnozowanie problemów w klastrze. Poniżej przedstawiono przykładowe zapytanie, które robi to: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.2-preview&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigurationCompleted`

1. Usługa chaos:

Brak zdarzeń dla po Chaos usługa jest uruchomiona lub zatrzymana, który jest dostępne na poziomie klastra. Aby wyświetlić ostatnie korzystanie z usługi Chaos, użyj następującej kwerendy: `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.2-preview&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

