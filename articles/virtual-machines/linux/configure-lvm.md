---
title: Konfigurowanie LVM na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak skonfigurować LVM w systemie Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: na
author: mimckitt
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 2c245d56b0fa0a09da04b83db11391a9291d3856
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251728"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurowanie LVM na maszynie wirtualnej z systemem Linux na platformie Azure
W tym dokumencie omówiono sposób konfigurowania Menedżera woluminów logicznych (LVM) na maszynie wirtualnej platformy Azure. LVM mogą być używane na dysku systemu operacyjnego lub na dyskach z danymi na maszynach wirtualnych platformy Azure, jednak domyślnie większość obrazów w chmurze nie ma skonfigurowanych LVM na dysku systemu operacyjnego. Poniższe kroki zakoncentrują się na konfigurowaniu LVM dla dysków z danymi.

## <a name="linear-vs-striped-logical-volumes"></a>Liniowe a rozłożone woluminy logiczne
LVM może służyć do łączenia wielu dysków fizycznych w jeden wolumin magazynu. Domyślnie LVM zazwyczaj tworzy liniowe woluminy logiczne, co oznacza, że magazyn fizyczny jest połączony ze sobą. W takim przypadku operacje odczytu/zapisu zwykle będą wysyłane tylko do jednego dysku. Z kolei możemy również utworzyć rozłożone woluminy logiczne, w przypadku których operacje odczytu i zapisu są dystrybuowane do wielu dysków znajdujących się w grupie woluminów (podobnie jak RAID0). Ze względu na wydajność prawdopodobnie chcesz umieścić woluminy logiczne, aby odczyty i zapisy używały wszystkich dołączonych dysków danych.

W tym dokumencie opisano sposób łączenia kilku dysków z danymi w jedną grupę woluminów, a następnie tworzenia woluminu logicznego. Poniższe kroki są uogólnione w celu pracy z większością dystrybucji. W większości przypadków narzędzia i przepływy pracy związane z zarządzaniem LVM na platformie Azure różnią się od innych środowisk. W zwykły sposób należy również zapoznać się z dostawcą systemu Linux w celu uzyskania dokumentacji i najlepszych rozwiązań dotyczących używania LVM z określoną dystrybucją.

## <a name="attaching-data-disks"></a>Dołączanie dysków danych
Jeden z nich będzie zazwyczaj miał być uruchamiany z co najmniej dwoma pustymi dyskami danych podczas korzystania z LVM. W zależności od potrzeb we/wy można dołączać dyski, które są przechowywane w magazynie w warstwie Standardowa, z maksymalnie 500 operacji we/wy na dysk lub w naszym magazynie w warstwie Premium przy użyciu maksymalnie 5000 operacji we/wy na dysk. W tym artykule opisano sposób aprowizacji i dołączania dysków danych do maszyny wirtualnej z systemem Linux. Zapoznaj się z artykułem Microsoft Azure. [Dołącz dysk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby uzyskać szczegółowe instrukcje dotyczące dołączania pustego dysku danych do maszyny wirtualnej z systemem Linux na platformie Azure.

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

    Na SLES11 należy również edytować `/etc/sysconfig/lvm` i ustawić `LVM_ACTIVATED_ON_DISCOVERED` na "Enable":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Konfigurowanie maszyny wirtualnej z systemem Linux
W tym przewodniku przyjęto założenie, że zostały dołączone trzy dyski danych, do których odnosi się `/dev/sdc`, `/dev/sdd` i `/dev/sde`. Ścieżki te mogą być niezgodne z nazwami ścieżek dysków w maszynie wirtualnej. Aby wyświetlić listę dostępnych dysków, można uruchomić "`sudo fdisk -l`" lub podobne polecenie.

1. Przygotuj woluminy fizyczne:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Utwórz grupę woluminów. W tym przykładzie wywołujemy grupę woluminów `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Utwórz woluminy logiczne. Poniższe polecenie utworzy pojedynczy wolumin logiczny o nazwie `data-lv01` w celu rozdzielenia całej grupy woluminów, ale należy pamiętać, że jest również możliwe utworzenie wielu woluminów logicznych w grupie woluminów.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatowanie woluminu logicznego

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 używają `-t ext3` zamiast ext4. SLES11 obsługuje tylko dostęp tylko do odczytu do systemów plików ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Dodaj nowy system plików do/etc/fstab
> [!IMPORTANT]
> Niewłaściwe edytowanie pliku `/etc/fstab` może spowodować nierozruchowy system. W razie wątpliwości zapoznaj się z dokumentacją dystrybucji, aby uzyskać informacje o tym, jak prawidłowo edytować ten plik. Przed rozpoczęciem edycji zaleca się również utworzenie kopii zapasowej pliku `/etc/fstab`.

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

3. Otwórz `/etc/fstab` w edytorze tekstów i Dodaj wpis dla nowego systemu plików, na przykład:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Następnie Zapisz i Zamknij `/etc/fstab`.

4. Sprawdź, czy wpis `/etc/fstab` jest poprawny:

    ```bash    
    sudo mount -a
    ```

    Jeśli to polecenie spowoduje komunikat o błędzie, Sprawdź składnię w pliku `/etc/fstab`.
   
    Następnie uruchom `mount` polecenie, aby upewnić się, że system plików jest zainstalowany:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. Obowiązkowe Parametry rozruchowe failsafe w `/etc/fstab`
   
    Wiele dystrybucji obejmuje parametry instalacji `nobootwait` lub `nofail`, które mogą zostać dodane do pliku `/etc/fstab`. Te parametry zezwalają na błędy podczas instalowania określonego systemu plików i umożliwiają dalsze rozruch systemu Linux, nawet jeśli nie można poprawnie zainstalować systemu plików RAID. Aby uzyskać więcej informacji na temat tych parametrów, zapoznaj się z dokumentacją dystrybucji.
   
    Przykład (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Obsługa przycinania/mapowania
Niektóre jądra systemu Linux obsługują operacje przycinania/mapowania do odrzucania nieużywanych bloków na dysku. Te operacje są szczególnie przydatne w przypadku magazynu w warstwie Standardowa, aby poinformować platformę Azure, że usunięte strony nie są już prawidłowe i mogą zostać odrzucone. Odrzucanie stron może zaoszczędzić koszt w przypadku tworzenia dużych plików, a następnie ich usuwania.

Istnieją dwa sposoby włączania obsługi przycinania na maszynie wirtualnej z systemem Linux. W zwykły sposób zapoznaj się z dystrybucją, aby uzyskać zalecane podejście:

- Użyj opcji instalacji `discard` w `/etc/fstab`, na przykład:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- W niektórych przypadkach opcja `discard` może mieć wpływ na wydajność. Alternatywnie można uruchomić polecenie `fstrim` ręcznie z wiersza polecenia lub dodać je do crontab w celu regularnego uruchamiania:

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
