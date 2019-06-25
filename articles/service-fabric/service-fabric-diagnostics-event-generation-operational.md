---
title: Lista zdarzeń w sieci szkieletowej usługi platformy Azure | Dokumentacja firmy Microsoft
description: Pełną listę zdarzeń udostępnianych przez usługi Azure Service Fabric, aby ułatwić monitorowanie klastrów.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde0464985f756132c60453c4e79ffefd4a1dd2c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "65788594"
---
# <a name="list-of-service-fabric-events"></a>Lista zdarzeń usługi Service Fabric 

Usługa Service Fabric udostępnia zestaw podstawowy zdarzenia klastra informujące o stanie klastra jako [usługi Service Fabric zdarzenia](service-fabric-diagnostics-events.md). Te są oparte na akcje wykonane przez usługę Service Fabric na węzły klastra lub przez klaster właściciela / — operator decyzji związanych z zarządzaniem. Te zdarzenia są dostępne przez skonfigurowanie na wiele sposobów, w tym konfigurowanie [usługi Azure Monitor dzienników na potrzeby klastra](service-fabric-diagnostics-oms-setup.md), lub przeszukując [bazy danych EventStore](service-fabric-diagnostics-eventstore.md). Na maszynach Windows te zdarzenia są podawane w dzienniku zdarzeń — tak aby było widać usługi Service Fabric zdarzeniach w Podglądzie zdarzeń. 

Poniżej przedstawiono niektóre cechy te zdarzenia
* Każde zdarzenie jest powiązany z określonej jednostki w klastrze np. aplikacji, usługi, Node, repliki.
* Każde zdarzenie zawiera zestaw typowych pól: EventInstanceId EventName i kategorii.
* Każde zdarzenie zawiera pola, które powiązać zdarzenia do jednostki, który jest skojarzony. Na przykład zdarzenie ApplicationCreated musi pola, które określają nazwę aplikacji, utworzonej.
* Zdarzenia są skonstruowane w taki sposób, że mogą być używane w wielu różnych narzędzi w celu dalszej analizy. Ponadto istotne szczegóły dotyczące zdarzenia są definiowane jako osobne właściwości, w przeciwieństwie do długi ciąg. 
* Zdarzenia są zapisywane przez różne podsystemy w usłudze Service Fabric są identyfikowane za pomocą Source(Task) poniżej. Więcej informacji znajduje się w tych podsystemów w [Architektura usługi Service Fabric](service-fabric-architecture.md) i [omówienie techniczne usługi Service Fabric](service-fabric-technical-overview.md).

Oto lista tych zdarzeń usługi Service Fabric, uporządkowane według jednostki.

## <a name="cluster-events"></a>Zdarzenia klastra

**Zdarzenia uaktualniania klastra**

Szczegółowe informacje na temat uaktualniania klastra można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md).

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Uaktualnienie | Rozpoczęto Uaktualnianie klastra | CM | Informacyjne |
| 29628 | ClusterUpgradeCompleted | Uaktualnienie | Ukończono uaktualnianie klastra | CM | Informacyjne | 
| 29629 | ClusterUpgradeRollbackStarted | Uaktualnienie | Rozpoczęto Uaktualnianie klastra można wycofać  | CM | Ostrzeżenie | 
| 29630 | ClusterUpgradeRollbackCompleted | Uaktualnienie | Uaktualnianie klastra została ukończona, wycofywanie | CM | Ostrzeżenie | 
| 29631 | ClusterUpgradeDomainCompleted | Uaktualnienie | Domeny uaktualnienia zakończył uaktualnianie podczas uaktualniania klastra | CM | Informacyjne | 

## <a name="node-events"></a>Węzeł zdarzenia

