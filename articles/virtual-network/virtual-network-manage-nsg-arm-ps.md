---
title: "Zarządzanie grupami zabezpieczeń sieci - programu Azure PowerShell | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak zarządzać grupami zabezpieczeń sieci przy użyciu programu PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca7f4926ca4edf9d20612aca74f6ae5f0ed847b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-groups-using-powershell"></a>Zarządzanie grupami zabezpieczeń sieci przy użyciu programu PowerShell

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania Menedżera zasobów, które firma Microsoft zaleca w przypadku większości nowych wdrożeń zamiast klasycznym modelu wdrażania.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Pobieranie informacji
Można wyświetlić istniejących grup NSG, pobrać reguł dla istniejącej grupy NSG i dowiedzieć się, jakie zasoby grupy NSG jest skojarzona z.

### <a name="view-existing-nsgs"></a>Wyświetlanie istniejących grup NSG
Aby wyświetlić wszystkich istniejących grup NSG w ramach subskrypcji, uruchom `Get-AzureRmNetworkSecurityGroup` polecenia cmdlet.

Oczekiwany wynik:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Aby wyświetlić listę grup NSG w określonej grupy zasobów, uruchom `Get-AzureRmNetworkSecurityGroup` polecenia cmdlet.

Oczekiwane dane wyjściowe:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>Wyświetl listę wszystkich reguł dla grupy NSG
Aby wyświetlić reguły NSG o nazwie **frontonu NSG**, wprowadź następujące polecenie:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

Oczekiwane dane wyjściowe:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> Można również użyć `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` Aby wyświetlić listę domyślnych reguł z **frontonu NSG** NSG.
> 

### <a name="view-nsgs-associations"></a>Wyświetlanie grup NSG powiązań
Aby wyświetlić zasobów **frontonu NSG** grupa NSG jest skojarzenia, uruchom następujące polecenie:

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

Wyszukaj **Networkinterface** i **podsieci** właściwości, jak pokazano poniżej:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

W poprzednim przykładzie grupa NSG nie jest skojarzony z żadnych interfejsów sieciowych (NIC); jest on skojarzony z podsiecią o nazwie **frontonu**.

## <a name="manage-rules"></a>Zarządzaj regułami
Można dodać reguły do istniejącej grupy NSG, edytować istniejące zasady i Usuń reguły.

### <a name="add-a-rule"></a>Dodawanie reguły
Można dodać, dzięki czemu reguły **przychodzących** ruch do portu **443** z dowolnej maszyny do **frontonu NSG** NSG, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać istniejącej grupy NSG i zapisze go w zmiennej:

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Uruchom następujące polecenie, aby dodać regułę do grupy NSG:

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Aby zapisać zmiany wprowadzone w grupie NSG, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    Oczekiwane dane wyjściowe, pokazujący tylko reguły zabezpieczeń:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Zmień reguły
Aby zmienić reguły utworzone powyżej, aby zezwalać na ruch przychodzący z **Internet** , wykonaj poniższe kroki.

1. Uruchom następujące polecenie, aby pobrać istniejącej grupy NSG i zapisze go w zmiennej:

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Uruchom następujące polecenie, przy użyciu nowych ustawień reguły:

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. Aby zapisać zmiany wprowadzone w grupie NSG, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Oczekiwane dane wyjściowe, pokazujący tylko reguły zabezpieczeń:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Usuwanie reguły
1. Uruchom następujące polecenie, aby pobrać istniejącej grupy NSG i zapisze go w zmiennej:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Uruchom następujące polecenie, aby usunąć regułę z grupy NSG:

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. Zapisz zmiany wprowadzone w grupie NSG, uruchamiając następujące polecenie:

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    Oczekiwano danych wyjściowych wyświetlane tylko zasady zabezpieczeń, a powiadomienie **reguła https** nie jest już wyświetlane:
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Zarządzanie skojarzenia
Możesz skojarzyć grupy NSG do podsieci i karty sieciowe. Można również usunąć skojarzenie grupy NSG z dowolnego zasobu, który jest ona skojarzona.

