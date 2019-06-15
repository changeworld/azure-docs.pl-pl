---
title: Używać systemu Linux, rozwiązywanie problemów z maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z maszyny Wirtualnej systemu Linux, łącząc dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: dfb85b0f9f1dda611c613cb296177cf28391adc0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318854"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Rozwiązywanie problemów z maszyny Wirtualnej z systemem Linux przez dołączenie dysku systemu operacyjnego do odzyskiwania maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure
Linux maszyny wirtualnej (VM) napotkał błąd podczas rozruchu lub dysk, może być konieczne wykonanie kroków rozwiązywania problemów na samym wirtualnym dysku twardym. Typowym przykładem może być nieprawidłowy wpis w `/etc/fstab` że zapobiega maszyny Wirtualnej możliwość wykonania rozruchu pomyślnie. Ten artykuł szczegółowo opisuje jak połączyć wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux, aby naprawić wszystkie błędy, a następnie ponownie utworzyć oryginalną maszynę Wirtualną za pomocą wiersza polecenia platformy Azure. 


## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Usuń maszynę Wirtualną napotyka problemy, utrzymując wirtualnych dysków twardych.
2. Dołącz i zainstaluj wirtualny dysk twardy do innej maszyny Wirtualnej systemu Linux na potrzeby rozwiązywania problemów.
3. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub uruchom dowolnych narzędzi w celu rozwiązywania problemów na oryginalnym wirtualnym dysku twardym.
4. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
5. Utwórz Maszynę wirtualną za pomocą oryginalnego wirtualnego dysku twardego.

