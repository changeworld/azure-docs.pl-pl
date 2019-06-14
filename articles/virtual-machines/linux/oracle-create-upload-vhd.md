---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux Oracle | Dokumentacja firmy Microsoft
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: ecd30d30434d91893102ce6ec0df21daa84b677c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542411"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Linux w środowisku Oracle dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że już zainstalowano systemu operacyjnego Oracle Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcji Hyper-V. Aby uzyskać instrukcje, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Uwagi dotyczące instalacji systemu Linux Oracle
* Zobacz również [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na temat przygotowywania systemu Linux na platformie Azure.
* Oracle firmy Red Hat zgodne jądra i swojego UEK3 (podzielenie jądra Enterprise) są obsługiwane w funkcji Hyper-V i platformą Azure. Aby uzyskać najlepsze wyniki należy upewnić się, że najnowsze jądra podczas przygotowywania wirtualnego dysku twardego systemu Linux Oracle.
* UEK2 firmy Oracle nie jest obsługiwane w funkcji Hyper-V i platformą Azure, ponieważ nie zawiera wymaganych sterowników.
* VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalowania systemu Linux zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi, jeśli preferowane.
* NUMA nie jest obsługiwana dla większych rozmiarów maszyn wirtualnych z powodu błędu w wersje jądra systemu Linux poniżej 2.6.37. Ten problem ma wpływ przede wszystkim na za pomocą nadrzędnego Red Hat 2.6.32 jądra. Ręczna instalacja agenta systemu Linux platformy Azure (waagent) zostanie automatycznie wyłączyć technologię NUMA w konfiguracji programu GRUB jądra systemu Linux. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.
* Upewnij się, że `Addons` repozytorium jest włączona. Edytuj plik `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) i zmień wiersz `enabled=0` do `enabled=1` w obszarze **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4+
Należy wykonać kroki konfiguracji określone w system operacyjny dla maszyny wirtualnej do uruchamiania na platformie Azure.

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno dla maszyny wirtualnej.
3. Odinstaluj NetworkManager, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Uwaga:** Pakiet nie jest już zainstalowany, to polecenie zakończy się niepowodzeniem z komunikatem o błędzie. Jest to oczekiwane.
4. Utwórz plik o nazwie **sieci** w `/etc/sysconfig/` katalog zawierający następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalog zawierający następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Zmodyfikuj zasady usługi udev, aby uniknąć generowania reguł statycznej interfejsy sieci Ethernet. Te reguły, które może powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Upewnij się, że Usługa sieciowa rozpocznie się w czasie rozruchu, uruchamiając następujące polecenie:
   
        # chkconfig network on
8. Zainstaluj pyasn1 języka python, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
9. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby zrobić to open "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna obejmuje następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Zapewni to także wszystkie konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Spowoduje to wyłączenie NUMA z powodu błędu w firmy Oracle firmy Red Hat zgodne jądra.
   
   Oprócz powyższego, zaleca się *Usuń* następujące parametry:
   
        rhgb quiet crashkernel=auto
   
   Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr powoduje zmniejszenie ilości dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.
10. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.
11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie. Najnowsza wersja to 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Pamiętaj, że instalowanie pakietu WALinuxAgent spowoduje usunięcie NetworkManager pakietów NetworkManager gnome jeśli one nie zostały już usunięte zgodnie z opisem w kroku 2.
12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w /etc/waagent.conf odpowiednio:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0+
**Zmiany w systemie Oracle Linux 7**

Przygotowywanie maszyny wirtualnej Oracle Linux 7 dla systemu Azure jest bardzo podobny do Oracle Linux 6, jednak istnieje kilka istotnych różnic warte odnotowania:

* Red Hat zgodne jądra i UEK3 firmy Oracle są obsługiwane na platformie Azure.  Jądra UEK3 jest zalecane.
* Pakiet NetworkManager nie powodował konfliktu z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zaleca się, że nie jest usuwany.
* GRUB2 obecnie jest używany jako domyślny program inicjujący, dzięki czemu procedury do edycji jądra parametry został zmieniony (zobacz poniżej).
* XFS jest teraz domyślnego systemu plików. Nadal można ext4 systemu plików, w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Utwórz plik o nazwie **sieci** w `/etc/sysconfig/` katalog zawierający następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalog zawierający następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Zmodyfikuj zasady usługi udev, aby uniknąć generowania reguł statycznej interfejsy sieci Ethernet. Te reguły, które może powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Upewnij się, że Usługa sieciowa rozpocznie się w czasie rozruchu, uruchamiając następujące polecenie:
   
        # sudo chkconfig network on
7. Zainstaluj pakiet pyasn1 języka python, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
8. Uruchom następujące polecenie, aby wyczyścić bieżących metadanych yum i zainstaluj wszystkie aktualizacje:
   
        # sudo yum clean all
        # sudo yum -y update
9. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. W tym celu otwórz "/ etc/domyślne/chodników" w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru, na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Zapewni to także wszystkie konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. On również wyłącza nowe konwencje nazw OEL 7 dla kart interfejsu sieciowego. Oprócz powyższego, zaleca się *Usuń* następujące parametry:
   
       rhgb quiet crashkernel=auto
   
   Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr powoduje zmniejszenie ilości dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.
10. Po zakończeniu edycji "/ etc/domyślne/chodników" na powyżej, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.
12. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (w opisie poprzedniego kroku), zmodyfikuj następujące parametry w /etc/waagent.conf odpowiednio:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do Twojej VHD Oracle Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).

