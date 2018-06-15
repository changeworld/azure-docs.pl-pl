---
title: Tworzenie i przekazywanie dysku VHD na podstawie CentOS Linux na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym na podstawie CentOS Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: d7c35b79dcdf75dbb3f891dc4c66cbf893b61c03
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777692"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu CentOS dla platformy Azure
* [Przygotowanie maszyny wirtualnej CentOS 6.x dla platformy Azure](#centos-6x)
* [Przygotowanie maszyny wirtualnej CentOS 7.0 + Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, zainstalowano CentOS (lub podobny pochodnej) systemu operacyjnego Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

**Informacje o instalacji centOS**

* Zobacz także [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na przygotowanie systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox oznacza to, wybierając **stały rozmiar** zamiast domyślnego dynamicznie przydzielane podczas tworzenia dysku.
* Podczas instalowania systemu Linux jest *zalecane* używasz standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, gdy dysk systemu operacyjnego kiedykolwiek musi być dołączona do innego identyczne maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi.
* Wymagana jest obsługa jądra służący do instalowania systemów plików funkcji zdefiniowanej przez użytkownika. Przy pierwszym uruchomieniu komputera na platformie Azure konfiguracji inicjowania obsługi administracyjnej jest przekazywany do maszyny Wirtualnej systemu Linux za pomocą nośnika sformatowany UDF, dołączonego do gościa. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików funkcji zdefiniowanej przez użytkownika do odczytu konfiguracji i udostępnić Maszynie wirtualnej.
* Wersje jądra systemu Linux poniżej 2.6.37 nie obsługują NUMA w funkcji Hyper-V o dużym rozmiarze maszyny Wirtualnej. Ten problem wpływa głównie na starszą dystrybucji przy użyciu nadrzędnego Red Hat 2.6.32 jądra i ustalono w RHEL 6.6 (jądra-2.6.32 504). Komputerów z systemami niestandardowych jądra starsze niż 2.6.37 lub systemem RHEL jądra starsze niż 2.6.32-504 należy ustawić parametr rozruchowego `numa=off` na wiersza polecenia w grub.conf jądra. Aby uzyskać więcej informacji, zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Zobacz [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="centos-6x"></a>CentOS 6.x

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. W CentOS 6 NetworkManager może zakłócać agenta systemu Linux platformy Azure. Odinstalowania tego pakietu, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

4. Utwórz lub Edytuj plik `/etc/sysconfig/network` i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Utwórz lub Edytuj plik `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Modyfikuj reguły udev, aby uniknąć generowania statyczne reguły dla interfejsów Ethernet. Te reguły może spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Upewnij się, że usługa sieci zostanie uruchomiona podczas rozruchu, uruchamiając następujące polecenie:
   
        # sudo chkconfig network on

8. Jeśli chcesz użyć wstecznych OpenLogic, które znajdują się w centrach danych platformy Azure, a następnie zastąp `/etc/yum.repos.d/CentOS-Base.repo` pliku z następujących repozytoriów.  Spowoduje to również dodanie **[openlogic]** repozytorium, która obejmuje dodatkowe pakiety, takie jak agenta systemu Linux platformy Azure:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    >[!Note]
    Pozostała część tego przewodnika zakłada, używane są co najmniej `[openlogic]` repozytorium, która będzie używana do zainstalowania agenta systemu Linux Azure poniżej.


9. Dodaj następujący wiersz do /etc/yum.conf:
    
        http_caching=packages

10. Uruchom następujące polecenie, aby wyczyścić bieżących metadanych yum i zaktualizuj system z najnowszych pakietów:
    
        # yum clean all

    Jeśli tworzysz obrazu dla starszej wersji CentOS, zalecane jest wszystkich pakietów aktualizacji do najnowszej wersji:

        # sudo yum -y update

    Po uruchomieniu tego polecenia może być wymagane ponowne uruchomienie komputera.

11. (Opcjonalnie) Zainstaluj sterowniki dla usługi integracji systemu Linux (LIS).
   
    >[!IMPORTANT]
    Kroku **wymagane** dla CentOS 6.3 i wcześniejszych oraz opcjonalny w przypadku nowszych wersjach.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Alternatywnie, można wykonać instrukcje dotyczące ręcznej instalacji [stronę pobierania LIS](https://go.microsoft.com/fwlink/?linkid=403033) do zainstalowania obr. / min na maszynie Wirtualnej.
 
12. Zainstaluj agenta systemu Linux platformy Azure i zależności:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    Pakiet WALinuxAgent spowoduje usunięcie NetworkManager i pakiety NetworkManager gnome jeśli ich nie zostały już usunięte zgodnie z opisem w kroku 3.


13. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/boot/grub/menu.lst` w edytorze tekstów i upewnij się, że jądra domyślna zawiera następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów.
    
    Oprócz powyższego, zaleca się *Usuń* następujące parametry:
    
        rhgb quiet crashkernel=auto
    
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego.  `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie Wirtualnej najmniej 128 MB, które mogą być problemy na mniejsze rozmiary maszyn wirtualnych.

    >[!Important]
    CentOS 6.5 lub starszej należy również ustawić parametr jądra `numa=off`. Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.

15. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.


- - -
## <a name="centos-70"></a>CentOS 7.0+
**Zmiany w CentOS 7 (lub podobny pochodne)**

Przygotowywanie maszyny wirtualnej CentOS 7 dla platformy Azure jest bardzo podobny do CentOS 6, jednak istnieje kilka istotnych różnic, warto zauważyć:

* Pakiet NetworkManager nie powodował konfliktu z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zaleca się, że nie zostanie usunięta.
* GRUB2 teraz jest używana jako inicjującego domyślne, więc procedury do edycji parametry jądra został zmieniony (patrz poniżej).
* XFS jest teraz domyślny system plików. Nadal można ext4 systemu plików, w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. Utwórz lub Edytuj plik `/etc/sysconfig/network` i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Utwórz lub Edytuj plik `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Modyfikuj reguły udev, aby uniknąć generowania statyczne reguły dla interfejsów Ethernet. Te reguły może spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Jeśli chcesz użyć wstecznych OpenLogic, które znajdują się w centrach danych platformy Azure, a następnie zastąp `/etc/yum.repos.d/CentOS-Base.repo` pliku z następujących repozytoriów.  Spowoduje to również dodanie **[openlogic]** repozytorium, zawierającą pakietów dla agenta systemu Linux platformy Azure:
   
        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

    >[!Note]
    Pozostała część tego przewodnika zakłada, używane są co najmniej `[openlogic]` repozytorium, która będzie używana do zainstalowania agenta systemu Linux Azure poniżej.

7. Uruchom następujące polecenie, aby wyczyścić bieżący metadanych yum i zainstaluj wszystkie aktualizacje:
   
        # sudo yum clean all

    Jeśli tworzysz obrazu dla starszej wersji CentOS, zalecane jest wszystkich pakietów aktualizacji do najnowszej wersji:

        # sudo yum -y update

    Ponowne uruchomienie, może być wymagane po uruchomieniu tego polecenia.

8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` w edytorze tekstów i edytowanie `GRUB_CMDLINE_LINUX` parametrów, na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. On również wyłącza nowe CentOS 7 konwencje nazewnictwa dla kart sieciowych. Oprócz powyższego, zaleca się *Usuń* następujące parametry:
   
        rhgb quiet crashkernel=auto
   
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie Wirtualnej najmniej 128 MB, które mogą być problemy na mniejsze rozmiary maszyn wirtualnych.

9. Po zakończeniu edycji `/etc/default/grub` na powyżej, uruchom następujące polecenie, aby odbudować chodników konfiguracji:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Jeśli tworzenie obrazu z **VMWare, VirtualBox lub KVM:** funkcji Hyper-V upewnij się, że sterowniki są uwzględnione w initramfs:
   
   Edytuj `/etc/dracut.conf`, Dodaj zawartość:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Odbuduj initramfs:
   
        # sudo dracut -f -v

11. Zainstaluj agenta systemu Linux platformy Azure i zależności:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
   
   Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:
   
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz używać wirtualnego dysku twardego systemu CentOS Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).

