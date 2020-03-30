---
title: Tworzenie maszyny wirtualnej platformy Azure z przyspieszoną siecią — usługa Azure PowerShell
description: Dowiedz się, jak utworzyć maszynę wirtualną systemu Linux z przyspieszoną obsługą sieci.
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
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245059"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Tworzenie maszyny wirtualnej systemu Windows z przyspieszoną siecią przy użyciu programu Azure PowerShell

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną systemu Windows za pomocą przyspieszonej sieci. Aby utworzyć maszynę wirtualną z systemem Linux z przyspieszoną siecią, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z przyspieszoną siecią](create-vm-accelerated-networking-cli.md). Przyspieszona sieć umożliwia wirtualną wirtualną wirtualną wirtualizację pojedynczego katalogu głównego (SR-IOV), znacznie poprawiając jej wydajność sieciową. Ta ścieżka o wysokiej wydajności omija hosta z ścieżki danych, zmniejszając opóźnienia, jitter i wykorzystanie procesora CPU, do użytku z najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Poniższy obraz przedstawia komunikację między dwoma maszynami wirtualnymi z przyspieszoną siecią i bez niej:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej sieci cały ruch sieciowy do i z maszyny Wirtualnej musi przechodzić przez hosta i przełącznika wirtualnego. Przełącznik wirtualny zapewnia wszystkie wymuszanie zasad, takie jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacja i inne zwirtualizowane usługi sieciowe dla ruchu sieciowego. Aby dowiedzieć się więcej o przełącznikach wirtualnych, zobacz [Wirtualizacja sieci Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx).

Dzięki przyspieszonej sieci ruch sieciowy dociera do interfejsu sieciowego maszyny Wirtualnej, a następnie jest przekazywanie dalej do maszyny Wirtualnej. Wszystkie zasady sieciowe stosowane przełącznik wirtualny są teraz odciążane i stosowane w sprzęcie. Stosowanie zasad w sprzęcie umożliwia karcie sieciowej przekazywanie ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijając hosta i przełącznika wirtualnego, zachowując wszystkie zasady stosowane w hoście.

Korzyści z przyspieszonej sieci mają zastosowanie tylko do maszyny Wirtualnej, na których jest włączona. Aby uzyskać najlepsze wyniki, jest idealnym rozwiązaniem, aby włączyć tę funkcję na co najmniej dwóch maszyn wirtualnych połączonych z tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikowania się w sieciach wirtualnych lub łączenia lokalnego, ta funkcja ma minimalny wpływ na ogólne opóźnienie.

## <a name="benefits"></a>Korzyści
* **Mniejsze opóźnienie / wyższe pakiety na sekundę (pps):** Usunięcie przełącznika wirtualnego z ścieżki danych powoduje usunięcie czasu spędzanego przez pakiety na hoście w celu przetwarzania zasad i zwiększenie liczby pakietów, które mogą być przetwarzane wewnątrz maszyny Wirtualnej.
* **Zmniejszono tremę:** Przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad do sprzętu usuwa tę zmienność, dostarczając pakiety bezpośrednio do maszyny Wirtualnej, usuwając komunikację hosta z maszyną wirtualną i wszystkie przerwania oprogramowania i przełączniki kontekstu.
* **Zmniejszone wykorzystanie procesora:** Pomijanie przełącznika wirtualnego na hoście prowadzi do mniejszego wykorzystania procesora CPU do przetwarzania ruchu sieciowego.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane po wyjęciu z pudełka z galerii Azure:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości rozmiarów wystąpień ogólnego przeznaczenia i zoptymalizowanych pod kątem obliczeń z 2 lub więcej procesorami wirtualnymi.  Obsługiwane serie to: D/DSv2 i F/Fs

W przypadku wystąpień obsługujących hiperwątkową przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorami wirtualnymi. Obsługiwane serie to: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms i Ms/Mmsv2.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach platformy Azure i usłudze Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszonej sieci na uruchomionej maszynie wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez włączonej przyspieszonej sieci może mieć włączoną funkcję tylko wtedy, gdy zostanie zatrzymana i cofnięta.

