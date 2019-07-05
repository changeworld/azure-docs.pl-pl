---
title: Planowanie pojemności i skalowania dla usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące planowania i skalowanie klastrów usługi Service Fabric i aplikacji.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444718"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planowanie wydajności i skalowania dla usługi Azure Service Fabric

Zanim utworzysz dowolnego klastra usługi Azure Service Fabric, lub Skaluj zasoby obliczeniowe, które Klaster hosta, koniecznie planowania pojemności. Aby uzyskać więcej informacji na temat planowania pojemności, zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Aby uzyskać dalsze wskazówki skalowalności klastra, zobacz [zagadnienia dotyczące skalowalności usługi Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Oprócz biorąc pod uwagę węzła właściwości typu i klastra, należy się spodziewać operacji skalowania trwać dłużej niż godzinę, aby zakończyć w środowisku produkcyjnym. Ta kwestia obowiązuje niezależnie od liczby maszyn wirtualnych w przypadku dodawania.

## <a name="autoscaling"></a>Skalowanie automatyczne
Należy wykonać operacje skalowania za pomocą szablonów usługi Azure Resource Manager, ponieważ jest ona najlepszym rozwiązaniem, aby traktować [konfiguracje zasobów jako kod]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Przy użyciu skalowania automatycznego za pomocą zestawów skalowania maszyn wirtualnych spowoduje, że numerów wersji szablonu usługi Resource Manager niedokładnie zdefiniować liczbę wystąpień dla zestawów skalowania maszyn wirtualnych. Definicja niedokładne zwiększa ryzyko, że przyszłych wdrożeniach spowoduje niepożądanych operacji skalowania. Ogólnie rzecz biorąc należy używać automatycznego skalowania, jeśli:

* Wdrażanie szablonów usługi Resource Manager przy użyciu odpowiednią pojemność zadeklarowana nie obsługuje danego przypadku użycia.
     
   Oprócz skalowanie ręczne, możesz skonfigurować [ciągłej integracji i potoku dostaw w usługom DevOps platformy Azure przy użyciu projekty wdrażania grup zasobów platformy Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Ten potok jest najczęściej wyzwalane przez aplikację logiki, która korzysta z metryk wydajności maszyny wirtualnej z kwerendy [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Potok efektywnego skalowania na podstawie na dowolnych metryki ma, przy jednoczesnej optymalizacji dla szablonów usługi Resource Manager.
* Musisz skalować w poziomie tylko jeden węzeł zestawu skalowania maszyn wirtualnych w danym momencie.
   
   Aby skalować w poziomie przez co najmniej trzy węzły w czasie, należy [skalowanie klastra usługi Service Fabric, dodając zestaw skalowania maszyn wirtualnych](virtual-machine-scale-set-scale-node-type-scale-out.md). Jest najbezpieczniejszym umożliwiającą skalowanie w pionie i skalowania w poziomie skalowania maszyn wirtualnych w poziomie, ustawia jeden węzeł w danym momencie.
* Masz niezawodność Silver lub nowszej dla klastra usługi Service Fabric i Silver trwałości lub nowszego w dowolnej skali, w którym możesz skonfigurować reguły skalowania automatycznego.
  
   Minimalna wydajność dla reguły skalowania automatycznego musi być równa lub większa niż pięć wystąpień maszyny wirtualnej. Ponadto należy, równa lub większa niż wartość minimalna z warstwy niezawodności dla typu węzła podstawowego.

> [!NOTE]
> Stanowe usługi Service fabric Service Fabric: / System/InfastructureService/< NODE_TYPE_NAME > działa na każdy typ węzła, który ma trwałości Silver lub wyższego. To usługa jedyny system, który jest obsługiwany na platformie Azure na żadnym z typów węzła klastrów.

## <a name="vertical-scaling-considerations"></a>Zagadnienia skalowanie pionowe

[Skalowanie w pionie](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) typ węzła w usłudze Azure Service Fabric wymaga kilku procedury i zagadnienia. Na przykład:

* Klaster musi być w dobrej kondycji przed skalowaniem. W przeciwnym razie będzie zdestabilizować dalsze klastra.
* Silver trwałości, poziomu lub nowszej jest wymagana dla wszystkich usługi Service Fabric typy węzłów klastra, które hostują usług stanowych.

> [!NOTE]
> Danego typu węzła podstawowego, który jest hostem stanowej usługi systemowe Service Fabric musi być Silver trwałości, poziomu lub nowszej. Po włączeniu Silver trwałość, klaster operacje, takie jak uaktualnienia, dodawanie lub usuwanie węzłów i tak dalej będzie przebiegać wolniej, ponieważ system optymalizację pod kątem bezpieczeństwa danych nad szybkość działania.

Skalowanie zestawu skalowania maszyn wirtualnych w pionie jest operacją destrukcyjną. Zamiast tego należy skalować w poziomie klaster przez dodanie nowego zestawu skalowania z odpowiednią jednostkę SKU. Następnie należy przeprowadzić migrację usług do żądanej jednostki SKU i ukończyć bezpieczne pionowy operacji skalowania. Zmiana jednostki SKU zasobu zestawu skalowania maszyny wirtualnej jest operacją destrukcyjną, ponieważ jego odtwarza z obrazu hostów, które powoduje usunięcie wszystkich lokalnie trwały stan.

Klaster używa usługi Service Fabric [— właściwości węzła i ograniczeniami dotyczącymi umieszczania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) zdecydować, czy do hostowania usługi aplikacji. Podczas skalowania w pionie typu węzła podstawowego, należy zadeklarować wartości właściwości identyczne `"nodeTypeRef"`. Te wartości można znaleźć w rozszerzeniu usługi Service Fabric dla zestawów skalowania maszyn wirtualnych. 

Poniższy fragment kodu szablonu usługi Resource Manager zawiera właściwości, które będzie zadeklarować. Ma taką samą wartość dla zestawów skalowania użytkownicy nowo aprowizowanych, nastąpiło skalowanie w celu, które jest obsługiwane tylko jako tymczasowy stanowej usługi dla klastra.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nie należy pozostawiać klaster za pomocą wielu zestawów skalowania, które używały tych samych `nodeTypeRef` dłużej niż wartość właściwości wymagane do wykonania pomyślnego uruchomienia operacji skalowania pionowy.
>
> Sprawdzanie poprawności działania w środowiskach testowych przed próbą zmiany w środowisku produkcyjnym. Domyślnie usługi systemowe klastra usługi Service Fabric mają ograniczenie umieszczania, typ podstawowy węzeł docelowy.

Właściwości węzła i ograniczeniami dotyczącymi umieszczania zadeklarowany wykonaj następujące kroki w jednym wystąpieniu maszyny Wirtualnej w danym momencie. Dzięki temu usługi systemowe (i usługi stanowe) na łagodne zamykanie na wystąpieniu maszyny Wirtualnej, który usuwasz jako nowej repliki powstają w innym miejscu.

1. Za pomocą programu PowerShell, uruchom `Disable-ServiceFabricNode` z zamiarem `RemoveNode` można wyłączyć w węźle, które zamierzasz usunąć. Usuń typ węzła, który ma najwyższy numer. Na przykład w przypadku klastra z węzłami sześciu usunąć wystąpienia maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` aby upewnić się, że węzeł zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Może to potrwać kilka godzin w każdym węźle. Nie Kontynuuj, dopóki nie przeszła węzeł na wyłączone.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tego typu węzła. Obecnie najwyższy wystąpienia maszyny Wirtualnej zostaną usunięte.
4. Powtórz kroki od 1 do 3, zgodnie z potrzebami, ale nigdy nie skalowania w zależności od liczby wystąpień w typach węzła podstawowego mniejszą niż gwarantuje warstwy niezawodności. Zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) listę zalecanych wystąpień.
5. Gdy wszystkie maszyny wirtualne zostaną usunięte (reprezentowana jako "W dół") sieci szkieletowej: / System/InfrastructureService / [nazwa węzła] pojawi się stan błędu. Następnie można zaktualizować zasobu klastra, aby usunąć typ węzła. Możesz użyć wdrażanie szablonu ARM, lub Edytuj zasób klastra za pośrednictwem [usługi Azure resource manager](https://resources.azure.com). Spowoduje to uruchomienie uaktualniania klastra, co spowoduje usunięcie sieci szkieletowej: / System/InfrastructureService / [typ węzła] usługi, który jest w stanie błędu.
 6. Po, opcjonalnie można usunąć zestawu skalowania maszyn wirtualnych, będą również widzieć węzłów jako "W dół" w narzędziu Service Fabric Explorer wyświetlić jednak. Ostatnim krokiem powinno być pora to oczyścić za pomocą `Remove-ServiceFabricNodeState` polecenia.

### <a name="example-scenario"></a>Przykładowy scenariusz
Jest to obsługiwany scenariusz, kiedy do wykonywania operacji skalowania pionowy: użytkownik chce migrować sieci Web i aplikacji klastra usługi Service Fabric z dysków niezarządzanych do usługi managed disks bez przestojów aplikacji. 

Aprowizacja nowego zestawu skalowania maszyn wirtualnych z dyskami zarządzanymi i wykonaj uaktualnienie aplikacji z ograniczeniami dotyczącymi umieszczania, których platformą docelową zaprowizowaną pojemnością. Klaster usługi Service Fabric, następnie można zaplanować obciążenie na wydajność węzła aprowizowany klaster, która jest wdrażana przez domenę uaktualnienia bez przestojów aplikacji. 

Punkty końcowe puli zaplecza [jednostki SKU podstawowego modułu równoważenia obciążenia usługi Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) może być maszynami wirtualnymi w pojedynczym zestawie dostępności lub zestawie skalowania maszyn wirtualnych. Oznacza to, że nie można użyć podstawowa jednostka SKU modułu równoważenia obciążenia, po przeniesieniu aplikacji usługi Service Fabric systemów między zestawami skalowania, bez powodowania inaccessibility tymczasowe z punktu końcowego zarządzania klastrem usługi Service Fabric. Ta zasada obowiązuje, nawet jeśli klastra i jego aplikacji nadal działa.

Użytkownicy często aprowizacja modułu równoważenia obciążenia standardowej jednostki SKU podczas przeprowadzania wirtualnego wymiany adresu IP między równoważenia obciążenia podstawowej jednostki SKU i zasobów modułu równoważenia obciążenia standardowej jednostki SKU. Ta technika ogranicza wszystkie przyszłe inaccessibility wymagane dla adresów VIP zamianę około 30 sekund.

## <a name="horizontal-scaling"></a>skalowanie w poziomie,

Można zrobić, skalowanie w poziomie albo [ręcznie](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) lub [programowo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Jeśli nastąpiło skalowanie typ węzła, który ma niezawodności na poziomie Silver lub Gold, skalowanie jest powolne.

### <a name="scaling-out"></a>Skalowanie w poziomie

Skalowanie klastra usługi Service Fabric, zwiększając liczbę wystąpień dla konkretnej maszyny wirtualnej zestawu skalowania. Można skalować w poziomie programowo przy użyciu `AzureClient` i identyfikator żądanego zestawie skalowania, aby zwiększyć jej pojemność.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Aby skalować ręcznie, zaktualizuj pojemność we właściwości SKU żądaną [zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) zasobów.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skalowanie w

Skalowanie w wymaga większej uwagi niż skalowanie w poziomie. Na przykład:

* Uruchom usługi systemowe Service Fabric w typie podstawowym węzłem w klastrze. Nigdy nie jest zamknięty lub w dół liczbę wystąpień tego typu węzła, co wymaga mniejszej liczby wystąpień niż gwarantuje warstwy niezawodności. 
* Usługi stanowe konieczne jest liczba węzłów, które są zawsze się do zapewnienia dostępności i Zachowaj stan usługi. Jako minimum należy liczbę węzłów równa docelowej repliki zestaw liczbę partycji lub usługi.

Aby skalować w ręcznie, wykonaj następujące kroki:

1. Za pomocą programu PowerShell, uruchom `Disable-ServiceFabricNode` z zamiarem `RemoveNode` można wyłączyć w węźle, które zamierzasz usunąć. Usuń typ węzła, który ma najwyższy numer. Na przykład w przypadku klastra z węzłami sześciu usunąć wystąpienia maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` aby upewnić się, że węzeł zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Może to potrwać kilka godzin w każdym węźle. Nie Kontynuuj, dopóki nie przeszła węzeł na wyłączone.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tego typu węzła. Obecnie najwyższy wystąpienia maszyny Wirtualnej zostaną usunięte.
4. Powtórz kroki od 1 do 3, dopóki nie możesz aprowizować pojemność, która ma. Nie skalować w dół liczbę wystąpień w typach węzła podstawowego, aby była ona mniejsza niż gwarantuje warstwy niezawodności. Zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) listę zalecanych wystąpień.

Umożliwiającą skalowanie w pionie ręcznie, zaktualizuj pojemność we właściwości SKU żądaną [zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) zasobów.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Należy przygotować węzeł zamknięcia umożliwiającą skalowanie w pionie programowo. Dowiedz się, że węzeł, który ma zostać usunięty (węzeł najwyższego wystąpienie). Na przykład:

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Dezaktywowanie i usuwanie węzłów, korzystając z tych samych `FabricClient` wystąpienia (`client` w tym przypadku) i wystąpienie węzła (`instanceIdString` w tym przypadku) używanej w poprzednim kodzie:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Skalowanie w dół klastra, zobaczysz wystąpienia węzła usuniętych i maszyn wirtualnych, wyświetlana w złej kondycji w narzędziu Service Fabric Explorer. Opis tego zachowania zawiera [zachowania można zaobserwować w narzędziu Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Możesz:
> * Wywołaj [polecenia Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) o nazwie odpowiedni węzeł.
> * Wdrażanie [aplikacją pomocniczą automatycznego skalowania usługi Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) w klastrze. Tej aplikacji gwarantuje, że węzły skalowane w dół są usuwane z narzędzia Service Fabric Explorer.

## <a name="reliability-levels"></a>Poziom niezawodności

[Poziom niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) jest właściwością zasobu klastra usługi Service Fabric. Nie można skonfigurować inaczej dla typów pojedynczych węzłów. Określa współczynnik replikacji usług systemowych dla klastra, a ustawienie poziomie zasobów klastra. 

Poziom niezawodności określi, minimalna liczba węzłów, które musi być typu węzła podstawowego. Warstwa niezawodności może przyjmować następujące wartości:

* Platynowy: Uruchamia usługi systemowe wraz z liczbą zestawu replik docelowej siedmiu i dziewięć węzły obiektów początkowych.
* Złoty: Uruchamia usługi systemowe wraz z liczbą zestawu replik docelowej siedmiu lub siedmiu węzłów inicjatora.
* Srebrny: Docelowa liczba replik zestaw pięciu do pięciu węzłów inicjatora uruchamia usług systemowych.
* Brązowy: Uruchamia usługi systemowe wraz z liczbą docelowej repliki zestaw trzech i trzy węzły obiektów początkowych.

Poziom niezawodności zalecaną minimalną jest Silver.

Poziom niezawodności znajduje się w sekcji właściwości [zasobów Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), podobnie do następującego:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Poziom trwałości

> [!WARNING]
> Typy węzłów z trwałością brązowa uzyskać _brakiem uprawnień_. Zadania infrastruktury, które mają wpływ na obciążeń bezstanowych będzie nie można zatrzymać lub opóźnione, które mogą mieć wpływ na Twoich obciążeń. 
>
> Na użytek trwałości brązowa tylko typy węzłów, działających obciążeń bezstanowych. W przypadku obciążeń produkcyjnych Uruchom Silver lub nowszej, aby zapewnić stan spójności. Wybierz odpowiednie niezawodność, w oparciu o wskazówki zawarte w [dokumentacji planowania pojemności na potrzeby](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Poziom trwałości musi być ustawiona w dwóch zasobów. Jeden jest profil rozszerzenia [zasobów zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Inny zasób podlega `nodeTypes` w [zasobów Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
