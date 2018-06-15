---
title: Rozwiń węzeł wirtualne dyski twarde na Maszynę wirtualną systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć wirtualne dyski twarde na Maszynę wirtualną systemu Linux 2.0 interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: c3dcd2f9c71b64ac48c2c7b6f51d2694d649c15e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
ms.locfileid: "30291286"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Jak rozszerzyć wirtualne dyski twarde na maszynę Wirtualną systemu Linux za pomocą wiersza polecenia platformy Azure
Domyślny rozmiar wirtualnego dysku twardego systemu operacyjnego (OS) jest zwykle 30 GB na maszynie wirtualnej systemu Linux (VM) na platformie Azure. Możesz [Dodaj dyski danych](add-disk.md) zapewnienie dodatkowego miejsca, ale mogą też chcieć rozwiń istniejącego dysku danych. Ten artykuł zawiera szczegóły dotyczące sposobu rozszerzania zarządzanych dysków dla maszyny Wirtualnej systemu Linux 2.0 interfejsu wiersza polecenia platformy Azure. Można również rozwinąć niezarządzane dysk systemu operacyjnego z [Azure CLI 1.0](expand-disks-nodejs.md).

> [!WARNING]
> Zawsze upewnij się, utworzono kopię zapasową danych przed wykonaniem dysku zmienić rozmiar operacji. Aby uzyskać więcej informacji, zobacz [kopii zapasowych maszyn wirtualnych systemu Linux na platformie Azure](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Rozwiń węzeł zarządzany dysku platformy Azure
Upewnij się, że masz najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W tym artykule wymaga istniejącej maszyny Wirtualnej na platformie Azure z co najmniej jeden dysk danych dołączona i przygotowane. Jeśli nie masz już maszyny Wirtualnej, który można użyć, zobacz [tworzenie i przygotowywanie maszyny Wirtualnej z dyskami danych](tutorial-manage-disks.md#create-and-attach-disks).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają *myResourceGroup* i *myVM*.

1. Nie można wykonać operacji na wirtualnych dyskach twardych z uruchomionych maszyn wirtualnych. Cofnięcie przydziału maszyny Wirtualnej z [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Aby zwiększyć rozmiaru wirtualnego dysku twardego, można cofnąć przydziału maszyny Wirtualnej. `az vm stop` zwalnia zasoby obliczeniowe. Aby zwolnić zasoby obliczeniowe, użyj `az vm deallocate`.

2. Wyświetl listę dysków zarządzanych w grupie zasobów o [Lista dysków az](/cli/azure/disk#az_disk_list). W poniższym przykładzie przedstawiono listę dysków zarządzanych w tej grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń węzeł wymagana dysk z [aktualizacja dysku az](/cli/azure/disk#az_disk_update). Poniższy przykład rozszerza dysków zarządzanych o nazwie *myDataDisk* jako *200*rozmiar Gb:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysków zarządzanych zaktualizowano rozmiar jest mapowany do najbliższej rozmiaru dysków zarządzanych. Dla tabeli dysków zarządzanych w dostępne rozmiary i warstw, zobacz [Azure zarządzanych dysków Przegląd — cennik i rozliczenia](../windows/managed-disks-overview.md#pricing-and-billing).

3. Uruchom maszyny Wirtualnej z [uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_start). W następującym przykładzie uruchomiono maszyny Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Rozwiń węzeł partycję dysku i system plików
Użyj rozwinięte dysku, należy rozwinąć odpowiedni partycji i systemu plików.

1. SSH do maszyny Wirtualnej przy użyciu odpowiednich poświadczeń. Możesz uzyskać publicznego adresu IP maszyny Wirtualnej z [az maszyny wirtualnej pokazu](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. Użyj rozwinięte dysku, należy rozwinąć odpowiedni partycji i systemu plików.

    a. Jeśli już zainstalowane, odinstaluj dysku:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Użyj `parted` do wyświetlania informacji o dysku i zmienić rozmiar partycji:

    ```bash
    sudo parted /dev/sdc
    ```

    Wyświetl informacje o istniejących układu partycji z `print`. Wynik jest podobny do poniższego przykładu, który zawiera informacje o dysku 215 Gb ma rozmiar:

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

    c. Rozwiń węzeł partycji o `resizepart`. Wprowadź numer partycji *1*i rozmiaru dla nowej partycji:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Aby zakończyć pracę, wprowadź `quit`

3. Z partycją zmiany rozmiaru, Sprawdź spójność partycji z `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. Teraz Zmień rozmiar plików z `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. Zainstaluj partycji do odpowiedniej lokalizacji, takich jak `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Aby sprawdzić, zmiany rozmiaru dysku systemu operacyjnego, użyj `df -h`. Następujące przykładowe dane wyjściowe zawiera dysk danych */dev/sdc1*, jest teraz 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz dodatkowego magazynu, możesz również [Dodaj dyski danych do maszyny Wirtualnej systemu Linux](add-disk.md). Aby uzyskać więcej informacji o szyfrowaniu dysków, zobacz [szyfrowania dysków na Maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia Azure](encrypt-disks.md).
