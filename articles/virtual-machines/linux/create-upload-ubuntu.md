---
title: Utwórz i przekaż dysk VHD systemu Linux Ubuntu na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/24/2019
ms.author: szark
ms.openlocfilehash: 50651a31cd407da3ce32be3c2ddbbd24e6ca6b69
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671567"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Ubuntu dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Oficjalne obrazy w chmurze systemu Ubuntu
Ubuntu teraz publikuje oficjalne wirtualnych dysków twardych platformy Azure do pobrania na [ https://cloud-images.ubuntu.com/ ](https://cloud-images.ubuntu.com/). Jeśli potrzebujesz do tworzenia własnych wyspecjalizowanego obrazu systemu Ubuntu na platformie Azure, zamiast niż wykonać poniższą procedurę ręcznego, zaleca się rozpoczynać się one znane pracy wirtualne dyski twarde i dostosowywać odpowiednio do potrzeb. Zainstalowane najnowsze wersje obrazu, zawsze można znaleźć w następujących lokalizacjach:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że już zainstalowano system operacyjny Ubuntu Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcji Hyper-V. Aby uzyskać instrukcje, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji systemu Ubuntu**

* Zobacz również [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na temat przygotowywania systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalowania systemu Linux zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="manual-steps"></a>Wymagane ręczne wykonanie czynności
> [!NOTE]
> Przed próbą utworzenia własnego niestandardowego obrazu systemu Ubuntu na platformie Azure, rozważ użycie obrazów wstępnie skompilowane i przetestowane z [ https://cloud-images.ubuntu.com/ ](https://cloud-images.ubuntu.com/) zamiast tego.
> 
> 

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno dla maszyny wirtualnej.

3. Zastąp bieżący repozytoriów w obrazu do użycia w systemie Ubuntu repozytoriów platformy Azure. Kroki różnią się nieco w zależności od wersji systemu Ubuntu.
   
    Przed rozpoczęciem edycji `/etc/apt/sources.list`, zalecane jest wykonanie kopii zapasowej:
   
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

4. Teraz następujące obrazy Ubuntu Azure *włączania sprzętu* jądra (HWE). Aktualizacja systemu operacyjnego do najnowszych jądra, uruchamiając następujące polecenia:

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

    **Zobacz też:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Zmodyfikuj wiersza rozruchu jądra dla chodników uwzględnić dodatkowe jądra parametry dla platformy Azure. Aby zrobić to open `/etc/default/grub` w edytorze tekstów, Znajdź zmienną o nazwie `GRUB_CMDLINE_LINUX_DEFAULT` (lub dodać ją w razie potrzeby) i dostosuj go do obejmują następujące parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Zapisz i zamknij ten plik, a następnie uruchom `sudo update-grub`. Pozwoli to zagwarantować, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego umożliwić udzielenie pomocy technicznej platformy Azure w debugowaniu problemów.

6. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.

7. Zainstaluj agenta systemu Linux dla platformy Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  `walinuxagent` Pakietów może spowodować usunięcie `NetworkManager` i `NetworkManager-gnome` pakietów, jeśli są zainstalowane.


1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="references"></a>Odwołania
[Ubuntu sprzętu (HWE) umożliwia jądra](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do wirtualnego dysku twardego z systemem Ubuntu Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).

