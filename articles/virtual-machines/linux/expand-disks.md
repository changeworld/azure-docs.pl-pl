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
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: 0c2d4d1413b6cfd0b5e457e720b59c6c7b575092
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974548"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Sposobu rozszerzania wirtualnych dysków twardych na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure

Domyślny rozmiar wirtualnego dysku twardego systemu operacyjnego (OS) jest zazwyczaj 30 GB miejsca na maszynie wirtualnej systemu Linux (VM) na platformie Azure. Możesz [dodawanie dysków danych](add-disk.md) zapewnienie dodatkowego miejsca do magazynowania, ale może też chcesz rozszerzyć istniejący dysk danych. Ten artykuł szczegółowo opisuje sposobu rozszerzania dysków zarządzanych dla maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure. 

> [!WARNING]
> Zawsze upewnij się, że utworzono kopię zapasową danych przed wykonaniem dysku zmiany rozmiaru operacji. Aby uzyskać więcej informacji, zobacz [tworzenie kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Rozwiń dyskach zarządzanych platformy Azure
Upewnij się, że masz najnowszy [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index#az_login).

Ten artykuł wymaga istniejącej maszyny Wirtualnej na platformie Azure przy użyciu co najmniej jeden dysk danych dołączony i przygotowane. Jeśli nie masz jeszcze maszyny Wirtualnej, którego można używać, zobacz [Utwórz i przygotuj Maszynę wirtualną z dyskami danych](tutorial-manage-disks.md#create-and-attach-disks).

W następujących przykładach nazwy parametru przykład należy zastąpić własnymi wartościami. Przykładowe nazwy parametru zawierają *myResourceGroup* i *myVM*.

1. Nie można wykonać operacji na wirtualnych dyskach twardych z maszyny Wirtualnej z systemem. Cofnij Przydział maszyny Wirtualnej za pomocą [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Należy cofnąć przydział maszyny Wirtualnej, aby rozwinąć wirtualnego dysku twardego. `az vm stop` zwalnia zasoby obliczeniowe. Aby zwolnić zasoby obliczeniowe, użyj `az vm deallocate`.

1. Wyświetlanie listy dysków zarządzanych w grupie zasobów za pomocą [listy dysków az](/cli/azure/disk#az_disk_list). Poniższy przykład wyświetla listę dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń węzeł wymagana dysk z [aktualizacji dysku az](/cli/azure/disk#az_disk_update). Poniższy przykład rozszerza dysk zarządzany o nazwie *myDataDisk* jako *200*Gb, rozmiar:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysku zarządzanego, zaktualizowano rozmiaru jest mapowany na najbardziej zbliżony rozmiar dysku zarządzanego. Dla tabeli warstwy i rozmiary dostępnych dysków zarządzanych, zobacz [Azure Omówienie usługi Managed Disks — cennik i rozliczenia](../windows/managed-disks-overview.md#pricing-and-billing).

1. Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm#az_vm_start). Poniższy przykład uruchamia maszynę Wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Rozwiń węzeł partycji dysku i system plików
Aby użyć zwiększony rozmiar dysku, trzeba zwiększyć odpowiedni partycję i system plików.

1. SSH z maszyną Wirtualną przy użyciu odpowiednich poświadczeń. Można uzyskać publicznego adresu IP maszyny Wirtualnej za pomocą [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Aby użyć zwiększony rozmiar dysku, trzeba zwiększyć odpowiedni partycję i system plików.

    a. Jeśli już zainstalowany, należy odinstalować dysku:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Użyj `parted` do wyświetlania informacji o dysku i zmienić rozmiar partycji:

    ```bash
    sudo parted /dev/sdc
    ```

    Wyświetl informacje o istniejących układ partycji z `print`. Dane wyjściowe będą podobne do poniższego przykładu, który pokazuje, że odpowiedni dysk ma rozmiar 215 Gb:

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

    d. Aby zakończyć pracę, wprowadź `quit`

1. Z partycją ze zmienionym rozmiarem, Sprawdź spójność partycji z `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Teraz dowolnie zmieniać rozmiar systemu plików przy użyciu `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Zainstaluj partycję w odpowiedniej lokalizacji, takich jak `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Aby sprawdzić, czy zmieniono rozmiar dysku systemu operacyjnego, należy użyć `df -h`. Następujące przykładowe dane wyjściowe zawiera dysk danych */dev/sdc1*, teraz wynosi 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz dodatkowego magazynu, możesz również [dodawanie dysków danych do maszyny Wirtualnej z systemem Linux](add-disk.md). Aby uzyskać więcej informacji o szyfrowaniu dysku, zobacz [szyfrowania dysków na Maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](encrypt-disks.md).
