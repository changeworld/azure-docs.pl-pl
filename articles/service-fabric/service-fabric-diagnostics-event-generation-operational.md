---
title: Lista zdarzeń w sieci szkieletowej usługi platformy Azure | Dokumentacja firmy Microsoft
description: Pełną listę zdarzeń udostępnianych przez usługi Azure Service Fabric, aby ułatwić monitorowanie klastrów.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: da4a779bca806fe6aa392db96eafc6c20f8ddcf6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182165"
---
# <a name="list-of-service-fabric-events"></a>Lista zdarzeń usługi Service Fabric 

Usługa Service Fabric udostępnia zestaw podstawowy zdarzenia klastra informujące o stanie klastra jako [usługi Service Fabric zdarzenia](service-fabric-diagnostics-events.md). Te są oparte na akcje wykonane przez usługę Service Fabric na węzły klastra lub przez klaster właściciela / — operator decyzji związanych z zarządzaniem. Te zdarzenia są dostępne dla zapytań [bazy danych EventStore](service-fabric-diagnostics-eventstore.md) w klastrze lub za pośrednictwem kanału operacyjnej. Na maszynach Windows kanał operacyjny jest również podłączany do dziennika zdarzeń — tak aby było widać usługi Service Fabric zdarzeniach w Podglądzie zdarzeń. 

>[!NOTE]
>Lista zdarzeń usługi Service Fabric w przypadku klastrów w wersji < 6.2 można znaleźć w poniższej sekcji. 

Poniżej przedstawiono dostępne na platformach, posortowane według jednostki mapowania ich na listę wszystkich zdarzeń.

## <a name="cluster-events"></a>Zdarzenia klastra

**Zdarzenia uaktualniania klastra**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | Rozpoczęto Uaktualnianie klastra | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29628 | ClusterUpgradeCompleted | Ukończono uaktualnianie klastra| MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29629 | ClusterUpgradeRollbackStarted | Rozpoczęto Uaktualnianie klastra można wycofać | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | Uaktualnianie klastra została ukończona, wycofywanie | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Ukończono uaktualnianie domeny podczas uaktualniania klastra | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |

## <a name="node-events"></a>Węzeł zdarzenia

**Zdarzenia cyklu życia węzła** 

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | Dezaktywacja węzła zostało zakończone. | FM | Informacyjne | 1 |
| 18603 | NodeUp | Klastra wykrył, że uruchomieniu węzła | FM | Informacyjne | 1 |
| 18604 | NodeDown | Klastra wykrył, że węzeł został zamknięty. |  FM | Informacyjne | 1 |
| 18605 | NodeAddedToCluster | Nowy węzeł został dodany do klastra | FM | Informacyjne | 1 |
| 18606 | NodeRemovedFromCluster | Po usunięciu węzła z klastra | FM | Informacyjne | 1 |
| 18607 | NodeDeactivateStarted | Dezaktywacja węzła została uruchomiona. | FM | Informacyjne | 1 |
| 25620 | NodeOpening | Węzeł jest uruchamiana. Pierwszy etap cyklu życia węzła | FabricNode | Informacyjne | 1 |
| 25621 | NodeOpenSucceeded | Węzeł został uruchomiony pomyślnie | FabricNode | Informacyjne | 1 |
| 25622 | NodeOpenFailed | Węzeł nie powiodło się | FabricNode | Informacyjne | 1 |
| 25623 | NodeClosing | Węzeł jest zamykany. Początek końcowym etapie cyklu życia węzła | FabricNode | Informacyjne | 1 |
| 25624 | NodeClosed | Węzeł pomyślnie wyłączone | FabricNode | Informacyjne | 1 |
| 25625 | NodeAborting | Węzeł jest uruchamiana ungracefully zamknięcia | FabricNode | Informacyjne | 1 |
| 25626 | NodeAborted | Węzeł ungracefully został zamknięty | FabricNode | Informacyjne | 1 |

## <a name="application-events"></a>Zdarzenia aplikacji

**Zdarzenia cyklu życia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Utworzono nową aplikację | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29625 | ApplicationDeleted | Istniejąca aplikacja została usunięta. | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 23083 | ApplicationProcessExited | Proces w ramach aplikacji został zakończony | Hosting | Informacyjne | 1 |

**Zdarzenia uaktualnienia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | Uaktualnienie aplikacji została uruchomiona. | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29622 | ApplicationUpgradeCompleted | Ukończono uaktualnianie aplikacji | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | Uaktualnienie aplikacji rozpoczął wycofywania |MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | Uaktualnienie aplikacji została zakończona, wycofywanie | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Ukończono uaktualnianie domeny podczas uaktualniania aplikacji | MENEDŻER CERTYFIKATÓW | Informacyjne | 1 |

## <a name="service-events"></a>Zdarzenia usługi

**Zdarzenia cyklu życia usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Utworzono nową usługę | FM | Informacyjne | 1 |
| 18658 | ServiceDeleted | Istniejąca usługa została usunięta. | FM | Informacyjne | 1 |

## <a name="partition-events"></a>Zdarzenia w partycji

**Zdarzenia przenoszenia partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Ponowna konfiguracja partycji zostało zakończone. | POMOC ZDALNA | Informacyjne | 1 |

## <a name="container-events"></a>Zdarzenia kontenera

