---
title: Rozwiązywanie problemów z raportów o kondycji systemu | Dokumentacja firmy Microsoft
description: W tym artykule opisano raportów kondycji wysyłane przez składniki sieci szkieletowej usług Azure i ich użycia dla klastra rozwiązywaniu problemów lub problemy z aplikacjami
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 8304790b5eba4679b0633641c82d57316e7f8ec4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Używanie raportów kondycji systemu do rozwiązywania problemów
Składniki sieci szkieletowej usług Azure udostępnia raporty kondycji systemu na wszystkich jednostek w klastrze dodatkowych zabiegów. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) tworzy i usuwa jednostki na podstawie raportów systemu. Również organizuje ona je w hierarchii, która przechwytuje interakcje jednostki.

> [!NOTE]
> Aby zapoznać się z kondycją pojęcia, Dowiedz się więcej na [model kondycji sieci szkieletowej usług](service-fabric-health-introduction.md).
> 
> 

Raportów o kondycji systemu zapewniają wgląd w klastrze i funkcjonalność aplikacji i flagi problemów. Dla aplikacji i usług systemowych raportów kondycji Sprawdź, czy jednostki są zaimplementowane i są działa prawidłowo z punktu widzenia sieci szkieletowej usług. Raporty nie zawierają żadnych monitorowanie kondycji logiki biznesowej usługi lub wykrywania zawieszone procesy. Usługi użytkownik może uzupełnić dane kondycji z użyciem informacji specyficznych ich logiki.

> [!NOTE]
> Wysyłany przez użytkownika watchdogs raportów o kondycji są widoczne tylko *po* składników systemu Tworzenie jednostki. Po usunięciu jednostki magazynu kondycji spowoduje automatyczne usunięcie wszystkich skojarzonych z nim raportów kondycji. Dotyczy to po utworzeniu nowego wystąpienia jednostki. Przykładem jest po utworzeniu nowego wystąpienia repliki usługi stanowej utrwalonych. Wszystkie raporty skojarzone z wystąpieniem stare usunąć i wyczyścić ze sklepu.
> 
> 

Składnik systemu raporty są identyfikowane przez źródło, w którym rozpoczyna się od "**systemu.**" prefiks. Watchdogs nie można użyć tego samego prefiksu dla ich źródła, jak raporty z nieprawidłowe parametry są odrzucane.

Oto niektóre raporty systemu, aby zrozumieć, co ich wyzwala i Dowiedz się, jak rozwiązać potencjalne problemy, które reprezentują.

> [!NOTE]
> Sieć szkieletowa usług w dalszym ciągu Dodaj raporty warunków odsetek zwiększających wgląd w działania wykonywane w klastrze i aplikacje. Istniejące raporty mogą być dołączane więcej szczegółów, aby ułatwić rozwiązanie problemu szybciej.
> 
> 

## <a name="cluster-system-health-reports"></a>Klaster systemowych raportów kondycji
Jednostki kondycji klastra jest tworzony automatycznie w magazynie kondycji. Jeśli wszystko działa prawidłowo, nie ma raportu system.

### <a name="neighborhood-loss"></a>Utrata otoczenie
**System.Federation** zgłasza błąd, jeśli wykryje utraty otoczenia. Raport jest w poszczególnych węzłach, a identyfikator węzła jest uwzględniony w nazwie właściwości. Jeden otoczenie jest zgubiony w kręgu całej sieci szkieletowej usług, zwykle można spodziewać się dwa zdarzenia, które reprezentują obie strony raportu odstępu. W przypadku utraty więcej klubów ma więcej zdarzeń.

Raport określa limit czasu globalnego dzierżawy jako czas wygaśnięcia (TTL). Raport jest ponowne wysłanie co pół czas TTL, jak długo warunek pozostaje aktywna. Zdarzenie zostanie automatycznie usunięta po jego wygaśnięciu. Ważność przypadku Usuń zachowanie gwarantuje, że raportu są czyszczone z magazynu kondycji poprawnie, nawet, jeśli węzeł raportowania jest wyłączony.

* **SourceId**: System.Federation
* **Właściwość**: rozpoczyna się od **otoczenie** i zawiera informacje na węźle.
* **Następne kroki**: Sprawdź, dlaczego otoczenie zostaną utracone. Na przykład sprawdzić komunikację między węzłami klastra.

