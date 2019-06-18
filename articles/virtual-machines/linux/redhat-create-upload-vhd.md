---
title: Tworzenie i przekazywanie Red Hat Enterprise Linux wirtualnego dysku twardego do użycia na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: 9d5e9c6c8a104c16d1ff4e96929ff47ed6fd5ff6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65966116"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Red Hat dla platformy Azure
W tym artykule dowiesz się, jak przygotować maszynę wirtualną Red Hat Enterprise Linux (RHEL) do użycia na platformie Azure. Wersje systemu RHEL, które zostały omówione w tym artykule są 6.7 + i 7.1 +. Funkcji hypervisor do przygotowywania, które zostały omówione w tym artykule są maszyny wirtualne funkcji Hyper-V, na podstawie jądra (KVM) i VMware. Aby uzyskać więcej informacji na temat wymagania kwalifikacyjne dotyczące udziału w systemie Red Hat Cloud Access program zobacz [witryny sieci Web firmy Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) i [systemem RHEL na platformie Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Sposoby automatyzacji tworzenia RHEL obrazy dla [kreatora obrazów platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z Menedżera funkcji Hyper-V

### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, że zostały już uzyskane plik ISO z witryny sieci Web firmy Red Hat i zainstalowane obrazu systemu RHEL do wirtualnego dysku twardego (VHD). Aby uzyskać więcej szczegółów na temat sposobu używania Menedżera funkcji Hyper-V do zainstalowania obrazu systemu operacyjnego, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji systemu RHEL**

* Platforma Azure nie obsługuje formatu VHDX. Platforma Azure obsługuje tylko stała wirtualnego dysku twardego. Można użyć Menedżera funkcji Hyper-V umożliwia przekonwertowanie dysku na format wirtualnego dysku twardego, lub można użyć polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox, wybierz **ustalony rozmiar** zamiast domyślnego dynamicznie przydzielane opcji podczas tworzenia dysku.
* Platforma Azure obsługuje tylko maszyny wirtualne generacji 1. Możesz przekonwertować maszyny wirtualnej generacji 1, z dysku VHDX format pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku stałym rozmiarze. Nie można zmienić generacji maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maksymalny rozmiar, jaki jest dozwolony dla wirtualnego dysku twardego jest 1,023 GB.
* Menedżer woluminów logicznych (LVM) jest obsługiwana i może być używana dla dysku systemu operacyjnego lub dysków z danymi w usłudze Azure virtual machines. Jednak ogólnie zaleca się używać standardowych partycji na dysku systemu operacyjnego, a nie LVM. To rozwiązanie pozwoli uniknąć LVM wystąpił konflikt między nazwą sklonowane maszyny wirtualne, szczególnie w przypadku, gdy będziesz potrzebować Dołącz dysk systemu operacyjnego do innej maszyny wirtualnej identyczne do rozwiązywania problemów. Zobacz też [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dokumentacji.
* Wymagana jest obsługa jądra instalowanie systemów plików Format dysku uniwersalnym (UDF). Podczas pierwszego rozruchu na platformie Azure sformatowanych przy użyciu funkcji zdefiniowanej przez użytkownika nośnika, który jest dołączony do gościa przekazuje konfiguracji aprowizacji maszyny wirtualnej systemu Linux. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików UDF odczytywać konfigurację i aprowizacja maszyny wirtualnej.
* Konfiguruj partycji wymiany dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Więcej szczegółów można znaleźć w poniższych krokach. Zobacz też [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej systemu RHEL 6 z Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

1. W RHEL 6 NetworkManager może kolidować z agentem systemu Linux platformy Azure. Ten pakiet należy odinstalować, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu sieci Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/boot/grub/menu.lst` w edytorze tekstu i upewnij się, że jądra domyślna obejmuje następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To będzie również upewnij się, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.
    
    Ponadto zaleca się, że usunąć następujące parametry:
    
        rhgb quiet crashkernel=auto
    
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego.  Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB. Ta konfiguracja może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.


1. Upewnij się, że serwer protokołu secure shell (SSH) jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co zazwyczaj jest ustawieniem domyślnym. Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujący wiersz:

        ClientAliveInterval 180

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instalowanie pakietu WALinuxAgent usuwa NetworkManager i NetworkManager gnome pakietów, jeśli nie zostały już usunięte w kroku 3.

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Należy pamiętać, że dysk lokalny zasób to tymczasowy dysk i czy może być opróżnione, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, należy odpowiednio zmodyfikować następujące parametry w /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kliknij przycisk **akcji** > **Zamknij** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To będzie również upewnij się, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Ta konfiguracja również wyłączenie nowe konwencje nazw RHEL 7, dla kart interfejsu sieciowego. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, zwykle jest to wartość domyślna. Modyfikowanie `/etc/ssh/sshd_config` obejmujący następujący wiersz:

        ClientAliveInterval 180

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kliknij przycisk **akcji** > **Zamknij** w Menedżerze funkcji Hyper-V. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Przygotowywanie maszyny wirtualnej systemu RHEL 6 z KVM

1. Pobierz obraz KVM RHEL 6 z witryny sieci Web firmy Red Hat.

1. Ustaw hasło główne.

    Generowanie zaszyfrowane hasło, a następnie skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmień wartość pola drugiego użytkownika root z "." zaszyfrowane hasło.

1. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku na **qcow2**, a model urządzenia interfejsu sieci wirtualnej **virtio**. Następnie uruchom maszynę wirtualną i zaloguj się jako użytkownik główny.

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu sieci Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # chkconfig network on

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby przeprowadzić tę konfigurację, otwórz `/boot/grub/menu.lst` w edytorze tekstu i upewnij się, że jądra domyślna obejmuje następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To będzie również upewnij się, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.
    
    Ponadto zaleca się usunięcie następujących parametrów:
    
        rhgb quiet crashkernel=auto
    
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.


1. Dodaj moduły funkcji Hyper-V do initramfs:  

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ponownie skompiluj initramfs:

        # dracut -f -v

1. Odinstaluj pakiet cloud-init:

        # yum remove cloud-init

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # chkconfig sshd on

    Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w **/etc/waagent.conf** odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

        # subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną w KVM.

1. Konwertowanie obrazu qcow2 formatu wirtualnego dysku twardego.

> [!NOTE]
> Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z KVM

1. Pobierz obraz KVM RHEL 7 z witryny sieci Web firmy Red Hat. Ta procedura wykorzystuje RHEL 7, co w przykładzie.

1. Ustaw hasło główne.

    Generowanie zaszyfrowane hasło, a następnie skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmień drugie pole użytkownika root z "." zaszyfrowane hasło.

1. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku na **qcow2**, a model urządzenia interfejsu sieci wirtualnej **virtio**. Następnie uruchom maszynę wirtualną i zaloguj się jako użytkownik główny.

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj swoją subskrypcję firmy Red Hat pozwala na instalowanie pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby przeprowadzić tę konfigurację, otwórz `/etc/default/grub` w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Polecenie to zapewnia, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Polecenie również wyłącza nowe konwencje nazw RHEL 7, dla kart interfejsu sieciowego. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Dodawanie modułów funkcji Hyper-V w initramfs.

    Edytuj `/etc/dracut.conf` i Dodaj zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ponownie skompiluj initramfs:

        # dracut -f -v

1. Odinstaluj pakiet cloud-init:

        # yum remove cloud-init

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # systemctl enable sshd

    Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

    Włącz usługę waagent:

        # systemctl enable waagent.service

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

        # subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną w KVM.

1. Konwertowanie obrazu qcow2 formatu wirtualnego dysku twardego.

> [!NOTE]
> Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z programu VMware
### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, zainstalowano maszynę wirtualną systemu RHEL w środowisku VMware. Aby uzyskać szczegółowe informacje o sposobie instalowania systemu operacyjnego w programie VMware, zobacz [Przewodnik instalacji systemu operacyjnego gościa VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Po zainstalowaniu systemu operacyjnego Linux, firma Microsoft zaleca użycie standardowe partycje, a nie LVM, co często jest ustawieniem domyślnym dla wielu urządzeń. Pozwoli to uniknąć LVM wystąpił konflikt między nazwą klonowana maszyna wirtualna, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny wirtualnej w celu rozwiązywania problemów. LVM lub woluminu macierzy RAID używać na dyskach danych, jeśli preferowane.
* Konfiguruj partycji wymiany dysku systemu operacyjnego. Można skonfigurować agenta systemu Linux, aby utworzyć plik wymiany na dysk tymczasowy zasobów. Więcej informacji na ten temat można znaleźć w kolejnych krokach.
* Po utworzeniu wirtualnego dysku twardego, wybierz **Store dysku wirtualnego w postaci jednego pliku**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Przygotowywanie maszyny wirtualnej systemu RHEL 6 z programu VMware
1. W RHEL 6 NetworkManager może kolidować z agentem systemu Linux platformy Azure. Ten pakiet należy odinstalować, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

1. Utwórz plik o nazwie **sieci** w katalogu/etc/sysconfig/zawierający następujący tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu sieci Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   To będzie również upewnij się, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Dodaj moduły funkcji Hyper-V do initramfs:

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ponownie skompiluj initramfs:

        # dracut -f -v

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, zwykle jest to wartość domyślna. Modyfikowanie `/etc/ssh/sshd_config` obejmujący następujący wiersz:

    ClientAliveInterval 180

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną i konwertowania pliku VMDK do pliku VHD.

> [!NOTE]
> Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z programu VMware
1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstów i Edytuj `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ta konfiguracja gwarantuje, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. On również wyłącza nowe konwencje nazw RHEL 7 dla kart interfejsu sieciowego. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Dodaj moduły funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf`, Dodaj zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Ponownie skompiluj initramfs:

        # dracut -f -v

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. To ustawienie jest zazwyczaj domyślną. Modyfikowanie `/etc/ssh/sshd_config` obejmujący następujący wiersz:

        ClientAliveInterval 180

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, jeśli maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną i przekonwertować plik VMDK format wirtualnego dysku twardego.

> [!NOTE]
> Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z obrazu ISO za pomocą pliku Stwórz automatycznie
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Przygotowywanie maszyny wirtualnej RHEL 7, z pliku Stwórz

1.  Tworzenie pliku Stwórz, który zawiera następującą zawartością, a następnie zapisz plik. Aby uzyskać szczegółowe informacje na temat instalowania Stwórz, zobacz [Przewodnik instalacji Stwórz](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Umieść plik Stwórz, w którym system instalacji do niego dostęp.

1. W Menedżerze funkcji Hyper-V Utwórz nową maszynę wirtualną. Na **Podłączanie wirtualnego dysku twardego** wybierz opcję **Dołącz wirtualny dysk twardy później**i ukończenie Kreatora nowej maszyny wirtualnej.

1. Otwórz ustawienia maszyny wirtualnej:

    a.  Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej. Upewnij się, że wybrano **formatu wirtualnego dysku twardego** i **o stałym rozmiarze**.

    b.  Dołącz instalacji ISO do stacji dysków DVD.

    c.  Ustaw systemu BIOS z dysku CD.

1. Uruchom maszynę wirtualną. Gdy pojawi się w podręczniku instalacji, naciśnij klawisz **kartę** do konfigurowania opcji rozruchu.

1. Wprowadź `inst.ks=<the location of the kickstart file>` na końcu opcji rozruchu, a następnie naciśnij klawisz **Enter**.

1. Poczekaj na zakończenie instalacji. Po zakończeniu, maszyna wirtualna zostanie zamknięta automatycznie. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="known-issues"></a>Znane problemy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Sterownik funkcji Hyper-V nie może znajdować się w początkowej dysku RAM, korzystając z funkcji hypervisor innej niż Hyper-V

W niektórych przypadkach instalatorów systemu Linux może nie zawierać sterowników dla funkcji Hyper-V w początkowej dysku RAM (initrd lub initramfs) chyba że Linux wykryje, że jest uruchomiona w środowisku funkcji Hyper-V.

Podczas korzystania z różnych wirtualizacji systemu (czyli Virtualbox, Xen, itp.), aby przygotować obraz systemu Linux, może być konieczne odbudowanie initrd w celu zapewnienia, że co najmniej modułów jądra hv_vmbus i hv_storvsc są dostępne w początkowej dysku RAM. To znany problem z co najmniej w systemach, które są oparte na nadrzędne dystrybucji firmy Red Hat.

Aby rozwiązać ten problem, Dodaj modułów funkcji Hyper-V do initramfs i skompilować go ponownie:

Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Ponownie skompiluj initramfs:

        # dracut -f -v

Aby uzyskać więcej informacji, zapoznaj się z informacjami [odbudowywania initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz przystąpić do wirtualnego dysku twardego w systemie Red Hat Enterprise Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy, w przypadku przekazywania pliku VHD na platformie Azure, zobacz [Utwórz Maszynę wirtualną systemu Linux z niestandardowego dysku](upload-vhd.md#option-1-upload-a-vhd).

Aby uzyskać więcej szczegółów na temat funkcji hypervisor, które są certyfikowane do uruchamiania w systemie Red Hat Enterprise Linux, zobacz [witryny sieci Web firmy Red Hat](https://access.redhat.com/certified-hypervisors).
