---
title: Tworzenie i przekazywanie SUSE Linux wirtualnego dysku twardego na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 2372550548f40ad07b4f76c19bc3bc1cb8380830
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu SLES lub openSUSE dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że zainstalowano już SUSE lub openSUSE systemu operacyjnego Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE instalacji uwagi
* Zobacz także [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na przygotowanie systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji), podczas instalowania systemu Linux. Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Zobacz [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="use-suse-studio"></a>Użyj SUSE Studio
[SUSE Studio](http://www.susestudio.com) można łatwo tworzyć i zarządzać obrazów SLES i openSUSE platformy Azure i funkcji Hyper-V. Jest to zalecane podejście do dostosowywania własnych obrazów SLES i openSUSE.

Jako alternatywę do tworzenia własnych wirtualnego dysku twardego, SUSE publikuje również obrazów BYOS (Bring Your własnej subskrypcji) dla SLES na [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Przygotowanie SUSE Linux Enterprise Server 11 z dodatkiem SP4
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** można otworzyć okna dla maszyny wirtualnej.
3. Zarejestruj systemu SUSE Linux Enterprise, aby zezwolić na aktualizacje pobierania i instalowania pakietów.
4. Zaktualizuj system z najnowszych poprawek:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Linux platformy Azure z repozytorium SLES:
   
        # sudo zypper install WALinuxAgent
6. Sprawdź, czy agenta waagent jest ustawiony na "on" w chkconfig, a jeśli nie, ją włączyć automatyczne uruchamianie:
   
        # sudo chkconfig waagent on
7. Sprawdź, czy Usługa agenta waagent jest uruchomiona i jeśli nie, uruchom go: 
   
        # sudo service waagent start
8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby zrobić to open "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna zawiera następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów.
9. Upewnij się, że /boot/grub/menu.lst i /etc/fstab zarówno odwołanie dysku za pomocą jego identyfikatora UUID (przez uuid) zamiast identyfikator dysku (według identyfikatora). 
   
    Pobierz identyfikator UUID
   
        # ls /dev/disk/by-uuid/
   
    Jeśli /dev/disk/by-id / jest używany, aktualizacji zarówno /boot/grub/menu.lst i/etc/fstab przy użyciu wartości odpowiednich przez uuid
   
    Przed zmianą
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po zmianie
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modyfikuj reguły udev, aby uniknąć generowania statyczne reguły dla interfejsów Ethernet. Te reguły może spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Jest zalecane, aby edytować plik "/ etc/sysconfig/sieci/dhcp" i zmień `DHCLIENT_SET_HOSTNAME` parametru do następującego:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Komentarz "/ etc/sudoers", lub usuń następujące linie, jeśli istnieją:
    
     Ustawienia domyślne targetpw # podanie hasła użytkownika docelowego to główny wszystkich ALL=(ALL) wszystkie # ostrzeżenie! Użyj tylko wtedy, wraz z 'Targetpw wartości domyślnych'!
13. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
14. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Uwaga: Ustaw tę wartość na dowolnym należy się.
15. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo agenta waagent-force - deprovision
    # <a name="export-histsize0"></a>Eksportuj HISTSIZE = 0
    # <a name="logout"></a>wyloguj
16. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

- - -
## <a name="prepare-opensuse-131"></a>Przygotowanie openSUSE 13.1 +
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** można otworzyć okna dla maszyny wirtualnej.
3. W powłoce, uruchom polecenie "`zypper lr`". Jeśli to polecenie zwraca dane wyjściowe podobne do następujących, a następnie repozytoria są skonfigurowane zgodnie z oczekiwaniami — bez korekt są niezbędne (należy pamiętać, że numery wersji może się różnić):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Jeśli polecenie zwróci "Nie zdefiniowano... repozytoria" Użyj następujących poleceń, aby dodać te repozytoriów:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Następnie należy sprawdzić repozytoria zostały dodane, uruchamiając polecenie "`zypper lr`" ponownie. W przypadku, gdy jeden z repozytoriami odpowiednich aktualizacji nie jest włączona, należy je włączyć, wprowadzając następujące polecenie:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualizacja jądra do najnowszej dostępnej wersji:
   
        # sudo zypper up kernel-default
   
    Lub zaktualizuj system z najnowszych poprawek:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Linux platformy Azure.
   
   # <a name="sudo-zypper-install-walinuxagent"></a>Zainstaluj zypper sudo WALinuxAgent
6. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. W tym celu należy otworzyć "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna zawiera następujące parametry:
   
     Konsola = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. Ponadto należy usunąć następujące parametry z wiersza rozruchu jądra Jeśli istnieją:
   
     Rezerwacja libata.atapi_enabled=0 = 0x1f0, 0x8
7. Jest zalecane, aby edytować plik "/ etc/sysconfig/sieci/dhcp" i zmień `DHCLIENT_SET_HOSTNAME` parametru do następującego:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Ważne:** "/ etc/sudoers", komentarz lub usuń następujące linie, jeśli istnieją:
   
     Ustawienia domyślne targetpw # podanie hasła użytkownika docelowego to główny wszystkich ALL=(ALL) wszystkie # ostrzeżenie! Użyj tylko wtedy, wraz z 'Targetpw wartości domyślnych'!
9. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
10. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Uwaga: Ustaw tę wartość na dowolnym należy się.
11. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
    
    # <a name="sudo-waagent--force--deprovision"></a>sudo agenta waagent-force - deprovision
    # <a name="export-histsize0"></a>Eksportuj HISTSIZE = 0
    # <a name="logout"></a>wyloguj
12. Upewnij się, że Agent systemu Linux platformy Azure jest uruchamiany podczas uruchamiania:
    
        # sudo systemctl enable waagent.service
13. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz używać wirtualnego dysku twardego SUSE Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).
