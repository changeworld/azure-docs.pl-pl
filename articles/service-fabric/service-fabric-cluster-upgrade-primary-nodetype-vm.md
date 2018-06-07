---
title: Uaktualnienia klastra usługi sieć szkieletowa usług Azure SKU maszyn wirtualnych lub systemu operacyjnego | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uaktualnić maszyn wirtualnych w głównej nodetype klastra sieci szkieletowej usług.
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
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: bf707bf4b1c001b5467dd9954e9c6feb55e65654
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655744"
---
# <a name="upgrade-the-primary-node-type-vms-of-a-service-fabric-cluster"></a>Typ węzła podstawowego maszyn wirtualnych klastra usługi sieć szkieletowa uaktualnienia
W tym artykule opisano sposób uaktualniania maszyn wirtualnych typu węzła podstawowego klaster sieci szkieletowej usług działających na platformie Azure.  Klaster sieci szkieletowej usług jest zestawem połączonych z siecią maszyn wirtualnych lub fizycznych, w których są wdrożone i zarządzane z mikrousług. Komputer lub maszynę Wirtualną, która jest częścią klastra jest nazywany węzłem. Zestawy skalowania maszyny wirtualnej są zasobami obliczeń platformy Azure używanego do wdrażania i zarządzania nimi jako zestaw kolekcji maszyn wirtualnych. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Następnie można zarządzać osobno każdy typ węzła. Po utworzeniu klastra sieci szkieletowej usług, można skalować typ węzła klastra w pionie (zmienić zasoby węzłów) lub Uaktualnij system operacyjny tego typu węzła maszyn wirtualnych.  Można skalować klastra w dowolnym momencie, nawet jeśli obciążeń są uruchomione w klastrze.  Ponieważ skalowanie klastra, aplikacje automatycznie skalować również.

> [!WARNING]
> Firma Microsoft zaleca, że należy zmieniać SKU maszyny Wirtualnej typu węzeł/zestaw skalowania, chyba że jest uruchomiona na [Silver trwałości lub większą](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjnego danych w miejscu infrastruktury. Możliwość opóźnienia lub monitora tę zmianę może to oznaczać, że operacji może spowodować utratę danych dla stanowych usług lub spowodować innych nieprzewidzianych problemów z działaniem, nawet w przypadku obciążeń bezstanowych. 
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Uaktualnij rozmiaru i systemu operacyjnego typu węzła podstawowego maszyny wirtualne
Oto proces aktualizowania rozmiar maszyny Wirtualnej i systemu operacyjnego typu węzła podstawowego maszyn wirtualnych.  Po uaktualnieniu typ węzła podstawowego maszyny wirtualne są rozmiar D4_V2 standardowe i uruchomiony system Windows Server Datacenter 2016 z kontenerami.

> [!WARNING]
> Przed podjęciem próby wykonania tej procedury na klastra produkcyjnego, zaleca się Przestudiowanie przykładowe szablony oraz weryfikowanie procesu w odniesieniu do klastra testowego. Klaster również jest niedostępny w czasie.

1. Wdrażanie klastra początkowego dwa typy węzłów i skali dwa zestawy (skala jednego na typ węzła) przy użyciu tych przykład [szablonu](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) i [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) plików.  Oba zestawy skali są D2_V2 standardowy rozmiar i uruchomiony system Windows Server 2012 R2 Datacenter.  Poczekaj, aż klaster, aby dokończyć uaktualnianie linii bazowej.   
2. Opcjonalnie — wdrożenie przykładowego stanowe do klastra.
3. Po podjęciu decyzji uaktualnić typu węzła podstawowego maszyn wirtualnych, należy dodać skali nowego typu węzła podstawowego przy użyciu tych przykład [szablonu](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) i [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) pliki, więc typ węzła podstawowego zawiera teraz dwa zestawy skalowania.  System usług i aplikacji użytkownika mogą migracji między maszynami wirtualnymi w dwa zestawy skalowania różne.  Nowy zestaw skali maszyny wirtualne są rozmiaru D4_V2 standardowe i uruchom systemu Windows Server Datacenter 2016 z kontenerów.  Nowy moduł równoważenia obciążenia i publiczny adres IP dodawane są także nowy zestaw skali.  
    Aby znaleźć nowej skali ustawiona w szablonie, wyszukaj "Microsoft.Compute/virtualMachineScaleSets" zasobu o nazwie *vmNodeType2Name* parametru.  Nowy zestaw skali zostanie dodany do przy użyciu typu węzła podstawowego -> właściwości virtualMachineProfile - > extensionProfile -> Rozszerzenia -> Właściwości -> Ustawienia -> Ustawienia wartość nodetyperef równą.
4. Sprawdź stan klastra i upewnij się, że wszystkie węzły są w dobrej kondycji.
5. Wyłącz w węzłach starego zestaw skali z zamiarem Usuń węzeł typu węzła podstawowego. Można wyłączyć w całości i operacje są umieszczane w kolejce. Poczekaj, aż wszystkie węzły są wyłączone, co może zająć trochę czasu.  Jako starsze węzłów typu węzła jest wyłączone, system węzłów usługi i inicjatora migracji z maszynami wirtualnymi nowej skali w typ węzła podstawowego.
6. Usuń starsze skali ustawiony na podstawie typu węzła podstawowego.
7. Usuń moduł równoważenia obciążenia skojarzone z stary zestaw skali. Klaster jest niedostępna, gdy nowego publicznego adresu IP adres i obciążenia równoważenia są skonfigurowane do nowego zestawu skali.  
8. Ustawienia DNS sklepu publicznego adresu IP skojarzonego z węzła starego podstawowego wpisz zestaw skali w zmiennej i usunąć ten publiczny adres IP.
9. Zastąp ustawienia DNS publicznego adresu IP skojarzonego z nowej skali typu węzła podstawowego ustawiony za pomocą ustawień DNS usunięto publiczny adres IP.  Klaster będzie teraz znowu dostępne.
10. Usuń stan węzła węzeł z klastra.  Jeśli poziom trwałości stary zestaw skali był silver lub złota, ten krok jest przeprowadzana automatycznie przez system.
11. Jeśli wdrożono stanowe aplikacji w poprzednim kroku, sprawdzenie aplikacji.

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

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalność aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure lub](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure obliczeniowe zestawu SDK.
* [Skalowanie klastra standaone przychodzący lub wychodzący](service-fabric-cluster-windows-server-add-remove-nodes.md).

