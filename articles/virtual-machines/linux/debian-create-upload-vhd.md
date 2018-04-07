---
title: Przygotowanie Debian dysku VHD systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Debian 7 i 8 pliki VHD dla wdrożenia na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: f8e98ae823d03dae475efca48a4ce32f27317882
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Przygotowywanie wirtualnego dysku twardego systemu Debian dla platformy Azure
## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono zainstalowano system operacyjny Debian Linux z pliku ISO pobrane z [Debian witryny sieci Web](https://www.debian.org/distrib/) do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD; Funkcja Hyper-V jest tylko przykładem. Aby uzyskać instrukcje, za pomocą funkcji Hyper-V, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Informacje o instalacji
* Zobacz także [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na przygotowanie systemu Linux na platformie Azure.
* Nowszy format VHDX nie jest obsługiwane na platformie Azure. Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub **convert-vhd** polecenia cmdlet.
* Zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji), podczas instalowania systemu Linux. Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux platformy Azure. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Zobacz [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Użyj zarządzania Azure, aby utworzyć wirtualne dyski twarde Debian
Brak dostępnych narzędzi do generowania Debian wirtualne dyski twarde dla platformy Azure, takich jak [azure — zarządzanie](https://github.com/credativ/azure-manage) skrypty z [credativ](http://www.credativ.com/). Jest to zalecane podejście i tworzenie obrazu od początku. Na przykład, aby utworzyć Debian VHD 8, uruchom następujące polecenia, aby pobrać azure w zarządzaniu (i zależności) i uruchom skrypt azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ręcznie przygotowanie Debian wirtualnego dysku twardego
1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Komentarz linii **deb cdrom** w `/etc/apt/source.list` po skonfigurowaniu maszyny Wirtualnej z plikiem ISO.
4. Edytuj `/etc/default/grub` plik i zmodyfikuj **GRUB_CMDLINE_LINUX** parametru w następujący sposób, aby uwzględnić jądra dodatkowe parametry dla platformy Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Odbuduj chodników, a następnie uruchom:
   
        # sudo update-grub
6. Dodaj repozytoria Azure w Debian /etc/apt/sources.list Debian 7 lub 8:
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Joasia"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Zainstaluj agenta systemu Linux platformy Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Debian 7 jest wymagany do uruchamiania na podstawie 3.16 jądra z repozytorium wheezy backports. Najpierw utwórz plik o nazwie /etc/apt/preferences.d/linux.pref z następującą zawartość:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Następnie uruchom "sudo instalacji stanie get linux obrazu — amd64" Aby zainstalować nowy jądra.
3. Anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure, a następnie uruchom:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Kliknij przycisk **akcji** -> zamykania w dół w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz używać Debian wirtualnego dysku twardego do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).

