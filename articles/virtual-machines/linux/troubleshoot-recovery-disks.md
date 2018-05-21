---
title: Użyj Linux, rozwiązywanie problemów z maszyny Wirtualnej z 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyny Wirtualnej systemu Linux łącząc dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: bff31dafdf3263ec189f67da7de8fea6eb3d2662
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Rozwiązywanie problemów z maszyny Wirtualnej systemu Linux, dołączając dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej z 2.0 interfejsu wiersza polecenia platformy Azure
Maszyny wirtualnej systemu Linux (VM) napotkał błąd podczas rozruchu lub dysk, należy wykonać kroki rozwiązywania problemów na wirtualnym dysku twardym, sam. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab` , w związku z było pomyślnie uruchomić maszynę Wirtualną. W tym artykule szczegółowo przedstawiają, jak używać Azure CLI 2.0 w celu połączenia wirtualnego dysku twardego do innej maszyny Wirtualnej systemu Linux, napraw błędy, a następnie ponownie utwórz oryginalnego maszyny Wirtualnej. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuwanie maszyny Wirtualnej, wystąpią problemy, utrzymując wirtualnych dysków twardych.
2. Dołączanie i zainstalować wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytowanie plików lub uruchamianie narzędzi do rozwiązywania problemów z na oryginalny wirtualny dysk twardy.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz maszynę Wirtualną przy użyciu oryginalny wirtualny dysk twardy.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Aby wykonać te kroki rozwiązywania problemów, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W poniższych przykładach zastąpić parametr własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.


## <a name="determine-boot-issues"></a>Określenie zagadnień rozruchu
Sprawdź serial wyniki w celu ustalenia, dlaczego nie będzie mógł poprawnie rozruchu maszyny Wirtualnej. Typowym przykładem jest nieprawidłowy wpis w `/etc/fstab`, lub podstawowej wirtualnego dysku twardego jest usunięty lub przeniesiony.

Pobierz dzienniki rozruchu z [az maszyny wirtualnej diagnostyki rozruchu get rozruchu dziennika](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). Poniższy przykład pobiera wyjściowych serial z maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Przejrzyj wyniki serial w celu ustalenia, dlaczego maszyny Wirtualnej nie może uruchomić. Jeśli serial dane wyjściowe nie jest oznaczenie, konieczne może być Przejrzyj pliki dziennika w `/var/log` po utworzeniu wirtualny dysk twardy połączony z rozwiązywania problemów z maszyną Wirtualną.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegóły istniejącego wirtualnego dysku twardego
Przed dołączeniem wirtualnego dysku twardego (VHD) do innej maszyny Wirtualnej, należy zidentyfikować identyfikator URI dysku systemu operacyjnego. 

Wyświetl informacje o sieci maszyny Wirtualnej z [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show). Użyj `--query` flagę, aby wyodrębnić identyfikator URI do dysku systemu operacyjnego. Poniższy przykład pobiera informacje o dysku dla maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Identyfikator URI jest podobny do **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej są tylko metadane, który definiuje rozmiaru lub lokalizacji i odwołuje się do zasobów, takich jak wirtualny dysk twardy lub sieć wirtualna karta sieciowa (NIC). Każdy wirtualny dysk twardy ma dzierżawę przypisana w momencie dołączony do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dzierżawy w dalszym ciągu skojarzyć dysk systemu operacyjnego z maszyny Wirtualnej, nawet w przypadku tej maszyny Wirtualnej w stanie zatrzymania i deallocated.

Pierwszy krok w celu odzyskania maszyny Wirtualnej jest usunąć samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej, możesz dołączyć wirtualnego dysku twardego do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Usuń maszynę Wirtualną z [usunąć maszyny wirtualnej az](/cli/azure/vm#az_vm_delete). Poniższy przykład powoduje usunięcie maszyny Wirtualnej o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Poczekaj na Maszynie wirtualnej zakończył, usuwanie przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej. Dzierżawy na wirtualnym dysku twardym, który kojarzy go z maszyny Wirtualnej musi zostać zwolniony przed dołączeniem wirtualnego dysku twardego do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołączanie istniejącego wirtualnego dysku twardego do innej maszyny Wirtualnej
Dla następnych kilku krokach możesz użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Istniejącego wirtualnego dysku twardego należy dołączyć do tej maszyny Wirtualnej rozwiązywania problemów, aby przeglądać i edytowania zawartości na dysku. Ten proces umożliwia poprawić błędy konfiguracji lub przejrzyj dodatkowe aplikacji lub plików dziennika systemu, np. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

Dołączanie istniejącego wirtualnego dysku twardego z [dołączyć az wirtualna niezarządzanych dysku](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). Po dołączeniu istniejącego wirtualnego dysku twardego, określ identyfikator URI dysku uzyskanych w poprzednim `az vm show` polecenia. Poniższy przykład dołącza istniejącego wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Zainstalować dysk dołączonych danych

> [!NOTE]
> Poniższe przykłady szczegółowo opisano kroki wymagane w przypadku maszyny Wirtualnej systemu Ubuntu. Jeśli używasz różnych distro Linux, takich jak Red Hat Enterprise Linux i SUSE, lokalizacje plików dziennika i `mount` poleceń mogą być nieco inne. Zapoznaj się z dokumentacją dotyczącą Twojej distro określonych dla odpowiednie zmiany w poleceniach.

1. SSH do rozwiązywania problemów z maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Jeśli dysk jest dołączony do rozwiązywania problemów z maszyny Wirtualnej pierwszego dysku danych, dysk prawdopodobnie jest podłączony do `/dev/sdc`. Użyj `dmseg` do wyświetlania dołączonych dysków:

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

    W powyższym przykładzie dysk systemu operacyjnego jest w `/dev/sda` i dysku tymczasowym podana dla każdej maszyny Wirtualnej jest w `/dev/sdb`. Jeśli masz wiele dysków z danymi, należy je na `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog do zainstalowania istniejącego wirtualnego dysku twardego. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącego wirtualnego dysku twardego, zainstaluj wymagane partycji. Poniższy przykład instaluje pierwszej partycji podstawowej na `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu unikatowym identyfikatorem wirtualnego dysku twardego (UUID). Instalowanie wirtualnego dysku twardego za pomocą identyfikator UUID nie jest w tym scenariuszu rozwiązywania problemów z krótkim konieczne. Jednak w ramach normalnego użytkowania edycji `/etc/fstab` można zainstalować wirtualnych dysków twardych przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować niepowodzenie do rozruchu maszyny Wirtualnej.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalny wirtualny dysk twardy
Istniejącego wirtualnego dysku twardego zainstalowane można teraz wykonywać żadnych konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstaluj obraz i odłączyć oryginalny wirtualny dysk twardy
Po rozwiązaniu błędy, odinstaluj i odłączyć istniejącego wirtualnego dysku twardego z rozwiązywania problemów z maszyny Wirtualnej. Nie można użyć wirtualnego dysku twardego z innych maszyn wirtualnych, do czasu zwolnienia dzierżawy dołączanie wirtualnego dysku twardego do rozwiązywania problemów z maszyny Wirtualnej.

1. W sesji SSH do rozwiązywania problemów z maszyny Wirtualnej należy odinstalować istniejącego wirtualnego dysku twardego. Najpierw zmienić poza katalogu nadrzędnego punktu instalacji:

    ```bash
    cd /
    ```

    Teraz należy odinstalować istniejącego wirtualnego dysku twardego. Poniższy przykład odinstalowuje urządzenia pod adresem `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Zakończ sesję SSH do rozwiązywania problemów z maszyny Wirtualnej. Na dyskach danych dołączonych do rozwiązywania problemów z maszyny Wirtualnej z listy [lista niezarządzanych dysków maszyny wirtualnej az](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). Poniższy przykład zawiera na dyskach danych dołączonych do maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Zanotuj nazwę dla istniejącego wirtualnego dysku twardego. Na przykład nazwa dysku z identyfikatora URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** jest **myVHD**. 

    Odłączyć dysku danych od maszyny Wirtualnej [odłączyć az wirtualna niezarządzanych dysku](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). Poniższy przykład odłącza dysk o nazwie `myVHD` z maszyny Wirtualnej o nazwie `myVMRecovery` w `myResourceGroup` grupy zasobów:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej z oryginalny dysk twardy
