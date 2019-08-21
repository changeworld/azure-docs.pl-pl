---
title: Korzystanie z maszyny wirtualnej Rozwiązywanie problemów z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną z systemem Linux, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu interfejsu wiersza polecenia
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641065"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu interfejsu wiersza polecenia Azure
Jeśli maszyna wirtualna z systemem Linux napotyka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na wirtualnym dysku twardym. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab` programie, który uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano sposób łączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure w celu usunięcia błędów, a następnie ponownego utworzenia oryginalnej maszyny wirtualnej. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
1. Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej.
1. Utwórz dysk na podstawie migawki dysku systemu operacyjnego.
1. Dołącz i zainstaluj nowy dysk systemu operacyjnego na innej maszynie wirtualnej z systemem Linux w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy na nowym dysku systemu operacyjnego.
1. Odinstaluj i odłącz nowy dysk systemu operacyjnego z maszyny wirtualnej rozwiązywania problemów.
1. Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy.

Aby wykonać te kroki rozwiązywania problemów, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się do konta platformy Azure przy użyciu polecenia [AZ login](/cli/azure/reference-index).

> [!Important]
> Skrypty w tym artykule mają zastosowanie tylko do maszyn wirtualnych, które korzystają z [dysku zarządzanego](../linux/managed-disks-overview.md). 

W poniższych przykładach Zastąp nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmują `myResourceGroup` i `myVM`.

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Sprawdź dane wyjściowe seryjnej, aby określić, dlaczego nie można prawidłowo uruchomić maszyny wirtualnej. Typowym przykładem jest nieprawidłowy wpis w `/etc/fstab`lub źródłowy wirtualny dysk twardy, który jest usuwany lub przenoszony.

Pobierz dzienniki rozruchowe za pomocą [AZ VM Boot-Diagnostics Get-Boot-Log](/cli/azure/vm/boot-diagnostics). Poniższy przykład pobiera dane wyjściowe seryjne z maszyny wirtualnej o `myVM` nazwie w grupie zasobów o `myResourceGroup`nazwie:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Przejrzyj dane wyjściowe seryjnej, aby określić dlaczego rozruch maszyny wirtualnej kończy się niepowodzeniem. Jeśli dane wyjściowe seryjnej nie zapewniają żadnych wskazań, może być konieczne przejrzenie plików dziennika `/var/log` w programie po powiązaniu wirtualnego dysku twardego z maszyną wirtualną rozwiązywania problemów.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład powoduje zatrzymanie maszyny wirtualnej o `myVM` nazwie z grupy zasobów o `myResourceGroup`nazwie:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Przed przetworzeniem do następnego kroku poczekaj na zakończenie usuwania maszyny wirtualnej.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej

Migawka to pełna kopia tylko do odczytu dysku VHD. Nie można go dołączyć do maszyny wirtualnej. W następnym kroku utworzymy dysk na podstawie tej migawki. Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z dysku systemu operacyjnego maszyny wirtualnej o nazwie "myVM". 

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
## <a name="create-a-disk-from-the-snapshot"></a>Utwórz dysk na podstawie migawki

Ten skrypt tworzy dysk zarządzany o nazwie `newOSDisk` z migawki o nazwie. `mysnapshot`  

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
Teraz masz kopię oryginalnego dysku systemu operacyjnego. Nowy dysk można zainstalować na innej maszynie wirtualnej z systemem Windows w celu rozwiązywania problemów.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Dołącz nowy wirtualny dysk twardy do innej maszyny wirtualnej
W następnych kilku krokach do rozwiązywania problemów służy inna maszyna wirtualna. Podłącz dysk do tej maszyny wirtualnej rozwiązywania problemów, aby przeglądać i edytować zawartość tego dysku. Ten proces umożliwia poprawienie wszystkich błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu, na przykład. Wybierz lub Utwórz inną maszynę wirtualną do użycia na potrzeby rozwiązywania problemów.

Dołącz istniejący wirtualny dysk twardy przy użyciu [AZ VM Unmanaged-Disk Attach](/cli/azure/vm/unmanaged-disk). Po dołączeniu istniejącego wirtualnego dysku twardego Określ identyfikator URI na dysku uzyskanym w poprzednim `az vm show` poleceniu. Poniższy przykład dołącza istniejący wirtualny dysk twardy do rozwiązywania problemów z maszyną wirtualną `myVMRecovery` o nazwie w grupie zasobów `myResourceGroup`o nazwie:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>Instalowanie dołączonego dysku z danymi

> [!NOTE]
> W poniższych przykładach szczegółowo opisano kroki wymagane na maszynie wirtualnej Ubuntu. Jeśli używasz innego dystrybucji systemu Linux, takiego jak Red Hat Enterprise Linux lub SUSE, lokalizacje i `mount` polecenia w pliku dziennika mogą być nieco inne. Zapoznaj się z dokumentacją dotyczącą określonych dystrybucji, aby uzyskać odpowiednie zmiany w poleceniach.

1. Użyj odpowiednich poświadczeń protokołu SSH do rozwiązywania problemów z maszyną wirtualną. Jeśli ten dysk jest pierwszym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, prawdopodobnie dysk jest `/dev/sdc`podłączony do programu. Użyj `dmseg` , aby wyświetlić dołączone dyski:

    ```bash
    dmesg | grep SCSI
    ```

    Dane wyjściowe są podobne do poniższego przykładu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    W poprzednim przykładzie dysk systemu operacyjnego jest na `/dev/sda` dysku, a dysk tymczasowy podany dla każdej maszyny wirtualnej `/dev/sdb`ma wartość. Jeśli masz wiele dysków danych, powinny one znajdować się `/dev/sdd`w `/dev/sde`, i tak dalej.

2. Utwórz katalog, aby zainstalować istniejący wirtualny dysk twardy. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącym wirtualnym dysku twardym, zainstaluj wymaganą partycję. W poniższym przykładzie jest instalowana Pierwsza partycja podstawowa w `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest zainstalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu uniwersalnego identyfikatora UUID dla wirtualnego dysku twardego. W przypadku tego krótkiego scenariusza rozwiązywania problemów zainstalowanie wirtualnego dysku twardego przy użyciu identyfikatora UUID nie jest konieczne. Jednak w normalnych warunkach Edycja `/etc/fstab` w celu zainstalowania wirtualnych dysków twardych przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować niepowodzenie rozruchu maszyny wirtualnej.


