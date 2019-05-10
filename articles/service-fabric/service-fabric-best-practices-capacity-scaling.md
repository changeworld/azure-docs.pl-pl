---
title: Usługa Azure Service Fabric do planowania wydajności i najlepsze rozwiązania dotyczące skalowania | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c72392e46805049703300dd6f60fc7bf08b9053b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235763"
---
# <a name="capacity-planning-and-scaling"></a>Planowanie wydajności i skalowania

Przed utworzeniem dowolnego klastra usługi Azure Service Fabric lub skalowanie zasobów obliczeniowych hostingu klastra, ważne jest planowania pojemności. Aby uzyskać więcej informacji na temat planowania pojemności, zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Aby uzyskać dalsze najlepszym rozwiązaniem jest Zobacz wskazówki dotyczące skalowalności klastra [zagadnienia dotyczące skalowalności usługi Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

Oprócz biorąc pod uwagę charakterystyki węzła typu i klastra, należy również zaplanować operacje trwać dłużej niż godzinę, aby zakończyć w środowisku produkcyjnym, bez względu na liczbę maszyn wirtualnych, które dodajesz skalowania.

## <a name="auto-scaling"></a>Auto Scaling
Operacje skalowania należy wykonywać za pomocą wdrażania szablonu zasobów platformy Azure, ponieważ jest najlepszym rozwiązaniem, aby traktować [konfiguracje zasobów jako kod]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)i za pomocą zestawu skalowania maszyn wirtualnych, skalowanie automatyczne spowoduje usługi numerów wersji szablonu usługi Resource Manager niedokładnie Definiowanie skalowania maszyny wirtualnej Ustaw liczbę wystąpień; zwiększenie ryzyka dla przyszłych wdrożeniach, co powoduje niezamierzone operacje skalowania i ogólnie rzecz biorąc należy używać automatycznego skalowania, jeśli:

* Wdrażanie szablonów usługi Resource Manager przy użyciu odpowiednią pojemność zadeklarowana nie obsługuje danego przypadku użycia.
  * Oprócz skalowanie ręczne, możesz skonfigurować [ciągłej integracji i potoku dostaw w usługom DevOps platformy Azure przy użyciu projekty wdrażania grupy zasobów platformy Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), które często jest wyzwalane przez aplikację logiki, która korzysta z metryki wydajności maszyny wirtualnej z kwerendy [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); efektywnie automatyczne skalowanie zależnie od niezależnie od metryki, należy przy jednoczesnej optymalizacji do dodania wartości usługi Azure Resource Manager.