### <a name="deployment-through-azure-resource-manager"></a>Wdrażanie za pośrednictwem usługi Azure Resource Manager
Maszyny wirtualne (klasyczne) nie można wdrożyć z przyspieszoną siecią.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny Wirtualnej systemu Windows z akceleracjową siecią platformy Azure
## <a name="portal-creation"></a>Tworzenie portalu
Chociaż w tym artykule przedstawiono kroki tworzenia maszyny wirtualnej z przyspieszoną siecią przy użyciu programu Azure Powershell, można również [utworzyć maszynę wirtualną z przyspieszoną siecią za pomocą portalu Azure.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Podczas tworzenia maszyny wirtualnej w portalu w bloku **Tworzenie maszyny wirtualnej** wybierz kartę **Sieć.**  Na tej karcie dostępna jest opcja **przyspieszonej sieci**.  Jeśli wybrano [obsługiwany system operacyjny](#supported-operating-systems) i rozmiar maszyny [Wirtualnej,](#supported-vm-instances)ta opcja zostanie automatycznie zapełniona jako "On".  Jeśli nie, wypełni opcję "Off" dla przyspieszonej sieci i da użytkownikowi powód, dla którego nie jest włączona.   
* *Uwaga:* Tylko obsługiwane systemy operacyjne można włączyć za pośrednictwem portalu.  Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszoną sieć, utwórz maszynę wirtualną przy użyciu interfejsu wiersza polecenia lub programu Powershell. 

Po utworzeniu maszyny wirtualnej można potwierdzić, że przyspieszona sieć jest włączona, postępując zgodnie z instrukcjami w funkcji Potwierdź, że przyspieszona sieć jest włączona.

## <a name="powershell-creation"></a>Tworzenie programu Powershell
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps) w wersji 1.0.0 lub nowszej. Aby znaleźć aktualnie zainstalowaną `Get-Module -ListAvailable Az`wersję, uruchom program . Jeśli chcesz zainstalować lub uaktualnić, zainstaluj najnowszą wersję modułu Az z [Galerii programu PowerShell](https://www.powershellgallery.com/packages/Az). W sesji programu PowerShell zaloguj się do konta platformy Azure przy użyciu [connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmowały *myResourceGroup*, *myNic*i *myVM*.

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji centralus:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Najpierw utwórz konfigurację podsieci za pomocą [new-azVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Poniższy przykład tworzy podsieć o nazwie *mySubnet:*

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Utwórz sieć wirtualną za pomocą [sieci New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)za pomocą podsieci *mySubnet.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Najpierw utwórz regułę grupy zabezpieczeń sieci z [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
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

Utwórz grupę zabezpieczeń sieci z [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) i przypisz do niej regułę zabezpieczeń *Zezwalaj na wszystko RDP.* Oprócz *reguły Zezwalaj na wszystko-RDP* grupa zabezpieczeń sieci zawiera kilka reguł domyślnych. Jedna reguła domyślna wyłącza cały dostęp przychodzący z Internetu, dlatego reguła *Zezwalaj na wszystko RDP-Wszystko* jest przypisana do sieciowej grupy zabezpieczeń, dzięki czemu można zdalnie połączyć się z maszyną wirtualną po jej utworzeniu.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Skojarz grupę zabezpieczeń sieci z podsiecią *mySubnet* z [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Reguła w sieciowej grupie zabezpieczeń jest skuteczna dla wszystkich zasobów wdrożonych w podsieci.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Tworzenie interfejsu sieciowego z przyspieszoną siecią
Utwórz publiczny adres IP przy użyciu polecenia [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Publiczny adres IP nie jest wymagany, jeśli nie planujesz dostępu do maszyny wirtualnej z Internetu, ale aby wykonać kroki opisane w tym artykule, jest to wymagane.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Utwórz interfejs sieciowy z [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) z włączoną obsługą przyspieszonej sieci i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w podsieci *mySubnet* sieci wirtualnej *myVnet* i przypisuje do niego publiczny adres IP *myPublicIp:*

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Ustaw poświadczenia maszyny Wirtualnej `$cred` na zmienną przy użyciu [get-credential:](/powershell/module/microsoft.powershell.security/get-credential)

```powershell
$cred = Get-Credential
```

Najpierw zdefiniuj maszynę wirtualną za pomocą [pliku New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Poniższy przykład definiuje maszynę wirtualną o nazwie *myVM* o rozmiarze maszyny Wirtualnej, która obsługuje przyspieszoną sieć *(Standard_DS4_v2):*

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Aby uzyskać listę wszystkich rozmiarów i cech maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Utwórz pozostałą część konfiguracji maszyny wirtualnej za pomocą [systemu Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) i [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). W poniższym przykładzie utworzy się maszynę wirtualną z systemem Windows Server 2016:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Podłącz interfejs sieciowy utworzony wcześniej za pomocą [dodatku AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Na koniec utwórz maszynę wirtualną za pomocą [nowego AzVM:](/powershell/module/az.compute/new-azvm)

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Potwierdź, że sterownik jest zainstalowany w systemie operacyjnym

Po utworzeniu maszyny Wirtualnej na platformie Azure połącz się z maszyną wirtualną i upewnij się, że sterownik jest zainstalowany w systemie Windows.

1. W przeglądarce internetowej otwórz [witrynę](https://portal.azure.com) Azure portal i zaloguj się za pomocą konta platformy Azure.
2. W polu zawierającym tekst *Wyszukiwanie zasobów* u góry witryny Azure portal wpisz *myVm*. Gdy **myVm** pojawi się w wynikach wyszukiwania, kliknij go. Jeśli **tworzenie** jest widoczne pod przyciskiem **Połącz,** platforma Azure nie została jeszcze zakończona tworzenie maszyny Wirtualnej. Kliknij **przycisk Połącz** w lewym górnym rogu przeglądu dopiero po tym, jak nie będzie już widoczne **tworzenie** pod przyciskiem **Połącz.**
3. Wprowadź nazwę użytkownika i hasło wprowadzone w [obszarze Utwórz maszynę wirtualną](#create-the-virtual-machine). Jeśli nigdy nie masz połączenia z maszyną wirtualną systemu Windows na platformie Azure, zobacz [Łączenie się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kliknij prawym przyciskiem myszy przycisk Start systemu Windows i kliknij polecenie **Menedżer urządzeń**. Rozwiń węzeł **Karty sieciowe.** Upewnij się, że jest wyświetlany **wirtualny adapter Ethernet funkcji Mellanox ConnectX-3,** jak pokazano na poniższym rysunku:

    ![Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Przyspieszona sieć jest teraz włączona dla maszyny Wirtualnej.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz przyspieszoną sieć na istniejących maszynach wirtualnych
Jeśli utworzono maszynę wirtualną bez przyspieszonej sieci, można włączyć tę funkcję na istniejącej maszynie wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszoną sieć, spełniając następujące wymagania wstępne, które są również opisane powyżej:

* Maszyna wirtualna musi mieć obsługiwany rozmiar dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwany obraz Galerii Platformy Azure (i wersja jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS muszą zostać zatrzymane/cofnięto alokacji przed włączeniem przyspieszonej sieci na dowolnej karcie sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Poszczególne maszyny wirtualne & maszyn wirtualnych w zestawie dostępności
Najpierw zatrzymać/zdezawukuj przydzielenie maszyny wirtualnej lub, jeśli zestaw dostępności, wszystkie maszyny wirtualne w zestawie:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Ważne, należy pamiętać, że jeśli maszyna wirtualna została utworzona indywidualnie, bez zestawu dostępności, wystarczy zatrzymać/zdelocytować poszczególne maszyny Wirtualnej, aby włączyć przyspieszoną sieć.  Jeśli maszyna wirtualna została utworzona z zestawem dostępności, wszystkie maszyny wirtualne zawarte w zestawie dostępności będą musiały zostać zatrzymane/cofnięte przed włączeniem przyspieszonej sieci na dowolnej z kart sieciowych. 

Po zatrzymaniu włącz przyspieszoną sieć na karcie sieciowej maszyny Wirtualnej:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Uruchom ponownie maszynę wirtualną lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że przyspieszona sieć jest włączona:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
VMSS jest nieco inna, ale następuje ten sam przepływ pracy.  Najpierw zatrzymaj maszyny wirtualne:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po zatrzymaniu maszyn wirtualnych zaktualizuj właściwość Przyspieszona sieć w interfejsie sieciowym:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Należy pamiętać, że VMSS ma uaktualnienia maszyn wirtualnych, które stosują aktualizacje przy użyciu trzech różnych ustawień, automatyczne, toczenia i ręczne.  W tych instrukcjach zasady są ustawione na automatyczne, dzięki czemu VMSS będzie odebrać zmiany natychmiast po ponownym uruchomieniu.  Aby ustawić go na automatyczny, tak aby zmiany były natychmiast pobierane:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Na koniec uruchom ponownie usługę VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po ponownym uruchomieniu poczekaj na zakończenie uaktualnień, ale po zakończeniu, VF pojawi się wewnątrz maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwanego systemu operacyjnego i rozmiaru maszyny Wirtualnej)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych za pomocą przyspieszonej sieci

Maszyny wirtualne z włączoną obsługą przyspieszonej sieci można zwymiarować tylko na maszyny wirtualne obsługujące przyspieszoną sieć.  

Nie można zmienić rozmiaru maszyny Wirtualnej z włączoną obsługą sieci maszyny Wirtualnej, które nie obsługuje przyspieszonej sieci przy użyciu operacji zmieniania rozmiaru.  Zamiast tego, aby zmienić rozmiar jednej z tych maszyn wirtualnych:

* Zatrzymaj/zdajlokuj maszynę wirtualną lub jeśli w zestawie dostępności/VMSS zatrzymaj/zdeokuj wszystkie maszyny wirtualne w zestawie/VMSS.
* Przyspieszona sieć musi być wyłączona na karcie sieciowej maszyny Wirtualnej lub jeśli w zestawie dostępności/VMSS wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu przyspieszonej sieci zestaw VM/availability/VMSS można przenieść do nowego rozmiaru, który nie obsługuje przyspieszonej sieci i ponownego uruchomienia.
