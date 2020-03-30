---
title: Rozwiązywanie problemów z raportami kondycji systemu
description: Zawiera opis raportów kondycji wysyłanych przez składniki sieci szkieletowej usługi Azure i ich użycia w celu rozwiązywania problemów z klastrem lub aplikacjami
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: a76ae803b1283ce50d2f4e259943ce5ffcf0274c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282018"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Używanie raportów kondycji systemu do rozwiązywania problemów
Składniki sieci szkieletowej usługi Azure zapewniają raporty kondycji systemu dotyczące wszystkich jednostek w klastrze od razu po wyjęciu z pudełka. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) tworzy i usuwa jednostki na podstawie raportów systemowych. Organizuje je również w hierarchii, która przechwytuje interakcje jednostek.

> [!NOTE]
> Aby zrozumieć pojęcia związane ze zdrowiem, przeczytaj więcej w [modelu kondycji sieci szkieletowej usługi](service-fabric-health-introduction.md).
> 
> 

Raporty kondycji systemu zapewniają wgląd w funkcje klastra i aplikacji oraz flagi problemów. W przypadku aplikacji i usług raporty kondycji systemu weryfikują, czy jednostki są implementowane i zachowują się poprawnie z punktu widzenia sieci szkieletowej usług. Raporty nie zapewniają monitorowania kondycji logiki biznesowej usługi lub wykrywania procesów, które nie odpowiadają. Usługi użytkownika można wzbogacić dane kondycji z informacjami specyficznymi dla ich logiki.

> [!NOTE]
> Raporty kondycji wysyłane przez watchdogs użytkownika są widoczne tylko *po* składniki systemu utworzyć jednostkę. Po usunięciu encji magazyn kondycji automatycznie usuwa wszystkie skojarzone z nią raporty dotyczące kondycji. To samo dotyczy utworzenia nowego wystąpienia jednostki. Przykładem jest, gdy jest tworzony nowy stanowy wystąpienia repliki utrwalone usługi. Wszystkie raporty skojarzone ze starym wystąpieniem są usuwane i czyszczone ze sklepu.
> 
> 

Raporty składników systemu są identyfikowane przez źródło, które rozpoczyna się od "**System .** . Watchdogs nie można użyć tego samego prefiksu dla swoich źródeł, jak raporty z nieprawidłowymi parametrami są odrzucane.

Przyjrzyjmy się niektórym raportom systemowym, aby zrozumieć, co je wyzwala i dowiedzieć się, jak rozwiązać potencjalne problemy, które reprezentują.

> [!NOTE]
> Sieci szkieletowej usług w dalszym ciągu dodawać raporty na temat warunków zainteresowania, które poprawiają wgląd w to, co dzieje się w klastrze i aplikacji. Istniejące raporty można udoskonalić, aby szybciej rozwiązać problem.
> 
> 

## <a name="cluster-system-health-reports"></a>Raporty kondycji systemu klastrów
Encja kondycji klastra jest tworzona automatycznie w magazynie kondycji. Jeśli wszystko działa poprawnie, nie ma raportu systemowego.

### <a name="neighborhood-loss"></a>Utrata sąsiedztwa
**System.Federation** zgłasza błąd po wykryciu utraty sąsiedztwa. Raport pochodzi z poszczególnych węzłów, a identyfikator węzła jest uwzględniony w nazwie właściwości. Jeśli jedno sąsiedztwo zostanie utracone w całym pierścieniu sieci szkieletowej usług, zazwyczaj można oczekiwać dwóch zdarzeń, które reprezentują obie strony raportu luki. Jeśli więcej dzielnic zostaną utracone, jest więcej wydarzeń.

Raport określa limit czasu dzierżawy globalnej jako czas wygaśnięcia (TTL). Raport jest resent co połowa czasu trwania TTL tak długo, jak warunek pozostaje aktywny. Zdarzenie zostanie automatycznie usunięte po jego wygaśnięciu. Zachowanie Remove-when-expired zapewnia, że raport jest czyszczony z magazynu kondycji poprawnie, nawet jeśli węzeł raportowania jest w dół.

* **SourceId**: System.Federation
* **Właściwość**: Rozpoczyna się od **sąsiedztwa** i zawiera informacje o węźle.
* **Kolejne kroki**: Zbadaj, dlaczego okolica jest zagubiona. Na przykład sprawdź komunikację między węzłami klastra.

### <a name="rebuild"></a>Ponowne kompilowanie

