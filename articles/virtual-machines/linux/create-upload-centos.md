---
title: Utwórz i przekaż dysk VHD opartych na systemie CentOS Linux na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym opartych na systemie CentOS Linux.
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
ms.openlocfilehash: 4e32d2357636cb488d3a58b78b025860da3f74c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60327987"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu CentOS dla platformy Azure

* [Przygotowywanie maszyny wirtualnej w wersji 6.x CentOS dla platformy Azure](#centos-6x)
* [Przygotowywanie maszyny wirtualnej CentOS 7.0 + dla platformy Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że już zainstalowano CentOS (lub podobny) wirtualnego dysku twardego systemu operacyjnego Linux. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcji Hyper-V. Aby uzyskać instrukcje, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji centOS**

* Zobacz również [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na temat przygotowywania systemu Linux na platformie Azure.
* VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox oznacza to, wybierając **ustalony rozmiar** zamiast domyślnego dynamicznie przydzielany podczas tworzenia dysku.
* Podczas instalowania systemu Linux jest *zalecane* używasz standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej identyczne do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi.
* Wymagana jest obsługa jądra instalowanie systemów plików funkcji zdefiniowanej przez użytkownika. Podczas pierwszego rozruchu na platformie Azure konfiguracji aprowizacji jest przekazywany do maszyny Wirtualnej systemu Linux przy użyciu sformatowanych przy użyciu funkcji zdefiniowanej przez użytkownika nośnika, który jest dołączony do gościa. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików UDF odczytu jego konfigurację do aprowizowania maszyny Wirtualnej.
* Wersje jądra systemu Linux poniżej 2.6.37 nie obsługują architektury NUMA funkcji Hyper-v przy użyciu większych rozmiarów maszyn wirtualnych. Ten problem wpływa głównie na starsze dystrybucje przy użyciu nadrzędnych jądra Red Hat 2.6.32 i została naprawiona w RHEL, 6.6 (jądra-2.6.32 504). Komputerów z systemami starsze niż 2.6.37 niestandardowe jądra lub starszych jądra na podstawie RHEL niż 2.6.32-504 należy ustawić parametr rozruchu `numa=off` na wiersza polecenia w grub.conf jądra. Aby uzyskać więcej informacji, zobacz firmy Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

## <a name="centos-6x"></a>CentOS 6.x

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. CentOS 6 NetworkManager może kolidować z agentem systemu Linux platformy Azure. Ten pakiet należy odinstalować, uruchamiając następujące polecenie:

        # sudo rpm -e --nodeps NetworkManager

4. Tworzenie lub edytowanie pliku `/etc/sysconfig/network` i Dodaj następujący tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Tworzenie lub edytowanie pliku `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:

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

        # sudo chkconfig network on

8. Jeśli chcesz użyć OpenLogic duplikatów, które znajdują się w obrębie centrów danych platformy Azure, a następnie zastąp `/etc/yum.repos.d/CentOS-Base.repo` plików za pomocą następujących repozytoriów.  Spowoduje to również dodanie **[openlogic]** repozytorium, które obejmuje dodatkowych pakietów, takich jak agenta systemu Linux platformy Azure:

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

    > [!Note]
    > Dalszej części tego przewodnika założy, używane są co najmniej `[openlogic]` repozytorium, które będzie używane do zainstalowania agenta systemu Linux platformy Azure poniżej.

9. Dodaj następujący wiersz do /etc/yum.conf:

        http_caching=packages

10. Uruchom następujące polecenie, aby wyczyścić bieżących metadanych yum i aktualizowanie systemu przy użyciu najnowszych pakietów:

        # yum clean all

    Jeśli tworzysz obrazu dla starszych wersji systemu CentOS, zalecane jest Aktualizuj wszystkie pakiety do najnowszej wersji:

        # sudo yum -y update

    Po uruchomieniu tego polecenia może być konieczne ponowne uruchomienie komputera.

11. (Opcjonalnie) Zainstaluj sterowniki dla integracji usługi LIS (Linux).

    > [!IMPORTANT]
    > Ten krok jest **wymagane** centos 6.3 i wcześniejszych oraz opcjonalny w przypadku nowszych wersji.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Alternatywnie, można wykonać instrukcje instalacji ręcznej w systemie [stronę pobierania LIS](https://go.microsoft.com/fwlink/?linkid=403033) instalacji RPM na maszynie Wirtualnej.

12. Zainstaluj agenta systemu Linux platformy Azure i zależności:

        # sudo yum install python-pyasn1 WALinuxAgent

    Spowoduje to usunięcie pakietu WALinuxAgent NetworkManager i pakietów NetworkManager gnome jeśli one nie zostały już usunięte zgodnie z opisem w kroku 3.

13. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/boot/grub/menu.lst` w edytorze tekstów i upewnij się, że jądra domyślna obejmuje następujące parametry:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Zapewni to także wszystkie konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.

    Oprócz powyższego, zaleca się *Usuń* następujące parametry:

        rhgb quiet crashkernel=auto

    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego.  `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr powoduje zmniejszenie ilości dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

    > [!Important]
    > CentOS 6.5 lub starszej, również należy ustawić parametr jądra `numa=off`. Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.

15. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Kliknij przycisk **akcji -> Zamknij dół** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

- - -

## <a name="centos-70"></a>CentOS 7.0+

**Zmiany w CentOS 7 (i podobne pochodne)**

Przygotowywanie maszyny wirtualnej CentOS 7 dla systemu Azure jest bardzo podobny do CentOS 6, jednak istnieje kilka istotnych różnic warte odnotowania:

* Pakiet NetworkManager nie powodował konfliktu z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zaleca się, że nie jest usuwany.
* GRUB2 obecnie jest używany jako domyślny program inicjujący, dzięki czemu procedury do edycji jądra parametry został zmieniony (zobacz poniżej).
* XFS jest teraz domyślnego systemu plików. Nadal można ext4 systemu plików, w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. Tworzenie lub edytowanie pliku `/etc/sysconfig/network` i Dodaj następujący tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Tworzenie lub edytowanie pliku `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Zmodyfikuj zasady usługi udev, aby uniknąć generowania reguł statycznej interfejsy sieci Ethernet. Te reguły, które może powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Jeśli chcesz użyć OpenLogic duplikatów, które znajdują się w obrębie centrów danych platformy Azure, a następnie zastąp `/etc/yum.repos.d/CentOS-Base.repo` plików za pomocą następujących repozytoriów.  Spowoduje to również dodanie **[openlogic]** repozytorium, które zawiera pakiety dla agenta systemu Linux platformy Azure:

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

    > [!Note]
    > Dalszej części tego przewodnika założy, używane są co najmniej `[openlogic]` repozytorium, które będzie używane do zainstalowania agenta systemu Linux platformy Azure poniżej.

7. Uruchom następujące polecenie, aby wyczyścić bieżących metadanych yum i zainstaluj wszystkie aktualizacje:

        # sudo yum clean all

    Jeśli tworzysz obrazu dla starszych wersji systemu CentOS, zalecane jest Aktualizuj wszystkie pakiety do najnowszej wersji:

        # sudo yum -y update

    Wymagany jest ponowny rozruch może po uruchomieniu tego polecenia.

8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru, na przykład:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

   Zapewni to także wszystkie konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. On również wyłącza nowe konwencje nazw CentOS 7 dla kart interfejsu sieciowego. Oprócz powyższego, zaleca się *Usuń* następujące parametry:

        rhgb quiet crashkernel=auto

    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr powoduje zmniejszenie ilości dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

9. Po zakończeniu edycji `/etc/default/grub` na powyżej, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Jeśli tworzenie obrazu z **VirtualBox, KVM lub VMware:** Upewnij się, że sterowniki funkcji Hyper-V znajdują się w initramfs:

    Edytuj `/etc/dracut.conf`, Dodaj zawartość:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Ponownie skompiluj initramfs:

        # sudo dracut -f -v

11. Zainstaluj agenta systemu Linux platformy Azure i zależności:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

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

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przystąpić do wirtualnego dysku twardego systemu CentOS Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).
