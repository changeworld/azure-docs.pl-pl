---
title: 'Tworzenie bramy sieci VPN opartej na marszrutach: wiersz polecenia polecenia'
description: Szybko dowiedz się, jak utworzyć bramę sieci VPN przy użyciu interfejsu wiersza polecenia
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/04/2018
ms.author: cherylmc
ms.openlocfilehash: 121790fce220874babedf67cd72471caa7e92ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241103"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Tworzenie bramy sieci VPN opartej na trasie przy użyciu interfejsu wiersza polecenia

Ten artykuł ułatwia szybkie tworzenie bramy sieci VPN opartej na marszrutach przy użyciu interfejsu wiersza polecenia platformy Azure. Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Można również użyć bramy sieci VPN do łączenia sieci wirtualnych.

Kroki opisane w tym artykule utworzy sieć wirtualną, podsieć, podsieć bramy i bramę sieci VPN opartą na trasie (brama sieci wirtualnej). Utworzenie bramy sieci wirtualnej może potrwać co najmniej 45 minut. Po zakończeniu tworzenia bramy można następnie utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów przy użyciu polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [tworzenie sieci wirtualnej az.](/cli/azure/network/vnet) Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w lokalizacji **EastUS:**

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Dodawanie podsieci bramy

Podsieć bramy zawiera zastrzeżone adresy IP używane przez usługi bramy sieci wirtualnej. Aby dodać podsieć bramy, należy użyć następujących przykładów:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Przesłanie żądania dotyczącego publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzielony publiczny adres IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej. Użyj następującego przykładu, aby zażądać publicznego adresu IP:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN za pomocą polecenia [az network vnet-gateway create](/cli/azure/group).

Jeśli to polecenie zostanie `--no-wait` uruchomione przy użyciu parametru, nie zobaczysz żadnych opinii ani danych wyjściowych. Parametr `--no-wait` umożliwia utworzenie bramy w tle. Nie oznacza to, że brama sieci VPN jest tworzona natychmiast.

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

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Wyświetlanie bramy sieci VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpowiedź wygląda podobnie do tego:

```output
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

Aby wyświetlić publiczny adres IP przypisany do bramy, użyj następującego przykładu:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Wartość skojarzona z polem **ipAddress** jest publicznym adresem IP bramy sieci VPN.

Przykładowa odpowiedź:

```output
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

Jeśli nie potrzebujesz już utworzonych zasobów, usuń [grupę AZ,](/cli/azure/group) aby usunąć grupę zasobów. Spowoduje to usunięcie grupy zasobów i wszystkich znajdujących się w niej zasobów.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między siecią wirtualną a inną siecią wirtualną. Możesz też utworzyć połączenie między siecią wirtualną a lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Tworzenie połączenia z inną siecią wirtualną](vpn-gateway-vnet-vnet-rm-ps.md)
