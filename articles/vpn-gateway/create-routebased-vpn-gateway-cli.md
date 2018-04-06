---
title: 'Utwórz bramę sieci VPN platformy Azure opartej na trasach: interfejs wiersza polecenia | Dokumentacja firmy Microsoft'
description: Szybko dowiedzieć się, jak utworzyć bramę sieci VPN przy użyciu interfejsu wiersza polecenia
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: d0f4e292c6f5a2725b4a9efe91e78c6e634ea64e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Utwórz bramę sieci VPN opartej na trasach przy użyciu interfejsu wiersza polecenia

Ten artykuł pomaga szybko utworzyć bramę sieci VPN platformy Azure opartej na trasach przy użyciu wiersza polecenia platformy Azure. Brama sieci VPN jest używany podczas tworzenia połączenia sieci VPN do sieci lokalnej. Aby połączyć sieci wirtualnych umożliwia także bramy sieci VPN.

Kroki opisane w tym artykule spowoduje utworzenie sieci wirtualnej, podsieci, podsieć bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Brama sieci wirtualnej może trwać 45 minut lub dłużej, aby utworzyć. Po zakończeniu tworzenia bramy następnie można utworzyć połączenia. Kroki te wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli do zainstalowania i używania interfejsu wiersza polecenia lokalnie, w tym artykule, wymaga używasz interfejsu wiersza polecenia Azure w wersji 2.0.4 lub nowszej. Aby znaleźć zainstalowanej wersji, uruchom `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tworzenie grupy zasobów przy użyciu [Tworzenie grupy az](/cli/azure/group#az_group_create) polecenia. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Tworzenie sieci wirtualnej

Tworzenie sieci wirtualnej przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create) polecenia. Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w **EastUS** lokalizacji:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Dodaj podsieć bramy

Podsieć bramy zawiera zastrzeżone adresy IP, które używają usługi bramy sieci wirtualnej. Aby dodać podsieci bramy, należy użyć poniższych przykładach:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Żądaj publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzielonego publicznego adresu IP. Publiczny adres IP zostaną przydzielone do bramy sieci VPN, utworzony dla sieci wirtualnej. Skorzystaj z następującego przykładu, aby zażądać publiczny adres IP:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN za pomocą polecenia [az network vnet-gateway create](/cli/azure/group#az_network_vnet_gateway_create).

Po uruchomieniu tego polecenia przy użyciu `--no-wait` parametru nie widać żadnych opinie i danych wyjściowych. `--no-wait` Parametr umożliwia bramy do utworzenia w tle. Nie oznacza to od razu utworzyć bramę sieci VPN.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Brama sieci VPN może zająć 45 minut lub dłużej, aby utworzyć.

## <a name="viewgw"></a>Widok bramy sieci VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpowiedź wygląda podobnie do następującej:

```
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Widok publiczny adres IP

Aby wyświetlić publiczny adres IP przypisany do bramy, skorzystaj z następującego przykładu:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Wartość skojarzoną z **ipAddress** pole jest publiczny adres IP bramy sieci VPN.

Przykład odpowiedzi:

```
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne zasoby utworzone, użyj [usunięcie grupy az](/cli/azure/group#az_group_delete) można usunąć grupy zasobów. Spowoduje to usunięcie grupy zasobów i wszystkie zasoby, które zawiera.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu bramy podczas tworzenia, można utworzyć połączenie między sieci wirtualnej i innej sieci wirtualnej. Lub Utwórz połączenie między sieci wirtualnej i lokalizacji lokalnej.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Utwórz połączenie punkt lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Utwórz połączenie do innej sieci wirtualnej](vpn-gateway-vnet-vnet-rm-ps.md)