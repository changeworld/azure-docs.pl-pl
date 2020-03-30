---
title: Lista zdarzeń sieci szkieletowej usługi Azure
description: Pełna lista zdarzeń dostarczonych przez usługę Azure Service Fabric w celu monitorowania klastrów.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258540"
---
# <a name="list-of-service-fabric-events"></a>Lista zdarzeń sieci szkieletowej usług 

Sieci szkieletowej usług udostępnia podstawowy zestaw zdarzeń klastra, aby poinformować o stanie klastra jako [zdarzenia sieci szkieletowej usług.](service-fabric-diagnostics-events.md) Są one oparte na akcjach wykonywanych przez sieci szkieletowej usług w węzłach i klastra lub decyzji dotyczących zarządzania podejmowanych przez właściciela/operatora klastra. Dostęp do tych zdarzeń można uzyskać, konfigurując je na wiele sposobów, w tym konfigurując [dzienniki usługi Azure Monitor za pomocą klastra](service-fabric-diagnostics-oms-setup.md)lub przeprowadzając kwerendę w [sklepie EventStore.](service-fabric-diagnostics-eventstore.md) Na komputerach z systemem Windows te zdarzenia są podawane do EventLog — dzięki czemu można zobaczyć zdarzenia sieci szkieletowej usług w Podglądzie zdarzeń. 

Oto niektóre cechy tych zdarzeń
* Każde zdarzenie jest powiązane z określoną jednostką w klastrze, np.
* Każde zdarzenie zawiera zestaw typowych pól: EventInstanceId, EventName i Category.
* Każde zdarzenie zawiera pola, które wiążą zdarzenie z jednostką, z którą jest skojarzone. Na przykład ApplicationCreated zdarzenie będzie miało pola, które identyfikują nazwę aplikacji utworzone.
* Zdarzenia są skonstruowane w taki sposób, że mogą być zużywane w różnych narzędziach do dalszej analizy. Ponadto odpowiednie szczegóły dla zdarzenia są zdefiniowane jako oddzielne właściwości, w przeciwieństwie do długiego String. 
* Zdarzenia są zapisywane przez różne podsystemy w sieci szkieletowej usług są identyfikowane przez Source(Task) poniżej. Więcej informacji można znaleźć na temat tych podsystemów w przeglądzie technicznym [architektury sieci szkieletowej](service-fabric-architecture.md) usług i [sieci szkieletowej usług.](service-fabric-technical-overview.md)

Oto lista tych zdarzeń sieci szkieletowej usług organizowanych według encji.

## <a name="cluster-events"></a>Zdarzenia klastra

**Zdarzenia uaktualniania klastra**

Więcej informacji na temat uaktualnień klastra można znaleźć [tutaj](service-fabric-cluster-upgrade-windows-server.md).

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | Rozpoczęcie aktualizacji klastra | Uaktualnienie | Rozpoczęto uaktualnianie klastra | CM | Informacyjne |
| 29628 | ClusterUpgradeUpełnione | Uaktualnienie | Uaktualnienie klastra zostało zakończone | CM | Informacyjne | 
| 29629 | ClusterUpgradeRollbackStarted | Uaktualnienie | Uaktualnienie klastra rozpoczęło wycofywanie  | CM | Ostrzeżenie | 
| 29630 | ClusterUpgradeRollbackUpełnione | Uaktualnienie | Uaktualnienie klastra zostało zakończone wycofywaniem | CM | Ostrzeżenie | 
| 29631 | ClusterUpgradeDomainCompleted | Uaktualnienie | Domena uaktualnienia zakończyła uaktualnianie podczas uaktualniania klastra | CM | Informacyjne | 

## <a name="node-events"></a>Zdarzenia węzła

