---
title: Menedżer zasobów klastra usługi Service Fabric — zasady umieszczania | Dokumentacja firmy Microsoft
description: Omówienie umieszczania dodatkowych zasad i reguł dla usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d5aea441f15cbf7a2a444439c06cd5f74a559d3f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670340"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zasady umieszczania dla usługi Service fabric
Zasady rozmieszczania są dodatkowe reguły, które mogą służyć do zarządzania usługi umieszczania w niektórych scenariuszach określone, mniej znane. Przykłady takich scenariuszy to:

- Klaster usługi Service Fabric rozmieszczonych w odległości geograficznych, takich jak wiele lokalnych centrów danych lub w wielu regionach platformy Azure
- Środowiska obejmuje wiele obszarów formantu geopolitycznych lub prawnych lub innym przypadku, gdy masz zasady granice muszą zapewniać
- Istnieją zagadnienia wydajności lub opóźnienie komunikacji ze względu na duże odległości lub użyj łączy wolniej lub mniej niezawodnej sieci
- Należy zachować pewne obciążeń zlokalizowana jako najlepszy nakład pracy, przy użyciu innych obciążeń lub w pobliżu klientów

Większość z tych wymagań dostosowanie fizyczny układ klastra, reprezentowane jako domeny awarii klastra. 

Zasady umieszczania zaawansowanych, które adres te scenariusze są następujące:

1. Nieprawidłowy domen
2. Wymagane domen
3. Preferowany domen
4. Nie można przydzielać pakowania repliki

Większość następujące elementy sterujące można skonfigurować za pomocą właściwości węzła i ograniczeniami dotyczącymi umieszczania, ale niektóre są bardziej skomplikowane. Aby było prostsze, Menedżer zasobów klastra usługi Service Fabric udostępnia te zasady umieszczania dodatkowe. Zasady rozmieszczania są skonfigurowane na podstawie wystąpienia usługi na o silnych nazwach. One może też być aktualizowana dynamicznie.

## <a name="specifying-invalid-domains"></a>Określanie nieprawidłowy domen
**InvalidDomain** zasady rozmieszczania pozwala określić, że określonej domeny błędów jest nieprawidłowy dla określonej usługi. Te zasady zapewniają, że określonej usługi nigdy nie uruchamia się w określonym obszarze, na przykład ze względów geopolitycznych lub firmowe zasady. Wiele domen nieprawidłowy może być określona za pomocą odrębnych zasad.

<center>

![Przykład Nieprawidłowa domena][Image1]
</center>

Kod:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Określenie wymaganych domen
Zasady rozmieszczania wymaganej domeny wymaga, że usługa jest obecna tylko w określonej domenie. Przy użyciu oddzielnych zasad można określić wiele domen wymagana.

<center>

![Przykład wymaganej domeny][Image2]
</center>

Kod:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Określanie domeny preferowanych dla repliki podstawowej usługi stanowej
Domena podstawowa preferowane Określa domeny błędów można umieścić podstawowy w. Podstawowy kończy się w tej domenie, gdy wszystko jest w dobrej kondycji. Jeśli domeny znajduje się replika podstawowa nie powiedzie się lub kończy pracę, podstawowy przenosi się do innej lokalizacji, najlepiej w tej samej domenie. Jeśli nie ma tej nowej lokalizacji domeny preferowanych, Menedżer zasobów klastra jego przeniesienie domeny preferowanych tak szybko, jak to możliwe. Naturalnie to ustawienie tylko ma sens dla usług stanowych. Te zasady są najbardziej przydatne w klastrach, które są łączone w różnych regionach platformy Azure lub w wielu centrach danych, ale ma usług, które wolą umieszczania w określonej lokalizacji. Utrzymywanie kolory podstawowe blisko ich użytkowników lub innych usług pomaga w zapewnieniu mniejsze opóźnienia, zwłaszcza w przypadku operacji odczytu, które są obsługiwane przez kolory podstawowe domyślnie.

<center>

