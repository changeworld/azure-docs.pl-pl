---
title: Dodawanie dysku danych do maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się dodać dysk danych trwałych do maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 1c8d4d2b26b356c524523d73d53fd641eef5f3cb
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465837"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Dodawanie dysku do maszyny wirtualnej z systemem Linux
W tym artykule pokazano, jak dołączyć dysk trwały z maszyną wirtualną tak, aby zachować swoje dane — nawet wtedy, gdy maszyna wirtualna jest aprowizowany ponownie z powodu konserwacji lub zmienianie jej rozmiaru.


## <a name="attach-a-new-disk-to-a-vm"></a>Dołączyć nowy dysk do maszyny Wirtualnej

Jeśli chcesz dodać dysk z danymi nową, pustą na maszynie Wirtualnej, użyj [dołączanie dysku maszyny wirtualnej az](/cli/azure/vm/disk?view=azure-cli-latest) polecenia `--new` parametru. Jeśli maszyna wirtualna jest w strefie dostępności, dysk jest automatycznie tworzony w innej strefie niż maszyna wirtualna. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności](../../availability-zones/az-overview.md). W poniższym przykładzie tworzony jest dysk o nazwie *myDataDisk* oznacza to 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku

Można dołączyć istniejącego dysku, należy znaleźć identyfikator dysku i przekaż identyfikator, który ma [dołączanie dysku maszyny wirtualnej az](/cli/azure/vm/disk?view=azure-cli-latest) polecenia. Następujące przykładowe zapytania dla dysku o nazwie *myDataDisk* w *myResourceGroup*, dołącza go do maszyny Wirtualnej o nazwie *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Łączenie z maszyną Wirtualną systemu Linux zainstaluj nowy dysk

Partycjonowanie, formatowania i instalacji nowego dysku, dzięki czemu maszyny Wirtualnej systemu Linux można korzystać protokołu SSH z maszyną wirtualną. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład nawiązuje połączenie z maszyną wirtualną za pomocą publicznego wpis DNS *mypublicdns.westus.cloudapp.azure.com* nazwy użytkownika *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po nawiązaniu połączenia z maszyną wirtualną, możesz przystąpić do dołączenia dysku. Po pierwsze Znajdź dysk przy użyciu `dmesg` (metody odnajdywania nowego dysku mogą się różnić). W poniższym przykładzie użyto dmesg do filtrowania *SCSI* dyski:

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
> Zalecane jest, możesz używać najnowszych wersji fdisk lub ulec, które są dostępne dla Twojej dystrybucji.

W tym miejscu *sdc* to dysk, który chcemy. Określ partycję dysku za pomocą `parted`, jeśli rozmiar dysku wynosi 2 tebibajtów (TiB) lub większą, należy użyć partycji GPT, jeśli jest w obszarze 2TiB, wówczas można użyć partycji MBR lub GPT. Jeśli używasz partycji MBR, możesz użyć `fdisk`. Stał się podstawowym dysku na partycji 1, a następnie zaakceptuj ustawienia domyślne. Poniższy przykład rozpoczyna się `fdisk` proces */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Użyj `n` polecenie, aby dodać nową partycję. W tym przykładzie mamy też `p` dla podstawowego Podziel na partycje i zaakceptuj pozostałe wartości domyślne. Dane wyjściowe będą podobne do poniższego przykładu:

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

Drukowanie tabeli partycji, wpisując `p` , a następnie użyj `w` można zapisać tabeli na dysku, a następnie zakończyć pracę. Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu:

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
Użyj poniższego polecenia, aby zaktualizować Jądro:
```
partprobe 
```

Teraz Zapisz system plików z partycją `mkfs` polecenia. Określ typ swojego systemu plików, a nazwa urządzenia. Poniższy przykład tworzy *ext4* systemu plików na */dev/sdc1* partycji, który został utworzony w poprzednich krokach:

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

Teraz Utwórz katalog do instalacji systemu plików przy użyciu `mkdir`. Poniższy przykład tworzy katalog w */datadrive*:

```bash
sudo mkdir /datadrive
```

Użyj `mount` następnie zainstalować w systemie plików. Poniższy przykład instaluje */dev/sdc1* partycji do */datadrive* punktu instalacji:

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk zostanie ponownie zainstalowany automatycznie po ponownym uruchomieniu, należy dodać do */etc/fstab* pliku. Również zdecydowanie zaleca się, że identyfikator UUID (powszechnie Unikatowy identyfikator) jest używany w */etc/fstab* do odwoływania się do stacji, a nie tylko nazwy urządzenia (takich jak */dev/sdc1*). Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, za pomocą identyfikator UUID pozwala uniknąć nieprawidłową dysku montażu do danej lokalizacji. Pozostałe dyski danych będzie można przypisać te takich samych identyfikatorów urządzeń. Aby znaleźć identyfikator UUID nowy dysk, użyj `blkid` narzędzie:

```bash
sudo blkid
```

Dane wyjściowe wyglądają podobnie do poniższego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nieprawidłowo edycji **/etc/fstab** plików nieraz powodowały rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat sposobu prawidłowo edytować ten plik. Zalecane jest również, czy kopia zapasowa pliku/etc/fstab został utworzony przed rozpoczęciem edycji.

Następnie otwórz */etc/fstab* plik w edytorze tekstów w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie, użyj wartości identyfikatora UUID dla */dev/sdc1* urządzenia, który został utworzony w poprzednich krokach, a punkt instalacji programu */datadrive*. Dodaj następujący wiersz na końcu */etc/fstab* pliku:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Później usunięcie dysku danych bez konieczności edytowania fstab spowodować, że maszyna wirtualna może zakończyć się niepowodzeniem, rozruch. Większości dystrybucji zapewniają albo *nofail* i/lub *nobootwait* opcje fstab. Te opcje umożliwiają systemowi rozruchu, nawet w przypadku awarii dysku, należy zainstalować w czasie rozruchu. Zapoznaj się dokumentacją danej dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.
>
> *Nofail* opcja gwarantuje, że maszyna wirtualna zacznie nawet, jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji, możesz napotkać zachowanie zgodnie z opisem w [nie SSH do maszyny Wirtualnej systemu Linux z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> Konsoli szeregowej maszyny Wirtualnej platformy Azure może służyć do dostępu do konsoli dla maszyny wirtualnej, jeśli modyfikowanie fstab spowodowało błąd rozruchu. Więcej szczegółów znajduje się w [dokumentację konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP obsługę systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługuje TRIM/UNMAP operacji można odrzucić nieużywanych bloków na dysku. Ta funkcja jest szczególnie przydatne w magazynie standard storage platformy Azure, które usunięto strony nie są już prawidłowe i można odrzucać i może oszczędzać pieniądze, jeśli utworzysz dużych plików, a następnie je usunąć.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji Zalecanym podejściem:

* Użyj `discard` instalacji opcji */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie, możesz uruchomić `fstrim` polecenie ręcznie z poziomu wiersza polecenia lub dodać do swojej crontab regularnego uruchamiania:

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

## <a name="next-steps"></a>Kolejne kroki

* Aby upewnić się, maszyny Wirtualnej systemu Linux jest prawidłowo skonfigurowany, zapoznaj się z [zoptymalizowania wydajności systemu Linux maszyny](optimization.md) zalecenia.
* Zwiększyć pojemność przechowywania przez dodanie dodatkowych dysków i [konfigurowanie macierzy RAID](configure-raid.md) uzyskać wyższą wydajność.
