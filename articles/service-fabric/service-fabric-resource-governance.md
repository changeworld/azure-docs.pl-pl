---
title: Nadzór nad zasobami dla kontenerów i usług
description: Usługa Azure Service Fabric umożliwia określenie limitów zasobów dla usług działających wewnątrz lub na zewnątrz kontenerów.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 85520876d7f0c89450b572d28dee6cb66ed2231d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772384"
---
# <a name="resource-governance"></a>Nadzór nad zasobami

Podczas uruchamiania wielu usług w tym samym węźle lub klastrze, jest możliwe, że jedna usługa może zużywać więcej zasobów, głodując innych usług w procesie. Ten problem jest określany jako problem "hałaśliwego sąsiada". Usługa Azure Service Fabric umożliwia deweloperowi określić rezerwacje i limity na usługę, aby zagwarantować zasoby i ograniczyć użycie zasobów.

> Przed kontynuowaniem tego artykułu zaleca się zapoznanie się z [modelem aplikacji sieci szkieletowej usług](service-fabric-application-model.md) i [modelem hostingu sieci szkieletowej usług.](service-fabric-hosting-model.md)
>

## <a name="resource-governance-metrics"></a>Metryki zarządzania zasobami

Zarządzanie zasobami jest obsługiwane w sieci szkieletowej usług zgodnie z [pakietem usług](service-fabric-application-model.md). Zasoby, które są przypisane do pakietu usługi można dalej podzielić na pakiety kodu. Limity zasobów, które są określone, oznaczają również rezerwację zasobów. Usługa Sieci szkieletowej obsługuje określanie procesora CPU i pamięci na pakiet usługi, z dwoma [wbudowanymi metrykami:](service-fabric-cluster-resource-manager-metrics.md)

* *PROCESOR* (nazwa `servicefabric:/_CpuCores`metryki): rdzeń logiczny, który jest dostępny na komputerze-hoście. Wszystkie rdzenie we wszystkich węzłach są ważone tak samo.

* *Pamięć* (nazwa `servicefabric:/_MemoryInMB`metryki): Pamięć jest wyrażona w megabajtach i jest mapowana na pamięć fizyczną dostępną na komputerze.

W przypadku tych dwóch metryk [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-cluster-description.md) śledzi całkowitą pojemność klastra, obciążenie każdego węzła w klastrze i pozostałe zasoby w klastrze. Te dwa metryki są równoważne innym użytkownikom lub metryki niestandardowej. Można z nich korzystać we wszystkich istniejących funkcjach:

* Klaster można [równoważyć](service-fabric-cluster-resource-manager-balancing.md) zgodnie z tymi dwoma metrykami (zachowanie domyślne).
* Klaster może być [defragmentacji](service-fabric-cluster-resource-manager-defragmentation-metrics.md) zgodnie z tymi dwoma metryki.
* Opisując [klaster,](service-fabric-cluster-resource-manager-cluster-description.md)buforowane pojemności można ustawić dla tych dwóch metryk.

[Dynamiczne raportowanie obciążenia](service-fabric-cluster-resource-manager-metrics.md) nie jest obsługiwane dla tych metryk, a obciążenia dla tych metryk są definiowane w czasie tworzenia.

## <a name="resource-governance-mechanism"></a>Mechanizm zarządzania zasobami

Środowisko uruchomieniowe sieci szkieletowej usług obecnie nie zapewnia rezerwacji zasobów. Po otwarciu procesu lub kontenera środowisko wykonawcze ustawia limity zasobów dla obciążeń, które zostały zdefiniowane w czasie tworzenia. Ponadto środowisko wykonawcze odrzuca otwieranie nowych pakietów usług, które są dostępne po przekroczeniu zasobów. Aby lepiej zrozumieć, jak działa proces, weźmy przykład węzła z dwoma rdzeniami procesora (mechanizm zarządzania pamięcią jest równoważny):

1. Najpierw kontener jest umieszczany w węźle, żądając jednego rdzenia procesora CPU. Środowisko wykonawcze otwiera kontener i ustawia limit procesora CPU na jeden rdzeń. Kontener nie będzie mógł używać więcej niż jednego rdzenia.