**Zdarzenia cyklu życia węzła** 

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Przejścia stanu | Dezaktywacja węzła zostało zakończone. | FM | Informacyjne | 
| 18603 | NodeUp | Przejścia stanu | Klastra wykrył, że uruchomieniu węzła | FM | Informacyjne | 
| 18604 | NodeDown | Przejścia stanu | Klastra wykrył, że węzeł został zamknięty. Podczas ponownego uruchomienia węzła zobaczysz zdarzenia NodeDown, następuje zdarzeń NodeUp |  FM | Błąd | 
| 18605 | NodeAddedToCluster | Przejścia stanu |  Nowy węzeł został dodany do klastra i usługi Service Fabric można wdrażać aplikacje dla tego węzła | FM | Informacyjne | 
| 18606 | NodeRemovedFromCluster | Przejścia stanu |  Węzeł został usunięty z klastra. Usługa Service Fabric wdroży już aplikacje do tego węzła | FM | Informacyjne | 
| 18607 | NodeDeactivateStarted | Przejścia stanu |  Dezaktywacja węzła została uruchomiona. | FM | Informacyjne | 
| 25621 | NodeOpenSucceeded | Przejścia stanu |  Węzeł został uruchomiony pomyślnie | FabricNode | Informacyjne | 
| 25622 | NodeOpenFailed | Przejścia stanu |  Węzeł nie udało się przyłączeniu pierścienia | FabricNode | Błąd | 
| 25624 | NodeClosed | Przejścia stanu |  Węzeł pomyślnie wyłączone | FabricNode | Informacyjne | 
| 25626 | NodeAborted | Przejścia stanu |  Węzeł ungracefully został zamknięty | FabricNode | Błąd | 

## <a name="application-events"></a>Zdarzenia aplikacji

**Zdarzenia cyklu życia aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Cykl życia | Utworzono nową aplikację | CM | Informacyjne | 
| 29625 | ApplicationDeleted | Cykl życia | Istniejąca aplikacja została usunięta. | CM | Informacyjne | 
| 23083 | ApplicationProcessExited | Cykl życia | Proces w ramach aplikacji został zakończony | Hosting | Informacyjne | 

**Zdarzenia uaktualnienia aplikacji**

Szczegółowe informacje na temat uaktualniania aplikacji można znaleźć [tutaj](service-fabric-application-upgrade.md).

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Uaktualnienie | Uaktualnienie aplikacji została uruchomiona. | CM | Informacyjne | 
| 29622 | ApplicationUpgradeCompleted | Uaktualnienie | Ukończono uaktualnianie aplikacji | CM | Informacyjne | 
| 29623 | ApplicationUpgradeRollbackStarted | Uaktualnienie | Uaktualnienie aplikacji rozpoczął wycofywania |CM | Ostrzeżenie | 
| 29624 | ApplicationUpgradeRollbackCompleted | Uaktualnienie | Uaktualnienie aplikacji została zakończona, wycofywanie | CM | Ostrzeżenie | 
| 29626 | ApplicationUpgradeDomainCompleted | Uaktualnienie | Domeny uaktualnienia zakończył uaktualnianie podczas uaktualniania aplikacji | CM | Informacyjne | 

## <a name="service-events"></a>Zdarzenia usługi

**Zdarzenia cyklu życia usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Cykl życia | Utworzono nową usługę | FM | Informacyjne | 
| 18658 | ServiceDeleted | Cykl życia | Istniejąca usługa została usunięta. | FM | Informacyjne | 

## <a name="partition-events"></a>Zdarzenia w partycji

**Zdarzenia przenoszenia partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Cykl życia | Ponowna konfiguracja partycji zostało zakończone. | RA | Informacyjne | 

## <a name="replica-events"></a>Zdarzenia repliki

