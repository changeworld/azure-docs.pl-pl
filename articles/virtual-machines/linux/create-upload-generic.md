---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux na platformie Azure
description: Dowiedz się utworzyć i przekazać Azure wirtualnego dysku twardego (VHD) z systemem operacyjnym Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972353"
---
# <a name="information-for-non-endorsed-distributions"></a>Informacje o nieobsługiwanych dystrybucjach
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platforma Azure stosowana jest umowa SLA do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jest to jeden z [dystrybucje zatwierdzone na](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jest używany. Te dystrybucje zalecane dla obrazów systemu Linux znajdują się w witrynie Azure Marketplace za pomocą wymaganej konfiguracji.

* [Dystrybucje zatwierdzone na systemie Linux na platformie Azure —](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Pomoc techniczna dotycząca obrazów systemu Linux w systemie Microsoft Azure](https://support.microsoft.com/kb/2941892)

Wszystkie dystrybucje działających na platformie Azure, trzeba spełnić pewne wymagania wstępne, aby mogły zostać poprawnie działać na platformie.  Ten artykuł stanowi kompleksowe w żadnym wypadku nie, ponieważ każda dystrybucja ma inny, i jest możliwe, że nawet, jeśli spełnione są wszystkie poniższe kryteria wymagane znacznie dostosowanie systemu Linux, aby upewnić się, że poprawnie działa na platformie.

To z tego powodu, które firma Microsoft zaleca rozpoczęcie od [systemu Linux na dystrybucje zatwierdzone dla platformy Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) gdy jest to możliwe. Następujący artykuł przeprowadzi Cię przez jak przygotować różnych zalecanych dystrybucjach systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje systemu centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian systemu Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

W pozostałej części tego artykułu koncentruje się na ogólne wskazówki dotyczące uruchamiania dystrybucji systemu Linux na platformie Azure.

## <a name="general-linux-installation-notes"></a>Uwagi dotyczące instalacji ogólne systemu Linux
* VHDX format jest nieobsługiwane na platformie Azure, tylko **stałej wirtualnego dysku twardego**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox oznacza to, wybierając **ustalony rozmiar** zamiast domyślnego dynamicznie przydzielany podczas tworzenia dysku.
* Platforma Azure obsługuje tylko maszyny wirtualne generacji 1. Możesz przekonwertować maszyny wirtualnej generacji 1, z dysku VHDX format pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku stałym rozmiarze. Ale nie można zmienić generacji maszynę wirtualną. Aby uzyskać więcej informacji, zobacz [należy utworzyć maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maksymalny dozwolony rozmiar wirtualnego dysku twardego jest 1,023 GB.
* Podczas instalowania systemu Linux jest *zalecane* używasz standardowe partycje, a nie LVM (często domyślnie w przypadku instalacji wielu). Pozwoli to uniknąć LVM wystąpił konflikt między nazwą sklonowany w przypadku maszyn wirtualnych, szczególnie w przypadku, gdy dysk systemu operacyjnego nigdy nie musi być dołączony do innej maszyny Wirtualnej identyczne do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używana dla dysków z danymi.
* Wymagana jest obsługa jądra instalowanie systemów plików funkcji zdefiniowanej przez użytkownika. Podczas pierwszego rozruchu na platformie Azure konfiguracji aprowizacji jest przekazywany do maszyny Wirtualnej systemu Linux przy użyciu sformatowanych przy użyciu funkcji zdefiniowanej przez użytkownika nośnika, który jest dołączony do gościa. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików UDF odczytu jego konfigurację do aprowizowania maszyny Wirtualnej.
* Wersje jądra systemu Linux poniżej 2.6.37 nie obsługują architektury NUMA funkcji Hyper-v przy użyciu większych rozmiarów maszyn wirtualnych. Ten problem wpływa głównie na starsze dystrybucje przy użyciu nadrzędnych jądra Red Hat 2.6.32 i została naprawiona w RHEL, 6.6 (jądra-2.6.32 504). Komputerów z systemami starsze niż 2.6.37 niestandardowe jądra lub starszych jądra na podstawie RHEL niż 2.6.32-504 należy ustawić parametr rozruchu `numa=off` na wiersza polecenia w grub.conf jądra. Aby uzyskać więcej informacji, zobacz firmy Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysk tymczasowy zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1MB. Podczas konwersji z pierwotnych dysku wirtualnego dysku twardego należy się upewnić, że rozmiar dysku surowego jest wielokrotnością 1MB przed dokonaniem konwersji. Więcej informacji można znaleźć w poniższych krokach.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalowanie modułów jądra bez funkcji Hyper-V
System Azure działa na funkcji hypervisor Hyper-V, więc Linux wymaga zainstalowania niektórych modułów jądra, aby można było uruchomić na platformie Azure. Jeśli masz maszynę Wirtualną, która została utworzona poza funkcji Hyper-V, pliki instalacyjne systemu Linux może nie zawierać sterowników dla funkcji Hyper-V początkowej ramdisk (initrd lub initramfs) chyba że wykryje, że jest uruchomiona w środowisku funkcji Hyper-V. Przygotuj obraz systemu Linux za pomocą różnych wirtualizacji systemu (czyli Virtualbox, KVM itp.), może być konieczne odbudowanie initrd, aby upewnić się, że co najmniej `hv_vmbus` i `hv_storvsc` modułów jądra są dostępne w początkowej dysku RAM.  To znany problem z co najmniej na systemy oparte na nadrzędne dystrybucji firmy Red Hat.

Ponowne tworzenie obrazu initrd lub initramfs przy użyciu mechanizmu mogą się różnić w zależności od dystrybucji. Odpowiednie procedury zapoznaj się z dokumentacją danej dystrybucji lub pomocy technicznej.  Poniżej przedstawiono przykładowy sposób odbudowywania przy użyciu initrd `mkinitrd` narzędzie:

Najpierw należy utworzyć kopię zapasową istniejącego obrazu initrd:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Następnie odbudować initrd z `hv_vmbus` i `hv_storvsc` modułów jądra:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Zmiany rozmiaru wirtualnych dysków twardych
Obrazy VHD na platformie Azure musi mieć rozmiar wirtualny wyrównane do 1 MB.  Zwykle wirtualne dyski twarde utworzone za pomocą funkcji Hyper-V powinien już być wyrównane poprawnie.  Jeśli wirtualny dysk twardy nie jest prawidłowo wyrównana, otrzymasz komunikat o błędzie podobny do poniższego, podczas próby utworzenia *obraz* z wirtualnego dysku twardego:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Aby rozwiązać ten problem, Zmień rozmiar maszyny Wirtualnej przy użyciu konsoli Menedżera funkcji Hyper-V lub [dysku VHD zmiany rozmiaru](http://technet.microsoft.com/library/hh848535.aspx) polecenia cmdlet programu Powershell.  Jeśli nie są wykonywane w środowisku Windows, zaleca się używanie qemu img do konwersji (w razie potrzeby) i zmienianie rozmiaru wirtualnego dysku twardego.

> [!NOTE]
> Jest to znana Usterka w wersjach qemu img > = 2.2.1 powodujący sformatowany wirtualnego dysku twardego. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się używać qemu-img 2.2.0 lub małe lub aktualizacji do wersji 2.6 lub nowszej. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Zmiana rozmiaru wirtualnego dysku twardego bezpośrednio przy użyciu narzędzi takich jak `qemu-img` lub `vbox-manage` może spowodować rozruch wirtualnego dysku twardego.  Dlatego zaleca się pierwszej konwersji wirtualnego dysku twardego do obrazu dysku RAW.  Jeśli już utworzono obraz maszyny Wirtualnej jako obraz RAW dysku (domyślnie dla niektórych funkcji hypervisor, na przykład KVM) możesz pominąć ten krok:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Oblicz wymagany rozmiar obrazu dysku, aby upewnić się, że rozmiar pamięci wirtualnej jest wyrównany do 1 MB.  Dzięki temu może być pomocny następujący skrypt powłoki bash.  Skrypt używa "`qemu-img info`" Aby określi rozmiar obrazu dysku wirtualnego, a następnie oblicza rozmiar do następnego 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Zmień rozmiar pierwotne dysku przy użyciu $rounded_size, jak w powyższym skryptu:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Teraz przekształcić pierwotne dysku do dysku VHD o stałym rozmiarze:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Lub z wersją qemu **2.6** obejmują `force_size` opcji:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Wymagania dotyczące jądra systemu Linux
Sterowniki usługi LIS (Linux Integration) dla funkcji Hyper-V i platformą Azure są tworzone bezpośrednio do nadrzędnego jądra systemu Linux. Wiele dystrybucji, które zawierają najnowszej wersji jądra systemu Linux (np. 3.x) już te sterowniki, które są dostępne lub inny sposób dostarczyć backported wersje tych sterowników za pomocą ich jądra.  Te sterowniki są stale aktualizowane w jądrze nadrzędnego z nowych poprawek i funkcji, tak, gdy jest to możliwe jest zalecane uruchamianie [zatwierdzonego dla dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zawierającego te poprawki i aktualizacje.

Jeśli używasz wariant wersje Red Hat Enterprise Linux **6.0 6.3**, a następnie należy zainstalować najnowsze sterowniki usług LIS dla funkcji Hyper-V. Można znaleźć sterowniki [w tym miejscu](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Począwszy od systemu RHEL **6.4 +** (i ich pochodnych) sterowników LIS są już dołączone do jądra, więc nie instalacji dodatkowych pakietów są potrzebne do uruchomienia tych systemów na platformie Azure.

Jeśli niestandardowe jądra jest wymagana, zaleca się używać nowsza wersja jądra (czyli **3.8 +**). Dla tych dystrybucji lub dostawców, którzy mają swoje własne jądra jest wymagany do regularnie Poprawka usterki systemu LIS wysiłku sterowniki z nadrzędnego jądra swoje niestandardowe jądra.  Nawet wtedy, gdy są już uruchomione stosunkowo najnowszej wersji jądra, wysoce zalecane jest aby śledzić nadrzędnego poprawek w sterowników LIS i poprawka usterki systemu te zgodnie z potrzebami. Lokalizacja plików źródłowych sterowników LIS jest dostępna w [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) pliku w drzewie źródła jądra systemu Linux:

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

Co najmniej braku następujące poprawki wiadomo, że spowodować problemy na platformie Azure, a więc te muszą być zawarte w jądrze. Ta lista w żadnym wypadku nie jest wyczerpująca lub zakończenie wszystkich dystrybucji:

* [ata_piix: Odrocz dysków sterowników na funkcji Hyper-V domyślnie](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: konto dla pakietów w drodze w ścieżce RESETOWANIA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: unikaj użycia WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Wyłącz zapisu w tej samej macierzy RAID i sterowniki karty hosta wirtualnego](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: wyłuskanie wskaźnika NULL poprawki](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: pierścień błędów buforu może spowodować blokowanie operacji We/Wy](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochronę przed podwójnego wykonywania __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent systemu Linux platformy Azure
[Agenta systemu Linux platformy Azure](../extensions/agent-linux.md) (waagent) jest wymagana do prawidłowego aprowizacji maszyny wirtualnej z systemem Linux na platformie Azure. Możesz pobrać najnowszą wersję, problemów z plików lub przesyłać żądania ściągnięcia w [repozytorium GitHub agenta systemu Linux](https://github.com/Azure/WALinuxAgent).

* Agenta systemu Linux jest wydawane na mocy licencji Apache w wersji 2.0. Wiele dystrybucji już Podaj RPM lub deb pakietów dla agenta, a więc w niektórych przypadkach, to można instalować i aktualizowane przy minimalnym nakładzie pracy.
* Agent systemu Linux platformy Azure wymaga Python v2.6 +.
* Agent wymaga również moduł pyasn1 języka python. Większości dystrybucji to zapewnić jako osobny pakiet, który może zostać zainstalowany.
* W niektórych przypadkach agenta systemu Linux dla platformy Azure może nie być zgodny z NetworkManager. Wiele pakietów obr. / min/Deb dostarczone przez dystrybucje skonfiguruj NetworkManager jako konflikt pakietu waagent, a zatem odinstaluje NetworkManager po zainstalowaniu pakietu agenta systemu Linux.
* Agent systemu Linux platformy Azure musi mieć powyżej minimalna obsługiwana wersja znajduje się w artykule dla [szczegóły](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Wymagania dotyczące systemu Linux ogólne

* Zmodyfikuj wiersza rozruchu jądra w CHODNIKÓW lub GRUB2, aby uwzględnić następujące parametry. Gwarantuje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Gwarantuje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego mogą ułatwić rozwiązanie Azure pomocy technicznej w debugowaniu problemów.
  
    Oprócz powyższego, zaleca się *Usuń* następujące parametry, jeśli nie istnieją:
  
        rhgb quiet crashkernel=auto
  
    Graficzne i cichy rozruchu nie jest przydatne w środowisku chmury, w której chcemy, aby wszystkie dzienniki mają być wysyłane do portu szeregowego. `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej stronie, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, może być kłopotliwe w przypadku mniejszych rozmiarów maszyn wirtualnych.

* Instalowanie agenta systemu Linux dla platformy Azure
  
    Agenta systemu Linux dla platformy Azure jest wymagany do inicjowania obsługi obrazu systemu Linux na platformie Azure.  Wiele dystrybucji zapewniają agenta jako pakiet RPM lub Deb (pakiet jest zwykle nazywany "WALinuxAgent" lub "walinuxagent").  Agenta można również zainstalować ręcznie, wykonując kroki opisane w [przewodnik agenta systemu Linux](../extensions/agent-linux.md).

* Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to opcja domyślna.

* Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego
  
    Agent systemu Linux platformy Azure może automatycznie skonfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Dysk lokalny zasób jest *tymczasowe* dysku i może opróżnić, gdy maszyna wirtualna jest anulowanie aprowizacji. Po zainstalowaniu agenta systemu Linux dla platformy Azure (zobacz poprzedni krok), zmodyfikuj następujące parametry w /etc/waagent.conf odpowiednio:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* W ostatnim kroku uruchom następujące polecenia, aby anulować aprowizację maszyny wirtualnej:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Na Virtualbox może zostać wyświetlony następujący błąd, po uruchomieniu "waagent-force - deprovision": `[Errno 5] Input/output error`. Ten komunikat o błędzie nie jest krytyczne i można je zignorować.
  > 
  > 

* Zamknij maszynę wirtualną i przekazywanie wirtualnego dysku twardego do systemu Azure.