Aby utworzyć Maszynę wirtualną z oryginalny wirtualny dysk twardy, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Rzeczywiste szablonu JSON jest przy użyciu następującego łącza:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

Szablon wdraża maszynę Wirtualną przy użyciu identyfikatora URI dysku VHD z wcześniejszych polecenia. Wdrażanie szablonu z [Utwórz wdrożenie grupy az](/cli/azure/group/deployment#az_group_deployment_create). Podaj identyfikator URI do oryginalny dysk VHD, a następnie określ typ systemu operacyjnego, rozmiar maszyny Wirtualnej i Nazwa maszyny Wirtualnej w następujący sposób:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego diagnostyki rozruchu może nie automatycznie włączone. Włącz diagnostykę rozruchu z [włączyć diagnostyki rozruchu az wirtualna](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). Poniższy przykład umożliwia włączenie diagnostycznych rozszerzenia na Maszynie wirtualnej o nazwie `myDeployedVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z maszyn wirtualnych dysków zarządzanych przez Trwa dołączanie nowego dysku systemu operacyjnego
1. Zatrzymaj następujące zarządzane dysku maszyny Wirtualnej systemu Windows.
2. [Tworzenie migawki dysków zarządzanych w](../windows/snapshot-copy-managed-disk.md) dysku systemu operacyjnego maszyny wirtualnej dysku zarządzanego.
3. [Tworzenie dysku zarządzanego z migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołącz zarządzanych dysku jako dysku danych maszyny wirtualnej](../windows/attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4 dysk systemu operacyjnego](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyny Wirtualnej platformy Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z łącznością aplikacji na maszynie Wirtualnej systemu Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).