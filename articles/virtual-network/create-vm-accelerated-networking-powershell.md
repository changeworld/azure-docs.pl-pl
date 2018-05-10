---
title: Utwórz maszynę wirtualną platformy Azure za pomocą sieci przyspieszony | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia maszyny wirtualnej systemu Linux za pomocą przyspieszony sieci.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: de69cdf69f30639d048dccd7d433c86f6cb9db7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną systemu Windows za pomocą przyspieszony sieci

W tym samouczku Dowiedz się jak utworzyć maszynę wirtualną (VM) systemu Windows za pomocą przyspieszony sieci. Aby utworzyć Maszynę wirtualną systemu Linux za pomocą przyspieszony sieci, zobacz [Utwórz Maszynę wirtualną systemu Linux za pomocą przyspieszony sieci](create-vm-accelerated-networking-cli.md). Przyspieszone sieci umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Ta ścieżka wysokiej wydajności pomija hosta z ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU do użycia z najbardziej wymagających obciążeń sieci na obsługiwanych typów maszyny Wirtualnej. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z włączonymi i wyłączonymi przyspieszonego sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonego sieci hosta i przełącznik wirtualny musi przejść przez cały ruch sieciowy i maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak grupy zabezpieczeń sieci, list kontroli dostępu, izolacji i innych usług z wirtualizacją sieci dla ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszonego w sieci, ruch sieciowy dociera do maszyny Wirtualnej interfejsu sieciowego (NIC), a następnie jest przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które ma zastosowanie przełącznika wirtualnego są teraz Odciążone i sprzętu. Stosowanie zasad w sprzęcie umożliwia kartę Sieciową do przesyłania dalej ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijanie hosta i przełącznik wirtualny, przy zachowaniu zasady zastosowaniu na hoście.

Korzyści wynikające z przyspieszonego sieci dotyczą tylko maszynę Wirtualną, która jest włączone. Aby uzyskać najlepsze wyniki to idealne rozwiązanie w celu włączenia tej funkcji na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi lub łączącego lokalnymi, ta funkcja ma minimalny wpływ na ogólną opóźnienia.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć opóźnienia / wyższy pakietów na sekundę (pps):** usunięcie przełącznika wirtualnego z ścieżki danych usuwa czasu, jaki poświęcają pakietów na hoście dla przetwarzanie zasad i zwiększa liczbę pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Zmniejszona zakłócenia:** przełącznik wirtualny przetwarzania zależy od ilości zasad, które musi zostać zastosowana i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszanie zasad na sprzęcie usuwa tego zmienności dostarczania pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji maszyny Wirtualnej i wszystkich oprogramowania przerwań i przełączenia kontekstu.
* **Zmniejszyć użycie procesora CPU:** pomijanie przełącznika wirtualnego na hoście prowadzi do mniej użycie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Fabrycznej z galerii Azure obsługiwane są następujące dystrybucji: 
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter** 

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszone sieci jest obsługiwana w najbardziej ogólnego przeznaczenia i rozmiary obliczeniowe są zoptymalizowane pod kątem wystąpienia z Vcpu 2 lub większą.  Te serie obsługiwane są: D/DSv2 i F/Fs

Przyspieszony sieci jest obsługiwany w wystąpieniach, które obsługują wielowątkowość na wystąpień maszyn wirtualnych z co najmniej 4 Vcpu. Są obsługiwane serii: D/DSv3, E/ESv3 Fsv2 i Ms/Mms

Aby uzyskać więcej informacji na wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępna we wszystkich publicznych regiony platformy Azure i chmury Azure dla instytucji rządowych.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszony sieci na uruchomionej maszynie Wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez przyspieszonego sieci włączone może mieć tylko funkcja włączona po zatrzymaniu i alokację.

