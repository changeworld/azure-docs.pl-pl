---
title: Używanie maszyny Wirtualnej z systemem Linux do rozwiązywania problemów z interfejsem wiersza polecenia platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną systemu Linux, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73620559"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania za pomocą interfejsu wiersza polecenia platformy Azure
Jeśli maszyna wirtualna systemu Linux (VM) napotka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być `/etc/fstab` nieprawidłowy wpis, który uniemożliwia maszynie wirtualnej pomyślne uruchomienie. W tym artykule opisano, jak używać interfejsu wiersza polecenia platformy Azure do połączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Linux w celu naprawienia błędów, a następnie ponownego utworzenia oryginalnej maszyny Wirtualnej. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj maszynę wirtualną, na które dotyczy problem.
1. Zrób migawkę z dysku systemu operacyjnego maszyny Wirtualnej.
1. Utwórz dysk z migawki dysku systemu operacyjnego.
1. Podłącz i zamontuj nowy dysk systemu operacyjnego do innej maszyny Wirtualnej z systemem Linux w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolne narzędzia, aby rozwiązać problemy na nowym dysku systemu operacyjnego.
1. Odmontuj i odłącz nowy dysk systemu operacyjnego od maszyny wirtualnej do rozwiązywania problemów.
1. Zmień dysk systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem.

Aby wykonać te kroki rozwiązywania problemów, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

> [!Important]
> Skrypty w tym artykule dotyczą tylko maszyn wirtualnych korzystających z [dysku zarządzanego](../linux/managed-disks-overview.md). 

W poniższych przykładach zastąp nazwy parametrów własnymi wartościami, takimi jak `myResourceGroup` i `myVM`.

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Sprawdź dane wyjściowe szeregowe, aby ustalić, dlaczego maszyna wirtualna nie jest w stanie poprawnie uruchomić. Typowym przykładem jest `/etc/fstab`nieprawidłowy wpis w programie lub podstawowy wirtualny dysk twardy, który jest usuwany lub przenoszony.

Pobierz dzienniki rozruchowe z [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). Poniższy przykład pobiera dane wyjściowe `myVM` szeregowe z `myResourceGroup`maszyny Wirtualnej o nazwie w grupie zasobów o nazwie:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Przejrzyj dane wyjściowe szeregowe, aby ustalić, dlaczego maszyna wirtualna nie uruchamia się. Jeśli dane wyjściowe szeregowe nie dostarczają żadnych wskazówek, `/var/log` może być konieczne przejrzenie plików dziennika, gdy wirtualny dysk twardy jest podłączony do maszyny wirtualnej do rozwiązywania problemów.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład zatrzymuje maszynę wirtualną o nazwie `myVM` z grupy zasobów o nazwie: `myResourceGroup`

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Zrób migawkę z dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem

Migawka jest pełną, tylko do odczytu kopię dysku VHD. Nie można dołączyć do maszyny Wirtualnej. W następnym kroku utworzymy dysk z tej migawki. Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z dysku systemu operacyjnego maszyny wirtualnej o nazwie "myVM". 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Tworzenie dysku na podstawie migawki

Ten skrypt tworzy dysk `myOSDisk` zarządzany o `mySnapshot`nazwie z migawki o nazwie .  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Jeśli grupa zasobów i migawka źródła nie znajduje się w tym samym regionie, `az disk create`po uruchomieniu zostanie wyświetlony błąd "Zasób nie został znaleziony". W takim przypadku należy `--location <region>` określić, aby utworzyć dysk w tym samym regionie co migawka źródłowa.

Teraz masz kopię oryginalnego dysku systemu operacyjnego. Ten nowy dysk można zainstalować na innej maszynie wirtualnej systemu Windows w celu rozwiązywania problemów.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Podłączenie nowego wirtualnego dysku twardego do innej maszyny Wirtualnej
W kolejnych kilku krokach należy użyć innej maszyny Wirtualnej do rozwiązywania problemów. Dysk należy dołączyć do tej maszyny Wirtualnej do rozwiązywania problemów, aby przeglądać i edytować zawartość dysku. Ten proces umożliwia skorygowanie błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu.

