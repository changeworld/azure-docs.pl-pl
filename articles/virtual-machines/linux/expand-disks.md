---
title: Rozszerzanie wirtualnych dysków twardych na maszynie Wirtualnej systemu Linux
description: Dowiedz się, jak rozwinąć wirtualne dyski twarde na maszynie Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945183"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Rozszerzanie wirtualnych dysków twardych na maszynie Wirtualnej systemu Linux za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób rozwijania dysków zarządzanych dla maszyny wirtualnej systemu Linux (VM) za pomocą interfejsu wiersza polecenia platformy Azure. Można [dodać dyski danych,](add-disk.md) aby zapewnić dodatkowe miejsce do magazynowania, a także rozwinąć istniejący dysk danych. Domyślny rozmiar wirtualnego dysku twardego dla systemu operacyjnego (OS) jest zazwyczaj 30 GB na maszynie wirtualnej z systemem Linux na platformie Azure. 

> [!WARNING]
> Zawsze upewnij się, że system plików jest w dobrej kondycji, typ tabeli partycji dysku będzie obsługiwać nowy rozmiar i upewnij się, że kopia zapasowa danych jest archiwizowanie przed wykonaniem operacji ponownego rozmiaru dysku. Aby uzyskać więcej informacji, zobacz [łańz maszyn wirtualnych z systemem Linux na platformie Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Rozwijanie dysku zarządzanego platformy Azure
Upewnij się, że masz zainstalowaną najnowszą [platformę Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index#az-login)

Ten artykuł wymaga istniejącej maszyny Wirtualnej na platformie Azure z co najmniej jednym dyskiem danych dołączonym i przygotowanym. Jeśli nie masz jeszcze maszyny Wirtualnej, której można użyć, zobacz [Tworzenie i przygotowywanie maszyny wirtualnej z dyskami z danymi](tutorial-manage-disks.md#create-and-attach-disks).

W poniższych przykładach zastąp przykładowe nazwy parametrów, takie jak *myResourceGroup* i *myVM,* własnymi wartościami.

1. Nie można wykonywać operacji na wirtualnych dyskach twardych przy uruchomionej maszynie wirtualnej. Zdeokuj swoją [maszynę wirtualną za pomocą az vm deallocate](/cli/azure/vm#az-vm-deallocate). W poniższym przykładzie wiele osób przydziela maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Maszyna wirtualna musi zostać cofnięta, aby rozwinąć wirtualny dysk twardy. Zatrzymywanie `az vm stop` maszyny Wirtualnej z nie zwalnia zasobów obliczeniowych. Aby zwolnić zasoby obliczeniowe, użyj programu `az vm deallocate`.

1. Wyświetlanie listy dysków zarządzanych w grupie zasobów z [listą dysków AZ](/cli/azure/disk#az-disk-list). W poniższym przykładzie jest wyświetlana lista dysków zarządzanych w grupie zasobów o nazwie *myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Rozwiń wymagany dysk za pomocą [aktualizacji dysku AZ](/cli/azure/disk#az-disk-update). Poniższy przykład rozszerza dysk zarządzany o nazwie *myDataDisk* do *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Po rozwinięciu dysku zarządzanego zaktualizowany rozmiar jest zaokrąglany w górę do najbliższego rozmiaru dysku zarządzanego. Aby zapoznać się z tabelą dostępnych rozmiarów i warstw zarządzanych dysków, zobacz [Omówienie dysków zarządzanych platformy Azure — ceny i rozliczenia](../windows/managed-disks-overview.md).

1. Uruchom [maszynę wirtualną z az vm start](/cli/azure/vm#az-vm-start). Poniższy przykład uruchamia maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup:*

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Rozwijanie partycji dysku i systemu plików
Aby użyć rozwiniętego dysku, rozwiń partycję podstawową i system plików.

1. SSH do maszyny Wirtualnej z odpowiednimi poświadczeniami. Publiczny adres IP maszyny Wirtualnej można zobaczyć z [az vm show:](/cli/azure/vm#az-vm-show)

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Rozwiń podstawową partycję i system plików.

    a. Jeśli dysk jest już zamontowany, odinstaluj go:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Służy `parted` do wyświetlania informacji o dysku i ponownego rozmiaru partycji:

    ```bash
    sudo parted /dev/sdc
    ```

    Wyświetlanie informacji o istniejącym `print`układzie partycji za pomocą pliku . Dane wyjściowe są podobne do następującego przykładu, który pokazuje, że dysk bazowy wynosi 215 GB:

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

    d. Rozwiń partycję za pomocą pliku `resizepart`. Wprowadź numer partycji, *1*i rozmiar dla nowej partycji:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Aby wyjść, `quit`wprowadź .

1. Po ponownym rozmiarze partycji sprawdź `e2fsck`spójność partycji za pomocą:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Zmienić rozmiar systemu `resize2fs`plików za pomocą:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Zamontuj partycję w `/datadrive`żądanym miejscu, takim jak:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Aby sprawdzić, czy dysk danych został `df -h`ponownie szesnasty, użyj programu . Poniższy przykładowy wynik pokazuje, że dysk danych */dev/sdc1* ma teraz 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Następne kroki
* Jeśli potrzebujesz dodatkowego miejsca do magazynowania, możesz również [dodać dyski z danymi do maszyny Wirtualnej z systemem Linux](add-disk.md). 
* Aby uzyskać więcej informacji na temat szyfrowania [dysków, zobacz Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux](disk-encryption-overview.md).
