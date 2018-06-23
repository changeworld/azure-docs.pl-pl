---
title: Dodaj dysk danych do maszyny Wirtualnej systemu Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się dodać dysk danych trwałych do maszyny Wirtualnej systemu Linux z platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41090943e4053ddf0ea46e9da1b3b5c7dbbf132
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331227"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Dodawanie dysku do maszyny wirtualnej z systemem Linux
W tym artykule przedstawiono sposób dołączyć trwałe dysku do maszyny Wirtualnej, dzięki czemu można zachować dane — nawet wtedy, gdy maszyna wirtualna jest ponownie udostępnić z powodu konserwacji lub zmiany rozmiaru. 


## <a name="attach-a-new-disk-to-a-vm"></a>Dołączanie nowego dysku do maszyny Wirtualnej

Jeśli chcesz dodać dysk danych nowy, pusty na maszynie Wirtualnej, użyj [dołączyć dysku maszyny wirtualnej az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) z `--new` parametru. Jeśli maszyna wirtualna jest w strefie dostępności, dysk zostanie automatycznie utworzone w tej samej strefie jako maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Przegląd stref dostępności](../../availability-zones/az-overview.md). Poniższy przykład tworzy dysk o nazwie *myDataDisk* oznacza to 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku 

Aby dołączyć istniejącego dysku, Znajdź identyfikator dysku i przekaż identyfikator [dołączyć dysku maszyny wirtualnej az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) polecenia. Następujące przykładowe zapytania dla dysku o nazwie *myDataDisk* w *myResourceGroup*, dołącza go do maszyny Wirtualnej o nazwie *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Połączenie z maszyną Wirtualną systemu Linux do zainstalowania nowego dysku
Na partycje, formatowania i instalacji nowego dysku, więc maszyny Wirtualnej systemu Linux może być używany, nawiąż połączenie z maszyną Wirtualną. Aby uzyskać więcej informacji, zobacz temat dotyczący [korzystania z protokołu SSH systemu Linux na platformie Azure](mac-create-ssh-keys.md). Poniższy przykład nawiązuje połączenie z maszyną wirtualną z publicznego wpisu DNS *mypublicdns.westus.cloudapp.azure.com* nazwy użytkownika *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Po nawiązaniu połączenia z maszyną Wirtualną, możesz dołączyć dysku. Po pierwsze Znajdź przy użyciu dysku `dmesg` (metody używane do wykrywania nowego dysku może się różnić). W poniższym przykładzie użyto dmesg do filtrowania *SCSI* dysków:

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

W tym miejscu *sdc* dysku, która ma. Partycji dysku z `fdisk`był dysk podstawowy na partycji 1 i zaakceptuj ustawienia domyślne. W następującym przykładzie uruchomiono `fdisk` proces */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Użyj `n` polecenie, aby dodać nową partycję. W tym przykładzie mamy także `p` dla podstawowego Podziel na partycje i zaakceptuj pozostałe wartości domyślne. Dane wyjściowe będą podobne do poniższego przykładu:

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

Drukowanie tabeli partycji, wpisując `p` , a następnie użyj `w` można zapisać tabeli na dysk i zakończyć. Dane wyjściowe powinny wyglądać podobnie do poniższego przykładu:

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

Teraz zapisać systemu plików z partycją `mkfs` polecenia. Określ typ swojego systemu plików i nazwę urządzenia. Poniższy przykład tworzy *ext4* system plików na */dev/sdc1* partycji, który został utworzony w poprzednich krokach:

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

Teraz Utwórz katalog należy zainstalować system plików przy użyciu `mkdir`. Poniższy przykład tworzy katalog w */datadrive*:

```bash
sudo mkdir /datadrive
```

Użyj `mount` następnie zainstalować system plików. Poniższy przykład instaluje */dev/sdc1* partycji do */datadrive* punktu instalacji:

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk jest instalacja przeprowadzona automatycznie po ponownym uruchomieniu, musi zostać dodany do */etc/fstab* pliku. Również zdecydowanie zaleca się, że identyfikator UUID (powszechnie Unikatowy identyfikator) jest używana w */etc/fstab* do odwoływania się do stacji, a nie tylko nazwę urządzenia (takich jak */dev/sdc1*). Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, przy użyciu identyfikatora UUID pozwala uniknąć niepoprawne dysku są zainstalowane w danej lokalizacji. Pozostałe dyski danych może następnie można przypisać te takich samych identyfikatorów urządzenia. Aby znaleźć identyfikator UUID nowy dysk, użyj `blkid` narzędzie:

```bash
sudo -i blkid
```

Dane wyjściowe wygląda podobnie do poniższego przykładu:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Nieprawidłowo edycji **/etc/fstab** pliku może spowodować rozruch systemu. Jeśli nie wiesz, można znaleźć dystrybucji dokumentacji, aby uzyskać informacje dotyczące prawidłowo edytować ten plik. Zalecane jest również, że kopia zapasowa pliku /etc/fstab został utworzony przed przystąpieniem do edytowania.

Następnie otwórz folder */etc/fstab* plik w edytorze tekstów w następujący sposób:

```bash
sudo vi /etc/fstab
```

W tym przykładzie należy użyć wartości identyfikatora UUID dla */dev/sdc1* urządzenia, który został utworzony w poprzednich krokach i punktu instalacji z */datadrive*. Dodaj następujący wiersz na końcu */etc/fstab* pliku:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Później usunąć dysk z danymi bez konieczności edytowania fstab może spowodować niepowodzenie do rozruchu maszyny Wirtualnej. Większości dystrybucji podać *nofail* i/lub *nobootwait* fstab opcje. Te opcje umożliwiają systemu przeprowadzić rozruch nawet w przypadku awarii dysku do zainstalowania w czasie rozruchu. Dokumentacja programu dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.
> 
> *Nofail* opcji zapewnia, że maszyna wirtualna zacznie nawet wtedy, gdy system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji, możesz napotkać zachowanie zgodnie z opisem w [nie SSH do maszyny Wirtualnej systemu Linux z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>PRZYCINANIE/UNMAP obsługę systemu Linux na platformie Azure
Niektóre jądra systemu Linux obsługują PRZYCINANIE/UNMAP operacji Odrzuć nieużywanych bloków na dysku. Ta funkcja jest szczególnie przydatna w standardowe magazynu Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone i można oszczędzić pieniądze, jeśli Tworzenie dużych plików, a następnie usuń je.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji zalecane podejście:

* Użyj `discard` zainstalować opcję */etc/fstab*, na przykład:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` ręcznie polecenie w wierszu polecenia lub dodać go do Twojego crontab regularnego uruchamiania:
  
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
* Aby maszyny Wirtualnej systemu Linux jest skonfigurowany prawidłowo, przejrzeć [maszyny Linux Optymalizowanie](optimization.md) zalecenia.
* Rozwiń węzeł pojemności pamięci masowej przez dodanie dodatkowych dysków i [skonfigurować RAID](configure-raid.md) dla wyższą wydajność.

