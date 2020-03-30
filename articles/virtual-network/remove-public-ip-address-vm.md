---
title: Odłączanie publicznego adresu IP od maszyny Wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak odłączyć publiczny adres IP od maszyny Wirtualnej
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900738"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Odłączanie publicznego adresu IP od maszyny Wirtualnej platformy Azure 

W tym artykule dowiesz się, jak odłączyć publiczny adres IP od maszyny wirtualnej platformy Azure (VM).

Za pomocą [witryny Azure portal](#azure-portal), [interfejsu wiersza polecenia](#azure-cli) platformy Azure (CLI) lub programu [PowerShell](#powershell) można odłączyć publiczny adres IP od maszyny Wirtualnej.

## <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do lub wyszukaj maszynę wirtualną, z której chcesz odłączyć publiczny adres IP, a następnie wybierz go.
3. Na stronie Maszyny Wirtualnej wybierz **pozycję Przegląd**, wybierz publiczny adres IP, jak pokazano na poniższym rysunku:

   ![Wybierz publiczny adres IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na stronie publicznego adresu IP wybierz **pozycję Przegląd**, a następnie wybierz pozycję **Odłączenie**, jak pokazano na poniższym rysunku:

    ![Odłączyć publiczny adres IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. W **obszarze Odłącz publiczny adres IP**wybierz pozycję **Tak**.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj [narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach interfejsu wiersza polecenia. Wybranie **opcji Wypróbuj wywołuje** powłokę w chmurze, za pomocą której można zalogować się do konta platformy Azure.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie `az login`w bash, zaloguj się na platformie Azure za pomocą programu .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego podłączonego do maszyny Wirtualnej. Użyj polecenia [aktualizacji sieci az nic-ip-config,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) aby odłączyć publiczny adres IP od konfiguracji IP. Poniższy przykład odcina publiczny adres IP o nazwie *myVMPublicIP* od konfiguracji IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic,* który jest dołączony do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny Wirtualnej, użyj polecenia [az vm nic list,](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) aby je wyświetlić. Na przykład następujące polecenie zawiera listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe obejmują jeden lub więcej wierszy, które są podobne do następującego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *myVMVMNic* jest nazwą interfejsu sieciowego.

   - Jeśli nie znasz nazwy konfiguracji IP dla interfejsu sieciowego, użyj polecenia [az network nic ip-config list,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) aby je pobrać. Na przykład następujące polecenie zawiera listę nazw publicznych konfiguracji IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Jeśli nie znasz nazwy publicznej konfiguracji IP dla interfejsu sieciowego, użyj polecenia [az network nic ip-config show,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) aby je pobrać. Na przykład następujące polecenie zawiera listę nazw publicznych konfiguracji IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Zainstaluj [program PowerShell](/powershell/azure/install-az-ps)lub użyj usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma preinstalowany program PowerShell i skonfigurowany do używania z kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach programu PowerShell. Wybranie **opcji Wypróbuj wywołuje** powłokę w chmurze, za pomocą której można zalogować się do konta platformy Azure.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się na platformie Azure za pomocą programu `Connect-AzAccount`.
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego podłączonego do maszyny Wirtualnej. Użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby uzyskać interfejs sieciowy. Ustaw wartość publicznego adresu IP na wartość null, a następnie użyj polecenia [Set-AzNetworkInterface,](/powershell/module/Az.Network/Set-AzNetworkInterface) aby zapisać nową konfigurację IP w interfejsie sieciowym.

   Poniższy przykład odcina publiczny adres IP o nazwie *myVMPublicIP* od interfejsu sieciowego o nazwie *myVMVMNic,* który jest dołączony do maszyny wirtualnej o nazwie *myVM*. Wszystkie zasoby należą do grupy zasobów o nazwie *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Jeśli nie znasz nazwy interfejsu sieciowego podłączonego do maszyny Wirtualnej, użyj polecenia [Get-AZVM,](/powershell/module/Az.Compute/Get-AzVM) aby je wyświetlić. Na przykład następujące polecenie zawiera listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *myVMVMNic* jest nazwą interfejsu sieciowego.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy konfiguracji IP dla interfejsu sieciowego, użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby je pobrać. Na przykład następujące polecenie zawiera listę nazw konfiguracji IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *ipconfigmyVM* jest nazwą konfiguracji IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skojarzyć publiczny adres IP z maszyną wirtualną](associate-public-ip-address-vm.md).
