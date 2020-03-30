---
title: Planowanie pojemności i skalowanie sieci szkieletowej usług Azure
description: Najważniejsze wskazówki dotyczące planowania i skalowania klastrów i aplikacji sieci szkieletowej usług.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258995"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Planowanie pojemności i skalowanie sieci szkieletowej usług Azure

Przed utworzeniem dowolnego klastra sieci szkieletowej usługi Azure lub skalowania zasobów obliczeniowych, które obsługują klaster, należy zaplanować pojemność. Aby uzyskać więcej informacji na temat planowania pojemności, zobacz [Planowanie pojemności klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Aby uzyskać dalsze wskazówki dotyczące najlepszych rozwiązań dotyczących skalowalności klastra, zobacz [Zagadnienia dotyczące skalowalności sieci szkieletowej usług.](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations)

Oprócz uwzględnienia typu węzła i charakterystyki klastra, należy oczekiwać, że operacje skalowania potrwać dłużej niż godzinę, aby zakończyć dla środowiska produkcyjnego. Ta uwaga jest spełniony niezależnie od liczby maszyn wirtualnych, które dodajesz.

## <a name="autoscaling"></a>Skalowanie automatyczne
Operacje skalowania należy wykonywać za pomocą szablonów usługi Azure Resource Manager, ponieważ najlepszym rozwiązaniem jest traktowanie [konfiguracji zasobów jako kodu.]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) 

Za pomocą automatycznego skalowania za pomocą zestawów skalowania maszyny wirtualnej sprawi, że szablon Menedżera zasobów wersjonowane niedokładnie zdefiniować liczbę wystąpień dla zestawów skalowania maszyny wirtualnej. Niedokładna definicja zwiększa ryzyko, że przyszłe wdrożenia spowoduje niezamierzone operacje skalowania. Ogólnie rzecz biorąc, należy użyć skalowania automatycznego, jeśli:

* Wdrażanie szablonów Menedżera zasobów z zadeklarowanym pojemnością nie obsługuje przypadku użycia.
     
   Oprócz ręcznego skalowania można skonfigurować [potok ciągłej integracji i dostarczania w usługach Azure DevOps przy użyciu projektów wdrażania grup zasobów platformy Azure.](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts) Ten potok jest często wyzwalany przez aplikację logiki, która używa metryk wydajności maszyny wirtualnej, których dotyczy zapytanie z [interfejsu API REST usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough) Potok skutecznie skaluje się automatycznie na podstawie dowolnych metryk, podczas gdy optymalizujesz szablony Menedżera zasobów.
* Należy skalować poziomo tylko jeden węzeł zestawu skalowania maszyny wirtualnej naraz.
   
   Aby skalować w poziomie przez co najmniej trzy węzły naraz, należy [skalować w poziomie klaster sieci szkieletowej usług, dodając zestaw skalowania maszyny wirtualnej.](virtual-machine-scale-set-scale-node-type-scale-out.md) Najbezpieczniej jest skalować i skalować w poziomie skala maszyny wirtualnej ustawia poziomo, jeden węzeł na raz.
* Masz niezawodność Silver lub wyższą dla klastra sieci szkieletowej usług i trwałość silver lub wyższą na dowolnej skali, w której konfigurujesz reguły skalowania automatycznego.
  
   Minimalna pojemność reguł skalowania automatycznego musi być równa lub większa niż pięć wystąpień maszyny wirtualnej. Musi być również równa lub większa niż minimum warstwy niezawodności dla typu węzła podstawowego.

> [!NOTE]
> Sieci szkieletowej usług stanowych sieci szkieletowej:/System/InfastructureService/<NODE_TYPE_NAME> działa na każdym typie węzła, który ma silver lub większą trwałość. Jest to jedyna usługa systemowa, która jest obsługiwana do uruchamiania na platformie Azure na dowolnym typie węzłów klastrów.

## <a name="vertical-scaling-considerations"></a>Zagadnienia dotyczące skalowania w pionie

[Skalowanie w pionie](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) typu węzła w sieci szkieletowej usługi Azure wymaga kilku kroków i zagadnień. Przykład:

* Klaster musi być w dobrej kondycji przed skalowaniem. W przeciwnym razie zdestabilizujesz klaster dalej.
* Poziom trwałości silver lub większy jest wymagany dla wszystkich typów węzłów klastra sieci szkieletowej usług, które obsługują usługi stanowe.

> [!NOTE]
> Typ węzła podstawowego, który obsługuje usługi systemu sieci szkieletowej usług stanowych, musi mieć poziom trwałości Silver lub wyższy. Po włączeniu trwałości silver operacje klastra, takie jak uaktualnienia, dodawanie lub usuwanie węzłów i tak dalej będzie wolniejszy, ponieważ system optymalizuje bezpieczeństwo danych nad szybkością operacji.

Skalowanie w pionie zestaw skalowania maszyny wirtualnej jest operacją destrukcyjną. Zamiast tego skaluj poziomo klaster, dodając nowy zestaw skalowania z żądaną jednostką SKU. Następnie należy przeprowadzić migrację usług do żądanej jednostki SKU, aby ukończyć bezpieczną operację skalowania pionowego. Zmiana jednostki SKU zestawu zasobów zestawu skalowania maszyny wirtualnej jest operacją destrukcyjną, ponieważ ponownie wyobraża hosty, co usuwa wszystkie lokalnie utrwalone stany.

