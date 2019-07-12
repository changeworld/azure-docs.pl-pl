---
title: Przygotowanie systemu Debian dysku VHD systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia obrazów systemu Debian wirtualnego dysku twardego do wdrożenia na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: bdeaf4ec4a276e7cdd94402159f6adac474b3af8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671529"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Przygotowywanie wirtualnego dysku twardego systemu Debian dla platformy Azure
## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, już zainstalowany system operacyjny Linux systemu Debian z pliku ISO pobrane z [Debian witryny sieci Web](https://www.debian.org/distrib/) do wirtualnego dysku twardego. Istnieje wiele narzędzi, do tworzenia plików VHD; Funkcji Hyper-V jest tylko przykładem. Aby uzyskać instrukcje, za pomocą funkcji Hyper-V, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Uwagi dotyczące instalacji
* Zobacz też [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na temat przygotowywania systemu Linux na platformie Azure.
* Nowszy format VHDX nie jest obsługiwane na platformie Azure. Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub **convert-vhd** polecenia cmdlet.
* Podczas instalowania systemu Linux, zaleca się użycie standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux platformy Azure. Więcej informacji można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego, należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Umożliwia zarządzanie Azure tworzenie Debian wirtualnych dysków twardych
Brak dostępnych narzędzi do generowania Debian wirtualne dyski twarde dla systemu Azure, takich jak [platformy azure — zarządzanie](https://github.com/credativ/azure-manage) skryptów z [Credativ](https://www.credativ.com/). Jest to zalecane podejście w stosunku do tworzenia obrazu od podstaw. Na przykład utworzyć następujące wykonywania Debian 8 dysku VHD do polecenia Pobierz `azure-manage` narzędzie (i zależności) i uruchom `azure_build_image` skryptu:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ręcznie przygotowanie wirtualnego dysku twardego systemu Debian
1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Po zainstalowaniu systemu operacyjnego przy użyciu obrazu ISO, następnie przekształcić w komentarz wiersza odnoszących się do "`deb cdrom`" w `/etc/apt/source.list`.

4. Edytuj `/etc/default/grub` plik i Modyfikuj **GRUB_CMDLINE_LINUX** parametru w następujący sposób, aby uwzględnić dodatkowe jądra parametry dla platformy Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Ponownie skompiluj konfigurację programu grub, a następnie uruchom:

        # sudo update-grub

6. Dodawanie repozytoriów platformy Azure Debian firmy do /etc/apt/sources.list dla Debian 8 i 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Zainstaluj agenta systemu Linux dla platformy Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Debian 9 + zalecane jest używane nowe jądro Debian chmurę do użytku z maszynami wirtualnymi na platformie Azure. Aby zainstalować ten nowy jądra, należy najpierw utworzyć plik o nazwie /etc/apt/preferences.d/linux.pref z następującą zawartością:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Następnie uruchom "" sudo "apt-get install linux — obraz — cloud-amd64" Aby zainstalować nowe jądra systemu Debian chmury.

9. Anulowanie aprowizacji maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure, a następnie uruchom:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Kliknij przycisk **akcji** -> zamykania w dół w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do wirtualnego dysku twardego systemu Debian umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).

