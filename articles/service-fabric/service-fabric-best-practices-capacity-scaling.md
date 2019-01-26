---
title: Usługa Azure Service Fabric do planowania wydajności i najlepsze rozwiązania dotyczące skalowania | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące planowania i skalowanie klastrów usługi Service Fabric i aplikacji.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: ff58cc713a6aba211f9eeb1dc42912d21c5b0bdb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913953"
---
# <a name="capacity-planning-and-scaling"></a>Planowanie wydajności i skalowania

Przed utworzeniem dowolnego klastra usługi Azure Service Fabric lub skalowanie zasobów obliczeniowych hostingu klastra, ważne jest planowania pojemności. Aby uzyskać więcej informacji na temat planowania pojemności, zobacz [Planowanie pojemności klastra usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Oprócz biorąc pod uwagę charakterystyki element Nodetype i klastra, wybierz plan zapewniający skalowanie w operacji trwać dłużej niż godzinę, aby zakończyć w środowisku produkcyjnym, bez względu na liczbę maszyn wirtualnych, które dodajesz.

## <a name="vertical-scaling-considerations"></a>Zagadnienia skalowanie pionowe

[Skalowanie w pionie](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out#upgrade-the-size-and-operating-system-of-the-primary-node-type-vms) typ węzła w usłudze Azure Service Fabric wymaga kilku procedury i zagadnienia. Na przykład:
* Klaster musi być w dobrej kondycji przed skalowaniem. W przeciwnym razie zostanie tylko zdestabilizować dalsze klastra.
* **Srebrny trwałości mniejsze niż poziom** jest wymagana dla wszystkich usługi sieci szkieletowej klastra elementy NodeType obsługujące usług stanowych.

> [!NOTE]
> Usługi podstawowego elementu NodeType, który jest hostem usługi systemowe Service Fabric usługi stanowe musi być Silver trwałości, poziomu lub nowszej. Po włączeniu silver trwałość, klaster operacje, takie jak uaktualnienia, dodawanie lub usuwanie węzłów i tak dalej będzie przebiegać wolniej, ponieważ system optymalizację pod kątem bezpieczeństwa danych nad szybkość działania.

Pionowy, skalowanie zestawu skalowania maszyn wirtualnych jest operacją destrukcyjną. Zamiast tego w poziomie skalowania klastra, dodając nowy zestaw skalowania z odpowiednią jednostką SKU i przeprowadzić migrację usług do żądanej jednostki SKU i ukończyć bezpieczne pionowy operacji skalowania. Zmiana zasobu jednostki SKU zestawu skalowania maszyn wirtualnych jest operacją destrukcyjną, ponieważ jego odtwarza z obrazu hosty, które powoduje usunięcie wszystkich lokalnie utrwalonego stanu.

Usługa Service Fabric [— właściwości węzła i ograniczeniami dotyczącymi umieszczania](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) są używane przez klaster zdecydować, czy do hostowania usługi aplikacji. Podczas skalowania w pionie typu węzła podstawowego, Zadeklaruj wartości właściwości identyczne `"nodeTypeRef"`, który znajduje się w rozszerzenia sieci szkieletowej usługi zestawu skalowania maszyn wirtualnych. Poniższy fragment kodu szablonu usługi Resource Manager pokazuje właściwości ciąg zostanie zadeklarowany, z tą samą wartością nowe zestawami skalowania elastycznie skalowanie w celu i jest obsługiwany tylko jako tymczasowe stateful dla klastra:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nie pozostawiaj klaster za pomocą wielu zestawów skalowania, które używały tych samych `nodeTypeRef` dłużej niż wartość właściwości wymagane do wykonania pomyślnego uruchomienia operacji skalowania pionowy.
> Sprawdzanie poprawności działania w środowiskach testowych przed podjęciem próby zmiany środowiska produkcyjnego. Domyślnie Usługa Service Fabric klaster systemu usługi mają ograniczenie umieszczania pod kątem tylko podstawowy element nodetype.

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

Skalowanie klastra usługi Service Fabric, zwiększając liczbę wystąpień dla danego zestawu skalowania maszyn wirtualnych. Można skalować w poziomie programowo przy użyciu AzureClient i identyfikator dla żądanego zestawie skalowania, aby zwiększyć jej pojemność.

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

## <a name="reliability-levels"></a>Poziom niezawodności

[Poziom niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) właściwość zasobu klastra usługi Service Fabric jest i nie można skonfigurować inaczej poszczególne elementy NodeType. Określa współczynnik replikacji usług systemowych dla klastra, a ustawienie poziomie zasobów klastra. Poziom niezawodności określi, minimalna liczba węzłów, które musi być typu węzła podstawowego. Warstwa niezawodności może przyjmować następujące wartości:
* Platinum — uruchamia usługi systemowe wraz z liczbą docelowego zestawu replik siedmiu
* Złoty — uruchamia usługi systemowe wraz z liczbą docelowego zestawu replik siedmiu
* Srebrny — uruchamia usługi systemowe wraz z liczbą zestawu replik docelowej do 5
* Bronze — uruchamia usługi systemowe wraz z liczbą docelowego zestawu replik trzech

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