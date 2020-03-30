---
title: Wdrażanie klastra w strefach dostępności
description: Dowiedz się, jak utworzyć klaster sieci szkieletowej usług Azure w strefach dostępności.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609982"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Wdrażanie klastra sieci szkieletowej usługi Azure w strefach dostępności
Strefy dostępności na platformie Azure to oferta o wysokiej dostępności, która chroni aplikacje i dane przed awariami centrów danych. Strefa dostępności to unikatowa fizyczna lokalizacja wyposażona w niezależne zasilanie, chłodzenie i sieci w regionie platformy Azure.

Sieci szkieletowej usług obsługuje klastry, które obejmują strefy dostępności, wdrażając typy węzłów, które są przypięte do określonych stref. Zapewni to wysoką dostępność aplikacji. Strefy dostępności platformy Azure są dostępne tylko w wybranych regionach. Aby uzyskać więcej informacji, zobacz [Omówienie stref dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Przykładowe szablony są dostępne: [szablon strefy między dostępnością sieci szkieletowej usług](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Zalecana topologia dla podstawowego typu węzłów klastrów sieci szkieletowej usług Azure obejmujących strefy dostępności
Klaster sieci szkieletowej usług rozproszony w strefach dostępności zapewnia wysoką dostępność stanu klastra. Aby rozlinąć klaster sieci szkieletowej usług między strefami, należy utworzyć typ węzła podstawowego w każdej strefie dostępności obsługiwanej przez region. Spowoduje to równomierne rozłożenie węzłów źródłowych na każdy z typów węzłów podstawowych.

Zalecana topologia dla typu węzła podstawowego wymaga zasobów opisanych poniżej:

* Poziom niezawodności klastra ustawiony na Platinum.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinny być mapowane do własnego zestawu skalowania maszyny wirtualnej znajduje się w różnych strefach.
    * Każdy zestaw skalowania maszyny wirtualnej powinien mieć co najmniej pięć węzłów (Silver Durability).
* Pojedynczy publiczny zasób IP przy użyciu standardowej jednostki SKU.
* Zasób modułu równoważenia obciążenia przy użyciu standardowej jednostki SKU.
* Grupa sieciowej sieciowej, do której odwołuje się podsieć, w której można wdrożyć zestawy skalowania maszyny wirtualnej.

>[!NOTE]
> Właściwość grupy pojedynczego umieszczania zestawu pojedynczych miejsc docelowych maszyny wirtualnej musi być ustawiona na true, ponieważ sieć szkieletowa usług nie obsługuje zestawu skalowania pojedynczej maszyny wirtualnej, który obejmuje strefy.

 ![Architektura strefy dostępności sieci szkieletowej usług Azure][sf-architecture]

## <a name="networking-requirements"></a>Wymagania dotyczące sieci
### <a name="public-ip-and-load-balancer-resource"></a>Publiczny zasób ip i modułu równoważenia obciążenia
Aby włączyć właściwość zones na zasób zestawu skalowania maszyny wirtualnej, moduł równoważenia obciążenia i zasob IP, do którego odwołuje się ten zestaw skalowania maszyny wirtualnej, muszą używać *standardowej* jednostki SKU. Utworzenie modułu równoważenia obciążenia lub zasobu IP bez właściwości SKU spowoduje utworzenie podstawowej jednostki SKU, która nie obsługuje stref dostępności. Standardowy moduł równoważenia obciążenia SKU domyślnie zablokuje cały ruch z zewnątrz; aby zezwolić na ruch zewnętrzny, grupa sieciowa musi zostać wdrożona w podsieci.

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
> Nie jest możliwe do zmiany w miejscu jednostki SKU na publicznych zasobów IP i modułu równoważenia obciążenia. Jeśli przeprowadzasz migrację z istniejących zasobów, które mają podstawową jednostkę SKU, zobacz sekcję migracji w tym artykule.

### <a name="virtual-machine-scale-set-nat-rules"></a>Reguły skalowania maszyny wirtualnej ustawione na nat
Przychodzące reguły translatora adresów sieciowych modułu równoważenia obciążenia powinny być zgodne z pulami NAT z zestawu skalowania maszyny wirtualnej. Każdy zestaw skalowania maszyny wirtualnej musi mieć unikatową pulę przychodzącej usługi NAT.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Reguły wychodzące modułu SKU Load Balancer
Standardowy moduł równoważenia obciążenia i standardowy publiczny adres IP wprowadzają nowe możliwości i różne zachowania do łączności wychodzącej w porównaniu z wykorzystaniem podstawowych jednostek SKU. Jeśli chcesz łączności wychodzącej podczas pracy ze standardowymi jednostkami SKU, należy jawnie zdefiniować ją za pomocą standardowych publicznych adresów IP lub standardowego publicznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Połączenia wychodzące](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) i [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Standardowy szablon odwołuje się do grupy ndg, która domyślnie zezwala na cały ruch wychodzący. Ruch przychodzący jest ograniczony do portów, które są wymagane dla operacji zarządzania siecią szkieletową usług. Reguły sieciowej sieciowej sieciowej można zmodyfikować, aby spełnić twoje wymagania.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Włączanie stref w zestawie skalowania maszyny wirtualnej
Aby włączyć strefę, w zestawie skalowania maszyny wirtualnej należy uwzględnić następujące trzy wartości w zasób zestawu skalowania maszyny wirtualnej.

* Pierwszą wartością jest właściwość **zones,** która określa, w której strefie dostępności zostanie wdrożony zestaw skalowania maszyny wirtualnej.
* Druga wartość to właściwość "singlePlacementGroup", która musi być ustawiona na true.
* Trzecią wartością jest właściwość "faultDomainOverride" w rozszerzeniu zestawu skalowania maszyny wirtualnej sieci szkieletowej usług. Wartość dla tej właściwości powinna zawierać region i strefę, w której zostanie umieszczony ten zestaw skalowania maszyny wirtualnej. Przykład: "faultDomainOverride": "eastus/az1" Wszystkie zasoby zestawu skalowania maszyny wirtualnej muszą być umieszczone w tym samym regionie, ponieważ klastry sieci szkieletowej usługi Azure nie mają obsługi między regionami.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Włączanie wielu typów węzłów podstawowych w zasobie klastra sieci szkieletowej usług
Aby ustawić jeden lub więcej typów węzłów jako podstawowych w zasobie klastra, należy ustawić właściwość "isPrimary" na "true". Podczas wdrażania klastra sieci szkieletowej usług w strefach dostępności, powinny mieć trzy typy węzłów w różnych strefach.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migracja do stref dostępności z klastra przy użyciu podstawowego modułu równoważenia obciążenia jednostki SKU i podstawowej jednostki SKU IP
Aby przeprowadzić migrację klastra, który używał modułu równoważenia obciążenia i adresu IP z podstawową jednostką SKU, należy najpierw utworzyć całkowicie nowy moduł równoważenia obciążenia i zasób IP przy użyciu standardowej jednostki SKU. Nie jest możliwe zaktualizowanie tych zasobów w miejscu.

Nowe LB i IP powinny być odwoływane w nowych typach węzłów strefy dostępności krzyżowej, których chcesz użyć. W powyższym przykładzie dodano trzy nowe zasoby zestawu skalowania maszyny wirtualnej w strefach 1,2 i 3. Te zestawy skalowania maszyny wirtualnej odwołują się do nowo utworzonych lb i IP i są oznaczone jako typy węzłów podstawowych w zasobie klastra sieci szkieletowej usług.

Aby rozpocząć, należy dodać nowe zasoby do istniejącego szablonu Menedżera zasobów. Zasoby te obejmują:
* Publiczny zasób IP przy użyciu standardowej jednostki SKU.
* Zasób modułu równoważenia obciążenia przy użyciu standardowej jednostki SKU.
* Grupa sieciowej sieciowej, do której odwołuje się podsieć, w której można wdrożyć zestawy skalowania maszyny wirtualnej.
* Trzy typy węzłów oznaczone jako podstawowe.
    * Każdy typ węzła powinien być mapowany do własnego zestawu skalowania maszyny wirtualnej znajdującego się w różnych strefach.
    * Każdy zestaw skalowania maszyny wirtualnej powinien mieć co najmniej pięć węzłów (Silver Durability).

Przykład tych zasobów można znaleźć w [przykładowym szablonie](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Po zakończeniu wdrażania zasobów można rozpocząć wyłączanie węzłów w typie węzła podstawowego z oryginalnego klastra. Ponieważ węzły są wyłączone, usługi systemowe będą migrować do nowego typu węzła podstawowego, który został wdrożony w powyższym kroku.

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

Gdy węzły są wyłączone, usługi systemowe będą uruchamiane na typ węzła podstawowego, który jest rozłożony na strefy. Następnie można usunąć wyłączone węzły z klastra. Po usunięciu węzłów można usunąć oryginalne zasoby zestawu ip, modułu równoważenia obciążenia i zestawu skalowania maszyny wirtualnej.

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

Następnie należy usunąć odwołania do tych zasobów z wdrożonego szablonu Menedżera zasobów.

Ostatnim krokiem będzie aktualizowanie nazwy DNS i publicznego adresu IP.

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