* Musisz skalować w poziomie 1 węzeł zestawu skalowania maszyn wirtualnych w danym momencie.
  * Aby skalowanie w poziomie przez co najmniej 3 węzły w czasie, należy [skalowanie klastra usługi Service Fabric na zewnątrz, dodając zestaw skalowania maszyn wirtualnych](https://docs.microsoft.com/azure service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), jest najbezpieczniejszym umożliwiającą skalowanie w pionie i poziomie maszyny wirtualnej zestawów skalowania w poziomie 1 węzeł w danym momencie.
* Masz niezawodność Silver lub nowszej dla klastra usługi Service Fabric i trwałości Silver lub większą w dowolnej skali zestaw, możesz skonfigurować reguły skalowania automatycznego.
  * Pojemność reguły skalowania automatycznego (minimum) musi być równa lub większa niż 5 wystąpień maszyny wirtualnej, a musi być równa lub większa niż wartość minimalna z warstwy niezawodności dla typu węzła podstawowego.

> [!NOTE]
> Usługa Azure Service Fabric stanowej usługi Service fabric: / System/InfastructureService/< NODE_TYPE_NAME > działa na każdy typ węzła, który ma Silver lub zapewnienia większej niezawodności, która jest tylko usługa systemowa, która jest obsługiwany na platformie Azure na żadnym z typów węzła klastrów .

## <a name="vertical-scaling-considerations"></a>Zagadnienia skalowanie pionowe

[Skalowanie w pionie](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) typ węzła w usłudze Azure Service Fabric wymaga kilku procedury i zagadnienia. Na przykład:

* Klaster musi być w dobrej kondycji przed skalowaniem. W przeciwnym razie zostanie tylko zdestabilizować dalsze klastra.
* **Srebrny trwałości mniejsze niż poziom** jest wymagana dla wszystkich typów węzłów klastra usługi Service Fabric, które hostują usług stanowych.

> [!NOTE]
> Danego typu węzła podstawowego, który jest hostem usługi systemowe Service Fabric usługi stanowe musi być Silver trwałości, poziomu lub nowszej. Po włączeniu silver trwałość, klaster operacje, takie jak uaktualnienia, dodawanie lub usuwanie węzłów i tak dalej będzie przebiegać wolniej, ponieważ system optymalizację pod kątem bezpieczeństwa danych nad szybkość działania.

Skalowanie zestawu skalowania maszyn wirtualnych w pionie jest operacją destrukcyjną. Zamiast tego w poziomie skalowania klastra, dodając nowy zestaw skalowania z odpowiednią jednostką SKU i przeprowadzić migrację usług do żądanej jednostki SKU i ukończyć bezpieczne pionowy operacji skalowania. Zmiana jednostki SKU zasobu zestawu skalowania maszyny wirtualnej jest operacją destrukcyjną, ponieważ ponowne obrazy hosty, które powoduje usunięcie wszystkich lokalnie utrwalonego stanu.

Usługa Service Fabric [— właściwości węzła i ograniczeniami dotyczącymi umieszczania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) są używane przez klaster zdecydować, czy do hostowania usługi aplikacji. Podczas skalowania w pionie typu węzła podstawowego, Zadeklaruj wartości właściwości identyczne `"nodeTypeRef"`, który znajduje się w skali maszyny wirtualnej Ustaw rozszerzenia usługi Service Fabric. Poniższy fragment kodu szablonu usługi Resource Manager pokazuje właściwości ciąg zostanie zadeklarowany, z tą samą wartością nowe zestawami skalowania elastycznie skalowanie w celu i jest obsługiwany tylko jako tymczasowe stateful dla klastra:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nie pozostawiaj klaster za pomocą wielu zestawów skalowania, które używały tych samych `nodeTypeRef` dłużej niż wartość właściwości wymagane do wykonania pomyślnego uruchomienia operacji skalowania pionowy.
> Sprawdzanie poprawności działania w środowiskach testowych przed podjęciem próby zmiany środowiska produkcyjnego. Domyślnie Usługa Service Fabric klaster systemu usługi mają ograniczenie umieszczania tylko typ węzła podstawowego docelowy.

Właściwości węzła i ograniczeniami dotyczącymi umieszczania zadeklarowany wykonaj następujące kroki w jednym wystąpieniu maszyny Wirtualnej w danym momencie. Dzięki temu usługi systemowe (i usługi stanowe) na łagodne zamykanie na wystąpieniu maszyny Wirtualnej, które są usuwane zgodnie z nowej repliki powstają w innym miejscu.

1. Za pomocą programu PowerShell, uruchom `Disable-ServiceFabricNode` z zamiarem "RemoveNode" Wyłącz węzeł zamierzasz usunąć. Usuń typ węzła, który ma najwyższy numer. Na przykład jeśli masz klaster z sześciu węzłów, usuń wystąpienia maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` aby upewnić się, że węzeł zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Może to potrwać kilka godzin w każdym węźle. Nie Kontynuuj, dopóki nie przeszła węzeł na wyłączone.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tego typu węzła. Obecnie najwyższy wystąpienia maszyny Wirtualnej zostaną usunięte.
4. Powtórz kroki od 1 do 3, zgodnie z potrzebami, ale nigdy nie skalowania w zależności od liczby wystąpień w typach węzła podstawowego mniejszą niż gwarantuje warstwy niezawodności. Zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) listę zalecanych wystąpień.

## <a name="horizontal-scaling"></a>skalowanie w poziomie,

Skalowanie w usłudze Service Fabric w poziomie może się to odbywać [ręcznie](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) lub [programowo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Jeśli są skalowania typ węzła, który ma trwałości silver lub gold, skalowanie jest powolne.

### <a name="scaling-out"></a>Skalowanie w poziomie

Skalowanie klastra usługi Service Fabric, zwiększając liczbę wystąpień dla konkretnej maszyny wirtualnej zestawu skalowania. Można skalować w poziomie programowo przy użyciu AzureClient i identyfikator dla żądanego zestawie skalowania, aby zwiększyć jej pojemność.

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
* Usługa stanowa konieczne jest liczba węzłów, które są zawsze się do zapewnienia dostępności i Zachowaj stan usługi. Jako minimum należy liczbę węzłów równa docelowej liczbę zestawu replik partycji/usługi.

Aby skalować w ręcznie, wykonaj następujące kroki:

1. Za pomocą programu PowerShell, uruchom `Disable-ServiceFabricNode` z zamiarem "RemoveNode" Wyłącz węzeł zamierzasz usunąć. Usuń typ węzła, który ma najwyższy numer. Na przykład jeśli masz klaster z sześciu węzłów, usuń wystąpienia maszyny wirtualnej "MyNodeType_5".
2. Uruchom `Get-ServiceFabricNode` aby upewnić się, że węzeł zmienił się na wyłączone. Jeśli nie, zaczekaj, aż węzeł jest wyłączony. Może to potrwać kilka godzin w każdym węźle. Nie Kontynuuj, dopóki nie przeszła węzeł na wyłączone.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tego typu węzła. Obecnie najwyższy wystąpienia maszyny Wirtualnej zostaną usunięte.
4. Powtórz kroki od 1 do 3, zgodnie z potrzebami, ale nigdy nie skalowania w zależności od liczby wystąpień w typach węzła podstawowego mniejszą niż gwarantuje warstwy niezawodności. Zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) listę zalecanych wystąpień.

Umożliwiającą skalowanie w pionie ręcznie, zaktualizuj pojemność we właściwości SKU żądaną [zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) zasobów.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Powtórz kroki od 1 do 3, aprowizować pojemność, która ma. Nie skalować w dół liczbę wystąpień w typach węzła podstawowego, aby była ona mniejsza niż gwarantuje warstwy niezawodności. Szczegółowe informacje na temat warstwy niezawodności i liczbę wystąpień, które są wymagane, [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Należy przygotować węzeł dla zamknięcie systemu w celu skalowania w programowo. Obejmuje to uważa się, że węzeł, który ma zostać usunięty, który jest najwyższy węzeł wystąpienia i dezaktywowanie go. Na przykład:

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

Po zidentyfikowaniu węzła do usunięcia, dezaktywować i usuń go, korzystając z tych samych `FabricClient` wystąpienia (`client` w tym przypadku) i nazwa wystąpienia węzła (`instanceIdString` w tym przypadku) używane w powyższym kodzie:

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Skalowanie klastra w dół możesz zobaczą wystąpienia węzła usuniętych i maszyn wirtualnych, wyświetlana w złej kondycji w narzędziu Service Fabric Explorer. Opis tego zachowania zawiera [zachowania można zaobserwować w narzędziu Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Możesz:
> * Wywołaj [polecenia Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) o nazwie odpowiedni węzeł.
> * Wdrażanie [aplikacja usługi Service fabric skalowania automatycznego Pomocnika](https://github.com/Azure/service-fabric-autoscale-helper/) w klastrze, który zapewnia skalowany w dół węzły są usuwane z narzędzia Service Fabric Explorer.

## <a name="reliability-levels"></a>Poziom niezawodności

[Poziom niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) właściwość zasobu klastra usługi Service Fabric jest i nie można skonfigurować inaczej dla typów pojedynczych węzłów. Określa współczynnik replikacji usług systemowych dla klastra, a ustawienie poziomie zasobów klastra. Poziom niezawodności określi, minimalna liczba węzłów, które musi być typu węzła podstawowego. Warstwa niezawodności może przyjmować następujące wartości:

* Platinum — uruchamia usługi systemowe wraz z liczbą zestawu replik docelowej siedmiu i dziewięć węzły obiektów początkowych.
* Złoty — uruchamia usługi systemowe wraz z liczbą zestawu replik docelowej siedmiu lub siedmiu węzłów inicjatora.
* Srebrny — uruchamia usługi systemowe wraz z liczbą docelowej repliki zestaw pięciu do pięciu węzły obiektów początkowych.
* Bronze — uruchamia usługi systemowe wraz z liczbą docelowej repliki zestaw trzech i trzy węzły obiektów początkowych.

Poziom niezawodności zalecaną minimalną jest Silver.

Poziom niezawodności znajduje się w sekcji właściwości [zasobów Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), podobnie do następującego:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Poziom trwałości

> [!WARNING]
> Typy węzłów z trwałością brązowa uzyskać _brakiem uprawnień_. Oznacza to, że zadania infrastruktury, które wpływają na obciążeń bezstanowych będzie nie można zatrzymać lub opóźnione, co może mieć wpływ na Twoich obciążeń. Na użytek trwałości brązowa tylko typy węzłów, działających obciążeń bezstanowych. W przypadku obciążeń produkcyjnych Uruchom Silver lub nowszej, aby zapewnić stan spójności. Wybierz odpowiednie niezawodność, w oparciu o wskazówki zawarte w [dokumentacji planowania pojemności na potrzeby](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Poziom trwałości musi być ustawiona w dwóch zasobów. Profil rozszerzenia [zasobów zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

A następnie w obszarze `nodeTypes` w [Microsoft.ServiceFabric/clusters zasobów](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
