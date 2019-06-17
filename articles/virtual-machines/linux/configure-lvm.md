---
title: Konfigurowanie LVM na maszynie wirtualnej z systemem Linux | Dokumentacja firmy Microsoft
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
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 08f98775360b8c0a82f68f322053cb71f0e79af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60739084"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurowanie LVM na Maszynę wirtualną systemu Linux na platformie Azure
W tym dokumencie omówiono konfigurowanie Menedżera woluminów logicznych (LVM) na maszynie wirtualnej platformy Azure. LVM może być używana dla dysku systemu operacyjnego lub dysków danych w maszynach wirtualnych platformy Azure, jednak domyślnie większość obrazy w chmurze nie ma LVM skonfigurowane na dysku systemu operacyjnego. Poniższe kroki koncentruje się na temat konfigurowania LVM dla dysków z danymi.

## <a name="linear-vs-striped-logical-volumes"></a>Liniowych, a woluminy rozłożone logiczne
LVM może służyć do łączenia liczbę dysków fizycznych w jednym woluminie magazynu. Domyślnie LVM zwykle utworzy liniowej woluminów logicznych, co oznacza, ze sobą połączonych magazynu fizycznego. W tym przypadku operacji odczytu/zapisu będą zwykle tylko wysyłane do jednego dysku. Z kolei musimy również utworzyć woluminy rozłożone logiczne odczyty i zapisy są dystrybuowane na wiele dysków znajdujących się w grupie woluminu (podobnie jak RAID0). Ze względu na wydajność prawdopodobnie warto stripe woluminów logicznych, więc, że odczyty i zapisy wykorzystanie wszystkich dysków dołączonych danych.

W tym dokumencie opisano jak połączyć kilka dysków danych w grupie pojedynczy wolumin, a następnie utworzyć woluminy rozłożone logiczne. Poniższe kroki zostały uogólnione do pracy z większości dystrybucji. W większości przypadków narzędzi i przepływów pracy związanych z zarządzaniem LVM na platformie Azure nie są całkowicie innego niż innych środowiskach. W zwykły sposób także zapoznaj się z dostawcą systemu Linux na potrzeby dokumentacji i najlepsze rozwiązania dotyczące używania LVM przy użyciu określonej dystrybucji.

## <a name="attaching-data-disks"></a>Dołączanie dysków danych
Jeden będzie zazwyczaj chcesz rozpocząć pracę z co najmniej dwóch dysków z danymi pusty, korzystając z LVM. Zgodnie z potrzebami we/wy, można dołączyć dyski, które są przechowywane w naszych magazynu w warstwie standardowa, z maksymalnie 500 operacji We/Wy/ps na dysku lub naszej usługi Premium storage przy użyciu maksymalnie 5000 operacji We/Wy/ps na dysk. W tym artykule nie przejdzie do szczegółów na temat sposobu obsługi administracyjnej i dołączanie dysków danych do maszyny wirtualnej systemu Linux. Zapoznaj się z artykułem Microsoft Azure [dołączanie dysku](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szczegółowe informacje na temat sposobu dołączanie pustego dysku danych do maszyny wirtualnej z systemem Linux na platformie Azure.

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

* **SLES 12 i dystrybucją systemu openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    Na SLES11, musisz również zmienić `/etc/sysconfig/lvm` i ustaw `LVM_ACTIVATED_ON_DISCOVERED` "Włącz":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurowanie maszyny wirtualnej z systemem Linux
W tym przewodniku przyjęto założenie, że zostały dołączone trzy dyski danych, które będzie nazywamy `/dev/sdc`, `/dev/sdd` i `/dev/sde`. Te ścieżki mogą być niezgodne nazwy ścieżek dysków na maszynie wirtualnej. Można uruchomić "`sudo fdisk -l`" lub podobne polecenie, aby wyświetlić listę dostępnych dysków.

1. Przygotuj woluminy fizyczne:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Utwórz grupę woluminu. W tym przykładzie dzwonimy grupy woluminu `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Tworzenie woluminów logicznych. Poniższe polecenie Firma Microsoft utworzy pojedynczy wolumin logiczną o nazwie `data-lv01` span grupy całego woluminu, ale należy pamiętać, że możliwe jest też jest to możliwe, aby utworzyć wiele woluminów logicznych w grupie woluminu.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Sformatuj wolumin logiczne

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Przy użyciu SLES11 `-t ext3` zamiast ext4. SLES11 obsługuje tylko dostęp tylko do odczytu do ext4 systemy plików.

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do/etc/fstab
> [!IMPORTANT]
> Nieprawidłowo edycji `/etc/fstab` plików nieraz powodowały rozruch systemu. Jeśli nie wiesz, zapoznaj się dokumentacją dystrybucji informacji na temat sposobu prawidłowo edytować ten plik. Warto również zalecamy, aby kopia zapasowa `/etc/fstab` przed rozpoczęciem edycji zostanie utworzony plik.

1. Na przykład utworzenia punktu instalacji żądaną nowy system plików:

    ```bash  
    sudo mkdir /data
    ```

2. Znajdź ścieżkę logiczną woluminu

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

4. Sprawdzić, czy `/etc/fstab` wpis jest poprawny:

    ```bash    
    sudo mount -a
    ```

    To polecenie zwróci komunikat o błędzie Sprawdź składnię `/etc/fstab` pliku.
   
    Następne uruchomienie `mount` polecenie, aby upewnić się, jest zainstalowany system plików:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcjonalnie) Przed uszkodzeniami rozruchu parametrów w `/etc/fstab`
   
    Włącz wiele dystrybucji `nobootwait` lub `nofail` instalacji parametry, które mogą być dodawane do `/etc/fstab` pliku. Parametry te umożliwiają błędy w przypadku instalowania w określonym systemie plików i umożliwiają systemu Linux przejść do rozruchu, nawet jeśli nie można poprawnie zainstalować system plików woluminu macierzy RAID. Zapoznaj się z dokumentacją danej dystrybucji, aby uzyskać więcej informacji na temat tych parametrów.
   
    Przykład (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP pomocy technicznej
Niektóre jądra systemu Linux obsługuje TRIM/UNMAP operacji można odrzucić nieużywanych bloków na dysku. Operacje te są szczególnie przydatne w magazynie standard storage do informowania platformy Azure, po usunięciu strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucenie strony można zapisać kosztów, jeśli utworzysz dużych plików, a następnie je usunąć.

Istnieją dwa sposoby, aby umożliwić PRZYCINANIE obsługi w maszynie Wirtualnej systemu Linux. W zwykły sposób poszukaj dystrybucji Zalecanym podejściem:

- Użyj `discard` instalacji opcji `/etc/fstab`, na przykład:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach `discard` opcji może mieć wpływ na wydajność. Alternatywnie, możesz uruchomić `fstrim` polecenie ręcznie z poziomu wiersza polecenia lub dodać do swojej crontab regularnego uruchamiania:

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
