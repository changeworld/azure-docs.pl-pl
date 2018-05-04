---
title: Lista zdarzeń sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Pełną listę zdarzeń dostarczonych przez sieć szkieletowa usług Azure, aby ułatwić monitorowanie klastrów.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 8426218cf8f0819f20c78d9d84e202541d025b60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="list-of-service-fabric-events"></a>Lista zdarzeń sieci szkieletowej usług 

Usługa sieci szkieletowej udostępnia zestaw podstawowy klaster zdarzeń informujące o stanie klastra jako [zdarzenia sieci szkieletowej usług](service-fabric-diagnostics-events.md). Te są oparte na akcje wykonywane przez sieć szkieletowa usług na węzły i klaster lub zarządzania decyzje przez właściciela/operatora klastra. Te zdarzenia są dostępne dla zapytań [EventStore](service-fabric-diagnostics-eventstore.md) w klastrze lub za pośrednictwem kanału operacyjną. Na komputerach z systemem Windows operacyjne kanału jest również podłączonymi do dziennika zdarzeń — pozwala zobaczyć, usługa sieci szkieletowej zdarzeniach w Podglądzie zdarzeń. 

>[!NOTE]
>Lista zdarzeń usługi sieć szkieletowa usług dla klastrów w wersji < 6.2 można znaleźć w poniższej sekcji. 

W tym miejscu jest dostępna w platformie posortowane według jednostki, które są one mapowane na listę wszystkich zdarzeń.

## <a name="cluster-events"></a>Zdarzenia klastra

**Zdarzenia uaktualnienia klastra**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informacyjne | C1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informacyjne | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informacyjne | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informacyjne | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informacyjne | 1 |

**Klaster zdarzenia raportów kondycji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informacyjne | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informacyjne | 1 |

**Zdarzenia usługi chaos**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Pola | Informacyjne | 1 |
| 50023 | ChaosStoppedEvent | Pola | Informacyjne | 1 |

## <a name="node-events"></a>Zdarzenia węzła

**Zdarzenia cyklu życia węzła** 

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informacyjne | 1 |
| 18603 | NodeUpOperational | FM | Informacyjne | 1 |
| 18604 | NodeDownOperational | FM | Informacyjne | 1 |
| 18605 | NodeAddedOperational | FM | Informacyjne | 1 |
| 18606 | NodeRemovedOperational | FM | Informacyjne | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informacyjne | 1 |
| 25620 | NodeOpening | FabricNode | Informacyjne | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informacyjne | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informacyjne | 1 |
| 25623 | NodeClosing | FabricNode | Informacyjne | 1 |
| 25624 | NodeClosed | FabricNode | Informacyjne | 1 |
| 25625 | NodeAborting | FabricNode | Informacyjne | 1 |
| 25626 | NodeAborted | FabricNode | Informacyjne | 1 |

**Zdarzenia raportów kondycji węzła**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informacyjne | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informacyjne | 1 |

**Chaos węzła zdarzenia**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Pola | Informacyjne | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Pola | Informacyjne | 1 |

## <a name="application-events"></a>Zdarzenia aplikacji

**Zdarzenia cyklu życia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informacyjne | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informacyjne | 1 |
| 23083 | ProcessExitedOperational | Hosting | Informacyjne | 1 |

**Zdarzenia uaktualnienia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informacyjne | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informacyjne | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informacyjne | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informacyjne | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informacyjne | 1 |

**Zdarzenia raportów kondycji aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informacyjne | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informacyjne | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informacyjne | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informacyjne | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informacyjne | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informacyjne | 1 |

**Chaos zdarzeń aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Pola | Informacyjne | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Pola | Informacyjne | 1 |

## <a name="service-events"></a>Zdarzenia usługi

**Zdarzenia cyklu życia usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informacyjne | 1 |
| 18658 | ServiceDeletedOperational | FM | Informacyjne | 1 |

**Zdarzenia raportowania kondycji usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informacyjne | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informacyjne | 1 |

## <a name="partition-events"></a>Zdarzenia partycji

**Zdarzenia przenoszenia partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | POMOC ZDALNA | Informacyjne | 1 |

**Zdarzenia raportów kondycji partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informacyjne | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informacyjne | 1 |

**Chaos partycji zdarzenia**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Pola | Informacyjne | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Pola | Informacyjne | 1 |

**Partycja analizy zdarzeń**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Pola | Informacyjne | 1 |

## <a name="replica-events"></a>Zdarzenia repliki

**Zdarzenia raportów kondycji repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informacyjne | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informacyjne | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informacyjne | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informacyjne | 1 |

**Chaos repliki zdarzenia**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Pola | Informacyjne | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Pola | Informacyjne | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Pola | Informacyjne | 1 |

## <a name="container-events"></a>Kontenery zdarzeń

**Zdarzenia cyklu życia kontenera** 

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | Informacyjne | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | Informacyjne | 1 |
| 23082 | ContainerExitedOperational | Hosting | Informacyjne | 1 |

## <a name="other-events"></a>Inne zdarzenia

**Zdarzenia korelacji**

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom | Wersja |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Pola | Informacyjne | 1 |

## <a name="events-prior-to-version-62"></a>Zdarzenia poprzedzające wersję 6.2

Poniżej przedstawiono pełną listę zdarzeń udostępnianych przez usługi sieć szkieletowa poprzedzające wersję 6.2.

| Identyfikator zdarzenia | Name (Nazwa) | Źródło (zadania) | Poziom |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informacyjne |
| 25621 | NodeOpenedSuccess | FabricNode | Informacyjne |
| 25622 | NodeOpenedFailed | FabricNode | Informacyjne |
| 25623 | NodeClosing | FabricNode | Informacyjne |
| 25624 | NodeClosed | FabricNode | Informacyjne |
| 25625 | NodeAborting | FabricNode | Informacyjne |
| 25626 | NodeAborted | FabricNode | Informacyjne |
| 29627 | ClusterUpgradeStart | CM | Informacyjne |
| 29628 | ClusterUpgradeComplete | CM | Informacyjne |
| 29629 | ClusterUpgradeRollback | CM | Informacyjne |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informacyjne |
| 29631 | ClusterUpgradeDomainComplete | CM | Informacyjne |
| 23074 | ContainerActivated | Hosting | Informacyjne |
| 23075 | ContainerDeactivated | Hosting | Informacyjne |
| 29620 | ApplicationCreated | CM | Informacyjne |
| 29621 | ApplicationUpgradeStart | CM | Informacyjne |
| 29622 | ApplicationUpgradeComplete | CM | Informacyjne |
| 29623 | ApplicationUpgradeRollback | CM | Informacyjne |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informacyjne |
| 29625 | ApplicationDeleted | CM | Informacyjne |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informacyjne |
| 18566 | ServiceCreated | FM | Informacyjne |
| 18567 | ServiceDeleted | FM | Informacyjne |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat ogólnych [generowania zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w sieci szkieletowej usług
* Modyfikowanie użytkownika [diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md) konfigurację, aby zbierać więcej dzienników
* [Konfigurowanie usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) wyświetlić sieci działa kanału dzienniki
