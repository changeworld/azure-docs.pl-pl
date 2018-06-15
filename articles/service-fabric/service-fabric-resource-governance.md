---
title: Azure ładu zasobów sieci szkieletowej usług dla usług i kontenery | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług Azure można określić limity zasobów dla usług uruchomionych wewnątrz lub na zewnątrz kontenerów.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 49c7e2c99cce13880781a67806543b1cde0c12b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208016"
---
# <a name="resource-governance"></a>Nadzór nad zasobami 

Jeśli korzystasz z wielu usług na tym samym węźle lub klastra, jest możliwe, że jedna usługa może zużywać więcej zasobów, starving innych usług w procesie. Ten problem jest określana jako problem "zakłócenia sąsiada". Sieć szkieletowa usług Azure umożliwia deweloperowi Określ zastrzeżenia i limity dla usługi do zagwarantowania zasobów oraz ograniczenie użycia zasobów.

> Przed przejściem w tym artykule, firma Microsoft zaleca czy należy zapoznać się z [model aplikacji usługi sieć szkieletowa](service-fabric-application-model.md) i [modelu hostingu sieci szkieletowej usług](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metryki ładu zasobów 

Zarządzanie zasobów jest obsługiwane w sieci szkieletowej usług zgodnie z [pakiet usługi](service-fabric-application-model.md). Zasoby, które są przypisane do pakietu z można podzielić między pakiety kodu. Ograniczenia zasobów, które są określone to także oznaczać rezerwacji zasobów. Określanie Procesora i pamięci na pakiet usługi z dwóch wbudowanych obsługuje sieci szkieletowej usług [metryki](service-fabric-cluster-resource-manager-metrics.md):

* *Procesor CPU* (Nazwa metryki `servicefabric:/_CpuCores`): rdzenia logicznego, który jest dostępny na komputerze hosta. Wszystkie rdzenie obejmujące wszystkie węzły są ważone takie same.

* *Pamięć* (Nazwa metryki `servicefabric:/_MemoryInMB`): pamięci jest wyrażona w megabajtach i mapowania pamięci fizycznej, która jest dostępna na komputerze.

Dla tych dwóch metryki [Menedżera zasobów klastra](service-fabric-cluster-resource-manager-cluster-description.md) śledzi klastra całkowita pojemność, obciążenia na każdym węźle w klastrze, a pozostałe zasoby w klastrze. Te dwie metryki są równoważne innego użytkownika lub niestandardowa metryka. Wszystkie istniejące funkcje mogą być używane z nimi:
* Klaster może być [zrównoważonym](service-fabric-cluster-resource-manager-balancing.md) zgodnie z tych dwóch metryk (domyślnie).
* Klaster może być [defragmentacji](service-fabric-cluster-resource-manager-defragmentation-metrics.md) zgodnie z tych dwóch metryk.
* Gdy [opisujące klastra](service-fabric-cluster-resource-manager-cluster-description.md), buforowane pojemności może być ustawiona dla tych dwóch metryk.

[Obciążenie dynamicznego raportowania](service-fabric-cluster-resource-manager-metrics.md) nie jest obsługiwana dla tych metryk i ładuje dla tych metryk są zdefiniowane w czasie tworzenia.

## <a name="resource-governance-mechanism"></a>Mechanizm ładu zasobów

Środowisko uruchomieniowe usługi sieć szkieletowa aktualnie nie zapewnić dla zasobów. Po otwarciu procesu lub kontener środowiska uruchomieniowego ustawia z limitami zasobów do obciążeń, które zostały zdefiniowane w czasie tworzenia. Ponadto środowisko uruchomieniowe odrzuca otwierania nowych pakietów usługi, które są dostępne po przekroczeniu zasobów. Aby lepiej zrozumieć, jak proces działa, Spójrzmy na przykład węzła o dwa rdzenie procesora CPU (mechanizm zarządzania pamięci jest równoważny):

1. Po pierwsze kontener znajduje się w węźle żądania rdzeniu Procesora. Środowisko uruchomieniowe otwiera kontenera i ustawia limitu procesora CPU jednego rdzenia. Kontener nie będzie mogła użyć więcej niż jednego rdzenia.

2. Następnie repliki usługi znajduje się w węźle i odpowiedni pakiet usługi określa limit rdzeniu Procesora. Środowisko uruchomieniowe otwiera pakiet kodu i ustawia swój limit CPU jednego rdzenia.

W tym momencie sumy limitów jest równa pojemności węzła. Proces i kontener są uruchomiony z jednego rdzenia, a nie od wzajemnie zakłócać. Sieć szkieletowa usług nie umieścić więcej repliki lub kontenery podczas określania limitu procesora CPU.

Istnieją dwie sytuacje, w których innych procesów może doprowadzić do ograniczenia procesora CPU. W takich sytuacjach proces i kontener w naszym przykładzie mogą wystąpić sąsiada naprawienie problemu:

* *Mieszanie, której działalność i postanowieniom usługi i kontenery*: Jeśli użytkownik tworzy usługi bez żadnych ładu zasobu określona, środowiska uruchomieniowego widzi ona jako zużywające żadnych zasobów i umieścić go w węźle w naszym przykładzie. W takim przypadku ten nowy proces zużywa skutecznie niektórych Procesora kosztem usług, które są już uruchomione w węźle. Istnieją dwa rozwiązania tego problemu. Nie mieszać której działalność i postanowieniom usługi na tym samym klastrze, albo użyj [ograniczenia umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) tak, że te dwa typy usług nie przechodzili na ten sam zestaw węzłów.

* *Po uruchomieniu inny proces na węźle, poza usługi sieci szkieletowej (na przykład usługa systemu operacyjnego)*: W tej sytuacji proces spoza sieci szkieletowej usług twierdzi dla procesora CPU również z istniejącymi usługami. Rozwiązanie tego problemu jest skonfigurowanie zdolności węzła poprawnie kontu na obciążenie systemu operacyjnego, jak pokazano w następnej sekcji.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Konfiguracja klastra umożliwiających zarządzanie zasobów

Gdy węzeł uruchamia i dołączy do klastra, Service Fabric wykrywa ilość dostępnej pamięci i dostępna liczba rdzeni, a następnie ustawia pojemności węzła dla tych dwóch zasobów. 

Aby opuścić miejsca w buforze dla systemu operacyjnego i inne procesy, może być uruchomiona w węźle, Service Fabric korzysta tylko 80% dostępnych zasobów w węźle. Tej wartości procentowej można konfigurować i można zmieniać w manifeście klastra. 

Poniżej przedstawiono przykładowy sposób nakazać programowi Service Fabric, aby korzystać z 50% dostępne możliwości procesora CPU i 70% dostępnej pamięci: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Instalacji pełnej ręcznej możliwości węzła, należy umożliwia regularne mechanizmu opisu węzłów w klastrze. Poniżej przedstawiono przykład sposobu konfigurowania węzeł z czterech rdzeni i 2 GB pamięci: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Gdy jest włączone automatyczne wykrywanie dostępnych zasobów i pojemności węzła ręcznie są zdefiniowane w manifeście klastra, Service Fabric sprawdza, czy węzeł ma za mało zasobów do obsługi pojemność zdefiniowanego przez użytkownika:
* Jeśli węzeł możliwości, które są zdefiniowane w manifeście są mniejsze niż lub równe dostępnych zasobów w węźle, Service Fabric korzysta możliwości, które są określone w manifeście.

* Jeśli węzeł możliwości, które są zdefiniowane w manifeście są większe od dostępnych zasobów, usługi sieć szkieletowa używa dostępnych zasobów jako możliwości węzła.

Automatyczne wykrywanie dostępnych zasobów można wyłączyć, jeśli nie jest wymagana. Aby ją wyłączyć, należy zmienić następujące ustawienia:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Aby uzyskać optymalną wydajność należy również włączone następujące ustawienie w manifeście klastra: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specify-resource-governance"></a>Określ zasób ładu 

Limity ładu zasobów są określone w manifeście aplikacji (sekcja ServiceManifestImport), jak pokazano w poniższym przykładzie:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

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
  
W tym przykładzie pakiet usługi o nazwie **ServicePackageA** pobiera jeden rdzeń w węzłach, na której jest umieszczona. Ten pakiet usługi zawiera dwa pakiety kodu (**CodeA1** i **CodeA2**), i określ zarówno `CpuShares` parametru. Część CpuShares 512:256 dzieli podstawowe w pakietach dwóch kodu. 

W związku z tym w tym przykładzie CodeA1 pobiera dwóch podstawowa i CodeA2 pobiera jedna trzecia podstawowa (i zastrzeżenie gwarancji soft tego samego). Jeśli nie określono CpuShares pakietów kodu, usługi sieć szkieletowa dzieli rdzeni równomiernie między nimi.

Limity pamięci są bezwzględne, oba pakiety kodu są ograniczone do 1024 MB pamięci (i zastrzeżenie gwarancji soft tego samego). Pakiety kodu (kontenery lub procesów) nie można przydzielić więcej pamięci niż ten limit i próby tak powoduje wyjątek braku pamięci. Aby wymuszanie limitu zasobów działało, wszystkie pakiety kodu w ramach pakietu usług powinny mieć określone limity pamięci.

### <a name="using-application-parameters"></a>Przy użyciu parametrów aplikacji

Podczas określania ładu zasobów jest możliwość użycia [parametry aplikacji](service-fabric-manage-multiple-environment-app-configuration.md) do zarządzania wielu konfiguracji aplikacji. W poniższym przykładzie przedstawiono sposób użycia parametrów aplikacji:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

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

W tym przykładzie domyślne wartości parametrów są ustawiane w środowisku produkcyjnym, gdzie każdy pakiet usługi jak 4 rdzenie i 2 GB pamięci. Istnieje możliwość zmiany domyślnych wartości z pliki parametrów aplikacji. W tym przykładzie jednego pliku parametrów może służyć do testowania aplikacji lokalnie, gdzie ona otrzyma mniej zasobów niż w środowisku produkcyjnym: 

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
> Określanie ładu zasobów z parametrami aplikacji jest dostępnych w programie Service Fabric w wersji 6.1.<br> 
>
> Parametry aplikacji używanego do określania ładu zasobów sieci szkieletowej usług nie można obniżyć wersji przed wersji 6.1. 


## <a name="other-resources-for-containers"></a>Inne zasoby dla kontenerów
Oprócz Procesora i pamięci jest można określić limity innych zasobów dla kontenerów. Ograniczenia te są określone na poziomie pakietu kodu i są stosowane po uruchomieniu kontenera. W przeciwieństwie do Procesora i pamięci, Menedżer zasobów klastra nie jest pamiętać o tych zasobów i nie czy kontroli zdolności lub równoważenia obciążenia dla nich. 

* *MemorySwapInMB*: ilość pamięci wymiany, używaną przez kontener.
* *MemoryReservationInMB*: miękki limit ładu pamięci, które są wymuszane tylko w przypadku wykrycia rywalizacji pamięci w węźle.
* *CpuPercent*: procent Procesora używanego w kontenerze. Limity CPU są określone dla pakietu usługi, ten parametr skutecznie jest ignorowana.
* *MaximumIOps*: Maksymalna liczba IOPS używaną przez kontener (odczytu i zapisu).
* *MaximumIOBytesps*: maksymalna we/wy (w bajtach na sekundę) używanego w kontenerze (odczytu i zapisu).
* *BlockIOWeight*: We/Wy bloku wagi dla względem innych kontenerów.

Te zasoby można łączyć z Procesora i pamięci. Poniżej przedstawiono przykładowy sposób określić dodatkowe zasoby dla kontenerów:

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
* Aby dowiedzieć się więcej na temat Menedżera zasobów klastra, przeczytaj [wprowadzenie Menedżera zasobów klastra usługi sieć szkieletowa](service-fabric-cluster-resource-manager-introduction.md).
* Dowiedz się więcej o modelu aplikacji, usługi pakiety i pakiety kodu — i jak replik mapy do nich — odczytu [modelu aplikacji w sieci szkieletowej usług](service-fabric-application-model.md).