Dla maszyny Wirtualnej używającej dysków zarządzanych, zobacz [Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie dysku systemu operacyjnego nowy](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Aby wykonać te kroki rozwiązywania problemów, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami nazw parametrów. Przykładowe nazwy parametru zawierają `myResourceGroup`, `mystorageaccount`, i `myVM`.


## <a name="determine-boot-issues"></a>Określenia problemów rozruchu
Sprawdź serial wyniki w celu ustalenia, dlaczego nie jest poprawnie rozruch maszyny Wirtualnej. Typowym przykładem jest nieprawidłowy wpis w `/etc/fstab`, albo podstawowy wirtualny dysk twardy jest usunięty lub przeniesiony.

Pobierz dzienniki rozruchu z [az vm diagnostyki rozruchu get-boot-log](/cli/azure/vm/boot-diagnostics). Poniższy przykład pobiera serial dane wyjściowe z maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Przejrzyj serial dane wyjściowe, aby ustalić, dlaczego maszyny Wirtualnej nie może uruchomić. Jeśli serial dane wyjściowe nie ma żadnego wskazania, konieczne może być Przejrzyj pliki dziennika w `/var/log` po utworzeniu wirtualny dysk twardy podłączony do maszyny wirtualnej rozwiązywania problemów.


## <a name="view-existing-virtual-hard-disk-details"></a>Szczegółowe informacje są istniejącego wirtualnego dysku twardego
Zanim będzie możliwe dołączenie wirtualnego dysku twardego (VHD) do innej maszyny Wirtualnej, należy zidentyfikować identyfikator URI dysku systemu operacyjnego. 

Wyświetlanie informacji o maszynie Wirtualnej za pomocą [az vm show](/cli/azure/vm). Użyj `--query` flagę, aby wyodrębnić identyfikator URI do dysku systemu operacyjnego. Poniższy przykład pobiera informacje o dysku dla maszyny Wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Identyfikator URI jest podobny do **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** .

## <a name="delete-existing-vm"></a>Usuń istniejącą maszynę Wirtualną
Wirtualne dyski twarde i maszyny wirtualne to dwa odrębne zasoby na platformie Azure. Wirtualny dysk twardy jest, gdzie są przechowywane sam system operacyjny, aplikacje i konfiguracje. Samej maszyny Wirtualnej jest po prostu metadanymi, definiują rozmiar lub lokalizację, która odwołuje się do zasobów, takich jak wirtualny dysk twardy lub karty interfejsu sieci wirtualnej (NIC). Każdy wirtualny dysk twardy ma przypisaną dzierżawę, kiedy dołączone do maszyny Wirtualnej. Dyski danych można dołączać i odłączać nawet wtedy, gdy maszyna wirtualna jest uruchomiona, ale dysku systemu operacyjnego nie można odłączyć, chyba że zasób maszyny wirtualnej zostanie usunięty. Dalszym ciągu będzie kojarzyła dysk systemu operacyjnego z maszyną Wirtualną, nawet wtedy, gdy ta maszyna wirtualna jest w stanie zatrzymania i ma cofnięty przydział dzierżawy.

Pierwszy krok, aby odzyskać maszynę Wirtualną jest nieusuwanie samego zasobu maszyny Wirtualnej. Usunięcie maszyny wirtualnej pozostawia wirtualne dyski twarde na koncie magazynu. Po usunięciu maszyny Wirtualnej możesz dołączyć wirtualny dysk twardy do innej maszyny Wirtualnej do rozwiązywania oraz usuwania błędów.

Usuń maszynę Wirtualną za pomocą [Usuń az vm](/cli/azure/vm). Poniższy przykład usuwa maszynę Wirtualną o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Poczekaj, aż maszyny Wirtualnej zostało zakończone, usuwanie, przed dołączeniem wirtualny dysk twardy do innej maszyny Wirtualnej. Dzierżawy dla wirtualnego dysku twardego, który kojarzy ją z maszyny Wirtualnej musi zostać zwolnione, zanim będzie możliwe dołączenie wirtualny dysk twardy do innej maszyny Wirtualnej.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Dołącz istniejący wirtualny dysk twardy do innej maszyny Wirtualnej
W następnych kilku krokach należy użyć innej maszyny Wirtualnej na potrzeby rozwiązywania problemów. Możesz dołączyć istniejący wirtualny dysk twardy do tej maszyny Wirtualnej rozwiązywania problemów do przeglądania i edytowania zawartości na dysku. Ten proces umożliwia Popraw wszelkie błędy konfiguracji lub przejrzyj dodatkową aplikację i plikach dziennika systemowego, na przykład. Wybierz lub Utwórz inną maszynę Wirtualną do użycia na potrzeby rozwiązywania problemów.

Dołącz istniejący wirtualny dysk twardy z [dołączyć az vm unmanaged dysku](/cli/azure/vm/unmanaged-disk). Po dołączeniu istniejącego wirtualnego dysku twardego Określ identyfikator URI dysku uzyskanego w poprzednim `az vm show` polecenia. Poniższy przykład dołącza istniejącego wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów, o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Zainstaluj dysk danych dołączony

> [!NOTE]
> Poniższe przykłady zawierają szczegółowe kroki wymagane na maszynie Wirtualnej systemu Ubuntu. Jeśli używasz innego dystrybucja systemu Linux, takie jak Red Hat Enterprise Linux lub SUSE, lokalizacje plików dziennika i `mount` polecenia mogą być nieco inne. Zapoznaj się z dokumentacją dla swojej dystrybucji określonych dla odpowiednie zmiany w poleceniach.

1. SSH z maszyną Wirtualną rozwiązywania problemów, przy użyciu odpowiednich poświadczeń. Jeśli ten dysk jest pierwszy dysk danych dołączony do maszyny Wirtualnej rozwiązywania problemów, dysku, prawdopodobnie jest podłączony do `/dev/sdc`. Użyj `dmseg` do wyświetlania dołączonych dysków:

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

    W powyższym przykładzie dysk systemu operacyjnego wynosi `/dev/sda` i podane dla każdej maszyny Wirtualnej znajduje się na dysku tymczasowego `/dev/sdb`. Jeśli masz wiele dysków z danymi, powinny one być w `/dev/sdd`, `/dev/sde`i tak dalej.

2. Utwórz katalog do zainstalowania istniejącego wirtualnego dysku twardego. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącego wirtualnego dysku twardego, zainstaluj partycję wymagane. Poniższy przykład instaluje pierwszej partycji podstawowej na `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest instalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu powszechnie Unikatowy identyfikator (UUID) wirtualnego dysku twardego. Instalowanie wirtualnego dysku twardego, użyj identyfikator UUID nie jest w tym scenariuszu rozwiązywania problemów z krótkim konieczne. Jednak w trakcie normalnego użytkowania edytowanie `/etc/fstab` zainstalować wirtualne dyski twarde przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować maszynę Wirtualną, aby zakończyć się niepowodzeniem, rozruch.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów na oryginalnym wirtualnym dysku twardym
Istniejący wirtualny dysk twardy zainstalowany można teraz wykonywać żadnej konserwacji i kroki rozwiązywania problemów, zgodnie z potrzebami. Po usunięciu problemów wykonaj następujące kroki.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Gdy błędy są rozwiązywane, odinstaluj i Odłącz istniejący wirtualny dysk twardy z maszyny Wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z innej maszyny Wirtualnej, dopóki dzierżawa dołączanie wirtualnego dysku twardego do maszyny Wirtualnej rozwiązywania problemów zostanie zwolniona.

1. W sesji SSH dla maszyny Wirtualnej rozwiązywania problemów należy odinstalować istniejącego wirtualnego dysku twardego. Najpierw zmień poza katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Teraz odinstalować istniejącego wirtualnego dysku twardego. Poniższy przykład umożliwia odinstalowanie urządzenia pod adresem `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy z maszyny Wirtualnej. Zamknij sesję SSH dla maszyny Wirtualnej rozwiązywania problemów. Listy dołączonych dysków danych do maszyny Wirtualnej rozwiązywania problemów z [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk). Poniższy przykład wyświetla listę dysków danych dołączonych do maszyny Wirtualnej o nazwie `myVMRecovery` w grupie zasobów o nazwie `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Zanotuj nazwę dla istniejącego wirtualnego dysku twardego. Na przykład nazwa dysku z identyfikatora URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** jest **myVHD**. 

    Dołączanie dysku danych z maszyny Wirtualnej [maszyny wirtualnej az unmanaged-disk detach](/cli/azure/vm/unmanaged-disk). Poniższy przykład powoduje odłączenie dysku o nazwie `myVHD` z maszyny Wirtualnej o nazwie `myVMRecovery` w `myResourceGroup` grupy zasobów:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Tworzenie maszyny Wirtualnej na podstawie oryginalnego dysku twardego
Aby utworzyć Maszynę wirtualną z oryginalnego wirtualnego dysku twardego, użyj [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Jest prawdziwy szablon JSON z łącza:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Szablon umożliwia wdrożenie maszyny Wirtualnej przy użyciu identyfikatora URI wirtualnego dysku twardego z wcześniej polecenia. Wdrażanie szablonu przy użyciu [Utwórz wdrożenie grupy az](/cli/azure/group/deployment). Podaj identyfikator URI do oryginalnego wirtualnego dysku twardego, a następnie określ typ systemu operacyjnego, rozmiaru maszyny Wirtualnej i nazwę maszyny Wirtualnej w następujący sposób:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Włączyć ponownie diagnostykę rozruchu
Po utworzeniu maszyny Wirtualnej z istniejącego wirtualnego dysku twardego Diagnostyka rozruchu może nie być włączane automatycznie. Włącz diagnostykę rozruchu przy użyciu [Włącz diagnostykę rozruchu az vm](/cli/azure/vm/boot-diagnostics). Poniższy przykład umożliwia rozszerzenie diagnostyki na maszynie Wirtualnej o nazwie `myDeployedVM` w grupie zasobów o nazwie `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Rozwiązywanie problemów z dysku zarządzanego maszyny Wirtualnej przez dołączenie nowy dysk systemu operacyjnego
1. Zatrzymywanie danej maszyny Wirtualnej.
2. [Tworzenie migawki dysku zarządzanego](../linux/snapshot-copy-managed-disk.md) dysku systemu operacyjnego, dysku zarządzanego maszyny wirtualnej.
3. [Tworzenie dysku zarządzanego na podstawie migawki](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Dołączanie dysku zarządzanego jako dysku danych maszyny wirtualnej](../windows/attach-disk-ps.md).
5. [Zmień dysk z danymi z kroku 4, na dysku systemu operacyjnego](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z Maszyną wirtualną platformy Azure](troubleshoot-ssh-connection.md). W przypadku problemów z dostępem do aplikacji działających na maszynie Wirtualnej, zobacz [aplikacji Rozwiązywanie problemów z łącznością na maszynie Wirtualnej systemu Linux](troubleshoot-app-connection.md).

