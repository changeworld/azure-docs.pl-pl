---
title: Tworzenie i przekazywanie dysku VHD Ubuntu Linux na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: f9a4588a444b5bfeb37f0bd98ada6a336baabb04
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Ubuntu dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Obrazy Ubuntu oficjalnego w chmurze
Ubuntu teraz publikuje oficjalnego Azure wirtualne dyski twarde do pobrania na [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Jeśli musisz utworzyć własne specjalistyczne obraz Ubuntu na platformie Azure, zamiast niż wykonać poniższą procedurę ręcznego, zaleca się rozpoczynać się one znane pracy wirtualne dyski twarde i dostosowywać odpowiednio do potrzeb. Najnowsze wersje obrazu zawsze można znaleźć w następujących lokalizacjach:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, zainstalowano system operacyjny Ubuntu Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

**Informacje o instalacji Ubuntu**

* Zobacz także [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na przygotowanie systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji), podczas instalowania systemu Linux. Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Zobacz [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="manual-steps"></a>Ręczne
> [!NOTE]
> Przed przystąpieniem do tworzenia własnego niestandardowego obrazu Ubuntu na platformie Azure, rozważ użycie obrazów wbudowanych i przetestowany z [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) zamiast tego.
> 
> 

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** można otworzyć okna dla maszyny wirtualnej.

3. Zastąp bieżący repozytoria w obrazu do użycia przez Ubuntu repozytoriów Azure. Kroki się nieco różnić w zależności od wersji Ubuntu.
   
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

4. Teraz następujące obrazy Ubuntu Azure *włączenie sprzętu* jądra (HWE). Zaktualizuj system operacyjny do najnowszej jądra, uruchamiając następujące polecenia:

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


5. Zmodyfikuj linii rozruchu jądra chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby zrobić to open `/etc/default/grub` w edytorze tekstów, odnaleźć zmiennej o nazwie `GRUB_CMDLINE_LINUX_DEFAULT` (lub Dodaj ją w razie potrzeby) i edytować obejmują następujące parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Zapisz i zamknij plik, a następnie uruchom `sudo update-grub`. Daje to pewność, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy technicznej dotyczącej debugowanie problemów.

6. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.

7. Zainstaluj agenta systemu Linux platformy Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    `walinuxagent` Może spowodować usunięcie pakietu `NetworkManager` i `NetworkManager-gnome` pakiety, jeśli są one zainstalowane.

8. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz używać wirtualnego dysku twardego Ubuntu Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).

## <a name="references"></a>Dokumentacja
Ubuntu jądra (HWE) na włączenie sprzętu:

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

