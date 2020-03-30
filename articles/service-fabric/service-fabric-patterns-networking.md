---
title: Wzorce sieci dla sieci szkieletowej usług Azure
description: W tym artykule opisano typowe wzorce sieci szkieletowej usług i jak utworzyć klaster przy użyciu funkcji sieci platformy Azure.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466294"
---
# <a name="service-fabric-networking-patterns"></a>Wzorce sieci szkieletowej usług
Klaster sieci szkieletowej usług Azure można zintegrować z innymi funkcjami sieci platformy Azure. W tym artykule pokażemy, jak utworzyć klastry, które korzystają z następujących funkcji:

- [Istniejąca sieć wirtualna lub podsieć](#existingvnet)
- [Statyczny publiczny adres IP](#staticpublicip)
- [Wewnętrzny moduł równoważenia obciążenia](#internallb)
- [Wewnętrzny i zewnętrzny moduł równoważenia obciążenia](#internalexternallb)

Sieć szkieletowa usług działa w standardowym zestawie skalowania maszyny wirtualnej. Wszelkie funkcje, których można użyć w zestawie skalowania maszyny wirtualnej, można używać z klastrem sieci szkieletowej usług. Sekcje sieci szablonów usługi Azure Resource Manager dla zestawów skalowania maszyn wirtualnych i sieci szkieletowej usług są identyczne. Po wdrożeniu w istniejącej sieci wirtualnej można łatwo włączyć inne funkcje sieci, takie jak Azure ExpressRoute, Azure VPN Gateway, sieciowa grupa zabezpieczeń i komunikacja równorzędna sieci wirtualnej.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Zezwalanie dostawcy zasobów sieci szkieletowej usług na wykonywanie zapytań do klastra

Sieć szkieletowa usług jest unikatowa w stosunku do innych funkcji sieciowych w jednym aspekcie. Portal [Azure](https://portal.azure.com) wewnętrznie używa dostawcy zasobów sieci szkieletowej usług do wywoływania klastra w celu uzyskania informacji o węzłach i aplikacjach. Dostawca zasobów sieci szkieletowej usług wymaga publicznie dostępnego dostępu przychodzącego do portu bramy HTTP (port 19080, domyślnie) w punkcie końcowym zarządzania. [Eksplorator sieci szkieletowej usług](service-fabric-visualizing-your-cluster.md) używa punktu końcowego zarządzania do zarządzania klastrem. Dostawca zasobów sieci szkieletowej usług używa również tego portu do wykonywania zapytań o informacje dotyczące klastra, aby wyświetlić je w witrynie Azure portal. 

Jeśli port 19080 nie jest dostępny od dostawcy zasobów sieci szkieletowej usług, w portalu pojawi się komunikat, taki jak *węzły nie znaleziono,* a lista węzłów i aplikacji jest pusta. Jeśli chcesz zobaczyć klaster w witrynie Azure portal, moduł równoważenia obciążenia musi uwidaczniać publiczny adres IP, a grupa zabezpieczeń sieci musi zezwalać na ruch przychodzący portu 19080. Jeśli konfiguracja nie spełnia tych wymagań, witryna Azure portal nie wyświetla stanu klastra.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Szablony

Wszystkie szablony sieci szkieletowej usług znajdują się w [usłudze GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Szablony w stanie jako —jest za pomocą następujących poleceń programu PowerShell. Jeśli wdrażasz istniejący szablon usługi Azure Virtual Network lub statyczny szablon publicznego adresu IP, najpierw przeczytaj sekcję [Ustawienia początkowe](#initialsetup) w tym artykule.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Konfiguracja początkowa

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

W poniższym przykładzie rozpoczynamy od istniejącej sieci wirtualnej o nazwie ExistingRG-vnet w grupie zasobów **ExistingRG.** Podsieć nosi nazwę domyślna. Te zasoby domyślne są tworzone podczas tworzenia standardowej maszyny wirtualnej (VM) za pomocą portalu Azure. Można utworzyć sieć wirtualną i podsieć bez tworzenia maszyny Wirtualnej, ale głównym celem dodania klastra do istniejącej sieci wirtualnej jest zapewnienie łączności sieciowej z innymi maszynami wirtualnymi. Tworzenie maszyny Wirtualnej daje dobry przykład, jak istniejąca sieć wirtualna jest zwykle używany. Jeśli klaster sieci szkieletowej usług używa tylko wewnętrznego modułu równoważenia obciążenia bez publicznego adresu IP, można użyć maszyny Wirtualnej i jej publicznego adresu IP jako bezpiecznego *pola szybkiego dostępu.*

### <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

Statyczny publiczny adres IP zazwyczaj jest dedykowanym zasobem, który jest zarządzany oddzielnie od maszyn wirtualnych lub maszyn wirtualnych, do których jest przypisany. Jest aprowisowana w dedykowanej grupie zasobów sieciowych (w przeciwieństwie do samej grupy zasobów klastra sieci szkieletowej usług). Utwórz statyczny publiczny adres IP o nazwie staticIP1 w tej samej grupie zasobów ExistingRG w portalu Azure lub przy użyciu programu PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Szablon sieci szkieletowej usług

W przykładach w tym artykule używamy szablonu sieci szkieletowej usług.json. Za pomocą standardowego kreatora portalu można pobrać szablon z portalu przed utworzeniem klastra. Można również użyć jednego z [przykładowych szablonów,](https://github.com/Azure-Samples/service-fabric-cluster-templates)takich jak [bezpieczny pięciowęzłowy klaster sieci szkieletowej usług.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Istniejąca sieć wirtualna lub podsieć

1. Zmień parametr podsieci na nazwę istniejącej podsieci, a następnie dodaj dwa nowe parametry, aby odwołać się do istniejącej sieci wirtualnej:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. Komentarz `nicPrefixOverride` atrybut , `Microsoft.Compute/virtualMachineScaleSets`ponieważ używasz istniejącej podsieci i została wyłączona ta zmienna w kroku 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Zmień `vnetID` zmienną, aby wskazywała istniejącą sieć wirtualną:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Usuń `Microsoft.Network/virtualNetworks` z zasobów, dzięki czemu platforma Azure nie tworzy nowej sieci wirtualnej:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Skomentuj sieć `dependsOn` wirtualną `Microsoft.Compute/virtualMachineScaleSets`z atrybutu , aby nie polegać na tworzeniu nowej sieci wirtualnej:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Wdrażanie szablonu:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po wdrożeniu sieć wirtualna powinna zawierać nowe maszyny wirtualne zestawu skalowania. Typ węzła zestawu skalowania maszyny wirtualnej powinien wyświetlać istniejącą sieć wirtualną i podsieć. Można również użyć protokołu RDP (Remote Desktop Protocol), aby uzyskać dostęp do maszyny wirtualnej, która była już w sieci wirtualnej, oraz do pingowania nowych maszyn wirtualnych zestawu skalowania:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Inny przykład można znaleźć [w innym przypadku, który nie jest specyficzny dla sieci szkieletowej usług](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

1. Dodawanie parametrów nazwy istniejącej statycznej grupy zasobów IP, nazwy i w pełni kwalifikowanej nazwy domeny (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Usuń `dnsName` parametr. (Statyczny adres IP już go ma).

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Dodaj zmienną, aby odwołać się do istniejącego statycznego adresu IP:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Usuń `Microsoft.Network/publicIPAddresses` z zasobów, dzięki czemu platforma Azure nie tworzy nowego adresu IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Skomentuj adres `dependsOn` IP `Microsoft.Network/loadBalancers`z atrybutu , aby nie polegać na tworzeniu nowego adresu IP:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. W `Microsoft.Network/loadBalancers` zasobie zmień `publicIPAddress` element, `frontendIPConfigurations` aby odwołać się do istniejącego statycznego adresu IP zamiast nowo utworzonego:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. W `Microsoft.ServiceFabric/clusters` zasobie `managementEndpoint` zmień numer FQDN DNS statycznego adresu IP. Jeśli używasz bezpiecznego klastra, upewnij się, że zmienisz *http://* *na https://*. (Należy zauważyć, że ten krok dotyczy tylko klastrów sieci szkieletowej usług. Jeśli używasz zestawu skalowania maszyny wirtualnej, pomiń ten krok.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Wdrażanie szablonu:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Po wdrożeniu widać, że moduł równoważenia obciążenia jest powiązany z publicznym statycznym adresem IP z innej grupy zasobów. Punkt końcowy połączenia klienta sieci szkieletowej usług i punkt końcowy [Eksploratora sieci](service-fabric-visualizing-your-cluster.md) szkieletowej usług wskazują nazwy FQDN DNS statycznego adresu IP.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia

W tym scenariuszu zastępuje zewnętrznego modułu równoważenia obciążenia w domyślnym szablonie sieci szkieletowej usług moduł równoważenia obciążenia tylko wewnętrznego. Zobacz [wcześniej w tym artykule,](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) aby uzyskać implikacje dla witryny Azure portal i dostawcy zasobów sieci szkieletowej usług.

1. Usuń `dnsName` parametr. (To nie jest potrzebne.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcjonalnie, jeśli używasz metody alokacji statycznej, można dodać statyczny parametr adresu IP. Jeśli używasz metody alokacji dynamicznej, nie trzeba wykonać tego kroku.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Usuń `Microsoft.Network/publicIPAddresses` z zasobów, dzięki czemu platforma Azure nie tworzy nowego adresu IP:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Usuń atrybut `dependsOn` adresu IP `Microsoft.Network/loadBalancers`, aby nie polegać na tworzeniu nowego adresu IP. Dodaj atrybut `dependsOn` sieci wirtualnej, ponieważ moduł równoważenia obciążenia zależy teraz od podsieci z sieci wirtualnej:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Zmień `frontendIPConfigurations` ustawienie modułu równoważenia `publicIPAddress`obciążenia z używania `privateIPAddress`, do korzystania z podsieci i . `privateIPAddress`używa wstępnie zdefiniowanego statycznego wewnętrznego adresu IP. Aby użyć dynamicznego adresu `privateIPAddress` IP, usuń `privateIPAllocationMethod` element, a następnie zmień na **Dynamiczny**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. W `Microsoft.ServiceFabric/clusters` zasobie `managementEndpoint` zmień, aby wskazać adres wewnętrznego modułu równoważenia obciążenia. Jeśli używasz bezpiecznego klastra, upewnij się, że zmienisz *http://* na *https://*. (Należy zauważyć, że ten krok dotyczy tylko klastrów sieci szkieletowej usług. Jeśli używasz zestawu skalowania maszyny wirtualnej, pomiń ten krok.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Wdrażanie szablonu:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Po wdrożeniu moduł równoważenia obciążenia używa prywatnego statycznego adresu IP 10.0.0.250. Jeśli masz inną maszynę w tej samej sieci wirtualnej, możesz przejść do wewnętrznego punktu końcowego [Eksploratora sieci szkieletowej usług.](service-fabric-visualizing-your-cluster.md) Należy zauważyć, że łączy się z jednym z węzłów za modułem równoważenia obciążenia.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Wewnętrzny i zewnętrzny moduł równoważenia obciążenia

W tym scenariuszu należy rozpocząć od istniejącego modułu równoważenia obciążenia zewnętrznego typu pojedynczego węzła i dodać wewnętrzny moduł równoważenia obciążenia dla tego samego typu węzła. Port zaplecza dołączony do puli adresów zaplecza można przypisać tylko do pojedynczego modułu równoważenia obciążenia. Wybierz moduł równoważenia obciążenia, który będzie miał porty aplikacji, a który moduł równoważenia obciążenia będzie miał punkty końcowe zarządzania (porty 19000 i 19080). Jeśli punkty końcowe zarządzania zostaną umieszczone na wewnętrznym bilansirze obciążenia, należy pamiętać o ograniczeniach dostawcy zasobów sieci szkieletowej usług omówionych [wcześniej w artykule](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). W przykładzie, którego używamy, punkty końcowe zarządzania pozostają na zewnętrznym modułem równoważenia obciążenia. Należy również dodać port aplikacji port 80 i umieścić go na wewnętrznym modułem równoważenia obciążenia.

W klastrze typu dwóch węzłów jeden typ węzła znajduje się na zewnętrznym modułem równoważenia obciążenia. Drugi typ węzła znajduje się na wewnętrznym modułem równoważenia obciążenia. Aby użyć klastra typu dwuwęzłowego, w szablonie typu dwóch węzłów utworzonym przez portal (dostarczanym z dwoma modułami równoważenia obciążenia), przełącz drugi moduł równoważenia obciążenia na wewnętrzny moduł równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [sekcję Moduł równoważenia obciążenia tylko wewnętrznego.](#internallb)

1. Dodaj parametr adresu IP statycznego modułu wewnętrznego równoważenia obciążenia. (Informacje na temat używania dynamicznego adresu IP można znaleźć we wcześniejszych sekcjach tego artykułu).

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Dodaj parametr portu aplikacji 80.

3. Aby dodać wewnętrzne wersje istniejących zmiennych sieciowych, skopiuj je i wklej oraz dodaj "-Int" do nazwy:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Jeśli zaczniesz od szablonu wygenerowanego przez portal, który używa portu aplikacji 80, domyślny szablon portalu dodaje AppPort1 (port 80) na zewnętrznym modułem równoważenia obciążenia. W takim przypadku usuń AppPort1 z `loadBalancingRules` zewnętrznego modułu równoważenia obciążenia i sond, aby można było dodać go do wewnętrznego modułu równoważenia obciążenia:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Dodaj drugi `Microsoft.Network/loadBalancers` zasób. Wygląda podobnie do wewnętrznego modułu równoważenia obciążenia utworzonego w sekcji [Moduł równoważenia obciążenia tylko wewnętrznego,](#internallb) ale używa zmiennych modułu równoważenia obciążenia "-Int" i implementuje tylko port aplikacji 80. Spowoduje to `inboundNatPools`również usunięcie , aby zachować punkty końcowe RDP na publicznym równoważku obciążenia. Jeśli chcesz, aby prow wewnętrznego `inboundNatPools` modułu równoważenia obciążenia, przejdź z zewnętrznego modułu równoważenia obciążenia do tego wewnętrznego modułu równoważenia obciążenia:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. W `networkProfile` przypadku `Microsoft.Compute/virtualMachineScaleSets` zasobu dodaj wewnętrzną pulę adresów zaplecza:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Wdrażanie szablonu:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Po wdrożeniu można zobaczyć dwa moduły równoważenia obciążenia w grupie zasobów. Podczas przeglądania modułów równoważenia obciążenia do publicznego adresu IP i punktów końcowych zarządzania (porty 19000 i 19080) są przypisane do publicznego adresu IP. Można również zobaczyć statyczny wewnętrzny adres IP i punkt końcowy aplikacji (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia używają tej samej puli zaplecza ustawionej w skali maszyny wirtualnej.

## <a name="notes-for-production-workloads"></a>Uwagi dotyczące obciążeń produkcyjnych

Powyższe szablony GitHub są przeznaczone do pracy z domyślną jednostką SKU dla usługi Azure Standard Load Balancer (SLB), podstawową jednostką SKU. Ta slb nie ma umowy SLA, więc dla obciążeń produkcyjnych należy użyć standardowej jednostki SKU. Aby uzyskać więcej informacji na ten temat, zobacz [omówienie standardowego modułu równoważenia obciążenia platformy Azure](/azure/load-balancer/load-balancer-standard-overview). Każdy klaster sieci szkieletowej usług przy użyciu standardowej jednostki SKU dla slb musi upewnić się, że każdy typ węzła ma regułę zezwalającą na ruch wychodzący na porcie 443. Jest to konieczne do ukończenia instalacji klastra, a każde wdrożenie bez takiej reguły zakończy się niepowodzeniem. W powyższym przykładzie modułu równoważenia obciążenia "tylko wewnętrznego" do szablonu należy dodać dodatkowy moduł równoważenia obciążenia zewnętrznego z regułą zezwalającą na ruch wychodzący dla portu 443.

## <a name="next-steps"></a>Następne kroki
[Tworzenie klastra](service-fabric-cluster-creation-via-arm.md)

Po wdrożeniu można zobaczyć dwa moduły równoważenia obciążenia w grupie zasobów. Podczas przeglądania modułów równoważenia obciążenia do publicznego adresu IP i punktów końcowych zarządzania (porty 19000 i 19080) są przypisane do publicznego adresu IP. Można również zobaczyć statyczny wewnętrzny adres IP i punkt końcowy aplikacji (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia używają tej samej puli zaplecza ustawionej w skali maszyny wirtualnej.