**Zdarzenia cyklu życia węzła** 

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- | 
| 18602 | Moduł DeaktywowanyZakończony | StateTransition (Transport stanu) | Dezaktywacja węzła została zakończona | FM | Informacyjne | 
| 18603 | WęzełUp | StateTransition (Transport stanu) | Klaster wykrył uruchomiono węzeł | FM | Informacyjne | 
| 18604 | Dojście do węzła | StateTransition (Transport stanu) | Klaster wykrył zamknięty węzeł. Podczas ponownego uruchamiania węzła zostanie wyświetlona zdarzenie NodeDown, po którym następuje zdarzenie NodeUp |  FM | Błąd | 
| 18605 | NodeAddedToCluster | StateTransition (Transport stanu) |  Nowy węzeł został dodany do klastra, a sieci szkieletowej usług można wdrożyć aplikacje do tego węzła | FM | Informacyjne | 
| 18606 | NodeRemovedFromCluster | StateTransition (Transport stanu) |  Węzeł został usunięty z klastra. Sieci szkieletowej usług nie będzie już wdrażać aplikacji w tym węźle | FM | Informacyjne | 
| 18607 | Rozpoczęcie deaktywacji węzła | StateTransition (Transport stanu) |  Rozpoczęto dezaktywację węzła | FM | Informacyjne | 
| 25621 | NodeOpenSucceeded | StateTransition (Transport stanu) |  Węzeł został pomyślnie uruchomiony | TkaninaNoda | Informacyjne | 
| 25622 | NodeOpenFailed (WęzełOpenFailed) | StateTransition (Transport stanu) |  Nie można uruchomić węzła i dołączyć do pierścienia | TkaninaNoda | Błąd | 
| 25624 | NodeClosed (Zamknięte węzły) | StateTransition (Transport stanu) |  Węzeł został pomyślnie zamknięty | TkaninaNoda | Informacyjne | 
| 25626 | WęzełBorted | StateTransition (Transport stanu) |  Węzeł został niewdzięcznie zamknięty | TkaninaNoda | Błąd | 

## <a name="application-events"></a>Zdarzenia aplikacji

**Zdarzenia cyklu życia aplikacji**

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | AplikacjaTworzona | Cyklem życia | Utworzono nową aplikację | CM | Informacyjne | 
| 29625 | AplikacjaDeleted | Cyklem życia | Istniejąca aplikacja została usunięta | CM | Informacyjne | 
| 23083 | AplikacjaProcessExited | Cyklem życia | Proces w aplikacji został zakończyny | Hosting | Informacyjne | 

**Zdarzenia uaktualniania aplikacji**

Więcej informacji na temat uaktualnień aplikacji można znaleźć [tutaj](service-fabric-application-upgrade.md).

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | Rozpoczęcie aktualizacji aplikacji | Uaktualnienie | Rozpoczęto uaktualnienie aplikacji | CM | Informacyjne | 
| 29622 | ApplicationUpgradeUpełnione | Uaktualnienie | Uaktualnienie aplikacji zostało zakończone | CM | Informacyjne | 
| 29623 | ApplicationUpgradeRollbackStarted | Uaktualnienie | Uaktualnienie aplikacji rozpoczęło wycofywanie |CM | Ostrzeżenie | 
| 29624 | ApplicationUpgradeRollbackUpełnione | Uaktualnienie | Uaktualnienie aplikacji zakończyło wycofywanie | CM | Ostrzeżenie | 
| 29626 | ApplicationUpgradeDomainCompleted | Uaktualnienie | Domena uaktualnienia zakończyła uaktualnianie podczas uaktualniania aplikacji | CM | Informacyjne | 

## <a name="service-events"></a>Zdarzenia serwisowe

**Zdarzenia cyklu życia serwisu**

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18657 | UsługaTworzona | Cyklem życia | Utworzono nową usługę | FM | Informacyjne | 
| 18658 | UsługaDeletowana | Cyklem życia | Istniejąca usługa została usunięta | FM | Informacyjne | 

## <a name="partition-events"></a>Zdarzenia partycji

**Zdarzenia przenoszenia partycji**

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom | 
| --- | --- | ---| --- | --- | --- |
| 18940 | Konfiguracja partycji | Cyklem życia | Zakończono ponowną konfigurację partycji | RA | Informacyjne | 

## <a name="replica-events"></a>Zdarzenia repliki

**Zdarzenia cyklu życia repliki**

