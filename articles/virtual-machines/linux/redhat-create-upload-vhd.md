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
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: 9893ab83584e2fc93ea9cf29c0b74b957080d5ad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Red Hat dla platformy Azure
W tym artykule dowiesz się, jak przygotować maszyny wirtualnej Red Hat Enterprise Linux (RHEL) do użycia na platformie Azure. Wersje RHEL, które zostały omówione w tym artykule są 6.7 + i 7.1 +. Funkcje hypervisor w celu przygotowania, które zostały omówione w tym artykule są maszyny wirtualnej funkcji Hyper-V, na podstawie jądra (KVM) i VMware. Aby uzyskać więcej informacji o wymaganiach dotyczących kwalifikuje się do uczestnictwa w programie dostęp do chmury Red Hat, zobacz [Red Hat dostęp do chmury witryny sieci Web](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) i [systemem RHEL na platformie Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z Menedżera funkcji Hyper-V

### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, że zostały już uzyskane z pliku ISO z witryny sieci Web Red Hat i zainstalowane obrazu RHEL do wirtualnego dysku twardego (VHD). Aby uzyskać więcej informacji o sposobie użycia Menedżera funkcji Hyper-V do zainstalowania obrazu systemu operacyjnego, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

**RHEL uwagi instalacji**

* Azure nie obsługuje formatu VHDX. Azure obsługuje tylko stałe wirtualnego dysku twardego. Menedżer funkcji Hyper-V umożliwia Konwertuj dysk na format wirtualnego dysku twardego, lub można użyć polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox, wybierz **stały rozmiar** zamiast domyślnego dynamicznie przydzielane opcji podczas tworzenia dysku.
* Azure obsługuje tylko maszyny wirtualne generacji 1. Maszyny wirtualne generacji 1 można przekonwertować z VHDX do formatu pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku o stałym rozmiarze. Nie można zmienić generację maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [należy tworzyć maszyny wirtualne generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maksymalny rozmiar, jaki jest dozwolony dla wirtualnego dysku twardego jest 1,023 GB.
* Po zainstalowaniu systemu operacyjnego Linux, zaleca się użycie standardowe partycje, a nie logiczne woluminu Manager (LVM), często jest to wartość domyślna dla wielu urządzeń. Takie rozwiązanie pozwoli uniknąć LVM nazwa powoduje konflikt z sklonowane maszyny wirtualne, szczególnie jeśli kiedykolwiek zajdzie potrzeba Podłącz dysk systemu operacyjnego do innej maszyny wirtualnej identyczne do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi.
* Wymagana jest obsługa jądra służący do instalowania systemów plików uniwersalny Format dysku (UDF). Przy pierwszym uruchomieniu komputera na platformie Azure sformatowany UDF nośnika dołączonego do Gość przekazuje inicjowania obsługi konfiguracji maszyny wirtualnej systemu Linux. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików funkcji zdefiniowanej przez użytkownika do odczytu konfiguracji i udostępnić maszynie wirtualnej.
* Wersje starsze niż 2.6.37 jądra systemu Linux nie obsługują dostępu niejednolitego pamięci (NUMA) w ramach funkcji Hyper-V o dużym rozmiarze maszyny wirtualnej. Ten problem wpływa głównie na starszą dystrybucje, korzystających z nadrzędnego jądra Red Hat 2.6.32 zostało ustalone w 6.6 RHEL (jądra-2.6.32 504). Systemy systemem jądra niestandardowych, które są starsze niż 2.6.37 lub należy ustawić na podstawie RHEL jądra, które są starsze niż 2.6.32-504 `numa=off` rozruch parametru w wierszu polecenia jądra w grub.conf. Aby uzyskać więcej informacji, zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Więcej szczegółów można znaleźć w poniższych krokach. Zobacz też [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Przygotowanie RHEL 6 maszyny wirtualnej z Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. 6 RHEL NetworkManager może zakłócać agenta systemu Linux platformy Azure. Odinstalowania tego pakietu, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

4. Utwórz lub Edytuj `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

8. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/boot/grub/menu.lst` w edytorze tekstu i upewnij się, że jądra domyślna zawiera następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To zapewni również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów.
    
    Ponadto zaleca się, że należy usunąć następujące parametry:
    
        rhgb quiet crashkernel=auto
    
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego.  Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszyny wirtualnej za pomocą 128 MB lub większej. Ta konfiguracja może być problemy na mniejsze rozmiary maszyny wirtualnej.

    >[!Important]
    RHEL 6.5 lub starszej należy również ustawić `numa=off` parametru jądra. Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

11. Upewnij się, że serwer bezpiecznej powłoki (SSH) jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, zazwyczaj jest to wartość domyślna. Zmień /etc/ssh/sshd_config ma zawierać następujący wiersz:

        ClientAliveInterval 180

12. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instalowanie pakietu WALinuxAgent usuwa NetworkManager i pakiety NetworkManager gnome Jeśli nie zostały już usunięte w kroku 3.

13. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Należy pamiętać, że dysk zasobu lokalnego jest tymczasowe i czy mogą być opróżniany podczas maszyny wirtualnej jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Wyrejestruj subskrypcji (w razie potrzeby), uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

15. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. Kliknij przycisk **akcji** > **zamknąć** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Przygotowanie RHEL 7 maszyny wirtualnej z Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. Utwórz lub Edytuj `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

6. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstów i edycja `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To zapewni również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. Ta konfiguracja również wyłącza nowych systemów RHEL 7 konwencji nazewnictwa, dla kart sieciowych. Ponadto zaleca się, że należy usunąć następujące parametry:
   
        rhgb quiet crashkernel=auto
   
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej najmniej 128 MB, mogą być problemy na mniejsze rozmiary maszyny wirtualnej.

8. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby odbudować chodników konfiguracji:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Sprawdź, czy serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, która zwykle jest ustawiona domyślnie. Modyfikowanie `/etc/ssh/sshd_config` ma zawierać następujący wiersz:

        ClientAliveInterval 180

10. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Zauważ, że dysk lokalny zasób jest tymczasowego, i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, należy zmodyfikować następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

        # sudo subscription-manager unregister

14. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Kliknij przycisk **akcji** > **zamknąć** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Przygotowanie maszyny wirtualnej RHEL 6 z KVM

1. Pobierz obraz KVM RHEL 6 z Red Hat witryny sieci Web.

2. Ustaw hasło główne.

    Generowanie zaszyfrowane hasło, a następnie skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmiany w tym polu drugiego użytkownika root z "!" zaszyfrowane hasło.

3. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku **qcow2**i ustaw model urządzenia interfejsu sieci wirtualnej **virtio**. Następnie uruchom maszynę wirtualną, a następnie zaloguj się jako katalogu głównego.

4. Utwórz lub Edytuj `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # chkconfig network on

8. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę konfigurację, otwórz `/boot/grub/menu.lst` w edytorze tekstu i upewnij się, że jądra domyślna zawiera następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To zapewni również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów.
    
    Ponadto zaleca się, że należy usunąć następujące parametry:
    
        rhgb quiet crashkernel=auto
    
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej najmniej 128 MB, mogą być problemy na mniejsze rozmiary maszyny wirtualnej.

    >[!Important]
    RHEL 6.5 lub starszej należy również ustawić `numa=off` parametru jądra. Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

10. Dodawanie modułów funkcji Hyper-V do initramfs:  

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Odbuduj initramfs:

        # dracut -f -v

11. Odinstaluj init chmury:

        # yum remove cloud-init

12. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # chkconfig sshd on

    Zmień /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

        # chkconfig waagent on

15. Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Zauważ, że dysk lokalny zasób jest tymczasowego, i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, należy zmodyfikować następujące parametry w **/etc/waagent.conf** odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Wyrejestruj subskrypcji (w razie potrzeby), uruchamiając następujące polecenie:

        # subscription-manager unregister

17. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Zamknij maszynę wirtualną w KVM.

19. Konwertuj obraz qcow2 na format wirtualnego dysku twardego.

> [!NOTE]
> Jest znaną usterką w wersjach qemu img > = 2.2.1, których wynikiem jest nieprawidłowo sformatowany dysk VHD. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się qemu-img 2.2.0 lub starszego, lub po zaktualizowaniu 2.6 lub nowszy. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
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

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Przygotowanie maszyny wirtualnej RHEL 7 z KVM

1. Pobierz obraz KVM RHEL 7 z Red Hat witryny sieci Web. Ta procedura wykorzystuje RHEL 7, co w przykładzie.

2. Ustaw hasło główne.

    Generowanie zaszyfrowane hasło, a następnie skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmień drugie pole użytkownika głównego z "!" zaszyfrowane hasło.

3. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku **qcow2**i ustaw model urządzenia interfejsu sieci wirtualnej **virtio**. Następnie uruchom maszynę wirtualną, a następnie zaloguj się jako katalogu głównego.

4. Utwórz lub Edytuj `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

7. Zarejestruj subskrypcję Red Hat umożliwia przeprowadzenie instalacji pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę konfigurację, otwórz `/etc/default/grub` w edytorze tekstów i edycja `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To polecenie sprawia, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. Polecenie również wyłącza nowych systemów RHEL 7 konwencji nazewnictwa, dla kart sieciowych. Ponadto zaleca się, że należy usunąć następujące parametry:
   
        rhgb quiet crashkernel=auto
   
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej najmniej 128 MB, mogą być problemy na mniejsze rozmiary maszyny wirtualnej.

9. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby odbudować chodników konfiguracji:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Dodawanie modułów funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf` i Dodaj zawartość:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Odbuduj initramfs:

        # dracut -f -v

11. Odinstaluj init chmury:

        # yum remove cloud-init

12. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # systemctl enable sshd

    Zmień /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

    Włącz usługę agenta waagent:

        # systemctl enable waagent.service

15. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Zauważ, że dysk lokalny zasób jest tymczasowego, i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, należy zmodyfikować następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Wyrejestruj subskrypcji (w razie potrzeby), uruchamiając następujące polecenie:

        # subscription-manager unregister

17. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Zamknij maszynę wirtualną w KVM.

19. Konwertuj obraz qcow2 na format wirtualnego dysku twardego.

> [!NOTE]
> Jest znaną usterką w wersjach qemu img > = 2.2.1, których wynikiem jest nieprawidłowo sformatowany dysk VHD. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się qemu-img 2.2.0 lub starszego, lub po zaktualizowaniu 2.6 lub nowszy. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
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
W tej sekcji założono, zainstalowano RHEL maszyny wirtualnej w środowisku programu VMware. Aby uzyskać więcej informacji o sposobie instalowania systemu operacyjnego w środowisku programu VMware, zobacz [Przewodnik instalacji systemu operacyjnego gościa VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* Po zainstalowaniu systemu operacyjnego Linux, zaleca się użycie standardowe partycje, a nie LVM, często jest to wartość domyślna dla wielu urządzeń. Pozwoli to uniknąć konfliktów nazw LVM ze sklonowanej maszyny wirtualnej, zwłaszcza w przypadku, gdy dysk systemu operacyjnego kiedykolwiek musi zostać dołączony do innej maszyny wirtualnej w celu rozwiązania problemu. LVM RAID można lub na dyskach danych, jeśli preferowane.
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Można skonfigurować agenta systemu Linux, aby utworzyć plik wymiany na dysku zasobów. Więcej informacji na ten temat można znaleźć w kolejnych krokach.
* Po utworzeniu wirtualnego dysku twardego, wybierz **magazynu wirtualnego dysku w postaci jednego pliku**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Przygotowanie RHEL 6 maszyny wirtualnej z programu VMware
1. 6 RHEL NetworkManager może zakłócać agenta systemu Linux platformy Azure. Odinstalowania tego pakietu, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

2. Utwórz plik o nazwie **sieci** w katalogu/etc/sysconfig/zawierający następujący tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. Przenieś lub usuń reguły udev, aby uniknąć generowania statyczne reguły dla interfejsu Ethernet. Te reguły powodować problemy podczas klonowania maszyny wirtualnej Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

6. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` w edytorze tekstów i edycja `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   To zapewni również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. Ponadto zaleca się, że należy usunąć następujące parametry:
   
        rhgb quiet crashkernel=auto
   
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej najmniej 128 MB, mogą być problemy na mniejsze rozmiary maszyny wirtualnej.

9. Dodawanie modułów funkcji Hyper-V do initramfs:

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Odbuduj initramfs:

        # dracut -f -v

10. Sprawdź, czy serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, która zwykle jest ustawiona domyślnie. Modyfikowanie `/etc/ssh/sshd_config` ma zawierać następujący wiersz:

    ClientAliveInterval 180

11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Zauważ, że dysk lokalny zasób jest tymczasowego, i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, należy zmodyfikować następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Wyrejestruj subskrypcji (w razie potrzeby), uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

14. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Zamknij maszynę wirtualną i konwertowania pliku VMDK do pliku VHD.

> [!NOTE]
> Jest znaną usterką w wersjach qemu img > = 2.2.1, których wynikiem jest nieprawidłowo sformatowany dysk VHD. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się qemu-img 2.2.0 lub starszego, lub po zaktualizowaniu 2.6 lub nowszy. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
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


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Przygotowanie RHEL 7 maszyny wirtualnej z programu VMware
1. Utwórz lub Edytuj `/etc/sysconfig/network` pliku i Dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Upewnij się, że Usługa sieciowa będzie uruchamiane podczas rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

4. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstów i edycja `GRUB_CMDLINE_LINUX` parametru. Na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Taka konfiguracja powoduje, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. On również wyłącza nowych systemów RHEL 7 konwencje nazewnictwa dla kart sieciowych. Ponadto zaleca się, że należy usunąć następujące parametry:
   
        rhgb quiet crashkernel=auto
   
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowanych w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej najmniej 128 MB, mogą być problemy na mniejsze rozmiary maszyny wirtualnej.

6. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby odbudować chodników konfiguracji:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Dodawanie modułów funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf`, Dodaj zawartość:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Odbuduj initramfs:

        # dracut -f -v

8. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. To ustawienie jest zazwyczaj domyślnie. Modyfikowanie `/etc/ssh/sshd_config` ma zawierać następujący wiersz:

        ClientAliveInterval 180

9. Pakiet WALinuxAgent `WALinuxAgent-<version>`, ma zostać przypisany do repozytorium dodatki Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po udostępnieniu maszyny wirtualnej na platformie Azure. Zauważ, że dysk lokalny zasób jest tymczasowego, i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku, należy zmodyfikować następujące parametry w `/etc/waagent.conf` odpowiednio:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

        # sudo subscription-manager unregister

13. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Zamknij maszynę wirtualną i konwertowania pliku VMDK do formatu wirtualnego dysku twardego.

> [!NOTE]
> Jest znaną usterką w wersjach qemu img > = 2.2.1, których wynikiem jest nieprawidłowo sformatowany dysk VHD. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się qemu-img 2.2.0 lub starszego, lub po zaktualizowaniu 2.6 lub nowszy. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z obrazu ISO za pomocą pliku kickstart automatycznie
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Przygotowanie RHEL 7 maszyny wirtualnej z pliku kickstart

1.  Utwórz plik kickstart, który zawiera następującą zawartość, a następnie zapisz plik. Aby uzyskać szczegółowe informacje o instalacji kickstart, zobacz [Przewodnik instalacji Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Umieść plik kickstart, gdy system instalacji do niego dostęp.

3. W Menedżerze funkcji Hyper-V Utwórz nową maszynę wirtualną. Na **Podłączanie wirtualnego dysku twardego** wybierz pozycję **Dołącz wirtualny dysk twardy później**i ukończenie Kreatora nowej maszyny wirtualnej.

4. Otwórz ustawienia maszyny wirtualnej:

    a.  Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej. Upewnij się wybrać **formatu wirtualnego dysku twardego** i **o stałym rozmiarze**.

    b.  Dołącz instalacji ISO na dysk DVD.

    c.  Ustaw systemu BIOS z dysku CD.

5. Uruchamia maszynę wirtualną. Gdy pojawi się w podręczniku instalacji, naciśnij klawisz **kartę** do konfigurowania opcji rozruchu.

6. Wprowadź `inst.ks=<the location of the kickstart file>` na końcu opcje rozruchu, a następnie naciśnij klawisz **Enter**.

7. Poczekaj na zakończenie instalacji. Po zakończeniu, maszyna wirtualna zostanie zamknięta automatycznie. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="known-issues"></a>Znane problemy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Sterownik funkcji Hyper-V nie może znajdować się w początkowej dysku RAM, korzystając z funkcji hypervisor z systemem innym niż do funkcji Hyper-V

W niektórych przypadkach instalatorów systemu Linux może nie zawierać sterowników dla funkcji Hyper-V w początkowej dysku RAM (initrd lub initramfs), chyba że Linux wykryje, że jest on uruchomiony w środowisku funkcji Hyper-V.

Podczas korzystania z różnych wirtualizacji systemu (czyli Virtualbox, Xen itp.), aby przygotować obraz systemu Linux, może być konieczne odbudowanie initrd w celu zapewnienia, że co najmniej modułów jądra hv_vmbus i hv_storvsc są dostępne w początkowej dysku RAM. To znany problem z co najmniej w systemach, które są oparte na nadrzędnego dystrybucji Red Hat.

Aby rozwiązać ten problem, Dodaj modułów funkcji Hyper-V do initramfs i odbuduj go:

Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Odbuduj initramfs:

        # dracut -f -v

Aby uzyskać więcej informacji, zapoznaj się z informacjami [odbudowywania initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz używać wirtualnego dysku twardego Red Hat Enterprise Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).

Aby uzyskać więcej informacji o funkcjach hypervisor, które są certyfikowane do uruchamiania Red Hat Enterprise Linux, zobacz [Red Hat witryny sieci Web](https://access.redhat.com/certified-hypervisors).