2. Następnie replika usługi jest umieszczana w węźle, a odpowiedni pakiet usług określa limit jednego rdzenia procesora CPU. Środowisko wykonawcze otwiera pakiet kodu i ustawia limit procesora CPU na jeden rdzeń.

W tym momencie suma limitów jest równa pojemności węzła. Proces i kontener są uruchomione z jednym rdzeniem każdego i nie zakłócają siebie nawzajem. Sieci szkieletowej usług nie umieszcza więcej kontenerów lub replik, gdy są one określania limitu procesora CPU.

Istnieją jednak dwie sytuacje, w których inne procesy mogą walczyć o procesora CPU. W takich sytuacjach proces i kontener z naszego przykładu może wystąpić problem hałaśliwego sąsiada:

* *Mieszanie zarządzanych i nierządowanych usług i kontenerów:* Jeśli użytkownik tworzy usługę bez określonego zarządzania zasobami, środowisko wykonawcze widzi, że nie zużywa żadnych zasobów i może umieścić go w węźle w naszym przykładzie. W takim przypadku ten nowy proces skutecznie zużywa niektóre procesora CPU kosztem usług, które są już uruchomione w węźle. Istnieją dwa rozwiązania tego problemu. Nie mieszaj usług zarządzanych i nieobjętych w tym samym klastrze lub nie [używaj ograniczeń umieszczania,](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) aby te dwa typy usług nie kończyły się na tym samym zestawie węzłów.

* *Po uruchomieniu innego procesu w węźle, poza sieci szkieletowej usług (na przykład usługi systemu operacyjnego)*: W tej sytuacji proces poza sieci szkieletowej usług również walczy dla procesora CPU z istniejących usług. Rozwiązaniem tego problemu jest poprawne skonfigurowanie pojemności węzłów w celu uwzględnienia narzutów systemu operacyjnego, jak pokazano w następnej sekcji.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguracja klastra umożliwiająca zarządzanie zasobami

Gdy węzeł uruchamia i dołącza do klastra, usługa Service Fabric wykrywa dostępną ilość pamięci i dostępną liczbę rdzeni, a następnie ustawia pojemność węzłów dla tych dwóch zasobów.

Aby pozostawić miejsce w buforze dla systemu operacyjnego, a dla innych procesów może być uruchomiony w węźle, sieci szkieletowej usług używa tylko 80% dostępnych zasobów w węźle. Ta wartość procentowa jest konfigurowalna i można ją zmienić w manifeście klastra.

Oto przykład poinstruowania sieci szkieletowej usług, aby używała 50% dostępnego procesora i 70% dostępnej pamięci:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

W przypadku większości klientów i scenariuszy automatyczne wykrywanie pojemności węzłów dla procesora i pamięci jest zalecaną konfiguracją (automatyczne wykrywanie jest domyślnie włączone). Jeśli jednak potrzebna jest pełna ręczna konfiguracja pojemności węzłów, można skonfigurować te na typ węzła przy użyciu mechanizmu opisującego węzły w klastrze. Oto przykład konfigurowania typu węzła z czterema rdzeniami i 2 GB pamięci:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Gdy automatyczne wykrywanie dostępnych zasobów jest włączone, a pojemności węzłów są ręczne zdefiniowane w manifeście klastra, usługa Service Fabric sprawdza, czy węzeł ma wystarczającą ilość zasobów do obsługi pojemności zdefiniowanej przez użytkownika:

* Jeśli pojemności węzłów, które są zdefiniowane w manifeście są mniejsze lub równe dostępnych zasobów w węźle, a następnie sieci szkieletowej usług używa pojemności, które są określone w manifeście.

* Jeśli pojemności węzłów, które są zdefiniowane w manifeście są większe niż dostępne zasoby, sieci szkieletowej usług używa dostępnych zasobów jako pojemności węzła.

