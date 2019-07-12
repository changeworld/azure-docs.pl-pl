---
title: Użyj Windows, rozwiązywanie problemów z maszyny Wirtualnej przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyn wirtualnych Windows na platformie Azure, łącząc dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 94abf9c8621e842605a4fab521fa4df853e1fb4a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709296"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Rozwiązywanie problemów z maszyny Wirtualnej z systemem Windows, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu programu Azure PowerShell
Windows maszyny wirtualnej (VM) na platformie Azure napotka błąd rozruchu lub dysk, może być konieczne wykonanie kroków rozwiązywania problemów na dysku. Typowym przykładem może być Niepowodzenie aktualizacji aplikacji, która uniemożliwia maszynie Wirtualnej możliwość wykonania rozruchu pomyślnie. W tym artykule szczegółowo przedstawiono sposób podłącz dysk do innej Windows maszyny Wirtualnej, aby naprawić wszystkie błędy, a następnie napraw oryginalną maszynę Wirtualną za pomocą programu Azure PowerShell. 

> [!Important]
> Skrypty w tym artykule dotyczą tylko maszyn wirtualnych, które używają [dysku zarządzanego](../windows/managed-disks-overview.md). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Teraz możemy użyć programu Azure PowerShell do zmiany dysku systemu operacyjnego dla maszyny Wirtualnej. Firma Microsoft nie będzie konieczne usunięcie i ponowne utworzenie maszyny Wirtualnej.

Proces rozwiązywania problemów jest następujący:

1. Zatrzymywanie dotyczy maszyny Wirtualnej.
2. Tworzenie migawki z dysku systemu operacyjnego maszyny wirtualnej.
3. Tworzenie dysku na podstawie migawki dysku systemu operacyjnego.
4. Dołącz dysk jako dysk danych do maszyny Wirtualnej odzyskiwania.
5. Podłącz do maszyny Wirtualnej odzyskiwania. Edytuj pliki lub uruchom wszystkie narzędzia, aby naprawić problemy na skopiowanego dysku systemu operacyjnego.
6. Odinstaluj i Odłącz dysk od maszyny Wirtualnej odzyskiwania.
7. Objęte maszyny wirtualnej, należy zmienić dysk systemu operacyjnego.

Skrypty do odzyskiwania maszyny Wirtualnej można użyć do automatyzowania czynności 1, 2, 3, 4, 6 i 7. Aby uzyskać więcej dokumentacji i instrukcje, zobacz [skrypty odzyskiwania maszyny Wirtualnej dla maszyny Wirtualnej usługi Resource Manager](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager).

Upewnij się, że masz [najnowszą wersję programu Azure PowerShell](/powershell/azure/overview) zainstalowane i zalogować się do Twojej subskrypcji:

```powershell
Connect-AzAccount
```

W poniższych przykładach należy zastąpić własnymi wartościami nazw parametrów. 

## <a name="determine-boot-issues"></a>Określenia problemów rozruchu
Możesz wyświetlić zrzut ekranu maszyny wirtualnej na platformie Azure, aby ułatwić rozwiązywanie problemów rozruchu. Ten zrzut ekranu może pomóc w ustaleniu, dlaczego maszyny Wirtualnej nie można uruchomić. Poniższy przykład pobiera zrzut ekranu z maszyny Wirtualnej Windows o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Przejrzyj zrzut ekranu, aby ustalić, dlaczego maszyny Wirtualnej nie może uruchomić. Należy pamiętać, wszystkie określone komunikaty o błędach lub kody błędów, pod warunkiem.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład zatrzymuje maszynę Wirtualną o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Poczekaj, aż maszyny Wirtualnej zostało zakończone, usuwanie, przed rozpoczęciem przetwarzania do następnego kroku.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Tworzenie migawki z dysku systemu operacyjnego maszyny wirtualnej

Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z systemu operacyjnego dysk maszyny Wirtualnej o nazwie "myVM". 

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

Migawka jest pełna, tylko do odczytu kopię dysku VHD. Nie można dołączyć do maszyny Wirtualnej. W następnym kroku zostanie utworzony dysk z tą migawką.

