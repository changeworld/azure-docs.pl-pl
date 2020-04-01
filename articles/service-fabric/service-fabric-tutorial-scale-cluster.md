---
title: skalowanie klastra usługi Service Fabric na platformie Azure
description: W tym samouczku dowiesz się, jak skalować klaster sieci szkieletowej usług na platformie Azure w trybie out and in oraz jak oczyścić resztki zasobów.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: f1b813576a94541cdc2ab0a67fea71b6f49696c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78251795"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Samouczek: skalowanie klastra usługi Service Fabric na platformie Azure

Ten samouczek jest częścią trzecią serii i pokazuje, jak skalować istniejący klaster w poziomie i w. Ukończenie tego samouczka pozwoli Ci uzyskać wiedzę na temat skalowania klastra i czyszczenia pozostałych zasobów.  Aby uzyskać więcej informacji na temat skalowania klastra działającego na platformie Azure, przeczytaj artykuł [Skalowanie klastrów sieci szkieletowej usług](service-fabric-cluster-scaling.md).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększanie zasobów węzłów (skalowanie w górę)

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

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Zainstaluj [platformę Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub [platformę Azure CLI](/cli/azure/install-azure-cli).
* Tworzenie bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) na platformie Azure

## <a name="important-considerations-and-guidelines"></a>Ważne zagadnienia i wytyczne

Obciążenia aplikacji zmieniają się wraz z czasem, czy istniejące usługi potrzebują więcej (lub mniej) zasobów?  [Dodawanie lub usuwanie węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) z typu węzła w celu zwiększenia lub zmniejszenia zasobów klastra.

Czy musisz dodać więcej niż 100 węzłów do klastra?  Pojedynczy typ/skalowanie węzła sieci szkieletowej usługi nie może zawierać więcej niż 100 węzłów/maszyn wirtualnych.  Aby skalować klaster poza 100 węzłów, [dodaj dodatkowe typy węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type).

Czy aplikacja ma wiele usług i czy którykolwiek z nich musi być publiczny lub internetowy?  Typowe aplikacje zawierają usługę bramy frontonu, która odbiera dane wejściowe od klienta i co najmniej jedną usługę zaplecza, które komunikują się z usługami frontonu. W takim przypadku zaleca się [dodanie co najmniej dwóch typów węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) do klastra.  

