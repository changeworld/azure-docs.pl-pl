---
title: Rozwiązywanie problemów z raportami kondycji systemu | Dokumentacja firmy Microsoft
description: W tym artykule opisano Raporty kondycji wysyłane przez składniki usługi Azure Service Fabric i ich użycia w celu rozwiązywania problemów z klastra lub problemy z aplikacjami
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: b190db401b8ae31582ea31cf59d30f20baccf8c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060363"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Używanie raportów kondycji systemu do rozwiązywania problemów
Składniki usługi Service Fabric platformy Azure udostępniają raportów kondycji systemu na wszystkich jednostek w klastrze, gotową do. [Magazynu kondycji](service-fabric-health-introduction.md#health-store) tworzy i usuwa jednostki na podstawie raportów systemu. On również organizuje ich w hierarchii, która przechwytuje interakcje jednostki.

> [!NOTE]
> Aby zrozumieć pojęcia związane ze zdrowiem, Dowiedz się więcej o [model kondycji usługi Service Fabric](service-fabric-health-introduction.md).
> 
> 

Raportów o kondycji systemu zapewniają widoczność flagi problemów i funkcjonalność aplikacji i klastra. Dla aplikacji i usług raportami kondycji systemu sprawdź, czy jednostki są implementowane i czy działa prawidłowo z perspektywy usługi Service Fabric. Raporty nie udostępniają żadnych monitorowanie kondycji logikę biznesową usługi lub wykrywania procesów, które nie odpowiadają. Użytkownik usługi pozwala wzbogacić dane kondycji na informacje specyficzne dla swojej logiki.

> [!NOTE]
> Wysyłany przez użytkownika watchdogs raportów o kondycji są widoczne tylko *po* składników systemu tworzenia jednostki. Po usunięciu jednostki magazynu kondycji automatycznie usuwa wszystkie raporty kondycji skojarzonych z nim. Jest taka sama wartość true w przypadku, gdy tworzone jest nowe wystąpienie jednostki. Przykładem jest, gdy tworzone jest nowe wystąpienie repliki usługi stanowej utrwalonych. Wszystkie raporty skojarzone z wystąpieniem stare są usuwane i wyczyszczone z magazynu.
> 
> 

Składnik systemu raporty są identyfikowane przez źródło, w którym rozpoczyna się od "**systemu.** " prefiks. Watchdogs nie można użyć tego samego prefiksu dla swoich źródeł, jak raporty z nieprawidłowymi parametrami są odrzucane.

Spójrzmy na niektóre raporty systemu, aby zrozumieć, co wyzwala je i Dowiedz się, jak rozwiązać potencjalne problemy, które reprezentują.

> [!NOTE]
> Usługa Service Fabric w dalszym ciągu dodawać raporty na warunkach interesujące, które zwiększają wgląd w działania wykonywane w klastrze i aplikacje. Istniejące raporty mogą być dołączane więcej szczegółów, aby ułatwić szybsze rozwiązanie problemu.
> 
> 

## <a name="cluster-system-health-reports"></a>Klaster z raportami kondycji systemu
Jednostki kondycja klastra jest tworzony automatycznie w magazynie kondycji. Jeśli wszystko będzie działać poprawnie, nie ma raportu system.

### <a name="neighborhood-loss"></a>Utratę otoczenia
**System.Federation** zgłasza błąd, jeśli wykryje utratę otoczenia. Raport jest z poszczególnych węzłów, a identyfikator węzła jest uwzględniony w nazwie właściwości. Utracony otoczenie jednego pierścienia całej usługi Service Fabric zazwyczaj można spodziewać się dwa zdarzenia, które reprezentują obie strony raportu luki. W przypadku utraty więcej kluby istnieją więcej zdarzeń.

Raport określa limit czasu globalnego dzierżawy jako czas wygaśnięcia (TTL). Raport jest wysyłane ponownie co pół czas TTL, tak długo, jak warunek pozostaje aktywna. Zdarzenie jest automatycznie usuwany po jego wygaśnięciu. Zachowanie Remove gdy wygasł gwarantuje, czy raport jest czyszczony z magazynu kondycji poprawnie, nawet jeśli węzeł raportowania nie działa.

* **SourceId**: System.Federation
* **Właściwość**: Rozpoczyna się od **otoczenie** i zawiera informacje o węźle.
* **Następne kroki**: Należy zbadać, dlaczego klubu zostaną utracone. Na przykład sprawdzić komunikacji między węzłami klastra.

### <a name="rebuild"></a>Ponowne kompilowanie

Usługa Menedżer trybu Failover (FM) zarządza informacjami o węzłach klastra. Gdy FM traci swoje dane i przechodzi do utraty danych, nie może zagwarantować, że ma najnowsze informacje o węzłach klastra. W takim przypadku system przechodzi przez ponowną kompilację i System.FM zbiera dane z wszystkich węzłów w klastrze, aby ponownie skompilować jego stan. Czasami z powodu sieci lub problemy z węzła ponownej kompilacji można uzyskać zatrzymane lub wstrzymane. Taka sama sytuacja może wystąpić w usłudze główny menedżer trybu Failover (FMM). FMM jest usługa bezstanowa systemowa, która śledzi gdzie wszystkie FMs znajdują się w klastrze. Podstawowy FMM jest zawsze węzła o identyfikatorze najbardziej zbliżony do 0. Jeśli pobiera tego węzła, ponownej kompilacji, zostanie wywołany.
Jedną z powyższych warunków sytuacji **System.FM** lub **System.FMM** oznacza go za pomocą raportu o błędach. Ponowna kompilacja mogła zostać zablokowana na jeden z dwóch etapów:

* **Oczekiwanie na emisji**: FM/FMM czeka na odpowiedź emisji wiadomości z innych węzłów.

  * **Następne kroki**: Sprawdź, czy występuje problem z połączeniem sieciowym między węzłami.
* **Oczekiwanie na węzły**: FM/FMM już Odebrano odpowiedź emisji z innych węzłów i czeka na odpowiedź od określonych węzłów. Raport o kondycji zawiera listę węzłów, dla których FM/FMM Trwa oczekiwanie na odpowiedź.
   * **Następne kroki**: Sprawdź połączenie sieciowe między FM/FMM i listy węzłów. Zbadaj każdy węzeł wymienionych w przypadku pozostałych możliwych problemów.

* **SourceID**: System.FM lub System.FMM
* **Właściwość**: Ponowna kompilacja.
* **Następne kroki**: Sprawdź połączenie sieciowe między węzłami, a także stan określonych węzłów, które są wymienione na opis raport o kondycji.

### <a name="seed-node-status"></a>Stan węzła inicjatora
**System.FM** zgłosi Ostrzeżenie poziomu klastra, jeśli niektóre węzły obiektów początkowych w złej kondycji. Węzły obiektów początkowych są węzły, które zachować dostępność klastra źródłowego. Te węzły pomóc upewnić się, że klaster będzie pozostawał się poprzez ustanowienie dzierżawy z innymi węzłami i służy jako tiebreakers podczas niektórych rodzajów błędów sieci. Jeśli większość węzły obiektów początkowych nie działają w klastrze, a ich on przełączony z powrotem, klaster zostaje automatycznie zamknięty. 

Węzeł inicjujący jest w złej kondycji, gdy jego stan węzła nie działa, usunięte lub nieznany.
Ostrzeżenie raport stan węzła inicjatora spowoduje wyświetlenie listy wszystkich węzłów inicjatora w złej kondycji wraz ze szczegółowymi informacjami.

* **SourceID**: System.FM
* **Właściwość**: SeedNodeStatus
* **Następne kroki**: Jeśli to ostrzeżenie jest wyświetlane w klastrze, wykonaj poniższe instrukcje, aby rozwiązać ten problem: Dla klastra z systemem usługi Service Fabric w wersji 6.5 lub nowszej: Dla klastra usługi Service Fabric na platformie Azure po awarii węzła inicjatora, usługi Service Fabric spróbuje automatycznie zmienić do węzła innego niż inicjatora. Aby to zrobić, upewnij się, że liczba węzłów innych niż inicjatora w typie podstawowym węzłem jest większa lub równa liczbie w dół węzły obiektów początkowych. Jeśli to konieczne, należy dodać więcej węzłów do typu węzła podstawowego, aby to osiągnąć.
W zależności od stanu klastra może upłynąć trochę czasu, aby rozwiązać ten problem. Po zakończeniu tej operacji w raporcie ostrzeżenia są automatycznie usuwane.

Dla klastra autonomicznego usługi Service Fabric wyczyść raportu ostrzeżenie wszystkie węzły inicjatora konieczne poprawi. W zależności od tego, dlaczego węzły obiektów początkowych jest w złej kondycji, należy wykonać różne akcje: Jeśli węzeł inicjator jest w dół, użytkownicy potrzebują, aby wyświetlić ten węzeł inicjatora; Jeśli węzeł inicjatora jest usunięte lub nieznany, to węzeł inicjujący [musi zostać usunięte z klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
Raport ostrzeżenia są automatycznie usuwane po poprawi wszystkie węzły obiektów początkowych.

W przypadku klastra usługi Service Fabric w wersji starszej niż 6.5: W tym przypadku ostrzeżenie raportu musi ręcznie wyczyścić. **Upewnij się, wszystkie węzły inicjatora poprawi przed wyczyszczeniem raportu użytkowników**: Jeśli węzeł inicjujący jest wyłączony, użytkownicy muszą wywołać ten węzeł inicjatora; w przypadku usunięty lub nieznany węzeł inicjujący ten węzeł inicjujący musi zostać usunięte z klastra.
Po poprawi wszystkie węzły inicjatora, użyj następującego polecenia programu PowerShell do [wyczyść raportu ostrzeżenie](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
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
**System.FabricNode** zgłosi ostrzeżenie w przypadku certyfikatów używanych przez węzeł wygasną. Występują trzy certyfikaty w każdym węźle: **Certificate_cluster**, **Certificate_server**, i **Certificate_default_client**. Jeśli czas wygaśnięcia jest co najmniej dwa tygodnie, raport stan kondycji jest OK. Jeśli czas wygaśnięcia jest w ciągu dwóch tygodni, jego typ jest ostrzeżenie. TTL te zdarzenia są nieskończone, i usuwane, gdy węzeł opuści klastra.

* **SourceId**: System.FabricNode
* **Właściwość**: Rozpoczyna się od **certyfikatu** i zawiera więcej informacji na temat typu certyfikatu.
* **Następne kroki**: Zaktualizuj certyfikaty, jeśli są one wygasną.

### <a name="load-capacity-violation"></a>Naruszenie pojemności obciążenia
Moduł równoważenia obciążenia w sieci szkieletowej usługi zgłosi ostrzeżenie, gdy wykryje naruszenie pojemności węzła.

* **SourceId**: System.PLB
* **Właściwość**: Rozpoczyna się od **pojemności**.
* **Następne kroki**: Sprawdź podane metryki i wyświetlić bieżącą pojemność w węźle.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Węzeł pojemności niezgodność metryki nadzoru zasobów
System.Hosting raporty, ostrzeżenie, jeśli określony węzeł pojemności w manifeście klastra są większe niż rzeczywista wydajność węzła dla metryki nadzoru zasobów (liczba rdzeni procesora CPU i pamięci). Raport o kondycji jest wyświetlany, gdy pierwszy pakiet usługi, który używa [nadzór nad zasobami](service-fabric-resource-governance.md) rejestruje się w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: Ten problem może być problemem, ponieważ pakiety zarządzania usługi nie są wymuszane, zgodnie z oczekiwaniami i [nadzór nad zasobami](service-fabric-resource-governance.md) nie działa prawidłowo. Zaktualizuj manifest klastra przy użyciu pojemności poprawny węzeł dla następujących metryk lub nie Podaj je i pozwolić na usługi Service Fabric automatycznie Wykryj dostępnych zasobów.

## <a name="application-system-health-reports"></a>Aplikacja raportów o kondycji systemu
System.CM, który reprezentuje usługę Menedżer klastra, jest urząd certyfikacji który zarządza informacjami o aplikacji.

### <a name="state"></a>Stan
System.CM raportów, jako OK, gdy aplikacja została utworzona lub zaktualizowana. Informuje magazynu kondycji, gdy aplikacja zostanie usunięta, dzięki czemu może ono zostać usunięte z magazynu.

* **SourceId**: System.CM
* **Właściwość**: Stan.
* **Następne kroki**: Jeśli aplikacja została utworzona lub zaktualizowana, powinien on zawierać raport o kondycji Menedżera klastra. W przeciwnym razie sprawdź stan aplikacji, wysyłając zapytanie. Na przykład użyć polecenia cmdlet programu PowerShell **Get ServiceFabricApplication - ApplicationName** *applicationName*.

W poniższym przykładzie pokazano zdarzenia stanu na **Service fabric: / WordCount** aplikacji:

```powershell
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

## <a name="service-system-health-reports"></a>Usługa raportów o kondycji systemu
System.FM, który reprezentuje usługę Menedżer trybu Failover, jest urząd certyfikacji który zarządza informacjami o usługach.

### <a name="state"></a>Stan
System.FM raporty jako OK po utworzeniu usługi. Usuwa jednostki z magazynu kondycji po usunięciu usługi.

* **SourceId**: System.FM
* **Właściwość**: Stan.

W poniższym przykładzie pokazano zdarzenia stanu usługi **Service fabric: / WordCount/WordCountWebService**:

```powershell
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
**System.PLB** zgłasza błąd, jeśli wykryje, że aktualizacja usługi jest skorelowany z innej usługi, który tworzy łańcuch koligacji. Raport jest czyszczona po pomyślnej aktualizacji.

* **SourceId**: System.PLB
* **Właściwość**: **ServiceDescription**.
* **Następne kroki**: Sprawdź opisy skorelowany usług.

## <a name="partition-system-health-reports"></a>Partycja raportów o kondycji systemu
System.FM, który reprezentuje usługę Menedżer trybu Failover, jest urząd certyfikacji który zarządza informacjami o partycjach usługi.

### <a name="state"></a>Stan
System.FM raportów, jako OK, gdy partycja została utworzona i jest w dobrej kondycji. Usuwa jednostki z magazynu kondycji po usunięciu tej partycji.

Jeśli partycja jest poniżej liczbę replik minimalne, zgłasza błąd. Jeśli partycja nie jest poniżej liczbę replik minimalne, ale jest poniżej docelowej liczby replik, zgłosi ostrzeżenie. System.FM zgłasza błąd, jeśli partycja utraciła kworum.

Inne istotne zdarzenia obejmują ostrzeżenie podczas ponownej konfiguracji trwa dłużej, niż oczekiwano i kiedy Kompilacja trwa dłużej, niż oczekiwano. Oczekiwany czas kompilacji i ponowna konfiguracja są konfigurowalne oparte na scenariuszach usługi. Na przykład jeśli usługa ma terabajt stanu, takich jak Azure SQL Database, Kompilacja trwa dłużej niż usługi z małą ilością stanu.

* **SourceId**: System.FM
* **Właściwość**: Stan.
* **Następne kroki**: Jeśli stan kondycji nie jest OK, jest to możliwe, że niektóre repliki nie zostały utworzone, otwarte lub promowane do podstawowej lub dodatkowej poprawnie. 

Jeśli opis opisuje utraciła kworum, następnie badanie raport o kondycji szczegółowe dla replik, które nie działają lub wskazanie ich kopii zapasowej ułatwia przełączyć partycji do trybu online.

Jeśli opis w tym artykule opisano zablokowane w partycji [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md), wówczas raport o kondycji w replice podstawowej zawiera dodatkowe informacje.

W przypadku innych raportów kondycji System.FM byłoby raportów na repliki lub partycji lub usługi z innymi składnikami systemu. 

Poniższe przykłady przedstawiają niektóre z tych raportów. 

Poniższy przykład przedstawia partycji dobrej kondycji:

```powershell
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

Poniższy przykład przedstawia kondycję partycję, która jest poniżej docelowej liczba replik. Następnym krokiem jest, aby uzyskać opis partycji, który pokazuje, jak skonfigurowano: **MinReplicaSetSize** trzy i **TargetReplicaSetSize** to siedem. Następnie Pobierz liczbę węzłów w klastrze, co w tym przypadku jest pięć. Tak w tym przypadku dwie repliki nie można umieścić, ponieważ docelowa liczba replik jest większa niż liczba dostępnych węzłów.

```powershell
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

Poniższy przykład przedstawia kondycję partycji, który jest zablokowany w ponownej konfiguracji przez użytkownika nie zapewniane anulowania tokenu w **RunAsync** metody. Badanie raport o kondycji z repliką oznaczony jako podstawowy (P) może pomóc przejść dalsze analizowanie problemu.

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Raport o kondycji przedstawia stan repliki partycji w trakcie ponownej konfiguracji: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Dla każdej repliki zawiera raport o kondycji:
- Poprzedniej konfiguracji roli
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Węzeł, na którym jest uruchomiony repliki
- Identyfikator repliki:

W przypadku podobnie jak w przykładzie dalsze badanie jest wymagana. Badanie kondycji każdego poszczególnych repliki, począwszy od replik oznaczone jako `Primary` i `Secondary` (131482789658160654 i 131482789688598467) w poprzednim przykładzie.

### <a name="replica-constraint-violation"></a>Naruszenie ograniczenia repliki
**System.PLB** zgłosi ostrzeżenie, jeśli wykryje naruszenie ograniczenia repliki, a nie można umieścić wszystkie repliki partycji. Raport zawiera informacje o pokazują, których ograniczeń i właściwości zapobiec umieszczania repliki.

* **SourceId**: System.PLB
* **Właściwość**: Rozpoczyna się od **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Replika raportów o kondycji systemu
**System.RA**, która reprezentuje składnika agent rekonfiguracji, jest serwerem autorytatywnym dla stanu repliki.

### <a name="state"></a>Stan
Po utworzeniu repliki, System.RA raporty OK.

* **SourceId**: System.RA
* **Właściwość**: Stan.

Poniższy przykład przedstawia repliki w dobrej kondycji:

```powershell
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
Ta właściwość jest używana do wskazania ostrzeżenia i błędy podczas próby otwarcia repliki, zamknij repliki lub przejście repliki z jednej roli do innego. Aby uzyskać więcej informacji, zobacz [cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Awarii może być wyjątków zgłaszanych przez wywołania interfejsu API lub awarie z procesem hosta usługi w tym czasie. Na wypadek awarii ze względu na interfejs API wywołuje z C# kod, Usługa Service Fabric dodaje wyjątek i ślad stosu do raport o kondycji.

Tych ostrzeżeń są wywoływane po ponowieniu próby akcji lokalnie pewnej liczby godzin (w zależności od zasad). Usługa Service Fabric ponawia próbę akcji aż osiągnie maksymalny próg. Po osiągnięciu osiągnie maksymalny próg, może próbować działania, aby naprawić tę sytuację. Ta próba może spowodować te ostrzeżenia jako zrezygnuje akcję w tym węźle. Na przykład jeśli replika nie powiodło się otwarcie w węźle, usługi Service Fabric wywołuje ostrzeżenie kondycji. Jeśli się nie otworzy repliki, usługi Service Fabric działa na własnym naprawy. Ta akcja może obejmować próbę tej samej operacji w innym węźle. Ta próba powoduje, że ostrzeżenie zgłoszone dla tej repliki do wyczyszczenia. 

* **SourceId**: System.RA
* **Właściwość**: **ReplicaOpenStatus**, **ReplicaCloseStatus**, i **ReplicaChangeRoleStatus**.
* **Następne kroki**: Badanie kodu usługi lub zrzuty awaryjne na ustaleniu, dlaczego nie może wykonać operację.

Poniższy przykład przedstawia kondycję repliki, który zgłasza `TargetInvocationException` z jego metody open. Opis zawiera punkt awarii, **IStatefulServiceReplica.Open**, typ wyjątku **wyjątek TargetInvocationException**i ślad stosu.

```powershell
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

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Poniższy przykład przedstawia repliki, która stale uległa awarii podczas zamykania:

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Ponowne konfigurowanie
Ta właściwość jest używana do wskazania podczas przeprowadzania repliki [ponownej konfiguracji](service-fabric-concepts-reconfiguration.md) wykryje, że ponownej konfiguracji jest zablokowany lub została zablokowana. Raport o kondycji mogą znajdować się na repliki, którego bieżącej roli jest kluczem podstawowym, z wyjątkiem w przypadku wymiany podstawowej ponownej konfiguracji, gdzie znajduje się na repliki, która jest jest obniżony z podstawowej do aktywnej pomocniczej.

Ponownej konfiguracji mogą zostać zablokowane dla jednego z następujących powodów:

- Akcja w lokalnej replice tej samej repliki, wykonanie ponownej konfiguracji, nie może zostać zakończone. W tym przypadku badania raportów kondycji w tej replice od innych składników, System.RAP lub System.RE, może zawierać dodatkowe informacje.

- Akcja nie może zostać zakończone na zdalnej repliki. Repliki, dla których są oczekujące akcje wymienione w raporcie kondycji. W raportach o kondycji dla tych replik w zdalnym, powinna być podejmowana bliższe zbadanie tej sprawy. Można również problemy z komunikacją między ten węzeł i węzeł zdalny.

W rzadkich przypadkach nowej konfiguracji może zablokowane z powodu komunikacji lub innych problemów między tym węzłem i usługą Menedżer trybu Failover.

* **SourceId**: System.RA
* **Właściwość**: Ponowna konfiguracja.
* **Następne kroki**: Zbadaj replik lokalnym lub zdalnym, w zależności od opis raport o kondycji.

Poniższy przykład przedstawia raport o kondycji, w którym rekonfiguracja została zablokowana w lokalnej replice. W tym przykładzie go jest ze względu na to usługa nie zapewniane token anulowania.

```powershell
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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

W poniższym przykładzie przedstawiono raportu kondycji w przypadku, gdy zmiana konfiguracji jest zablokowany, trwa oczekiwanie na odpowiedź z dwóch replik w zdalnym. W tym przykładzie istnieją trzy repliki w partycji, w tym bieżącego podstawowego. 

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
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Raport o kondycji pokazuje, że rekonfiguracja została zablokowana, oczekiwania na odpowiedź z dwóch replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Dla każdej repliki podano następujące informacje:
- Poprzedniej konfiguracji roli
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Identyfikator węzła
- Identyfikator repliki:

Aby odblokować ponownej konfiguracji:
- **Dół** repliki powinny zostać wznowione. 
- **Stanie inbuild** repliki należy ukończyć kompilacji i przejście do gotowe.

### <a name="slow-service-api-call"></a>Powolne wywołanie interfejsu API usługi
**System.RAP** i **System.Replicator** raportu ostrzeżenie, jeśli wywołanie do kodu usługi użytkownika trwa dłużej niż skonfigurowany czas. To ostrzeżenie jest czyszczona po zakończeniu wywołanie.

* **SourceId**: System.RAP lub System.Replicator
* **Właściwość**: Nazwa powolne interfejsu API. Opis zawiera więcej szczegółów na temat interfejsu API została oczekujące czas.
* **Następne kroki**: Należy zbadać, dlaczego wywołanie trwa dłużej, niż oczekiwano.

W poniższym przykładzie pokazano zdarzenie kondycji z System.RAP niezawodne usługi, która nie jest zapewniane anulowania tokenu w **RunAsync**:

```powershell
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

Właściwości i tekstu wskazują, którego interfejs API został zablokowany. Następne kroki do wykonania dla różnych interfejsów API, zablokowane są różne. Dowolnego interfejsu API na *IStatefulServiceReplica* lub *IStatelessServiceInstance* jest zazwyczaj usterki w kodzie usługi. W poniższej sekcji opisano, jak te przełożyć na [modelu usług Reliable Services](service-fabric-reliable-services-lifecycle.md):

- **IStatefulServiceReplica.Open**: To ostrzeżenie wskazuje, że wywołanie `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, lub jeśli je przesłonięto, `OnOpenAsync` jest zablokowany.

- **IStatefulServiceReplica.Close** i **IStatefulServiceReplica.Abort**: Najbardziej często zdarza się nie zapewniane token anulowania, przekazany do usługi `RunAsync`. Być może, `ICommunicationListener.CloseAsync`, lub jeśli je przesłonięto, `OnCloseAsync` jest zablokowany.

- **IStatefulServiceReplica.ChangeRole (S)** i **IStatefulServiceReplica.ChangeRole(N)** : Najbardziej często zdarza się nie zapewniane token anulowania, przekazany do usługi `RunAsync`. W tym scenariuszu najlepszym rozwiązaniem jest ponowne uruchomienie repliki.

- **IStatefulServiceReplica.ChangeRole(P)** : Najbardziej często zdarza się, że usługi nie zwrócił zadanie z `RunAsync`.

Inne wywołania interfejsu API można utknięcie znajdują się na **IReplicator** interfejsu. Na przykład:

- **IReplicator.CatchupReplicaSet**: To ostrzeżenie wskazuje, jedna z następujących czynności. Ma za mało zapasową replik. Aby zobaczyć, jeśli jest to możliwe, sprawdź stan repliki replik partycji lub raport o kondycji System.FM dla zablokowane ponownej konfiguracji. Lub replik nie potwierdza operacji. Polecenia cmdlet programu PowerShell `Get-ServiceFabricDeployedReplicaDetail` umożliwia określenie postępu wszystkich replik. Problem jest związany z replikami którego `LastAppliedReplicationSequenceNumber` wartość znajduje się za podstawowy `CommittedSequenceNumber` wartość.

- **IReplicator.BuildReplica (\<zdalnego ReplicaId >)** : Ostrzeżenie to wskazuje na problem w procesie kompilacji. Aby uzyskać więcej informacji, zobacz [cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Może być to spowodowane błędną konfiguracją adresu replikatora. Aby uzyskać więcej informacji, zobacz [skonfigurować stanowych usług Reliable Services](service-fabric-reliable-services-configuration.md) i [określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md). Może być również problem w węźle zdalnym.

### <a name="replicator-system-health-reports"></a>Replikator raportów o kondycji systemu
**Kolejka replikacji jest pełna:** 
**System.Replicator** zgłosi ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym kolejki replikacji zwykle zapełnieniu, ponieważ co najmniej jedną replikę pomocniczą działają wolno na potwierdzenie operacji. Na serwerze pomocniczym zazwyczaj dzieje gdy usługa stanie się powoli stosują operacje. Ostrzeżenia są usuwane, gdy kolejka nie będzie już zapełniony.

* **SourceId**: System.Replicator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.
* **Następne kroki**: W przypadku raportu na serwerze podstawowym, sprawdź połączenie między węzłami w klastrze. Jeśli wszystkie połączenia są w dobrej kondycji, może to być co najmniej jeden pomocniczy wolnych z opóźnieniem dysku do zastosowania operacji. W przypadku raportu na serwerze pomocniczym, sprawdzenia użycia dysku i wydajności w węźle. Sprawdź połączenie wychodzące z powolnego węzła do podstawowej.

**RemoteReplicatorConnectionStatus:** 
**System.Replicator** w replice podstawowej zgłosi ostrzeżenie, gdy połączenie dodatkowej replikatora (zdalnym) nie jest w dobrej kondycji. Adres zdalnego replikatora jest widoczny w komunikacie raportu, co pozwala na bardziej wygodne, czy przekazano nieprawidłową konfigurację, lub jeśli występują problemy z siecią między replikatorów.

* **SourceId**: System.Replicator
* **Właściwość**: **RemoteReplicatorConnectionStatus**.
* **Następne kroki**: Sprawdź komunikat o błędzie i upewnij się, że adres ze zdalnym replikatorem to jest poprawnie skonfigurowana. Na przykład jeśli ze zdalnym replikatorem to jest otwierany przy użyciu adresu nasłuchiwania "localhost", nie jest dostępny z zewnątrz. Jeśli adres wydaje się prawidłowe, sprawdź połączenie między węzeł podstawowy i adres zdalny można znaleźć wszelkich potencjalnych problemów z siecią.

### <a name="replication-queue-full"></a>Kolejka replikacji jest pełna
**System.Replicator** zgłosi ostrzeżenie, gdy kolejka replikacji jest pełna. Na serwerze podstawowym kolejki replikacji zwykle zapełnieniu, ponieważ co najmniej jedną replikę pomocniczą działają wolno na potwierdzenie operacji. Na serwerze pomocniczym zazwyczaj dzieje gdy usługa stanie się powoli stosują operacje. Ostrzeżenia są usuwane, gdy kolejka nie będzie już zapełniony.

* **SourceId**: System.Replicator
* **Właściwość**: **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.

### <a name="slow-naming-operations"></a>Wolne operacje nazewnictwa
**System.NamingService** raportów kondycji w jego replice podstawowej, gdy operacja Naming trwa dłużej niż dopuszczalne. Nazewnictwo operacji należą do nich [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) lub [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). W obszarze FabricClient można znaleźć więcej metod. Na przykład, można go znaleźć w [metod zarządzania usługi](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) lub [metod zarządzania właściwość](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Usługa nazewnictwa jest rozpoznawany jako nazwy usług lokalizacji w klastrze. Użytkownikom można przy jego użyciu zarządzać nazwy usługi i właściwości. Jest utrwalony na partycje usługi Service Fabric. Jedna z partycji reprezentuje *Authority Owner*, który zawiera metadane dotyczące wszystkich nazw usługi Service Fabric i usługi. Nazw usługi Service Fabric są zamapowane na różne partycje, o nazwie *Name Owner* partycji, dzięki czemu usługa jest rozszerzalny. Przeczytaj więcej na temat [nazewnictwa service](service-fabric-architecture.md).
> 
> 

Podczas operacji Naming trwa dłużej, niż oczekiwano, operacja zostanie oflagowana z raportem ostrzeżenie w replice podstawowej partycji usługi nazewnictwa, która służy operacja. Jeśli operacja zakończy się pomyślnie, to ostrzeżenie jest wyczyszczone. Jeśli operacja zakończy się z powodu błędu, raport o kondycji zawiera szczegóły dotyczące błędu.

* **SourceId**: System.NamingService
* **Właściwość**: Rozpoczyna się od prefiksu "**Duration_** " i identyfikuje wolne działanie i nazwa usługi Service Fabric, na którym jest stosowany operację. Na przykład jeśli utworzyć usługę pod nazwą **Service fabric: / MyApp/Moja_usługa** trwa zbyt długo, że właściwość jest **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" wskazuje roli nazewnictwa partycji dla tej nazwy i operacji.
* **Następne kroki**: Sprawdź, dlaczego Naming kończy się niepowodzeniem. Każda operacja mogą mieć różnych przyczyn. Na przykład usługa delete mogą zostać zablokowane. Usługa mogła zostać zablokowana, ponieważ host aplikacji często występuje awaria w węźle z powodu błędu użytkownika, w kodzie usługi.

Poniższy przykład pokazuje usługi operacji tworzenia. Operacja trwała dłużej niż skonfigurowany czas trwania. "AO" ponawia próbę i wysyła pracy "No" "NO" ukończyć ostatniej operacji z przekroczeniem limitu czasu. W tym przypadku samej repliki jest kluczem podstawowym dla "AO" i "NO" ról.

```powershell
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

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication raportów o kondycji systemu
**System.Hosting** urzędu na jednostkach wdrożone.

### <a name="activation"></a>Aktywacja
System.Hosting raporty jako OK po pomyślnym aktywowaniu aplikacji w węźle. W przeciwnym razie zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: **Aktywacja**, łącznie z wersją wdrożenia.
* **Następne kroki**: Jeśli aplikacja jest w złej kondycji, należy zbadać, dlaczego aktywacja nie powiodła się.

Poniższy przykład przedstawia pomyślnej aktywacji:

```powershell
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
* **Następne kroki**: Należy zbadać, dlaczego pobieranie nie powiodło się w węźle.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage raportów o kondycji systemu
**System.Hosting** urzędu na jednostkach wdrożone.

### <a name="service-package-activation"></a>Aktywowanie pakietu usługi
System.Hosting raporty tak dobrze, jeśli aktywacji pakietu usługi w węźle zakończy się. W przeciwnym razie zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: Aktywacja.
* **Następne kroki**: Należy zbadać, dlaczego aktywacja nie powiodła się.

### <a name="code-package-activation"></a>Aktywowanie pakietu kodu
System.Hosting raporty OK dla każdego pakietu kodu Jeśli aktywacja zakończy się pomyślnie. W przypadku niepowodzenia aktywacji zgłosi ostrzeżenie zgodnie z konfiguracją. Jeśli **CodePackage** nie może aktywować lub kończy się z powodu błędu większy niż skonfigurowany **CodePackageHealthErrorThreshold**, zgłasza błąd, hostingu. Jeśli pakiet usługi zawiera wiele pakietów kodu, raport aktywacji jest generowany dla każdego z nich.

* **SourceId**: System.Hosting
* **Właściwość**: Używa prefiksu **CodePackageActivation** i zawiera nazwę pakietu kodu i punktu wejścia jako *CodePackageActivation:CodePackageName:SetupEntryPoint / punktu wejścia*. Na przykład **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Rejestracja typu usługi
System.Hosting raportów jako OK, jeśli pomyślnie zarejestrowano typ usługi. Zgłasza błąd, jeśli nie przeprowadzono rejestrację w czasie, zgodnie z konfiguracją przy użyciu **ServiceTypeRegistrationTimeout**. Jeśli środowisko wykonawcze jest zamknięte, typ usługi jest wyrejestrowywany z węzła i hosting zgłosi ostrzeżenie.

* **SourceId**: System.Hosting
* **Właściwość**: Używa prefiksu **ServiceTypeRegistration** i zawiera nazwę typu. Na przykład **ServiceTypeRegistration:FileStoreServiceType**.

Pakiet usług wdrożonych w dobrej kondycji można znaleźć w poniższym przykładzie:

```powershell
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
* **Następne kroki**: Należy zbadać, dlaczego pobieranie nie powiodło się w węźle.

### <a name="upgrade-validation"></a>Weryfikacja uaktualnienia
System.Hosting zgłasza błąd, jeśli sprawdzanie poprawności podczas uaktualniania nie powiedzie się lub Jeśli uaktualnienie nie powiedzie się w węźle.

* **SourceId**: System.Hosting
* **Właściwość**: Używa prefiksu **FabricUpgradeValidation** i zawiera uaktualnionej wersji.
* **Opis**: Wskazuje wystąpił błąd.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Pojemność niezdefiniowanego węzła dla metryki nadzoru zasobów
System.Hosting zgłosi ostrzeżenie, jeśli wydajność węzłów nie są zdefiniowane w manifeście klastra, a konfiguracja do automatycznego wykrywania jest wyłączona. Usługa Service Fabric wywołuje ostrzeżenie kondycji, w każdym przypadku, gdy pakiet usługi, który używa [nadzór nad zasobami](service-fabric-resource-governance.md) rejestruje się w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki**: Preferowanym sposobem rozwiązania tego problemu jest zmiana manifestu klastra, aby włączyć automatyczne wykrywanie dostępnych zasobów. Innym sposobem jest zaktualizować manifest klastra przy użyciu pojemności został prawidłowo określony węzeł dla tych metryk.

## <a name="next-steps"></a>Kolejne kroki
* [Wyświetlanie raportów o kondycji usługi Service Fabric](service-fabric-view-entities-aggregated-health.md)

* [Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Monitorować i diagnozować usługi lokalnie](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Uaktualnianie aplikacji usługi Service Fabric](service-fabric-application-upgrade.md)