| Eventid | Nazwa | Kategoria | Opis |Źródło (zadanie) | Poziom |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOted | Cyklem życia | Niezawodny słownik został otwarty | Dyktatysarz | Informacyjne |
| 61702 | ReliableDictionary Zamknięte | Cyklem życia | Niezawodny słownik został zamknięty | Dyktatysarz | Informacyjne |
| 61703 | ReliableDictionaryCheckpointZrecuacyjny | Cyklem życia | Niezawodny słownik odzyskał swój punkt kontrolny | Dyktatysarz | Informacyjne |
| 61704 | ReliableDictionaryCheckpointFilesSent | Cyklem życia | Replika wysłała pliki punktów kontrolnych niezawodnego słownika | Dyktatysarz | Informacyjne |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Cyklem życia | Replika otrzymała pliki punktów kontrolnych niezawodnego słownika | Dyktatysarz | Informacyjne |
| 61963 | ReliableQueueOpened | Cyklem życia | Niezawodna kolejka została otwarta | Rozproszona kolejka | Informacyjne |
| 61964 | ReliableQueueClosed (Zamknięte w kolejce) | Cyklem życia | Niezawodna kolejka została zamknięta | Rozproszona kolejka | Informacyjne |
| 61965 | ReliableQueueCheckpointRecovered (Program ReliableQueueCheckpointRecovered) | Cyklem życia | Niezawodna kolejka odzyskała swój punkt kontrolny | Rozproszona kolejka | Informacyjne |
| 61966 | ReliableQueueCheckpointFilesSent | Cyklem życia | Replika wysłała pliki punktu kontrolnego niezawodnej kolejki | Rozproszona kolejka | Informacyjne |
| 63647 | ReliableQueueCheckpointFilesReceived | Cyklem życia | Replika odebrała niezawodne pliki punktu kontrolnego kolejki | Rozproszona kolejka | Informacyjne |
| 63648 | ReliableConcurrentQueueOpened | Cyklem życia | Niezawodna kolejka współbieżna została otwarta | ReliableConcurrentQueue | Informacyjne |
| 63649 | ReliableConcurrentQueueClosed | Cyklem życia | Niezawodna kolejka współbieżna została zamknięta | ReliableConcurrentQueue | Informacyjne |
| 63650 | ReliableConcurrentQueueCheckpointNamniejobszczony | Cyklem życia | Niezawodna kolejka równoczesnych odzyskała swój punkt kontrolny | ReliableConcurrentQueue | Informacyjne |
| 61687 | TStoreError ( TStoreError ) | Niepowodzenie | Niezawodna kolekcja otrzymała nieoczekiwany błąd | Sklep tstore | Błąd |
| 63831 | PrimaryFullCopyInitiated | Cyklem życia | Replika podstawowa zainicjowała pełną kopię | TReplicator ( | Informacyjne |
| 63832 | PrimaryPartialCopyInitiated | Cyklem życia | Replika podstawowa zainicjowała kopię częściową | TReplicator ( | Informacyjne |
| 16831 | BuildIdleReplicaStarted | Cyklem życia | Replika podstawowa rozpoczęła tworzenie repliki bezczynnej | Replikacja | Informacyjne |
| 16832 | BuildIdleReplicaPokończony | Cyklem życia | Replika podstawowa zakończyła tworzenie repliki bezczynnej | Replikacja | Informacyjne |
| 16833 | BuildIdleReplicaFailed | Cyklem życia | Replika podstawowa nie powiodła się podczas tworzenia repliki bezczynnej budynku | Replikacja | Ostrzeżenie |
| 16834 | PrimaryReplicationQueueFull | Health | Kolejka replikacji repliki podstawowej jest pełna | Replikacja | Ostrzeżenie |
| 16835 | PrimaryReplicationQueueWarning (Ponowne rozliczenie podstawowego) | Health | Kolejka replikacji repliki podstawowej jest prawie pełna | Replikacja | Ostrzeżenie |
| 16836 | PrimaryReplicationQueueWarningZlecił | Health | Kolejka replikacji repliki podstawowej jest w porządku | Replikacja | Informacyjne |
| 16837 | DodatkowarelikacjaQueueFull | Health | Kolejka replikacji repliki pomocniczej jest pełna | Replikacja | Ostrzeżenie |
| 16838 | WtórnerelikacjaQueueWarning | Health | Kolejka replikacji repliki pomocniczej jest prawie pełna | Replikacja | Ostrzeżenie |
| 16839 | SecondaryReplicationQueueWarningZlecił | Health | Kolejka replikacji repliki pomocniczej jest w porządku | Replikacja | Informacyjne |
| 16840 | PodstawowyfaultedSlowSecondary | Health | Replika podstawowa ma uszkodzoną powolną replikę pomocniczą | Replikacja | Ostrzeżenie |
| 16841 | ReplicatorFaulted | Health | Replika ma uszkodzony | Replikacja | Ostrzeżenie |

## <a name="container-events"></a>Zdarzenia kontenera

**Zdarzenia cyklu życia kontenera** 

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 23074 | Kontener aktywowany | Kontener został uruchomiony | Hosting | Informacyjne | 1 |
| 23075 | KontenerZaaktywowany | Kontener został zatrzymany | Hosting | Informacyjne | 1 |
| 23082 | KontenerWydajony | Kontener został wyjęty - Sprawdź flagę UnexpectedTermination | Hosting | Informacyjne | 1 |

## <a name="health-reports"></a>Raporty o stanie zdrowia

[Model kondycji sieci szkieletowej usług](service-fabric-health-introduction.md) zapewnia bogatą, elastyczną i rozszerzalną ocenę kondycji i raportowanie. Uruchamianie sieci szkieletowej usług w wersji 6.2, dane kondycji są zapisywane jako zdarzenia platformy w celu zapewnienia historycznych rekordów kondycji. Aby utrzymać niską liczbę zdarzeń kondycji, piszemy tylko następujące zdarzenia jako zdarzenia sieci szkieletowej usług:

* Wszystkie `Error` `Warning` raporty dotyczące zdrowia
* `Ok`raporty o kondycji podczas przejść
* Po `Error` wygaśnięciu zdarzenia lub `Warning` kondycji. Może to służyć do określenia, jak długo jednostka była w złej kondycji

**Zdarzenia raportu kondycji klastra**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | --- | --- | --- | --- |
| 54428 | Raport clusternewhealthreport | Dostępny jest nowy raport kondycji klastra | Hm | Informacyjne | 1 |
| 54437 | Raport ClusterHealthReportExpired | Wygasł istniejący raport kondycji klastra | Hm | Informacyjne | 1 |

**Zdarzenia raportu o kondycji węzła**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport (Raport o stanie zdrowia) | Dostępny jest nowy raport o kondycji węzła | Hm | Informacyjne | 1 |
| 54432 | NodeHealthReportExpired (Raport o stanie zdrowia) | Wygasło istniejący raport kondycji węzła | Hm | Informacyjne | 1 |

**Zdarzenia raportu kondycji aplikacji**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54425 | Raport o stanie zdrowia aplikacji | Utworzono nowy raport kondycji aplikacji. Dotyczy to aplikacji nierozmieszczonych. | Hm | Informacyjne | 1 |
| 54426 | Wdrożony raport o stanie zdrowia | Utworzono nowy wdrożony raport kondycji aplikacji | Hm | Informacyjne | 1 |
| 54427 | Raport Wdrożony pakiet usługOwynowożerowy | Utworzono nowy wdrożony raport kondycji usługi | Hm | Informacyjne | 1 |
| 54434 | ApplicationHealthReportExpired | Wygasło istniejący raport kondycji aplikacji | Hm | Informacyjne | 1 |
| 54435 | Wdrożony Raport zdrowotny AplikacjiExpired | Wygasł istniejący wdrożony raport kondycji aplikacji | Hm | Informacyjne | 1 |
| 54436 | Wdrożony pakiet usługOwyHealthReportExpired | Wygasł istniejący wdrożony raport kondycji usługi | Hm | Informacyjne | 1 |

**Zdarzenia raportu kondycji usługi**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport (Raport o stanie zdrowia) | Utworzono nowy raport kondycji usługi | Hm | Informacyjne | 1 |
| 54433 | ServiceHealthReportExpired (ServiceHealthReportExpired) | Wygasł istniejący raport kondycji usługi | Hm | Informacyjne | 1 |

**Zdarzenia raportu kondycji partycji**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54422 | Raport partycjiNewHealthReport | Utworzono nowy raport o kondycji partycji | Hm | Informacyjne | 1 |
| 54431 | Raport partycjiHealthReportExpired | Wygasło istniejący raport kondycji partycji | Hm | Informacyjne | 1 |

**Zdarzenia raportu kondycji repliki**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Utworzono raport kondycji repliki stanowej | Hm | Informacyjne | 1 |
| 54430 | StatelessInstanceNewHealthReport | Utworzono nowy raport kondycji wystąpienia bezstanowego | Hm | Informacyjne | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Wygasł istniejący raport kondycji repliki stanowej | Hm | Informacyjne | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Wygasło istniejący raport kondycji wystąpienia bezstanowego | Hm | Informacyjne | 1 |

## <a name="chaos-testing-events"></a>Zdarzenia testujące chaos 

**Wydarzenia sesji Chaosu**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Rozpoczęła się sesja testowa Chaosu | Testowalność | Informacyjne | 1 |
| 50023 | Chaossnął | Sesja testowania chaosu została zatrzymana | Testowalność | Informacyjne | 1 |

**Zdarzenia węzła Chaos**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Węzeł ma zaplanowane ponowne uruchomienie w ramach sesji testowania chaosu | Testowalność | Informacyjne | 1 |
| 50087 | ChaosNodeRestartNieje | Węzeł zakończył ponowne uruchamianie w ramach sesji testowania chaosu | Testowalność | Informacyjne | 1 |

**Zdarzenia aplikacji Chaos**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Podczas sesji testowej chaosu zaplanowano ponowne uruchomienie pakietu kodu. | Testowalność | Informacyjne | 1 |
| 50101 | ChaosCodePackageRestartPokończony | Podczas sesji testowej chaosu ukończono ponowne uruchomienie pakietu kodu | Testowalność | Informacyjne | 1 |

**Zdarzenia partycji Chaos**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Partycja podstawowa została zaplanowana do przeniesienia w ramach sesji testowania chaosu | Testowalność | Informacyjne | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Partycja pomocnicza została zaplanowana do przeniesienia w ramach sesji testowania chaosu | Testowalność | Informacyjne | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Dostępna jest głębsza analiza przenoszenia partycji podstawowej | Testowalność | Informacyjne | 1 |

**Zdarzenia repliki chaosu**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Ponowne uruchomienie repliki zostało zaplanowane w ramach sesji testowej Chaos | Testowalność | Informacyjne | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Usunięcie repliki zostało zaplanowane w ramach sesji testowej Chaos | Testowalność | Informacyjne | 1 |
| 50093 | ChaosReplicaRemovalKoletowane | Usunięcie repliki zostało zakończone w ramach sesji testowania chaosu | Testowalność | Informacyjne | 1 |

## <a name="other-events"></a>Inne wydarzenia

**Zdarzenia korelacji**

| Eventid | Nazwa | Opis |Źródło (zadanie) | Poziom | Wersja |
| --- | --- | ---| --- | --- | --- |
| 65011 | KorelacjaOperacyjna | Wykryto korelację | Testowalność | Informacyjne | 1 |

## <a name="events-prior-to-version-62"></a>Zdarzenia sprzed wersji 6.2

Oto pełna lista zdarzeń dostarczonych przez sieci szkieletowej usług przed wersją 6.2.

| Eventid | Nazwa | Źródło (zadanie) | Poziom |
| --- | --- | --- | --- |
| 25620 | Otwieranie węzła | TkaninaNoda | Informacyjne |
| 25621 | WęzełOtwartysk sukces | TkaninaNoda | Informacyjne |
| 25622 | NodeOpenedFailed | TkaninaNoda | Informacyjne |
| 25623 | Zamknięcie węzła | TkaninaNoda | Informacyjne |
| 25624 | NodeClosed (Zamknięte węzły) | TkaninaNoda | Informacyjne |
| 25625 | WęzełBorywanie | TkaninaNoda | Informacyjne |
| 25626 | WęzełBorted | TkaninaNoda | Informacyjne |
| 29627 | Rozpoczęcie aktualizacji klastra | CM | Informacyjne |
| 29628 | ClusterUpgradeComplete (Wykań, administrator klastra) | CM | Informacyjne |
| 29629 | ClusterUpgradeRollback | CM | Informacyjne |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informacyjne |
| 29631 | ClusterUpgradeDomainComplete | CM | Informacyjne |
| 23074 | Kontener aktywowany | Hosting | Informacyjne |
| 23075 | KontenerZaaktywowany | Hosting | Informacyjne |
| 29620 | AplikacjaTworzona | CM | Informacyjne |
| 29621 | Rozpoczęcie aktualizacji aplikacji | CM | Informacyjne |
| 29622 | ZastosowanieUpgradeComplete | CM | Informacyjne |
| 29623 | ApplicationUpgradeRollback | CM | Informacyjne |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informacyjne |
| 29625 | AplikacjaDeleted | CM | Informacyjne |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informacyjne |
| 18566 | UsługaTworzona | FM | Informacyjne |
| 18567 | UsługaDeletowana | FM | Informacyjne |

## <a name="next-steps"></a>Następne kroki

* Omówienie [diagnostyki w sieci szkieletowej usług](service-fabric-diagnostics-overview.md)
* Dowiedz się więcej o spotkaniu EventStore in [Service Fabric Eventstore](service-fabric-diagnostics-eventstore.md)
* Modyfikowanie konfiguracji [diagnostyki platformy Azure](service-fabric-diagnostics-event-aggregation-wad.md) w celu zbierania większej liczby dzienników
* [Konfigurowanie usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) w celu wyświetlania dzienników kanałów operacyjnych