**Zdarzenia cyklu życia repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Category | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Cykl życia | Otworzył niezawodnego słownika | DistributedDictionary | Informacyjne |
| 61702 | ReliableDictionaryClosed | Cykl życia | Niezawodnego słownika został zamknięty. | DistributedDictionary | Informacyjne |
| 61703 | ReliableDictionaryCheckpointRecovered | Cykl życia | Niezawodnego słownika odzyskał jego punktu kontrolnego | DistributedDictionary | Informacyjne |
| 61704 | ReliableDictionaryCheckpointFilesSent | Cykl życia | Replika wysłał plików punktu kontrolnego niezawodnego słownika. | DistributedDictionary | Informacyjne |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Cykl życia | Replika otrzymał plików punktu kontrolnego niezawodnego słownika. | DistributedDictionary | Informacyjne |
| 61963 | ReliableQueueOpened | Cykl życia | Niezawodna kolejka został otwarty. | DistributedQueue | Informacyjne |
| 61964 | ReliableQueueClosed | Cykl życia | Niezawodna kolejka została zamknięta. | DistributedQueue | Informacyjne |
| 61965 | ReliableQueueCheckpointRecovered | Cykl życia | Niezawodna kolejka została odzyskana jego punktu kontrolnego | DistributedQueue | Informacyjne |
| 61966 | ReliableQueueCheckpointFilesSent | Cykl życia | Replika została wysłana niezawodna kolejka plików punktu kontrolnego | DistributedQueue | Informacyjne |
| 63647 | ReliableQueueCheckpointFilesReceived | Cykl życia | Replika otrzymał niezawodna kolejka plików punktu kontrolnego | DistributedQueue | Informacyjne |
| 63648 | ReliableConcurrentQueueOpened | Cykl życia | Niezawodna kolejka współbieżna został otwarty. | ReliableConcurrentQueue | Informacyjne |
| 63649 | ReliableConcurrentQueueClosed | Cykl życia | Niezawodna kolejka współbieżna zostało zamknięte. | ReliableConcurrentQueue | Informacyjne |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Cykl życia | Niezawodna kolejka współbieżna odzyskał jego punktu kontrolnego | ReliableConcurrentQueue | Informacyjne |
| 61687 | TStoreError | Niepowodzenie | Niezawodne kolekcji odebrał nieoczekiwany błąd | TStore | Błąd |
| 63831 | PrimaryFullCopyInitiated | Cykl życia | Replika podstawowa została zainicjowana pełnego kopiowania | TReplicator | Informacyjne |
| 63832 | PrimaryPartialCopyInitiated | Cykl życia | Replika podstawowa została zainicjowana częściowej kopii | TReplicator | Informacyjne |
| 16831 | BuildIdleReplicaStarted | Cykl życia | Replika podstawowa została uruchomiona, tworzenia replik w stanie bezczynności | Replikacja | Informacyjne |
| 16832 | BuildIdleReplicaCompleted | Cykl życia | Replika podstawowa została ukończona, tworzenia replik w stanie bezczynności | Replikacja | Informacyjne |
| 16833 | BuildIdleReplicaFailed | Cykl życia | Replika podstawowa nie powiodło się tworzenie replik w stanie bezczynności | Replikacja | Ostrzeżenie |
| 16834 | PrimaryReplicationQueueFull | Health | Kolejka replikacji replika podstawowa jest pełna | Replikacja | Ostrzeżenie |
| 16835 | PrimaryReplicationQueueWarning | Health | Kolejki replikacji replika podstawowa jest w pełni | Replikacja | Ostrzeżenie |
| 16836 | PrimaryReplicationQueueWarningMitigated | Health | Kolejka replikacji replika podstawowa jest OK | Replikacja | Informacyjne |
| 16837 | SecondaryReplicationQueueFull | Health | Kolejka replikacji replika pomocnicza jest pełna | Replikacja | Ostrzeżenie |
| 16838 | SecondaryReplicationQueueWarning | Health | Kolejki replikacji replika pomocnicza zbliża się pełne | Replikacja | Ostrzeżenie |
| 16839 | SecondaryReplicationQueueWarningMitigated | Health | Kolejka replikacji replika pomocnicza jest OK | Replikacja | Informacyjne |
| 16840 | PrimaryFaultedSlowSecondary | Health | Wystąpił błąd powolne repliki pomocniczej replice podstawowej | Replikacja | Ostrzeżenie |
| 16841 | ReplicatorFaulted | Health | Wystąpił błąd repliki | Replikacja | Ostrzeżenie |

## <a name="container-events"></a>Zdarzenia kontenera

**Zdarzenia cyklu życia kontenera** 

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Kontener został uruchomiony. | Hosting | Informacyjne | 1 |
| 23075 | ContainerDeactivated | Kontener został zatrzymany. | Hosting | Informacyjne | 1 |
| 23082 | ContainerExited | Kontener został zakończony — Sprawdź plik flagi UnexpectedTermination | Hosting | Informacyjne | 1 |

## <a name="health-reports"></a>Raportów o kondycji

[Model kondycji sieci szkieletowej usług](service-fabric-health-introduction.md) umożliwia ocenę kondycji funkcjonalnej, elastyczny i rozszerzalny i raportowania. Począwszy od usługi Service Fabric w wersji 6.2, dane dotyczące kondycji są zapisywane jako zdarzenia platformy do dostarczają historycznych informacji na temat kondycji. Aby niskich woluminu zdarzenia dotyczące kondycji, napiszemy są tylko następujące zdarzenia usługi Service Fabric:

