---
title: Wdrażanie klastra usługi Azure Service Fabric w różnych strefach dostępności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster usługi Azure Service Fabric w różnych strefach dostępności.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077458"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Wdrażanie klastra usługi Azure Service Fabric w różnych strefach dostępności
Strefy dostępności na platformie Azure to oferta, która chroni aplikacje i dane przed awariami centrów danych o wysokiej dostępności. Strefa dostępności to unikatowe lokalizacji fizycznej, wyposażone w niezależne zasilanie, chłodzenie i usługi sieciowe w obrębie regionu platformy Azure.

Usługa Service Fabric obsługuje klastry, które rozciągają się w strefach dostępności, wdrażając typy węzłów, które są przypięte do określonej strefy. Zapewni to wysoką dostępność aplikacji. Strefy dostępności platformy Azure są dostępne tylko w wybranych regionach. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Dostępne są przykładowe szablony: [Usługa Service Fabric cross Szablon strefy dostępności](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Zalecane topologii dla typu węzła podstawowego klastrów usługi Azure Service Fabric, dzieląc go w strefach dostępności
Klaster usługi Service Fabric rozproszonych w różnych strefach dostępności zapewnia wysoką dostępność stanu klastra. Na klastrze usługi Service Fabric w strefach, należy utworzyć typu węzła podstawowego w każdej strefie dostępności obsługiwane przez region. To spowoduje równomierne rozłożenie węzły obiektów początkowych w każdego typu węzła podstawowego.

Topologia zalecana dla typu węzła podstawowego wymaga zasobów przedstawionych poniżej:

* Poziom niezawodności klastra Ustaw platynowy.
* Trzy typy węzeł oznaczony jako podstawowy.
    * Każdy typ węzła powinny być mapowane na swój własny zestaw skalowania maszyn wirtualnych znajdujących się w różnych strefach.
    * Każdy zestaw skali maszyny wirtualnej powinien mieć co najmniej pięciu węzłów (trwałość Silver).
* Pojedynczy publiczny zasób adresu IP, przy użyciu standardowej jednostki SKU.
* Pojedynczy zasób modułu równoważenia obciążenia przy użyciu standardowej jednostki SKU.
* Sieciowa grupa zabezpieczeń odwołuje się podsieci, w której wdrażać swoje zestawy skalowania maszyn wirtualnych.

>[!NOTE]
> Zestaw skalowania maszyn wirtualnych, właściwość grupy umieszczania pojedynczego muszą być ustawione na wartość true, ponieważ usługa Service Fabric obsługuje zestaw skalowania pojedynczej maszyny wirtualnej, który obejmuje strefy.

 ![Architektura strefy dostępności sieci szkieletowej usługi platformy Azure][sf-architecture]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny adres IP i zasobu modułu równoważenia obciążenia
Aby włączyć stref na skalowania maszyn wirtualnych z właściwością zasobów, moduł równoważenia obciążenia i zasób adresu IP, które odwołuje się ten zestaw skalowania maszyn wirtualnych zarówno używać *standardowa* jednostki SKU. Tworzenie modułu równoważenia obciążenia lub zasób adresu IP bez właściwości SKU zostanie utworzona podstawowa jednostka SKU, która nie obsługuje stref dostępności. Moduł równoważenia obciążenia standardowej jednostki SKU blokuje cały ruch z zewnątrz domyślnie; Aby zezwolić na zewnątrz ruchu, sieciowej grupy zabezpieczeń musi zostać wdrożony do tej podsieci.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Nie jest możliwe wykonanie zmiany w miejscu, jednostki SKU na publiczny adres IP i Obciążenie zasobów modułu równoważenia. Jeśli migrujesz z istniejących zasobów, które mają podstawowej jednostki SKU, zobacz sekcję migracji, części tego artykułu.

### <a name="virtual-machine-scale-set-nat-rules"></a>Reguły translatora adresów Sieciowych zestawu skalowania maszyn wirtualnych
Moduł równoważenia obciążenia reguły dla ruchu przychodzącego translatora adresów Sieciowych powinny odpowiadać pule translatora adresów Sieciowych z zestawu skalowania maszyn wirtualnych. Każdy zestaw skali maszyny wirtualnej musi mieć unikatowy pulę translatora adresów Sieciowych dla ruchu przychodzącego.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardowe reguły ruchu wychodzącego modułu równoważenia obciążenia jednostki SKU
Standardowy moduł równoważenia obciążenia i standardowego publicznego adresu IP wprowadzenia nowych możliwości i różne zachowania do połączenia wychodzącego w porównaniu do korzystania z podstawowych jednostek SKU. Jeśli chcesz korzystać z łączności wychodzącej podczas pracy ze standardowej jednostki SKU, należy jawnie można zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowego publicznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [połączeń wychodzących](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) i [standardowa usługi Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardowego szablonu odwołuje się do sieciowej grupy zabezpieczeń, co pozwala cały ruch wychodzący domyślnie. Ruch przychodzący jest ograniczony do portów, które są wymagane dla operacji zarządzania usługi Service Fabric. Reguły sieciowej grupy zabezpieczeń można zmodyfikować zgodnie z wymaganiami.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Włączanie strefy na zestaw skalowania maszyn wirtualnych zestawu
Aby włączyć strefę, w skali maszyny wirtualnej zestawu musi zawierać następujące trzy wartości zasobu zestawu skalowania maszyny wirtualnej.

* Pierwsza wartość jest **stref** właściwość, która określa strefę dostępności, który zostanie wdrożony zestaw skalowania maszyn wirtualnych.
* Druga wartość jest właściwość "singlePlacementGroup", który musi być ustawiony na wartość true.
* Trzecia wartość jest właściwość "faultDomainOverride" rozszerzenie zestawu skalowania maszyn wirtualnych usługi Service Fabric. Wartość tej właściwości powinno obejmować regionu i strefy, w którym zostanie umieszczony ten zestaw skalowania maszyn wirtualnych. Przykład: "faultDomainOverride": "eastus/az1" zasobów zestawu wszystkich skalowania maszyn wirtualnych muszą być umieszczone w tym samym regionie, ponieważ nie ma między region, obsługa klastrów usługi Azure Service Fabric.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Włączanie wielu podstawowych typów węzła w zasobie klastra usługi Service Fabric
Co najmniej jeden typ węzła jako podstawowy w zasobie klastra ustawia właściwość "isPrimary" wartość "true". W przypadku wdrażania klastra usługi Service Fabric w strefach dostępności, należy mieć trzy typy węzłów w różnych strefach.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
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
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Należy przeprowadzić migrację do strefy dostępności z klastra przy użyciu podstawowego modułu równoważenia obciążenia jednostki SKU i podstawowe IP jednostki SKU
Aby migracja klastra, który używał usługi równoważenia obciążenia i adres IP z podstawowej jednostki SKU, należy najpierw utworzyć zupełnie nowy zasób modułu równoważenia obciążenia i adres IP, używając standardowej jednostki SKU. Nie jest możliwe zaktualizować tych zasobów w miejscu.

Nowe równoważenia obciążenia i adresu IP powinien odwoływać się do nowego wielu stref dostępności typy węzłów, które chcesz użyć. W przykładzie powyżej skalowania maszyn wirtualnych z trzech nowych zasobów zestawu zostały dodane w strefach 1,2 i 3. Te skalowania maszyn wirtualnych ustawia odwołanie do nowo utworzonego modułu równoważenia obciążenia i adres IP i są oznaczone jako typy węzła podstawowego w zasobie klastra usługi Service Fabric.

Aby rozpocząć, należy dodać nowe zasoby do istniejącego szablonu usługi Resource Manager. Te zasoby obejmują:
* Zasób publicznego adresu IP przy użyciu standardowej jednostki SKU.
* Zasób modułu równoważenia obciążenia przy użyciu standardowej jednostki SKU.
* Sieciowa grupa zabezpieczeń odwołuje się podsieci, w której wdrażać swoje zestawy skalowania maszyn wirtualnych.
* Trzy typy węzeł oznaczony jako podstawowy.
    * Każdy typ węzła powinny być mapowane na swój własny zestaw skalowania maszyn wirtualnych znajdujących się w różnych strefach.
    * Każdy zestaw skali maszyny wirtualnej powinien mieć co najmniej pięciu węzłów (trwałość Silver).

Przykładem te zasoby można znaleźć w [przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Zasoby zostały wykonane, wdrażania, możesz rozpocząć Wyłącz węzłów na podstawowy typ węzła z oryginalnym klastrze. Ponieważ węzły są wyłączone, usługi systemowe zostaną zmigrowane do nowego typu węzła podstawowego, który miał został wdrożony w poprzednim kroku.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Gdy węzły są wszystkie wyłączone, usługi systemowe będą uruchomione na podstawowy typ węzła, który jest rozłożona się w różnych strefach. Następnie można usunąć wyłączonych węzłów z klastra. Gdy węzły zostaną usunięte, możesz usunąć oryginalny adres IP modułu równoważenia obciążenia, i zasobów zestawu skalowania maszyn wirtualnych.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Następnie należy usunąć odwołania do tych zasobów z szablonu usługi Resource Manager, która miała być wdrożona.

Ostatnim krokiem będzie obejmować Aktualizowanie nazwy DNS i publicznego adresu IP.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
