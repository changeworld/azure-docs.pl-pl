---
title: Menedżer zasobów klastra usługi sieć szkieletowa — zasad umieszczania | Dokumentacja firmy Microsoft
description: Omówienie zasad umieszczania dodatkowe i zasady usługi sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 66c51b08884c9d7a4d522c94f7b81774ec7a8bda
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642006"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zasady umieszczania dla usługi sieci szkieletowej usług
Zasady umieszczania są dodatkowe reguły, które mogą służyć do kierowania rozmieszczenie usługi, w niektórych scenariuszach określone, mniej typowe. Przykłady takich scenariuszy to:

- Klaster usługi Service Fabric obejmuje odległości geograficznego, takich jak wiele lokalnych centrów danych lub w regionach platformy Azure
- Środowiska obejmuje wiele obszarów kontrolki geograficznymi lub prawnych lub innym przypadku, gdy masz zasad granice muszą zapewniać
- Brak komunikacji wydajności lub opóźnień uwagi ze względu na duże odległości lub użyj łączy sieciowych wolniejszych lub bardziej zawodne
- Należy zachować pewne obciążeń rozmieszczony wspólnie jako starań, z innymi obciążeniami lub w pobliżu klientów

Większość wymagań dostosowanie fizycznego układu klastra reprezentowane jako domeny awarii klastra. 

Zasady umieszczania zaawansowane, które wyjść naprzeciw te scenariusze są następujące:

1. Nieprawidłowy domen
2. Wymagane domen
3. Wykorzystanie preferowanych domen
4. Brak zezwolenia pakowania repliki

Większość następujących formantów można skonfigurować za pomocą właściwości węzła i ograniczenia dotyczące umieszczania, ale niektóre są bardziej skomplikowane. Z prośbą prostszy, Menedżer zasobów klastra sieci szkieletowej usług zawiera zasady te dodatkowe umieszczania. Zasady umieszczania są konfigurowane na podstawie wystąpienia na nazwę usługi. Mogą być aktualizowane również dynamicznie.

## <a name="specifying-invalid-domains"></a>Określanie domen nieprawidłowy
**InvalidDomain** zasady rozmieszczania pozwala określić, że określonej domeny błędów jest nieprawidłowa dla określonej usługi. Ta zasada zapewnia, że określonej usługi nigdy nie uruchamia się w określonym obszarze, na przykład z powodów polityki geograficznymi lub firmowych. Przy użyciu oddzielnych zasad można określić wielu domen nieprawidłowy.

