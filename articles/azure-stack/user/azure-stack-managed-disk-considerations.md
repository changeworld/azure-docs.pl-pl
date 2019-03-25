---
title: Różnice i zagadnienia dotyczące dysków zarządzanych i zarządzane obrazy w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Poznaj różnice i zagadnienia podczas pracy z dysków zarządzanych i obrazy zarządzanych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: c1975c885efc0a2a22b2ab478f8bc9afbcc8bce3
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400363"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Dyski zarządzane w usłudze Azure Stack: różnice i zagadnienia

Ten artykuł zawiera podsumowanie znane różnice między [dysków zarządzanych w usłudze Azure Stack](azure-stack-manage-vm-disks.md) i [dysków zarządzanych dla platformy Azure](../../virtual-machines/windows/managed-disks-overview.md). Aby dowiedzieć się więcej o różnicach wysokiego poziomu usługi Azure Stack i platformą Azure, zobacz [kluczowe zagadnienia](azure-stack-considerations.md) artykułu.

Usługa Managed disks upraszcza zarządzanie dyskami dla maszyn wirtualnych IaaS dzięki zarządzaniu [kont magazynu](../azure-stack-manage-storage-accounts.md) skojarzone z dyskami maszyn wirtualnych.

> [!Note]  
> Dyski zarządzane w usłudze Azure Stack jest dostępna aktualizacja 1808. Włączono domyślnie podczas tworzenia maszyn wirtualnych przy użyciu portalu Azure Stack, począwszy od aktualizacji 1811.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Ściągawka: różnice dysku zarządzanego

| Cecha | Azure (globalna) | Azure Stack |
| --- | --- | --- |
|Szyfrowanie danych magazynowanych |Szyfrowanie usługi Azure Storage (SSE), usługa Azure Disk Encryption (ADE)     |Funkcja BitLocker 128-bitowego szyfrowania AES      |
|Image (Obraz)          | Obsługa zarządzany obraz niestandardowy |Obsługiwane|
|Opcje kopii zapasowych |Obsługa usługi Azure Backup |Nie jest jeszcze obsługiwany |
|Opcje odzyskiwania po awarii |Obsługa usługi Azure Site Recovery |Nie jest jeszcze obsługiwany|
|Typy dysków     |Dysk SSD w warstwie Premium SSD w warstwie standardowa (wersja zapoznawcza) i standardowych dysków Twardych |Premium SSD, HDD standardowe |
|Dyski w warstwie Premium  |W pełni obsługiwane |Mogą być udostępniane, ale bez ograniczeń wydajności lub gwarancji  |
|Dyski Premium operacje We/Wy  |Zależy od rozmiaru dysku  |2300 operacje We/Wy na dysk |
|Przepływność dysków Premium |Zależy od rozmiaru dysku |145 MB na sekundę na dysk |
|Rozmiar dysku  |Azure Premium Disk: P4 (32 GiB) do P80 (32 TiB)<br>Dysk SSD w warstwie standardowa platformy Azure: E10 (128 GiB) do E80 (32 TiB)<br>Dysk Azure standardowych dysków Twardych: S4 (32 GiB) do S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Kopiowanie migawki dysków|Tworzenie migawki Azure dołączone do uruchomionej maszyny Wirtualnej obsługiwane dyski zarządzane|Nie jest jeszcze obsługiwany |
|Wydajność dysków analitycznych |Agregują metryki i metryki na dysk obsługiwane |Nie jest jeszcze obsługiwany |
|Migracja      |Zapewnia narzędzia do migracji z istniejącego niezarządzanych Menedżera zasobów maszyn wirtualnych platformy Azure bez konieczności ponowne utworzenie maszyny Wirtualnej  |Nie jest jeszcze obsługiwany |

> [!NOTE]  
> Usługa Managed disks operacje We/Wy i Przepływność w usłudze Azure Stack jest liczbą limit zamiast elastycznie numer, który może wpływać na sprzęt i obciążeń działających w usłudze Azure Stack.

## <a name="metrics"></a>Metryki

Istnieją różnice za pomocą metryk usługi storage:

- Dzięki usłudze Azure Stack dane transakcji w metryk magazynowania nie odróżniają przepustowość sieci wewnętrznych lub zewnętrznych.
- Transakcji danych platformy Azure Stack w metryk usługi storage nie ma na maszynie wirtualnej dostęp do dysków zainstalowanych.

## <a name="api-versions"></a>Wersje interfejsu API

Usługa Azure Stack zarządzane dyski następujący interfejs API obsługuje wersje:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konwertowanie do usługi managed disks

Poniższy skrypt umożliwia konwertowanie obecnie aprowizowanej maszyny Wirtualnej z dysków niezarządzanych do dysków zarządzanych. Zastąp symbole zastępcze własnymi wartościami:

