---
title: Rozwiń wirtualnych dysków twardych na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć wirtualnych dysków twardych na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4113d582647b5bea86980824714936d24dafc870
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511145"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozwiń wirtualnych dysków twardych na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure

W tym artykule opisano, jak rozwinąć dysków zarządzanych dla maszyny wirtualnej systemu Linux (VM) z wiersza polecenia platformy Azure. Możesz [dodawanie dysków danych](add-disk.md) zapewnienie dodatkowego miejsca do magazynowania miejsca, a także rozszerzyć istniejący dysk danych. Domyślny rozmiar wirtualnego dysku twardego systemu operacyjnego (OS) jest zazwyczaj 30 GB na Maszynę wirtualną systemu Linux na platformie Azure. 

> [!WARNING]
> Zawsze upewnij się, czy usługi systemu plików jest w dobrej kondycji, danego typu tabeli partycji dysku będzie obsługiwać nowe rozmiary i upewnij się, że przed wykonaniem operacji zmiany rozmiaru dysku danych jest wykonywana kopia zapasowa. Aby uzyskać więcej informacji, zobacz [tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Rozwiń dysku zarządzanego platformy Azure
Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i jest zalogowany na koncie platformy Azure przy użyciu [az login](/cli/azure/reference-index#az-login).

Ten artykuł wymaga istniejącej maszyny Wirtualnej na platformie Azure przy użyciu co najmniej jeden dysk danych dołączony i przygotowane. Jeśli nie masz jeszcze maszyny Wirtualnej, którego można używać, zobacz [Utwórz i przygotuj Maszynę wirtualną z dyskami danych](tutorial-manage-disks.md#create-and-attach-disks).

W następujących przykładach Zastąp przykładowe nazwy parametru takich jak *myResourceGroup* i *myVM* własnymi wartościami.

1. Nie można wykonać operacji na wirtualnych dyskach twardych z maszyny Wirtualnej z systemem. Cofnij Przydział maszyny Wirtualnej za pomocą [az vm deallocate](/cli/azure/vm#az-vm-deallocate). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Należy cofnąć przydział maszyny Wirtualnej, aby rozwinąć wirtualnego dysku twardego. Zatrzymywanie maszyny Wirtualnej przy użyciu `az vm stop` nie spowoduje zwolnienia zasobów obliczeniowych. Aby zwolnić zasoby obliczeniowe, użyj `az vm deallocate`.

1. Wyświetlanie listy dysków zarządzanych w grupie zasobów za pomocą [listy dysków az](/cli/azure/disk#az-disk-list). Poniższy przykład wyświetla listę dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń węzeł wymagana dysk z [aktualizacji dysku az](/cli/azure/disk#az-disk-update). Poniższy przykład rozszerza dysk zarządzany o nazwie *myDataDisk* do *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysku zarządzanego, zaktualizowano rozmiar jest zaokrąglana w górę do najbliższej rozmiaru dysku zarządzanego. Dla tabeli warstwy i rozmiary dostępnych dysków zarządzanych, zobacz [Azure Omówienie usługi Managed Disks — cennik i rozliczenia](../windows/managed-disks-overview.md).

1. Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm#az-vm-start). Poniższy przykład uruchamia maszynę Wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozwiń węzeł partycji dysku i system plików
Aby użyć zwiększony rozmiar dysku, rozwiń węzeł partycji podstawowej oraz system plików.

1. SSH z maszyną Wirtualną przy użyciu odpowiednich poświadczeń. Widać, publiczny adres IP swojej maszyny wirtualnej za pomocą [az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Rozwiń węzeł partycji podstawowej i system plików.

    a. Jeśli dysk jest już zainstalowany, należy go odinstalować:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Użyj `parted` do wyświetlania informacji o dysku i zmienić rozmiar partycji:

    ```bash
    sudo parted /dev/sdc
    ```

    Wyświetl informacje o istniejących układ partycji z `print`. Dane wyjściowe będą podobne do poniższego przykładu, który pokazuje, że dysk podstawowy jest 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Rozwiń węzeł partycji o `resizepart`. Wprowadź numer partycji *1*i rozmiar nowej partycji:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Aby zakończyć pracę, wprowadź `quit`.

1. Z partycją ze zmienionym rozmiarem, Sprawdź spójność partycji z `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Zmiana rozmiaru systemu plików przy użyciu `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Zainstaluj partycję w odpowiedniej lokalizacji, takich jak `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Aby sprawdzić, czy zmieniono rozmiar dysku danych, należy użyć `df -h`. Następujące przykładowe dane wyjściowe zawiera dysk danych */dev/sdc1* teraz wynosi 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Kolejne kroki
* Jeśli potrzebujesz dodatkowego miejsca do magazynowania, możesz również [dodawanie dysków danych do maszyny Wirtualnej z systemem Linux](add-disk.md). 
* Aby uzyskać więcej informacji o szyfrowaniu dysku, zobacz [szyfrowania dysków na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md).
