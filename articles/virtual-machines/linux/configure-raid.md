---
title: Konfigurowanie programowej macierzy RAID na maszynie wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą mdadm konfigurowanie macierzy RAID w systemie Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: e773fdcb031f0f8f896ea40d76231fd54a603dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328803"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurowanie macierzy RAID oprogramowania w systemie Linux
To typowy scenariusz używać macierzy RAID oprogramowania w maszynach wirtualnych systemu Linux na platformie Azure można prezentować wiele dołączonych dysków z danymi w postaci na jednym urządzeniu RAID. Zazwyczaj ten może służyć do zwiększenia wydajności i umożliwienia ulepszoną przepływność w porównaniu z użyciem jednego dysku.

## <a name="attaching-data-disks"></a>Dołączanie dysków danych
Co najmniej dwóch dysków z danymi puste są wymagane do skonfigurowania urządzenia RAID.  Głównym powodem tworzenia urządzenia RAID to poprawić wydajność operacji We/Wy dysku.  Zgodnie z potrzebami we/wy, można dołączyć dyski, które są przechowywane w naszych magazynu w warstwie standardowa, z maksymalnie 500 operacji We/Wy/ps na dysku lub naszej usługi Premium storage przy użyciu maksymalnie 5000 operacji We/Wy/ps na dysk. W tym artykule nie przechodzi do szczegółów na temat sposobu obsługi administracyjnej i dołączanie dysków danych do maszyny wirtualnej systemu Linux.  Zapoznaj się z artykułem Microsoft Azure [dołączanie dysku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe informacje na temat sposobu dołączanie pustego dysku danych do maszyny wirtualnej z systemem Linux na platformie Azure.

## <a name="install-the-mdadm-utility"></a>Zainstaluj narzędzie mdadm
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS i Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES i openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Tworzenie partycji dysku
W tym przykładzie utworzymy partition jednego dysku na /dev/sdc. /Dev/sdc1 zostanie wywołana nowej partycji dysku.

1. Rozpocznij `fdisk` umożliwiającą tworzenie partycji

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

1. Naciśnij klawisz "n" w wierszu, aby utworzyć **n**partycji nowy:

    ```bash
    Command (m for help): n
    ```

1. Następnie naciśnij klawisz p, aby utworzyć **p**zosta partycji:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Naciśnij "1", aby wybrać partycji o numerze 1:

    ```bash
    Partition number (1-4): 1
    ```

1. Wybierz punkt początkowy nowej partycji lub naciśnij klawisz `<enter>` aby zaakceptować wartości domyślne, aby umieścić partycji na początku ilość wolnego miejsca na dysku:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Wybierz rozmiar partycji, na przykład wpisz +10G, aby utworzyć partycję 10 GB. Lub naciśnij `<enter>` utworzyć jedną partycję, która obejmuje cały dysk:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Następnie należy zmienić identyfikator i **t**yp partycji z domyślny identyfikator "83" (Linux) do Identyfikatora "fd" (Linux raid automatycznie):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Na koniec zapisać tabeli partycji na dysku i zakończyć fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Tworzenie macierzy RAID
1. Poniższy przykład spowoduje "stripe" (RAID: poziom 0) trzy partycje znajdujących się na trzy osobne dyski z danymi (sdc1 sdd1, sde1).  Po uruchomieniu tego polecenia nowe urządzenie RAID **/dev/md127** zostanie utworzony. Należy również zauważyć, że jeśli te dyski danych możemy wcześniej częścią innej macierzy RAID unieczynnienia może być konieczne dodanie `--force` parametr `mdadm` polecenia:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Tworzenie systemu plików na nowym urządzeniu RAID
   
    a. **CentOS, Oracle Linux, systemu SLES 12, openSUSE i Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** — Włączanie boot.md i tworzenie mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po wprowadzeniu zmian w systemach SUSE może być konieczne ponowne uruchomienie komputera. Ten krok jest *nie* wymagane w przypadku systemu SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do/etc/fstab
> [!IMPORTANT]
> Nieprawidłowo edycji pliku/etc/fstab może spowodować rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat sposobu prawidłowo edytować ten plik. Zalecane jest również, czy kopia zapasowa pliku/etc/fstab został utworzony przed rozpoczęciem edycji.

1. Na przykład utworzenia punktu instalacji żądaną nowy system plików:

    ```bash
    sudo mkdir /data
    ```
1. Podczas edycji/etc/fstab, **UUID** powinna być używana do odwołania, system plików, a nie nazwę urządzenia.  Użyj `blkid` narzędzie w celu określenia identyfikatora UUID dla nowego systemu plików:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Otwórz/etc/fstab w edytorze tekstów i Dodaj wpis dla nowego systemu plików, na przykład:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Lub na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Następnie zapisz i zamknij/etc/fstab.

1. Testowanie, czy/etc/fstab wpis jest poprawny:

    ```bash  
    sudo mount -a
    ```

    Jeśli to polecenie zwróci komunikat o błędzie, sprawdź, czy w składni plik/etc/fstab.
   
    Następne uruchomienie `mount` polecenie, aby upewnić się, jest zainstalowany system plików:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Opcjonalnie) Przed uszkodzeniami rozruchu parametrów
   
    **Konfiguracja fstab**
   
    Włącz wiele dystrybucji `nobootwait` lub `nofail` instalacji parametry, które mogą być dodawane do pliku/etc/fstab. Parametry te umożliwiają błędy w przypadku instalowania w określonym systemie plików i umożliwiają systemu Linux przejść do rozruchu, nawet jeśli nie można poprawnie zainstalować system plików woluminu macierzy RAID. Zapoznaj się z dokumentacją danej dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.
   
    Przykład (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux rozruchu parametrów**
   
    Oprócz powyższych parametrów parametr jądra "`bootdegraded=true`" umożliwia systemowi rozruch nawet wtedy, gdy RAID jest traktowany jako uszkodzony lub obniżonej wydajności, aby uzyskać przykład, jeśli dysk danych zostanie przypadkowo usunięty z maszyny wirtualnej. Domyślnie ta może również spowodować uniemożliwiającego systemu.
   
    Zapoznaj się dokumentacją danej dystrybucji na temat edytowania prawidłowo jądra parametry. Na przykład w wiele dystrybucji (CentOS, Oracle Linux SLES 11) te parametry mogą być dodawane ręcznie do "`/boot/grub/menu.lst`" pliku.  W systemie Ubuntu tego parametru można dodać do `GRUB_CMDLINE_LINUX_DEFAULT` zmiennej na "/ etc/domyślne/chodników".


## <a name="trimunmap-support"></a>TRIM/UNMAP pomocy technicznej
Niektóre jądra systemu Linux obsługuje TRIM/UNMAP operacji można odrzucić nieużywanych bloków na dysku. Operacje te są szczególnie przydatne w magazynie standard storage do informowania platformy Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucenie strony można zapisać kosztów, jeśli utworzysz dużych plików, a następnie je usunąć.

> [!NOTE]
> RAID nie mogą wystawiać polecenia odrzucenia, jeśli rozmiar fragmentu dla tablicy jest ustawiona na wartość mniejszą niż domyślna (512KB). Jest to spowodowane szczegółowości unmap na hoście jest również 512KB. Jeśli został zmodyfikowany rozmiaru fragmentu tablicy za pośrednictwem firmy mdadm `--chunk=` parametru, a następnie usuń mapowanie/TRIM żądań może być ignorowane przez jądro.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji Zalecanym podejściem:

- Użyj `discard` instalacji opcji `/etc/fstab`, na przykład:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie, możesz uruchomić `fstrim` polecenie ręcznie z poziomu wiersza polecenia lub dodać do swojej crontab regularnego uruchamiania:

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