Klaster używa właściwości węzła sieci szkieletowej [usług i ograniczeń umieszczania,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) aby zdecydować, gdzie mają być hostowane usługi aplikacji. Podczas skalowania w pionie typu węzła podstawowego `"nodeTypeRef"`należy zadeklarować identyczne wartości właściwości dla obiektu . Te wartości można znaleźć w rozszerzeniu sieci szkieletowej usług dla zestawów skalowania maszyny wirtualnej. 

Poniższy fragment szablonu Menedżera zasobów zawiera właściwości, które należy zadeklarować. Ma taką samą wartość dla nowo aprowizacji zestawów skalowania, które są skalowanie do i jest obsługiwany tylko jako tymczasowa usługa stanowa dla klastra.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nie pozostawiaj klastra uruchomionego z wieloma `nodeTypeRef` zestawami skalowania, które używają tej samej wartości właściwości dłuższej niż wymagana do ukończenia pomyślnej operacji skalowania pionowego.
>
> Zawsze sprawdzaj poprawność operacji w środowiskach testowych przed podjęciem próby zmian w środowisku produkcyjnym. Domyślnie usługi systemu klastra sieci szkieletowej usług sieci szkieletowych mają ograniczenie umieszczania tylko do docelowego typu węzła podstawowego.

Z właściwości węzła i ograniczenia umieszczania zadeklarowane, wykonaj następujące kroki po jednym wystąpieniu maszyny Wirtualnej w czasie. Dzięki temu usługi systemowe (i usługi stanowe) mają być zamknięte bezpiecznie w wystąpieniu maszyny Wirtualnej, które usuwasz, jak nowe repliki są tworzone w innym miejscu.

