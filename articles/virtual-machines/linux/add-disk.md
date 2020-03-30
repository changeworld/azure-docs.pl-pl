---
title: Dodawanie dysku danych do maszyny Wirtualnej systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak dodać trwały dysk danych do maszyny Wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a80a1fe21ba0b40aebf9e426e3d49f499c2d2a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250415"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Dodawanie dysku do maszyny wirtualnej z systemem Linux
W tym artykule pokazano, jak dołączyć dysk trwały do maszyny Wirtualnej, dzięki czemu można zachować dane — nawet jeśli maszyna wirtualna jest ponownie aprowizacja z powodu konserwacji lub zmiany rozmiaru.


## <a name="attach-a-new-disk-to-a-vm"></a>Dołączanie nowego dysku do maszyny Wirtualnej

Jeśli chcesz dodać nowy, pusty dysk danych na maszynie Wirtualnej, użyj polecenia `--new` [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) z parametrem. Jeśli maszyna wirtualna znajduje się w strefie dostępności, dysk jest automatycznie tworzony w tej samej strefie co maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Omówienie stref dostępności](../../availability-zones/az-overview.md). Poniższy przykład tworzy dysk o nazwie *myDataDisk* o rozmiarze 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku

Aby dołączyć istniejący dysk, znajdź identyfikator dysku i przekaż identyfikator do polecenia [az vm disk attach.](/cli/azure/vm/disk?view=azure-cli-latest) Poniższy przykład kwerendy dla dysku o nazwie *myDataDisk* w *myResourceGroup*, a następnie dołącza go do maszyny Wirtualnej o nazwie *myVM:*

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Połącz się z maszyną wirtualną z systemem Linux, aby zamontować nowy dysk

Aby podzielić, sformatować i zamontować nowy dysk, aby maszyna wirtualna z systemem Linux mogła go używać, SSH do maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład łączy się z maszyną wirtualną z publicznym wpisem DNS *mypublicdns.westus.cloudapp.azure.com* z nazwą użytkownika *azureuser:*

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po podłączeniu do maszyny Wirtualnej można dołączyć dysk. Najpierw znajdź dysk `dmesg` przy użyciu (metoda używana do odnajdywanie nowego dysku może się różnić). W poniższym przykładzie do filtrowania dysków *SCSI* użyto dmesg:

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

> [!NOTE]
> Zaleca się używanie najnowszych wersji fdisk lub parted, które są dostępne dla twojej dystrybucji.

Tutaj *sdc* to dysk, który chcemy. Partycja dysku `parted`z , jeśli rozmiar dysku jest 2 tebibajtów (TiB) lub większy, a następnie należy użyć partycjonowania GPT, jeśli jest pod 2TiB, a następnie można użyć partycjonowania MBR lub GPT. Jeśli używasz partycjonowania MBR, `fdisk`możesz użyć programu . Ustaw dysk podstawowy na partycji 1 i zaakceptuj inne ustawienia domyślne. Poniższy przykład rozpoczyna `fdisk` proces na */dev/sdc:*

```bash
sudo fdisk /dev/sdc
```

Nową partycję możesz dodać za pomocą polecenia `n`. W tym przykładzie `p` wybieramy również partycję podstawową i akceptujemy pozostałe wartości domyślne. Dane wyjściowe będą mieć postać podobną do następującej:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Wydrukuj tabelę `p` partycji, `w` wpisując, a następnie użyj do zapisania tabeli na dysku i zakończenia. Dane wyjściowe powinny wyglądać podobnie do następującego przykładu:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Użyj poniższego polecenia, aby zaktualizować jądro:
```
partprobe 
```

Teraz napisz system plików na partycji za `mkfs` pomocą polecenia. Określ typ systemu plików i nazwę urządzenia. Poniższy przykład tworzy system plików *ext4* na partycji */dev/sdc1,* która została utworzona w poprzednich krokach:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Dane wyjściowe są podobne do poniższego przykładu:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Teraz utwórz katalog, aby zainstalować `mkdir`system plików za pomocą programu . Poniższy przykład tworzy katalog w */datadrive:*

```bash
sudo mkdir /datadrive
```

Użyj, `mount` aby następnie zainstalować system plików. Poniższy przykład instaluje partycję */dev/sdc1* do punktu instalacji */datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk jest ponownie montowany automatycznie po ponownym uruchomieniu komputera, należy go dodać do pliku */etc/fstab.* Zaleca się również, aby UUID (Universally Unique IDentifier) był używany w */etc/fstab* w odniesieniu do dysku, a nie tylko do nazwy urządzenia *(np. /dev/sdc1).* Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, w przypadku używania identyfikatora UUID w danej lokalizacji nie zostanie zainstalowany nieprawidłowy dysk. Do pozostałych dysków danych zostałyby w takiej sytuacji przypisane te same identyfikatory urządzeń. Identyfikator UUID nowego dysku możesz znaleźć za pomocą narzędzia `blkid`:

```bash
sudo blkid
```

Dane wyjściowe wyglądają podobnie do następującego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nieprawidłowa edycja pliku **/etc/fstab** może spowodować nierozprzestłowienie systemu. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Zaleca się również utworzenie kopii zapasowej pliku /etc/fstab przed edycją.

Następnie otwórz plik */etc/fstab* w edytorze tekstu w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie należy użyć wartości UUID dla urządzenia */dev/sdc1* utworzonego w poprzednich krokach i punktu instalacji */datadrive*. Dodaj następujący wiersz na końcu pliku */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować, że maszyna wirtualna nie uruchomi się. Większość dystrybucji zapewnia opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają uruchomienie systemu, nawet jeśli dysk nie może zamontować w czasie rozruchu. Więcej informacji na temat tych parametrów można znaleźć w dokumentacji dystrybucji.
>
> Opcja *nofail* zapewnia uruchomienie maszyny Wirtualnej, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić zachowanie opisane w [nie można SSH do Linux VM z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Konsola szeregowa maszyny Wirtualnej platformy Azure może służyć do dostępu konsoli do maszyny Wirtualnej, jeśli modyfikacja fstab spowodowało niepowodzenie rozruchu. Więcej szczegółów można znaleźć w [dokumentacji konsoli szeregowej.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

### <a name="trimunmap-support-for-linux-in-azure"></a>Obsługa systemu TRIM/UNMAP dla systemu Linux na platformie Azure
Niektóre jądra Linuksa obsługują operacje TRIM/UNMAP, aby odrzucić nieużywane bloki na dysku. Ta funkcja jest przede wszystkim przydatne w magazynie standardowym, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą zostać odrzucone, a także można zaoszczędzić pieniądze, jeśli utworzysz duże pliki, a następnie je usuniesz.

Istnieją dwa sposoby włączania obsługi TRIM w maszynie wirtualnej z systemem Linux. Jak zwykle, skonsultuj się z dystrybucją, aby uzyskać zalecane podejście:

* Użyj `discard` opcji montowania w */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* W niektórych `discard` przypadkach opcja może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab, aby działać regularnie:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Następne kroki

* Aby upewnić się, że maszyna wirtualna z systemem Linux jest poprawnie skonfigurowana, zapoznaj się z zaleceniami [optymalizuj wydajność komputera z systemem Linux.](optimization.md)
* Zwiększ pojemność pamięci masowej, dodając dodatkowe dyski i [skonfiguruj macierz RAID](configure-raid.md) pod kątem dodatkowej wydajności.
