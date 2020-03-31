---
title: Menedżer zasobów klastra sieci szkieletowej usług — zasady umieszczania
description: Omówienie dodatkowych zasad i reguł umieszczania usług sieci szkieletowej usług
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834398"
---
# <a name="placement-policies-for-service-fabric-services"></a>Zasady umieszczania usług sieci szkieletowej usług
Zasady umieszczania są dodatkowe reguły, które mogą służyć do regulowania umieszczania usług w niektórych konkretnych, mniej typowych scenariuszy. Oto kilka przykładów tych scenariuszy:

- Klaster sieci szkieletowej usług obejmuje odległości geograficzne, takie jak wiele lokalnych centrów danych lub w regionach platformy Azure
- Środowisko obejmuje wiele obszarów kontroli geopolitycznej lub prawnej lub inne przypadki, w których masz granice zasad, które musisz wymusić
- Istnieją zagadnienia dotyczące wydajności lub opóźnienia komunikacji z powodu dużych odległości lub korzystania z wolniejszych lub mniej niezawodnych łączy sieciowych
- Należy zachować pewne obciążenia kolokowane jako najlepszy wysiłek, zarówno z innymi obciążeniami, jak i w pobliżu klientów

Większość z tych wymagań jest zgodna z fizycznym układem klastra, reprezentowanym jako domeny błędów klastra. 

Zaawansowane zasady umieszczania, które pomagają rozwiązać te scenariusze są następujące:

1. Nieprawidłowe domeny
2. Wymagane domeny
3. Preferowane domeny
4. Niezezwolanie na pakowanie replik

Większość z następujących formantów można skonfigurować za pomocą właściwości węzła i ograniczenia umieszczania, ale niektóre są bardziej skomplikowane. Aby uprościć, Menedżer zasobów klastra sieci szkieletowej usług udostępnia te dodatkowe zasady umieszczania. Zasady umieszczania są konfigurowane na podstawie wystąpienia usługi o nazwie. Można je również aktualizować dynamicznie.

## <a name="specifying-invalid-domains"></a>Określanie nieprawidłowych domen
Zasady umieszczania **invalidDomain** umożliwiają określenie, że określona domena błędów jest nieprawidłowa dla określonej usługi. Polityka ta gwarantuje, że dana usługa nigdy nie działa w określonym obszarze, na przykład ze względów polityki geopolitycznej lub korporacyjnej. Wiele nieprawidłowych domen można określić za pomocą oddzielnych zasad.

<center>

![Przykład nieprawidłowej domeny][Image1]
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
## <a name="specifying-required-domains"></a>Określanie wymaganych domen
Wymagane zasady umieszczania domeny wymagają, aby usługa była obecna tylko w określonej domenie. Wiele wymaganych domen można określić za pomocą oddzielnych zasad.

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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Określanie preferowanej domeny dla replik podstawowych usługi stanowej
Preferowana domena podstawowa określa domenę błędów, w która ma być umieszczana jako podstawowa. Podstawowa kończy się w tej domenie, gdy wszystko jest w dobrej kondycji. Jeśli domena lub replika podstawowa ulegnie awarii lub zostanie zamknięta, podstawowa zostanie przeniesiony do innej lokalizacji, najlepiej w tej samej domenie. Jeśli ta nowa lokalizacja nie znajduje się w preferowanej domenie, Menedżer zasobów klastra przenosi ją z powrotem do preferowanej domeny tak szybko, jak to możliwe. Oczywiście to ustawienie ma sens tylko dla usług stanowych. Ta zasada jest najbardziej przydatne w klastrach, które są łączone w regionach platformy Azure lub wielu centrów danych, ale mają usługi, które preferują umieszczanie w określonej lokalizacji. Utrzymywanie prawyborów blisko swoich użytkowników lub innych usług pomaga zapewnić mniejsze opóźnienia, szczególnie dla odczytów, które są obsługiwane przez zasady podstawowe domyślnie.

<center>

