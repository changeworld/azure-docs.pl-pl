---
title: Skalowanie w górę typu węzła usługi Azure Service Fabric
description: Dowiedz się, jak skalować klaster sieci szkieletowej usług, dodając zestaw skalowania maszyny wirtualnej.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464226"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu
W tym artykule opisano sposób skalowania w górę typu węzła podstawowego klastra sieci szkieletowej usług przez zwiększenie zasobów maszyny wirtualnej. Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem. Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure używany do wdrażania i zarządzania kolekcją maszyn wirtualnych jako zestaw. Każdy typ węzła zdefiniowany w klastrze platformy Azure jest [łączona jako oddzielny zestaw skalowania.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie zarządzać oddzielnie. Po utworzeniu klastra sieci szkieletowej usług można skalować typ węzła klastra w pionie (zmieniać zasoby węzłów) lub uaktualnić system operacyjny maszyn wirtualnych typu węzła.  Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.  W miarę skalowania klastra aplikacje są również skalowane automatycznie.

> [!WARNING]
> Nie należy rozpoczynać zmiany jednostki SKU maszyny Wirtualnej węzła podstawowego, jeśli kondycja klastra jest w złej kondycji. Jeśli kondycja klastra jest w złej kondycji, tylko zdestabilizować klastra dalej, jeśli spróbujesz zmienić jednostkę SKU maszyny Wirtualnej.
>
> Zaleca się, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu zestawu skalowania/węzła, chyba że działa ona z [trwałością silver lub większą.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest destrukcyjną dla danych operacją infrastruktury w miejscu. Bez pewnej możliwości opóźnienia lub monitorowania tej zmiany, jest możliwe, że operacja może spowodować utratę danych dla usług stanowych lub spowodować inne nieprzewidziane problemy operacyjne, nawet dla obciążeń bezstanowych. Oznacza to, że typ węzła podstawowego, który jest uruchomiony usługi sieci szkieletowej usługi stanowe lub dowolnego typu węzła, który jest uruchomiony stanowe obciążenia pracy aplikacji.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uaktualnianie rozmiaru i systemu operacyjnego maszyn wirtualnych typu węzła podstawowego
Oto proces aktualizowania rozmiaru maszyny Wirtualnej i systemu operacyjnego maszyn wirtualnych typu węzła podstawowego.  Po uaktualnieniu maszyny wirtualne typu węzła podstawowego to rozmiar Standardowy D4_V2 i uruchomione centrum danych systemu Windows Server 2016 z kontenerami.

> [!WARNING]
> Przed podjęciem tej próby tej procedury w klastrze produkcyjnym zaleca się zbadanie przykładowych szablonów i zweryfikowanie procesu względem klastra testowego. Klaster jest również niedostępny przez pewien czas. NIE można wprowadzać zmian w wielu maszynach wirtualnych zadeklarowanych jako ten sam typ węzła równolegle; należy wykonać oddzielne operacje wdrażania, aby zastosować zmiany do każdego NodeType VMSS indywidualnie.

1. Wdrażanie klastra początkowego z dwoma typami węzłów i dwoma zestawami skalowania (jeden zestaw skalowania na typ węzła) przy użyciu tych przykładowych plików [szablonów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) i [parametrów.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Oba zestawy skalowania mają rozmiar Standardowy D2_V2 i działają w centrum danych systemu Windows Server 2012 R2.  Poczekaj na ukończenie uaktualnienia planu bazowego przez klaster.   
2. Opcjonalnie — wdrożyć próbkę stanową do klastra.
3. Po podjęciu decyzji o uaktualnieniu maszyn wirtualnych typu węzła podstawowego dodaj nowy zestaw skalowania do typu węzła podstawowego przy użyciu tych przykładowych plików [szablonów](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) i [parametrów,](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) aby typ węzła podstawowego miał teraz dwa zestawy skalowania.  Usługi systemowe i aplikacje użytkowników mogą migrować między maszynami wirtualnymi w dwóch różnych zestawach skalowania.  Nowe maszyny wirtualne o rozmiarze są o rozmiarze Standardowy D4_V2 i uruchamiają centrum danych systemu Windows Server 2016 z kontenerami.  Nowy moduł równoważenia obciążenia i publiczny adres IP są również dodawane z nowym zestawem skalowania.  
    Aby znaleźć nowy zestaw skalowania w szablonie, wyszukaj zasób "Microsoft.Compute/virtualMachineScaleSets" nazwany *parametrem vmNodeType2Name.*  Nowy zestaw skalowania jest dodawany do typu węzła podstawowego przy użyciu właściwości >virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef.
4. Sprawdź kondycję klastra i sprawdź, czy wszystkie węzły są w dobrej kondycji.
5. Wyłącz węzły w starym zestawie skalowania typu węzła podstawowego z zamiarem usunięcia węzła. Można wyłączyć wszystkie naraz, a operacje są umieszczane w kolejce. Poczekaj, aż wszystkie węzły zostaną wyłączone, co może zająć trochę czasu.  Ponieważ starsze węzły w typie węzła są wyłączone, usługi systemowe i węzły źródłowe migrują do maszyn wirtualnych nowego zestawu skalowania w typie węzła podstawowego.
6. Usuń starszy zestaw skalowania z typu węzła podstawowego.
7. Usuń moduł równoważenia obciążenia skojarzony ze starym zestawem skalowania. Klaster jest niedostępny, gdy nowy publiczny adres IP i moduł równoważenia obciążenia są skonfigurowane dla nowego zestawu skalowania.  
8. Przechowuj ustawienia DNS publicznego adresu IP skojarzonego ze starym skalowaniem typu węzła podstawowego ustawionego w zmiennej i usuń ten publiczny adres IP.
9. Zastąp ustawienia DNS publicznego adresu IP skojarzonego z nowym zestawem skalowania typu węzła podstawowego ustawieniami DNS usuniętego publicznego adresu IP.  Klaster jest teraz dostępny ponownie.
10. Usuń stan węzłów z klastra.  Jeśli poziom trwałości starego zestawu skalowania był srebrny lub złoty, ten krok jest wykonywany przez system automatycznie.
11. Jeśli wdrożono aplikację stanową w poprzednim kroku, sprawdź, czy aplikacja jest funkcjonalna.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [dodać typ węzła do klastra](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu płynnego zestawu SDK obliczeń platformy Azure.
* [Skalowanie autonomicznego klastra w lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