<center>
![Nieprawidłowa domena przykład][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Określanie wymaganego domen
Zasady rozmieszczania wymaganej domeny wymaga, że usługa jest obecne tylko w określonej domenie. Przy użyciu oddzielnych zasad można określić wielu domen wymagana.

<center>
![Przykład wymaganej domeny][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Określanie preferowanych domen dla repliki podstawowej usługi stanowej
Preferowany domeny głównej Określa domeny błędów można umieścić w podstawowej. Podstawowy kończy się w tej domenie, gdy wszystko jest w dobrej kondycji. Jeśli domeny lub replika podstawowa nie powiedzie się lub kończy pracę, podstawowego przenosi do innej lokalizacji, najlepszym rozwiązaniem w tej samej domenie. Jeśli tej nowej lokalizacji nie ma preferowanych domen, Menedżer zasobów klastra jego przeniesienie preferowanych domen tak szybko, jak to możliwe. Oczywiście to ustawienie ma sens tylko dla stanowych usług. Ta zasada jest najbardziej przydatna w klastrach, które są łączone w regionach platformy Azure lub wiele centrów danych, ale ma usług, które preferowane umieszczania w określonej lokalizacji. Utrzymywanie kolory podstawowe bliski użytkowników lub innych usług pomaga zapewnić mniejsze opóźnienia, zwłaszcza w przypadku odczytów, które są obsługiwane przez kolory podstawowe domyślnie.

<center>
![Wykorzystanie preferowanych domen podstawowe i pracy awaryjnej][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Wymaganie dystrybucji repliki i brak zezwolenia pakowania
Repliki są _zwykle_ rozproszonych w domenach awarii i uaktualniania, gdy klaster jest w dobrej kondycji. Istnieją jednak przypadki, w których może mieć więcej niż jednej repliki dla danej partycji tymczasowo spakowana do jednej domeny. Na przykład, załóżmy, że klaster ma dziewięć węzłów w trzech domen błędów, fd: / 0, fd: / 1 i fd: / 2. Również Załóżmy, że usługa ma trzy repliki. Załóżmy, że węzły, które były używane przez te repliki w fd: / 1 i fd: / 2 zakończył działanie. Zwykle Menedżera zasobów klastra wolisz inne węzły w tych samym domen błędów. W takim przypadku Załóżmy, że z powodu problemów pojemności się, że żaden z innych węzłów w tych domenach nie był nieprawidłowy. Jeśli Menedżer zasobów klastra kompilacje zastępujące tych replik, będą musiały być Wybierz węzły w fd: / 0. Jednak podczas _który_ sytuację, w którym nastąpiło naruszenie ograniczenia domeny błędów. Pakowanie replik zwiększa ryzyko, że cały repliki można przestaną działać lub zostać utracone. 

> [!NOTE]
> Aby uzyskać więcej informacji na temat ograniczeń oraz ograniczenia, priorytetów ogólnie rzecz biorąc, zapoznaj się z [w tym temacie](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Jeśli kiedykolwiek w tym samouczku komunikat kondycji takich jak "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", a następnie został trafiony tego warunku lub przypominać go. Zwykle tylko jedną lub dwie repliki są pakowane tymczasowo. Tak długo, jak są mniej niż kworum replik w danej domenie, możesz bezpieczne. Opakowanie jest rzadko, jednak może się zdarzyć, oraz zwykle tych sytuacji przejściowej ponieważ węzły wrócić. Jeśli węzły pozostanie w dół i Menedżer zasobów klastra konieczne kompilacji wymiany, zazwyczaj dostępne są inne węzły w domenach awarii idealne.

Niektórych obciążeń wolisz zawsze używania docelowej liczby replik, nawet jeśli ich są pakować do mniejszej liczby domen. Te obciążenia są stawiając przed awariami całkowita liczba jednoczesnych domeny stałe i zwykle można odzyskać stanu lokalnego. Inne obciążenia zamiast wymagałoby wcześniejszej niż poprawności ryzyko lub utraty danych Przestój. Uruchom większości obciążeń produkcyjnych z więcej niż trzy repliki, więcej niż trzy domen błędów i wiele węzłów prawidłowy na domeny błędów. W związku z tym domyślne zachowanie umożliwia pakowania domeny domyślnie. Domyślne zachowanie umożliwia normalne balancing and failover, aby obsługiwać te ekstremalnych przypadkach, nawet w przypadku oznacza to, że tymczasowe domeny pakowania.

Jeśli chcesz wyłączyć takie pakowania dla danego obciążenia, można określić `RequireDomainDistribution` zasad w usłudze. Gdy ta zasada jest ustawiona, Menedżer zasobów klastra gwarantuje, że nie dwóch replik z tej samej partycji uruchomić w tej samej domenie awarii lub uaktualnienia.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Teraz czy można używać tych konfiguracji dla usług w klastrze, który nie został geograficznie łączone? Użytkownik może, ale nie ma dużą Przyczyna zbyt. O ile nie wymagają scenariuszy należy unikać konfiguracjach wymaganych, nieprawidłowy i preferowanych domen. Go nie ma sensu wszelkie próby wymusić danego obciążenia można uruchomić w jednym stojaku lub preferować niektórych segmentu klastra lokalnego zamiast innego. Różne konfiguracje sprzętu należy rozłożyć na domenach awarii i obsługiwane za pośrednictwem ograniczenia umieszczania normalne i właściwości węzła.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usługi [Dowiedz się więcej o konfigurowaniu usługi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
