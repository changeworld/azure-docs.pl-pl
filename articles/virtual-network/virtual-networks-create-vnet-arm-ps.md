---
title: Tworzenie sieci wirtualnej platformy Azure z wieloma podsieciami - PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną z wieloma podsieciami przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880563"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Tworzenie sieci wirtualnej z wieloma podsieciami przy użyciu programu PowerShell

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Tworzenie wielu podsieci w sieci wirtualnej umożliwia do segmentu sieci, dzięki czemu można filtrować lub sterowania przepływem ruchu między podsieciami. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie podsieci
> * Test łączności sieciowej między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 3,6 lub nowszej. Uruchom ` Get-Module -ListAvailable AzureRM` można odnaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz nową grupę zasobów o [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *EastUS* lokalizacji.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

**Prefiks adresu** jest określone w notacji CIDR. Prefiks adresu określonego obejmuje 10.0.0.0-10.0.255.254 adresów IP.

## <a name="create-a-subnet"></a>Tworzenie podsieci

Podsieć jest tworzony przez najpierw Tworzenie konfiguracji podsieci, a następnie zaktualizować sieci wirtualnej z konfiguracją podsieci. Tworzenie konfiguracji podsieci z [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy dwie konfiguracje podsieci dla *publicznego* i *prywatnej* podsieci:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

**Prefiks adresu** określona dla podsieci musi znajdować się w prefiksie adresu zdefiniowana dla sieci wirtualnej. Azure DHCP przypisuje adresy IP z prefiksem adresu podsieci z zasobami wdrożonymi w podsieci. Azure przypisuje 10.0.0.4-10.0.0.254 adresy tylko z zasobami wdrożonymi w **publicznego** podsieci, ponieważ Azure rezerwuje pierwsze cztery adresów (10.0.0.0-10.0.0.3 podsieci, w tym przykładzie) i ostatnich adresów () 10.0.0.255 podsieci, w tym przykładzie) w każdej podsieci.

Zapisać konfiguracje podsieci sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), co powoduje podsieci w sieci wirtualnej:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Przed wdrożeniem sieci wirtualnych platformy Azure i podsieci w środowisku produkcyjnym, zalecane jest, że należy dokładnie zapoznać się z przestrzeni adresowej [zagadnienia](manage-virtual-network.md#create-a-virtual-network) i [limity sieci wirtualnej](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Gdy zasoby są wdrażane na podsieci, niektóre sieci wirtualnej i zmiany podsieci, takie jak zmiana zakresów adresów, może wymagać ponownego wdrażania istniejących zasobów platformy Azure, wdrażana w obrębie podsieci.

## <a name="test-network-communication"></a>Test łączności sieciowej

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Jeden typ zasobów, które można wdrożyć w sieci wirtualnej jest maszyną wirtualną. Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby można było sprawdzić komunikację sieciową między nimi i Internetu w kolejnym kroku. 

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną z [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVmWeb* w *publicznego* podsieć *myVirtualNetwork* sieci wirtualnej. `-AsJob` Opcja tworzy maszynę wirtualną w tle, dzięki czemu można kontynuować do następnego kroku. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które chcesz zalogować się do maszyny wirtualnej z.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmWeb" `
    -AsJob
```

Azure automatycznie przypisać 10.0.0.4 jako prywatny adres IP maszyny wirtualnej, ponieważ 10.0.0.4 jest pierwszy dostępny adres IP w *publicznego* podsieci. 

Utwórz maszynę wirtualną w *prywatnej* podsieci.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -ImageName "Win2016Datacenter" `
    -Name "myVmMgmt"
```

Maszyna wirtualna ma kilka minut na utworzenie. Chociaż w dane wyjściowe, Azure nieprzypisany 10.0.1.4 jako prywatny adres IP maszyny wirtualnej, ponieważ 10.0.1.4 jest pierwszy dostępny adres IP w *prywatnej* podsieć *myVirtualNetwork*. 

Nie Kontynuuj pozostałe kroki dopiero po utworzeniu maszyny wirtualnej i programu PowerShell zwraca dane wyjściowe.

Maszyny wirtualne utworzone w tym artykule istnieje [interfejs sieciowy](virtual-network-network-interface.md) z jednym adresem IP, który jest przypisywany dynamicznie do interfejsu sieciowego. Po wdrożeniu maszyny Wirtualnej, można [dodać wiele prywatnych i publicznych adresów IP lub zmień metoda przypisywania adresów IP statycznej](virtual-network-network-interface-addresses.md#add-ip-addresses). Możesz [dodać interfejsów sieciowych](virtual-network-network-interface-vm.md#vm-add-nic), w granicach obsługiwane przez [rozmiar maszyny Wirtualnej](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) wybranym podczas tworzenia maszyny wirtualnej. Możesz również [Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV)](create-vm-accelerated-networking-powershell.md) dla maszyny Wirtualnej, ale tylko wtedy, gdy tworzenie maszyny Wirtualnej z rozmiar maszyny Wirtualnej, która obsługuje możliwości.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikację między maszynami wirtualnymi i z Internetu

Publiczny adres IP maszyny wirtualnej można połączyć z Internetu. Podczas tworzenia Azure *myVmMgmt* maszynę wirtualną, publiczny adres IP o nazwie *myVmMgmt* została także utworzona i przypisana do maszyny wirtualnej. Jeśli maszyna wirtualna nie musi mieć publiczny adres IP przypisane do niej, Azure przypisuje publicznego adresu IP do każdej maszyny wirtualnej, które tworzysz, domyślnie. Aby komunikować się z Internetu na maszynę wirtualną, publiczny adres IP musi można przypisać do maszyny wirtualnej. Wszystkie maszyny wirtualne mogą komunikować się wychodzące z Internetem, czy przypisano publiczny adres IP do maszyny wirtualnej. Aby dowiedzieć się więcej na temat połączenia wychodzące Internet na platformie Azure, zobacz [połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) do zwrócenia publicznego adresu IP maszyny wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVmMgmt* maszyny wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z *myVmMgmt* maszynę wirtualną z komputera lokalnego. Zastąp `<publicIpAddress>` adres IP zwrócony z poprzednie polecenie.

```
mstsc /v:<publicIpAddress>
```

Plik protokołu Remote Desktop Protocol (RDP) jest utworzony, pobrana na komputer i otworzyć. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić podczas obliczania wprowadzone poświadczenia możesz utworzono maszynę wirtualną), a następnie kliknij przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia. 

W kolejnym kroku ping jest używany do komunikacji z *myVmMgmt* maszynę wirtualną z *myVmWeb* maszyny wirtualnej. Polecenie ping używa protokołu ICMP, którego odmówiono przez zaporę systemu Windows, domyślnie. Włącz ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w wierszu polecenia:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Chociaż ping są używane w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.

Ze względów bezpieczeństwa jest często, aby ograniczyć liczbę maszyn wirtualnych, które można zdalnie łączyć się w sieci wirtualnej. W tym samouczku *myVmMgmt* maszyny wirtualnej jest używany do zarządzania *myVmWeb* maszyny wirtualnej w sieci wirtualnej. Użyj następującego polecenia do usług pulpitu zdalnego do *myVmWeb* maszynę wirtualną z *myVmMgmt* maszyny wirtualnej:

``` 
mstsc /v:myVmWeb
```

Do komunikacji *myVmMgmt* maszynę wirtualną z *myVmWeb* maszyny wirtualnej, wprowadź następujące polecenie w wierszu polecenia:

```
ping myvmmgmt
```

Pojawi się dane wyjściowe podobne do następujących przykładowe dane wyjściowe:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Można stwierdzić, że adres *myVmMgmt* 10.0.1.4 jest maszyny wirtualnej. 10.0.1.4 był pierwszy dostępny adres IP z zakresu adresów *prywatnej* podsieci, która została wdrożona *myVmMgmt* maszynę wirtualną w poprzednim kroku.  Zobaczysz, że w pełni kwalifikowaną nazwę maszyny wirtualnej jest *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Chociaż *dar5p44cif3ulfq00wxznl3i3f* część nazwy domeny różni się dla maszyny wirtualnej, pozostałej części nazwy domeny są takie same. Domyślnie wszystkie maszyny wirtualne Azure używają domyślna usługa Azure DNS. Wszystkie maszyny wirtualne sieci wirtualnej można rozpoznawania nazw innych maszyn wirtualnych w tej samej sieci wirtualnej przy użyciu usługi DNS domyślne platformy Azure. Zamiast przy użyciu usługi DNS domyślne platformy Azure, możesz użyć serwera DNS lub możliwości prywatnej domeny usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) lub [przy użyciu usługi Azure DNS dla domen prywatnej](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Aby zainstalować usługi Internet Information Services (IIS) dla systemu Windows Server na *myVmWeb* maszyny wirtualnej, wprowadź następujące polecenie w sesji programu PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po ukończeniu instalacji usług IIS, odłącz *myVmWeb* sesji usług pulpitu zdalnego, co spowoduje pozostawienie w *myVmMgmt* sesji usług pulpitu zdalnego. Otwórz przeglądarkę sieci web i przejdź do http://myvmweb. Strona powitalna usług IIS zostanie wyświetlony.

Odłącz *myVmMgmt* sesji usług pulpitu zdalnego.

Pobierz publiczny adres Azure przypisane do *myVmWeb* maszyny wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Na komputerze użytkownika, przejdź do publicznego adresu IP *myVmWeb* maszyny wirtualnej. Próba wyświetlenia strona powitalna usług IIS z tego komputera nie powiedzie się. Próba nie powiedzie się, ponieważ gdy maszyny wirtualne zostały wdrożone, Azure utworzyć grupę zabezpieczeń sieci dla każdej maszyny wirtualnej domyślnie. 

Grupa zabezpieczeń sieci zawiera zasady zabezpieczeń, które dozwolonych lub zablokowanych dla ruchu przychodzącego i wychodzącego ruchu sieciowego przez port i adres IP. Domyślne grupy zabezpieczeń sieci utworzone Azure umożliwia komunikację za pośrednictwem wszystkie porty między zasobami w tej samej sieci wirtualnej. Dla maszyn wirtualnych systemu Windows domyślną grupę zabezpieczeń sieci nie zezwala na cały ruch przychodzący z Internetu przez wszystkie porty, Zaakceptuj portu TCP 3389 (RDP). W związku z tym domyślnie można również RDP bezpośrednio do *myVmWeb* maszyny wirtualnej z Internetu, nawet jeśli nie możesz portu 3389 Otwórz na serwerze sieci web. Ponieważ przeglądanie sieci web komunikuje się za pośrednictwem portu 80, komunikacji nie z Internetu, ponieważ nie istnieje żadna reguła w domyślnej grupie zabezpieczeń sieci zezwala na ruch przez port 80.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrażania sieci wirtualnej z wieloma podsieciami. Również wiesz, że podczas tworzenia maszyny wirtualnej systemu Windows Azure tworzy interfejs sieciowy on dołączony do maszyny wirtualnej i tworzy sieciową grupę zabezpieczeń, który tylko zezwala na ruch przez port 3389, z Internetu. Przejście do dalej samouczkiem, aby dowiedzieć się, jak do filtrowania ruchu sieciowego do podsieci, a nie do poszczególnych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego do podsieci](./virtual-networks-create-nsg-arm-ps.md)