Automatyczne wykrywanie dostępnych zasobów można wyłączyć, jeśli nie jest to wymagane. Aby ją wyłączyć, zmień następujące ustawienie:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Aby uzyskać optymalną wydajność, w manifeście klastra należy również włączyć następujące ustawienie:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> Począwszy od sieci szkieletowej usług w wersji 7.0, zaktualizowaliśmy regułę obliczania pojemności zasobów węzłów w przypadkach, gdy użytkownik ręcznie podaje wartości pojemności zasobów węzła. Rozważmy następujący scenariusz:
>
> * W węźle znajduje się łącznie 10 rdzeni procesora
> * SF jest skonfigurowany do używania 80% całkowitych zasobów dla usług użytkownika (ustawienie domyślne), co pozostawia bufor 20% dla innych usług uruchomionych w węźle (w tym usług sieci szkieletowej usług)
> * Użytkownik decyduje się ręcznie zastąpić pojemność zasobu węzła dla metryki rdzeni procesora i ustawia ją na 5 rdzeni
>
> Zmieniliśmy regułę dotyczącą sposobu obliczania dostępnej pojemności usług użytkowników sieci szkieletowej w następujący sposób:
>
> * Przed siecią szkieletową usług 7.0 dostępna pojemność dla usług użytkowników zostanie obliczona na **5 rdzeni** (bufor pojemności 20% jest ignorowany)
> * Począwszy od usługi Service Fabric 7.0, dostępna pojemność dla usług użytkownika będzie obliczana na **4 rdzenie** (bufor pojemności 20% nie jest ignorowany)

## <a name="specify-resource-governance"></a>Określanie zarządzania zasobami

Limity ładu zasobów są określone w manifeście aplikacji (ServiceManifestImport sekcji), jak pokazano w poniższym przykładzie:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

W tym przykładzie pakiet usługi o nazwie **ServicePackageA** pobiera jeden rdzeń w węzłach, w których jest umieszczony. Ten pakiet usługi zawiera dwa pakiety kodu **(CodeA1** `CpuShares` i **CodeA2)** i oba określają parametr. Proporcja CpuShares 512:256 dzieli rdzeń na dwa pakiety kodu.

Tak więc w tym przykładzie CodeA1 pobiera dwie trzecie rdzenia, a CodeA2 pobiera jedną trzecią rdzenia (i rezerwację gwarancji miękkiej tego samego). Jeśli CpuShares nie są określone dla pakietów kodu, sieci szkieletowej usług dzieli rdzenie równo między nimi.

Limity pamięci są bezwzględne, więc oba pakiety kodu są ograniczone do 1024 MB pamięci (i rezerwacji gwarancji miękkiej tego samego). Pakiety kodu (kontenery lub procesy) nie można przydzielić więcej pamięci niż ten limit i próby tego powoduje wyjątek braku pamięci. Aby wymuszanie limitu zasobów działało, wszystkie pakiety kodu w ramach pakietu usług powinny mieć określone limity pamięci.

### <a name="using-application-parameters"></a>Korzystanie z parametrów aplikacji

Podczas określania ustawień zarządzania zasobami można używać [parametrów aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) do zarządzania wieloma konfiguracjami aplikacji. Poniższy przykład przedstawia użycie parametrów aplikacji:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

W tym przykładzie domyślne wartości parametrów są ustawiane dla środowiska produkcyjnego, gdzie każdy pakiet usług otrzyma 4 rdzenie i 2 GB pamięci. Istnieje możliwość zmiany wartości domyślnych za pomocą plików parametrów aplikacji. W tym przykładzie jeden plik parametru może służyć do testowania aplikacji lokalnie, gdzie można uzyskać mniej zasobów niż w produkcji:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> Określanie zarządzania zasobami za pomocą parametrów aplikacji jest dostępne począwszy od sieci szkieletowej usług w wersji 6.1.<br>
>
> Gdy parametry aplikacji są używane do określania zarządzania zasobami, sieci szkieletowej usług nie można obniżyć do wersji przed wersją 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Wymuszanie limitów zasobów dla usług użytkowników

