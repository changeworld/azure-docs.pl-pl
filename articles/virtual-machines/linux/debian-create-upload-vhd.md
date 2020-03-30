---
title: Przygotowanie dysku VHD dla Systemu Linux w Debianie
description: Dowiedz się, jak tworzyć obrazy dysków VHD Debiana do wdrożeń maszyn wirtualnych na platformie Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d54f7a11d929c31fee29a788eb3a2ae2cc8f2703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066710"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Przygotowanie dysku VHD debiana na platformę Azure
## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji przyjęto założenie, że system operacyjny Debian Linux został już zainstalowany z pliku .iso pobranego ze [strony Debiana](https://www.debian.org/distrib/) na wirtualny dysk twardy. Istnieje wiele narzędzi do tworzenia plików vhd; Funkcja Hyper-V jest tylko jednym z przykładów. Aby uzyskać instrukcje dotyczące funkcji Hyper-V, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Uwagi dotyczące instalacji
* Zobacz też [Ogólne uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać więcej wskazówek dotyczących przygotowywania systemu Linux dla platformy Azure.
* Nowszy format VHDX nie jest obsługiwany na platformie Azure. Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet **convert-vhd.**
* Podczas instalacji systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często domyślnych dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM ze sklonowanymi maszynami wirtualnymi, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej w celu rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi, jeśli jest to preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Azure Linux można skonfigurować do tworzenia pliku wymiany na dysku tymczasowego zasobu. Więcej informacji można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [Uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Użyj platformy Azure-Manage do tworzenia identyfikatorów VHD Debiana
Dostępne są narzędzia do generowania identyfikatorów VHD Debiana na platformie Azure, takie jak skrypty [zarządzania platformą azure](https://github.com/credativ/azure-manage) firmy [Credativ.](https://www.credativ.com/) Jest to zalecane podejście w porównaniu do tworzenia obrazu od podstaw. Na przykład, aby utworzyć Debian 8 VHD, uruchom `azure-manage` następujące polecenia, aby pobrać `azure_build_image` narzędzie (i zależności) i uruchomić skrypt:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ręczne przygotowanie dysku VHD Debiana
1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Jeśli system operacyjny został zainstalowany przy użyciu iso, a`deb cdrom`następnie `/etc/apt/source.list`skomentować dowolny wiersz odnoszący się do " " w .

4. Edytuj `/etc/default/grub` plik i zmodyfikuj parametr **GRUB_CMDLINE_LINUX** w następujący sposób, aby uwzględnić dodatkowe parametry jądra dla platformy Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Odbuduj grub i uruchom:

        # sudo update-grub

6. Dodaj repozytoria platformy Azure Debiana do /etc/apt/sources.list dla Debiana 8 lub 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Rozciągnięcie"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Zainstaluj agenta systemu Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. W przypadku Debiana 9+ zaleca się używanie nowego jądra Chmury Debiana do użytku z maszynami wirtualnymi na platformie Azure. Aby zainstalować to nowe jądro, najpierw utwórz plik o nazwie /etc/apt/preferences.d/linux.pref z następującą zawartością:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Następnie uruchom "sudo apt-get install linux-image-cloud-amd64", aby zainstalować nowe jądro Debian Cloud.

9. Anulowanie obsługi administracyjnej maszyny wirtualnej i przygotowanie jej do inicjowania obsługi administracyjnej na platformie Azure i uruchamianie:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Kliknij **pozycję Akcja** -> Zamknij w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz możesz użyć wirtualnego dysku twardego Debiana do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).

