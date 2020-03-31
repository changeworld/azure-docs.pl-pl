---
title: Tworzenie i przekazywanie dysku VHD systemu Linux ubuntu na platformie Azure
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Ubuntu Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066727"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem Ubuntu dla platformy Azure


Ubuntu publikuje teraz oficjalne vedy platformy [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)Azure do pobrania w . Jeśli chcesz utworzyć własny obraz Ubuntu specjalistyczne dla platformy Azure, zamiast korzystać z procedury ręcznej poniżej zaleca się, aby rozpocząć od tych znanych działających VHD i dostosować w razie potrzeby. Najnowsze wersje obrazów można zawsze znaleźć w następujących lokalizacjach:

* Ubuntu 12.04/Precyzyjne: [ubuntu-12.04-serwer-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [kosmiczny serwer-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz już zainstalowany system operacyjny Ubuntu Linux na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików vhd, na przykład rozwiązanie wirtualizacji, takie jak Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji Ubuntu**

* Zobacz też [Ogólne uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać więcej wskazówek dotyczących przygotowywania systemu Linux dla platformy Azure.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stały dysk VHD**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalacji systemu Linux zaleca się używanie standardowych partycji zamiast LVM (często domyślnych dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM ze sklonowanymi maszynami wirtualnymi, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej w celu rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi, jeśli jest to preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Zobacz [Uwagi dotyczące instalacji systemu Linux, aby](create-upload-generic.md#general-linux-installation-notes) uzyskać więcej informacji.

## <a name="manual-steps"></a>Kroki ręczne
> [!NOTE]
> Przed podjęciem próby utworzenia własnego niestandardowego obrazu Ubuntu dla platformy Azure, [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) należy rozważyć użycie wstępnie szesnastych i przetestowanych obrazów z zamiast tego.
> 
> 

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Połącz,** aby otworzyć okno dla maszyny wirtualnej.

3. Zastąp bieżące repozytoria w obrazie, aby użyć repozytorium platformy Azure ubuntu. Kroki różnią się nieznacznie w zależności od wersji Ubuntu.
   
    Przed edycją `/etc/apt/sources.list`zaleca się wykonanie kopii zapasowej:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Obrazy platformy Azure Ubuntu są teraz zgodne z jądrem *włączania sprzętu* (HWE). Zaktualizuj system operacyjny do najnowszego jądra, uruchamiając następujące polecenia:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Zobacz też:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Zmodyfikuj linię rozruchową jądra dla Gruba, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to `/etc/default/grub` zrobić, otwórz w edytorze `GRUB_CMDLINE_LINUX_DEFAULT` tekstu, znajdź zmienną o nazwie (lub dodaj ją w razie potrzeby) i edytuj ją, aby uwzględnić następujące parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Zapisz i zamknij ten plik, a następnie uruchom polecenie `sudo update-grub`. Zapewni to, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w rozwiązywaniu problemów z debugowaniem.

6. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.

7. Zainstaluj agenta systemu Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Pakiet `walinuxagent` może usunąć `NetworkManager` `NetworkManager-gnome` i pakiety, jeśli są zainstalowane.


1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="references"></a>Dokumentacja
[Jądro sprzętowe Ubuntu (HWE)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Następne kroki
Teraz możesz użyć wirtualnego dysku twardego Ubuntu Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).