Podczas stosowania zarządzania zasobami do usług sieci szkieletowej usług gwarantuje, że te usługi zarządzane przez zasoby nie może przekroczyć ich przydział zasobów, wielu użytkowników nadal trzeba uruchomić niektóre z ich usług sieci szkieletowej usług w trybie niezarządzanym. Podczas korzystania z niezarządzanych usług sieci szkieletowej usług, jest możliwe, aby uruchomić w sytuacjach, w których "runaway" niezarządzane usługi zużywają wszystkie dostępne zasoby w węzłach sieci szkieletowej usług, co może prowadzić do poważnych problemów, takich jak:

* Głód zasobów innych usług uruchomionych w węzłach (w tym usług systemu sieci szkieletowej usług)
* Węzły kończą się w stanie złej kondycji
* Interfejsy API zarządzania klastrami sieci szkieletowej sieci szkieletowej nie odpowiada

Aby zapobiec występowaniu tych sytuacji, sieci szkieletowej usług pozwala *wymusić limity zasobów dla wszystkich usług użytkowników sieci szkieletowej usług uruchomionych w węźle* (zarówno regulowane, jak i nieorządzone), aby zagwarantować, że usługi użytkownika nigdy nie będą używać więcej niż określona ilość zasobów. Osiąga się to przez ustawienie wartości dla enforceuserServiceMetricCapacities config w placementAndLoadBalancing sekcji ClusterManifest true. To ustawienie jest domyślnie wyłączone.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Uwagi dodatkowe:

* Wymuszanie limitu zasobów `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` dotyczy tylko metryk i zasobów
* Wymuszanie limitu zasobów działa tylko wtedy, gdy pojemności węzłów dla metryk zasobów są dostępne dla sieci szkieletowej usług za pośrednictwem mechanizmu automatycznego wykrywania lub za pośrednictwem użytkowników ręcznie określających możliwości węzłów (jak wyjaśniono w [sekcji Konfiguracja klastra umożliwiająca zarządzanie zasobami).](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance)Jeśli pojemności węzłów nie są skonfigurowane, nie można użyć możliwości wymuszania limitów zasobów, ponieważ sieci szkieletowej usług nie można wiedzieć, ile zasobów do zarezerwowania dla usług użytkownika.Sieci szkieletowej usług wyda ostrzeżenie o kondycji, jeśli "EnforceUserServiceMetricCapacities" jest true, ale możliwości węzła nie są skonfigurowane.

## <a name="other-resources-for-containers"></a>Inne zasoby kontenerów

Oprócz procesora CPU i pamięci, możliwe jest określenie innych limitów zasobów dla kontenerów. Te limity są określone na poziomie pakietu kodu i są stosowane podczas pracy kontenera. W przeciwieństwie do procesora CPU i pamięci, Menedżer zasobów klastra nie jest świadomy tych zasobów i nie będzie wykonywać żadnych kontroli pojemności lub równoważenia obciążenia dla nich.

* *MemorySwapInMB*: Ilość pamięci wymiany, która może być używana przez kontener.
* *MemoryReservationInMB*: Miękki limit dla nadzoru pamięci, który jest wymuszany tylko wtedy, gdy rywalizacja o pamięć jest wykrywana w węźle.
* *CpuPercent*: Procent procesora CPU, który może używać kontenera. Jeśli limity procesora CPU są określone dla pakietu usług, ten parametr jest skutecznie ignorowane.
* *MaximumIOps*: Maksymalna liczba operacji We/Wy, których kontener może używać (odczytywać i zapisywać).
* *MaximumIOBytesps*: Maksymalna liczba operacji we/wy (bajtów na sekundę), których kontener może używać (odczytywać i zapisywać).
* *BlockIOWeight*: Waga we/wy bloku dla w stosunku do innych pojemników.

Zasoby te mogą być łączone z procesorem i pamięcią. Oto przykład określania dodatkowych zasobów dla kontenerów:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Menedżerze zasobów klastra, przeczytaj artykuł [Wprowadzenie menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się więcej o modelu aplikacji, pakietach usług i pakietach kodu — i sposobie mapowania do nich replik - przeczytaj [modelowanie aplikacji w sieci szkieletowej usług](service-fabric-application-model.md).
