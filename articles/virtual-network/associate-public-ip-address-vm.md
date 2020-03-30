---
title: Kojarzenie publicznego adresu IP z maszyną wirtualną
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skojarzyć publiczny adres IP z maszyną wirtualną.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647462"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Kojarzenie publicznego adresu IP z maszyną wirtualną

W tym artykule dowiesz się, jak skojarzyć publiczny adres IP z istniejącą maszyną wirtualną(VM). Jeśli chcesz połączyć się z maszyną wirtualną z Internetu, maszyna wirtualna musi mieć skojarzony z nią publiczny adres IP. Jeśli chcesz utworzyć nową maszynę wirtualną z publicznym adresem IP, możesz to zrobić za pomocą [witryny Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [interfejsu wiersza polecenia platformy Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)lub [programu PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Publiczne adresy IP mają symboliczną opłatę. Aby uzyskać szczegółowe informacje, zobacz [cennik](https://azure.microsoft.com/pricing/details/ip-addresses/). Istnieje ograniczenie liczby publicznych adresów IP, których można używać na subskrypcję. Aby uzyskać szczegółowe informacje, zobacz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Za pomocą [witryny Azure Portal](#azure-portal), [interfejsu wiersza polecenia](#azure-cli) platformy Azure (CLI) lub programu [PowerShell](#powershell) można skojarzyć publiczny adres IP z maszyną wirtualną.

## <a name="azure-portal"></a>Portal Azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do lub wyszukaj maszynę wirtualną, do której chcesz dodać publiczny adres IP, a następnie wybierz go.
3. W obszarze **Ustawienia**wybierz pozycję **Sieć**, a następnie wybierz interfejs sieciowy, do którego chcesz dodać publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybierz interfejs sieciowy](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z interfejsami sieciowymi dołączonymi do maszyny Wirtualnej. Na poprzednim zdjęciu maszyna wirtualna ma tylko jeden interfejs sieciowy. Jeśli maszyna wirtualna miała wiele interfejsów sieciowych, wszystkie one będą wyświetlane i należy wybrać interfejs sieciowy, z którego chcesz skojarzyć publiczny adres IP.

4. Wybierz **konfiguracje IP,** a następnie wybierz konfigurację IP, jak pokazano na poniższym rysunku:

   ![Wybierz konfigurację IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z konfiguracjami IP dla interfejsu sieciowego. Na poprzednim zdjęciu interfejs sieciowy ma jedną konfigurację IP. Jeśli interfejs sieciowy miał wiele konfiguracji IP, wszystkie one pojawią się na liście i należy wybrać konfigurację IP, z którą chcesz skojarzyć publiczny adres IP.

5. Wybierz **enabled**, a następnie wybierz **adres IP (*Skonfiguruj wymagane ustawienia*).** Wybierz istniejący publiczny adres IP, który automatycznie zamyka pole **Wybierz publiczny adres IP.** Jeśli nie masz żadnych dostępnych publicznych adresów IP na liście, musisz je utworzyć. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Wybierz **pozycję Zapisz**, jak pokazano na poniższym rysunku, a następnie zamknij pole konfiguracji IP.

   ![Włącz publiczny adres IP](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Publiczne adresy IP, które pojawiają się są te, które istnieją w tym samym regionie co maszyna wirtualna. Jeśli w regionie utworzono wiele publicznych adresów IP, wszystkie pojawią się tutaj. Jeśli istnieją są wyszarzone, to dlatego, że adres jest już skojarzony z innym zasobem.

6. Wyświetl publiczny adres IP przypisany do konfiguracji IP, jak pokazano na poniższym rysunku. Może upłynąć kilka sekund, aby pojawić się adres IP.

   ![Wyświetlanie przypisanego publicznego adresu IP](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w każdym regionie, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli potrzebujesz adresu, który ma być przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

7. [Zezwalaj na ruch sieciowy na maszynie Wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj [narzędzie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)lub użyj usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach interfejsu wiersza polecenia. Wybranie **opcji Wypróbuj wywołuje** powłokę w chmurze, za pomocą której można zalogować się do konta platformy Azure.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie `az login`w bash, zaloguj się na platformie Azure za pomocą programu .
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego podłączonego do maszyny Wirtualnej. Użyj polecenia [aktualizacji sieci az nic-ip-config,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) aby skojarzyć publiczny adres IP z konfiguracją IP. Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic,* który istnieje w grupie zasobów o nazwie *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj polecenia [az network public-ip create,](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) aby go utworzyć. Na przykład następujące polecenie tworzy publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej o wszystkich ustawieniach publicznego adresu IP, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używanych dla każdego regionu platformy Azure. Aby wyświetlić listę pul adresów używanych w każdym regionie, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwy interfejsu sieciowego dołączonego do maszyny Wirtualnej, użyj polecenia [az vm nic list,](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) aby je wyświetlić. Na przykład następujące polecenie zawiera listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe obejmują jeden lub więcej wierszy, które są podobne do następującego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *myVMVMNic* jest nazwą interfejsu sieciowego.

   - Jeśli nie znasz nazwy konfiguracji IP dla interfejsu sieciowego, użyj polecenia [az network nic ip-config list,](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) aby je pobrać. Na przykład następujące polecenie zawiera listę nazw konfiguracji IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji IP za pomocą polecenia [az vm list-ip-addresses.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) W poniższym przykładzie przedstawiono adresy IP przypisane do istniejącej maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w każdym regionie, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli potrzebujesz adresu, który ma być przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy na maszynie Wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="powershell"></a>PowerShell

Zainstaluj [program PowerShell](/powershell/azure/install-az-ps)lub użyj usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma preinstalowany program PowerShell i skonfigurowany do używania z kontem. Wybierz przycisk **Wypróbuj** w kolejnych poleceniach programu PowerShell. Wybranie **opcji Wypróbuj wywołuje** powłokę w chmurze, za pomocą której można zalogować się do konta platformy Azure.

1. Jeśli używasz programu PowerShell lokalnie, zaloguj się na platformie Azure za pomocą programu `Connect-AzAccount`.
2. Publiczny adres IP jest skojarzony z konfiguracją IP interfejsu sieciowego podłączonego do maszyny Wirtualnej. Użyj poleceń [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) i [Get-AzVirtualNetworkSubnetConfig,](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) aby uzyskać sieć wirtualną i podsieć, w której znajduje się interfejs sieciowy. Następnie użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby uzyskać interfejs sieciowy i polecenie [Get-AzPublicIpAddress,](/powershell/module/az.network/get-azpublicipaddress) aby uzyskać istniejący publiczny adres IP. Następnie użyj polecenia [Set-AzNetworkInterfaceIpConfig,](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) aby skojarzyć publiczny adres IP z konfiguracją IP i polecenie [Set-AzNetworkInterface,](/powershell/module/Az.Network/Set-AzNetworkInterface) aby zapisać nową konfigurację IP do interfejsu sieciowego.

   Poniższy przykład kojarzy istniejący publiczny adres IP o nazwie *myVMPublicIP* z konfiguracją IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic,* który istnieje w podsieci o nazwie *myVMSubnet* w sieci wirtualnej o nazwie *myVMVNet*. Wszystkie zasoby należą do grupy zasobów o nazwie *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj polecenia [New-AzPublicIpAddress,](/powershell/module/Az.Network/New-AzPublicIpAddress) aby go utworzyć. Na przykład następujące polecenie tworzy *dynamiczny* publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup* w regionie *eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilku ustawień, które można dostosować. Aby dowiedzieć się więcej o wszystkich ustawieniach publicznego adresu IP, zobacz [Tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używanych dla każdego regionu platformy Azure. Aby wyświetlić listę pul adresów używanych w każdym regionie, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwy interfejsu sieciowego podłączonego do maszyny Wirtualnej, użyj polecenia [Get-AZVM,](/powershell/module/Az.Compute/Get-AzVM) aby je wyświetlić. Na przykład następujące polecenie zawiera listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *myVMVMNic* jest nazwą interfejsu sieciowego.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, w której `Get-AzNetworkInterface` znajduje się interfejs sieciowy, użyj polecenia, aby wyświetlić informacje. Na przykład następujące polecenie pobiera informacje o sieci wirtualnej i podsieci dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *myVMVNET* jest nazwą sieci wirtualnej, a *myVMSubnet* jest nazwą podsieci.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Jeśli nie znasz nazwy konfiguracji IP dla interfejsu sieciowego, użyj polecenia [Get-AzNetworkInterface,](/powershell/module/Az.Network/Get-AzNetworkInterface) aby je pobrać. Na przykład następujące polecenie zawiera listę nazw konfiguracji IP dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *ipconfigmyVM* jest nazwą konfiguracji IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji IP za pomocą polecenia [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) W poniższym przykładzie pokazano adres przypisany do publicznego adresu IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Jeśli nie znasz nazwy publicznego adresu IP przypisanego do konfiguracji IP, uruchom następujące polecenia, aby go uzyskać:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do przykładu, który następuje. W przykładowym wyjściu *myVMPublicIP* jest nazwą publicznego adresu IP przypisanego do konfiguracji IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w każdym regionie, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Przypisany adres może być dowolnym adresem w pulach używanych dla regionu. Jeśli potrzebujesz adresu, który ma być przypisany z określonej puli w regionie, użyj [prefiksu publicznego adresu IP](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy na maszynie Wirtualnej](#allow-network-traffic-to-the-vm) z regułami zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="allow-network-traffic-to-the-vm"></a>Zezwalaj na ruch sieciowy na maszynie Wirtualnej

Przed nawiązaniem połączenia z publicznym adresem IP z Internetu upewnij się, że w dowolnej sieciowej grupie zabezpieczeń, która mogła być skojarzona z interfejsem sieciowym, w podsieci znajduje się interfejs sieciowy, należy upewnić się, że są otwarte niezbędne porty, w której można było skojarzyć z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub w obu tych grupach. Chociaż grupy zabezpieczeń filtrują ruch do prywatnego adresu IP interfejsu sieciowego, gdy przychodzący ruch internetowy dociera do publicznego adresu IP, platforma Azure tłumaczy adres publiczny na prywatny adres IP, więc jeśli sieciowa grupa zabezpieczeń uniemożliwia przepływu ruchu, komunikacja z publicznym adresem IP kończy się niepowodzeniem. Skuteczne reguły zabezpieczeń interfejsu sieciowego i jego podsieci można wyświetlić za pomocą [portalu,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [interfejsu wiersza polecenia](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)lub [programu PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Następne kroki

Zezwalaj przychodzącemu ruchowi internetowemu na maszynę wirtualną za pomocą sieciowej grupy zabezpieczeń. Aby dowiedzieć się, jak utworzyć grupę zabezpieczeń sieci, zobacz [Praca z sieciowymi grupami zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups). Aby dowiedzieć się więcej o sieciowych grupach zabezpieczeń, zobacz [Grupy zabezpieczeń](security-overview.md).
