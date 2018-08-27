---
title: Skalowanie klastra usługi Azure Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować klaster usługi Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2018
ms.author: ryanwi
ms.openlocfilehash: 8e1c194ea2ebc0e06918c8389c9ee6f72afb3e86
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887792"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Skalowanie klastra usługi Service Fabric w poziomie przez dodanie zestawu skalowania maszyn wirtualnych
W tym artykule opisano sposób skalowanie klastra usługi Azure Service Fabric przez dodanie nowego zestawu do istniejącego klastra skalowania maszyny wirtualnej. Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem. Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [konfigurowany jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być zarządzany oddzielnie. Po utworzeniu klastra usługi Service Fabric można skalować do typu węzła klastra w pionie (Zmiana zasobów węzłów), Uaktualnij system operacyjny węzła typu maszyn wirtualnych lub dodać nowego zestawu do istniejącego klastra skalowania maszyny wirtualnej.  Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.  Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

> [!WARNING]
> Nie uruchamiaj można zmienić podstawowego elementu nodetype jednostki SKU maszyny Wirtualnej, jeśli kondycja klastra jest w złej kondycji. Jeśli kondycja klastra jest w złej kondycji, będzie tylko zdestabilizować sprawy, klastra, Jeśli spróbujesz zmienić jednostki SKU maszyny Wirtualnej.
>
> Firma Microsoft zaleca, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu węzeł/zestaw skalowania, chyba że jest uruchomiona na [srebrny trwałości lub nowszej](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjne danych w miejscu infrastruktury. Bez niektóre możliwość opóźnienia lub monitora, ta zmiana jest możliwe, że operacja może spowodować utratę danych w przypadku usług stanowych lub powodować inne nieprzewidziane problemy operacyjne, nawet w przypadku obciążeń bezstanowych. Oznacza to, typu węzła podstawowego, w którym jest uruchomiony system usługi stanowej usługi Service fabric, lub dowolny typ węzła, który jest uruchomiony na pracę aplikacji stanowych ładuje.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uaktualnij rozmiar i systemu operacyjnego typu węzła podstawowego maszyn wirtualnych
Oto proces aktualizowania rozmiaru maszyny Wirtualnej i systemu operacyjnego typu węzła podstawowego maszyn wirtualnych.  Po uaktualnieniu podstawowy typ węzła maszyny wirtualne są rozmiar Standard D4_V2 i uruchamianie systemu Windows Server 2016 Datacenter z kontenerami.

> [!WARNING]
> Przed podjęciem próby wykonania tej procedury w klastrze produkcyjnym, zalecamy zapoznają się z przykładowych szablonów, a następnie sprawdź procesu względem klastra testowego. Klaster również jest niedostępny w czasie.

1. Wdrożenie klastra początkowej za pomocą dwa typy węzłów i dwa scale sets (jeden skalowania na typ węzła) przy użyciu tych przykładowych [szablonu](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) i [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) plików.  Oba zestawy skalowania są rozmiar standardowa D2_V2 i uruchamianie systemu Windows Server 2012 R2 Datacenter.  Poczekaj, aż klaster, aby ukończyć uaktualnienie punktu odniesienia.   
2. Opcjonalne — wdrażanie próbkę stanowych w klastrze.
3. Po podjęciu decyzji uaktualnić podstawowy typ węzła maszyn wirtualnych, należy dodać nowego zestawu typu węzła podstawowego, przy użyciu tych przykładowych skalowania [szablonu](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) i [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) plików, dzięki czemu typu węzła podstawowego ma teraz dwa zestawy skalowania.  System usługi i aplikacje użytkownika są możliwe Migrowanie między maszynami wirtualnymi w dwóch zestawach skalowania innego.  Nowy zestaw skalowania maszyn wirtualnych są rozmiar Standard D4_V2 i uruchamianie systemu Windows Server 2016 Datacenter z kontenerami.  Nowy moduł równoważenia obciążenia i publicznego adresu IP są również dodawane za pomocą nowego zestawu skalowania.  
    Aby znaleźć nowy zestaw skalowania w szablonie, wyszukaj "Microsoft.Compute/virtualMachineScaleSets" zasób o nazwie określonej przez *vmNodeType2Name* parametru.  Nowy zestaw skalowania jest dodawany do przy użyciu typu węzła podstawowego -> właściwości virtualMachineProfile - > extensionProfile -> Rozszerzenia -> Właściwości -> Ustawienia -> Ustawienia elementu nodeTypeRef.
4. Sprawdzanie kondycji klastra i sprawdź, czy wszystkie węzły są w dobrej kondycji.
5. Wyłącz węzłów w zestawie skalowania starego typu węzła podstawowego z zamiarem Usuń węzeł. Możesz wyłączyć wszystkie na raz, a operacje są umieszczane w kolejce. Poczekaj, aż wszystkie węzły są wyłączone, co może zająć trochę czasu.  Jako starszy węzłów typu węzła są wyłączone, system węzłów usługi i inicjatora Przeprowadź migrację do maszyn wirtualnych w nowy zestaw skalowania w typu węzła podstawowego.
6. Usuń starsze zestawu skalowania na podstawie typu węzła podstawowego.
7. Usuwanie modułu równoważenia obciążenia, skojarzone z stary zestaw skalowania. Klaster jest niedostępna, gdy nowy publicznego adresu IP adres i Załaduj moduł równoważenia są skonfigurowane dla nowego zestawu skalowania.  
8. Ustawienia DNS Store publicznego adresu IP skojarzone z węzła podstawowego starego typu zestawu skalowania w zmiennej i Usuń ten publiczny adres IP.
9. Zastąp ustawienia DNS dla publicznego adresu IP skojarzone z nowego węzła podstawowego typu zestawu skalowania przy użyciu ustawień DNS usunięto publiczny adres IP.  Klaster będzie teraz znowu dostępne.
10. Usuń stan węzła, węzeł z klastra.  Jeśli poziom trwałości stare zestawu skalowania na poziomie silver lub gold, w tym kroku są wykonywane automatycznie przez system.
11. Jeśli wdrożono stanowych aplikacji w poprzednim kroku, sprawdź, czy aplikacja działa.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
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
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
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
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>Dodawanie rozbudowywać zestawu do istniejącego klastra
Dodawanie nowego elementu NodeType zestawu skalowania maszyn wirtualnych do istniejącego klastra, jest podobnie jak wyżej uaktualnienie typu węzła podstawowego, ale nie będzie używać tego samego elementu NodeTypeRef; Oczywiście nie będzie wyłączenie wszelkich aktywnie używanych zestawów skalowania maszyn wirtualnych, a nie utracić dostępność klastra, jeśli nie są uaktualniane typu węzła podstawowego. 

Właściwość elementu NodeTypeRef zadeklarowana we właściwościach rozszerzenia sieci szkieletowej usługi zestawu skalowania maszyn wirtualnych:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Ponadto należy dodać ten nowy typ węzła do zasobu klastra usługi Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra usługi Azure wewnątrz lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra usługi Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure compute zestawu SDK.
* [Skalowanie klastra autonomiczna wewnątrz lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

