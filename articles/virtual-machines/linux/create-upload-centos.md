---
title: Tworzenie i przesyłanie dysku VHD z systemem Linux opartym na centos
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Linuksa oparty na systemie CentOS.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066779"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej bazującej na systemie CentOS dla platformy Azure

Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Linuksa oparty na systemie CentOS.

* [Przygotowanie maszyny wirtualnej CentOS 6.x dla platformy Azure](#centos-6x)
* [Przygotowanie maszyny wirtualnej CentOS 7.0+ dla platformy Azure](#centos-70)


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że system operacyjny CentOS (lub podobna pochodna) systemu operacyjnego Linux został już zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików vhd, na przykład rozwiązanie wirtualizacji, takie jak Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji centos**

* Zobacz też [Ogólne uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać więcej wskazówek dotyczących przygotowywania systemu Linux dla platformy Azure.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stały dysk VHD**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox oznacza to **wybranie stałego rozmiaru,** w przeciwieństwie do domyślnego dynamicznie przydzielonego podczas tworzenia dysku.
* Podczas instalacji systemu Linux *zaleca się* używanie standardowych partycji zamiast LVM (często domyślnych dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z sklonowanych maszyn wirtualnych, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej identycznej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi.
* Wymagana jest obsługa jądra do montażu systemów plików UDF. Przy pierwszym uruchomieniu na platformie Azure konfiguracja inicjowania obsługi administracyjnej jest przekazywana do maszyny Wirtualnej systemu Linux za pośrednictwem nośnika w formacie UDF, który jest dołączony do gościa. Agent systemu Azure Linux musi być w stanie zainstalować system plików UDF, aby odczytać jego konfigurację i aprowizować maszynę wirtualną.
* Wersje jądra linuksa poniżej 2.6.37 nie obsługują numa na Hyper-V z większymi rozmiarami maszyn wirtualnych. Ten problem dotyczy przede wszystkim starszych dystrybucji przy użyciu jądra Red Hat 2.6.32 i został naprawiony w RHEL 6.6 (jądro-2.6.32-504). Systemy z niestandardowymi jądrami starszymi niż 2.6.37 lub jądrami opartymi na RHEL starszymi niż 2.6.32-504 muszą ustawić parametr `numa=off` rozruchowy w wierszu polecenia jądra w grub.conf. Aby uzyskać więcej informacji, zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Zobacz [Uwagi dotyczące instalacji systemu Linux, aby](create-upload-generic.md#general-linux-installation-notes) uzyskać więcej informacji.

## <a name="centos-6x"></a>CentOS 6.x

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. W systemie CentOS 6 networkmanager może zakłócać działanie agenta systemu Azure Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Zmodyfikuj reguły udev, aby uniknąć generowania reguł statycznych dla interfejsów Ethernet. Te reguły mogą powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

    ```bash
    sudo chkconfig network on
    ```

8. Jeśli chcesz użyć dublowania OpenLogic, które są hostowane w centrach `/etc/yum.repos.d/CentOS-Base.repo` danych platformy Azure, następnie zastąp plik następującymi repozytoriami.  Spowoduje to również dodanie repozytorium **[openlogic],** które zawiera dodatkowe pakiety, takie jak agent systemu Azure Linux:

   ```console
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
   ```

    > [!Note]
    > W dalszej części tego przewodnika przyjęto założenie, że używasz co najmniej `[openlogic]` repozytorium, które będzie używane do zainstalowania agenta systemu Azure Linux poniżej.

9. Dodaj następujący wiersz do /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zaktualizować system za pomocą najnowszych pakietów:

    ```bash
    yum clean all
    ```

    Jeśli nie tworzysz obrazu dla starszej wersji CentOS, zaleca się zaktualizowanie wszystkich pakietów do najnowszych:

    ```bash
    sudo yum -y update
    ```

    Po uruchomieniu tego polecenia może być wymagane ponowne uruchomienie.

11. (Opcjonalnie) Zainstaluj sterowniki dla usług integracji systemu Linux (LIS).

    > [!IMPORTANT]
    > Ten krok jest **wymagany** dla CentOS 6.3 i wcześniejszych i opcjonalnych dla nowszych wersji.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Alternatywnie można wykonać instrukcje instalacji ręcznej na [stronie pobierania LIS,](https://www.microsoft.com/download/details.aspx?id=51612) aby zainstalować rpm na maszynie wirtualnej.

12. Zainstaluj agenta systemu Azure Linux i zależności. Uruchom i włącz usługę waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Pakiet WALinuxAgent usunie pakiety NetworkManager i NetworkManager-gnome, jeśli nie zostały już usunięte zgodnie z opisem w kroku 3.

13. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz `/boot/grub/menu.lst` w edytorze tekstu i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w obsłudze platformy Azure w rozwiązywaniu problemów z debugowaniem.

    Oprócz powyższego zaleca się *usunięcie* następujących parametrów:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego.  Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejszy ilość dostępnej pamięci na maszynie Wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

    > [!Important]
    > CentOS 6.5 i wcześniej musi również `numa=off`ustawić parametr jądra . Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.

15. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (zobacz `/etc/waagent.conf` poprzedni krok) odpowiednio zmodyfikuj następujące parametry:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.



## <a name="centos-70"></a>CentOS 7.0+

**Zmiany w CentOS 7 (i podobnych instrumentach pochodnych)**

Przygotowanie maszyny wirtualnej CentOS 7 dla platformy Azure jest bardzo podobne do CentOS 6, jednak istnieje kilka ważnych różnic, na które warto zwrócić uwagę:

* Pakiet NetworkManager nie jest już w konflikcie z agentem systemu Azure Linux. Ten pakiet jest zainstalowany domyślnie i zaleca się, że nie jest usuwany.
* GRUB2 jest teraz używany jako domyślny bootloader, więc procedura edycji parametrów jądra uległa zmianie (patrz poniżej).
* XFS jest teraz domyślnym systemem plików. System plików ext4 może być nadal używany w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.

3. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Zmodyfikuj reguły udev, aby uniknąć generowania reguł statycznych dla interfejsów Ethernet. Te reguły mogą powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Jeśli chcesz użyć dublowania OpenLogic, które są hostowane w centrach `/etc/yum.repos.d/CentOS-Base.repo` danych platformy Azure, następnie zastąp plik następującymi repozytoriami.  Spowoduje to również dodanie repozytorium **[openlogic],** które zawiera pakiety dla agenta systemu Azure Linux:

   ```console
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
   ```
    
   > [!Note]
   > W dalszej części tego przewodnika przyjęto założenie, że używasz co najmniej `[openlogic]` repozytorium, które będzie używane do zainstalowania agenta systemu Azure Linux poniżej.

7. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zainstalować wszelkie aktualizacje:

    ```bash
    sudo yum clean all
    ```

    Jeśli nie tworzysz obrazu dla starszej wersji CentOS, zaleca się zaktualizowanie wszystkich pakietów do najnowszych:

    ```bash
    sudo yum -y update
    ```

    Po uruchomieniu tego polecenia może być wymagany ponowny rozruch.

8. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` edytor tekstu `GRUB_CMDLINE_LINUX` i edytuj parametr, na przykład:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w obsłudze platformy Azure w rozwiązywaniu problemów z debugowaniem. Wyłącza również nowe konwencje nazewnictwa CentOS 7 dla kart sieciowych. Oprócz powyższego zaleca się *usunięcie* następujących parametrów:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejszy ilość dostępnej pamięci na maszynie Wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

9. Po zakończeniu edycji `/etc/default/grub` na powyżej uruchom następujące polecenie, aby odbudować konfigurację grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Jeśli tworzenie obrazu z **VMware, VirtualBox lub KVM:** Upewnij się, że sterowniki Funkcji Hyper-V są zawarte w initramfs:

    Edytuj, `/etc/dracut.conf`dodaj zawartość:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Odbuduj initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Zainstaluj agenta systemu Azure Linux i zależności:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (zobacz `/etc/waagent.conf` poprzedni krok) odpowiednio zmodyfikuj następujące parametry:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz używać wirtualnego dysku twardego CentOS Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
