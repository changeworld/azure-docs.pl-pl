---
title: Tworzenie i przekazywanie wirtualnego dysku twardego Oracle Linux | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: ca556b493740d66922fbdd96bac45114b1b9b5c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30909330"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej systemu Linux w środowisku Oracle dla platformy Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, zainstalowano system operacyjny Oracle Linux do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji takich jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux instalacji uwagi
* Zobacz także [ogólne informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) więcej porad na przygotowanie systemu Linux na platformie Azure.
* Zgodne jądra Red Hat programu Oracle i ich UEK3 (podzielenie Enterprise jądra) w funkcji Hyper-V i platformy Azure są obsługiwane. Aby uzyskać najlepsze rezultaty należy upewnić się, że najnowsze jądra podczas przygotowywania Oracle Linux dysk VHD.
* UEK2 programu Oracle nie jest obsługiwana w funkcji Hyper-V i platformy Azure, ponieważ nie zawiera wymaganych sterowników.
* VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Zalecane jest użycie standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji), podczas instalowania systemu Linux. Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi, jeśli preferowane.
* NUMA nie jest obsługiwana dla większych rozmiarów maszyn wirtualnych ze względu na błąd wersjach jądra systemu Linux poniżej 2.6.37. Ten problem ma wpływ przede wszystkim za pomocą nadrzędnego Red Hat 2.6.32 jądra. Ręczna instalacja agenta systemu Linux platformy Azure (agenta waagent) automatycznie spowoduje wyłączenie NUMA w konfiguracji CHODNIKÓW jądra systemu Linux. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Zobacz [informacje o instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) Aby uzyskać więcej informacji.
* Upewnij się, że `Addons` repozytorium jest włączona. Przeprowadź edycję pliku `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) lub `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) i zmień wiersz `enabled=0` do `enabled=1` w obszarze **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Należy wykonać kroki konfiguracji określone w systemie operacyjnym maszyny wirtualnej do uruchamiania na platformie Azure.

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** można otworzyć okna dla maszyny wirtualnej.
3. Odinstaluj NetworkManager, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Uwaga:** Jeśli pakiet nie jest już zainstalowany, to polecenie zakończy się niepowodzeniem z komunikatem o błędzie. Taki stan jest oczekiwany.
4. Utwórz plik o nazwie **sieci** w `/etc/sysconfig/` katalog zawierający następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Utwórz plik o nazwie **ifcfg eth0** w `/etc/sysconfig/network-scripts/` katalog zawierający następujący tekst:
   
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
   
        # chkconfig network on
8. Zainstaluj python pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
9. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. Aby zrobić to open "/ boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądra domyślna zawiera następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. Spowoduje to wyłączenie NUMA z powodu błędów jądra zgodne Red Hat programu Oracle.
   
   Oprócz powyższego, zaleca się *Usuń* następujące parametry:
   
        rhgb quiet crashkernel=auto
   
   Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego.
   
   `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie Wirtualnej najmniej 128 MB, które mogą być problemy na mniejsze rozmiary maszyn wirtualnych.
10. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie. Najnowsza wersja to 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Warto zauważyć, że instalowanie pakietu WALinuxAgent spowoduje usunięcie NetworkManager pakiety NetworkManager gnome jeśli ich nie zostały już usunięte zgodnie z opisem w kroku 2.
12. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:
    
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

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0+
**Zmiany w Oracle Linux 7**

Przygotowywanie maszyny wirtualnej Oracle Linux 7 dla platformy Azure jest bardzo podobny do Oracle Linux 6, jednak istnieje kilka istotnych różnic, warto zauważyć:

* Zarówno jądra zgodne Red Hat, jak i programu Oracle UEK3 są obsługiwane na platformie Azure.  Zaleca się UEK3 jądra.
* Pakiet NetworkManager nie powodował konfliktu z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zaleca się, że nie zostanie usunięta.
* GRUB2 teraz jest używana jako inicjującego domyślne, więc procedury do edycji parametry jądra został zmieniony (patrz poniżej).
* XFS jest teraz domyślny system plików. Nadal można ext4 systemu plików, w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Connect** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Utwórz plik o nazwie **sieci** w `/etc/sysconfig/` katalog zawierający następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Utwórz plik o nazwie **ifcfg eth0** w `/etc/sysconfig/network-scripts/` katalog zawierający następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modyfikuj reguły udev, aby uniknąć generowania statyczne reguły dla interfejsów Ethernet. Te reguły może spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Upewnij się, że usługa sieci zostanie uruchomiona podczas rozruchu, uruchamiając następujące polecenie:
   
        # sudo chkconfig network on
7. Zainstaluj pakiet języka python pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
8. Uruchom następujące polecenie, aby wyczyścić bieżący metadanych yum i zainstaluj wszystkie aktualizacje:
   
        # sudo yum clean all
        # sudo yum -y update
9. Zmodyfikuj wiersza rozruchu jądra w konfiguracji chodników uwzględnienie jądra dodatkowe parametry dla platformy Azure. W tym celu otwórz "/ etc/domyślne/chodników" w edytorze tekstów i edytowanie `GRUB_CMDLINE_LINUX` parametrów, na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów. On również wyłącza nowe OEL 7 konwencje nazewnictwa dla kart sieciowych. Oprócz powyższego, zaleca się *Usuń* następujące parametry:
   
       rhgb quiet crashkernel=auto
   
   Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego.
   
   `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie Wirtualnej najmniej 128 MB, które mogą być problemy na mniejsze rozmiary maszyn wirtualnych.
10. Po zakończeniu edycji "/ etc/domyślne/chodników" na powyżej, uruchom następujące polecenie, aby odbudować chodników konfiguracji:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
12. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
    
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej i przygotowywania ich do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kliknij przycisk **akcji -> zamykania w dół** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania do platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Teraz możesz VHD programu Oracle Linux umożliwia tworzenie nowych maszyn wirtualnych na platformie Azure. Jeśli jest to czy jest przekazywanie pliku VHD na platformę Azure po raz pierwszy, zobacz [Utwórz Maszynę wirtualną systemu Linux na podstawie niestandardowych dysku](upload-vhd.md#option-1-upload-a-vhd).

