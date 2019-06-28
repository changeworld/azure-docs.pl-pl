---
title: Utwórz i przekaż dysk VHD SUSE Linux na platformie Azure
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
ms.openlocfilehash: 6841caf94ec9712ff85e420e4378f8c9572e586b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202952"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu SLES lub openSUSE dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że zainstalowano już SUSE lub dystrybucją systemu openSUSE Linux system operacyjny na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcji Hyper-V. Aby uzyskać instrukcje, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / uwagi dotyczące instalacji openSUSE
* Zobacz również [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na temat przygotowywania systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalowania systemu Linux zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi, jeśli preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="use-suse-studio"></a>Use SUSE Studio
[SUSE Studio](http://www.susestudio.com) można łatwo tworzyć i zarządzać z obrazów SLES i openSUSE dla platformy Azure i funkcji Hyper-V. Jest to zalecane podejście do dostosowywania własnych obrazów SLES i openSUSE.

Jako alternatywę do tworzenia własnych wirtualnego dysku twardego, SUSE, publikuje również obrazów BYOS (Bring Your własnej subskrypcji) dla systemu SLES o [repozytorium VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Prepare SUSE Linux Enterprise Server 11 SP4
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno dla maszyny wirtualnej.
3. Zarejestruj systemu SUSE Linux Enterprise, aby zezwalała na pobieranie aktualizacji i zainstaluj pakiety.
4. Aktualizowanie systemu przy użyciu najnowszych poprawek:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Linux platformy Azure z repozytorium w systemie SLES:
   
        # sudo zypper install python-azure-agent
6. Sprawdź Jeśli waagent ma wartość "włączone" w folderze chkconfig, a jeśli nie, włącz ją do automatycznego uruchamiania:
   
        # sudo chkconfig waagent on
7. Sprawdź, czy usługa waagent jest uruchomiona i jeśli nie, uruchom go: 
   
        # sudo service waagent start
8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby zrobić to open "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna obejmuje następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Daje to pewność, wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.
9. Upewnij się, że /boot/grub/menu.lst i/etc/fstab zarówno odwoływać się do dysku za pomocą jego identyfikatora UUID (przez uuid) zamiast identyfikator dysku (według identyfikatora). 
   
    Uzyskaj identyfikator UUID dysku
   
        # ls /dev/disk/by-uuid/
   
    Jeśli /dev/disk/by-id / jest używany, zaktualizuj /boot/grub/menu.lst i/etc/fstab przy użyciu wartości odpowiednich przez uuid
   
    Przed wprowadzeniem zmiany
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po zmianie
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Zmodyfikuj zasady usługi udev, aby uniknąć generowania reguł statycznej interfejsy sieci Ethernet. Te reguły, które może powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Zalecane jest, aby edytować plik "/ etc/sysconfig/sieci/dhcp" i zmień `DHCLIENT_SET_HOSTNAME` parametru do następującego:
    
     DHCLIENT_SET_HOSTNAME="no"
12. "/ Etc/sudo" komentarz lub usuń następujące wiersze, jeśli nie istnieją:
    
     Wartość domyślna targetpw # poprosić o podanie hasła użytkownika docelowego, czyli głównego wszystkich ALL=(ALL) wszystkich # ostrzeżenie! Użyj tylko wtedy, wraz z "Domyślne targetpw"!
13. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.
14. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w /etc/waagent.conf odpowiednio:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Uwaga: Ustaw tę pozycję na czegokolwiek potrzebujesz się.
15. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="prepare-opensuse-131"></a>Przygotowanie openSUSE 13.1 +
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno dla maszyny wirtualnej.
3. W powłoce, uruchom polecenie "`zypper lr`". Jeśli to polecenie zwraca dane wyjściowe podobne do następujących, a następnie repozytoriów są skonfigurowane zgodnie z oczekiwaniami — konieczne są nie zmiany (Zwróć uwagę, że numery wersji mogą się różnić):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Jeśli polecenie zwraca "Żadnych repozytoriów zdefiniowane...", użyj następujących poleceń do dodania tych repozytoriów:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Następnie należy sprawdzić repozytoriów zostały dodane, uruchamiając polecenie "`zypper lr`" ponownie. W przypadku, gdy w jednym z repozytoriów aktualizacji nie jest włączona, należy ją włączyć za pomocą następującego polecenia:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Zaktualizuj jądro do najnowszej dostępnej wersji:
   
        # sudo zypper up kernel-default
   
    Lub aktualizowanie systemu przy użyciu najnowszych poprawek:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Linux dla platformy Azure.
   
        # sudo zypper install WALinuxAgent
6. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, Otwórz "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna obejmuje następujące parametry:
   
     Konsola = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Daje to pewność, wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Ponadto Usuń następujące parametry z wiersza rozruchu jądra, jeśli istnieją:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Zalecane jest, aby edytować plik "/ etc/sysconfig/sieci/dhcp" i zmień `DHCLIENT_SET_HOSTNAME` parametru do następującego:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Ważne:** "/ Etc/sudo" komentarz lub usuń następujące wiersze, jeśli nie istnieją:
   
     Wartość domyślna targetpw # poprosić o podanie hasła użytkownika docelowego, czyli głównego wszystkich ALL=(ALL) wszystkich # ostrzeżenie! Użyj tylko wtedy, wraz z "Domyślne targetpw"!
9. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.
10. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w /etc/waagent.conf odpowiednio:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Uwaga: Ustaw tę pozycję na czegokolwiek potrzebujesz się.
11. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Upewnij się, że agenta systemu Linux dla platformy Azure jest uruchamiane podczas uruchamiania:
    
        # sudo systemctl enable waagent.service
13. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do wirtualnego dysku twardego systemu SUSE Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).
