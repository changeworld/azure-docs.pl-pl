---
title: Wzorce sieci dla usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano typowe wzorce sieciowych dla sieci szkieletowej usług oraz sposobu tworzenia klastra przy użyciu funkcji obsługi sieci platformy Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: b180e62804b875ca4547a9d09f19efff32ae0cd9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207227"
---
# <a name="service-fabric-networking-patterns"></a>Wzorce sieci sieci szkieletowej usług
Klaster sieci szkieletowej usług Azure można zintegrować z innymi funkcjami sieci platformy Azure. W tym artykule zostanie przedstawiony zostanie sposób tworzenia klastrów korzystających z następujących funkcji:

- [Istniejącej sieci wirtualnej lub podsieci](#existingvnet)
- [Statycznego publicznego adresu IP](#staticpublicip)
- [Moduł równoważenia obciążenia tylko do wewnętrznego](#internallb)
- [Moduł równoważenia obciążenia wewnętrznych i zewnętrznych](#internalexternallb)

Sieć szkieletowa usług jest uruchamiany w zestaw skali maszyny wirtualnej standardowego. Wszystkie funkcje są dostępne w zestawie skalowania maszyn wirtualnych, korzystania z klastra sieci szkieletowej usług. Sieci sekcje szablonów usługi Azure Resource Manager zestawy skalowania maszyny wirtualnej i sieci szkieletowej usług są identyczne. Po wdrożeniu do istniejącej sieci wirtualnej jest łatwo wdrożyć inne funkcje sieci, takich jak usługa Azure ExpressRoute, Brama sieci VPN platformy Azure, sieciowej grupy zabezpieczeń i sieci wirtualnej komunikacji równorzędnej.

Sieć szkieletowa usług to inaczej niż inne funkcje sieci w jednym aspekcie. [Portalu Azure](https://portal.azure.com) wewnętrznie używa dostawcy zasobów sieci szkieletowej usług do wywołania do klastra, aby uzyskać informacje na temat węzłów i aplikacji. Dostawcy zasobów sieci szkieletowej usług wymaga publicznie dostępnym dla ruchu przychodzącego dostępu do portu bramy HTTP (port 19080, domyślnie) dotyczące punktu zarządzania. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) używa punkt końcowy zarządzania do zarządzania klastrem. Dostawcy zasobów sieci szkieletowej usług również używa tego portu do wyszukiwania informacji o klastrze, aby wyświetlić w portalu Azure. 

Jeśli port 19080 nie jest dostępny od dostawcy zasobów sieci szkieletowej usług, takich jak wiadomość *węzłów nie można odnaleźć* pojawia się w portalu i listy węzeł i aplikacja zostanie wyświetlona pusta. Jeśli chcesz wyświetlić klastrem w portalu Azure, moduł równoważenia obciążenia musi ujawniać publicznego adresu IP i swojej grupy zabezpieczeń sieciowych muszą zezwalać na ruch przychodzący port 19080. Jeśli ustawienia nie spełnia te wymagania, stan klastra nie wyświetlany w portalu Azure.

## <a name="templates"></a>Szablony

Wszystkie szablony usługi sieć szkieletowa znajdują się w [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Powinno być możliwe do wdrażania szablonów jako — za pomocą następujących poleceń programu PowerShell. Jeśli wdrażasz istniejącego szablonu usługi Azure Virtual Network lub statycznego publicznego szablon adresu IP, najpierw przeczytać artykuł [początkowej instalacji](#initialsetup) sekcji tego artykułu.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Początkowej konfiguracji

### <a name="existing-virtual-network"></a>Istniejącej sieci wirtualnej

W poniższym przykładzie Rozpoczniemy z istniejącą siecią wirtualną o nazwie ExistingRG sieci wirtualnej, w **ExistingRG** grupy zasobów. Podsieć nosi nazwę domyślny. Te zasoby domyślne są tworzone podczas tworzenia standardowych maszyny wirtualnej (VM) za pomocą portalu Azure. Można utworzyć sieci wirtualnej i podsieci bez tworzenia maszyny Wirtualnej, ale dodawania klastra do istniejącej sieci wirtualnej głównym celem jest zapewnienie łączności sieciowej do innych maszyn wirtualnych. Tworzenie maszyny Wirtualnej zapewnia dobrym przykładem sposobu istniejącej sieci wirtualnej zwykle jest używana. Jeśli klaster sieci szkieletowej usług używa tylko do wewnętrznego modułu równoważenia obciążenia, bez publicznego adresu IP, możesz użyć maszyny Wirtualnej i jej publicznego adresu IP jako bezpieczny *skoku pole*.

### <a name="static-public-ip-address"></a>Statycznego publicznego adresu IP

Statycznego publicznego adresu IP jest zazwyczaj zasobów dedykowanych, zarządzanym oddzielnie z maszyny Wirtualnej lub maszyny wirtualne, który jest przypisany do. Jego obsługa została zainicjowana w dedykowanej grupy zasobów sieciowych (a nie w zasobie klastra usługi sieć szkieletowa grupy sam). Utwórz statycznego publicznego adresu IP o nazwie staticIP1 w tej samej grupie zasobów ExistingRG, w portalu Azure lub za pomocą programu PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

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

### <a name="service-fabric-template"></a>Szablon usługi sieć szkieletowa

W przykładach w niniejszym artykule używamy template.json sieci szkieletowej usług. Standardowy Kreator portalu służy do pobrania szablonu z portalu, przed utworzeniem klastra. Można również użyć jednej z [przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates), takiej jak [bezpiecznego klastra sieci szkieletowej usług pięcioma węzłami](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Istniejącej sieci wirtualnej lub podsieci

1. Zmień parametr podsieci nazwę istniejącą podsieć, a następnie dodaj dwa nowe parametry, aby odwołać istniejącej sieci wirtualnej:

    ```
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


2. Zmień `vnetID` zmiennej, aby wskazywała istniejącą siecią wirtualną:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Usuń `Microsoft.Network/virtualNetworks` z Twoich zasobów, dlatego Azure nie Utwórz nową sieć wirtualną:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
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

4. Komentarz sieci wirtualnej z `dependsOn` atrybutu `Microsoft.Compute/virtualMachineScaleSets`, więc nie zależą od tworzenia nowej sieci wirtualnej:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Wdrażanie szablonu:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Po wdrożeniu sieci wirtualnej powinna zawierać nowego zestawu skalowania maszyn wirtualnych. Typ węzła zestawu skali maszyny wirtualnej powinny być widoczne istniejącej sieci wirtualnej i podsieci. Na polecenie ping nowej skali ustawić maszyn wirtualnych i maszynę Wirtualną, która została już w sieci wirtualnej dostępu do można zostać używać protokołu RDP (Remote Desktop):

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Na przykład innego, zobacz [, który nie jest specyficzne dla platformy Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statycznego publicznego adresu IP

1. Dodawanie parametrów nazwę istniejącej grupy zasobów statycznego adresu IP, nazwa i Pełna nazwa domeny (FQDN):

    ```
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

2. Usuń `dnsName` parametru. (Statyczny adres IP już istnieje.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Dodawanie zmiennej, aby odwołać istniejących statyczny adres IP:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Usuń `Microsoft.Network/publicIPAddresses` z Twoich zasobów, dlatego Azure nie Utwórz nowy adres IP:

    ```
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

5. Komentarz adresu IP z `dependsOn` atrybutu `Microsoft.Network/loadBalancers`, więc nie zależą od tworzenia nowego adresu IP:

    ```
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

6. W `Microsoft.Network/loadBalancers` zasobów, zmień `publicIPAddress` elementu `frontendIPConfigurations` istniejących statyczny adres IP zamiast nowo utworzone odwołanie do:

    ```
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

7. W `Microsoft.ServiceFabric/clusters` zasobów, zmień `managementEndpoint` do nazwy FQDN DNS statycznego adresu IP. Jeśli używane są bezpieczne klastra, upewnij się, możesz zmienić *http://* do *https://*. (Należy pamiętać, że ten krok ma zastosowanie tylko do klastrów sieci szkieletowej usług. Jeśli korzystasz z zestawu skalowania maszyn wirtualnych, Pomiń ten krok.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Wdrażanie szablonu:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Po wdrożeniu widać, że moduł równoważenia obciążenia jest powiązany z publiczny statyczny adres IP z innej grupy zasobów. Punktu końcowego połączenia klienta usługi Service Fabric i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) punkt końcowy nazwy FQDN DNS statyczny adres IP.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Moduł równoważenia obciążenia tylko do wewnętrznego

W tym scenariuszu zastępuje zewnętrznej usługi równoważenia obciążenia w sieci szkieletowej usług domyślnego szablonu usługi równoważenia obciążenia wewnętrznych. Konsekwencje dla portalu Azure i dostawcy zasobów usługi Service Fabric można zobaczyć w poprzedniej sekcji.

1. Usuń `dnsName` parametru. (Nie jest wymagana.)

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Opcjonalnie Jeśli używasz statyczną metodą przydziału, można dodać parametr statyczny adres IP. Jeśli używasz metody przydzielania, nie trzeba wykonać ten krok.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Usuń `Microsoft.Network/publicIPAddresses` z Twoich zasobów, dlatego Azure nie Utwórz nowy adres IP:

    ```
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

4. Usuń adres IP `dependsOn` atrybutu `Microsoft.Network/loadBalancers`, więc nie zależą od tworzenia nowego adresu IP. Dodawanie sieci wirtualnej `dependsOn` atrybutu, ponieważ zależy od podsieci z sieci wirtualnej usługi równoważenia obciążenia:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Zmiana modułu równoważenia obciążenia `frontendIPConfigurations` z przy użyciu `publicIPAddress`, przy użyciu podsieci i `privateIPAddress`. `privateIPAddress` używa wstępnie zdefiniowanych statyczne wewnętrzne adresy IP. Aby korzystać z dynamicznego adresu IP, należy usunąć `privateIPAddress` elementu, a następnie zmień `privateIPAllocationMethod` do **dynamiczne**.

    ```
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

6. W `Microsoft.ServiceFabric/clusters` zasobów, zmień `managementEndpoint` aby wskazywał adres usługi równoważenia obciążenia wewnętrznego. Jeśli używasz bezpiecznego klastra, upewnij się, można zmienić *http://* do *https://*. (Należy pamiętać, że ten krok ma zastosowanie tylko do klastrów sieci szkieletowej usług. Jeśli korzystasz z zestawu skalowania maszyn wirtualnych, Pomiń ten krok.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Wdrażanie szablonu:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Po wdrożeniu przez moduł równoważenia obciążenia używa prywatnego 10.0.0.250 statyczny adres IP. Jeśli masz inną maszynę w tej samej sieci wirtualnej, można przejść do wewnętrznej [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) punktu końcowego. Należy pamiętać, że łączy się jednym z węzłów za usługą równoważenia obciążenia.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Moduł równoważenia obciążenia wewnętrznych i zewnętrznych

W tym scenariuszu można rozpoczynać się od istniejącego typu jednowęzłowej zewnętrznej usługi równoważenia obciążenia i Dodaj wewnętrznego modułu równoważenia obciążenia dla tego samego typu węzła. Tylko do modułu równoważenia obciążenia w jednej można przypisać portu zaplecza, połączony z pulą adresów zaplecza. Wybierz, które usługa równoważenia obciążenia będzie miał porty Twojej aplikacji i które usługa równoważenia obciążenia mają zarządzania punktów końcowych (porty 19000 i 19080). Jeśli punkty końcowe zarządzania zostanie umieszczony na wewnętrzny moduł równoważenia obciążenia, należy pamiętać zasobów sieci szkieletowej usług ograniczenia dostawcy opisem we wcześniejszej części tego artykułu. W tym przykładzie używamy, zarządzania, punktów końcowych zachowana na zewnętrznej usługi równoważenia obciążenia. Możesz również dodać port aplikacji portu 80 i umieść ją na wewnętrzny moduł równoważenia obciążenia.

W klastrze typu węzła dwa jest jednego typu węzła w zewnętrznej usługi równoważenia obciążenia. Typ węzła znajduje się na wewnętrzny moduł równoważenia obciążenia. Do używania klastrze dwóch węzłów typu w szablonie typu węzła dwa utworzonych przez portal (który pochodzi z modułami równoważenia obciążenia dwa), Przełącz drugi modułu równoważenia obciążenia do wewnętrznego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [modułu równoważenia obciążenia tylko do wewnętrznego](#internallb) sekcji.

1. Dodaj parametr adres IP usługi równoważenia statyczna obciążenia wewnętrznego. (Aby uzyskać informacje powiązane z użyciem dynamicznego adresu IP, zobacz wcześniejszej części tego artykułu).

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Dodaj parametr port 80 aplikacji.

3. Dodaj wewnętrzne wersji istniejącej sieci zmiennych, skopiuj i wklej je, a następnie dodaj "-Int" do nazwy:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Po uruchomieniu z szablonem generowanych przez portal, który korzysta z aplikacji portu 80, domyślny szablon portalu dodaje AppPort1 (port 80) w zewnętrznej usługi równoważenia obciążenia. W takim przypadku usuń AppPort1 z zewnętrznej usługi równoważenia obciążenia `loadBalancingRules` i sond, dzięki czemu można dodać go do wewnętrznego modułu równoważenia obciążenia:

    ```
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

5. Dodaj drugi `Microsoft.Network/loadBalancers` zasobów. Podobny do wewnętrznego modułu równoważenia obciążenia utworzony w [modułu równoważenia obciążenia tylko do wewnętrznego](#internallb) sekcji, ale używa "-Int" zmienne usługi równoważenia obciążenia i implementuje tylko aplikacji portu 80. Spowoduje to również usunięcie `inboundNatPools`, aby zachować punktów końcowych protokołu RDP na publiczny moduł równoważenia obciążenia. Protokół RDP na wewnętrzny moduł równoważenia obciążenia, przenieść `inboundNatPools` z zewnętrznego modułu równoważenia obciążenia wewnętrznego usługi równoważenia obciążenia:

    ```
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

6. W `networkProfile` dla `Microsoft.Compute/virtualMachineScaleSets` zasobów, dodać puli adresów zaplecza wewnętrznego:

    ```
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
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Po wdrożeniu widać dwie usługi równoważenia obciążenia w grupie zasobów. Po przejściu do usługi równoważenia obciążenia widać publicznego adresu IP adres i zarządzania punktów końcowych (porty 19000 i 19080) przypisany do publicznego adresu IP. Możesz również sprawdzić statyczne wewnętrzne IP adres i aplikacji punktu końcowego (port 80) przypisany do wewnętrznego modułu równoważenia obciążenia. Obie usługi równoważenia obciążenia, użyj tej samej puli zaplecza zestaw skali maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie klastra](service-fabric-cluster-creation-via-arm.md)
