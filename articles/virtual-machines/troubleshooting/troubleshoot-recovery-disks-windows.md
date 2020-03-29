---
title: Korzystanie z maszyny Wirtualnej systemu Windows w programie Azure PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną systemu Windows na platformie Azure, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374136"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Rozwiązywanie problemów z maszyną wirtualną systemu Windows, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu programu Azure PowerShell
Jeśli maszyna wirtualna systemu Windows (VM) na platformie Azure napotka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na samym dysku. Typowym przykładem może być nieudana aktualizacja aplikacji, która uniemożliwia maszynie wirtualnej pomyślne uruchomienie. W tym artykule opisano, jak używać programu Azure PowerShell do łączenia dysku z inną maszyną wirtualną systemu Windows w celu naprawienia błędów, a następnie naprawy oryginalnej maszyny Wirtualnej. 

> [!Important]
> Skrypty w tym artykule dotyczą tylko maszyn wirtualnych korzystających z [dysku zarządzanego](../windows/managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Możemy teraz użyć programu Azure PowerShell, aby zmienić dysk systemu operacyjnego dla maszyny Wirtualnej. Nie musimy już usuwać i odtwarzać maszyny Wirtualnej.

Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj maszynę wirtualną, na które dotyczy problem.
2. Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej.
3. Utwórz dysk z migawki dysku systemu operacyjnego.
4. Dołącz dysk jako dysk danych do odzyskiwania maszyny Wirtualnej.
5. Połącz się z maszyną wirtualną odzyskiwania. Edytuj pliki lub uruchom dowolne narzędzia, aby rozwiązać problemy na skopiowanym dysku systemu operacyjnego.
6. Odmontuj i odłącz dysk od odzyskiwania maszyny Wirtualnej.
7. Zmień dysk systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem.

Za pomocą poleceń naprawy maszyny Wirtualnej można zautomatyzować kroki 1, 2, 3, 4, 6 i 7. Aby uzyskać więcej dokumentacji i instrukcji, zobacz [Naprawianie maszyny Wirtualnej systemu Windows przy użyciu poleceń naprawy maszyny wirtualnej platformy Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Upewnij się, że masz zainstalowaną i zalogowano najnowszą usługę [Azure PowerShell](/powershell/azure/overview) do subskrypcji:

```powershell
Connect-AzAccount
```

W poniższych przykładach zastąp nazwy parametrów własnymi wartościami. 

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Możesz wyświetlić zrzut ekranu maszyny Wirtualnej na platformie Azure, aby pomóc w rozwiązywaniu problemów z rozruchem. Ten zrzut ekranu może pomóc w określeniu, dlaczego maszyna wirtualna nie uruchamia się. Poniższy przykład pobiera zrzut ekranu z `myVM` maszyny Wirtualnej `myResourceGroup`systemu Windows o nazwie w grupie zasobów o nazwie:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Przejrzyj zrzut ekranu, aby ustalić, dlaczego maszyna wirtualna nie uruchamia się. Zanotuj wszystkie określone komunikaty o błędach lub kody błędów.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład zatrzymuje maszynę wirtualną o nazwie `myVM` z grupy zasobów o nazwie: `myResourceGroup`

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Poczekaj, aż maszyna wirtualna zakończy usuwanie przed przetworzeniem do następnego kroku.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Tworzenie migawki z dysku systemu operacyjnego maszyny Wirtualnej

Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z dysku systemu operacyjnego maszyny wirtualnej o nazwie "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Migawka jest pełną, tylko do odczytu kopię dysku VHD. Nie można dołączyć do maszyny Wirtualnej. W następnym kroku utworzymy dysk z tej migawki.

## <a name="create-a-disk-from-the-snapshot"></a>Tworzenie dysku na podstawie migawki

Ten skrypt tworzy dysk `newOSDisk` zarządzany o `mysnapshot`nazwie z migawki o nazwie .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Teraz masz kopię oryginalnego dysku systemu operacyjnego. Ten dysk można zainstalować na innej maszynie wirtualnej systemu Windows w celu rozwiązywania problemów.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Dołączanie dysku do innej maszyny Wirtualnej systemu Windows w celu rozwiązywania problemów

Teraz dołączamy kopię oryginalnego dysku systemu operacyjnego do maszyny Wirtualnej jako dysk danych. Ten proces umożliwia poprawianie błędów konfiguracji lub przeglądanie dodatkowych plików dziennika aplikacji lub systemu na dysku. Poniższy przykład dołącza dysk `newOSDisk` o nazwie `RecoveryVM`do maszyny Wirtualnej o nazwie .

> [!NOTE]
> Aby dołączyć dysk, kopia oryginalnego dysku systemu operacyjnego i maszyny wirtualnej odzyskiwania musi znajdować się w tej samej lokalizacji.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Połącz się z maszyną wirtualną odzyskiwania i rozwiązywanie problemów na podłączonym dysku

1. RDP do maszyny Wirtualnej odzyskiwania przy użyciu odpowiednich poświadczeń. Poniższy przykład pobiera plik połączenia RDP dla `RecoveryVM` maszyny Wirtualnej `myResourceGroup`o nazwie w `C:\Users\ops\Documents`grupie zasobów o nazwie i pobiera go do "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Dysk danych powinien być automatycznie wykrywany i dołączany. Wyświetl listę dołączonych woluminów, aby określić literę dysku w następujący sposób:

    ```powershell
    Get-Disk
    ```

    Poniższy przykładowy wynik przedstawia dysk podłączony do dysku **2**. (Można również `Get-Volume` użyć, aby wyświetlić literę dysku):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Po zamontowaniu kopii oryginalnego dysku systemu operacyjnego można wykonać wszelkie czynności konserwacyjne i rozwiązywanie problemów zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-os-disk"></a>Odmontuj i odłącz oryginalny dysk systemu operacyjnego
Po usunięciu błędów należy odinstalować i odłączyć istniejący dysk od odzyskiwania maszyny Wirtualnej. Nie można używać dysku z żadną inną maszyną wirtualną, dopóki nie zostanie zwolniona dzierżawa dołączająca dysk do maszyny wirtualnej odzyskiwania.

1. Z poziomu sesji RDP odinstaluj dysk danych na maszynie wirtualnej odzyskiwania. Potrzebny jest numer dysku `Get-Disk` z poprzedniego polecenia cmdlet. Następnie użyj, `Set-Disk` aby ustawić dysk jako offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Upewnij się, że dysk `Get-Disk` jest teraz ustawiony jako w trybie offline przy użyciu ponownie. Poniższy przykładowy wynik pokazuje, że dysk jest teraz ustawiony jako w trybie offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Wyjdź z sesji RDP. Z sesji programu Azure PowerShell `newOSDisk` usuń dysk o nazwie z maszyny wirtualnej o nazwie "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmienianie dysku systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem

Za pomocą programu Azure PowerShell można zamienić dyski systemu operacyjnego. Nie trzeba usuwać i ponownie utworzyć maszynę wirtualną.

W tym przykładzie `myVM` zatrzymuje maszynę wirtualną o nazwie i przypisuje dysk o nazwie `newOSDisk` jako nowy dysk systemu operacyjnego. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Weryfikowanie i włączanie diagnostyki rozruchu

Poniższy przykład włącza rozszerzenie diagnostyczne `myVMDeployed` na maszynie `myResourceGroup`Wirtualnej o nazwie w grupie zasobów o nazwie:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami protokołu RDP z maszyną wirtualną platformy Azure.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać więcej informacji na temat korzystania z Menedżera zasobów, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).