## <a name="fix-issues-on-the-new-os-disk"></a>Rozwiązywanie problemów z nowym dyskiem systemu operacyjnego
Po zainstalowaniu istniejącego wirtualnego dysku twardego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-the-new-os-disk"></a>Odinstalowywanie i odłączanie nowego dysku systemu operacyjnego
Po rozwiązaniu problemów należy odinstalować i odłączyć istniejący wirtualny dysk twardy z maszyny wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki dzierżawa nie dołączy wirtualnego dysku twardego do maszyny wirtualnej rozwiązywania problemów.

1. Odinstaluj istniejący wirtualny dysk twardy z sesji SSH do rozwiązywania problemów z maszyną wirtualną. Najpierw Zmień katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Odinstaluj już istniejący wirtualny dysk twardy. Poniższy przykład Odinstalowuje urządzenie w `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy od maszyny wirtualnej. Zamknij sesję SSH z maszyną wirtualną rozwiązywania problemów. Wyświetl listę dołączonych dysków danych na maszynie wirtualnej rozwiązywania problemów przy użyciu [AZ VM Unmanaged-Disk list](/cli/azure/vm/unmanaged-disk). Poniższy przykład zawiera listę dysków danych podłączonych do maszyny wirtualnej o `myVMRecovery` nazwie w grupie zasobów o `myResourceGroup`nazwie:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Zanotuj nazwę istniejącego wirtualnego dysku twardego. Na przykład nazwa dysku o identyfikatorze URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** to **myVHD**. 

    Odłącz dysk danych z maszyny wirtualnej [AZ VM Unmanaged-Disk](/cli/azure/vm/unmanaged-disk)odłączenie. Poniższy przykład powoduje odłączenie dysku o `myVHD` nazwie z maszyny wirtualnej `myVMRecovery` o nazwie `myResourceGroup` w grupie zasobów:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmiana dysku systemu operacyjnego dla maszyny wirtualnej, której to dotyczy

Aby zamienić dyski systemu operacyjnego, można użyć Azure PowerShell. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej.

Ten przykład powoduje zatrzymanie maszyny wirtualnej `myVM` o nazwie i przypisanie dysku `newOSDisk` o nazwie jako nowego dysku systemu operacyjnego. 

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

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure](troubleshoot-ssh-connection.md). Problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć w temacie Rozwiązywanie problemów z [łącznością aplikacji na maszynie wirtualnej z systemem Linux](troubleshoot-app-connection.md).

