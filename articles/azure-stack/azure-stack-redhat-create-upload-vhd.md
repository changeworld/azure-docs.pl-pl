---
title: Tworzenie i przekazywanie Red Hat Enterprise Linux wirtualnego dysku twardego do użycia w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Red Hat Linux.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: mabrigg
ms.reviewer: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: 728839accbce80ece6795e098d5d2855320fab06
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006682"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat do usługi Azure Stack

W tym artykule dowiesz się, jak przygotować maszynę wirtualną Red Hat Enterprise Linux (RHEL) do użycia w usłudze Azure Stack. Wersje systemu RHEL, które zostały omówione w tym artykule są 7.1 +. Funkcji hypervisor do przygotowywania, które zostały omówione w tym artykule są maszyny wirtualne funkcji Hyper-V, na podstawie jądra (KVM) i VMware.

Aby uzyskać informacje dotyczące obsługi w systemie Red Hat Enterprise Linux, zapoznaj się [firmy Red Hat i usługi Azure Stack: Często zadawane pytania dotyczące](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z Menedżera funkcji Hyper-V

W tej sekcji założono, że już istnieje plik ISO z witryny sieci Web firmy Red Hat i zainstalowane obrazu systemu RHEL do wirtualnego dysku twardego (VHD). Aby uzyskać więcej informacji o sposobie instalowania obrazu systemu operacyjnego za pomocą Menedżera funkcji Hyper-V, zobacz [należy zainstalować rolę funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Uwagi dotyczące instalacji systemu RHEL

* Usługa Azure Stack nie obsługuje formatu VHDX. Platforma Azure obsługuje tylko stała wirtualnego dysku twardego. Można użyć Menedżera funkcji Hyper-V umożliwia przekonwertowanie dysku na format wirtualnego dysku twardego, lub można użyć polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox, wybierz **ustalony rozmiar** zamiast domyślnego dynamicznie przydzielane opcji podczas tworzenia dysku.
* Usługa Azure Stack obsługuje tylko maszyny wirtualne generacji 1. Możesz przekonwertować maszyny wirtualnej generacji 1, z dysku VHDX format pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku stałym rozmiarze. Nie można zmienić generacji maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maksymalny rozmiar, jaki jest dozwolony dla wirtualnego dysku twardego jest 1,023 GB.
* Po zainstalowaniu systemu operacyjnego Linux, firma Microsoft zaleca użycie standardowe partycje, a nie logiczne woluminu Menedżera (LVM), co często jest ustawieniem domyślnym dla wielu urządzeń. Tej praktyką pozwala uniknąć LVM wystąpił konflikt między nazwą sklonowane maszyny wirtualne, szczególnie w przypadku, gdy będziesz potrzebować Dołącz dysk systemu operacyjnego do innej maszyny wirtualnej identyczne do rozwiązywania problemów.
* Wymagana jest obsługa jądra instalowanie systemów plików Format dysku uniwersalnym (UDF). Przy pierwszym uruchomieniu komputera sformatowanych przy użyciu funkcji zdefiniowanej przez użytkownika nośnika, który jest dołączony do gościa przekazuje konfiguracji aprowizacji maszyny wirtualnej systemu Linux. Agent systemu Linux platformy Azure musi być zainstalowany system plików UDF odczytywać konfigurację i aprowizacja maszyny wirtualnej.
* Konfiguruj partycji wymiany dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux. Więcej informacji na temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1 MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego, należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1 MB przed dokonaniem konwersji. Więcej szczegółów można znaleźć w poniższych krokach.
* Usługa Azure Stack nie obsługuje pakietu cloud-init. Maszyna wirtualna musi być skonfigurowany z obsługiwaną wersją systemu Windows Azure Linux Agent (WALA).

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst, zgodnie z potrzebami:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Upewnij się, uruchamiania usługi sieci w czasie rozruchu, uruchamiając następujące polecenie:

    ```bash
    sudo systemctl enable network
    ```

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstu i modyfikować `GRUB_CMDLINE_LINUX` parametru. Na przykład:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Daje to gwarancję, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Ta konfiguracja również wyłączenie nowe konwencje nazw RHEL 7, dla kart interfejsu sieciowego.

   Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych. Zaleca się usunięcie następujących parametrów:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Zatrzymaj i Odinstaluj pakiet cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, zwykle jest to wartość domyślna. Modyfikowanie `/etc/ssh/sshd_config` obejmujący następujący wiersz:

    ```sh
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Dysk lokalny zasób jest tymczasowe i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

    ```bash
    sudo subscription-manager unregister
    ```

1. Jeśli używasz systemu, który został wdrożony przy użyciu urzędu certyfikacji przedsiębiorstwa systemu RHEL maszyny wirtualnej nie będzie zaufania certyfikatu głównego usługi Azure Stack. Należy umieścić, do magazynu zaufanych certyfikatów głównych. Zobacz [dodawanie zaufanych rtyfikaty serwerowi](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Kliknij przycisk **akcji** > **Zamknij** w Menedżerze funkcji Hyper-V.

1. Konwertuj wirtualny dysk twardy o stałym rozmiarze wirtualnego dysku twardego przy użyciu funkcji "Edytuj dysk" Menedżera funkcji Hyper-V lub polecenia programu PowerShell Convert-VHD. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z KVM

1. Pobierz obraz KVM RHEL 7 z witryny sieci Web firmy Red Hat. Ta procedura wykorzystuje RHEL 7, co w przykładzie.

1. Ustaw hasło główne.

    Generowanie zaszyfrowane hasło, a następnie skopiuj dane wyjściowe polecenia:

    ```bash
    openssl passwd -1 changeme
    ```

   Ustaw hasło główne z guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Zmień drugie pole użytkownika root z "." zaszyfrowane hasło.

1. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku na **qcow2**, a model urządzenia interfejsu sieci wirtualnej **virtio**. Następnie uruchom maszynę wirtualną i zaloguj się jako użytkownik główny.

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Upewnij się, uruchamiania usługi sieci w czasie rozruchu, uruchamiając następujące polecenie:

    ```bash
    sudo systemctl enable network
    ```

1. Zarejestruj swoją subskrypcję firmy Red Hat pozwala na instalowanie pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby przeprowadzić tę konfigurację, otwórz `/etc/default/grub` w edytorze tekstu i modyfikować `GRUB_CMDLINE_LINUX` parametru. Na przykład:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Polecenie to zapewnia, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. Polecenie również wyłącza nowe konwencje nazw RHEL 7, dla kart interfejsu sieciowego

   Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której wszystkie dzienniki są wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych. Zaleca się usunięcie następujących parametrów:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Dodawanie modułów funkcji Hyper-V w initramfs.

    Edytuj `/etc/dracut.conf` i Dodaj zawartość:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Ponownie skompiluj initramfs:

    ```bash
    dracut -f -v
    ```

1. Zatrzymaj i Odinstaluj pakiet cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

    ```bash
    systemctl enable sshd
    ```

    Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Podczas tworzenia niestandardowego dysku vhd dla usługi Azure Stack, należy pamiętać, że wersja WALinuxAgent między 2.2.20 i 2.2.35.1 (zarówno wyłącznie) nie działają w środowiskach usługi Azure Stack, systemem kompilacji przed 1903. Aby rozwiązać ten problem, należy zastosować poprawkę 1901/1902 lub wykonaj w drugiej połowie ta część instrukcji. 

    Jeśli korzystasz z usługi Azure Stack kompilacji 1903 (lub nowszym) albo zastosowano poprawkę 1901/1902, Pobierz pakiet WALinuxAgent z repozytorium dodatki Redhat w następujący sposób:
    
   Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

   Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```bash
    yum install WALinuxAgent
    ```

   Włącz usługę waagent:

    ```bash
    systemctl enable waagent.service
    ```
    
    
    Jeśli używasz usługi Azure Stack kompilacji przed 1903 i nie zostały zastosowane poprawki 1901/1902, należy wykonać te instrukcje, aby pobrać WALinuxAgent:
    
   a.   Pobierz narzędzia setuptools
    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```
   b. Pobierz i Rozpakuj najnowszą wersję agenta w usłudze github. Jest to przykład gdzie możemy pobrać "2.2.36" wersji z repozytorium github.
    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.36.zip
    unzip v2.2.36.zip
    cd WALinuxAgent-2.2.36
    ```
    c. Instalowanie pliku setup.py
    ```bash
    sudo python setup.py install
    ```
    d. Uruchom ponownie waagent
    ```bash
    sudo systemctl restart waagent
    ```
    e. Należy sprawdzić, czy wersja agenta jest zgodna z nazwą Twojej pobrany. W tym przykładzie należy go 2.2.36.
    
    ```bash
    waagent -version
    ```

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Dysk lokalny zasób jest tymczasowe i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

    ```bash
    subscription-manager unregister
    ```

1. Jeśli używasz systemu, który został wdrożony przy użyciu urzędu certyfikacji przedsiębiorstwa systemu RHEL maszyny wirtualnej nie będzie zaufania certyfikatu głównego usługi Azure Stack. Należy umieścić, do magazynu zaufanych certyfikatów głównych. Zobacz [dodawanie zaufanych rtyfikaty serwerowi](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Zamknij maszynę wirtualną w KVM.

1. Konwertowanie obrazu qcow2 formatu wirtualnego dysku twardego.

    > [!NOTE]
    > Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.

    Format raw, najpierw przekonwertuj obrazu:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Upewnij się, że rozmiar obrazu raw jest powiązana z 1 MB. W przeciwnym razie Zaokrąglenie w górę rozmiar w celu zapewnienia zgodności z 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Przekonwertuj pierwotne dysku wirtualnego dysku twardego stałych rozmiarach:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Lub z wersją qemu **2.6** obejmują `force_size` opcji:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z programu VMware

W tej sekcji założono, zainstalowano maszynę wirtualną systemu RHEL w środowisku VMware. Aby uzyskać szczegółowe informacje o sposobie instalowania systemu operacyjnego w programie VMware, zobacz [Przewodnik instalacji systemu operacyjnego gościa VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Po zainstalowaniu systemu operacyjnego Linux, firma Microsoft zaleca użycie standardowe partycje, a nie LVM, co często jest ustawieniem domyślnym dla wielu urządzeń. Umożliwia to uniknięcie LVM wystąpił konflikt między nazwą klonowana maszyna wirtualna, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny wirtualnej w celu rozwiązywania problemów. LVM lub woluminu macierzy RAID używać na dyskach danych, jeśli preferowane.
* Konfiguruj partycji wymiany dysku systemu operacyjnego. Można skonfigurować agenta systemu Linux, aby utworzyć plik wymiany na dysk tymczasowy zasobów. Więcej informacji na ten temat można znaleźć w kolejnych krokach.
* Po utworzeniu wirtualnego dysku twardego, wybierz **Store dysku wirtualnego w postaci jednego pliku**.

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z programu VMware

1. Tworzenie lub edytowanie `/etc/sysconfig/network` pliku i Dodaj następujący tekst:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Tworzenie lub edytowanie `/etc/sysconfig/network-scripts/ifcfg-eth0` pliku i Dodaj następujący tekst:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Upewnij się, że Usługa sieciowa zostanie uruchomione w czasie rozruchu, uruchamiając następujące polecenie:

    ```bash
    sudo chkconfig network on
    ```

1. Zarejestruj swoją subskrypcję firmy Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersza rozruchu jądra w konfiguracji programu grub obejmujący jądra dodatkowe parametry dla platformy Azure. Aby wykonać tę zmianę, otwórz `/etc/default/grub` w edytorze tekstu i modyfikować `GRUB_CMDLINE_LINUX` parametru. Na przykład:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Ta konfiguracja gwarantuje, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów. On również wyłącza nowe konwencje nazw RHEL 7 dla kart interfejsu sieciowego. Ponadto zaleca się usunięcie następujących parametrów:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Graficzne i cichy rozruchu nie są przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. Możesz pozostawić `crashkernel` opcji skonfigurowane w razie potrzeby. Należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej, o co najmniej 128 MB, który może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`, uruchom następujące polecenie, aby ponownie skompilować konfigurację programu grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Dodaj moduły funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf`, Dodaj zawartość:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Ponownie skompiluj initramfs:

    ```bash
    dracut -f -v
    ```

1. Zatrzymaj i Odinstaluj pakiet cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. To ustawienie jest zazwyczaj domyślną. Modyfikowanie `/etc/ssh/sshd_config` obejmujący następujący wiersz:

    ```sh
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>`, wypchnięciu do repozytorium dodatki firmy Red Hat. Włącz repozytorium dodatki, uruchamiając następujące polecenie:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.

    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Pamiętaj, że dysk lokalny zasób jest dyskiem tymczasowym może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure w poprzednim kroku, zmodyfikuj następujące parametry w `/etc/waagent.conf` odpowiednio:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Jeśli chcesz wyrejestrować subskrypcji, uruchom następujące polecenie:

    ```bash
    sudo subscription-manager unregister
    ```

1. Jeśli używasz systemu, który został wdrożony przy użyciu urzędu certyfikacji przedsiębiorstwa systemu RHEL maszyny wirtualnej nie będzie zaufania certyfikatu głównego usługi Azure Stack. Należy umieścić, do magazynu zaufanych certyfikatów głównych. Zobacz [dodawanie zaufanych rtyfikaty serwerowi](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej i przygotować je do inicjowania obsługi na platformie Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Zamknij maszynę wirtualną i przekonwertować plik VMDK format wirtualnego dysku twardego.

    > [!NOTE]
    > Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Format raw, najpierw przekonwertuj obrazu:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Upewnij się, że rozmiar obrazu raw jest powiązana z 1 MB. W przeciwnym razie Zaokrąglenie w górę rozmiar w celu zapewnienia zgodności z 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Przekonwertuj pierwotne dysku wirtualnego dysku twardego stałych rozmiarach:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Lub z wersją qemu **2.6** obejmują `force_size` opcji:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Przygotowywanie maszyny wirtualnej z systemem Red Hat z obrazu ISO za pomocą pliku Stwórz automatycznie

1. Tworzenie pliku Stwórz, który zawiera następującą zawartością, a następnie zapisz plik. Aby uzyskać szczegółowe informacje na temat instalowania Stwórz, zobacz [Przewodnik instalacji Stwórz](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
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

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
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

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Umieść plik Stwórz, w którym system instalacji do niego dostęp.

1. W Menedżerze funkcji Hyper-V Utwórz nową maszynę wirtualną. Na **Podłączanie wirtualnego dysku twardego** wybierz opcję **Dołącz wirtualny dysk twardy później**i ukończenie Kreatora nowej maszyny wirtualnej.

1. Otwórz ustawienia maszyny wirtualnej:

    a. Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej. Upewnij się, że wybrano **formatu wirtualnego dysku twardego** i **o stałym rozmiarze**.

    b. Dołącz instalacji ISO do stacji dysków DVD.

    c. Ustaw systemu BIOS z dysku CD.

1. Uruchamia maszynę wirtualną. Gdy pojawi się w podręczniku instalacji, naciśnij klawisz **kartę** do konfigurowania opcji rozruchu.

1. Wprowadź `inst.ks=<the location of the kickstart file>` na końcu opcji rozruchu, a następnie naciśnij klawisz **Enter**.

1. Poczekaj na zakończenie instalacji. Po zakończeniu, maszyna wirtualna jest wyłączana automatycznie. Wirtualnego dysku twardego systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="known-issues"></a>Znane problemy

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Sterownik funkcji Hyper-V nie może znajdować się w początkowej dysku RAM, korzystając z funkcji hypervisor innej niż Hyper-V

W niektórych przypadkach instalatorów systemu Linux może nie zawierać sterowników dla funkcji Hyper-V w początkowej dysku RAM (initrd lub initramfs) chyba że Linux wykryje, że jest uruchomiona w środowisku funkcji Hyper-V.

Podczas korzystania z różnych wirtualizacji systemu (czyli Virtualbox, Xen, itp.), aby przygotować obraz systemu Linux, może być konieczne odbudowanie initrd w celu zapewnienia, że co najmniej modułów jądra hv_vmbus i hv_storvsc są dostępne w początkowej dysku RAM. To znany problem z co najmniej w systemach, które są oparte na nadrzędne dystrybucji firmy Red Hat.

Aby rozwiązać ten problem, Dodaj modułów funkcji Hyper-V do initramfs i skompilować go ponownie:

Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Ponownie skompiluj initramfs:

```bash
dracut -f -v
```

Aby uzyskać więcej informacji, zobacz [odbudowywania initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przystąpić do wirtualnego dysku twardego w systemie Red Hat Enterprise Linux umożliwia tworzenie nowych maszyn wirtualnych w usłudze Azure Stack. Jeśli po raz pierwszy, w przypadku przekazywania pliku wirtualnego dysku twardego do usługi Azure Stack, zobacz [tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Aby uzyskać więcej informacji na temat funkcji hypervisor, które są certyfikowane do uruchamiania w systemie Red Hat Enterprise Linux, zobacz [witryny sieci Web firmy Red Hat](https://access.redhat.com/certified-hypervisors).
