---
title: Skojarz publiczny adres IP do maszyny wirtualnej
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak skojarzyć publiczny adres IP do maszyny wirtualnej.
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
ms.openlocfilehash: 1b201957a33acd609eed8a2373c8201bdefe9d7d
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64691972"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Skojarz publiczny adres IP do maszyny wirtualnej

W tym artykule dowiesz się, jak skojarzyć publiczny adres IP do istniejącej maszyny wirtualnej (VM). Jeśli chcesz nawiązać połączenie z maszyną wirtualną z Internetu, maszyna wirtualna musi mieć publiczny adres IP, powiązany. Jeśli chcesz utworzyć nową maszynę Wirtualną z publicznym adresem IP, możesz to zrobić za pomocą [witryny Azure portal](virtual-network-deploy-static-pip-arm-portal.md), [interfejsu wiersza polecenia platformy Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md), lub [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Publiczne adresy IP mają nominalnej. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/ip-addresses/). Istnieje limit liczby publicznych adresów IP używanych na subskrypcję. Aby uzyskać więcej informacji, zobacz [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Możesz użyć [witryny Azure portal](#azure-portal), Azure [interfejsu wiersza polecenia](#azure-cli) (CLI) lub [PowerShell](#powershell) skojarzyć publiczny adres IP do maszyny Wirtualnej.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do, lub Wyszukaj maszynę wirtualną, którą chcesz dodać publiczny adres IP, a następnie wybierz ją.
3. W obszarze **ustawienia**, wybierz opcję **sieć**, a następnie wybierz interfejs sieciowy, do którego chcesz dodać publiczny adres IP, jak pokazano na poniższej ilustracji:

   ![Wybierz interfejs sieciowy](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z interfejsów sieciowych dołączonych do maszyny Wirtualnej. Na poprzedniej ilustracji maszyna wirtualna ma tylko jeden interfejs sieciowy. Jeśli maszyna wirtualna ma wiele interfejsów sieciowych, zostaną one wyświetlone i będzie wybierz interfejs sieciowy, którą chcesz skojarzyć publiczny adres IP.

4. Wybierz **konfiguracje adresów IP** a następnie wybierz konfigurację adresu IP, jak pokazano na poniższej ilustracji:

   ![Wybierz konfigurację adresu IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Publiczne adresy IP są skojarzone z konfiguracjami adresów IP dla karty sieciowej. Na poprzedniej ilustracji interfejs sieciowy ma jedną konfigurację adresu IP. Jeśli interfejs sieciowy wielu konfiguracji adresu IP, zostaną one wyświetlone na liście, a następnie należy wybrać konfigurację adresu IP, którą chcesz skojarzyć publiczny adres IP.

5. Wybierz **włączone**, a następnie wybierz **adresu IP (*Skonfiguruj wymagane ustawienia*)** . Wybieranie istniejącego publicznego adresu IP, który automatycznie zamyka **wybierz publiczny adres IP** pole. Jeśli nie masz żadnych dostępnych publiczne adresy IP na liście, należy ją utworzyć. Aby dowiedzieć się więcej, zobacz temat [tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Wybierz **Zapisz**, jak pokazano na ilustracji poniżej, a następnie zamknij okno do konfiguracji adresu IP.

   ![Włączanie publicznego adresu IP](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Publiczne adresy IP, które są wyświetlane są tymi, które istnieją w tym samym regionie co maszyna wirtualna. Jeśli masz wiele publicznych adresów IP, utworzone w regionie, zostaną wyświetlone wszystkie w tym miejscu. Jeśli dowolny są wygaszone, jest to, ponieważ adres jest już skojarzona z innego zasobu.

6. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP, jak pokazano na ilustracji poniżej. Może potrwać kilka sekund dla adresu IP są wyświetlane.

   ![Wyświetl przypisany publiczny adres IP](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adres przypisany, może być dowolny adres w pulach używany dla danego regionu. Jeśli potrzebujesz adresu do przypisania z określonej puli w regionie, użyj [publiczny adres IP prefiksu adresu](public-ip-address-prefix.md).

7. [Zezwalaj na ruch sieciowy do maszyny Wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zainstaluj [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona wstępnie zainstalowany interfejs wiersza polecenia platformy Azure skonfigurowany do użycia z Twoim kontem. Wybierz **wypróbuj** przycisku w interfejsie wiersza polecenia platformy polecenia poniżej. Wybieranie **wypróbuj** wywołuje Cloud Shell, która może zalogować się do konta platformy Azure za pomocą.

1. Jeśli używasz interfejsu wiersza polecenia lokalnie w powłoce Bash, zaloguj się do platformy Azure z `az login`.
2. Publiczny adres IP jest skojarzona z konfiguracji adresu IP interfejsu sieciowego dołączonych do maszyny Wirtualnej. Użyj [az sieci nic-ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) polecenie, aby skojarzyć publiczny adres IP z konfiguracją adresów IP. Poniższy przykład kojarzy istniejącego publicznego adresu IP o nazwie *myVMPublicIP* z konfiguracją adresów IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* znajdujące się w grupie zasobów o nazwie *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj [tworzenie sieci az public-ip](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) polecenie, aby go utworzyć. Na przykład następujące polecenie tworzy publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilka ustawień, które chcesz dostosować. Aby dowiedzieć się więcej na temat wszystkie ustawienia publicznego adresu IP, zobacz [tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używane dla każdego regionu platformy Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwę interfejsu sieciowego dołączonego do maszyny Wirtualnej, użyj [az vm nic listy](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) polecenie, aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do poniższego przykładu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     W poprzednim przykładzie *myVMVMNic* nazywa się interfejs sieciowy.

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla karty sieciowej, użyj [az sieci nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) polecenie, aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji adresów IP dla karty sieciowej o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP za pomocą [az vm-— adresy ip](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) polecenia. W poniższym przykładzie pokazano, adresy IP przypisane do istniejącej maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adres przypisany, może być dowolny adres w pulach używany dla danego regionu. Jeśli potrzebujesz adresu do przypisania z określonej puli w regionie, użyj [publiczny adres IP prefiksu adresu](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny Wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="powershell"></a>PowerShell

Zainstaluj [PowerShell](/powershell/azure/install-az-ps), lub za pomocą usługi Azure Cloud Shell. Usługa Azure Cloud Shell jest bezpłatną powłoką, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Ma ona programu PowerShell wstępnie zainstalowane i skonfigurowane do korzystania z Twoim kontem. Wybierz **wypróbuj** przycisku w programie PowerShell polecenia poniżej. Wybieranie **wypróbuj** wywołuje Cloud Shell, która może zalogować się do konta platformy Azure za pomocą.

1. Jeśli przy użyciu programu PowerShell lokalnie, zaloguj się do platformy Azure z `Connect-AzAccount`.
2. Publiczny adres IP jest skojarzona z konfiguracji adresu IP interfejsu sieciowego dołączonych do maszyny Wirtualnej. Użyj [Get AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) i [Get AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) poleceń, aby uzyskać sieć wirtualną i podsieć, należącym do interfejsu sieciowego. Następnie użyj [Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) polecenie, aby uzyskać interfejs sieciowy i [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) polecenie, aby uzyskać istniejącego publicznego adresu IP. Następnie użyj [AzNetworkInterfaceIpConfig zestaw](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) polecenie, aby skojarzyć publiczny adres IP z konfiguracją adresów IP i [AzNetworkInterface zestaw](/powershell/module/Az.Network/Set-AzNetworkInterface) polecenie, aby zapisać nową konfigurację adresu IP do Interfejs sieciowy.

   Poniższy przykład kojarzy istniejącego publicznego adresu IP o nazwie *myVMPublicIP* z konfiguracją adresów IP o nazwie *ipconfigmyVM* istniejącego interfejsu sieciowego o nazwie *myVMVMNic* znajdujące się w podsieci o nazwie *myVMSubnet* w sieci wirtualnej o nazwie *myVMVNet*. Wszystkie zasoby znajdują się w grupie zasobów o nazwie *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Jeśli nie masz istniejącego publicznego adresu IP, użyj [New AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) polecenie, aby go utworzyć. Na przykład następujące polecenie tworzy *dynamiczne* publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup* w  *eastus* regionu.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Poprzednie polecenie tworzy publiczny adres IP z wartościami domyślnymi dla kilka ustawień, które chcesz dostosować. Aby dowiedzieć się więcej na temat wszystkie ustawienia publicznego adresu IP, zobacz [tworzenie publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres jest przypisywany z puli publicznych adresów IP używane dla każdego regionu platformy Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Jeśli nie znasz nazwę interfejsu sieciowego dołączonego do maszyny Wirtualnej, użyj [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) polecenie, aby je wyświetlić. Na przykład następujące polecenie wyświetla listę nazw interfejsów sieciowych dołączonych do maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do następującego. W przykładowych danych wyjściowych *myVMVMNic* nazywa się interfejs sieciowy.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Jeśli nie znasz nazwę sieci wirtualnej lub podsieci, która znajduje się w interfejsie sieciowym, użyj `Get-AzNetworkInterface` polecenie, aby wyświetlić informacje. Na przykład następujące polecenie pobiera wirtualnego informacji w sieci i podsieci dla interfejsu sieciowego o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do następującego. W przykładowych danych wyjściowych *myVMVNET* jest nazwą sieci wirtualnej i *myVMSubnet* jest nazwą podsieci.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Jeśli nie znasz nazwy konfiguracji adresu IP dla karty sieciowej, użyj [Get AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) polecenie, aby je pobrać. Na przykład następujące polecenie wyświetla listę nazw konfiguracji adresów IP dla karty sieciowej o nazwie *myVMVMNic* w grupie zasobów o nazwie *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do następującego. W przykładowych danych wyjściowych *ipconfigmyVM* jest nazwa konfiguracji adresu IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Wyświetl publiczny adres IP przypisany do konfiguracji adresu IP za pomocą [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) polecenia. W poniższym przykładzie przedstawiono adres przypisany publiczny adres IP o nazwie *myVMPublicIP* w grupie zasobów o nazwie *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Jeśli nie znasz nazwę publiczny adres IP przypisany do konfiguracji adresu IP, uruchom następujące polecenia, aby pobrać go:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Dane wyjściowe zawiera jeden lub więcej wierszy, które są podobne do następującego. W przykładowych danych wyjściowych *myVMPublicIP* nazywa się publiczny adres IP przypisany do konfiguracji adresu IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adres jest przypisywany z puli adresów używanych w każdym regionie platformy Azure. Aby wyświetlić listę pul adresów używanych w poszczególnych regionach, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adres przypisany, może być dowolny adres w pulach używany dla danego regionu. Jeśli potrzebujesz adresu do przypisania z określonej puli w regionie, użyj [publiczny adres IP prefiksu adresu](public-ip-address-prefix.md).

4. [Zezwalaj na ruch sieciowy do maszyny Wirtualnej](#allow-network-traffic-to-the-vm) przy użyciu reguł zabezpieczeń w sieciowej grupie zabezpieczeń.

## <a name="allow-network-traffic-to-the-vm"></a>Zezwalaj na ruch sieciowy do maszyny Wirtualnej

Zanim będzie można połączyć publiczny adres IP z Internetu, upewnij się, że wymagane porty otworzyć w dowolnej grupie zabezpieczeń sieci, które mogą mieć skojarzone do interfejsu sieciowego i/lub podsieci, w której znajduje się interfejs sieciowy. Chociaż zabezpieczeń grupy filtrowania ruchu na prywatny adres IP interfejsu sieciowego, gdy ruch przychodzący z Internetu dociera do publicznego adresu IP Azure tłumaczy publiczny adres prywatny adres IP adres, więc jeśli sieciowa grupa zabezpieczeń uniemożliwia przepływ ruchu komunikacji z publicznym adresem IP nie powiedzie się. Możesz wyświetlić obowiązujących reguł zabezpieczeń dla interfejsu sieciowego i jej podsieci przy użyciu [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [interfejsu wiersza polecenia](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), lub [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Kolejne kroki

Zezwalaj na ruch przychodzący z Internetu do maszyny Wirtualnej z sieciową grupą zabezpieczeń. Aby dowiedzieć się, jak utworzyć sieciową grupę zabezpieczeń, zobacz [Praca z sieciowymi grupami zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups). Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [grup zabezpieczeń](security-overview.md).