Usługa Menedżer trybu failover (FM) zarządza informacjami o węzłach klastra. Gdy FM traci swoje dane i przechodzi do utraty danych, nie może zagwarantować, że ma najbardziej zaktualizowane informacje o węzłach klastra. W takim przypadku system przechodzi przebudowy i System.FM zbiera dane ze wszystkich węzłów w klastrze w celu odbudowania jego stanu. Czasami, z powodu problemów z siecią lub węzłami, odbudowanie może utknąć lub utknąć w martwym punkcie. To samo może się zdarzyć w usłudze Fmm (Failover Manager Master). FMM jest bezstanową usługą systemową, która śledzi, gdzie wszystkie fms znajdują się w klastrze. Podstawowy FMM jest zawsze węzłem o identyfikatorze najbliższym 0. Jeśli ten węzeł zostanie usunięty, zostanie wyzwolona przebudowa.
Gdy jeden z poprzednich warunków się dzieje, **System.FM** lub **System.FMM** flagi go za pośrednictwem raportu o błędach. Przebudowa może utknąć w jednej z dwóch faz:

* **Oczekiwanie na nadawanie**: FM/FMM czeka na odpowiedź wiadomości nadawanie z innych węzłów.

  * **Następne kroki:** Sprawdź, czy istnieje problem z połączeniem sieciowym między węzłami.
* **Oczekiwanie na węzły**: FM/FMM otrzymało już odpowiedź na emisję z innych węzłów i oczekuje na odpowiedź z określonych węzłów. Raport kondycji zawiera listę węzłów, dla których FM/FMM oczekuje na odpowiedź.
   * **Następne kroki:** Zbadaj połączenie sieciowe między FM/FMM a wymienionymi węzłami. Zbadaj każdy wymieniony węzeł pod kątem innych możliwych problemów.

* **SourceID**: System.FM lub System.FMM
* **Właściwość**: Odbuduj.
* **Następne kroki:** Zbadaj połączenie sieciowe między węzłami, a także stan określonych węzłów, które są wymienione w opisie raportu kondycji.

### <a name="seed-node-status"></a>Stan węzła źródłowego
**System.FM** zgłasza ostrzeżenie o poziomie klastra, jeśli niektóre węzły źródłowe są w złej kondycji. Węzły źródłowe są węzłami, które utrzymują dostępność klastra źródłowego. Te węzły pomagają zapewnić, że klaster pozostaje w górę, ustanawiając dzierżawy z innymi węzłami i służąc jako tiebreakers podczas niektórych rodzajów awarii sieci. Jeśli większość węzłów źródłowych są w dół w klastrze i nie są one przywracane, klaster automatycznie zamyka. 

Węzeł źródłowy jest w złej kondycji, jeśli jego stan węzła jest W dół, Usunięte lub Nieznany.
Raport ostrzegawczy dla stanu węzła źródłowego wyświetli listę wszystkich węzłów źródłowych w złej kondycji ze szczegółowymi informacjami.

* **SourceID**: System.FM
* **Właściwość**: SeedNodeStatus
* **Następne kroki:** Jeśli to ostrzeżenie jest wyświetlane w klastrze, wykonaj poniższe instrukcje, aby go naprawić: Dla klastra z uruchomień sieci szkieletowej usług w wersji 6.5 lub nowszej: Dla klastra sieci szkieletowej usług na platformie Azure, po węźle źródłowym ulegnie usłudze, usługa Service Fabric spróbuje automatycznie zmienić go na węzeł niesiewny. Aby tak się stało, upewnij się, że liczba węzłów innych niż seed w typie węzła podstawowego jest większa lub równa liczbie węzłów źródłowych W dół. W razie potrzeby dodaj więcej węzłów do typu węzła podstawowego, aby to osiągnąć.
W zależności od stanu klastra może upłynąć trochę czasu, aby rozwiązać problem. Po wykonaniu tej sprawy raport ostrzegawczy jest automatycznie czyszczony.

W przypadku autonomicznego klastra sieci szkieletowej usług, aby wyczyścić raport ostrzegawczy, wszystkie węzły źródłowe muszą stać się w dobrej kondycji. W zależności od tego, dlaczego węzły źródłowe są w złej kondycji, należy podjąć różne akcje: jeśli węzeł źródłowy jest w dół, użytkownicy muszą doprowadzić ten węzeł źródłowy w górę; Jeśli węzeł źródłowy jest Usunięty lub Nieznany, ten węzeł źródłowy [musi zostać usunięty z klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes).
Raport ostrzegawczy jest automatycznie czyszczony, gdy wszystkie węzły źródłowe stają się w dobrej kondycji.

