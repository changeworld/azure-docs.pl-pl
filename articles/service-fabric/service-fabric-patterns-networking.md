---
title: Wzorce sieci dla usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano typowe wzorce sieci dla usługi Service Fabric oraz sposób tworzenia klastra przy użyciu funkcji sieci platformy Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: d5aa09f3ff899766e6eb6d1784e4417f7b48eac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66110272"
---
# <a name="service-fabric-networking-patterns"></a>Wzorce sieci usługi Service Fabric
Klaster usługi Azure Service Fabric można zintegrować z inne funkcje sieci platformy Azure. W tym artykule pokazujemy, jak tworzyć klastry, korzystających z następujących funkcji:

- [Istniejącej sieci wirtualnej lub podsieci](#existingvnet)
- [Statyczny publiczny adres IP](#staticpublicip)
- [Moduł równoważenia obciążenia tylko do wewnętrznego](#internallb)
- [Moduł równoważenia obciążenia wewnętrznych i zewnętrznych](#internalexternallb)

Usługa Service Fabric działa w zestawie skalowania maszyny wirtualnej standardowego. Wszystkie funkcje, które można używać w zestawie skalowania maszyn wirtualnych, korzystania z klastra usługi Service Fabric. Części sieci szablonów usługi Azure Resource Manager dla zestawów skalowania maszyn wirtualnych i Service Fabric są identyczne. Po wdrożeniu do istniejącej sieci wirtualnej jest łatwo wdrożyć inne funkcje sieci, takich jak usługi Azure ExpressRoute, Brama sieci VPN platformy Azure, sieciowej grupy zabezpieczeń i komunikacji równorzędnej sieci wirtualnej.

Usługa Service Fabric jest niż inne funkcje sieci w jednym z aspektów. [Witryny Azure portal](https://portal.azure.com) wewnętrznie używa dostawcy zasobów usługi Service Fabric w celu wywołania do klastra, aby uzyskać informacje na temat węzłów i aplikacji. Dostawca zasobów usługi Service Fabric wymaga publicznie dostępu przychodzącego do portu bramy protokołu HTTP (port 19080, domyślnie) na punkt końcowy zarządzania. [Narzędzie Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) używa punktu końcowego zarządzania w celu zarządzania klastrem. Dostawcy zasobów usługi Service Fabric używa również ten port do wyszukiwania informacji o klastrze, aby wyświetlić w witrynie Azure portal. 

Jeśli portem 19080 nie jest dostępny od dostawcy zasobów usługi Service Fabric, wiadomości, takich jak *węzłów nie można odnaleźć* pojawia się w portalu i listy węzłów i aplikacja pojawi się pusta. Jeśli chcesz zobaczyć klastrem w witrynie Azure portal, moduł równoważenia obciążenia musi ujawniać publicznego adresu IP i sieciowej grupy zabezpieczeń muszą zezwalać na przychodzący ruch na porcie 19080. Jeśli ustawienia nie spełnia tych wymagań, witryny Azure portal nie wyświetla stan klastra.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Szablony

Wszystkie szablony usługi Service Fabric znajdują się w [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Powinno być możliwe do wdrożenia szablony jako — przy użyciu następujących poleceń programu PowerShell. Jeśli wdrażasz istniejącego szablonu usługi Azure Virtual Network lub statyczny publiczny szablon adresu IP, najpierw zapoznaj się z [początkowej instalacji](#initialsetup) dalszej części tego artykułu.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Konfiguracja początkowa

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

W poniższym przykładzie Zaczniemy z istniejącej sieci wirtualnej o nazwie ExistingRG między sieciami wirtualnymi, w **ExistingRG** grupy zasobów. Podsieć nosi nazwę domyślną. Te zasoby domyślne są tworzone podczas tworzenia standardowych maszyny wirtualnej (VM) za pomocą witryny Azure portal. Można utworzyć sieć wirtualną i podsieć, bez tworzenia maszyny Wirtualnej, ale głównym celem dodawania klastrze do istniejącej sieci wirtualnej jest zapewnienie łączności sieciowej do innych maszyn wirtualnych. Tworzenie maszyny Wirtualnej zapewnia dobrym przykładem sposobu istniejącej sieci wirtualnej zwykle jest używana. Jeśli klaster usługi Service Fabric używa tylko wewnętrznego modułu równoważenia obciążenia, bez publicznego adresu IP, możesz użyć maszyny Wirtualnej i jej publicznego adresu IP jako bezpieczny *okno przeskoku*.

### <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

Statyczny publiczny adres IP jest ogólnie jest dedykowanym zasobem, zarządzanym oddzielnie z maszyny Wirtualnej lub maszyny wirtualne, które jest przypisany do. Zainicjowano w dedykowanej grupie zasobów sieciowych (w przeciwieństwie do zasobu klastra usługi Service Fabric grupowania, sam). Utwórz statyczny publiczny adres IP o nazwie staticIP1 w tej samej grupie zasobów ExistingRG, w witrynie Azure portal lub przy użyciu programu PowerShell:

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

### <a name="service-fabric-template"></a>Szablon usługi Service Fabric

W przykładach w tym artykule używamy template.json usługi Service Fabric. Można użyć standardowego kreatora portalu, aby pobrać szablon z poziomu portalu, przed przystąpieniem do tworzenia klastra. Możesz również użyć jednej z [przykładowe szablony](https://github.com/Azure-Samples/service-fabric-cluster-templates), takiej jak [bezpiecznego klastra usługi Service Fabric z pięcioma węzłami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Istniejącej sieci wirtualnej lub podsieci

1. Zmień wartość parametru podsieci nazwę istniejącej podsieci, a następnie dodaj dwa nowe parametry, aby odwoływać się do istniejącej sieci wirtualnej:

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

2. Komentarz `nicPrefixOverride` atrybutu `Microsoft.Compute/virtualMachineScaleSets`, ponieważ w przypadku korzystania z istniejącej podsieci i wyłączono tej zmiennej w kroku 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Zmiana `vnetID` zmiennej, aby wskazywała istniejącą sieć wirtualną:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Usuń `Microsoft.Network/virtualNetworks` z Twoich zasobów, dlatego Azure nie tworzy nową sieć wirtualną:

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

5. Komentarz dla sieci wirtualnej z `dependsOn` atrybutu `Microsoft.Compute/virtualMachineScaleSets`, dzięki czemu użytkownik nie należy polegać na temat tworzenia nowej sieci wirtualnej:

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

    Po wdrożeniu sieci wirtualnej powinno obejmować nowego zestawu skalowania maszyn wirtualnych. Typ węzła zestawu skali maszyny wirtualnej powinien być wyświetlony w istniejącej sieci wirtualnej i podsieci. Można również uzyskiwać protokołu RDP (Remote Desktop) dostęp do maszyny Wirtualnej, która była już w sieci wirtualnej, a następnie wysłać polecenie ping nowej skali maszyn wirtualnych w zestawie:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Inny przykład, zobacz [taki, który nie jest specyficzne dla usługi Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statyczny publiczny adres IP

1. Dodawanie parametrów nazwę istniejącej grupy zasobów statycznych adresów IP, nazwy i w pełni kwalifikowaną nazwę domeny (FQDN):

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

2. Usuń `dnsName` parametru. (Statyczny adres IP już ma jeden).

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Dodawanie zmiennej można odwoływać się do istniejącego statyczny adres IP:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Usuń `Microsoft.Network/publicIPAddresses` z Twoich zasobów, dlatego Azure nie tworzy nowego adresu IP:

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

5. Komentarz do adresu IP z `dependsOn` atrybutu `Microsoft.Network/loadBalancers`, dzięki czemu użytkownik nie należy polegać na utworzenie nowego adresu IP:

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

6. W `Microsoft.Network/loadBalancers` zasobów, zmień `publicIPAddress` elementu `frontendIPConfigurations` można odwoływać się do istniejącego statyczny adres IP zamiast nowo utworzony:

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

7. W `Microsoft.ServiceFabric/clusters` zasobów, zmień `managementEndpoint` do nazwy FQDN DNS statyczny adres IP. Jeśli używasz zabezpieczonego klastra upewnij się, możesz zmienić *http://* do *https://*. (Zwróć uwagę, że ten krok ma zastosowanie tylko w klastrach usługi Service Fabric. Jeśli używasz zestawu skalowania maszyn wirtualnych, Pomiń ten krok).

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

Po wdrożeniu możesz zobaczyć, że moduł równoważenia obciążenia jest powiązana z publiczny statyczny adres IP z grupy zasobów. Punkt końcowy połączenia klienta usługi Service Fabric i [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) punkt programu endpoint dla nazwy FQDN DNS statyczny adres IP.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Moduł równoważenia obciążenia tylko do wewnętrznego

W tym scenariuszu zamienia zewnętrznym modułem równoważenia obciążenia w szablonie usługi Service Fabric domyślnego modułu równoważenia obciążenia wyłącznie wewnętrznym. Konsekwencje dla witryny Azure portal i dostawcy zasobów usługi Service Fabric zobacz temat w poprzedniej sekcji.

1. Usuń `dnsName` parametru. (Nie jest potrzebna.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcjonalnie Jeśli używasz metody alokacji statycznej, można dodać parametrem statyczny adres IP. Jeśli używasz metody dynamicznej alokacji, nie musisz wykonać ten krok.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Usuń `Microsoft.Network/publicIPAddresses` z Twoich zasobów, dlatego Azure nie tworzy nowego adresu IP:

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

4. Usuń adres IP `dependsOn` atrybutu `Microsoft.Network/loadBalancers`, dzięki czemu użytkownik nie należy polegać na utworzenie nowego adresu IP. Dodaj sieć wirtualną `dependsOn` atrybutu, ponieważ moduł równoważenia obciążenia zależy od teraz podsieci z sieci wirtualnej:

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

5. Zmiana modułu równoważenia obciążenia `frontendIPConfigurations` ustawienie z przy użyciu `publicIPAddress`, za pomocą podsieci i `privateIPAddress`. `privateIPAddress` używa wstępnie zdefiniowanych statyczne wewnętrzne adresy IP. Aby użyć dynamicznego adresu IP, Usuń `privateIPAddress` elementu, a następnie zmień `privateIPAllocationMethod` do **dynamiczne**.

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

6. W `Microsoft.ServiceFabric/clusters` zasobów, zmień `managementEndpoint` wskaż adres usługi równoważenia obciążenia wewnętrznego. Jeśli korzystasz z zabezpieczonym klastrem, upewnij się, możesz zmienić *http://* do *https://*. (Zwróć uwagę, że ten krok ma zastosowanie tylko w klastrach usługi Service Fabric. Jeśli używasz zestawu skalowania maszyn wirtualnych, Pomiń ten krok).

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

Po wdrożeniu modułu równoważenia obciążenia używa prywatnego 10.0.0.250 statyczny adres IP. Jeśli masz inną maszynę w tej samej sieci wirtualnej, możesz przejść do wewnętrznego [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) punktu końcowego. Należy pamiętać, że nawiązuje połączenie z jednym z węzłów za modułem równoważenia obciążenia.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Moduł równoważenia obciążenia wewnętrznych i zewnętrznych

W tym scenariuszu rozpoczynać istniejącego modułu równoważenia obciążenia zewnętrznych typu z jednym węzłem i Dodaj wewnętrznego modułu równoważenia obciążenia dla tego samego typu węzła. Port zaplecza, połączony z pulą adresów zaplecza można przypisać tylko do modułu równoważenia obciążenia. Wybierz, w której moduł równoważenia obciążenia będzie miał porty Twojej aplikacji, a które modułu równoważenia obciążenia będzie miał punktami końcowymi usługi zarządzania (porty 19000 i 19080). W przypadku punktów końcowych zarządzania zostanie umieszczony na wewnętrznego modułu równoważenia obciążenia, należy pamiętać, zasobów usługi Service Fabric ograniczenia dostawcy z opisem we wcześniejszej części tego artykułu. W tym przykładzie używamy, zarządzania, punktów końcowych pozostają w module równoważenia obciążenia zewnętrznych. Możesz również dodać port 80 port aplikacji i umieść ją na wewnętrznego modułu równoważenia obciążenia.

W klastrze typu węzła dwóch jeden typ węzła jest na zewnętrznym modułem równoważenia obciążenia. Typ węzła znajduje się na wewnętrznego modułu równoważenia obciążenia. Korzystanie z klastra typ węzła dwóch, w szablonie dwóch węzłów typu w portalu zostały utworzone, (które pochodzą z dwoma modułami równoważenia obciążenia), Przełącz drugi moduł równoważenia obciążenia do wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [tylko do wewnętrznego równoważenia](#internallb) sekcji.

1. Dodaj parametr adres IP modułu równoważenia statycznego wczytywnia wewnętrznego. (Aby uzyskać informacje powiązane z użyciem dynamiczny adres IP, zobacz wcześniejsze sekcje w tym artykule).

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Dodaj parametr portu 80 w aplikacji.

3. Dodaj wewnętrzne wersje istniejących sieci zmiennych, skopiuj i wklej je, a następnie dodaj "-Int" do nazwy:

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

4. W przypadku uruchomienia przy użyciu szablonu wygenerowanych przez portal, która korzysta z aplikacji portu 80, domyślnego szablonu portalu dodaje AppPort1 (port 80) w module równoważenia obciążenia zewnętrznych. W takim przypadku usuń AppPort1 z zewnętrznym modułem równoważenia obciążenia `loadBalancingRules` sond i, dzięki czemu można dodać go do wewnętrznego modułu równoważenia obciążenia:

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

5. Dodaj drugą `Microsoft.Network/loadBalancers` zasobów. Będzie on podobny do wewnętrznego modułu równoważenia obciążenia utworzony w [tylko do wewnętrznego równoważenia](#internallb) sekcji, ale używa "-Int" zmiennych modułu równoważenia obciążenia i implementuje tylko aplikacji port 80. Spowoduje to również usunięcie `inboundNatPools`, aby zachować punktów końcowych protokołu RDP na publiczny moduł równoważenia obciążenia. Protokół RDP na wewnętrznego modułu równoważenia obciążenia, przenieść `inboundNatPools` z zewnętrznego module równoważenia obciążenia do tego wewnętrznego modułu równoważenia obciążenia:

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

6. W `networkProfile` dla `Microsoft.Compute/virtualMachineScaleSets` zasobu, Dodawanie puli adresów zaplecza wewnętrznego:

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

Po wdrożeniu możesz zobaczyć dwa moduły równoważenia obciążenia w grupie zasobów. Jeśli możesz przeglądać modułów równoważenia obciążenia, zostanie wyświetlony publicznego adresu IP adres i zarządzania punktów końcowych (porty 19000 i 19080) przypisany publiczny adres IP. Widać również statyczne wewnętrzny adres i aplikacji punktu końcowego adresu IP (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia, użyj tej samej puli zaplecza zestawu skalowania maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie klastra](service-fabric-cluster-creation-via-arm.md) ternalLB.json
    ```

Po wdrożeniu możesz zobaczyć dwa moduły równoważenia obciążenia w grupie zasobów. Jeśli możesz przeglądać modułów równoważenia obciążenia, zostanie wyświetlony publicznego adresu IP adres i zarządzania punktów końcowych (porty 19000 i 19080) przypisany publiczny adres IP. Widać również statyczne wewnętrzny adres i aplikacji punktu końcowego adresu IP (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Oba moduły równoważenia obciążenia, użyj tej samej puli zaplecza zestawu skalowania maszyn wirtualnych.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie klastra](service-fabric-cluster-creation-via-arm.md)