Czy twoje usługi mają różne potrzeby infrastrukturalne, takie jak większa pamięć RAM lub wyższe cykle procesora? Na przykład aplikacja zawiera usługę front-end i usługę zaplecza. Usługa front-end może działać na mniejszych maszynach wirtualnych (rozmiarach maszyn wirtualnych, takich jak D2), które mają porty otwarte dla Internetu. Usługa zaplecza jest jednak intensywnie obliczeniowa i musi działać na większych maszynach wirtualnych (o rozmiarach maszyn wirtualnych, takich jak D4, D6, D15), które nie są skierowane do Internetu. W takim przypadku zaleca się [dodanie dwóch lub więcej typów węzłów](#add-nodes-to-or-remove-nodes-from-a-node-type) do klastra. Dzięki temu każdy typ węzła mają różne właściwości, takie jak łączność z Internetem lub rozmiar maszyny Wirtualnej. Liczba maszyn wirtualnych może być skalowana niezależnie, jak również.

Podczas skalowania klastra platformy Azure należy pamiętać o następujących wskazówkach:

* Pojedynczy typ/skalowanie węzła sieci szkieletowej usługi nie może zawierać więcej niż 100 węzłów/maszyn wirtualnych.  Aby skalować klaster poza 100 węzłów, dodaj dodatkowe typy węzłów.
* Typy węzłów podstawowych z uruchomionymi obciążeniami produkcyjnymi powinny mieć [poziom trwałości][durability] gold lub silver i zawsze mają pięć lub więcej węzłów.
* Typy węzłów innych niż podstawowe z uruchomionymi stanowymi obciążeniami produkcyjnymi powinny zawsze mieć co najmniej pięć węzłów.
* Typy węzłów innych niż podstawowe z uruchomionymi obciążeniami produkcyjnymi bezstanowymi powinny zawsze mieć dwa lub więcej węzłów.
* Każdy typ węzła [poziomu trwałości][durability] Złota lub Srebra powinien zawsze mieć pięć lub więcej węzłów.
* Jeśli skalowanie w (usuwanie węzłów z) typ węzła podstawowego, nigdy nie należy zmniejszać liczbę wystąpień do mniej niż wymaga [tego poziom niezawodności.][reliability]

Aby uzyskać więcej informacji, zapoznaj się [ze wskazówkami dotyczącymi pojemności klastra](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Eksportowanie szablonu dla grupy zasobów

Po utworzeniu bezpiecznego [klastra systemu Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i pomyślnym skonfigurowaniu grupy zasobów wyeksportuj szablon Menedżera zasobów dla grupy zasobów. Eksportowanie szablonu umożliwia automatyzację przyszłych wdrożeń klastra i jego zasobów, ponieważ szablon zawiera całą kompletną infrastrukturę.  Aby uzyskać więcej informacji na temat eksportowania szablonów, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu witryny Azure portal](/azure/azure-resource-manager/manage-resource-groups-portal).

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do grupy zasobów zawierającej klaster (**sfclustertutorialgroup**, jeśli poniżej tego samouczka). 

2. W lewym okienku wybierz pozycję **Wdrożenia**lub wybierz łącze w obszarze **Wdrożenia**. 

3. Wybierz z listy najnowsze pomyślne wdrożenie.

4. W lewym okienku wybierz **pozycję Szablon,** a następnie wybierz pozycję **Pobierz,** aby wyeksportować szablon jako plik ZIP.  Zapisz szablon i parametry na komputerze lokalnym.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Dodawanie węzłów do lub usuwanie węzłów z typu węzła

Skalowanie i wysuw lub skalowanie w poziomie zmienia liczbę węzłów w klastrze. Podczas skalowania w lub w poziomie, należy dodać więcej wystąpień maszyny wirtualnej do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz plik *parameters.json.*  Jeśli klaster został wdrożony przy użyciu [przykładowego szablonu][template] w tym samouczku, istnieją trzy typy węzłów w klastrze i trzy parametry, które ustawiają liczbę węzłów dla każdego typu węzła: *nt0InstanceCount*, *nt1InstanceCount*i *nt2InstanceCount*.  *Nt1InstanceCount* parametr, na przykład ustawia liczbę wystąpień dla drugiego typu węzła i ustawia liczbę maszyn wirtualnych w zestawie skali skojarzonej maszyny wirtualnej.

Tak więc, aktualizując wartość *nt1InstanceCount* można zmienić liczbę węzłów w drugim typie węzła.  Należy pamiętać, że nie można skalować typ węzła do więcej niż 100 węzłów.  Typy węzłów innych niż podstawowe z uruchomionymi stanowymi obciążeniami produkcyjnymi powinny zawsze mieć co najmniej pięć węzłów. Typy węzłów innych niż podstawowe z uruchomionymi obciążeniami produkcyjnymi bezstanowymi powinny zawsze mieć dwa lub więcej węzłów.

W przypadku skalowania, usuwania węzłów z, typ węzła [poziom trwałości][durability] brązu należy [ręcznie usunąć stan tych węzłów](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  W przypadku warstwy trwałości Silver i Gold te kroki są wykonywane automatycznie przez platformę.

### <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu
Zapisz wszelkie zmiany w plikach *template.json* i *parameters.json.*  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Dodawanie typu węzła do klastra

Każdy typ węzła zdefiniowany w klastrze sieci szkieletowej usług uruchomionym na platformie Azure jest skonfigurowany jako [oddzielny zestaw skalowania maszyny wirtualnej.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie zarządzać oddzielnie. Można niezależnie skalować każdy typ węzła w górę lub w dół, mają otwarte różne zestawy portów i używać różnych metryk pojemności. Można również niezależnie zmienić jednostkę SKU systemu operacyjnego działającą w każdym węźle klastra, ale należy pamiętać, że nie można mieć kombinacji systemu Windows i Linuksa uruchomionego w przykładowym klastrze. Zestaw typu/skalowania pojedynczego węzła nie może zawierać więcej niż 100 węzłów.  Klaster można skalować poziomo do ponad 100 węzłów, dodając dodatkowe typy węzłów/zestawy skalowania. Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz plik *parameters.json.*  Jeśli klastra wdrożono przy użyciu [przykładowego szablonu][template] w tym samouczku, istnieją trzy typy węzłów w klastrze.  W tej sekcji można dodać czwarty typ węzła, aktualizując i wdrażając szablon Menedżera zasobów. 

Oprócz nowego typu węzła można również dodać skojarzony zestaw skalowania maszyny wirtualnej (który działa w osobnej podsieci sieci wirtualnej) i grupę zabezpieczeń sieci.  Można dodać nowy lub istniejący publiczny adres IP i zasoby modułu równoważenia obciążenia platformy Azure dla nowego zestawu skalowania.  Nowy typ węzła ma [poziom trwałości][durability] srebra i rozmiar "Standard_D2_V2".

W pliku *template.json* dodaj następujące nowe parametry:
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

W pliku *template.json* dodaj następujące nowe zmienne:
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

W pliku *template.json* dodaj nową podsieć do zasobu sieci wirtualnej:
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

W pliku *template.json* dodaj nowy publiczny adres IP i zasoby modułu równoważenia obciążenia:
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

W pliku *template.json* dodaj nowe zasoby sieciowej grupy zabezpieczeń i zestawu skalowania maszyny wirtualnej.  Właściwość NodeTypeRef we właściwościach rozszerzenia sieci szkieletowej usług zestawu skalowania maszyny wirtualnej mapuje określony typ węzła na zestaw skalowania.

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

W pliku *template.json* zaktualizuj zasób klastra i dodaj nowy typ węzła:
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

W pliku *parameters.json* dodaj następujące nowe parametry i wartości:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu
Zapisz wszelkie zmiany w plikach *template.json* i *parameters.json.*  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Usuwanie typu węzła z klastra
Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie, usuwając typ węzła (zestaw skalowania maszyny wirtualnej) i wszystkie jego węzły. Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze. W miarę skalowania klastra aplikacje są również skalowane automatycznie.

> [!WARNING]
> Za pomocą Remove-AzServiceFabricNodeType, aby usunąć typ węzła z klastra produkcyjnego nie jest zalecane do częstego używania. Jest to niebezpieczne polecenie, ponieważ usuwa zasób zestawu skalowania maszyny wirtualnej za typem węzła. 

Aby usunąć typ węzła, uruchom polecenie cmdlet [Remove-AzServiceFabricNodeType.](/powershell/module/az.servicefabric/remove-azservicefabricnodetype)  Typ węzła musi być [poziom trwałości][durability] Silver lub Gold Polecenie cmdlet usuwa zestaw skalowania skojarzony z typem węzła i zajmuje trochę czasu.  Następnie uruchom polecenie cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) na każdym z węzłów do usunięcia, co powoduje usunięcie stanu węzła i usunięcie węzłów z klastra. Jeśli istnieją usługi w węzłach, usługi są najpierw przenoszone do innego węzła. Jeśli menedżer klastra nie może znaleźć węzła repliki/usługi, operacja jest opóźniona/zablokowana.

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

## <a name="increase-node-resources"></a>Zwiększanie zasobów węzłów 
Po utworzeniu klastra sieci szkieletowej usług można skalować typ węzła klastra w pionie (zmieniać zasoby węzłów) lub uaktualnić system operacyjny maszyn wirtualnych typu węzła.  

> [!WARNING]
> Zaleca się, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu zestawu skalowania/węzła, chyba że jest uruchomiona przy trwałości Silver lub większej. Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją infrastruktury w miejscu destrukcyjną dla danych. Bez pewnej możliwości opóźnienia lub monitorowania tej zmiany, jest możliwe, że operacja może spowodować utratę danych dla usług stanowych lub spowodować inne nieprzewidziane problemy operacyjne, nawet dla obciążeń bezstanowych.

> [!WARNING]
> Zaleca się, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu węzła podstawowego, który jest niebezpieczną operacją i nieobsługiwał.  Jeśli potrzebujesz więcej pojemności klastra, można dodać więcej wystąpień maszyn wirtualnych lub dodatkowych typów węzłów.  Jeśli nie jest to możliwe, można utworzyć nowy klaster i [przywrócić stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra.  Jeśli nie jest to możliwe, można [zmienić jednostkę SKU maszyny Wirtualnej typu węzła podstawowego](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Aktualizowanie szablonu

[Eksportuj plik szablonu i parametrów](#export-the-template-for-the-resource-group) z grupy zasobów dla najnowszego wdrożenia.  Otwórz plik *parameters.json.*  Jeśli klastra wdrożono przy użyciu [przykładowego szablonu][template] w tym samouczku, istnieją trzy typy węzłów w klastrze.  

Rozmiar maszyn wirtualnych w drugim typie węzła jest ustawiany w parametrze *vmNodeType1Size.*  Zmień wartość parametru *vmNodeType1Size* z Standard_D2_V2 na [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), która podwaja zasoby każdego wystąpienia maszyny Wirtualnej.

Jednostka SKU maszyny Wirtualnej dla wszystkich typów trzech węzłów jest ustawiona w parametrze *vmImageSku.*  Ponownie, zmiana jednostki SKU maszyny Wirtualnej typu węzła należy podchodzić z ostrożnością i nie jest zalecane dla typu węzła podstawowego.

### <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu
Zapisz wszelkie zmiany w plikach *template.json* i *parameters.json.*  Aby wdrożyć zaktualizowany szablon, uruchom następujące polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Lub następujące polecenie interfejsu wiersza polecenia platformy Azure:
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie i usuwanie węzłów (skalowanie w poziomie i skalowanie)
> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększanie zasobów węzłów (skalowanie w górę)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Dodawanie i usuwanie typów węzłów (skalowanie w poziomie i skalowanie)
> * Zwiększanie zasobów węzłów (skalowanie w górę)

Przejdź do kolejnego samouczka, aby dowiedzieć się, jak uaktualnić środowisko uruchomieniowe klastra.
> [!div class="nextstepaction"]
> [Uaktualnianie środowiska uruchomieniowego klastra](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