W przypadku klastra z uruchomiona w wersji sieci szkieletowej usług starszej niż 6.5: W takim przypadku raport ostrzegawczy musi zostać wyczyszczony ręcznie. **Użytkownicy powinni upewnić się, że wszystkie węzły źródłowe stają się w dobrej kondycji przed wyczyszczeniem raportu:** jeśli węzeł źródłowy jest w dół, użytkownicy muszą doprowadzić ten węzeł źródłowy w górę;jeśli węzeł źródłowy jest usunięty lub nieznany, ten węzeł źródłowy musi zostać usunięty z klastra.
Po tym, jak wszystkie węzły źródłowe staną się zdrowe, użyj następującego polecenia z programu Powershell, aby [wyczyścić raport ostrzegawczy:](https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricclusterhealthreport)

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
**System.FabricNode** zgłasza ostrzeżenie, gdy certyfikaty używane przez węzeł są bliskie wygaśnięcia. Na węzeł przypadają trzy certyfikaty: **Certificate_cluster,** **Certificate_server**i **Certificate_default_client.** Gdy wygaśnięcie jest co najmniej dwa tygodnie, stan kondycji raportu jest OK. Gdy wygaśnięcie jest w ciągu dwóch tygodni, typ raportu jest ostrzeżenie. TTL tych zdarzeń jest nieskończona i są one usuwane, gdy węzeł opuszcza klaster.

* **SourceId**: System.FabricNode
* **Właściwość**: Rozpoczyna się od **certyfikatu** i zawiera więcej informacji o typie certyfikatu.
* **Następne kroki:** Zaktualizuj certyfikaty, jeśli zbliżają się do wygaśnięcia.

### <a name="load-capacity-violation"></a>Naruszenie nośności
Moduł równoważenia obciążenia sieci szkieletowej usług zgłasza ostrzeżenie, gdy wykryje naruszenie pojemności węzła.

* **ŹródłoId**: System.PLB
* **Właściwość**: Zaczyna się od **pojemności**.
* **Następne kroki:** Sprawdź podane metryki i wyświetl bieżącą pojemność w węźle.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Niezgodność pojemności węzłów dla metryk zarządzania zasobami
System.Hosting zgłasza ostrzeżenie, jeśli zdefiniowane pojemności węzłów w manifeście klastra są większe niż rzeczywiste pojemności węzłów dla metryk ładu zasobów (pamięci i rdzeni procesora CPU). Raport kondycji pojawia się, gdy pierwszy pakiet usług, który używa rejestrów [ładu zasobów](service-fabric-resource-governance.md) w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki:** Ten problem może być problemem, ponieważ zarządzanie pakietami usług nie są wymuszane zgodnie z oczekiwaniami, a [zarządzanie zasobami](service-fabric-resource-governance.md) nie działa poprawnie. Zaktualizuj manifest klastra o poprawne pojemności węzłów dla tych metryk lub nie określ ich i pozwól sieci szkieletowej usług automatycznie wykrywać dostępne zasoby.

## <a name="application-system-health-reports"></a>Raporty kondycji systemu aplikacji
System.CM, która reprezentuje usługę Menedżer klastrów, jest organem, który zarządza informacjami o aplikacji.

### <a name="state"></a>Stan
System.CM raporty jako OK, gdy aplikacja została utworzona lub zaktualizowana. Informuje magazyn kondycji, gdy aplikacja zostanie usunięta, dzięki czemu można ją usunąć ze sklepu.

* **SourceId**: System.CM
* **Właściwość**: Stan.
* **Następne kroki:** Jeśli aplikacja została utworzona lub zaktualizowana, powinna zawierać raport kondycji Menedżera klastrów. W przeciwnym razie sprawdź stan aplikacji, wystawiając kwerendę. Na przykład użyj polecenia cmdlet **Get-ServiceFabricApplication -ApplicationName** *applicationName*.

W poniższym przykładzie pokazano zdarzenie stanu w aplikacji **fabric:/WordCount:**

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

## <a name="service-system-health-reports"></a>Raporty dotyczące kondycji systemu usług
System.FM, która reprezentuje usługę Failover Manager, jest organem, który zarządza informacjami o usługach.

### <a name="state"></a>Stan
System.FM raporty jako OK po utworzeniu usługi. Usuwa jednostki z magazynu kondycji, gdy usługa jest usuwana.

* **SourceId**: System.FM
* **Właściwość**: Stan.

W poniższym przykładzie przedstawiono zdarzenie stanu w **sieci szkieletowej usługi:/WordCount/WordCountWebService:**

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
**System.PLB** zgłasza błąd, gdy wykryje, że aktualizowanie usługi jest skorelowane z inną usługą, która tworzy łańcuch koligacji. Raport jest czyszczony po pomyślnej aktualizacji.

* **ŹródłoId**: System.PLB
* **Właściwość**: **ServiceDescription**.
* **Następne kroki:** Sprawdź skorelowane opisy usług.

## <a name="partition-system-health-reports"></a>Raporty kondycji systemu partycji
System.FM, która reprezentuje usługę Failover Manager, jest organem, który zarządza informacjami o partycjach usługi.