## <a name="create-a-disk-from-the-snapshot"></a>Tworzenie dysku z migawki

Ten skrypt tworzy dysk zarządzany o nazwie `newOSDisk` z migawki o nazwie `mysnapshot`.  

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

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Masz teraz kopię dysku systemu operacyjnego, oryginalnym. Ten dysk do innej maszyny Wirtualnej Windows można zainstalować na potrzeby rozwiązywania problemów.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Dołącz dysk do innej maszyny Wirtualnej Windows do rozwiązywania problemów

Teraz możemy dołączyć kopię oryginalny dysk systemu operacyjnego do maszyny Wirtualnej jako dysk danych. Ten proces pozwala naprawić błędy konfiguracji lub przejrzyj dodatkową aplikację i plikach dziennika systemowego na dysku. Poniższy przykład dołącza dysk o nazwie `newOSDisk` do maszyny Wirtualnej o nazwie `RecoveryVM`.

> [!NOTE]
> Aby dołączyć dysku, kopię oryginalny dysk systemu operacyjnego i odzyskiwania maszyn wirtualnych musi być w tej samej lokalizacji.

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

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Nawiązywanie połączenia maszyny Wirtualnej odzyskiwania i rozwiązywanie problemów na dołączonym dysku

1. Protokół RDP do odzyskiwania maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Poniższy przykład pobiera plik połączenia RDP dla maszyny Wirtualnej o nazwie `RecoveryVM` w grupie zasobów o nazwie `myResourceGroup`i pobranie go do `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Dysk z danymi powinny automatycznie wykryte i dołączony. Wyświetl listę dołączone woluminy w celu określenia litery dysku w następujący sposób:

    ```powershell
    Get-Disk
    ```

    Następujące przykładowe dane wyjściowe zawiera dysk podłączony dysk **2**. (Możesz również użyć `Get-Volume` do wyświetlania literę dysku):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Po kopię oryginalny dysk systemu operacyjnego jest zainstalowany, można wykonać konserwacji oraz procedurę rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-os-disk"></a>Odinstalowywanie i odłączanie oryginalnego dysku systemu operacyjnego
Gdy błędy są rozwiązywane, odinstaluj i Odłącz istniejący dysk od maszyny Wirtualnej odzyskiwania. Nie można użyć dysku z innej maszyny Wirtualnej, dopóki dzierżawa, przyłączanie dysku do maszyny Wirtualnej odzyskiwania zostanie zwolniona.

1. Z w ramach sesji protokołu RDP, odinstaluj dysk z danymi na maszynę Wirtualną odzyskiwania. Potrzebujesz numer dysku z poprzedniego `Get-Disk` polecenia cmdlet. Następnie należy użyć `Set-Disk` można ustawić dysk w trybie offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Upewnij się, dysku są teraz ustawione w trybie offline, używając `Get-Disk` ponownie. Następujące przykładowe dane wyjściowe pokazuje, że dysk jest teraz skonfigurowane w trybie offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Zamknij sesję RDP. Z poziomu sesji programu Azure PowerShell, Usuń dysk o nazwie `newOSDisk` z maszyny Wirtualnej o nazwie "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmień dysk systemu operacyjnego dla maszyny Wirtualnej z zainfekowanego

Konieczność wymiany dysków systemu operacyjnego, można użyć programu Azure PowerShell. Nie masz usunięcie i ponowne utworzenie maszyny Wirtualnej.

W tym przykładzie zatrzymuje maszynę Wirtualną o nazwie `myVM` i przypisuje dysku o nazwie `newOSDisk` jako nowy dysk systemu operacyjnego. 

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

## <a name="verify-and-enable-boot-diagnostics"></a>Sprawdź i Włącz diagnostykę rozruchu

Poniższy przykład umożliwia rozszerzenie diagnostyki na maszynie Wirtualnej o nazwie `myVMDeployed` w grupie zasobów o nazwie `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [połączenia RDP Rozwiązywanie problemów z Maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać więcej informacji na temat przy użyciu usługi Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