Ten skrypt dołączyć `myNewOSDisk` dysk do `MyTroubleshootVM`maszyny Wirtualnej:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Zamontowanie dołączonego dysku danych

> [!NOTE]
> Poniższe przykłady szczegółowo kroki wymagane na maszynie wirtualnej Ubuntu. Jeśli używasz innej dystrybucji Linuksa, takiej jak Red Hat Enterprise Linux `mount` lub SUSE, lokalizacje i polecenia plików dziennika mogą być nieco inne. Odpowiednie zmiany w poleceniach można znaleźć w dokumentacji określonej dystrybucji.

1. SSH do maszyny Wirtualnej rozwiązywania problemów przy użyciu odpowiednich poświadczeń. Jeśli ten dysk jest pierwszym dyskiem danych dołączonym do maszyny wirtualnej do rozwiązywania problemów, dysk prawdopodobnie jest połączony z `/dev/sdc`programem . Służy `dmesg` do wyświetlania dołączonych dysków:

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

    W poprzednim przykładzie dysk systemu operacyjnego znajduje się na `/dev/sda` dysku tymczasowym dla każdej maszyny Wirtualnej jest na poziomie `/dev/sdb`. Jeśli masz wiele dysków z danymi, powinny one być na `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog, aby zainstalować istniejący wirtualny dysk twardy. Poniższy przykład tworzy katalog `troubleshootingdisk`o nazwie:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącym wirtualnym dysku twardym, zainstaluj wymaganą partycję. W poniższym przykładzie montuje `/dev/sdc1`się pierwszą partycję podstawową w:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków danych na maszynach wirtualnych na platformie Azure przy użyciu uniwersalnego unikatowego identyfikatora (UUID) wirtualnego dysku twardego. W tym krótkim scenariuszu rozwiązywania problemów montaż wirtualnego dysku twardego przy użyciu UUID nie jest konieczne. Jednak w normalnym użyciu `/etc/fstab` edycja do zainstalowania wirtualnych dysków twardych przy użyciu nazwy urządzenia, a nie UUID może spowodować, że maszyna wirtualna nie może uruchomić.


## <a name="fix-issues-on-the-new-os-disk"></a>Rozwiązywanie problemów na nowym dysku systemu operacyjnego
Po zamontowaniu istniejącego wirtualnego dysku twardego można teraz wykonać wszelkie czynności konserwacyjne i rozwiązywanie problemów zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-the-new-os-disk"></a>Odmontuj i odłącz nowy dysk systemu operacyjnego
Po usunięciu błędów należy odinstalować i odłączyć istniejący wirtualny dysk twardy od maszyny wirtualnej do rozwiązywania problemów. Nie można używać wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki nie zostanie zwolniona dzierżawa dołączająca wirtualny dysk twardy do maszyny wirtualnej do rozwiązywania problemów.

1. Od sesji SSH do rozwiązywania problemów z maszyną wirtualną odinstaluj istniejący wirtualny dysk twardy. Najpierw zmień katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Teraz odinstaluj istniejący wirtualny dysk twardy. Poniższy przykład odinstalowuje `/dev/sdc1`urządzenie w:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz odłącz wirtualny dysk twardy od maszyny Wirtualnej. Zamknij sesję SSH na maszynie wirtualnej rozwiązywania problemów:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmienianie dysku systemu operacyjnego dla maszyny wirtualnej, którego dotyczy problem

Za pomocą interfejsu wiersza polecenia platformy Azure można zamienić dyski systemu operacyjnego. Nie trzeba usuwać i ponownie utworzyć maszynę wirtualną.

W tym przykładzie `myVM` zatrzymuje maszynę wirtualną o nazwie i przypisuje dysk o nazwie `myNewOSDisk` jako nowy dysk systemu operacyjnego.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z połączeniem się z maszyną [wirtualną, zobacz Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure.](troubleshoot-ssh-connection.md) Aby uzyskać problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej z systemem Linux](troubleshoot-app-connection.md).