### <a name="state"></a>Stan
System.FM raporty jako OK, gdy partycja została utworzona i jest w dobrej kondycji. Usuwa jednostki z magazynu kondycji po usunięciu partycji.

Jeśli partycja jest poniżej minimalnej liczby replik, zgłasza błąd. Jeśli partycja nie jest poniżej minimalnej liczby replik, ale jest poniżej licznika repliki docelowej, zgłasza ostrzeżenie. Jeśli partycja jest w utracie kworum, System.FM zgłasza błąd.

Inne znaczące zdarzenia obejmują ostrzeżenie, gdy ponowna konfiguracja trwa dłużej niż oczekiwano i gdy kompilacja trwa dłużej niż oczekiwano. Oczekiwane czasy kompilacji i ponownej konfiguracji są konfigurowalne na podstawie scenariuszy usługi. Na przykład jeśli usługa ma terabajt stanu, takich jak Azure SQL Database, kompilacja trwa dłużej niż dla usługi o małej ilości stanu.

* **SourceId**: System.FM
* **Właściwość**: Stan.
* **Następne kroki:** Jeśli stan kondycji nie jest OK, jest możliwe, że niektóre repliki nie zostały utworzone, otwarte lub promowane do podstawowego lub pomocniczego poprawnie. 

Jeśli opis opisuje utratę kworum, a następnie badanie szczegółowego raportu kondycji dla replik, które są w dół i przynosząc je z powrotem do góry pomaga przywrócić partycję do trybu online.

Jeśli opis opisuje partycję utkniętą w [rekonfiguracji,](service-fabric-concepts-reconfiguration.md)raport kondycji repliki podstawowej zawiera dodatkowe informacje.

W przypadku innych System.FM raportów kondycji będą raporty dotyczące replik lub partycji lub usługi z innych składników systemu. 

W poniższych przykładach opisano niektóre z tych raportów. 

W poniższym przykładzie pokazano partycję w dobrej kondycji:

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

Poniższy przykład pokazuje kondycję partycji, która jest poniżej docelowej liczby replik. Następnym krokiem jest uzyskanie opisu partycji, który pokazuje, jak jest skonfigurowany: **MinReplicaSetSize** jest trzy i **TargetReplicaSetSize** jest siedem. Następnie pobierz liczbę węzłów w klastrze, który w tym przypadku wynosi pięć. Tak więc w tym przypadku nie można umieścić dwóch replik, ponieważ docelowa liczba replik jest wyższa niż liczba dostępnych węzłów.

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

Poniższy przykład pokazuje kondycję partycji, która utknęła w ponownej konfiguracji ze względu na użytkownika nie honorowanie tokenu anulowania w **RunAsync** metody. Badanie raportu kondycji dowolnej repliki oznaczonej jako podstawowa (P) może pomóc w dalszym przechodzeniu do szczegółów problemu.

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
Ten raport kondycji pokazuje stan replik partycji poddawanych ponownej konfiguracji: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Dla każdej repliki raport o kondycji zawiera:
- Poprzednia rola konfiguracji
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Węzeł, na którym jest uruchomiona replika
- Identyfikator repliki

W takim przypadku, jak w przykładzie, potrzebne jest dalsze dochodzenie. Zbadaj kondycję każdej pojedynczej repliki, `Primary` `Secondary` począwszy od replik oznaczonych jako i (131482789658160654 i 131482789688598467) w poprzednim przykładzie.

### <a name="replica-constraint-violation"></a>Naruszenie ograniczenia repliki
**System.PLB** zgłasza ostrzeżenie, jeśli wykryje naruszenie ograniczenia repliki i nie może umieścić wszystkich replik partycji. Szczegóły raportu pokazują, które ograniczenia i właściwości uniemożliwiają umieszczenie repliki.

* **ŹródłoId**: System.PLB
* **Właściwość**: Rozpoczyna się od **replicaconstraintviolation**.

## <a name="replica-system-health-reports"></a>Raporty dotyczące kondycji systemu repliki
**System.RA**, który reprezentuje składnik agenta ponownej konfiguracji, jest organem dla stanu repliki.

### <a name="state"></a>Stan
System.RA raportuje OK po utworzeniu repliki.

* **SourceId**: System.RA
* **Właściwość**: Stan.