![Preferowany domeny głównej i trybu Failover][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Wymaganie repliki dystrybucji i nie można przydzielać pakowania
Repliki są _zwykle_ rozproszone w domenach błędów i uaktualnień, gdy klaster jest w dobrej kondycji. Istnieją jednak przypadki, w których więcej niż jednej repliki dla danej partycji mogą kończyć się tymczasowo spakowane do jednej domeny. Na przykład, załóżmy, że klaster ma dziewięciu węzłów w trzy domeny błędów, fd: / 0, fd: / 1 i fd: / 2. Ponadto Załóżmy, że usługa ma trzy repliki. Załóżmy, że węzły, które były używane dla tych replik w fd: / 1 i fd: / 2 zakończył działanie. Zwykle Menedżer zasobów klastra chcesz użyć innych węzłów w tych tych samych domenach błędów. W tym przypadku Załóżmy, że z powodu problemów z pojemnością, że żaden z innych węzłów w tych domenach był nieprawidłowy. Jeśli Menedżer zasobów klastra jest kompilowany zamiany tych replik, będą musiały być Wybierz węzły fd: / 0. Jednakże, wykonując _,_ sytuację, w której nastąpiło naruszenie ograniczenia domeny błędów. Pakowanie replik zwiększa ryzyko, że cały repliki można obniżyć lub zostać utracone. 

> [!NOTE]
> Aby uzyskać więcej informacji na temat ograniczeń i ograniczenia, priorytetów ogólnie rzecz biorąc, zapoznaj się [w tym temacie](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Jeśli nigdy nie widzisz komunikat kondycji takich jak "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", a następnie osiągnięto ten warunek, lub coś, co podoba Ci się. Zazwyczaj jedną lub dwie repliki są pakowane razem tymczasowo. Tak długo, jak są mniej niż kworum replik w danej domenie, wszystko jest bezpieczne. Pakowanie jest rzadkie, ale może się zdarzyć, i zazwyczaj są to przejściowy od czasu możesz wrócić w węzłach. Jeśli węzły pozostają w dół, a Menedżer zasobów klastra musi stworzyć zamiany, zwykle dostępnych innych węzłów w domenach błędów idealnym rozwiązaniem.

Niektórych obciążeń wolisz, zawsze po docelowej liczby replik, nawet jeśli zapakowane do mniejszej liczby domen. Te obciążenia są zakład awariami łączna liczba jednoczesnych trwałej domeny i zazwyczaj można odzyskać stan lokalnego. Inne obciążenia zamiast zajęłoby wcześniejszej niż poprawność ryzyko lub utraty danych przestoju. Uruchom większości obciążeń produkcyjnych z więcej niż trzech replik, więcej niż trzy domeny błędów i wiele węzłów prawidłowy dla domeny błędów. W związku z tym domyślne zachowanie umożliwia pakowanie domeny domyślnie. Domyślne zachowanie umożliwia normalne równoważenia i trybu failover do obsługi tych ekstremalnych przypadkach, nawet w przypadku oznacza to, że pakowanie tymczasowej domeny.

Jeśli chcesz wyłączyć takie pakowania dla określonego obciążenia, możesz określić `RequireDomainDistribution` zasad w usłudze. Gdy ta zasada jest ustawiona, Menedżer zasobów klastra gwarantuje, że nie dwie repliki z tej samej partycji uruchamiane w tej samej domenie błędów lub uaktualnienia.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Teraz go będzie możliwe korzystanie z tych konfiguracji dla usług w klastrze, który nie został geograficznie łączone? Można wykonać następujące akcje, ale nie ma nam podać doskonały powód zbyt. Konfiguracje wymagane nieprawidłowy i preferowaną domeny należy unikać chyba, że scenariusze wymagają. Nie ma sensu wszelkie próby wymusić danego obciążenia, aby uruchomić w jednym stojaku lub preferowanie niektóre segmentu klastrem lokalnym za pośrednictwem innego. Różne konfiguracje sprzętu powinien być rozmieszczone w domenach błędów i obsługiwane za pośrednictwem właściwości węzła i ograniczeniami dotyczącymi umieszczania normalny.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług [Dowiedz się więcej na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