### <a name="associate-an-nsg-to-a-nic"></a>Kojarzenie grupy NSG z kartą sieciową
Aby skojarzyć **frontonu NSG** grupy NSG **TestNICWeb1** karty Sieciowej, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać istniejącej grupy NSG i zapisze go w zmiennej:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. Uruchom następujące polecenie, aby pobrać istniejącej karty NIC i zapisze go w zmiennej:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. Ustaw **grupy NetworkSecurityGroup** właściwość **kart** zmiennej wartość **NSG** zmiennej, wprowadzając następujące polecenie:

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. Aby zapisać zmiany wprowadzone do karty Sieciowej, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Oczekiwane dane wyjściowe są wyświetlane tylko **grupy NetworkSecurityGroup** właściwości:
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Usuń skojarzenie grupy NSG z karty Sieciowej
Aby usunąć skojarzenie **frontonu NSG** grupy NSG z **TestNICWeb1** karty Sieciowej, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać istniejącej karty NIC i zapisze go w zmiennej:

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. Ustaw **grupy NetworkSecurityGroup** właściwość **kart** zmienną **$null** , uruchamiając następujące polecenie:

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. Aby zapisać zmiany wprowadzone do karty Sieciowej, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    Oczekiwane dane wyjściowe są wyświetlane tylko **grupy NetworkSecurityGroup** właściwości:
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Usuń skojarzenie grupy NSG z podsiecią
Aby usunąć skojarzenie **frontonu NSG** grupy NSG z **frontonu** podsieci, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać istniejącej sieci wirtualnej i zapisze go w zmiennej:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Uruchom następujące polecenie, aby pobrać **frontonu** podsieci i zapisze go w zmiennej:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Ustaw **grupy NetworkSecurityGroup** właściwość **podsieci** zmienną **$null** , wprowadzając następujące polecenie:

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. Aby zapisać zmiany wprowadzone do podsieci, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Oczekiwano danych wyjściowych, pokazujący tylko właściwości elementu **frontonu** podsieci. Powiadomienie nie ma właściwości **grupy NetworkSecurityGroup**:
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Kojarzenie grupy NSG z podsiecią
Aby skojarzyć **frontonu NSG** grupy NSG **FronEnd** podsieci, wykonaj następujące czynności:

1. Uruchom następujące polecenie, aby pobrać istniejącej sieci wirtualnej i zapisze go w zmiennej:

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. Uruchom następujące polecenie, aby pobrać **frontonu** podsieci i zapisze go w zmiennej:

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. Uruchom następujące polecenie, aby pobrać istniejącej grupy NSG i zapisze go w zmiennej:

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. Ustaw **grupy NetworkSecurityGroup** właściwość **podsieci** zmienną **$null** , uruchamiając następujące polecenie:

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. Aby zapisać zmiany wprowadzone do podsieci, uruchom następujące polecenie:

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    Oczekiwane dane wyjściowe są wyświetlane tylko **grupy NetworkSecurityGroup** właściwość **frontonu** podsieci:
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Usuwanie grupy NSG
Grupy NSG można usuwać tylko, jeśli nie został skojarzony z żadnym zasobem. Aby usunąć grupy NSG, wykonaj poniższe kroki.

1. Aby sprawdzić zasoby skojarzone grupy NSG, uruchom `azure network nsg show` pokazane [skojarzenia grup NSG widoku](#View-NSGs-associations).
2. Jeśli grupa NSG jest skojarzona z dowolnej karty interfejsu sieciowego, uruchom `azure network nic set` pokazane [skojarzenie grupy NSG z karty Sieciowej](#Dissociate-an-NSG-from-a-NIC) dla poszczególnych kart sieciowych. 
3. Jeśli grupa NSG jest skojarzona z dowolnej podsieci, uruchom `azure network vnet subnet set` pokazane [skojarzenie grupy NSG z podsiecią](#Dissociate-an-NSG-from-a-subnet) dla każdej podsieci.
4. Aby usunąć grupy NSG, uruchom następujące polecenie:

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > `-Force` Parametru zapewnia nie trzeba potwierdzić usunięcie.
   > 

## <a name="next-steps"></a>Następne kroki
* [Włącz rejestrowanie](virtual-network-nsg-manage-log.md) dla grup NSG.