* Wszystkie `Error` lub `Warning` raportów o kondycji
* `Ok` Raporty dotyczące kondycji podczas przejścia
* Gdy `Error` lub `Warning` zdarzenie kondycji wygasa. To umożliwia określenie, ile jednostki złej kondycji

**Zdarzenia raportów kondycji klastra**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Dostępny jest nowy raport kondycji klastra | HM | Informacyjne | 1 |
| 54437 | ClusterHealthReportExpired | Istniejący raport kondycji klastra utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji węzła**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Nowy raport kondycji węzeł jest dostępny | HM | Informacyjne | 1 |
| 54432 | NodeHealthReportExpired | Istniejący raport kondycji węzła utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportu kondycji aplikacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Utworzono nowy raport kondycji aplikacji. To wdrożenie aplikacji. | HM | Informacyjne | 1 |
| 54426 | DeployedApplicationNewHealthReport | Utworzono nowy raport kondycji wdrożonej aplikacji | HM | Informacyjne | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Utworzono nowy raport kondycji wdrożonej usługi | HM | Informacyjne | 1 |
| 54434 | ApplicationHealthReportExpired | Istniejący raport kondycji aplikacji utracił ważność. | HM | Informacyjne | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Istniejący raport kondycji wdrożoną aplikację utracił ważność. | HM | Informacyjne | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Istniejący raport kondycji wdrożonej usługi utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji usługi**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Utworzono nowy raport kondycji usługi | HM | Informacyjne | 1 |
| 54433 | ServiceHealthReportExpired | Wygasła istniejącego raportu kondycji usługi | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji partycji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Utworzono nowy raport kondycji partycji | HM | Informacyjne | 1 |
| 54431 | PartitionHealthReportExpired | Istniejący raport kondycji partycji utracił ważność. | HM | Informacyjne | 1 |

**Zdarzenia raportów kondycji repliki**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Został utworzony raport o kondycji stanowych replik | HM | Informacyjne | 1 |
| 54430 | StatelessInstanceNewHealthReport | Utworzono nowy raport kondycji bezstanowe wystąpienia | HM | Informacyjne | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Istniejący raport kondycji stanowych replik utracił ważność. | HM | Informacyjne | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Istniejący raport kondycji wystąpienia o bezstanowa utracił ważność. | HM | Informacyjne | 1 |

## <a name="chaos-testing-events"></a>Zdarzenia testowania chaosu 

**Zdarzenia sesji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Rozpoczęto Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50023 | ChaosStopped | Chaos z sesji testowania została zatrzymana. | Testowalności | Informacyjne | 1 |

**Zdarzenia węzła chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Węzeł ma zaplanowane ponowne uruchomienie jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50087 | ChaosNodeRestartCompleted | Węzeł zostało zakończone, ponowne uruchomienie jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

**Zdarzenia aplikacji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Podczas Chaos z sesji testowania zostało zaplanowane ponowne uruchomienie pakietu kodu | Testowalności | Informacyjne | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Ponowne uruchomienie pakietu kodu zostało zakończone podczas Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

**Zdarzenia partycji chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Partycja podstawowa zaplanowano przenieść w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Partycji dodatkowych zaplanowano przenieść w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Dogłębną analizę przenoszenia partycja podstawowa jest dostępna | Testowalności | Informacyjne | 1 |

**Zdarzenia repliki chaosu**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Ponowne uruchomienie repliki zostało zaplanowane jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Usuwanie repliki zostało zaplanowane jako część Chaos z sesji testowania | Testowalności | Informacyjne | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Usuwanie repliki została zakończona w ramach elementu Chaos z sesji testowania | Testowalności | Informacyjne | 1 |

## <a name="other-events"></a>Inne zdarzenia

**Zdarzenia korelacji**

| Identyfikator zdarzenia | Name (Nazwa) | Opis |Źródło (zadanie) | Poziom | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Wykryto korelacji | Testowalności | Informacyjne | 1 |

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

* Zapoznaj się z omówieniem programu [diagnostyki w usłudze Service Fabric](service-fabric-diagnostics-overview.md)
* Dowiedz się więcej o bazie danych EventStore w [omówienie bazy danych Eventstore usługi Service Fabric](service-fabric-diagnostics-eventstore.md)
* Modyfikowanie swoje [diagnostyki Azure](service-fabric-diagnostics-event-aggregation-wad.md) konfigurację, aby zebrać więcej dzienniki
* [Konfigurowanie usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) się z Operational channel dzienników
