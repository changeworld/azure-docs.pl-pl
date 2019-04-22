---
title: Skalowanie klastra usługi Service Fabric na platformie Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak skalować klaster usługi Service Fabric na platformie Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fa9b091beacbc98c6939ec0454bd04da2b7561e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278704"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Samouczek: skalowanie klastra usługi Service Fabric na platformie Azure

Ten samouczek jest trzecią częścią serii i pokazuje, jak skalowanie istniejącego klastra, a w. Ukończenie tego samouczka pozwoli Ci uzyskać wiedzę na temat skalowania klastra i czyszczenia pozostałych zasobów.  Aby uzyskać więcej informacji na temat skalowania klastra działającego na platformie Azure, przeczytaj [skalowania usługi Service Fabric clusters](service-fabric-cluster-scaling.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększ zasoby węzła (skalowanie w górę)

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure przy użyciu szablonu
> * [Monitorowanie klastra](service-fabric-tutorial-monitor-cluster.md)
> * Skalowanie klastra w poziomie lub w pionie
> * [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)
> * [Usuwanie klastra](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj [programu Azure Powershell](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure

## <a name="important-considerations-and-guidelines"></a>Istotne zagadnienia i wytyczne

Zmień obciążeń aplikacji, wraz z upływem czasu, czy istniejących usług potrzebne zasoby więcej (lub mniej)?  [Dodawanie lub usuwanie węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu węzła, na zwiększanie lub zmniejszanie zasobów klastra.

Potrzebujesz dodać więcej niż 100 węzłów do klastra?  Jeden zestaw typu/skalowania węzła usługi Service Fabric nie może zawierać więcej niż 100 węzły/maszyny wirtualne.  Skalowanie klastra w ponad 100 węzłów [Dodaj dodatkowy węzeł typy](#add-nodes-to-or-remove-nodes-from-a-node-type).

Aplikacja ma wiele usług i czy któryś z nich muszą być publiczne lub połączone z Internetem?  Typowe aplikacje zawierają usługi frontonu bramy, który odbiera dane wejściowe z klienta i jeden lub więcej usług zaplecza, które komunikują się przy użyciu usługi frontonu. W takim przypadku firma Microsoft zaleca [Dodaj co najmniej dwa typy węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) do klastra.  

Czy usługi są potrzeb różnych infrastruktury, takich jak więcej pamięci RAM lub cykle procesora CPU wyższa? Na przykład aplikacja zawiera usługi frontonu i zaplecza. Usługa frontonu, można uruchomić na mniejszych maszynach wirtualnych (rozmiary maszyn wirtualnych na przykład D2), które mają otwarte porty niezbędne do Internetu. Usługi zaplecza, natomiast dużych obliczeń i musi uruchomić na większych maszyn wirtualnych (przy użyciu rozmiarów maszyn wirtualnych, takich jak D15 D4, D6,), nie będących internet skierowany. W tym przypadku zaleca się, że możesz [Dodaj co najmniej dwa typy węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) do klastra. Dzięki temu każdy typ węzła różne właściwości, takie jak łączność z Internetem lub rozmiar maszyny Wirtualnej. Liczba maszyn wirtualnych można skalować niezależnie, jak również.

Podczas skalowania w klastrze platformy Azure, należy przestrzegać następujących wytycznych:

* Jeden zestaw typu/skalowania węzła usługi Service Fabric nie może zawierać więcej niż 100 węzły/maszyny wirtualne.  Skalowanie ponad 100 węzłów klastra, Dodaj typy dodatkowego węzła.
* Typy węzła podstawowego obciążeń produkcyjnych powinny mieć [poziom trwałości] [ durability] Gold i Silver i zawsze mieć co najmniej pięć węzłów.
* typy węzłów innych niż podstawowe, obciążeń stanowych produkcyjnych zawsze powinien mieć co najmniej pięć węzłów.
* typy węzłów innych niż podstawowe, obciążeń produkcyjnych o bezstanowa zawsze powinien mieć co najmniej dwa węzły.
* Dowolny typ węzła [poziom trwałości] [ durability] Gold i Silver zawsze powinien mieć co najmniej pięć węzłów.
* W przypadku skalowania w (usuwania węzłów z) typu węzła podstawowego, nigdy nie powinno zmniejszyć liczbę wystąpień, aby była ona mniejsza niż co [poziom niezawodności] [ reliability] wymaga.

Aby uzyskać więcej informacji, przeczytaj [klastra pojemności wskazówki](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Eksportowanie szablonu dla grupy zasobów

Po utworzeniu bezpiecznego [klastra Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Konfigurowanie grupy zasobów, wyeksportuj szablon usługi Resource Manager dla grupy zasobów. Eksportowanie szablonu pozwala zautomatyzować procesy przyszłych wdrożeń klastra i jego zasobów, ponieważ szablon zawiera całej infrastruktury.  Aby uzyskać więcej informacji o eksportowaniu szablonów, przeczytaj [grup zasobów zarządzania usługi Azure Resource Manager przy użyciu witryny Azure portal](/azure/azure-resource-manager/manage-resource-groups-portal).

1. W [witryny Azure portal](https://portal.azure.com), przejdź do grupy zasobów zawierającej klaster (**sfclustertutorialgroup**, jeśli korzystasz z tego samouczka). 

2. W okienku po lewej stronie wybierz **wdrożeń**, lub wybierz link w obszarze **wdrożeń**. 

3. Wybierz najbardziej aktualną pomyślnego wdrożenia z listy.

4. W okienku po lewej stronie wybierz **szablonu** , a następnie wybierz **Pobierz** Aby wyeksportować szablon jako plik ZIP.  Zapisz szablon i parametry na komputerze lokalnym.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Dodaj węzły do lub usuwania węzłów z typu węzła

Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie zmiany liczby węzłów w klastrze. Skalowanie wewnątrz lub na zewnątrz, Dodaj więcej wystąpień maszyny wirtualnej do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablon i parametry](#export-the-template-for-the-resource-group) z grupy zasobów dla wdrożenia najbardziej aktualne.  Otwórz *parameters.json* pliku.  Jeśli wdrożono klaster przy użyciu [przykładowy szablon] [ template] w ramach tego samouczka, istnieją trzy typy węzłów w klastrze i trzy parametry, których Ustaw liczbę węzłów dla każdego typu węzła:  *nt0InstanceCount*, *nt1InstanceCount*, i *nt2InstanceCount*.  *Nt1InstanceCount* parametru, na przykład liczba wystąpień dla drugi typ węzła i ustawia liczbę maszyn wirtualnych w zestawie skalowania skojarzona maszyna wirtualna.

Tak, aktualizując wartości *nt1InstanceCount* zmianę liczby węzłów w drugi typ węzła.  Należy pamiętać, że nie można przeskalować typ węzła się do więcej niż 100 węzłów.  typy węzłów innych niż podstawowe, obciążeń stanowych produkcyjnych zawsze powinien mieć co najmniej pięć węzłów. typy węzłów innych niż podstawowe, obciążeń produkcyjnych o bezstanowa zawsze powinien mieć co najmniej dwa węzły.

Skalowania w usuwanie węzłów z typem węzła brązowa [poziom trwałości] [ durability] należy [ręcznie usunąć stan tych węzłów](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Warstwa trwałości Silver i Gold te kroki są wykonywane automatycznie przez platformę.

### <a name="deploy-the-updated-template"></a>Wdrożyć zaktualizowany szablon
Zapisz zmiany wprowadzone w *template.json* i *parameters.json* plików.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Lub następującego polecenia wiersza polecenia platformy Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Dodaj typ węzła do klastra

Każdy typ węzła, który jest zdefiniowany w klastrze usługi Service Fabric działających na platformie Azure jest konfigurowany jako [oddzielić zestawu skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być zarządzany oddzielnie. Możesz niezależnie skalować każdy typ węzła w górę lub w dół, mają różne zestawy otwartych portów i użyj różne metryki pojemności. Możesz również niezależnie Zmiana jednostki SKU systemu operacyjnego, uruchomione w każdym węźle klastra, ale należy pamiętać, że nie może mieć zarówno Windows i Linux działającego w klastrze próbki. Zestaw typów/skalowania pojedynczego węzła nie może zawierać więcej niż 100 węzłów.  Przez dodanie dodatkowego węzła typy/zestawów skalowania można skalować klastra w poziomie do więcej niż 100 węzłów. Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablon i parametry](#export-the-template-for-the-resource-group) z grupy zasobów dla wdrożenia najbardziej aktualne.  Otwórz *parameters.json* pliku.  Jeśli wdrożono klaster przy użyciu [przykładowy szablon] [ template] w ramach tego samouczka, istnieją trzy typy węzłów w klastrze.  W tej sekcji dodasz czwarty typ węzła, aktualizowanie i wdrażanie szablonu usługi Resource Manager. 

Oprócz nowego typu węzła możesz również dodać skojarzonej maszyny wirtualnej zestawu skalowania, (która jest uruchamiana w osobnej podsieci sieci wirtualnej) i sieciowe grupy zabezpieczeń.  Można dodać nowego lub istniejącego publicznego adresu IP i zasobów modułu równoważenia obciążenia platformy Azure dla nowego zestawu skalowania.  Nowy typ węzła ma [poziom trwałości] [ durability] Silver i rozmiar "Maszyna wirtualna Standard_D2_V2".

W *template.json* Dodaj nowe następujące parametry:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

W *template.json* plików, Dodaj następujące zmienne na nowy:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

W *template.json* plików, Dodawanie nowej podsieci do zasobu sieci wirtualnej:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

W *template.json* Dodaj nowe zasoby publicznych adresów IP adres i obciążenia równoważenia:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

W *template.json* plików, dodawanie nowych sieci zabezpieczeń maszyn wirtualnych i grupy zasobów zestawu skalowania.  Właściwość elementu NodeTypeRef we właściwościach rozszerzenia usługi Service Fabric, zestawu skalowania maszyn wirtualnych mapuje do typu określonego węzła zestawu skalowania.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

W *template.json* pliku, zaktualizuj zasób klastra i dodać nowy typ węzła:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

W *parameters.json* plików, Dodaj następujące nowe parametry i wartości:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Wdrożyć zaktualizowany szablon
Zapisz zmiany wprowadzone w *template.json* i *parameters.json* plików.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Lub następującego polecenia wiersza polecenia platformy Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Usuń typ węzła z klastra
Po utworzeniu klastra usługi Service Fabric, klaster można skalować w poziomie, usuwając typ węzła (zestaw skalowania maszyn wirtualnych) i wszystkich jego węzłów. Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze. Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

> [!WARNING]
> Przy użyciu AzServiceFabricNodeType Usuń, aby usunąć typ węzła z klastra produkcyjnego nie jest zalecane do użycia na częste. Jest to niebezpieczne polecenie jak usuwa zasobu zestawu skalowania maszyny wirtualnej za typ węzła. 

Aby usunąć typ węzła, uruchom [AzServiceFabricNodeType Usuń](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) polecenia cmdlet.  Typ węzła musi być na poziomie Silver lub Gold [poziom trwałości] [ durability] polecenie cmdlet usuwa zestaw skalowania, skojarzone z typem węzła i zajmuje trochę czasu.  Następnie uruchom [ServiceFabricNodeState Usuń](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) polecenie cmdlet na każdym z węzłów do usunięcia, które usuwa stan węzła i węzłów z klastra. W przypadku usług w węzłach, następnie usług są najpierw przenieść do innego węzła. Jeśli Menedżer klastra nie można odnaleźć węzła dla repliki/usługi, operacja jest opóźnione zablokowane.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Zwiększ zasoby węzła 
Po utworzeniu klastra usługi Service Fabric można skalować do typu węzła klastra w pionie (zmienić zasoby węzłów) lub Uaktualnij system operacyjny węzła typu maszyn wirtualnych.  

> [!WARNING]
> Firma Microsoft zaleca, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu węzeł/zestaw skalowania, chyba że jest uruchomiona w trwałości Silver lub większą. Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjne danych w miejscu infrastruktury. Bez niektóre możliwość opóźnienia lub monitora, ta zmiana jest możliwe, że operacja może spowodować utratę danych w przypadku usług stanowych lub powodować inne nieprzewidziane problemy operacyjne, nawet w przypadku obciążeń bezstanowych.

> [!WARNING]
> Firma Microsoft zaleca, nie należy zmieniać jednostki SKU maszyny Wirtualnej typu węzła podstawowego, co jest operacją niebezpieczne i nieobsługiwane.  Jeśli potrzebujesz większej pojemności klastra, możesz dodać więcej wystąpień maszyn wirtualnych lub typy dodatkowych węzłów.  Jeśli nie jest to możliwe, możesz utworzyć nowy klaster i [Przywróć stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra.  Jeśli nie jest to możliwe, możesz to zrobić [zmiany jednostki SKU maszyny Wirtualnej typu węzła podstawowego](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablon i parametry](#export-the-template-for-the-resource-group) z grupy zasobów dla wdrożenia najbardziej aktualne.  Otwórz *parameters.json* pliku.  Jeśli wdrożono klaster przy użyciu [przykładowy szablon] [ template] w ramach tego samouczka, istnieją trzy typy węzłów w klastrze.  

Rozmiar maszyn wirtualnych w drugi typ węzła jest ustawiana w *vmNodeType1Size* parametru.  Zmiana *vmNodeType1Size* wartość parametru maszyna wirtualna Standard_D2_V2 do [maszyna wirtualna Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), które rozwiązanie quorum zwiększa dwukrotnie zasobów w każdym wystąpieniu maszyny Wirtualnej.

Jednostka SKU maszyny Wirtualnej dla wszystkich typów z trzema węzłami jest ustawiana w *vmImageSku* parametru.  Ponownie Zmiana jednostki SKU maszyny Wirtualnej typu węzła powinna być skontaktowali się z ostrożnością i nie jest zalecane dla typu węzła podstawowego.

### <a name="deploy-the-updated-template"></a>Wdrożyć zaktualizowany szablon
Zapisz zmiany wprowadzone w *template.json* i *parameters.json* plików.  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Lub następującego polecenia wiersza polecenia platformy Azure:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększ zasoby węzła (skalowanie w górę)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
Skalowanie w pionie ND))
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększ zasoby węzła (skalowanie w górę)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
