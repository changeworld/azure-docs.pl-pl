---
title: Utwórz maszynę wirtualną platformy Azure za pomocą sieci przyspieszony | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia maszyny wirtualnej systemu Linux za pomocą przyspieszony sieci.
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: f3c8853331121fc1e267f6c569279f7d8df907b5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną systemu Windows za pomocą przyspieszony sieci

> [!IMPORTANT] 
> Maszyny wirtualne muszą być tworzone przyspieszony sieci włączone. Nie można włączyć tę funkcję w istniejących maszyn wirtualnych. Wykonaj poniższe kroki, aby włączyć przyspieszonego sieci:
>   1. Usuń maszynę wirtualną
>   2. Utwórz ponownie maszynę wirtualną z włączoną obsługą przyspieszonego sieci
>

W tym samouczku Dowiedz się jak utworzyć maszynę wirtualną (VM) systemu Windows za pomocą przyspieszony sieci. Przyspieszone sieci umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Ta ścieżka wysokiej wydajności pomija hosta z ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU do użycia z najbardziej wymagających obciążeń sieci na obsługiwanych typów maszyny Wirtualnej. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z włączonymi i wyłączonymi przyspieszonego sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonego sieci hosta i przełącznik wirtualny musi przejść przez cały ruch sieciowy i maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak grupy zabezpieczeń sieci, list kontroli dostępu, izolacji i innych usług z wirtualizacją sieci dla ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszonego w sieci, ruch sieciowy dociera do maszyny Wirtualnej interfejsu sieciowego (NIC), a następnie jest przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które ma zastosowanie przełącznika wirtualnego są teraz Odciążone i sprzętu. Stosowanie zasad w sprzęcie umożliwia kartę Sieciową do przesyłania dalej ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijanie hosta i przełącznik wirtualny, przy zachowaniu zasady zastosowaniu na hoście.

Korzyści wynikające z przyspieszonego sieci dotyczą tylko maszynę Wirtualną, która jest włączone. Aby uzyskać najlepsze wyniki to idealne rozwiązanie w celu włączenia tej funkcji na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi lub łączącego lokalnymi, ta funkcja ma minimalny wpływ na ogólną opóźnienia.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć opóźnienia / wyższy pakietów na sekundę (pps):** usunięcie przełącznika wirtualnego z ścieżki danych usuwa czasu, jaki poświęcają pakietów na hoście dla przetwarzanie zasad i zwiększa liczbę pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Zmniejszona zakłócenia:** przełącznik wirtualny przetwarzania zależy od ilości zasad, które musi zostać zastosowana i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszanie zasad na sprzęcie usuwa tego zmienności dostarczania pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji maszyny Wirtualnej i wszystkich oprogramowania przerwań i przełączenia kontekstu.
* **Zmniejszyć użycie procesora CPU:** pomijanie przełącznika wirtualnego na hoście prowadzi do mniej użycie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Microsoft Windows Server 2012 R2 Datacenter i Windows Server 2016.

## <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszone sieci jest obsługiwana w najbardziej ogólnego przeznaczenia i rozmiary obliczeniowe są zoptymalizowane pod kątem wystąpienia z co najmniej 4 Vcpu. W przypadkach, takich jak D/DSv3 lub E/ESv3 obsługujące wielowątkowość przyspieszony sieci jest obsługiwany w wystąpieniach maszyny Wirtualnej z co najmniej 8 Vcpu. Są obsługiwane serii: D/DSv2, D/DSv3 E/ESv3, F/Fs/Fsv2 i Ms/Mms.

Aby uzyskać więcej informacji na wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiony
Dostępna we wszystkich publicznych regiony platformy Azure i chmury Azure dla instytucji rządowych. 

## <a name="limitations"></a>Ograniczenia
Podczas przy użyciu tej możliwości istnieją następujące ograniczenia:

