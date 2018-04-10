---
title: "Zarządzanie grupami zabezpieczeń sieci - wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft"
description: "Dowiedz się, jak zarządzać grupami zabezpieczeń sieci przy użyciu interfejsu wiersza polecenia platformy Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee43bfd85bdda16cb496ec938c266c72f0d5688b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
---
# <a name="manage-network-security-groups-using-the-azure-cli"></a>Zarządzanie grupami zabezpieczeń sieci przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu modelu wdrażania Menedżera zasobów, które firma Microsoft zaleca w przypadku większości nowych wdrożeń zamiast klasycznym modelu wdrażania.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Wymagania wstępne
Jeśli nie zostało jeszcze, instalowania i konfigurowania najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login). 

## <a name="view-existing-nsgs"></a>Wyświetlanie istniejących grup NSG
Aby wyświetlić listę grup NSG w określonej grupy zasobów, uruchom [lista nsg sieci az](/cli/azure/network/nsg#az_network_nsg_list) z `-o table` format wyjściowy:

```azurecli
az network nsg list -g RG-NSG -o table
```

Oczekiwane dane wyjściowe:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Wyświetl listę wszystkich reguł dla grupy NSG
Aby wyświetlić reguły NSG o nazwie **frontonu NSG**Uruchom [Pokaż nsg sieci az](/cli/azure/network/nsg#az_network_nsg_show) polecenie, używając [Filtr kwerendy JMESPATH](/cli/azure/query-az-cli2) i `-o table` format wyjściowy:

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Oczekiwane dane wyjściowe:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> Można również użyć [listy reguł nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) Aby wyświetlić listę reguł niestandardowych z grupy NSG.
>

## <a name="view-nsg-associations"></a>Wyświetlanie NSG powiązań

Aby wyświetlić zasobów **frontonu NSG** grupa NSG jest skojarzony z uruchomiony `az network nsg show` polecenia: 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Wyszukaj **Networkinterface** i **podsieci** właściwości, jak pokazano w poniższym przykładzie danych wyjściowych:

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

W poprzednim przykładzie, grupa NSG nie jest skojarzony z żadnych interfejsów sieciowych (NIC) i jest on skojarzony z podsiecią o nazwie **frontonu**.

## <a name="add-a-rule"></a>Dodawanie reguły
Można dodać, dzięki czemu reguły **przychodzących** ruch do portu **443** z dowolnej maszyny do **frontonu NSG** NSG, wprowadź następujące polecenie:

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Oczekiwane dane wyjściowe:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Zmień reguły
Aby zmienić reguły utworzone wcześniej, aby zezwalać na ruch przychodzący z **Internet** , uruchom [aktualizacja reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) polecenia:

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Oczekiwane dane wyjściowe:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Usuwanie reguły
Aby usunąć regułę utworzone powyżej, uruchom następujące polecenie:

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Kojarzenie grupy NSG z kartą sieciową
Aby skojarzyć **frontonu NSG** grupy NSG **TestNICWeb1** karty Sieciowej, użyj [aktualizacji kart sieciowych az](/cli/azure/network/nic#az_network_nic_update) polecenia:

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Oczekiwane dane wyjściowe:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Usuń skojarzenie grupy NSG z karty Sieciowej

Można usunąć skojarzenia **frontonu NSG** grupy NSG z **TestNICWeb1** karty Sieciowej, uruchom [aktualizacja reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) polecenia ponownie zostać zastąpiony `--network-security-group` argumentu z pusty ciąg (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

W danych wyjściowych `networkSecurityGroup` jest ustawiona na wartość null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Usuń skojarzenie grupy NSG z podsiecią
Można usunąć skojarzenia **frontonu NSG** grupy NSG z **frontonu** podsieci, ponownie uruchom [aktualizacja reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) polecenia ponownie zostać zastąpiony `--network-security-group` argumentu za pomocą ciągu pustego (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

W danych wyjściowych `networkSecurityGroup` jest ustawiona na wartość null.

## <a name="associate-an-nsg-to-a-subnet"></a>Kojarzenie grupy NSG z podsiecią
Aby skojarzyć **frontonu NSG** grupy NSG **frontonu** podsieci ponownie, uruchom następujące polecenie:

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

W danych wyjściowych `networkSecurityGroup` klucz ma podobny dla wartości:

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Usuwanie grupy NSG
Grupy NSG można usuwać tylko, jeśli nie został skojarzony z żadnym zasobem. Aby usunąć grupy NSG, wykonaj następujące kroki:

1. Aby sprawdzić zasoby skojarzone grupy NSG, uruchom `azure network nsg show` pokazane [skojarzenia grup NSG widoku](#View-NSGs-associations).
2. Jeśli grupa NSG jest skojarzona z dowolnej karty interfejsu sieciowego, uruchom `azure network nic set` pokazane [skojarzenie grupy NSG z karty Sieciowej](#Dissociate-an-NSG-from-a-NIC) dla poszczególnych kart sieciowych. 
3. Jeśli grupa NSG jest skojarzona z dowolnej podsieci, uruchom `azure network vnet subnet set` pokazane [skojarzenie grupy NSG z podsiecią](#Dissociate-an-NSG-from-a-subnet) dla każdej podsieci.
4. Aby usunąć grupy NSG, uruchom następujące polecenie:

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Kolejne kroki
* [Włącz rejestrowanie](virtual-network-nsg-manage-log.md) dla grup NSG.

