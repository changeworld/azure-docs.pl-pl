---
title: Azure Service Fabric zdarzeń Store | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o bazie danych EventStore usługi Azure Service Fabric
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
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 36d01a9e6e55ae54377ba3f983f779dbc692c49a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681526"
---
# <a name="eventstore-service-overview"></a>Omówienie usługi bazy danych EventStore

>[!NOTE]
>Począwszy od usługi Service Fabric w wersji 6.4. interfejsy API bazy danych EventStore są dostępne tylko w przypadku klastrów Windows działających na platformie Azure, tylko. Pracujemy nad przenoszenie tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

## <a name="overview"></a>Omówienie

Wprowadzona w wersji 6.2, usługa bazy danych EventStore jest opcji monitorowania w usłudze Service Fabric. Bazy danych EventStore zapewnia sposób, aby sprawdzić stan klastra lub obciążeń w danym punkcie w czasie. Bazy danych EventStore jest stanowej usługi Service Fabric, która utrzymuje zdarzenia z klastra. Zdarzenia są udostępniane za pośrednictwem narzędzia Service Fabric Explorer, REST i interfejsów API. Bazy danych EventStore zapytań klastra bezpośrednio po to, aby uzyskać dane diagnostyczne w dowolnej jednostce w klastrze i powinny być używane do pomocy:

* Diagnozuj problemy z tworzenia i testowania lub gdy być może używasz potoku monitorowania
* Upewnij się, że akcje zarządzania, które są tworzone w klastrze są przetwarzane prawidłowo
* Pobierz "migawkę" jak usługi Service Fabric prowadzi interakcję z określonego obiektu

![Bazy danych EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Aby wyświetlić pełną listę zdarzeń, które są dostępne w bazie danych EventStore, zobacz [zdarzenia usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Począwszy od usługi Service Fabric w wersji 6.2. interfejsy API bazy danych EventStore są obecnie dostępne w klastrach Windows działających na platformie Azure tylko w wersji zapoznawczej. Pracujemy nad przenoszenie tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

Usługa bazy danych EventStore można wykonywać zapytania, zdarzenia, które są dostępne dla każdej jednostki i typu jednostki w klastrze. Oznacza to, że można wyszukiwać zdarzenia na następujących poziomach:
* Klastra: zdarzenia specyficzne dla klastra, sama (np. uaktualnienie klastra)
* Węzły: wszystkie zdarzenia poziomu węzeł
* Węzeł: zdarzenia specyficzne dla jednego węzła, identyfikowany przez `nodeName`
* Aplikacje: wszystkich zdarzeń na poziomie aplikacji
* Aplikacji: specyficzne dla aplikacji jednej identyfikowane przez zdarzenia `applicationId`
* Usługi: zdarzenia z wszystkich usług w klastrach
* Usługa: zdarzenia z określonej usługi identyfikowane przez `serviceId`
* Partycje: zdarzenia ze wszystkich partycji
* Partycja: zdarzenia z określonej partycji identyfikowane przez `partitionId`
* Replik partycji: zdarzenia ze wszystkich replik / wystąpień określonej partycji identyfikowane przez `partitionId`
* Repliki partycji: zdarzenia z określonym repliki / identyfikowane przez wystąpienie `replicaId` i `partitionId`

Aby dowiedzieć się więcej o interfejsie API, sprawdź [dokumentacja interfejsu API bazy danych EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Usługa bazy danych EventStore ma również możliwość korelowanie zdarzeń w klastrze. Patrząc na zdarzenia, które zostały napisane w tym samym czasie z różnymi jednostkami, które mogą mieć wpływ na siebie, usługa bazy danych EventStore jest można połączyć te zdarzenia, aby ułatwić zidentyfikowanie przyczyny działań w klastrze. Na przykład, jeśli jedna z aplikacji stają się zła, bez konieczności wprowadzania zmian wywołane, bazy danych EventStore również przyjrzeć się inne zdarzenia udostępnianych przez platformę i będą można skorelować za pomocą `Error` lub `Warning` zdarzeń. To ułatwia szybsze wykrywanie awarii i głównych przyczyn analizy.

## <a name="enable-eventstore-on-your-cluster"></a>Włączanie bazy danych EventStore w klastrze

### <a name="local-cluster"></a>Klaster lokalny

W [fabricSettings.json w klastrze](service-fabric-cluster-fabric-settings.md)Dodaj EventStoreService jako funkcja dodatku i przeprowadzić uaktualnienie klastra.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Klaster usługi Azure

W szablonie usługi Azure Resource Manager klastra, można włączyć usługę bazy danych EventStore, wykonując [uaktualnienie konfiguracji klastra](service-fabric-cluster-config-upgrade-azure.md) i dodając następujący kod, można użyć PlacementConstraints umieszczenie replik bazy danych EventStore Usługa na określonego elementu NodeType np. NodeType dedykowany dla usług systemowych. `upgradeDescription` Sekcji konfiguruje uaktualnienia config wyzwalanie ponownego uruchomienia w węzłach. Usuń z sekcji, w innym update.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              }
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Kolejne kroki
* Rozpoczynanie pracy z interfejsem API bazy danych EventStore - [w klastrach usługi Azure Service Fabric przy użyciu interfejsów API bazy danych EventStore](service-fabric-diagnostics-eventstore-query.md)
* Dowiedz się więcej o listę zdarzeń oferowany przez bazy danych EventStore - [zdarzenia usługi Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* Omówienie monitorowania i diagnostyki w usłudze Service Fabric - [monitorowania i diagnostyki dla usługi Service Fabric](service-fabric-diagnostics-overview.md)
* Wyświetl pełną listę wywołania interfejsu API — [dokumentacja interfejsu API REST w bazie danych EventStore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Dowiedz się więcej o monitorowaniu klastra- [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).