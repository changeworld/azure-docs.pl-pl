---
title: 'Tworzenie bramy sieci VPN platformy Azure opartej na trasach: INTERFEJS WIERSZA POLECENIA | Dokumentacja firmy Microsoft'
description: Szybko Dowiedz się, jak utworzyć bramę sieci VPN przy użyciu interfejsu wiersza polecenia
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: f5f62a6bfa1baa205e0496dd901f1f1eef660079
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391246"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Tworzenie bramy sieci VPN opartej na trasach przy użyciu interfejsu wiersza polecenia

Ten artykuł ułatwia szybkie tworzenie bramy sieci VPN platformy Azure opartej na trasach przy użyciu wiersza polecenia platformy Azure. Tworzenie bramy sieci VPN jest używany podczas tworzenia połączenia sieci VPN z siecią lokalną. Łączenie sieci wirtualnych umożliwia także tworzenie bramy sieci VPN.

Kroki opisane w tym artykule spowoduje utworzenie sieci wirtualnej, podsieci, podsieć bramy i bramy sieci VPN opartej na trasach (Brama sieci wirtualnej). Brama sieci wirtualnej może potrwać 45 minut lub więcej, aby utworzyć. Po zakończeniu tworzenia bramy będzie można utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group) polecenia. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 


```azurecli-interactive 
az group create --name TestRG1 --location eastus
```

## <a name="vnet"></a>Tworzenie sieci wirtualnej

Tworzenie sieci wirtualnej przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet) polecenia. Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w **EastUS** lokalizacji:

```azurecli-interactive 
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="gwsubnet"></a>Dodawanie podsieci bramy

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać podsieć bramy można użyć następujących przykładów:

```azurepowershell-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="PublicIP"></a>Żądanie publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzielanego publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN, które tworzysz dla sieci wirtualnej. Żądanie publicznego adresu IP, skorzystaj z następującego przykładu:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN za pomocą polecenia [az network vnet-gateway create](/cli/azure/group).

Po uruchomieniu tego polecenia przy użyciu `--no-wait` parametru nie widać żadnych informacji zwrotnych ani danych wyjściowych. `--no-wait` Parametr umożliwia bramy do utworzenia w tle. Nie oznacza to, że Brama VPN jest utworzona natychmiast.

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

Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej.

## <a name="viewgw"></a>Wyświetl bramy sieci VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpowiedź wygląda podobnie do poniższego:

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

### <a name="view-the-public-ip-address"></a>Wyświetlanie publicznego adresu IP

Aby wyświetlić publiczny adres IP przypisany do bramy, skorzystaj z następującego przykładu:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Wartość skojarzona z **ipAddress** pole jest publiczny adres IP bramy sieci VPN.

Przykładowa odpowiedź:

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

Jeśli nie potrzebujesz już zasobów utworzonych, użyj [usunięcie grupy az](/cli/azure/group) Aby usunąć grupę zasobów. Spowoduje to usunięcie grupy zasobów i wszystkich znajdujących się w niej zasobów.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu bramy podczas tworzenia, można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Lub Utwórz połączenie między siecią wirtualną a lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Utwórz połączenie lokacja lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Utwórz połączenie punkt lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Utwórz połączenie z inną siecią wirtualną](vpn-gateway-vnet-vnet-rm-ps.md)
