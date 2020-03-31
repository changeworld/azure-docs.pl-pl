---
title: Konfigurowanie macierzy RAID oprogramowania na maszynie wirtualnej w systemie Linux
description: Dowiedz się, jak skonfigurować raid na systemie Linux na platformie Azure za pomocą mdadm.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250259"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurowanie macierzy RAID oprogramowania w systemie Linux
Jest to typowy scenariusz, aby użyć macierzy RAID oprogramowania na maszynach wirtualnych systemu Linux na platformie Azure do przedstawienia wielu dołączonych dysków danych jako pojedynczego urządzenia RAID. Zazwyczaj może to służyć do zwiększenia wydajności i umożliwić lepszą przepływność w porównaniu do korzystania tylko z jednego dysku.

## <a name="attaching-data-disks"></a>Dołączanie dysków z danymi
Do skonfigurowania urządzenia RAID potrzebne są co najmniej dwa puste dyski z danymi.  Głównym powodem utworzenia urządzenia RAID jest zwiększenie wydajności we/wy dysku.  W zależności od potrzeb we/wy możesz dołączyć dyski przechowywane w naszym standardowym magazynie, z maksymalnie 500 we/wy na dysk lub naszą pamięć masową Premium z maksymalnie 5000 we/wy na dysk. W tym artykule nie zawiera szczegółowych informacji na temat sposobu aprowizowania i dołączania dysków danych do maszyny wirtualnej systemu Linux.  Zobacz artykuł microsoft azure [dołączyć dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe instrukcje dotyczące podłączania pustego dysku danych do maszyny wirtualnej systemu Linux na platformie Azure.

> [!IMPORTANT]
>Nie mieszaj dysków o różnych rozmiarach, co spowodowałoby, że wydajność zestawu raidset jest ograniczona do wydajności najwolniejszego dysku. 

## <a name="install-the-mdadm-utility"></a>Zainstaluj narzędzie mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES i openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Tworzenie partycji dysku
W tym przykładzie tworzymy partycję pojedynczego dysku na /dev/sdc. Nowa partycja dysku będzie nazywana /dev/sdc1.

1. Rozpoczynanie `fdisk` tworzenia partycji

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Naciśnij przycisk 'n' w wierszu, aby utworzyć partycję **n**ew:

    ```bash
    Command (m for help): n
    ```

1. Następnie naciśnij 'p', aby utworzyć partycję rimary **p:**

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Naciśnij "1", aby wybrać partycję numer 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Wybierz punkt początkowy nowej partycji `<enter>` lub naciśnij, aby zaakceptować wartość domyślną, aby umieścić partycję na początku wolnego miejsca na dysku:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Wybierz rozmiar partycji, na przykład typ "+10G", aby utworzyć partycję 10 gigabajtów. Możesz też `<enter>` nacisnąć przycisk Utwórz pojedynczą partycję obejmującą cały dysk:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Następnie zmień identyfikator i **t**ype partycji z domyślnego identyfikatora '83' (Linux) na ID 'fd' (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Na koniec zapisz tabelę partycji na dysku i zamknij fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Tworzenie macierzy RAID
1. Poniższy przykład będzie "paski" (raid poziom 0) trzy partycje znajdujące się na trzech oddzielnych dysków danych (sdc1, sdd1, sde1).  Po uruchomieniu tego polecenia tworzone jest nowe urządzenie RAID o nazwie **/dev/md127.** Należy również zauważyć, że jeśli te dyski danych, które wcześniej części innego `--force` nieistniejącego `mdadm` macierzy RAID może być konieczne, aby dodać parametr do polecenia:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Tworzenie systemu plików na nowym urządzeniu RAID
   
    **CentOS, Oracle Linux, SLES 12, openSUSE i Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - włącz boot.md i tworzenie mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po dokonaniu tych zmian w systemach SUSE może być wymagane ponowne uruchomienie komputera. Ten krok *nie* jest wymagany w SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do /etc/fstab
> [!IMPORTANT]
> Nieprawidłowa edycja pliku /etc/fstab może spowodować nierozprzestłowienie systemu. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Zaleca się również utworzenie kopii zapasowej pliku /etc/fstab przed edycją.

1. Utwórz żądany punkt instalacji dla nowego systemu plików, na przykład:

    ```bash
    sudo mkdir /data
    ```
1. Podczas edycji /etc/fstab **identyfikator UUID** powinien być używany do odwoływania się do systemu plików, a nie do nazwy urządzenia.  Użyj `blkid` narzędzia, aby określić UUID dla nowego systemu plików:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Otwórz /etc/fstab w edytorze tekstu i dodaj wpis dla nowego systemu plików, na przykład:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Lub na **SLES 11:**

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Następnie zapisz i zamknij /etc/fstab.

1. Sprawdź, czy wpis /etc/fstab jest poprawny:

    ```bash  
    sudo mount -a
    ```

    Jeśli to polecenie powoduje komunikat o błędzie, sprawdź składnię w pliku /etc/fstab.
   
    Następnie uruchom `mount` polecenie, aby upewnić się, że system plików jest zamontowany:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcjonalnie) Failsafe parametry rozruchu
   
    **konfiguracja fstab**
   
    Wiele dystrybucji zawiera parametry `nobootwait` `nofail` lub instalację, które mogą zostać dodane do pliku /etc/fstab. Parametry te pozwalają na błędy podczas montażu określonego systemu plików i pozwalają systemowi Linux kontynuować rozruch, nawet jeśli nie jest w stanie prawidłowo zamontować systemu plików RAID. Więcej informacji na temat tych parametrów można znaleźć w dokumentacji dystrybucji.
   
    Przykład (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametry rozruchu systemu Linux**
   
    Oprócz powyższych parametrów parametr jądra`bootdegraded=true`" " może pozwolić na uruchomienie systemu, nawet jeśli raid jest postrzegany jako uszkodzony lub zdegradowany, na przykład jeśli dysk danych zostanie przypadkowo usunięty z maszyny wirtualnej. Domyślnie może to również spowodować system nie rozruchowy.
   
    Zapoznaj się z dokumentacją dystrybucji dotyczącą prawidłowej edycji parametrów jądra. Na przykład w wielu dystrybucjach (CentOS, Oracle Linux, SLES 11) parametry`/boot/grub/menu.lst`te mogą być dodawane ręcznie do pliku " " .  Na Ubuntu ten parametr można `GRUB_CMDLINE_LINUX_DEFAULT` dodać do zmiennej na "/etc/default/grub".


## <a name="trimunmap-support"></a>Obsługa TRIM/UNMAP
Niektóre jądra Linuksa obsługują operacje TRIM/UNMAP, aby odrzucić nieużywane bloki na dysku. Te operacje są przydatne przede wszystkim w magazynie standardowym, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie stron może zaoszczędzić koszty, jeśli utworzysz duże pliki, a następnie je usuniesz.

> [!NOTE]
> Raid nie może wydawać polecenia odrzucania, jeśli rozmiar fragmentu dla tablicy jest ustawiony na mniej niż domyślny (512 KB). Jest tak, ponieważ ziarnistość unmap na hoście jest również 512KB. Jeśli rozmiar fragmentu tablicy został zmodyfikowany za `--chunk=` pomocą parametru mdadm, żądania TRIM/unmap mogą zostać zignorowane przez jądro.

Istnieją dwa sposoby włączania obsługi TRIM w maszynie wirtualnej z systemem Linux. Jak zwykle, skonsultuj się z dystrybucją, aby uzyskać zalecane podejście:

- Użyj `discard` opcji montowania w `/etc/fstab`, na przykład:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- W niektórych `discard` przypadkach opcja może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab, aby działać regularnie:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
