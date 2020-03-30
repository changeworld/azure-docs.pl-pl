---
title: Magazyn zdarzeń sieci szkieletowej usługi Azure
description: Dowiedz się więcej o magazynie eventstore sieci szkieletowej usług Azure, sposób, aby zrozumieć i monitorować stan klastra lub obciążeń w dowolnym momencie.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645739"
---
# <a name="eventstore-overview"></a>Omówienie sklepu EventStore

>[!NOTE]
>Od usługi Service Fabric w wersji 6.4. interfejsy API eventstore są dostępne tylko dla klastrów systemu Windows uruchomionych tylko na platformie Azure. Pracujemy nad przeniesieniem tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

## <a name="overview"></a>Omówienie

Wprowadzono w wersji 6.2, EventStore usługa jest opcją monitorowania w sieci szkieletowej usług. EventStore umożliwia zrozumienie stanu klastra lub obciążeń w danym momencie.
EventStore jest usługą sieci szkieletowej usług stanowych, która przechowuje zdarzenia z klastra. Zdarzenie jest udostępniane za pośrednictwem Eksploratora sieci szkieletowej usług, REST i interfejsów API. EventStore wysyła zapytanie do klastra bezpośrednio, aby uzyskać dane diagnostyczne na dowolnej encji w klastrze i powinny być używane do pomocy:

* Diagnozowanie problemów podczas opracowywania lub testowania lub w przypadku, gdy może być używany potok monitorowania
* Upewnij się, że akcje zarządzania podejmowane w klastrze są przetwarzane poprawnie
* Pobierz "migawkę" sposobu interakcji sieci szkieletowej usług z określoną encją

![Sklep eventowy](media/service-fabric-diagnostics-eventstore/eventstore.png)

Aby wyświetlić pełną listę zdarzeń dostępnych w magazynie zdarzeń, zobacz [Zdarzenia sieci szkieletowej usług.](service-fabric-diagnostics-event-generation-operational.md)

>[!NOTE]
>Od usługi Service Fabric w wersji 6.4. interfejsy API i środowisko ux w programie EventStore są ogólnie dostępne dla klastrów systemu Windows platformy Azure. Pracujemy nad przeniesieniem tej funkcji do systemu Linux, a także naszych autonomicznych klastrów.

Usługa EventStore można wyszukiwać dla zdarzeń, które są dostępne dla każdego typu jednostki i jednostki w klastrze. Oznacza to, że można wyszukiwać zdarzenia na następujących poziomach:
* Klaster: zdarzenia specyficzne dla samego klastra (np. uaktualnienie klastra)
* Węzły: wszystkie zdarzenia na poziomie węzła
* Węzeł: zdarzenia specyficzne dla jednego węzła, identyfikowane przez`nodeName`
* Aplikacje: wszystkie zdarzenia na poziomie aplikacji
* Zastosowanie: zdarzenia specyficzne dla jednej aplikacji zidentyfikowanej przez`applicationId`
* Usługi: zdarzenia ze wszystkich usług w klastrach
* Usługa: zdarzenia z określonej usługi zidentyfikowanej przez`serviceId`
* Partycje: zdarzenia ze wszystkich partycji
* Partycja: zdarzenia z określonej partycji identyfikowane przez`partitionId`
* Repliki partycji: zdarzenia ze wszystkich replik / wystąpień w obrębie określonej partycji identyfikowane przez`partitionId`
* Replika partycji: zdarzenia z określonej `replicaId` repliki / wystąpienia zidentyfikowane przez i`partitionId`

Aby dowiedzieć się więcej o interfejsie API, zapoznaj się z [odwołaniem do interfejsu API eventstore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Usługa EventStore ma również możliwość skorelowania zdarzeń w klastrze. Patrząc na zdarzenia, które zostały napisane w tym samym czasie z różnych jednostek, które mogą mieć wpływ na siebie nawzajem, EventStore usługa jest w stanie połączyć te zdarzenia, aby pomóc w identyfikacji przyczyn działań w klastrze. Na przykład jeśli jedna z aplikacji stanie się w złej kondycji bez żadnych zmian wywołanych, EventStore będzie również `Error` patrzeć `Warning` na inne zdarzenia udostępniane przez platformę i może skorelować to z lub zdarzenia. Pomaga to w szybszym wykrywaniu awarii i analizie przyczyn źródłowych.

## <a name="enable-eventstore-on-your-cluster"></a>Włączanie magazynu eventstore w klastrze

### <a name="local-cluster"></a>Klaster lokalny

W [fabricSettings.json w klastrze](service-fabric-cluster-fabric-settings.md)dodaj EventStoreService jako funkcję addOn i wykonaj uaktualnienie klastra.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Klaster platformy Azure w wersji 6.5+
Jeśli klaster platformy Azure zostanie uaktualniony do wersji 6.5 lub nowszej, EventStore zostanie automatycznie włączony w klastrze. Aby zrezygnować, należy zaktualizować szablon klastra o następujące czynności:

* Używanie wersji interfejsu `2019-03-01` API lub nowszej 
* Dodawanie następującego kodu do sekcji właściwości w klastrze
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Klaster platformy Azure w wersji 6.4

Jeśli używasz wersji 6.4, możesz edytować szablon usługi Azure Resource Manager, aby włączyć usługę EventStore. Odbywa się to przez wykonanie [uaktualnienia konfiguracji klastra](service-fabric-cluster-config-upgrade-azure.md) i dodanie następującego kodu, można użyć PlacementConstraints umieścić repliki usługi EventStore na określonym NodeType, np. Sekcja `upgradeDescription` konfiguruje uaktualnienie konfiguracji w celu wyzwolenia ponownego uruchomienia w węzłach. Sekcję można usunąć w innej aktualizacji.

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
              },
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


## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do interfejsu API eventstore — [przy użyciu interfejsów API eventstore w klastrach sieci szkieletowej usług Azure](service-fabric-diagnostics-eventstore-query.md)
* Dowiedz się więcej o liście zdarzeń oferowanych przez EventStore — [zdarzenia sieci szkieletowej usług](service-fabric-diagnostics-event-generation-operational.md)
* Omówienie monitorowania i diagnostyki w sieci szkieletowej usług — [monitorowanie i diagnostyka sieci szkieletowej usług](service-fabric-diagnostics-overview.md)
* Wyświetlanie pełnej listy wywołań interfejsu API — [odwołanie do interfejsu API usługi EventStore REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* Dowiedz się więcej o monitorowaniu klastra — [monitorowanie klastra i platformy](service-fabric-diagnostics-event-generation-infra.md).
