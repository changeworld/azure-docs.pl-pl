---
title: Dołączanie dysku danych do maszyny Wirtualnej systemu Linux
description: Użyj portalu, aby dołączyć nowy lub istniejący dysk danych do maszyny Wirtualnej z systemem Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969350"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Dołączanie dysku danych do maszyny Wirtualnej z systemem Linux za pomocą portalu 
W tym artykule pokazano, jak dołączyć zarówno nowe, jak i istniejące dyski do maszyny wirtualnej systemu Linux za pośrednictwem witryny Azure portal. Dysk danych można również [dołączyć do maszyny Wirtualnej systemu Windows w witrynie Azure portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Przed dołączeniem dysków do maszyny Wirtualnej zapoznaj się z tymi wskazówkami:

* Rozmiar maszyny wirtualnej określa, ile dysków danych można dołączyć. Aby uzyskać szczegółowe informacje, zobacz [Rozmiary maszyn wirtualnych](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dyski dołączone do maszyn wirtualnych są w rzeczywistości plikami vhd przechowywanymi na platformie Azure. Aby uzyskać szczegółowe informacje, zobacz nasze [Wprowadzenie do dysków zarządzanych](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Po podłączeniu dysku należy [połączyć się z maszyną wirtualną z systemem Linux, aby zamontować nowy dysk](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Znajdź maszynę wirtualną
1. Przejdź do [witryny Azure portal,](https://portal.azure.com/) aby znaleźć maszynę wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.
2. Wybierz maszynę wirtualną z listy.
3. Na pasku bocznym strony **Maszyny wirtualne** w obszarze **Ustawienia**wybierz pozycję **Dyski**.
   
    ![Otwieranie ustawień dysku](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Dołączanie nowego dysku

1. W okienku **Dyski** kliknij pozycję **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane **Nazwa** i wybierz pozycję **Utwórz dysk:**

    ![Tworzenie dysku zarządzanego platformy Azure](./media/attach-disk-portal/create-new-md.png)

3. Wprowadź nazwę dysku zarządzanego. Przejrzyj ustawienia domyślne, zaktualizuj w razie potrzeby, a następnie kliknij przycisk **Utwórz**.
   
   ![Przeglądanie ustawień dysku](./media/attach-disk-portal/create-new-md-settings.png)

4. Kliknij **przycisk Zapisz,** aby utworzyć dysk zarządzany i zaktualizować konfigurację maszyny Wirtualnej:

   ![Zapisz nowy dysk zarządzany platformy Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Po tym, jak platforma Azure utworzy dysk i dołączy go do maszyny wirtualnej, nowy dysk zostanie wyświetlony w ustawieniach dysku maszyny wirtualnej w obszarze **Dyski danych**. Ponieważ dyski zarządzane są zasobem najwyższego poziomu, dysk jest wyświetlany w katalogu głównym grupy zasobów:

   ![Dysk zarządzany platformy Azure w grupie zasobów](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
1. W okienku **Dyski** kliknij pozycję **+ Dodaj dysk danych**.
2. Kliknij menu rozwijane **Name,** aby wyświetlić listę istniejących dysków zarządzanych dostępnych dla subskrypcji platformy Azure. Wybierz dysk zarządzany do dołączenia:

   ![Dołączanie istniejącego dysku zarządzanego platformy Azure](./media/attach-disk-portal/select-existing-md.png)

3. Kliknij **przycisk Zapisz,** aby dołączyć istniejący dysk zarządzany i zaktualizować konfigurację maszyny Wirtualnej:
   
   ![Zapisywanie aktualizacji dysku zarządzanego platformy Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Po dołączeniu dysku przez platformę Azure do maszyny wirtualnej jest on wyświetlany w ustawieniach dysku maszyny wirtualnej w obszarze **Dyski danych**.

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

Tutaj *sdc* to dysk, który chcemy. 

### <a name="partition-a-new-disk"></a>Partycjonowanie nowego dysku
Jeśli używasz istniejącego dysku zawierającego dane, przejdź do montażu dysku. Jeśli podłączasz nowy dysk, musisz podzielić dysk na partycje.

> [!NOTE]
> Zaleca się używanie najnowszych wersji fdisk lub parted, które są dostępne dla twojej dystrybucji.

Podziel dysk na partycje za pomocą polecenia `fdisk`. Jeśli rozmiar dysku jest 2 tebibajtów (TiB) lub większy, należy `parted` użyć partycjonowania GPT, można użyć do wykonywania partycjonowania GPT. Jeśli rozmiar dysku jest poniżej 2TiB, można użyć partycjonowania MBR lub GPT. Ustaw dysk podstawowy na partycji 1 i zaakceptuj inne ustawienia domyślne. Poniższy przykład rozpoczyna `fdisk` proces na */dev/sdc:*

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

#### <a name="alternate-method-using-parted"></a>Metoda alternatywna przy użyciu części
Narzędzie fdisk wymaga interaktywnego wkładu, a zatem nie jest idealne do użytku w skryptach automatyzacji. Jednak [rozdzielone](https://www.gnu.org/software/parted/) narzędzie może być skryptowane, a tym samym nadaje się lepiej w scenariuszach automatyzacji. Narzędzie parted może służyć do partycjonowania i formatowania dysku danych. W poniższym przewodniku używamy nowego dysku danych /dev/sdc i sformatujemy go za pomocą systemu plików [XFS.](https://xfs.wiki.kernel.org/)
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Jak widać powyżej, używamy narzędzia [partprobe,](https://linux.die.net/man/8/partprobe) aby upewnić się, że jądro jest natychmiast świadomy nowej partycji i systemu plików. Nieużycie partprobe może spowodować, że polecenia blkid lub lslbk nie zwracają UUID dla nowego systemu plików natychmiast.

### <a name="mount-the-disk"></a>Montowanie dysku
Utwórz katalog, aby zainstalować `mkdir`system plików przy użyciu programu . Poniższy przykład tworzy katalog w */datadrive:*

```bash
sudo mkdir /datadrive
```

Użyj, `mount` aby następnie zainstalować system plików. Poniższy przykład instaluje partycję */dev/sdc1* do punktu instalacji */datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

Aby upewnić się, że dysk jest ponownie montowany automatycznie po ponownym uruchomieniu komputera, należy go dodać do pliku */etc/fstab.* Zaleca się również, aby UUID (Universally Unique IDentifier) był używany w */etc/fstab* w odniesieniu do dysku, a nie tylko do nazwy urządzenia *(np. /dev/sdc1).* Jeśli system operacyjny wykryje błąd dysku podczas rozruchu, w przypadku używania identyfikatora UUID w danej lokalizacji nie zostanie zainstalowany nieprawidłowy dysk. Do pozostałych dysków danych zostałyby w takiej sytuacji przypisane te same identyfikatory urządzeń. Identyfikator UUID nowego dysku możesz znaleźć za pomocą narzędzia `blkid`:

```bash
sudo -i blkid
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
Po zakończeniu zapisz plik */etc/fstab* i uruchom ponownie system.
> [!NOTE]
> Późniejsze usunięcie dysku z danymi bez edytowania fstab może spowodować, że maszyna wirtualna nie uruchomi się. Większość dystrybucji zapewnia opcje *nofail* i/lub *nobootwait* fstab. Te opcje umożliwiają uruchomienie systemu, nawet jeśli dysk nie może zamontować w czasie rozruchu. Więcej informacji na temat tych parametrów można znaleźć w dokumentacji dystrybucji.
> 
> Opcja *nofail* zapewnia uruchomienie maszyny Wirtualnej, nawet jeśli system plików jest uszkodzony lub dysk nie istnieje w czasie rozruchu. Bez tej opcji może wystąpić zachowanie opisane w [nie można SSH do Linux VM z powodu błędów FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

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

## <a name="next-steps"></a>Następne kroki
Można również [dołączyć dysk danych](add-disk.md) przy użyciu interfejsu wiersza polecenia platformy Azure.
