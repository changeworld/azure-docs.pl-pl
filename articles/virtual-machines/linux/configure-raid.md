---
title: Należy skonfigurować oprogramowanie RAID na maszynie wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować RAID w systemie Linux na platformie Azure przy użyciu mdadm.
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
ms.openlocfilehash: d6e831692da37645e264c6674f1ba54bb16d25d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911761"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurowanie macierzy RAID oprogramowania w systemie Linux
Jest to typowy scenariusz, aby korzystać z oprogramowania RAID na maszynach wirtualnych systemu Linux na platformie Azure można prezentować wiele dysków dołączonych danych jako pojedynczego urządzenia RAID. Zazwyczaj może być używany do zwiększenia wydajności i umożliwiają lepsze przepustowości w porównaniu do przy użyciu jednego dysku.

## <a name="attaching-data-disks"></a>Dołączanie dysków z danymi
Co najmniej dwa dyski danych puste są potrzebne do skonfigurowania urządzenie RAID.  Jest główną przyczyną tworzenia urządzenia RAID poprawić wydajność We/Wy dysku.  Oparte na potrzeby operacji We/Wy, można dołączać dysków, które są przechowywane w naszym Standard Storage z maksymalnie 500 we/wy/ps na dysku lub naszych Premium storage z maksymalnie 5000 ps/we/wy na dysk. W tym artykule nie przejść do szczegółów na temat obsługi administracyjnej i dołączać dysków z danymi do maszyny wirtualnej systemu Linux.  Zapoznaj się z artykułem Microsoft Azure [podłączyć dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe informacje o tym, jak można dołączyć dysku danych puste do maszyny wirtualnej systemu Linux na platformie Azure.

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
W tym przykładzie tworzymy partycji jednego dysku na /dev/sdc. /Dev/sdc1 zostanie wywołana nową partycję dysku.

1. Uruchom `fdisk` aby rozpocząć tworzenie partycji

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

2. Naciśnij przycisk "n" w wierszu polecenia, aby utworzyć **n**wa partycji:

    ```bash
    Command (m for help): n
    ```

3. Następnie naciśnij klawisz p, aby utworzyć **p**zosta partycji:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Naciśnij "1", aby wybrać numer partycji 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Wybierz punkt początkowy nową partycję, lub naciśnij przycisk `<enter>` aby zaakceptować ustawienie domyślne można umieścić na początku ilość wolnego miejsca na dysku partycji:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Wybierz rozmiar partycji, na przykład wpisz +10G, aby utworzyć partycję 10 GB. Możesz również nacisnąć klawisz `<enter>` Utwórz jedną partycję, która obejmuje cały dysk:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Następnie należy zmienić identyfikator i **t**typ partycji z domyślny identyfikator "83" (Linux) do Identyfikatora "fd" (Linux raid automatycznie):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Na koniec zapisu tabeli partycji na dysku i zamknąć fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Tworzenie macierzy RAID
1. Następujący przykład będzie "stripe" (poziom RAID 0) trzy partycji znajdujących się na trzy osobne dyski z danymi (sdc1, sdd1, sde1).  Po wywołaniu metody uruchomi to polecenie nowe urządzenie RAID **/dev/md127** jest tworzony. Należy również zauważyć, że jeśli dane te dyski możemy wcześniej częścią innego unieczynnienia macierzy RAID może być konieczne dodanie `--force` parametr `mdadm` polecenia:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Utwórz system plików na nowe urządzenie RAID
   
    a. **CentOS, Oracle Linux SLES 12, openSUSE i Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** — umożliwiają boot.md i utworzenie mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Po wprowadzeniu zmian w systemie SUSE może być wymagane ponowne uruchomienie komputera. Ten krok jest *nie* wymagane na SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do /etc/fstab
> [!IMPORTANT]
> Nieprawidłowo edytowania pliku /etc/fstab może spowodować rozruch systemu. Jeśli nie wiesz, można znaleźć dystrybucji dokumentacji, aby uzyskać informacje dotyczące prawidłowo edytować ten plik. Zalecane jest również, że kopia zapasowa pliku /etc/fstab został utworzony przed przystąpieniem do edytowania.

1. Utwórz punkt instalacji odpowiednią dla nowego systemu plików, na przykład:

    ```bash
    sudo mkdir /data
    ```
2. Podczas edytowania /etc/fstab, **UUID** należy odwoływać się do systemu plików, a nie do nazwy urządzenia.  Użyj `blkid` narzędzie, aby określić identyfikator UUID dla nowego systemu plików:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Otwórz /etc/fstab w edytorze tekstów i Dodaj wpis dla nowego systemu plików, na przykład:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Lub na **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Następnie zapisz i zamknij /etc/fstab.

4. Testowanie, czy/etc/fstab wpis jest poprawny:

    ```bash  
    sudo mount -a
    ```

    Jeśli to polecenie powoduje komunikat o błędzie, Sprawdź składnię pliku /etc/fstab.
   
    Uruchom `mount` polecenie, aby upewnić się, systemu plików:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Opcjonalnie) Parametry przed uszkodzeniami rozruchu
   
    **Konfiguracja fstab**
   
    Zawiera wiele dystrybucji `nobootwait` lub `nofail` parametrów, które mogą być dodawane do pliku/etc/fstab instalacji. Te parametry Zezwalaj na wypadek awarii w przypadku instalowania w określonym systemie plików i Zezwalaj na systemie Linux, aby kontynuować do rozruchu, nawet jeśli nie można poprawnie zainstalować system plików woluminu macierzy RAID. Zajrzyj do dokumentacji programu dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.
   
    Przykład (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametry rozruchu systemu Linux**
   
    Oprócz powyższych parametrów parametru jądra "`bootdegraded=true`" można zezwolić na system przeprowadzić rozruch nawet wtedy, gdy RAID jest traktowany jako uszkodzony lub obniżeniem, aby uzyskać przykład, jeśli dysk danych zostanie przypadkowo usunięty z maszyny wirtualnej. Domyślnie to może również spowodować-rozruchowy systemu.
   
    Zapoznaj się z dystrybucji dokumentacji na temat sposobu prawidłowo Edytuj parametry jądra. Na przykład w wiele dystrybucji (CentOS, Oracle Linux SLES 11) tych parametrów można dodać ręcznie do "`/boot/grub/menu.lst`" pliku.  Na Ubuntu można dodać do tego parametru `GRUB_CMDLINE_LINUX_DEFAULT` zmiennej na "/ etc/domyślne/chodników".


## <a name="trimunmap-support"></a>PRZYCINANIE/UNMAP pomocy technicznej
Niektóre jądra systemu Linux obsługują PRZYCINANIE/UNMAP operacji Odrzuć nieużywanych bloków na dysku. Operacje te są szczególnie przydatna w standardowe magazynu Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie strony można zapisać kosztów, jeśli Tworzenie dużych plików, a następnie usuń je.

> [!NOTE]
> RAID nie mogą wystawiać odrzucenia polecenia, jeśli rozmiar segmentu tablicy ma ustawioną wartość mniejszą niż domyślne (512KB). Jest to spowodowane szczegółowości unmap na hoście jest również 512KB. Jeśli zmodyfikowano rozmiar fragmentu tablicy za pomocą jego mdadm `--chunk=` parametr, a następnie usuń mapowanie/PRZYCINANIE żądań można zignorować przez jądro.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji zalecane podejście:

- Użyj `discard` zainstalować opcję `/etc/fstab`, na przykład:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` ręcznie polecenie w wierszu polecenia lub dodać go do Twojego crontab regularnego uruchamiania:

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