* **Tworzenie interfejsu sieci:** akcelerowanego sieci można włączyć tylko dla nowych kart sieciowych. Nie można włączyć dla istniejącej karty sieciowej.
* **Tworzenie maszyny Wirtualnej:** A kart interfejsu Sieciowego z włączoną obsługą przyspieszonego sieci może zostać dołączona tyko do maszyny Wirtualnej po utworzeniu maszyny Wirtualnej. Nie można dołączyć karty Sieciowej do istniejącej maszyny Wirtualnej. Jeśli dodawanie maszyny Wirtualnej do istniejących danych o dostępności, wszystkich maszyn wirtualnych w zestawie dostępności muszą również przyspieszyć sieci włączone.
* **Tylko wdrożenia za pośrednictwem usługi Azure Resource Manager:** maszyn wirtualnych (klasyczne) nie można wdrożyć za pomocą przyspieszony sieci.

Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszonego w sieci przy użyciu programu Azure PowerShell, możesz również [Utwórz maszynę wirtualną z przyspieszonego w sieci przy użyciu portalu Azure](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu, w obszarze **ustawienia**, wybierz pozycję **włączone**w obszarze **przyspieszony sieci**. Możliwość włączenia przyspieszonego sieci nie jest wyświetlane w portalu chyba, że wybrano [obsługiwanym systemie operacyjnym](#supported-operating-systems) i [rozmiar maszyny Wirtualnej](#supported-vm-instances). Po utworzeniu maszyny wirtualnej, należy wykonać instrukcje [potwierdzić sterownik jest zainstalowany w systemie operacyjnym](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) wersji 5.1.1 lub nowszym. Aby znaleźć obecnie zainstalowanej wersji, uruchom `Get-Module -ListAvailable AzureRM`. Aby zainstalować lub uaktualnić należy zainstalować najnowszą wersję modułu AzureRM z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureRM). W sesji programu PowerShell, zaloguj się do konta platformy Azure przy użyciu [Add-AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNic*, i *myVM*.

Utwórz nową grupę zasobów o [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Najpierw utwórz konfiguracji podsieci z [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). Poniższy przykład tworzy podsieć o nazwie *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Tworzenie sieci wirtualnej z [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), z *mySubnet* podsieci.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Najpierw utwórz reguły grupy zabezpieczeń sieci z [AzureRmNetworkSecurityRuleConfig nowy](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Utwórz grupę zabezpieczeń sieci z [AzureRmNetworkSecurityGroup nowy](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) i przypisz *Zezwalaj RDP wszystkie* regułę zabezpieczeń. Oprócz *Zezwalaj RDP wszystkie* reguł sieciowej grupy zabezpieczeń zawiera kilka reguł domyślnych. Jedna reguła domyślna wyłącza przychodzący dostęp przez Internet, czyli Dlaczego *Zezwalaj RDP wszystkie* reguły jest przypisany do grupy zabezpieczeń sieci, dzięki czemu można zdalne połączenia z maszyną wirtualną, po jego utworzeniu.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Skojarz grupę zabezpieczeń sieci, aby *mySubnet* podsieć o [AzureRmVirtualNetworkSubnetConfig zestawu](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Reguły do grupy zabezpieczeń sieci obowiązuje dla wszystkich zasobów wdrożonych w tej podsieci.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Utwórz interfejs sieciowy z przyspieszonego sieci
Utwórz publiczny adres IP przy użyciu polecenia [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Publiczny adres IP nie jest wymagane, jeśli nie planujesz dostęp do maszyny wirtualnej z Internetu, ale wykonać kroki opisane w tym artykule, jest to wymagane.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Tworzenie interfejsu sieciowego z [AzureRmNetworkInterface nowy](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) z przyspieszony sieci włączone i przypisać publiczny adres IP interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w *mySubnet* podsieć *myVnet* sieci wirtualnej i przypisuje *myPublicIp*  publicznego adresu IP do niej:

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ustaw poświadczenia maszyny Wirtualnej `$cred` przy użyciu zmiennej [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Najpierw należy zdefiniować maszyny Wirtualnej z [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvmconfig). W poniższym przykładzie zdefiniowano maszyny Wirtualnej o nazwie *myVM* z rozmiar maszyny Wirtualnej, który obsługuje przyspieszony Networking (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i właściwości, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utwórz reszty konfiguracji maszyny Wirtualnej z [AzureRmVMOperatingSystem zestaw](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) i [AzureRmVMSourceImage zestawu](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Poniższy przykład tworzy Maszynę wirtualną systemu Windows Server 2016:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Dołączyć interfejsu sieciowego, wcześniej utworzony za pomocą [AzureRmVMNetworkInterface Dodaj](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Na koniec należy utworzyć maszyny Wirtualnej z [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Upewnij się, że sterownik jest zainstalowany w systemie operacyjnym

Po utworzeniu maszyny Wirtualnej na platformie Azure, połączenie z maszyną Wirtualną i upewnij się, że sterownik jest zainstalowany w systemie Windows. 

1. Za pomocą przeglądarki internetowej, otwórz Azure [portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *myVm*. Gdy **myVm** pojawia się w wynikach wyszukiwania kliknij ją. Jeśli **tworzenie** jest widoczna w obszarze **Connect** przycisku Azure nie została jeszcze ukończona tworzenia maszyny Wirtualnej. Kliknij przycisk **Connect** w lewym górnym rogu omówienie tylko po nie jest już wyświetlana **tworzenie** w obszarze **Connect** przycisku.
3. Wprowadź nazwę użytkownika i hasła wprowadzonego w polu [Utwórz maszynę wirtualną](#create-the-virtual-machine). Jeśli nigdy nie podłączona do maszyny Wirtualnej systemu Windows na platformie Azure, zobacz [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kliknij prawym przyciskiem myszy przycisk Start systemu Windows, a następnie kliknij przycisk **Menedżera urządzeń**. Rozwiń węzeł **karty sieciowe** węzła. Upewnij się, że **Mellanox ConnectX 3 wirtualnej funkcja Ethernet karty** pojawia się, jak pokazano na poniższej ilustracji:
   
    ![Menedżer urządzeń](./media/create-vm-accelerated-networking/device-manager.png)

Przyspieszone sieci jest teraz włączony dla maszyny Wirtualnej.