![Preferowane domeny podstawowe i trybu failover][Image3]
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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Wymaganie dystrybucji replik i niezezwolnienia na pakowanie
Repliki są _zwykle rozproszone_ między domenami błędów i uaktualniania, gdy klaster jest w dobrej kondycji. Istnieją jednak przypadki, w których więcej niż jedna replika dla danej partycji może zostać tymczasowo spakowana do jednej domeny. Załóżmy na przykład, że klaster ma dziewięć węzłów w trzech domenach błędów: fd:/0, fd:/1 i fd:/2. Załóżmy również, że usługa ma trzy repliki. Załóżmy, że węzły, które były używane dla tych replik w fd:/1 i fd:/2 poszedł w dół. Zwykle Menedżer zasobów klastra wolałby inne węzły w tych samych domenach błędów. W takim przypadku załóżmy, że z powodu problemów z pojemnością żaden z innych węzłów w tych domenach nie był prawidłowy. Jeśli Menedżer zasobów klastra tworzy zamienniki dla tych replik, musiałby wybrać węzły w fd:/0. Jednak w ten sposób tworzy _sytuację,_ w której naruszenie ograniczenia domeny błędu. Pakowanie replik zwiększa prawdopodobieństwo, że cały zestaw replik może zejść w dół lub zostać utracone. 

> [!NOTE]
> Aby uzyskać więcej informacji na temat ograniczeń i priorytetów ograniczeń ogólnie, zapoznaj się z [tym tematem](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Jeśli kiedykolwiek widziałeś komunikat o zdrowiu, taki jak "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", to trafiłeś w ten stan lub coś w tym stylu. Zazwyczaj tylko jedna lub dwie repliki są tymczasowo spakowane razem. Tak długo, jak istnieje mniej niż kworum replik w danej domenie, jesteś bezpieczny. Pakowanie jest rzadkie, ale może się zdarzyć, i zwykle te sytuacje są przejściowe, ponieważ węzły wrócić. Jeśli węzły pozostają w dół, a Menedżer zasobów klastra musi tworzyć zamienniki, zazwyczaj istnieją inne węzły dostępne w idealnych domenach błędów.

Niektóre obciążenia wolałyby zawsze mieć docelową liczbę replik, nawet jeśli są one pakowane w mniejszej liczbie domen. Te obciążenia są zakłady na całkowitą jednoczesne trwałe błędy domeny i zwykle można odzyskać stan lokalny. Inne obciążenia wolałyby wziąć przestoje wcześniej niż korekta ryzyka lub utrata danych. Większość obciążeń produkcyjnych jest uruchamiana z więcej niż trzema replikami, więcej niż trzema domenami błędów i wieloma prawidłowymi węzłami na domenę błędów. Z tego powodu domyślne zachowanie domyślnie zezwala na pakowanie domeny domyślnie. Domyślne zachowanie umożliwia normalne równoważenie i praca awaryjna do obsługi tych skrajnych przypadkach, nawet jeśli oznacza to tymczasowe pakowania domeny.

Jeśli chcesz wyłączyć takie pakowanie dla danego obciążenia, `RequireDomainDistribution` można określić zasady w usłudze. Po ustawieniu tej zasady Menedżer zasobów klastra zapewnia, że nie dwie repliki z tej samej partycji są uruchamiane w tej samej domenie błędów lub uaktualnienia.

Kod:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Teraz, czy można użyć tych konfiguracji dla usług w klastrze, który nie był geograficznie łączony? Można, ale nie ma też wielkiego powodu. Należy unikać konfiguracji domeny wymagane, nieprawidłowe i preferowane, chyba że scenariusze wymagają ich. Nie ma sensu próbować wymusić danego obciążenia do uruchomienia w jednym stojaku lub preferować jakiś segment klastra lokalnego nad innym. Różne konfiguracje sprzętu powinny być rozłożone na domeny błędów i obsługiwane za pośrednictwem normalnych ograniczeń umieszczania i właściwości węzła.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
