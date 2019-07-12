---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 1f9512e4eabf76edecef594b6b6498782725c019
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671599"
---
# <a name="information-for-non-endorsed-distributions"></a>Informacje dotyczące niezatwierdzonych dystrybucji
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platforma Azure stosowana jest umowa SLA do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jest to jeden z [dystrybucje zatwierdzone na](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jest używany. Dla tych zalecanych dystrybucjach wstępnie skonfigurowanych obrazów systemu Linux znajdują się w witrynie Azure Marketplace.

* [Dystrybucje zatwierdzone na systemie Linux na platformie Azure —](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Pomoc techniczna dotycząca obrazów systemu Linux w systemie Microsoft Azure](https://support.microsoft.com/kb/2941892)

Wszystkie dystrybucje działających na platformie Azure mają pewne wymagania wstępne. W tym artykule nie może być wszechstronne, ponieważ każda dystrybucja różni się. Nawet jeśli spełnione są wszystkie poniższe kryteria, konieczne może być znacznie dostosować system Linux, aby go do prawidłowego działania.

Zalecamy rozpoczęcie od jednego z [systemu Linux na dystrybucje zatwierdzone dla platformy Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Następujące artykuły pokazują, jak przygotować różnych zalecanych dystrybucjach systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje systemu centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ten artykuł koncentruje się na ogólne wskazówki dotyczące uruchamiania dystrybucji systemu Linux na platformie Azure.

## <a name="general-linux-installation-notes"></a>Uwagi dotyczące instalacji ogólne systemu Linux
* Format wirtualnego dysku twardego (VHDX) funkcji Hyper-V nie jest obsługiwana na platformie Azure, tylko *stałej wirtualnego dysku twardego*.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) polecenia cmdlet. Jeśli używasz VirtualBox wybierz **ustalony rozmiar** zamiast domyślnego (dynamicznie przydzielane) podczas tworzenia dysku.
* Platforma Azure obsługuje tylko maszyny wirtualne generacji 1. Możesz przekonwertować maszyny wirtualnej generacji 1, z dysku VHDX format pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku stałym rozmiarze. Nie można zmienić generacji maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maksymalny dozwolony rozmiar wirtualnego dysku twardego jest 1,023 GB.
* Podczas instalowania systemu Linux zalecane jest użycie z standardowe partycje, a nie logiczne woluminu Menedżera (LVM) co jest ustawieniem domyślnym dla wielu urządzeń. Przy użyciu standardowych partycje pozwoli uniknąć LVM nazwa powoduje konflikt z sklonowanym maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie jest dołączony do innej maszyny Wirtualnej identyczne do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi.
* Obsługa jądra instalowanie systemów plików funkcji zdefiniowanej przez użytkownika jest wymagane. Podczas pierwszego rozruchu na platformie Azure konfiguracji aprowizacji jest przekazywany do maszyny Wirtualnej systemu Linux przy użyciu nośnika sformatowanych przy użyciu funkcji zdefiniowanej przez użytkownika, który jest dołączony do gościa. Agent systemu Linux platformy Azure musi być zainstalowany system plików UDF odczytu jego konfigurację do aprowizowania maszyny Wirtualnej.
* Wersje jądra systemu Linux wcześniej niż 2.6.37 nie obsługuje topologii NUMA funkcji Hyper-v przy użyciu większych rozmiarów maszyn wirtualnych. Ten problem wpływa głównie na starsze dystrybucje przy użyciu nadrzędnych jądra Red Hat 2.6.32 i został naprawiony w Red Hat Enterprise Linux (RHEL) 6.6 (jądra-2.6.32 504). Komputerów z systemami starsze niż 2.6.37 niestandardowe jądra lub starszych jądra na podstawie RHEL niż 2.6.32-504 należy ustawić parametr rozruchu `numa=off` w wierszu polecenia jądra w grub.conf. Aby uzyskać więcej informacji, zobacz [Red Hat bazy wiedzy 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do utworzenia pliku wymiany na dysku tymczasowego zasobów zgodnie z opisem w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1 MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego upewnij się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed dokonaniem konwersji, zgodnie z opisem w poniższych krokach.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalowanie modułów jądra bez funkcji Hyper-V
System Azure działa na funkcji hypervisor Hyper-V, więc Linux wymaga niektórych modułów jądra uruchamiane na platformie Azure. Jeśli masz maszynę Wirtualną, która została utworzona poza funkcji Hyper-V, pliki instalacyjne systemu Linux może nie zawierać sterowników funkcji Hyper-v początkowej ramdisk (initrd lub initramfs), chyba że maszyna wirtualna wykryje, że jest uruchomiona w środowisku funkcji Hyper-V. Przygotuj obraz systemu Linux za pomocą różnych wirtualizacji systemu (na przykład Virtualbox KVM i tak dalej), może być konieczne odbudowanie initrd, więc, na co najmniej hv_vmbus i hv_storvsc modułów jądra są dostępne w początkowej dysku RAM.  Jest to znany problem w systemach, na podstawie nadrzędnego dystrybucji firmy Red Hat i ewentualnie inne.

Ponowne tworzenie obrazu initrd lub initramfs przy użyciu mechanizmu mogą się różnić w zależności od dystrybucji. Odpowiednie procedury zapoznaj się z dokumentacją danej dystrybucji lub pomocy technicznej.  Poniżej przedstawiono przykładowy odbudowywania initrd przy użyciu `mkinitrd` narzędzie:

1. Tworzenie kopii zapasowej istniejącego obrazu initrd:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Ponownie skompiluj initrd przy użyciu modułów jądra hv_vmbus i hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Zmiany rozmiaru wirtualnych dysków twardych
Obrazy VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1 MB.  Zwykle wirtualne dyski twarde utworzone za pomocą funkcji Hyper-V są prawidłowo wyrównane.  Jeśli wirtualny dysk twardy nie jest prawidłowo wyrównana, podczas próby utworzenia obrazu z wirtualnego dysku twardego może wystąpić komunikat o błędzie podobny do następującego.

* Http wirtualnego dysku twardego:\//\<mystorageaccount >.blob.core.windows.net/vhds/MyLinuxVM.vhd ma nieobsługiwany rozmiar wirtualny 21475270656 bajtów. Rozmiar musi być liczbą całkowitą (w MB).

W takim przypadku Zmień rozmiar maszyny Wirtualnej przy użyciu konsoli Menedżera funkcji Hyper-V lub [dysku VHD zmiany rozmiaru](https://technet.microsoft.com/library/hh848535.aspx) polecenia cmdlet programu PowerShell.  Jeśli w środowisku Windows nie jest uruchomiony, zalecamy użycie `qemu-img` konwersji (jeśli jest to konieczne), i zmienianie rozmiaru wirtualnego dysku twardego.

> [!NOTE]
> Brak [znaną usterką w programie qemu img](https://bugs.launchpad.net/qemu/+bug/1490611) wersji > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zalecamy użycie programu `qemu-img` 2.2.0 lub mniej, lub wersji 2.6 lub nowszej.
> 

1. Zmiana rozmiaru wirtualnego dysku twardego bezpośrednio przy użyciu narzędzi takich jak `qemu-img` lub `vbox-manage` może spowodować rozruch wirtualnego dysku twardego.  Zaleca się najpierw konwertowania wirtualnego dysku twardego do obrazu dysku RAW.  Jeśli obraz maszyny Wirtualnej został utworzony jako obraz RAW dysku (domyślnie dla niektórych funkcji hypervisor, na przykład KVM), możesz pominąć ten krok.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Oblicz wymagany rozmiar obrazu dysku, dzięki czemu rozmiar pamięci wirtualnej jest wyrównany do 1 MB.  Następujące bash używa skryptu powłoki `qemu-img info` Określanie wirtualnych rozmiaru obrazu dysku, a następnie oblicza rozmiar do następnego 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Zmiana rozmiaru pierwotne dysk przy użyciu `$rounded_size` według stawki ustalonej powyżej.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Teraz przekonwertować pierwotne dysku do dysku VHD o stałym rozmiarze.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Lub z wersją qemu 2.6 zawierają `force_size` opcji.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Wymagania dotyczące jądra systemu Linux

Sterowniki usługi LIS (Linux Integration) dla funkcji Hyper-V i platformą Azure są tworzone bezpośrednio do nadrzędnego jądra systemu Linux. Wiele dystrybucji, które zawierają najnowszej wersji jądra systemu Linux (na przykład 3.x) już te sterowniki, które są dostępne lub inny sposób dostarczyć backported wersje tych sterowników za pomocą ich jądra.  Te sterowniki są stale aktualizowane w jądrze nadrzędnego z nowych poprawek i funkcji, więc jeśli jest to możliwe, firma Microsoft zaleca uruchamianie [zatwierdzonego dla dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zawierającego te poprawki i aktualizacje.

Jeśli korzystasz z wariantu Red Hat Enterprise Linux w wersji 6.0 i 6.3, a następnie należy zainstalować [najnowszych sterowników LIS dla funkcji Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Począwszy od systemu RHEL 6.4 + (i ich pochodnych) sterowników LIS są już dołączone do jądra, a więc nie instalacji dodatkowe pakiety są wymagane.

Jeśli wymagana jest niestandardowy jądra, firma Microsoft zaleca najnowszej wersji jądra (na przykład 3.8 i nowsze). Dystrybucje lub dostawców, którzy mają swoje własne jądra należy regularnie Poprawka usterki systemu sterowników LIS z nadrzędnego jądra swoje niestandardowe jądra.  Nawet jeśli korzystasz już z stosunkowo najnowszej wersji jądra, zdecydowanie zalecamy rejestrowanie informacji o dowolnej nadrzędne naprawia sterowników LIS i poprawka usterki systemu je stosownie do potrzeb. Lokalizacje plików źródłowych sterowników LIS są określone w [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) pliku w drzewie źródła jądra systemu Linux:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Następujące poprawki muszą być uwzględnione w jądrze. Ta lista nie może zostać zakończona dla wszystkich dystrybucji.

* [ata_piix: Odrocz dysków sterowników na funkcji Hyper-V domyślnie](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Konto dla pakietów w drodze w ścieżce RESETOWANIA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: unikaj użycia WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Wyłącz zapisu w tej samej macierzy RAID i sterowniki karty hosta wirtualnego](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Poprawka wyłuskanie wskaźnika o wartości NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: pierścień błędów buforu może spowodować blokowanie operacji We/Wy](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochronę przed podwójnego wykonywania __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent systemu Linux platformy Azure
[Agenta systemu Linux platformy Azure](../extensions/agent-linux.md) `waagent` aprowizuje maszyny wirtualnej z systemem Linux na platformie Azure. Pobierz najnowszą wersję, problemów z plików lub przesyłać żądania ściągnięcia w [repozytorium GitHub agenta systemu Linux](https://github.com/Azure/WALinuxAgent).

* Agenta systemu Linux jest wydawane na mocy licencji Apache w wersji 2.0. Wiele dystrybucji już Podaj RPM lub deb pakietów dla agenta, a te pakiety można łatwo zainstalować i zaktualizować.
* Agent systemu Linux platformy Azure wymaga Python v2.6 +.
* Agent wymaga również moduł pyasn1 języka python. Większości dystrybucji zawierają ten moduł jako osobny pakiet do zainstalowania.
* W niektórych przypadkach agenta systemu Linux dla platformy Azure może nie być zgodny z NetworkManager. Wiele pakietów obr. / min/Deb dostarczone przez dystrybucje NetworkManager należy skonfigurować jako konflikt pakietu waagent. W takich przypadkach zostanie odinstalowany NetworkManager po zainstalowaniu pakietu agenta systemu Linux.
* Agent systemu Linux platformy Azure musi być co najmniej [minimalna obsługiwana wersja](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Wymagania dotyczące systemu Linux ogólne

1. Tak, aby wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, należy zmodyfikować wiersza rozruchu jądra w CHODNIKÓW lub GRUB2, aby uwzględnić następujące parametry. Te komunikaty mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Zalecamy również *usuwanie* następujące parametry, jeśli takie istnieją.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Graficzne i cichy rozruchu nie jest przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki wysyłane do portu szeregowego. `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie Wirtualnej, przez co najmniej 128 MB, co może być problematyczne dla mniejszych rozmiarów maszyn wirtualnych.

1. Zainstaluj agenta systemu Linux dla platformy Azure.
  
    Agenta systemu Linux dla platformy Azure jest wymagany do inicjowania obsługi obrazu systemu Linux na platformie Azure.  Wiele dystrybucji zapewniają agenta jako pakiet RPM lub Deb (pakiet jest zwykle nazywany WALinuxAgent lub walinuxagent).  Agenta można również zainstalować ręcznie, wykonując kroki opisane w [przewodnik agenta systemu Linux](../extensions/agent-linux.md).

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Ta konfiguracja jest zazwyczaj domyślną.

1. Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego.
  
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Dysk lokalny zasób jest *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (krok 2 powyżej), zmodyfikuj następujące parametry w /etc/waagent.conf, zgodnie z potrzebami.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Na Virtualbox może zostać wyświetlony następujący błąd, po uruchomieniu `waagent -force -deprovision` z tekstem `[Errno 5] Input/output error`. Ten komunikat o błędzie nie jest krytyczne i można je zignorować.

* Zamknij maszynę wirtualną i przekazywanie wirtualnego dysku twardego do systemu Azure.

