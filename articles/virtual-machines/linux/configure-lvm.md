---
title: Konfigurowanie lvm na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak skonfigurować lvm na linuksie na platformie Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066794"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurowanie usługi LVM na maszynie Wirtualnej z systemem Linux na platformie Azure
W tym dokumencie omówimy sposób konfigurowania menedżera woluminów logicznych (LVM) na maszynie wirtualnej platformy Azure. LVM może być używany na dysku systemu operacyjnego lub dysków z danymi na maszynach wirtualnych platformy Azure, jednak domyślnie większość obrazów w chmurze nie będzie skonfigurowana lvm na dysku systemu operacyjnego. Poniższe kroki skupią się na konfigurowaniu lvm dla dysków z danymi.

## <a name="linear-vs-striped-logical-volumes"></a>Liniowe a rozłożone woluminy logiczne
LVM może służyć do łączenia wielu dysków fizycznych w jeden wolumin pamięci masowej. Domyślnie LVM zwykle tworzy liniowe woluminy logiczne, co oznacza, że magazyn fizyczny jest łączony ze sobą. W takim przypadku operacje odczytu/zapisu zazwyczaj będą wysyłane tylko na jeden dysk. Natomiast możemy również tworzyć rozłożone woluminy logiczne, w których odczyty i zapisy są dystrybuowane na wiele dysków znajdujących się w grupie woluminów (podobnie jak RAID0). Ze względu na wydajność jest prawdopodobne, że będzie chcesz paski woluminów logicznych, tak aby odczyty i zapisy wykorzystać wszystkie dołączone dyski danych.

W tym dokumencie opisano sposób łączenia kilku dysków danych w jedną grupę woluminów, a następnie tworzenie rozłożonego woluminu logicznego. Poniższe kroki są uogólnione do pracy z większością dystrybucji. W większości przypadków narzędzia i przepływy pracy do zarządzania LVM na platformie Azure nie różnią się zasadniczo od innych środowisk. Jak zwykle, należy również skonsultować się z dostawcą systemu Linux dokumentacji i najlepszych praktyk dotyczących korzystania LVM z określonej dystrybucji.

## <a name="attaching-data-disks"></a>Dołączanie dysków z danymi
Zwykle chcesz zacząć od dwóch lub więcej pustych dysków z danymi podczas korzystania z LVM. W zależności od potrzeb we/wy możesz dołączyć dyski przechowywane w naszym standardowym magazynie, z maksymalnie 500 we/wy na dysk lub naszą pamięć masową Premium z maksymalnie 5000 we/wy na dysk. W tym artykule nie będzie wchodzić w szczegóły dotyczące sposobu aprowizowania i dołączania dysków danych do maszyny wirtualnej systemu Linux. Zobacz artykuł microsoft azure [dołączyć dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe instrukcje dotyczące podłączania pustego dysku danych do maszyny wirtualnej systemu Linux na platformie Azure.

## <a name="install-the-lvm-utilities"></a>Instalowanie narzędzi LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

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

    Na SLES11 należy również `/etc/sysconfig/lvm` edytować i ustawić `LVM_ACTIVATED_ON_DISCOVERED` na "włącz":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurowanie maszyny wirtualnej z systemem Linux
W tym przewodniku założymy, że załączyłeś trzy dyski `/dev/sdd` `/dev/sde`z danymi, które będziemy nazywać `/dev/sdc`, i . Te ścieżki mogą nie odpowiadać nazw ścieżek dysku w maszynie wirtualnej. Można uruchomić`sudo fdisk -l`polecenie ' ' lub podobne, aby wyświetlić listę dostępnych dysków.

1. Przygotuj woluminy fizyczne:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Tworzenie grupy woluminów. W tym przykładzie wywołujemy `data-vg01`grupę woluminów:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Tworzenie woluminów logicznych. Poniższe polecenie utworzymy pojedynczy `data-lv01` wolumin logiczny wywoływany w celu utworzenia całej grupy woluminów, ale należy zauważyć, że możliwe jest również utworzenie wielu woluminów logicznych w grupie woluminów.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatowanie woluminu logicznego

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Z SLES11 `-t ext3` używać zamiast ext4. SLES11 obsługuje tylko dostęp tylko do odczytu do systemów plików ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do /etc/fstab
> [!IMPORTANT]
> Nieprawidłowe edytowanie pliku `/etc/fstab` może uniemożliwić rozruch systemu. Jeśli nie masz pewności, jak to zrobić, sprawdź informacje na temat prawidłowego edytowania tego pliku w dokumentacji dystrybucji. Zaleca się również utworzenie kopii `/etc/fstab` zapasowej pliku przed edycją.

1. Utwórz żądany punkt instalacji dla nowego systemu plików, na przykład:

    ```bash  
    sudo mkdir /data
    ```

2. Lokalizowanie ścieżki woluminu logicznego

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Otwórz `/etc/fstab` w edytorze tekstu i dodaj wpis dla nowego systemu plików, na przykład:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Następnie zapisz i `/etc/fstab`zamknij .

4. Sprawdzić, `/etc/fstab` czy wpis jest poprawny:

    ```bash    
    sudo mount -a
    ```

    Jeśli to polecenie powoduje komunikat o błędzie, `/etc/fstab` sprawdź składnię w pliku.
   
    Następnie uruchom `mount` polecenie, aby upewnić się, że system plików jest zamontowany:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcjonalnie) Failsafe parametry rozruchu w`/etc/fstab`
   
    Wiele dystrybucji zawiera parametry `nobootwait` `nofail` lub instalację, które `/etc/fstab` mogą zostać dodane do pliku. Parametry te pozwalają na błędy podczas montażu określonego systemu plików i pozwalają systemowi Linux kontynuować rozruch, nawet jeśli nie jest w stanie prawidłowo zamontować systemu plików RAID. Więcej informacji na temat tych parametrów można znaleźć w dokumentacji dystrybucji.
   
    Przykład (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Obsługa TRIM/UNMAP
Niektóre jądra Linuksa obsługują operacje TRIM/UNMAP, aby odrzucić nieużywane bloki na dysku. Te operacje są przydatne przede wszystkim w magazynie standardowym, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie stron może zaoszczędzić koszty, jeśli utworzysz duże pliki, a następnie je usuniesz.

Istnieją dwa sposoby włączania obsługi TRIM w maszynie wirtualnej z systemem Linux. Jak zwykle, skonsultuj się z dystrybucją, aby uzyskać zalecane podejście:

- Użyj `discard` opcji montowania w `/etc/fstab`, na przykład:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- W niektórych `discard` przypadkach opcja może mieć wpływ na wydajność. Alternatywnie można uruchomić `fstrim` polecenie ręcznie z wiersza polecenia lub dodać je do crontab, aby działać regularnie:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
