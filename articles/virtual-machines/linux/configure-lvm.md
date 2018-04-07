---
title: Skonfiguruj LVM na maszynie wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować LVM w systemie Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 9a22426d0422585714cb78d541a84d55d2fce6e0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Skonfiguruj LVM na Maszynę wirtualną systemu Linux na platformie Azure
Ten dokument przedstawimy Konfigurowanie menedżera woluminu logicznego (LVM) w sieci maszyny wirtualnej platformy Azure. Mimo że jest to możliwe, aby skonfigurować LVM na dowolny dysk dołączony do maszyny wirtualnej, domyślnie większość obrazów chmury nie będą miały LVM skonfigurowane na dysk systemu operacyjnego. Ma to zapobiec problemom z grup zduplikowanych woluminu, jeśli dysk systemu operacyjnego jest kiedykolwiek dołączona do innej maszyny Wirtualnej o takiej samej dystrybucji i typ, tj. podczas scenariusza odzyskiwania. Dlatego zalecane jest tylko do użycia LVM na dyskach danych.

## <a name="linear-vs-striped-logical-volumes"></a>Liniowego a woluminy rozłożone logiczne
LVM może służyć do łączenia liczbę dysków fizycznych w jednym woluminie magazynu. Domyślnie LVM zwykle utworzy liniowej woluminów logicznego, co oznacza, że łączone magazynu fizycznego. W takim przypadku operacji odczytu/zapisu zwykle tylko do wysyłany jest także jeden dysk. Z kolei można utworzyć woluminy rozłożone logiczne, które odczyty i zapisy są dystrybuowane do wielu dysków znajdujących się w grupie woluminu (tj. Podobnie RAID0). Ze względu na wydajność prawdopodobnie ma paskowych logicznej woluminów, tak że odczyty i zapisy wykorzystanie dysków dołączonych danych.

Tym dokumencie opisano sposób łączenia kilka dysków danych w grupie pojedynczy wolumin, a następnie utwórz woluminy rozłożone logiczne. Poniższe kroki są nieco uogólnione w celu pracy z większości dystrybucji. W większości przypadków narzędzia i przepływów pracy związanych z zarządzaniem LVM na platformie Azure nie są różni się od innych środowisk. W zwykły sposób również Przejrzyj dostawcą Linux dla dokumentacji i najlepsze rozwiązania dotyczące używania LVM z konkretnym dystrybucji.

## <a name="attaching-data-disks"></a>Dołączanie dysków z danymi
Jeden zazwyczaj można rozpoczynać się co najmniej dwa dyski pusty danych przy użyciu LVM. Oparte na potrzeby operacji We/Wy, można dołączać dysków, które są przechowywane w naszym Standard Storage z maksymalnie 500 we/wy/ps na dysku lub naszych Premium storage z maksymalnie 5000 ps/we/wy na dysk. W tym artykule nie przejdzie do szczegółów na temat obsługi administracyjnej i dołączać dysków z danymi do maszyny wirtualnej systemu Linux. Zobacz artykuł Microsoft Azure [podłączyć dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe informacje o tym, jak można dołączyć dysku danych puste do maszyny wirtualnej systemu Linux na platformie Azure.

## <a name="install-the-lvm-utilities"></a>Zainstaluj narzędzia LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS i Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 i openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    Na SLES11 trzeba również edytować `/etc/sysconfig/lvm` i ustaw `LVM_ACTIVATED_ON_DISCOVERED` do "Włącz":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurowanie maszyny wirtualnej z systemem Linux
W tym przewodniku zakładamy, że dołączono trzy dyski danych, które będzie nazywamy `/dev/sdc`, `/dev/sdd` i `/dev/sde`. Należy pamiętać, że mogą one nie zawsze być tej samej nazwy ścieżki maszyny Wirtualnej. Można uruchomić "`sudo fdisk -l`" lub podobne polecenie, aby wyświetlić listę dostępnych dysków.

1. Przygotuj woluminy fizyczne:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Utwórz grupę woluminu. W tym przykładzie wywołania grupy woluminu `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Utwórz woluminy logiczne. Poniższe polecenie Firma Microsoft utworzy pojedynczy wolumin logiczną o nazwie `data-lv01` span grupy całego woluminu, ale należy pamiętać, że również można utworzyć wiele woluminów logiczne w grupie woluminu.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Sformatuj wolumin logiczne

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Z użyciem SLES11 `-t ext3` zamiast ext4. SLES11 obsługuje tylko dostęp tylko do odczytu do ext4 systemy plików.

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do /etc/fstab
> [!IMPORTANT]
> Nieprawidłowo edycji `/etc/fstab` pliku może spowodować rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat prawidłowo edytowania tego pliku. Jest również zalecane kopii zapasowej `/etc/fstab` plik jest tworzony przed przystąpieniem do edytowania.

1. Utwórz punkt instalacji odpowiednią dla nowego systemu plików, na przykład:

    ```bash  
    sudo mkdir /data
    ```

2. Znajdź ścieżki woluminu logicznego

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Otwórz `/etc/fstab` w edytorze tekstów i Dodaj wpis dla nowego systemu plików, na przykład:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Następnie zapisz i Zamknij `/etc/fstab`.

4. Sprawdzić, czy `/etc/fstab` wpis jest prawidłowy:

    ```bash    
    sudo mount -a
    ```

    Jeśli to polecenie powoduje komunikat o błędzie Sprawdź składnię `/etc/fstab` pliku.
   
    Uruchom `mount` polecenie, aby upewnić się, systemu plików:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcjonalnie) Parametry rozruchu przed uszkodzeniami w `/etc/fstab`
   
    Zawiera wiele dystrybucji `nobootwait` lub `nofail` zainstalować parametrów, które mogą być dodawane do `/etc/fstab` pliku. Te parametry Zezwalaj na wypadek awarii w przypadku instalowania w określonym systemie plików i Zezwalaj na systemie Linux, aby kontynuować do rozruchu, nawet jeśli nie można poprawnie zainstalować system plików woluminu macierzy RAID. Zapoznaj się z dystrybucji dokumentację, aby uzyskać więcej informacji na temat tych parametrów.
   
    Przykład (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>PRZYCINANIE/UNMAP pomocy technicznej
Niektóre jądra systemu Linux obsługują PRZYCINANIE/UNMAP operacji Odrzuć nieużywanych bloków na dysku. Operacje te są szczególnie przydatna w standardowe magazynu Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie strony można zapisać kosztów, jeśli Tworzenie dużych plików, a następnie usuń je.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji zalecane podejście:

- Użyj `discard` zainstalować opcję `/etc/fstab`, na przykład:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` ręcznie polecenie w wierszu polecenia lub dodać go do Twojego crontab regularnego uruchamiania:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
