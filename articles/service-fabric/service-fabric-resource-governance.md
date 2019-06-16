---
title: Usługa Azure Service Fabric nadzór nad zasobami dla kontenerów i usług | Dokumentacja firmy Microsoft
description: Usługa Azure Service Fabric pozwala określić limity zasobów dla usług uruchomionych wewnątrz lub poza kontenerów.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: e011554e61411fddca034f024c30c2270593e07b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772539"
---
# <a name="resource-governance"></a>Nadzór nad zasobami

Jeśli korzystasz z wielu usług, na tym samym węźle lub klastra, jest to możliwe, że jedna usługa może zużywać więcej zasobów, w blokują inne usługi w ramach procesu. Ten problem, jest nazywana "hałaśliwym sąsiadem". Usługa Azure Service Fabric umożliwia dla deweloperów określić zastrzeżenia i limity dla usługi w celu zagwarantowania zasobów i ograniczenie użycia zasobów.

> Przed kontynuowaniem pracy z tym artykułem, firma Microsoft zaleca, możesz zapoznać się z [modelu aplikacji usługi Service Fabric](service-fabric-application-model.md) i [modelu hostingu usługi Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metryki nadzoru zasobów

Nadzór nad zasobami jest obsługiwana w usłudze Service Fabric przy użyciu [pakiet usługi](service-fabric-application-model.md). Zasoby, które są przypisane do pakietu z można podzielić między pakietami kodu. Limity zasobów, które są określone to także oznaczać rezerwacji zasobów. Usługa Service Fabric obsługuje określanie Procesora i pamięci na pakiet usługi przy użyciu dwóch wbudowanych [metryki](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor CPU* (Nazwa metryki `servicefabric:/_CpuCores`): Rdzeń logiczny, który jest dostępny na komputerze hosta. Wszystkich rdzeni we wszystkich węzłach są ważona takie same.

* *Pamięć* (Nazwa metryki `servicefabric:/_MemoryInMB`): Pamięć jest wyrażona w megabajtach i jest on mapowany do pamięci fizycznej, który jest dostępny na komputerze.

Aby uzyskać te dwie metryki [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-cluster-description.md) śledzi całkowita pojemność klastra, obciążenia na każdym węźle w klastrze, a pozostałe zasoby w klastrze. Te dwie metryki są równoważne dowolnemu użytkownikowi lub metryk niestandardowych. Wszystkie istniejące funkcje można używanych wraz z nimi:

* Klaster może być [równoważenia](service-fabric-cluster-resource-manager-balancing.md) zgodnie z te dwie metryki (zachowanie domyślne).
* Klaster może być [defragmentacji](service-fabric-cluster-resource-manager-defragmentation-metrics.md) zgodnie z te dwie metryki.
* Gdy [opisujące klaster](service-fabric-cluster-resource-manager-cluster-description.md), buforowane pojemności można ustawić te dwie metryki.

[Dynamiczne raportowanie obciążenia](service-fabric-cluster-resource-manager-metrics.md) nie jest obsługiwana dla tych metryk i ładuje, aby te metryki są definiowane w czasie jego tworzenia.

## <a name="resource-governance-mechanism"></a>Mechanizm zarządzania zasobów

Środowisko uruchomieniowe usługi Service Fabric aktualnie nie zapewnia rezerwacji dla zasobów. Po otwarciu procesu lub kontenera, środowisko uruchomieniowe Ustawia limity zasobów obciążeń, które zostały zdefiniowane w czasie jego tworzenia. Ponadto środowisko wykonawcze odrzuca otwarcia nowe pakiety usługi, które są dostępne po przekroczeniu zasobów. Aby lepiej zrozumieć, jak działa proces, Spójrzmy na przykład węzła przy użyciu dwóch rdzeni procesora CPU (mechanizm zarządzania pamięci jest równoważne):

1. Po pierwsze kontener jest umieszczany w węźle żądania jednego rdzenia procesora CPU. Środowisko uruchomieniowe otwiera kontener i ustawia limitu procesora CPU do jednego rdzenia. Kontener nie będzie mogła użyć więcej niż jednego rdzenia.

2. Następnie repliki usługi znajduje się w węźle, a odpowiedni pakiet usługi określa limit jednego rdzenia procesora CPU. Środowisko uruchomieniowe otwiera pakiet kodu i ustawia jego limitu procesora CPU do jednego rdzenia.

W tym momencie sumy limitów jest równy pojemność węzła. Proces kontenera z jednym rdzeniu i nie zakłóca ze sobą. Usługa Service Fabric nie umieszcza dalszych repliki lub kontenerów podczas określania limitu procesora CPU.

Istnieją dwie sytuacje, w których inne procesy mogą będą konkurować o procesora CPU. W takich sytuacjach proces i kontener, w tym przykładzie może wystąpić problem zasobożernymi:

* *Łączenie usług zarządzanych i niezarządzanych i kontenery*: Użytkownik tworzy usługi bez żadnych nadzór nad zasobami określone, środowisko uruchomieniowe uznaje korzysta z nie zasobów i można umieścić w węźle, w tym przykładzie. W takim przypadku ten nowy proces zużywa skutecznie niektóre Procesora kosztem usług, które zostały już uruchomione w węźle. Istnieją dwa rozwiązania tego problemu. Nie Mieszaj usług zarządzanych i niezarządzanych, w tym samym klastrze, albo użyj [ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) tak, że te dwa typy usług nie znajdą się na tym samym zestawie węzłów.

* *Po uruchomieniu inny proces na węźle, poza usługi Service Fabric (na przykład usługa systemu operacyjnego)* : W takiej sytuacji proces poza usługi Service Fabric również twierdzi dla Procesora z istniejącymi usługami. Rozwiązanie tego problemu jest skonfigurować węzła zdolności produkcyjnych poprawnie kontu obciążenie systemu operacyjnego, jak pokazano w następnej sekcji.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguracja klastra umożliwiające zarządzanie zasobami

Gdy węzeł zostanie uruchomione i dołączy do klastra, Usługa Service Fabric wykrywa dostępna ilość pamięci i liczba dostępnych rdzeni, a następnie ustawia wydajność węzła dla tych dwóch zasobów.

Pozostawienie miejsca w buforze system operacyjny i inne procesy, może być uruchomione w węźle, Usługa Service Fabric używa tylko 80% dostępnych zasobów w węźle. Ta wartość procentowa jest konfigurowane i można zmienić w manifeście klastra.

Poniżej przedstawiono przykładowy sposób wydać polecenie usługi Service Fabric do 50% dostępne możliwości procesora CPU i 70% dostępnej pamięci:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Jeśli potrzebujesz pełnej ręczne ustawienie wydajność węzłów, można użyć regularnych mechanizm do opisywania węzłów w klastrze. Poniżej przedstawiono przykład sposobu konfigurowania węzła za pomocą cztery rdzenie i 2 GB pamięci:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Gdy automatyczne wykrywanie dostępnych zasobów jest włączona, a wydajność węzłów ręcznie są zdefiniowane w manifeście klastra, usługi Service Fabric sprawdza, czy węzeł ma za mało zasobów do obsługi pojemności, zdefiniowanego przez użytkownika:

* Jeśli wydajność węzłów, które są zdefiniowane w manifeście są mniejsze niż lub równe dostępnych zasobów w węźle, Usługa Service Fabric używa pojemności, które są określone w manifeście.

* Jeśli wydajność węzłów, które są zdefiniowane w manifeście są większe od dostępnych zasobów, Usługa Service Fabric używa dostępnych zasobów jako węzeł pojemności.

Automatyczne wykrywanie dostępnych zasobów można wyłączyć, jeśli nie jest wymagana. Aby je wyłączyć, zmienić następujące ustawienia:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Aby uzyskać optymalną wydajność należy również włączone następujące ustawienie w manifeście klastra:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>Określ nadzór nad zasobami

Limity nadzoru zasobów są określone w manifeście aplikacji (sekcja ServiceManifestImport), jak pokazano w poniższym przykładzie:

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

W tym przykładzie pakiet usługi o nazwie **ServicePackageA** pobiera jednego rdzenia w węzłach, gdzie się znajduje. Ten pakiet usługi zawiera dwa pakiety kodu (**CodeA1** i **CodeA2**), a następnie określ zarówno `CpuShares` parametru. Część CpuShares 512:256 dzieli podstawowe w pakietach dwóch kodu.

W związku z tym w tym przykładzie CodeA1 pobiera dwie trzecie rdzenia, a następnie CodeA2 pobiera jedna trzecia podstawowa (i gwarancję nietrwałego rezerwację tej samej). Jeśli nie określono CpuShares pakietów kodu, Usługa Service Fabric dzieli rdzeni równomiernie między nimi.

Limity pamięci są bezwzględne, dzięki czemu oba pakiety kodu mogą zawierać maksymalnie 1024 MB pamięci (i gwarancję nietrwałego rezerwację tej samej). Pakiety kodu (kontenery lub procesy) nie może przydzielać pamięci ponad ten limit. podjęcie próby takiego przydzielenia spowoduje wyjątek braku pamięci. Aby wymuszanie limitu zasobów działało, wszystkie pakiety kodu w ramach pakietu usług powinny mieć określone limity pamięci.

### <a name="using-application-parameters"></a>Przy użyciu parametrów aplikacji

Podczas określania nadzór nad zasobami jest możliwość użycia [parametry aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) na zarządzanie wieloma konfiguracjami aplikacji. Poniższy przykład przedstawia użycie parametrów aplikacji:

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

W tym przykładzie domyślne wartości parametrów są ustawiane w środowisku produkcyjnym, gdzie każdy pakiet usługi otrzymamy 4 rdzenie, jak i 2 GB pamięci. Jest to możliwe, można zmienić wartości domyślnych za pomocą pliki parametrów aplikacji. W tym przykładzie jeden plik parametrów może służyć do testowania aplikacji w środowisku lokalnym, gdzie ją otrzymamy mniej zasobów niż w środowisku produkcyjnym:

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
> Określanie nadzór nad zasobami z parametrami aplikacji jest dostępna, począwszy od usługi Service Fabric wersji 6.1.<br>
>
> Parametry aplikacji używanego do określania nadzór nad zasobami usługi Service Fabric nie można zmienić na starszą wersję wersji wcześniejszej niż w wersji 6.1.

## <a name="other-resources-for-containers"></a>Inne zasoby dla kontenerów

Oprócz Procesora i pamięci jest to możliwe określić inne ograniczenia dotyczące zasobów dla kontenerów. Te limity są określone na poziomie pakietu kodu i są stosowane po uruchomieniu kontenera. W przeciwieństwie do procesora CPU i pamięci, Menedżer zasobów klastra nie jest świadomy tych zasobów i nie będzie wykonać żadnych testów wydajności lub równoważenia obciążenia dla nich.

* *MemorySwapInMB*: Ilość pamięci wymiany, używanego w kontenerze.
* *MemoryReservationInMB*: Zmienny limit nadzoru pamięci, który jest wymuszany, tylko wtedy, gdy wykryto rywalizacji o zasoby pamięci w węźle.
* *CpuPercent*: Procent procesora CPU, kontener może korzystać. Jeśli podano limity procesora CPU dla pakietu z tego parametru skutecznie jest ignorowana.
* *MaximumIOps*: Maksymalna liczba IOPS, można użyć kontenera (Odczyt i zapis).
* *MaximumIOBytesps*: Maksymalna we/wy (w bajtach na sekundę), można użyć kontenera (Odczyt i zapis).
* *BlockIOWeight*: Blok waga we/wy dla względem innych kontenerów.

Te zasoby można łączyć z procesora CPU i pamięci. Poniżej przedstawiono przykładowy sposób określić dodatkowe zasoby dla kontenerów:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o Menedżer zasobów klastra, przeczytaj [Przedstawiamy Menedżer zasobów klastra usługi Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się więcej o modelu aplikacji, pakietów usługi i pakiety kodu — i jak repliki mapy do nich — odczytać [modelu aplikacji w usłudze Service Fabric](service-fabric-application-model.md).
