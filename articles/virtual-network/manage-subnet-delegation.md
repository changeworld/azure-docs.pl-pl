---
title: Dodawanie lub usuwanie delegowania podsieci w sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak dodać lub usunąć delegowaną podsieć usługi na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201870"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Dodawanie lub usuwanie delegowania podsieci

Delegowanie podsieci daje jawne uprawnienia do usługi do tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. W tym artykule opisano sposób dodawania lub usuwania delegowanej podsieci dla usługi platformy Azure.

## <a name="portal"></a>Portal

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć, które później będziesz delegować do usługi platformy Azure.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć wirtualna sieci** > **Virtual network**.
1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wpisz *10.0.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz **EastUS**.|
    | Podsieć — nazwa | Wpisz *mySubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wpisz *10.0.0.0/24*. |
    |||
1. Pozostaw resztę jako domyślną, a następnie wybierz pozycję **Utwórz**.

### <a name="permissions"></a>Uprawnienia

Jeśli podsieć nie została utworzona, którą chcesz delegować do usługi `Microsoft.Network/virtualNetworks/subnets/write`platformy Azure, potrzebne jest następujące uprawnienie: .

Wbudowana rola [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) zawiera również niezbędne uprawnienia.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegowanie podsieci do usługi platformy Azure

W tej sekcji można delegować podsieci, który został utworzony w poprzedniej sekcji do usługi platformy Azure.

1. Na pasku wyszukiwania portalu wpisz *myVirtualNetwork*. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.
2. W wynikach wyszukiwania wybierz *myVirtualNetwork*.
3. Wybierz **podsieci**, w obszarze **USTAWIENIA**, a następnie wybierz **mySubnet**.
4. Na stronie *mySubnet* dla listy **delegowania podsieci** wybierz z usług wymienionych w obszarze **Delegowanie podsieci do usługi** (na przykład **Microsoft.DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Usuwanie delegowania podsieci z usługi platformy Azure

1. Na pasku wyszukiwania portalu wpisz *myVirtualNetwork*. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.
2. W wynikach wyszukiwania wybierz *myVirtualNetwork*.
3. Wybierz **podsieci**, w obszarze **USTAWIENIA**, a następnie wybierz **mySubnet**.
4. Na stronie *mySubnet* dla listy **Delegowanie podsieci** wybierz **pozycję Brak** z usług wymienionych w obszarze **Delegowanie podsieci do usługi**. 

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie zamiast tego, ten artykuł wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie **myResourceGroup** w lokalizacji **eastus:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie **mySubnet** w grupie zasobów **myResourceGroup** przy użyciu polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Uprawnienia

Jeśli podsieć nie została utworzona, którą chcesz delegować do usługi `Microsoft.Network/virtualNetworks/subnets/write`platformy Azure, potrzebne jest następujące uprawnienie: .

Wbudowana rola [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) zawiera również niezbędne uprawnienia.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegowanie podsieci do usługi platformy Azure

W tej sekcji można delegować podsieci, który został utworzony w poprzedniej sekcji do usługi platformy Azure. 

Użyj [aktualizacji podsieci sieci AZ,](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) aby zaktualizować podsieć o nazwie **mySubnet** delegowaniem do usługi platformy Azure.  W tym przykładzie **Microsoft.DBforPostgreSQL/serversv2** jest używany dla przykładowego delegowania:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Aby sprawdzić, czy delegowanie zostało zastosowane, użyj [programu AZ Network vnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Sprawdź, czy usługa jest delegowana do podsieci w ramach **serviceName**właściwości:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Usuwanie delegowania podsieci z usługi platformy Azure

Użyj [aktualizacji podsieci sieci AZ,](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) aby usunąć delegowanie z podsieci o nazwie **mySubnet:**

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Aby sprawdzić, czy delegowanie zostało usunięte, użyj [programu AZ Network vnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Sprawdź, czy usługa zostanie usunięta z podsieci pod **serviceName**właściwości:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Wyjście z polecenia jest nawiasem zerowym:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną o nazwie **myVnet** z podsiecią o nazwie **mySubnet** przy użyciu [New-AzVirtualNetWorkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) w **myResourceGroup** przy użyciu [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Przestrzeń adresowa IP dla sieci wirtualnej to **10.0.0.0/16**. Podsieć w sieci wirtualnej jest **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Uprawnienia

Jeśli podsieć nie została utworzona, którą chcesz delegować do usługi `Microsoft.Network/virtualNetworks/subnets/write`platformy Azure, potrzebne jest następujące uprawnienie: .

Wbudowana rola [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) zawiera również niezbędne uprawnienia.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegowanie podsieci do usługi platformy Azure

W tej sekcji można delegować podsieci, który został utworzony w poprzedniej sekcji do usługi platformy Azure. 

Użyj [Add-AzDelegation,](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) aby zaktualizować podsieć o nazwie **mySubnet** z delegowania o nazwie **myDelegation** do usługi Platformy Azure.  W tym przykładzie **Microsoft.DBforPostgreSQL/serversv2** jest używany dla przykładowego delegowania:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Użyj [Get-AzDelegation,](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) aby zweryfikować delegowanie:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Usuwanie delegowania podsieci z usługi platformy Azure

[Usuń-AzDelegation,](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) aby usunąć delegowanie z podsieci o nazwie **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Użyj [Get-AzDelegation,](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) aby sprawdzić, czy delegowanie zostało usunięte:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [zarządzać podsieciami na platformie Azure](virtual-network-manage-subnet.md).