### <a name="deployment-through-azure-resource-manager"></a>Wdrożenia za pośrednictwem usługi Azure Resource Manager
Nie można wdrożyć maszyn wirtualnych (klasyczne) za pomocą przyspieszony sieci.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny Wirtualnej systemu Windows Azure przyspieszonego sieci

Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszonego w sieci przy użyciu programu Azure PowerShell, możesz również [Utwórz maszynę wirtualną z przyspieszonego w sieci przy użyciu portalu Azure](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu, w obszarze **ustawienia**, wybierz pozycję **włączone**w obszarze **przyspieszony sieci**. Możliwość włączenia przyspieszonego sieci nie jest wyświetlane w portalu chyba, że wybrano [obsługiwanym systemie operacyjnym](#supported-operating-systems) i [rozmiar maszyny Wirtualnej](#supported-vm-instances). Po utworzeniu maszyny wirtualnej, należy wykonać instrukcje [potwierdzić sterownik jest zainstalowany w systemie operacyjnym](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj [programu Azure PowerShell](/powershell/azure/install-azurerm-ps) wersji 5.1.1 lub nowszym. Aby znaleźć obecnie zainstalowanej wersji, uruchom `Get-Module -ListAvailable AzureRM`. Aby zainstalować lub uaktualnić należy zainstalować najnowszą wersję modułu AzureRM z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureRM). W sesji programu PowerShell, zaloguj się do konta platformy Azure przy użyciu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNic*, i *myVM*.

Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

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

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz przyspieszony sieć na istniejące maszyny wirtualne
Jeśli utworzono Maszynę wirtualną bez przyspieszony sieci jest możliwe włączyć tę funkcję na istniejącej maszyny Wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszony sieci spełniając następujące wymagania wstępne opisane powyżej:

* Maszyna wirtualna musi być obsługiwany rozmiar przyspieszony sieci
* Maszyna wirtualna musi być obsługiwany obraz w galerii Azure (i wersji jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS musi być zatrzymana alokację przed włączeniem przyspieszony sieć na dowolnej karty interfejsu Sieciowego

### <a name="individual-vms--vms-in-an-availability-set"></a>Ustaw poszczególnych maszyn wirtualnych i maszyn wirtualnych w dostępności
Najpierw Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub, jeśli zbiór dostępności, wszystkich maszyn wirtualnych w zestawie:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Ważne, proszę Uwaga: Jeśli maszyna wirtualna została utworzona oddzielnie, bez zestawu dostępności, możesz tylko konieczne stop/cofnąć poszczególnych maszyn wirtualnych, aby włączyć przyspieszony sieci.  Jeśli maszyna wirtualna została utworzona z zestawu dostępności, zawarty w zestawie dostępności wszystkich maszyn wirtualnych należy być zatrzymana alokację przed włączeniem przyspieszony sieci na żadnym z kart sieciowych. 

Gdy usługa zostanie zatrzymana, należy włączyć przyspieszony sieci na karcie Sieciowej maszyny Wirtualnej:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Uruchom sieci maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkich maszyn wirtualnych w zestawie i sprawdź, czy włączono przyspieszony sieci: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS różni się nieznacznie, ale wynika z tego samego przepływu pracy.  Przestań maszyn wirtualnych:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Po zatrzymaniu maszyny wirtualnej należy zaktualizować właściwości przyspieszony sieci w interfejsie sieci:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Sprawdź Uwaga, VMSS ma stosowania aktualizacji za pomocą trzy różne ustawienia automatycznego, stopniowego i ręcznego uaktualnienia maszyny Wirtualnej.  W tych instrukcjach zasady są ustawione na tryb automatyczny, aby VMSS przejmą zmian natychmiast po ponownym uruchomieniu.  Ustawić ją na automatyczne, dzięki czemu zmiany są natychmiast pobrana: 

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Na koniec uruchom ponownie VMSS:

```azurepowershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Raz należy uruchomić ponownie, poczekaj, aż do zakończenia uaktualnienia, ale po ukończeniu, funkcji Wirtualnej pojawi się w ramach maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwany rozmiar systemu operacyjnego i maszyny Wirtualnej)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych za pomocą przyspieszony sieci

Tylko można zmienić rozmiar maszyn wirtualnych za pomocą przyspieszony sieci włączony do maszyn wirtualnych, które obsługują przyspieszony sieci.  

Nie można zmienić rozmiaru maszyny Wirtualnej za pomocą przyspieszony sieci włączone, do wystąpienia maszyny Wirtualnej, która nie obsługuje przyspieszony sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/Deallocate maszyny Wirtualnej lub w zestawie dostępności/VMSS, Zatrzymaj/cofnąć wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszone sieci musi zostać wyłączona na karcie Sieciowej maszyny Wirtualnej lub gdy w dostępności zestawu/VMSS, wszystkich maszyn wirtualnych w zestawie/VMSS.
* Po wyłączeniu przyspieszony sieci maszyny Wirtualnej/dostępność zestawu/VMSS można przenieść do nowego rozmiaru, który nie obsługuje przyspieszony sieci i ponowne uruchomienie.  