```powershell
$subscriptionId = 'subid'

# The name of your resource group
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk is located.
# The location should be the same as the location of the storage account in which VHD file is stored.
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Zarządzane obrazów

Platforma Azure obsługuje stosu *zarządzanych obrazów*, umożliwiające dysku należy utworzyć obiekt obrazu zarządzanego uogólnionej maszyny wirtualnej (zarówno niezarządzane i zarządzane), można tworzyć tylko zarządzane maszyny wirtualne w przyszłości. Obrazy zarządzanych umożliwiają dwa następujące scenariusze:

- Zostały uogólnione niezarządzanych maszyn wirtualnych i chcesz używać dysków zarządzanych w przyszłości.
- Masz zarządzanego uogólnionej maszyny Wirtualnej i chcesz utworzyć wiele, podobne zarządzanych maszyn wirtualnych.

### <a name="step-1-generalize-the-vm"></a>Krok 1: Uogólnianie maszyny Wirtualnej

Windows, postępuj zgodnie z [uogólnianie maszyny Wirtualnej Windows za pomocą programu Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) sekcji. Dla systemu Linux, postępuj zgodnie z kroku 1 [tutaj](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Pamiętaj uogólnić maszyny Wirtualnej. Tworzenie maszyny Wirtualnej z obrazu, który nie został prawidłowo uogólniony doprowadzi do **VMProvisioningTimeout** błędu.

### <a name="step-2-create-the-managed-image"></a>Krok 2: Tworzenie obrazu zarządzanego

Można użyć witryny portal, programu PowerShell lub interfejsu wiersza polecenia, aby utworzyć obrazu zarządzanego. Postępuj zgodnie z instrukcjami w artykule Azure [tutaj](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Krok 3: Wybierz przypadek użycia

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Przypadek 1: Migrowanie maszyn wirtualnych niezarządzanych do dysków zarządzanych

Upewnij się, że poprawnie uogólnianie maszyny Wirtualnej przed wykonaniem tego kroku. Po Generalizacja można już używać tej maszyny. Tworzenie maszyny Wirtualnej z obrazu, który nie został prawidłowo uogólniony doprowadzi do **VMProvisioningTimeout** błędu.

Postępuj zgodnie z instrukcjami [tutaj](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) do tworzenie obrazu zarządzanego na podstawie uogólnionego wirtualnego dysku twardego w ramach konta magazynu. Można użyć tego obrazu idąc dalej, aby tworzyć zarządzane maszyny wirtualne.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Przypadek 2: Tworzenie zarządzanej maszyny Wirtualnej na podstawie obrazu zarządzanego przy użyciu programu Powershell

Po utworzeniu obrazu z istniejącego zarządzanego dysku maszyny Wirtualnej przy użyciu skryptu [tutaj](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell), następujący skrypt przykładowy tworzy podobne maszyny Wirtualnej systemu Linux na podstawie istniejącego obiektu obrazu:

Moduł usługi Azure Stack PowerShell 1.7.0 lub nowszej: postępuj zgodnie z instrukcjami [tutaj](../../virtual-machines/windows/create-vm-generalized-managed.md).

Moduł usługi Azure Stack PowerShell 1.6.0 lub wcześniej:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Portal umożliwia również tworzenie maszyny Wirtualnej na podstawie obrazu zarządzanego. Aby uzyskać więcej informacji, zobacz platformy Azure zarządzanego obrazu artykuły [utworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](../../virtual-machines/windows/capture-image-resource.md) i [Utwórz Maszynę wirtualną z obrazu zarządzanego](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Konfigurowanie

Po zastosowaniu 1808 aktualizacji lub nowszej, należy wykonać następującą konfigurację, przed rozpoczęciem korzystania z dysków zarządzanych:

- Jeśli subskrypcja została utworzona przed aktualizacją 1808, wykonaj poniższe kroki, aby zaktualizować subskrypcji. W przeciwnym razie wdrażanie maszyn wirtualnych w ramach tej subskrypcji może zakończyć się komunikat o błędzie "Błąd wewnętrzny w Menedżerze dysków."
   1. W portalu dzierżawcy, przejdź do **subskrypcje** i Znajdź subskrypcji. Kliknij przycisk **dostawców zasobów**, następnie kliknij przycisk **Microsoft.Compute**, a następnie kliknij przycisk **ponownie zarejestrować**.
   2. W ramach tej samej subskrypcji, przejdź do **kontrola dostępu (IAM)** i upewnij się, że **usługi Azure Stack — dysk zarządzany** znajduje się na liście.
- Jeśli używasz środowiska z wieloma dzierżawami, poproś o swojej chmury operatora (która może znajdować się w Twojej organizacji lub od dostawcy usług) Zmień konfigurację wszystkich katalogów gościa czynności opisane w [w tym artykule](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). W przeciwnym razie wdrażanie maszyn wirtualnych w ramach subskrypcji skojarzonych z nim gościa może zakończyć się komunikat o błędzie "Błąd wewnętrzny w Menedżerze dysków."

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o maszynach wirtualnych usługi Azure Stack](azure-stack-compute-overview.md)