W poniższym przykładzie pokazano replikę w dobrej kondycji:

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
Ta właściwość służy do wskazywania ostrzeżeń lub błędów podczas próby otwarcia repliki, zamknięcia repliki lub przejścia repliki z jednej roli do drugiej. Aby uzyskać więcej informacji, zobacz [Cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Błędy mogą być wyjątki generowane z wywołań interfejsu API lub awarii procesu hosta usługi w tym czasie. W przypadku błędów spowodowanych wywołaniami interfejsu API z kodu Języka C# usługa sieci szkieletowej dodaje wyjątek i ślad stosu do raportu kondycji.

Te ostrzeżenia zdrowotne są wywoływane po ponowieniu próby działania lokalnie kilka razy (w zależności od zasad). Usługa Fabric ponawia ponowną akcję do maksymalnego progu. Po osiągnięciu maksymalnego progu może podjąć próbę podjęcia działań w celu skorygowania sytuacji. Ta próba może spowodować, że te ostrzeżenia zostaną wyczyszczone, ponieważ rezygnuje z akcji w tym węźle. Na przykład jeśli replika nie może otworzyć w węźle, sieci szkieletowej usług podnosi ostrzeżenie o kondycji. Jeśli replika nadal nie można otworzyć, usługa service fabric działa do samodzielnej naprawy. Ta akcja może obejmować wypróbowanie tej samej operacji w innym węźle. Ta próba powoduje, że ostrzeżenie podniesione dla tej repliki, które mają zostać wyczyszczone. 

* **SourceId**: System.RA
* **Właściwość**: **ReplicaOpenStatus**, **ReplicaCloseStatus**i **ReplicaChangeRoleStatus**.
* **Następne kroki:** Zbadaj kod usługi lub zrzuty awaryjne, aby zidentyfikować, dlaczego operacja nie powiódł się.

Poniższy przykład pokazuje kondycję repliki, `TargetInvocationException` która jest wyrzucanie z jego otwartej metody. Opis zawiera punkt awarii, **IStatefulServiceReplica.Open**, typ wyjątku **TargetInvocationException**i śledzenia stosu.

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

W poniższym przykładzie pokazano replikę, która stale ulega awarii podczas zamykania:

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
Ta właściwość jest używana do wskazania, gdy replika wykonująca [ponowną konfigurację](service-fabric-concepts-reconfiguration.md) wykryje, że ponowna konfiguracja jest zablokowana lub zablokowana. Ten raport kondycji może znajdować się w replice, której bieżąca rola jest podstawowa, z wyjątkiem przypadków rekonfiguracji podstawowej wymiany, gdzie może znajdować się w replice, która jest obniżana z podstawowej do aktywnej pomocniczej.

Ponowna konfiguracja może zostać zablokowana z jednego z następujących powodów:

- Akcja na repliki lokalnej, tej samej repliki co replika wykonująca ponowną konfigurację, nie jest likwiak. W takim przypadku badanie raportów kondycji tej repliki z innych składników, System.RAP lub System.RE, może dostarczyć dodatkowych informacji.

- Akcja nie jest dopełniana na repliki zdalnej. Repliki, dla których oczekują na akcje są wymienione w raporcie kondycji. Należy przeprowadzić dalsze badania dotyczące raportów dotyczących kondycji tych replik zdalnych. Mogą również wystąpić problemy z komunikacją między tym węzłem a węzłem zdalnym.

W rzadkich przypadkach ponowna konfiguracja może zostać zablokowana z powodu komunikacji lub innych problemów między tym węzłem a usługą Menedżera trybu failover.

* **SourceId**: System.RA
* **Właściwość**: Rekonfiguracja.
* **Następne kroki:** Zbadaj repliki lokalne lub zdalne w zależności od opisu raportu kondycji.

W poniższym przykładzie pokazano raport kondycji, w którym rekonfiguracja jest zablokowana w replice lokalnej. W tym przykładzie jest to spowodowane usługą nie honorując tokenu anulowania.

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

W poniższym przykładzie pokazano raport kondycji, w którym rekonfiguracja jest zablokowany oczekiwania na odpowiedź z dwóch replik zdalnych. W tym przykładzie istnieją trzy repliki w partycji, w tym bieżącej podstawowej. 

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

Ten raport kondycji pokazuje, że rekonfiguracja jest zablokowany oczekiwania na odpowiedź z dwóch replik: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Dla każdej repliki podane są następujące informacje:
- Poprzednia rola konfiguracji
- Bieżąca rola konfiguracji
- [Stan repliki](service-fabric-concepts-replica-lifecycle.md)
- Identyfikator węzła
- Identyfikator repliki

Aby odblokować ponowną konfigurację:
- Repliki **w dół** powinny być wychowywane. 
- Repliki **inbuild** należy zakończyć kompilacji i przejścia do gotowości.

### <a name="slow-service-api-call"></a>Powolne wywołanie interfejsu API usługi
**System.RAP** i **System.Replicator** zgłaszają ostrzeżenie, jeśli wywołanie kodu usługi użytkownika trwa dłużej niż skonfigurowany czas. Ostrzeżenie zostanie wyczyszczone po zakończeniu połączenia.

* **Identyfikator źródła:** System.RAP lub System.Replicator
* **Właściwość**: Nazwa powolnego interfejsu API. Opis zawiera więcej szczegółów na temat czasu oczekiwania interfejsu API.
* **Następne kroki:** Zbadaj, dlaczego połączenie trwa dłużej niż oczekiwano.

W poniższym przykładzie pokazano zdarzenie kondycji z System.RAP dla niezawodnej usługi, która nie honoruje tokenu anulowania w **RunAsync:**

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

Właściwość i tekst wskazują, który interfejs API utknął. Następne kroki do podjęcia dla różnych zablokowanych interfejsów API są różne. Dowolny interfejs API na *IStatefulServiceReplica* lub *IStatelessServiceInstance* jest zwykle błąd w kodzie usługi. W poniższej sekcji opisano, jak przekładają się one na [model niezawodnych usług:](service-fabric-reliable-services-lifecycle.md)

- **IStatefulServiceReplica.Open**: To ostrzeżenie wskazuje, `CreateServiceInstanceListeners` `ICommunicationListener.OpenAsync`że wywołanie , `OnOpenAsync` lub jeśli zostanie zastąpione, jest zablokowany.

- **IStatefulServiceReplica.Close** i **IStatefulServiceReplica.Abort**: Najczęstszym przypadkiem jest usługa niereżując token anulowania przekazany do `RunAsync`. Może być również `ICommunicationListener.CloseAsync`to, że , `OnCloseAsync` lub jeśli zostanie zastąpiony, jest zablokowany.

- **IStatefulServiceReplica.ChangeRole(S)** i **IStatefulServiceReplica.ChangeRole(N)**: Najczęstszym przypadkiem jest usługa niereżując token anulowania przekazany do `RunAsync`. . W tym scenariuszu najlepszym rozwiązaniem jest ponowne uruchomienie repliki.

- **IStatefulServiceReplica.ChangeRole(P)**: Najczęstszym przypadkiem jest to, że `RunAsync`usługa nie zwróciła zadania z .

Inne wywołania interfejsu API, które mogą utknąć są na **interfejsie IReplicator.** Przykład:

- **IReplicator.CatchupReplicaSet:** To ostrzeżenie wskazuje jedną z dwóch rzeczy. Nie ma wystarczających replik. Aby sprawdzić, czy tak jest, spójrz na stan replik replik w partycji lub System.FM raportu kondycji dla zablokowanej ponownej konfiguracji. Lub repliki nie są potwierdzanie operacji. Polecenie cmdlet programu `Get-ServiceFabricDeployedReplicaDetail` PowerShell może służyć do określenia postępu wszystkich replik. Problem leży w replikach, których `LastAppliedReplicationSequenceNumber` wartość `CommittedSequenceNumber` jest za wartości podstawowej.

- **IReplicator.BuildReplica(\<Remote ReplicaId>)**: To ostrzeżenie wskazuje na problem w procesie kompilacji. Aby uzyskać więcej informacji, zobacz [Cykl życia repliki](service-fabric-concepts-replica-lifecycle.md). Może to być spowodowane błędną konfiguracją adresu replikatora. Aby uzyskać więcej informacji, zobacz [Konfigurowanie stanowych niezawodnych usług](service-fabric-reliable-services-configuration.md) i [określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md). Może to być również problem w węźle zdalnym.

### <a name="replicator-system-health-reports"></a>Raporty dotyczące kondycji systemu replikatora
**Kolejka replikacji pełna:**
**System.Replicator** zgłasza ostrzeżenie, gdy kolejka replikacji jest zapełniona. W kolejce replikacji podstawowej zwykle staje się pełna, ponieważ co najmniej jedna replika pomocnicza jest powolna w potwierdzaniu operacji. Na pomocnicze, zwykle dzieje się tak, gdy usługa jest powolna, aby zastosować operacje. Ostrzeżenie jest wyczyszczone, gdy kolejka nie jest już pełna.

* **Identyfikator źródła:** System.Replicator
* **Właściwość:** **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.
* **Następne kroki:** Jeśli raport znajduje się na podstawowej, sprawdź połączenie między węzłami w klastrze. Jeśli wszystkie połączenia są w dobrej kondycji, może istnieć co najmniej jeden powolny pomocniczy z dużym opóźnieniem dysku do zastosowania operacji. Jeśli raport znajduje się w pomocniczej, najpierw sprawdź użycie dysku i wydajność w węźle. Następnie sprawdź połączenie wychodzące z powolnego węzła do podstawowego.

**RemoteReplicatorConnectionStatus:**
**System.Replicator** w replice podstawowej zgłasza ostrzeżenie, gdy połączenie z replikatorem pomocniczym (zdalnym) nie jest w dobrej kondycji. Adres replikatora zdalnego jest wyświetlany w komunikacie raportu, co ułatwia wykrycie, czy niewłaściwa konfiguracja została przekazana lub czy występują problemy z siecią między replikatorami.

* **Identyfikator źródła:** System.Replicator
* **Właściwość**: **RemoteReplicatorConnectionStatus**.
* **Następne kroki:** Sprawdź komunikat o błędzie i upewnij się, że adres replikatora zdalnego jest poprawnie skonfigurowany. Na przykład jeśli zdalny replikator jest otwarty z adresem nasłuchiwanym "localhost", nie jest osiągalny z zewnątrz. Jeśli adres wygląda poprawnie, sprawdź połączenie między węzłem podstawowym a adresem zdalnym, aby znaleźć potencjalne problemy z siecią.

### <a name="replication-queue-full"></a>Kolejka replikacji pełna
**System.Replicator** zgłasza ostrzeżenie, gdy kolejka replikacji jest zapełniona. W kolejce replikacji podstawowej zwykle staje się pełna, ponieważ co najmniej jedna replika pomocnicza jest powolna w potwierdzaniu operacji. Na pomocnicze, zwykle dzieje się tak, gdy usługa jest powolna, aby zastosować operacje. Ostrzeżenie jest wyczyszczone, gdy kolejka nie jest już pełna.

* **Identyfikator źródła:** System.Replicator
* **Właściwość:** **PrimaryReplicationQueueStatus** lub **SecondaryReplicationQueueStatus**, w zależności od roli repliki.

### <a name="slow-naming-operations"></a>Powolne operacje nazewnictwa
**System.NamingService** raportuje kondycję w swojej replice podstawowej, gdy operacja nazewnictwa trwa dłużej niż dopuszczalne. Przykładami operacji nazewnictwa są [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) lub [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Więcej metod można znaleźć w obszarze FabricClient. Można je na przykład znaleźć w obszarze [metody zarządzania usługami](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) lub [metody zarządzania właściwościami](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Usługa nazewnictwo rozpoznaje nazwy usług do lokalizacji w klastrze. Użytkownicy mogą go używać do zarządzania nazwami i właściwościami usług. Jest usługą sieci szkieletowej usług podzielonych na partycje. Jedna z partycji reprezentuje *właściciela urzędu*, który zawiera metadane dotyczące wszystkich nazw sieci szkieletowej usług i usług. Nazwy sieci szkieletowej usług są mapowane na różne partycje, nazywane partycjami *Name Owner,* więc usługa jest rozszerzalna. Dowiedz się więcej o [usłudze nazewnictwa](service-fabric-architecture.md).
> 
> 

Gdy operacja nazewnictwa trwa dłużej niż oczekiwano, operacja jest oflagowana raportem ostrzegawczym na podstawowej repliki partycji usługi nazewnictwa, która obsługuje operację. Jeśli operacja zakończy się pomyślnie, ostrzeżenie zostanie wyczyszczone. Jeśli operacja zakończy się z błędem, raport o kondycji zawiera szczegółowe informacje o błędzie.

* **SourceId**: System.NamingService
* **Właściwość**: Rozpoczyna się od prefiksu "**Duration_**" i identyfikuje powolną operację i nazwę sieci szkieletowej usług, na której jest stosowana operacja. Na przykład jeśli utwórz usługę w **sieci szkieletowej nazw:/MyApp/MyService** trwa zbyt długo, właściwość jest **Duration_AOCreateService.fabric:/MyApp/MyService**. "AO" wskazuje na rolę partycji nazewnictwa dla tej nazwy i operacji.
* **Następne kroki:** Sprawdź, dlaczego operacja nazewnictwa kończy się niepowodzeniem. Każda operacja może mieć różne przyczyny. Na przykład usługa usuwania może zostać zablokowana. Usługa może zostać zablokowana, ponieważ host aplikacji ulega awarii w węźle z powodu błędu użytkownika w kodzie usługi.

W poniższym przykładzie przedstawiono operację tworzenia usługi. Operacja trwała dłużej niż skonfigurowany czas trwania. "AO" ponawia próby i wysyła pracę na "NIE". "NIE" zakończył ostatnią operację z timeout. W takim przypadku ta sama replika jest podstawowa dla ról "AO" i "NO".

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

## <a name="deployedapplication-system-health-reports"></a>Wdrożone raporty dotyczące kondycji systemu aplikacji
**System.Hosting** jest organem wdrożonych jednostek.

### <a name="activation"></a>Aktywacja
System.Hosting raportuje jako OK, gdy aplikacja została pomyślnie aktywowana w węźle. W przeciwnym razie zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: **Aktywacja**, w tym wersja rollout.
* **Następne kroki:** Jeśli aplikacja jest w złej kondycji, należy zbadać, dlaczego aktywacja nie powiodła się.

W poniższym przykładzie przedstawiono pomyślną aktywację:

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

### <a name="download"></a>Pobierz
System.Hosting zgłasza błąd, jeśli pobieranie pakietu aplikacji nie powiedzie się.

* **SourceId**: System.Hosting
* **Właściwość**: **Pobierz**, w tym wersję rollout.
* **Następne kroki:** Sprawdź, dlaczego pobieranie nie powiodło się w węźle.

## <a name="deployedservicepackage-system-health-reports"></a>Wdrożone raporty kondycji systemuServicePackage
**System.Hosting** jest organem wdrożonych jednostek.

### <a name="service-package-activation"></a>Aktywacja pakietu serwisowego
System.Hosting raportuje jako OK, jeśli aktywacja pakietu usługi w węźle zakończy się pomyślnie. W przeciwnym razie zgłasza błąd.

* **SourceId**: System.Hosting
* **Właściwość**: Aktywacja.
* **Następne kroki:** Sprawdź, dlaczego aktywacja nie powiodła się.

### <a name="code-package-activation"></a>Aktywacja pakietu kodu
System.Hosting raportuje jako OK dla każdego pakietu kodu, jeśli aktywacja zakończy się pomyślnie. Jeśli aktywacja nie powiedzie się, zgłosi ostrzeżenie jako skonfigurowane. Jeśli **CodePackage** nie może aktywować lub zakończyć z błędem większym niż skonfigurowany **CodePackageHealthErrorThreshold**, hosting zgłasza błąd. Jeśli pakiet usługi zawiera wiele pakietów kodu, dla każdego z nich jest generowany raport aktywacji.

* **SourceId**: System.Hosting
* **Właściwość**: Używa **prefiksu CodePackageActivation** i zawiera nazwę pakietu kodu i punktu wejścia jako *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. Na przykład **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Rejestracja typu usługi
System.Hosting zgłasza się jako OK, jeśli typ usługi został pomyślnie zarejestrowany. Zgłasza błąd, jeśli rejestracja nie została wykonana w czasie, zgodnie z konfiguracją przy użyciu **ServiceTypeRegistrationTimeout**. Jeśli środowisko wykonawcze jest zamknięte, typ usługi jest wyrejestrowany z węzła i hosting zgłasza ostrzeżenie.

* **SourceId**: System.Hosting
* **Właściwość**: Używa **prefiksu ServiceTypeRegistration** i zawiera nazwę typu usługi. Na przykład **ServiceTypeRegistration:FileStoreServiceType**.

W poniższym przykładzie pokazano pakiet wdrożonych usług w dobrej kondycji:

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

### <a name="download"></a>Pobierz
System.Hosting zgłasza błąd, jeśli pobieranie pakietu usługi nie powiedzie się.

* **SourceId**: System.Hosting
* **Właściwość**: **Pobierz**, w tym wersję rollout.
* **Następne kroki:** Sprawdź, dlaczego pobieranie nie powiodło się w węźle.

### <a name="upgrade-validation"></a>Sprawdzanie poprawności uaktualnienia
System.Hosting zgłasza błąd, jeśli sprawdzanie poprawności podczas uaktualniania nie powiedzie się lub jeśli uaktualnienie nie powiedzie się w węźle.

* **SourceId**: System.Hosting
* **Właściwość**: Używa prefiksu **FabricUpgradeValidation** i zawiera wersję uaktualnienia.
* **Opis**: Wskazuje napotkany błąd.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Niezdefiniowana pojemność węzła dla metryk ładu zasobów
System.Hosting zgłasza ostrzeżenie, jeśli pojemność węzłów nie jest zdefiniowana w manifeście klastra, a konfiguracja automatycznego wykrywania jest wyłączona. Sieci szkieletowej usług podnosi ostrzeżenie o kondycji, gdy pakiet usług, który używa rejestrów [ładu zasobów](service-fabric-resource-governance.md) w określonym węźle.

* **SourceId**: System.Hosting
* **Właściwość**: **ResourceGovernance**.
* **Następne kroki:** Preferowanym sposobem rozwiązania tego problemu jest zmiana manifestu klastra, aby umożliwić automatyczne wykrywanie dostępnych zasobów. Innym sposobem jest zaktualizowanie manifestu klastra z poprawnie określonymi pojemnościami węzłów dla tych metryk.

## <a name="next-steps"></a>Następne kroki
* [Wyświetlanie raportów kondycji sieci szkieletowej usług](service-fabric-view-entities-aggregated-health.md)

* [Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Uaktualnienie aplikacji sieci szkieletowej usług](service-fabric-application-upgrade.md)