### <a name="rebuild"></a>Ponowne kompilowanie

Usługa menedżera trybu Failover (FM) zarządza informacjami o węzłach klastra. Gdy FM utracie danych i przechodzi do utraty danych, nie może zagwarantować ma najnowszych informacji o węzłach klastra. W takim przypadku system przechodzi przez kompilowania i System.FM zbiera dane ze wszystkich węzłów w klastrze w celu odbudowania jego stanu. Czasami z powodu sieci lub zagadnienia węzła Odbuduj można uzyskać zatrzymane lub wstrzymane. Taki sam może nastąpić z usługą Failover Manager głównego (FMM). FMM to usługa systemu bezstanowych, która śledzi ścieżkę, z których wszystkie FMs w klastrze. Podstawowy FMM jest zawsze węzeł o identyfikatorze najbliżej 0. Jeśli ten węzeł zostanie porzucone, zostanie wywołany odbudowie.
W przypadku jednego z powyższych warunków **System.FM** lub **System.FMM** flagi go za pośrednictwem raportów o błędach. Ponowna kompilacja mogła zostać zablokowana na jeden z dwóch faz:

* **Oczekiwanie na emisji**: FM/FMM czeka na odpowiedź emisji komunikatów w innych węzłach.

  * **Następne kroki**: Sprawdź, czy istnieje problem z połączeniem sieci między węzłami.
* **Oczekiwanie na węzłach**: FM/FMM już odebrał odpowiedź emisji z innych węzłów i czeka na odpowiedź od określonych węzłów. Raport o kondycji zawiera listę węzłów, dla których FM/FMM czeka na odpowiedź.
   * **Następne kroki**: Sprawdź połączenie sieciowe między FM/FMM i listy węzłów. Sprawdź każdy węzeł wymienionych w przypadku innych możliwych problemów.

* **SourceID**: System.FM lub System.FMM
* **Właściwość**: Skompiluj ponownie.
* **Następne kroki**: Sprawdź połączenie sieciowe między węzły, a także stan określonych węzłów, które są wyświetlane na opis raport o kondycji.