1. Z programu PowerShell `Disable-ServiceFabricNode` `RemoveNode` uruchom z zamiarem wyłączenia węzła, który zamierzasz usunąć. Usuń typ węzła, który ma największą liczbę. Na przykład jeśli masz klaster z sześcioma węzłami, usuń wystąpienie maszyny wirtualnej "MyNodeType_5".
2. Uruchom, `Get-ServiceFabricNode` aby upewnić się, że węzeł został przeniesiony do wyłączone. Jeśli nie, poczekaj, aż węzeł zostanie wyłączony. Może to potrwać kilka godzin dla każdego węzła. Nie kontynuuj, dopóki węzeł nie został przeniesiony do wyłączonego.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tym typie węzła. Najwyższe wystąpienie maszyny Wirtualnej zostanie teraz usunięte.
4. Powtórz kroki od 1 do 3 w razie potrzeby, ale nigdy nie skaluj w dół liczby wystąpień w typach węzłów podstawowych mniej niż wymaga tego warstwa niezawodności. Zobacz [Planowanie pojemności klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) dla listy zalecanych wystąpień.
5. Gdy wszystkie maszyny wirtualne znikną (reprezentowane jako "W dół") sieci szkieletowej:/System/InfrastructureService/[nazwa węzła] pokaże stan error. Następnie można zaktualizować zasób klastra, aby usunąć typ węzła. Można użyć wdrożenia szablonu ARM lub edytować zasób klastra za pośrednictwem [Menedżera zasobów platformy Azure](https://resources.azure.com). Spowoduje to uruchomienie uaktualnienia klastra, który usunie usługę szkieletową:/System/InfrastructureService/[node type] w stanie błędu.
 6. Po tym można opcjonalnie usunąć VMScaleSet, nadal będzie widać węzły jako "W dół" z widoku Eksploratora sieci szkieletowej usług choć. Ostatnim krokiem byłoby, aby `Remove-ServiceFabricNodeState` oczyścić je za pomocą polecenia.

## <a name="horizontal-scaling"></a>skalowanie w poziomie,

Skalowanie w poziomie można wykonać [ręcznie](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) lub [programowo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Jeśli skalujesz typ węzła, który ma trwałość Silver lub Gold, skalowanie będzie powolne.

### <a name="scaling-out"></a>Skalowanie w poziomie

Skalowanie w poziomie klastra sieci szkieletowej usług przez zwiększenie liczby wystąpień dla określonego zestawu skalowania maszyny wirtualnej. Można skalować w poziomie `AzureClient` programowo za pomocą i identyfikator dla zestawu skaly żądanej, aby zwiększyć pojemność.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Aby ręcznie skalować w poziomie, należy zaktualizować pojemność we właściwości jednostki SKU żądanego zasobu [zestawu skalowania maszyny wirtualnej.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skalowanie w

Skalowanie w wymaga więcej uwagę niż skalowanie w poziomie. Na przykład:

* Usługi systemu sieci szkieletowej usług są uruchamiane w typie węzła podstawowego w klastrze. Nigdy nie zamykaj ani nie skaluj w dół liczby wystąpień dla tego typu węzła, aby mieć mniej wystąpień niż gwarantuje warstwa niezawodności. 
* Dla usługi stanowej trzeba pewną liczbę węzłów, które są zawsze do utrzymania dostępności i zachowania stanu usługi. Co najmniej potrzebna jest liczba węzłów równych docelowej liczbie zestawów replik partycji lub usługi.

Aby skalować ręcznie, wykonaj następujące kroki:

1. Z programu PowerShell `Disable-ServiceFabricNode` `RemoveNode` uruchom z zamiarem wyłączenia węzła, który zamierzasz usunąć. Usuń typ węzła, który ma największą liczbę. Na przykład jeśli masz klaster z sześcioma węzłami, usuń wystąpienie maszyny wirtualnej "MyNodeType_5".
2. Uruchom, `Get-ServiceFabricNode` aby upewnić się, że węzeł został przeniesiony do wyłączone. Jeśli nie, poczekaj, aż węzeł zostanie wyłączony. Może to potrwać kilka godzin dla każdego węzła. Nie kontynuuj, dopóki węzeł nie został przeniesiony do wyłączonego.
3. Zmniejsz liczbę maszyn wirtualnych o jeden w tym typie węzła. Najwyższe wystąpienie maszyny Wirtualnej zostanie teraz usunięte.
4. W razie potrzeby powtarzaj kroki od 1 do 3, aż do momentu udostępnić odpowiednią pojemność. Nie skaluj w dół liczby wystąpień w typach węzłów podstawowych do mniej niż gwarantuje warstwa niezawodności. Zobacz [Planowanie pojemności klastra sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) dla listy zalecanych wystąpień.

Aby skalować ręcznie, należy zaktualizować pojemność we właściwości SKU żądanego zasobu [zestawu skalowania maszyny wirtualnej.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Należy przygotować węzeł do zamknięcia, aby skalować programowo. Znajdź węzeł do usunięcia (węzeł najwyższego wystąpienia). Przykład:

```csharp
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

Dezaktywuj i usuń `FabricClient` węzeł`client` przy użyciu tego samego`instanceIdString` wystąpienia (w tym przypadku) i wystąpienia węzła (w tym przypadku), które zostały użyte w poprzednim kodzie:

```csharp
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
> Po skalowaniu w dół klastra, zobaczysz usunięte węzeł/maszyna wirtualna wystąpienie wyświetlane w stanie w złej kondycji w Eksploratorze sieci szkieletowej usług. Aby uzyskać wyjaśnienie tego zachowania, zobacz [zachowania, które można zaobserwować w Eksploratorze sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Możesz:
> * Wywołanie [polecenia Usuń-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) z odpowiednią nazwą węzła.
> * Wdrażanie [aplikacji pomocnika skalowania automatycznego sieci szkieletowej](https://github.com/Azure/service-fabric-autoscale-helper/) w klastrze. Ta aplikacja zapewnia, że węzły skalowane w dół są czyszczone z Eksploratora sieci szkieletowej usług.

## <a name="reliability-levels"></a>Poziomy niezawodności

[Poziom niezawodności](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) jest właściwością zasobu klastra sieci szkieletowej usług. Nie można skonfigurować inaczej dla poszczególnych typów węzłów. Steruje współczynnikiem replikacji usług systemowych dla klastra i jest ustawieniem na poziomie zasobów klastra. 

Poziom niezawodności określi minimalną liczbę węzłów, które musi mieć typ węzła podstawowego. Warstwa niezawodności może przyjmować następujące wartości:

* Platyna: uruchamia usługi systemowe z docelową liczbą zestawów replik siedmiu i dziewięciu węzłów źródłowych.
* Złoto: Uruchamia usługi systemowe z docelową liczbą zestawów replik siedmiu i siedmiu węzłów źródłowych.
* Srebro: uruchamia usługi systemowe z docelową liczbą zestawów replik pięciu i pięciu węzłów źródłowych.
* Brązowy: Uruchamia usługi systemowe z docelową liczbą zestawów replik trzech i trzech węzłów źródłowych.

Minimalny zalecany poziom niezawodności to Silver.

Poziom niezawodności jest ustawiony w sekcji właściwości [zasobu Microsoft.ServiceFabric/clusters,](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)w ten sposób:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Poziomy trwałości

> [!WARNING]
> Typy węzłów z brązową trwałością nie uzyskują _żadnych uprawnień._ Zadania infrastruktury, które wpływają na obciążenia bezstanowe nie zostaną zatrzymane lub opóźnione, co może mieć wpływ na obciążenia. 
>
> Brązowa trwałość tylko dla typów węzłów, które uruchamiają obciążenia bezstanowe. W przypadku obciążeń produkcyjnych uruchom program Silver lub wyższy, aby zapewnić spójność stanu. Wybierz odpowiednią niezawodność na podstawie wskazówek zawartych w [dokumentacji planowania pojemności.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

Poziom trwałości musi być ustawiony w dwóch zasobach. Jednym z nich jest profil rozszerzenia [zasobu zestawu skalowania maszyny wirtualnej:](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

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

Drugi zasób `nodeTypes` znajduje się w [zasobie Microsoft.ServiceFabric/clusters:](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra linuksa](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