**Zdarzenia cyklu życia kontenera** 

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Kontener został uruchomiony. | Hosting | Informacyjne | 1 |
| 23075 | ContainerDeactivated | Kontener został zatrzymany. | Hosting | Informacyjne | 1 |
| 23082 | ContainerExited | Kontener został zakończony — Sprawdź plik flagi UnexpectedTermination | Hosting | Informacyjne | 1 |

## <a name="health-reports"></a>Raportów o kondycji

**Zdarzenia raportów kondycji klastra**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Dostępny jest nowy raport kondycji klastra | HM | Informacyjne | 1 |
| 54437 | ClusterHealthReportExpired | Istniejący raport kondycji klastra utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji węzła**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Nowy raport kondycji węzeł jest dostępny | HM | Informacyjne | 1 |
| 54432 | NodeHealthReportExpired | Istniejący raport kondycji węzła utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportu kondycji aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Utworzono nowy raport kondycji aplikacji. To wdrożenie aplikacji. | HM | Informacyjne | 1 |
| 54426 | DeployedApplicationNewHealthReport | Utworzono nowy raport kondycji wdrożonej aplikacji | HM | Informacyjne | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Utworzono nowy raport kondycji wdrożonej usługi | HM | Informacyjne | 1 |
| 54434 | ApplicationHealthReportExpired | Istniejący raport kondycji aplikacji utracił ważność. | HM | Informacyjne | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Istniejący raport kondycji wdrożoną aplikację utracił ważność. | HM | Informacyjne | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Istniejący raport kondycji wdrożonej usługi utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Utworzono nowy raport kondycji usługi | HM | Informacyjne | 1 |
| 54433 | ServiceHealthReportExpired | Wygasła istniejącego raportu kondycji usługi | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Utworzono nowy raport kondycji partycji | HM | Informacyjne | 1 |
| 54431 | PartitionHealthReportExpired | Istniejący raport kondycji partycji utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Został utworzony raport o kondycji stanowych replik | HM | Informacyjne | 1 |
| 54430 | StatelessInstanceNewHealthReport | Utworzono nowy raport kondycji bezstanowe wystąpienia | HM | Informacyjne | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Istniejący raport kondycji stanowych replik utracił ważność. | HM | Informacyjne | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Istniejący raport kondycji wystąpienia o bezstanowa utracił ważność. | HM | Informacyjne | 1 |

## <a name="chaos-testing-events"></a>Zdarzenia testowania chaosu 

**Zdarzenia sesji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Rozpoczęto Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50023 | ChaosStopped | Chaos z sesji testowania została zatrzymana. | Testowalności | Informacyjne | 1 |

**Zdarzenia węzła chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Węzeł ma zaplanowane ponowne uruchomienie jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50087 | ChaosNodeRestartCompleted | Węzeł zostało zakończone, ponowne uruchomienie jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

**Zdarzenia aplikacji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Podczas Chaos z sesji testowania zostało zaplanowane ponowne uruchomienie pakietu kodu | Testowalności | Informacyjne | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Ponowne uruchomienie pakietu kodu zostało zakończone podczas Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

**Zdarzenia partycji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Partycja podstawowa zaplanowano przenieść w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Partycji dodatkowych zaplanowano przenieść w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Dogłębną analizę przenoszenia partycja podstawowa jest dostępna | Testowalności | Informacyjne | 1 |

**Zdarzenia repliki chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Ponowne uruchomienie repliki zostało zaplanowane jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Usuwanie repliki zostało zaplanowane jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Usuwanie repliki została zakończona w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

## <a name="other-events"></a>Inne zdarzenia

**Zdarzenia korelacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Korelacja został detacted | Testowalności | Informacyjne | 1 |

## <a name="events-prior-to-version-62"></a>Zdarzenia przed wersję 6.2

Poniżej przedstawiono pełną listę zdarzeń udostępnianych przez usługę Service Fabric poprzedzające wersję 6.2.

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadanie) | Poziom |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informacyjne |
| 25621 | NodeOpenedSuccess | FabricNode | Informacyjne |
| 25622 | NodeOpenedFailed | FabricNode | Informacyjne |
| 25623 | NodeClosing | FabricNode | Informacyjne |
| 25624 | NodeClosed | FabricNode | Informacyjne |
| 25625 | NodeAborting | FabricNode | Informacyjne |
| 25626 | NodeAborted | FabricNode | Informacyjne |
| 29627 | ClusterUpgradeStart | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29628 | ClusterUpgradeComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29629 | ClusterUpgradeRollback | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29630 | ClusterUpgradeRollbackComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29631 | ClusterUpgradeDomainComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 23074 | ContainerActivated | Hosting | Informacyjne |
| 23075 | ContainerDeactivated | Hosting | Informacyjne |
| 29620 | ApplicationCreated | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29621 | ApplicationUpgradeStart | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29622 | ApplicationUpgradeComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29623 | ApplicationUpgradeRollback | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29624 | ApplicationUpgradeRollbackComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29625 | ApplicationDeleted | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 29626 | ApplicationUpgradeDomainComplete | MENEDŻER CERTYFIKATÓW | Informacyjne |
| 18566 | ServiceCreated | FM | Informacyjne |
| 18567 | ServiceDeleted | FM | Informacyjne |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat ogólnych [generowanie zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w usłudze Service Fabric
* Modyfikowanie swoje [diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md) konfigurację, aby zebrać więcej dzienniki
* [Konfigurowanie usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) się z Operational channel dzienników