## <a name="node-system-health-reports"></a>Węzeł systemowych raportów kondycji
System.FM, reprezentujący usługą Failover Manager service jest urzędu, który zarządza informacjami o węzłach klastra. Każdy węzeł powinien mieć jeden raport z System.FM przedstawiający jego stanu. Jednostek node zostaną usunięte po usunięciu stan węzła. Aby uzyskać więcej informacji, zobacz [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### <a name="node-updown"></a>Węzeł w górę lub w dół
System.FM raportów jako OK, gdy węzeł dołączy pierścień (jest uruchomiona). Zgłasza błąd, gdy węzeł odjazdem pierścienia (działa, albo do uaktualnienia lub po prostu ponieważ nie powiodła się). Hierarchii kondycji utworzony przez magazynu kondycji działa na jednostkach wdrożonej w korelacji z System.FM raportów węzłów. Traktuje węzła nadrzędnego wirtualnego wszystkich wdrożonych jednostek. Jednostek wdrożonych w tym węźle dostępnych za pośrednictwem zapytania, jeśli węzeł został zgłoszony jako czas przez System.FM z tego samego wystąpienia jako wystąpienie skojarzone z jednostkami. Gdy System.FM zgłasza, że węzeł jest wyłączony lub ponownie uruchomiony jako nowe wystąpienie magazynu kondycji automatycznie oczyszcza wdrożonej jednostek, które może istnieć tylko na dół węzła lub poprzednie wystąpienie węzła.

* **SourceId**: System.FM
* **Właściwość**: stan.
* **Następne kroki**: Jeśli węzeł nie działa w przypadku uaktualnienia, jego powinien przywracane jest zostaną uaktualnione. W takim przypadku stan kondycji powinna przejdź do OK. Jeśli węzeł nie wróć lub go nie powiedzie się, problem wymaga więcej dochodzenia.

W poniższym przykładzie przedstawiono System.FM zdarzenia o stanie kondycji OK dla węzła w:

```PowerShell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Wygaśnięcie certyfikatu
**System.FabricNode** zgłosi ostrzeżenie, gdy zbliża się ważności certyfikatów używanych przez węzeł. Występują trzy certyfikaty w każdym węźle: **Certificate_cluster**, **Certificate_server**, i **Certificate_default_client**. Jeśli czas wygaśnięcia jest co najmniej dwa tygodnie, stan kondycji raportu jest OK. Jeśli czas wygaśnięcia jest w ciągu dwóch tygodni, jego typ jest ostrzeżenie. TTL te zdarzenia jest nieskończone, i usuwane, gdy węzeł opuści klastra.

* **SourceId**: System.FabricNode
* **Właściwość**: rozpoczyna się od **certyfikatu** i zawiera więcej informacji na temat typu certyfikatu.
* **Następne kroki**: zaktualizować certyfikaty, jeśli są one wkrótce wygasną.

### <a name="load-capacity-violation"></a>Naruszenie pojemności obciążenia
Usługa równoważenia obciążenia sieci szkieletowej zgłosi ostrzeżenie po wykryciu naruszenie pojemności węzła.

* **SourceId**: System.PLB
* **Właściwość**: rozpoczyna się od **pojemności**.
* **Następne kroki**: Sprawdź podane metryki i wyświetlić w węźle pojemność bieżąca.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Węzeł Niezgodność pojemności dla zasobu metryki ładu
System.Hosting raporty, ostrzeżenie, jeśli zdefiniowano możliwości węzła w manifeście klastra są większe niż pojemności rzeczywistych węzła dla zasobu ładu metryki (rdzeni Procesora i pamięci). Raport o kondycji jest wyświetlany, gdy pierwszy pakiet usługi, która używa [ładu zasobów](service-fabric-resource-governance.md) rejestruje w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: ten problem może to stanowić problem, ponieważ Zarząd pakietów usługi nie są wymuszane, zgodnie z oczekiwaniami i [ładu zasobów](service-fabric-resource-governance.md) nie działa prawidłowo. Zaktualizować manifestu klastra o pojemności poprawny węzeł dla tych metryk lub nie określ je i umożliwić automatycznie Wykryj dostępne zasoby sieci szkieletowej usług.

## <a name="application-system-health-reports"></a>Aplikacja systemowych raportów kondycji
System.CM, reprezentujący usługę Menedżer klastra jest urzędu, który zarządza informacjami o aplikacji.

### <a name="state"></a>Stan
System.CM raportów, jako OK gdy aplikacji została utworzona lub zaktualizowana. Informuje magazynu kondycji po usunięciu aplikacji, dzięki czemu może być usunięty z magazynu.

* **SourceId**: System.CM
* **Właściwość**: stan.
* **Następne kroki**: Jeśli aplikacja została utworzona lub aktualizowane, powinny zawierać raport o kondycji Menedżera klastra. W przeciwnym razie sprawdź stan aplikacji, wysyłając zapytanie. Na przykład użyć polecenia cmdlet programu PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

W poniższym przykładzie przedstawiono zdarzenia stanu na **fabric: / WordCount** aplikacji:

```PowerShell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Usługa systemowych raportów kondycji
System.FM, reprezentujący usługą Failover Manager service jest urzędu, który zarządza informacjami o usługach.

### <a name="state"></a>Stan
System.FM raporty jako OK po utworzeniu usługi. Usuwa obiekt z magazynu kondycji po usunięciu usługi.

* **SourceId**: System.FM
* **Właściwość**: stan.

W poniższym przykładzie przedstawiono zdarzenia stanu usługi **fabric: / WordCount/WordCountWebService**:

```PowerShell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Błąd korelacji usługi
**System.PLB** zgłasza błąd, jeśli wykryje, czy uaktualnianie usługi są skorelowane z innej usługi, który tworzy łańcuch koligacji. Raport jest wyczyszczone po pomyślnej aktualizacji.

* **SourceId**: System.PLB
* **Właściwość**: **ServiceDescription**.
* **Następne kroki**: Sprawdź opisy skorelowane usług.

## <a name="partition-system-health-reports"></a>Partycja systemowych raportów kondycji
System.FM, reprezentujący usługą Failover Manager service jest urzędu, który zarządza informacjami o partycji usługi.

### <a name="state"></a>Stan
System.FM raportów, jako OK, gdy partycja został utworzony i działa prawidłowo. Usuwa obiekt z magazynu kondycji po usunięciu partycji.

W przypadku partycji mniej niż liczba minimalna repliki, zgłasza błąd. Jeśli partycja nie jest mniej niż liczba minimalna repliki, ale jest mniejsza od liczby replik docelowej, zgłosi ostrzeżenie. W przypadku partycji w wyniku utraty kworum, System.FM zgłasza błąd.

Inne zdarzenia zauważalne zawierać ostrzeżenie podczas ponownej konfiguracji trwa dłużej, niż oczekiwano, a jeśli Kompilacja trwa dłużej, niż oczekiwano. Przewidywany czas dla kompilacji i ponownej konfiguracji są konfigurowane na podstawie scenariuszy usługi. Na przykład jeśli usługa ma terabajt stanu, takie jak bazy danych SQL Azure, Kompilacja trwa dłużej niż usługi z małej ilości stanu.

* **SourceId**: System.FM
* **Właściwość**: stan.
* **Następne kroki**: Jeśli kondycja nie jest OK, istnieje możliwość, że niektóre repliki nie zostały utworzone, otwarte lub poziom jest podwyższany do podstawowej lub pomocniczej poprawnie. 

Jeśli opis opisuje utraty kworum, następnie badanie raport o kondycji szczegółowe replik, które nie działają lub ich przywracanie kopii zapasowej ułatwia przełączyć partycji do trybu online.

Jeśli opis opisuje partycję zatrzymane w [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md), a następnie raport o kondycji w replice podstawowej zawiera dodatkowe informacje.

W przypadku innych raportów kondycji System.FM byłoby raporty na repliki lub partycji lub usługi z innymi składnikami systemu. 

Poniższe przykłady przedstawiają niektóre z tych raportów. 

W poniższym przykładzie przedstawiono partycji dobrej kondycji:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Poniższy przykład przedstawia kondycję partycji, która znajduje się poniżej docelowej liczby replik. Następnym krokiem jest, aby uzyskać opis partycji, który przedstawia sposób skonfigurowania: **MinReplicaSetSize** trzy i **TargetReplicaSetSize** wynosi siedem. Następnie Pobierz liczbę węzłów w klastrze, w tym przypadku jest pięć. Tak w tym przypadku dwóch replik nie można umieścić, ponieważ docelowy liczba replik jest wyższa niż liczba dostępnych węzłów.

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Poniższy przykład przedstawia kondycję partycji, która jest zablokowana w ponownej konfiguracji z powodu użytkownika nie ramach anulowania tokenu w **RunAsync** metody. Badanie raport o kondycji z repliką oznaczony jako podstawowy (P) może pomóc przejść dalsze problem.

```PowerShell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Ten raport o kondycji pokazuje stan replik partycji trwa ponowna konfiguracja: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Dla każdej repliki zawiera raport o kondycji:
- Poprzedniej konfiguracji roli
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Węzeł, na którym działa repliki
- Identyfikator repliki

W przypadku tak jak w przykładzie dalszych badań jest wymagana. Badanie kondycji każdej repliki poszczególnych począwszy replik oznaczona jako `Primary` i `Secondary` (131482789658160654 i 131482789688598467) w poprzednim przykładzie.

### <a name="replica-constraint-violation"></a>Naruszenie ograniczenia repliki
**System.PLB** zgłosi ostrzeżenie, jeśli wykryje naruszenie ograniczenia repliki i nie można umieścić wszystkich replik partycji. Szczegóły raportu Pokaż których ograniczeń i właściwości zapobiec umieszczania repliki.

* **SourceId**: System.PLB
* **Właściwość**: rozpoczyna się od **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Repliki systemowych raportów kondycji
**System.RA**, reprezentuje składnika agenta rekonfiguracji, jest serwerem autorytatywnym dla stanu repliki.

### <a name="state"></a>Stan
System.RA raporty OK po utworzeniu repliki.

* **SourceId**: System.RA
* **Właściwość**: stan.

W poniższym przykładzie przedstawiono repliki dobrej kondycji:

```PowerShell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Ta właściwość służy do wskazania ostrzeżenia i błędy podczas próby otwarcia repliki, zamknij repliki lub przejście repliki z jednej roli do innego. Aby uzyskać więcej informacji, zobacz [cyklu życia repliki](service-fabric-concepts-replica-lifecycle.md). Błędy mogą być wyjątków zgłaszanych przez wywołania interfejsu API lub awarie procesu hosta usługi, w tym czasie. Na wypadek awarii ze względu na wywołania interfejsu API z kodu C# usługi Service Fabric dodaje do raport o kondycji wyjątku i ślad stosu.

Tych ostrzeżeń są wywoływane po ponowieniu próby akcji lokalnie pewną liczbę razy (w zależności od zasad). Sieć szkieletowa usług ponowi próbę akcji do maksymalnej wartości progowej. Po osiągnięciu maksymalnej wartości progowej, spróbuj do działania, aby naprawić tę sytuację. Ta próba może spowodować błąd tych ostrzeżeń, które zostanie wyczyszczony, ponieważ zrezygnuje akcji w tym węźle. Na przykład jeśli replika nie powiodło się otwarcie w węźle, usługi sieć szkieletowa zgłasza ostrzeżenie kondycji. Jeśli replika nadal nie można otworzyć, Service Fabric działa własnym naprawić. Ta akcja może obejmować w trakcie tej samej operacji w innym węźle. Ta próba powoduje, że ostrzeżenia dla tej repliki do wyczyszczenia. 

* **SourceId**: System.RA
* **Właściwość**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, i **ReplicaChangeRoleStatus**.
* **Następne kroki**: badanie zrzuty kodu lub awarii usługi do identyfikowania, dlatego nie może wykonać operacji.

Poniższy przykład przedstawia kondycję repliki wywołującej `TargetInvocationException` z jego metody open. Opis zawiera punktu awarii, **IStatefulServiceReplica.Open**, typ wyjątku **TargetInvocationException**i ślad stosu.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

W poniższym przykładzie przedstawiono stale uległa awarii podczas zamykania repliki:

```PowerShell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Ponowne konfigurowanie
Ta właściwość służy do wskazania podczas wykonywania repliki [ponowna konfiguracja](service-fabric-concepts-reconfiguration.md) wykryje, że ponownej konfiguracji jest zablokowany lub zablokowany. Raport o kondycji, może być repliki, w których bieżąca rola jest podstawowy, z wyjątkiem przypadków wymiany rekonfiguracji podstawowego, gdy jest na obniżenia z podstawowego na aktywny pomocniczej replice.

Ponownej konfiguracji może zostać zatrzymane dla jednego z następujących powodów:

- Akcja dla lokalnej repliki, replikę tego samego wykonania ponownej konfiguracji, co nie jest ukończenia. W takim przypadku badania Raporty kondycji na tej repliki z innymi składnikami, System.RAP lub System.RE, może zawierać dodatkowe informacje.

- Akcja nie kończy się na zdalnej repliki. Repliki, dla których są oczekującej akcji są wymienione w raport o kondycji. Dalsza analiza ma się odbywać na kondycję raportach tych replik zdalnego. Można również problemy z komunikacją między ten węzeł i zdalnego węzła.

W rzadkich przypadkach ponownej konfiguracji może zostać zatrzymane z powodu komunikacji lub inne problemy między ten węzeł i usługą Failover Manager service.

* **SourceId**: System.RA
* **Właściwość**: ponownej konfiguracji.
* **Następne kroki**: badanie replik lokalnych lub zdalnych, w zależności od opis raport o kondycji.

W poniższym przykładzie przedstawiono raport o kondycji, w której zmiana konfiguracji jest zablokowana dla lokalnej repliki. W tym przykładzie on się z powodu usługi nie ramach token anulowania.

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

W poniższym przykładzie przedstawiono raportu kondycji w przypadku, gdy zmiana konfiguracji zablokuje się w stanie oczekiwania na odpowiedź z dwóch replik zdalnego. W tym przykładzie istnieją trzy repliki w partycji, łącznie z bieżącej podstawowej. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: http://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Ten raport o kondycji pokazuje, że ponowną konfigurację zablokuje się w stanie oczekiwania na odpowiedź z dwóch replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Dla każdej repliki podane są następujące informacje:
- Poprzedniej konfiguracji roli
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Identyfikator węzła
- Identyfikator repliki

Aby odblokować ponownej konfiguracji:
- **Dół** repliki powinny zostać przeniesione. 
- **Stanie inbuild** repliki należy ukończyć kompilacji i przejście do gotowe.

### <a name="slow-service-api-call"></a>Wywołanie interfejsu API usługi powolne
**System.RAP** i **System.Replicator** raportować ostrzeżenie w przypadku wywołania do kodu użytkownika usługi trwa dłużej niż w skonfigurowanym czasie. To ostrzeżenie zostanie wyczyszczona po zakończeniu wywołania.

* **SourceId**: System.RAP lub System.Replicator
* **Właściwość**: Nazwa powolne interfejsu API. Opis zawiera więcej informacji na temat interfejsu API został oczekujące czas.
* **Następne kroki**: Sprawdź, dlaczego wywołanie trwa dłużej, niż oczekiwano.

W poniższym przykładzie przedstawiono zdarzenie kondycji z System.RAP niezawodnej usługi, który nie jest ramach anulowanie tokenów w **RunAsync**:

```PowerShell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Właściwości i tekst wskazują, które interfejs API został zablokowany. Następne kroki do wykonania dla różnych zablokowane interfejsy API są różne. Jakiegokolwiek interfejsu API na *IStatefulServiceReplica* lub *IStatelessServiceInstance* jest zazwyczaj na usterkę w kodzie usługi. W poniższej sekcji opisano, jak te przełożyć na [modelu niezawodne usługi](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: ostrzeżenie wskazuje, że wywołanie `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, lub jeśli została zastąpiona, `OnOpenAsync` jest zablokowana.

- **IStatefulServiceReplica.Close** i **IStatefulServiceReplica.Abort**: nie ramach token anulowania przekazanych do usługi jest najbardziej typowych przypadkach `RunAsync`. Można ją również który `ICommunicationListener.CloseAsync`, lub jeśli została zastąpiona, `OnCloseAsync` jest zablokowana.

- **IStatefulServiceReplica.ChangeRole (S)** i **IStatefulServiceReplica.ChangeRole(N)**: nie ramach token anulowania przekazanych do usługi jest najbardziej typowych przypadkach `RunAsync`.

- **IStatefulServiceReplica.ChangeRole(P)**: najbardziej często zdarza się, że usługa nie zwrócił zadania z `RunAsync`.

Inne wywołania interfejsu API, które mogą zostać zablokowane znajdują się na **IReplicator** interfejsu. Na przykład:

- **IReplicator.CatchupReplicaSet**: to ostrzeżenie oznacza jedną z następujących operacji. Brak wystarczających zapasową replik. Aby zobaczyć, jeśli jest to możliwe, sprawdź stan repliki replik partycji lub raport o kondycji System.FM zablokowane zmiany konfiguracji. Lub replik nie potwierdza operacji. Polecenia cmdlet programu PowerShell `Get-ServiceFabricDeployedReplicaDetail` może służyć do określenia postępu wszystkich replik. Problem jest związany z replikami którego `LastAppliedReplicationSequenceNumber` wartość znajduje się za serwerem podstawowym `CommittedSequenceNumber` wartość.

- **IReplicator.BuildReplica (<Remote ReplicaId>)**: ostrzeżenie wskazuje na problem w procesie kompilacji. Aby uzyskać więcej informacji, zobacz [cyklu życia repliki](service-fabric-concepts-replica-lifecycle.md). Może to wynikać z konfiguracji adresów replikatora. Aby uzyskać więcej informacji, zobacz [skonfigurować niezawodne usługi stanowej](service-fabric-reliable-services-configuration.md) i [określić zasobów w manifeście usługi](service-fabric-service-manifest-resources.md). Może to być również problem w węźle zdalnym.

### <a name="replicator-system-health-reports"></a>Replikator systemowych raportów kondycji
**Kolejka replikacji jest pełny:**
**System.Replicator** zgłosi ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym kolejki replikacji zazwyczaj zapełni, ponieważ co najmniej jeden replikach pomocniczych są przetwarzane wolno potwierdzić operacji. Na serwerze pomocniczym zwykle dzieje się tak, gdy usługa jest powolne zastosować operacji. To ostrzeżenie zostanie wyczyszczona po kolejki nie jest już pełna.

* **SourceId**: System.Replicator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**w zależności od roli repliki.
* **Następne kroki**: w przypadku raportu na serwerze podstawowym, sprawdź połączenie między węzłami w klastrze. Jeśli wszystkie połączenia są w dobrej kondycji, może to być co najmniej jednej pomocniczej powolne z opóźnieniem dysku, aby zastosować operacji. W przypadku raportu na serwerze pomocniczym, sprawdzenia użycia dysku i wydajność w węźle. Następnie zaznacz wychodzące połączenie z powolne węzła do serwera podstawowego.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** w replice podstawowej zgłosi ostrzeżenie, gdy połączenie dodatkowej replikatora (zdalnego) nie jest w dobrej kondycji. Adres zdalnego replikatora jest widoczny w komunikacie raportu, dzięki czemu wygodniej jest sprawdzane, czy przekazano nieprawidłową konfigurację, lub jeśli występują problemy z siecią między replikatorów.

* **SourceId**: System.Replicator
* **Właściwość**: **RemoteReplicatorConnectionStatus**.
* **Następne kroki**: komunikat o błędzie i upewnij się, adres zdalny replikatora jest poprawnie skonfigurowany. Na przykład po otwarciu zdalnej replikatora z adresem nasłuchiwania "localhost" nie jest dostępny z zewnątrz. Jeśli adres jest poprawny, sprawdź połączenie między węzła podstawowego i adres zdalny można znaleźć potencjalnych problemów z siecią.

### <a name="replication-queue-full"></a>Kolejka replikacji jest pełna
**System.Replicator** zgłosi ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym kolejki replikacji zazwyczaj zapełni, ponieważ co najmniej jeden replikach pomocniczych są przetwarzane wolno potwierdzić operacji. Na serwerze pomocniczym zwykle dzieje się tak, gdy usługa jest powolne zastosować operacji. To ostrzeżenie zostanie wyczyszczona po kolejki nie jest już pełna.

* **SourceId**: System.Replicator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**w zależności od roli repliki.

### <a name="slow-naming-operations"></a>Powolne operacje nazewnictwa
**System.NamingService** raportów kondycji na jego repliką podstawową, gdy operacja nazewnictwa trwa dłużej niż dopuszczalne. Przykłady operacji nazewnictwa [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) lub [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Więcej metod znajdują się w obszarze klienta fabricclient z rolą. Na przykład można go znaleźć w [usługi metod zarządzania](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) lub [metod zarządzania właściwości](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Usługa nazewnictwa rozpoznawania nazw usługi do lokalizacji w klastrze. Użytkownicy służy do zarządzania nazw usługi i właściwości. Jest podzielona na partycje utrwalony usługi sieci szkieletowej usług. Reprezentuje jedną z partycji *Authority Owner*, który zawiera metadane dotyczące wszystkich nazw usługi Service Fabric i usług. Nazwy sieci szkieletowej usług są zamapowane do różnych partycji o nazwie *właściciela* partycji, dlatego usługa jest rozszerzalny. Przeczytaj więcej na temat [Naming service](service-fabric-architecture.md).
> 
> 

Podczas operacji nazewnictwa trwa dłużej, niż oczekiwano, operacja oflagowane z raportem ostrzeżenie w replice podstawowej partycji usługi nazewnictwa, która służy operacji. Jeśli operacja zakończy się pomyślnie, ostrzeżenie jest wyczyszczone. Po zakończeniu operacji z powodu błędu, raport o kondycji zawiera szczegóły dotyczące błędu.

* **SourceId**: System.NamingService
* **Właściwość**: rozpoczyna się od prefiksu "**Duration_**" i identyfikuje wolne działanie i nazwa sieci szkieletowej usług, dla którego jest stosowana operacji. Na przykład jeśli Tworzenie usługi pod nazwą **fabric: / MyApp/Moja_usługa** trwa zbyt długo, ta właściwość jest **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" wskazuje rolą partycji nazewnictwa dla tej nazwy i operację.
* **Następne kroki**: Sprawdź, dlaczego nazewnictwa kończy się niepowodzeniem. Każda operacja mogą mieć różnych przyczyn. Na przykład usługi delete mogła zostać zablokowana. Usługa mogła zostać zablokowana, ponieważ host aplikacji przechowuje awarii w węźle z powodu błędu użytkownika w kodzie usługi.

Poniższy przykład przedstawia tworzenie operacji usługi. Operacja trwało dłużej niż skonfigurowany czas trwania. "AO" ponawia próbę i wysyła pracy do "No". "Nie" zakończona z limitem czasu ostatniej operacji. W takim przypadku samej repliki jest kluczem podstawowym "AO" i "NO" ról.

```PowerShell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication systemowych raportów kondycji
**System.Hosting** urzędu na wdrożonym jednostek.

### <a name="activation"></a>Uaktywnienie
System.Hosting raportów, jako OK gdy aplikacji został pomyślnie uaktywniony na węźle. W przeciwnym razie go zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: **aktywacji**, łącznie z wersją wdrożenia.
* **Następne kroki**: Jeśli aplikacja jest zła, zbadać, dlaczego aktywacja nie powiodła się.

W poniższym przykładzie przedstawiono pomyślnej aktywacji:

```PowerShell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Do pobrania
System.Hosting zgłasza błąd, jeśli pobieranie pakietu aplikacji nie powiedzie się.

* **SourceId**: System.Hosting
* **Właściwość**: **Pobierz**, łącznie z wersją wdrożenia.
* **Następne kroki**: Sprawdź, dlaczego pobieranie nie powiodło się w węźle.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage systemowych raportów kondycji
**System.Hosting** urzędu na wdrożonym jednostek.

### <a name="service-package-activation"></a>Aktywowanie pakietu usługi
System.Hosting raporty jako OK, jeżeli usługa aktywacji pakietu w węźle zakończy się pomyślnie. W przeciwnym razie go zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: aktywacji.
* **Następne kroki**: Sprawdź, dlaczego aktywacja nie powiodła się.

### <a name="code-package-activation"></a>Aktywowanie pakietu kodu
System.Hosting raporty jako OK dla każdego pakietu kodu Jeśli aktywacja zakończy się pomyślnie. W przypadku niepowodzenia aktywacji zgłosi ostrzeżenie zgodnie z konfiguracją. Jeśli **elementu CodePackage** nie może aktywować lub kończy się z powodu błędu większy niż skonfigurowany **CodePackageHealthErrorThreshold**, hosting zgłasza błąd. Jeśli pakiet usługi zawiera wiele pakietów kodu, aktywacji raport jest generowany dla każdego z nich.

* **SourceId**: System.Hosting
* **Właściwość**: używa prefiksu **CodePackageActivation** i zawiera nazwę pakietu kodu i punktu wejścia jako *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*. Na przykład **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Rejestracja typu usługi
Jako OK System.Hosting raportów, jeśli typ usługi został pomyślnie zarejestrowany. Zgłasza błąd, jeśli nie przeprowadzono rejestracji w czasie, zgodnie z konfiguracją przy użyciu **ServiceTypeRegistrationTimeout**. Jeśli środowisko uruchomieniowe jest zamknięty, typ usługi jest zarejestrowany z węzła i hosting zgłosi ostrzeżenie.

* **SourceId**: System.Hosting
* **Właściwość**: używa prefiksu **ServiceTypeRegistration** i zawiera nazwę typu usługi. Na przykład **ServiceTypeRegistration:FileStoreServiceType**.

W poniższym przykładzie przedstawiono pakietu wdrożonej usługi w dobrej kondycji:

```PowerShell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Do pobrania
System.Hosting zgłasza błąd, jeśli pobieranie pakietu usługi nie powiedzie się.

* **SourceId**: System.Hosting
* **Właściwość**: **Pobierz**, łącznie z wersją wdrożenia.
* **Następne kroki**: Sprawdź, dlaczego pobieranie nie powiodło się w węźle.

### <a name="upgrade-validation"></a>Weryfikacja uaktualnienia
System.Hosting zgłasza błąd, jeśli sprawdzanie poprawności podczas uaktualniania nie powiedzie się lub Jeśli uaktualnienie nie powiedzie się w węźle.

* **SourceId**: System.Hosting
* **Właściwość**: używa prefiksu **FabricUpgradeValidation** i zawiera uaktualnionej wersji.
* **Opis elementu**: wskazuje wystąpił błąd.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Niezdefiniowany węzła pojemności dla zasobu metryki ładu
System.Hosting zgłosi ostrzeżenie, jeśli węzeł pojemności nie są zdefiniowane w manifeście klastra i Konfiguracja wykrywania automatycznego jest wyłączona. Sieć szkieletowa usług zgłasza kondycji ostrzeżenie, gdy pakiet usługi, która używa [ładu zasobów](service-fabric-resource-governance.md) rejestruje w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: jest preferowany sposób, aby rozwiązać ten problem, aby zmienić manifest klastra, aby umożliwić automatyczne wykrywanie dostępnych zasobów. Innym sposobem jest można zaktualizować manifestu klastra o pojemności prawidłowo określony węzeł dla tych metryk.

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetl raporty dotyczące kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

* [Jak zgłosić i Sprawdź kondycję usług](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorowanie i diagnozowania usług lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Uaktualnianie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

