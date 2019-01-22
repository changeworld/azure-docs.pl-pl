---
title: Tworzenie maszyn wirtualnych dzięki przyspieszonej sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux z przyspieszonej sieci.
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
ms.openlocfilehash: 3ba7e8129d577faa87544f8feded51a14559eb51
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435536"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną Windows dzięki przyspieszonej sieci

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną (VM) Windows dzięki przyspieszonej sieci. Aby utworzyć Maszynę wirtualną systemu Linux przy użyciu Accelerated Networking, zobacz [tworzenie maszyny Wirtualnej z systemem Linux przy użyciu Accelerated Networking](create-vm-accelerated-networking-cli.md). Przyspieszona sieć umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Tej ścieżki o wysokiej wydajności pomija hosta ze ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU, do użytku z najbardziej wymagających obciążeń sieci na obsługiwane typy maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z lub bez przyspieszonej łączności sieciowej:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej łączności sieciowej hosta i przełącznik wirtualny musi przechodzić przez cały ruch sieciowy do i z maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacji i innych usług z wirtualizacją sieci do ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełączniku wirtualnym](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszoną siecią ruch sieciowy dociera do maszyny Wirtualnej interfejsu sieciowego (NIC) i jest następnie przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które mają zastosowanie przełącznika wirtualnego są teraz Odciążone i stosowane w sprzętu. Stosowanie zasad w sprzęcie umożliwia karty Sieciowej do przekazywania ruchu sieciowego bezpośrednio do maszyny Wirtualnej, hosta i przełącznik wirtualny, z pominięciem przy zachowaniu wszystkich zasad, które jest stosowany na hoście.

Korzyści z przyspieszoną siecią dotyczą tylko maszynę Wirtualną, która jest włączona. Aby uzyskać najlepsze wyniki jest idealnym rozwiązaniem włączyć tę funkcję na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi ani nawiązywania połączenia w środowisku lokalnym, ta funkcja ma minimalny wpływ na ogólny czas oczekiwania.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć czas oczekiwania wyższe pakietów na sekundę (pps):** Usunięcie przełącznika wirtualnego z ścieżki danych spowoduje usunięcie pakietów poświęcić czas na hoście dla przetwarzania zasad i zwiększa się liczba pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Jednoczesnym zmniejszeniu zakłóceń:** Przełącznik wirtualny przetwarzania zależy od tego, liczbę zasad, która musi zostać zastosowana i obciążenie procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad na sprzęcie usuwa tego zmienność, zapewniając pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji z maszyną Wirtualną i wszystkie przerwań oprogramowania i przełączeń kontekstu.
* **Obniżenie wykorzystanie procesora CPU:** Przełącznik wirtualny na hoście z pominięciem prowadzi do mniejsze wykorzystanie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Poniższe dystrybucje obsługiwane są gotowe w galerii platformy Azure: 
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter** 

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszona sieć jest obsługiwana w najbardziej ogólnego przeznaczenia i oferujące zoptymalizowane możliwości obliczeniowe wystąpień o rozmiarach z co najmniej 2 procesorów wirtualnych.  Te serie obsługiwane są następujące: D/DSv2 i F/Fs

W wystąpieniach będzie używanych obsługujące wielowątkowość przyspieszonej sieci jest obsługiwany na wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych. Seria obsługiwane są następujące: D/DSv3, E/ESv3, Fsv2 i Ms/Mms

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach platformy Azure i Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie Accelerated Networking na uruchomionej maszyny Wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez obsługą przyspieszonej sieci może mieć tylko funkcja jest włączona, gdy zostanie zatrzymana i z cofniętą alokacją.

### <a name="deployment-through-azure-resource-manager"></a>Wdrażanie za pomocą usługi Azure Resource Manager
Nie można wdrożyć maszyny wirtualne (klasyczne) dzięki przyspieszonej sieci.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Tworzenie Windows maszyny Wirtualnej przy użyciu platformy Azure przyspieszona sieć

Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszoną siecią przy użyciu programu Azure PowerShell, możesz również [Utwórz maszynę wirtualną z przyspieszoną siecią przy użyciu witryny Azure portal](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu w obszarze **ustawienia**, wybierz opcję **włączone**w obszarze **funkcji przyspieszonej łączności sieciowej**. Możliwość włączenia przyspieszonej łączności sieciowej nie jest wyświetlane w portalu, chyba że wybrano [obsługiwanym systemie operacyjnym](#supported-operating-systems) i [rozmiar maszyny Wirtualnej](#supported-vm-instances). Po utworzeniu maszyny wirtualnej, należy wykonać instrukcje w [upewnij się, sterownik jest zainstalowany w systemie operacyjnym](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj [programu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) wersji 5.1.1 lub nowszej. Aby znaleźć obecnie zainstalowaną wersją, uruchom `Get-Module -ListAvailable AzureRM`. Jeśli potrzebujesz zainstalować lub uaktualnić, zainstaluj najnowszą wersję modułu AzureRM z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureRM). W sesji programu PowerShell Zaloguj się do konta platformy Azure przy użyciu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione *myResourceGroup*, *myNic*, i *myVM*.

Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Najpierw utwórz konfigurację podsieci przy użyciu [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). Poniższy przykład tworzy podsieć o nazwie *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Tworzenie sieci wirtualnej za pomocą [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), za pomocą *mySubnet* podsieci.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Najpierw należy utworzyć reguły sieciowej grupy zabezpieczeń z [polecenie New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

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

Utwórz sieciową grupę zabezpieczeń z [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) i przypisać *Zezwalaj na RDP wszystkie* regułę zabezpieczeń. Oprócz *Zezwalaj na RDP wszystkie* reguły i sieciowa grupa zabezpieczeń zawiera kilka reguł domyślnych. Jedna reguła domyślna wyłącza wszelki dostęp dla ruchu przychodzącego z Internetu, co jest dlaczego *Zezwalaj na RDP wszystkie* reguły jest przypisany do grupy zabezpieczeń sieci, dzięki czemu można zdalnie łączyć się maszyna wirtualna po jego utworzeniu.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Kojarzenie sieciowej grupy zabezpieczeń do *mySubnet* podsieć o [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Reguła w sieciowej grupie zabezpieczeń obowiązuje dla wszystkich zasobów wdrożonych w podsieci.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Utwórz interfejs sieciowy z przyspieszoną siecią
Utwórz publiczny adres IP przy użyciu polecenia [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Publiczny adres IP nie jest wymagane, jeśli nie ma dostępu do maszyny wirtualnej z Internetu, ale wykonanie czynności opisanych w tym artykule, jest to wymagane.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Utwórz interfejs sieciowy z [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) z przyspieszonej sieci włączona i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w *mySubnet* podsieci *myVnet* sieci wirtualnej i przypisuje *myPublicIp*  publicznego adresu IP do niego:

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

Najpierw należy zdefiniować przy użyciu [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). W poniższym przykładzie zdefiniowano Maszynę wirtualną o nazwie *myVM* przy użyciu rozmiaru maszyny Wirtualnej, który obsługuje Accelerated Networking (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i właściwości, zobacz [rozmiarów maszyn wirtualnych Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utworzyć pozostałą część konfiguracji maszyny Wirtualnej za pomocą [AzureRmVMOperatingSystem zestaw](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) i [AzureRmVMSourceImage zestaw](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Poniższy przykład tworzy maszynę Wirtualną z systemem Windows Server 2016:

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

Dołączanie interfejsu sieciowego, która została wcześniej utworzona za pomocą [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Na koniec utwórz maszynę Wirtualną za pomocą [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Upewnij się, że sterownik jest zainstalowany w systemie operacyjnym

Po utworzeniu maszyny Wirtualnej na platformie Azure, nawiązać połączenie z maszyną Wirtualną i upewnij się, że sterownik jest zainstalowany w Windows.

1. Korzystając z przeglądarki internetowej, otwórz Azure [portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *myVm*. Gdy **myVm** pojawia się w wynikach wyszukiwania, kliknij go. Jeśli **tworzenie** jest widoczna w obszarze **Connect** przycisku Azure nie została jeszcze ukończona tworzenia maszyny Wirtualnej. Kliknij przycisk **Connect** w lewym górnym rogu przeglądu dopiero po użytkownik nie jest już wyświetlana **tworzenie** w obszarze **Connect** przycisku.
3. Wprowadź nazwę użytkownika i hasło wprowadzone w [Utwórz maszynę wirtualną](#create-the-virtual-machine). Jeśli nigdy nie nawiązano połączenie maszyny Wirtualnej z systemem Windows na platformie Azure, zobacz [Połącz z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kliknij prawym przyciskiem myszy przycisk Windows Start, a następnie kliknij przycisk **Menedżera urządzeń**. Rozwiń **karty sieciowe** węzła. Upewnij się, że **Mellanox ConnectX-3 wirtualna karta Ethernet funkcja** pojawi się, jak pokazano na poniższej ilustracji:

    ![Menedżer urządzeń](./media/create-vm-accelerated-networking/device-manager.png)

Przyspieszona sieć jest teraz włączone dla maszyny Wirtualnej.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz Accelerated Networking na istniejących maszynach wirtualnych
Jeśli utworzono maszynę Wirtualną bez przyspieszonej sieci jest możliwe włączyć tę funkcję na istniejącej maszyny Wirtualnej.  Maszyna wirtualna musi obsługiwać Accelerated Networking, spełniając następujące wymagania wstępne opisane powyżej:

* Maszyna wirtualna musi być obsługiwany rozmiar dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwany obraz w galerii platformy Azure (oraz wersja jądra systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub zestawu skalowania maszyn wirtualnych musi być zatrzymana/z cofniętą alokacją przed włączeniem Accelerated Networking na dowolnej karcie Sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Ustaw poszczególnych maszyn wirtualnych i maszyn wirtualnych w dostępności
Najpierw Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Ważne,. Uwaga: Jeśli Twoja maszyna wirtualna została utworzona pojedynczo, bez zestawu dostępności, możesz jedynie konieczne Zatrzymaj/Cofnij Przydział poszczególnych maszyn wirtualnych, aby umożliwić przyspieszonej sieci.  Jeśli maszyna wirtualna została utworzona przy użyciu zestawu dostępności, wszystkich maszyn wirtualnych znajdujących się w zestawie dostępności muszą być zatrzymana/z cofniętą alokacją przed włączeniem Accelerated Networking na żadnym z kart sieciowych. 

Po zatrzymaniu Włącz przyspieszonej sieci na karcie interfejsu Sieciowego maszyny wirtualnej:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Ponowne uruchomienie Twojej maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że włączono przyspieszonej sieci: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
Zestawu skalowania maszyn wirtualnych jest nieco inna, ale poniżej tego samego przepływu pracy.  Po pierwsze Zatrzymaj maszyny wirtualne:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Gdy maszyny wirtualne zostaną zatrzymane, zaktualizuj właściwość adres Accelerated Networking, w ramach interfejsu sieciowego:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

. Uwaga: zestawu skalowania maszyn wirtualnych ma stosowania aktualizacji przy użyciu trzech różnych ustawień automatycznego, stopniowe i ręcznego uaktualnienia maszyny Wirtualnej.  W tych instrukcjach zasady ustawiono automatyczne tak, aby VMSS przejmą zmiany od razu po ponownym uruchomieniu komputera.  Aby ustawić go na tryb automatyczny tak, aby zmiany są wykrywane natychmiast: 

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Na koniec uruchom ponownie zestawu skalowania maszyn wirtualnych:

```azurepowershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Po można ponownie uruchomić, poczekaj, aż uaktualnień zakończyć, ale po ukończeniu, VF pojawi się wewnątrz maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwany rozmiar maszyn wirtualnych i systemu operacyjnego)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych dzięki przyspieszonej sieci

Tylko można zmienić rozmiar maszyn wirtualnych dzięki przyspieszonej sieci, włączone na maszynach wirtualnych, które obsługują przyspieszonej sieci.  

Nie można zmienić rozmiaru maszyny Wirtualnej z przyspieszonej sieci włączona, do wystąpienia maszyny Wirtualnej, która nie obsługuje przyspieszonej sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub w zestawie dostępności/VMSS, Zatrzymaj/Cofnij Przydział wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszona sieć musi zostać wyłączone na karcie interfejsu Sieciowego maszyny Wirtualnej lub jeśli w dostępności zestawu/zestawu skalowania maszyn wirtualnych, wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu Accelerated Networking dostępności dla maszyny Wirtualnej/set/VMSS można przenieść na nowy rozmiar, który nie obsługuje Accelerated Networking i ponownie uruchomić.  



