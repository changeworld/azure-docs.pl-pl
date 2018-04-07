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
ms.openlocfilehash: 5d08c752af23845a31428b16ee3de594044b4129
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="information-for-non-endorsed-distributions"></a>Informacje o nieobsługiwanych dystrybucjach
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platforma Azure umowy SLA stosuje się do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jeden z [dystrybucje zatwierdzone](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jest używany. Dla tych potwierdzony dystrybucji obrazów Linux znajdują się w portalu Azure Marketplace z odpowiednią konfiguracją.

* [Dystrybucje zatwierdzone na systemie Linux na platformie Azure —](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Obsługa obrazów systemu Linux na platformie Microsoft Azure](https://support.microsoft.com/kb/2941892)

Wszystkie dystrybucje działających na platformie Azure należy spełnić kilka wymagań wstępnych, aby mieć możliwość działać poprawnie na platformie.  Ten artykuł zawiera kompleksowe w żadnym wypadku nie zgodnie z każdym dystrybucji jest inny, i jest dość możliwe, że nawet jeśli zostały spełnione poniższe kryteria nadal konieczne będzie znacznie dostosowanie systemu Linux, aby upewnić się, że poprawnie działa na platformie.

To dlatego zaleca się uruchamiania jednego z naszych [systemu Linux na dystrybucje zatwierdzone Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Jeśli to możliwe. Następujące artykuły przeprowadzi Cię przez proces przygotowania różnych potwierdzony dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* **[Na podstawie centOS dystrybucji](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian systemu Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Pozostała część ten artykuł koncentruje się na ogólne wskazówki dotyczące uruchamiania dystrybucji systemu Linux na platformie Azure.

## <a name="general-linux-installation-notes"></a>Informacje o instalacji ogólne systemu Linux
* VHDX format jest nieobsługiwane w systemie Azure, tylko **stały VHD**.  Dysk można przekonwertować na format wirtualnego dysku twardego za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox oznacza to, wybierając **stały rozmiar** zamiast domyślnego dynamicznie przydzielane podczas tworzenia dysku.
* Azure obsługuje tylko maszyny wirtualne generacji 1. Maszyny wirtualne generacji 1 można przekonwertować z VHDX do formatu pliku wirtualnego dysku twardego i dynamicznie powiększające się na dysku stałym rozmiarze. Ale nie można zmienić generację maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [należy tworzyć maszyny wirtualne generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maksymalny rozmiar dozwolony dla wirtualnego dysku twardego jest 1,023 GB.
* Podczas instalowania systemu Linux jest *zalecane* używasz standardowe partycje, a nie LVM (często domyślnie dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM sklonowany maszyn wirtualnych, szczególnie, gdy dysk systemu operacyjnego kiedykolwiek musi być dołączona do innego identyczne maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) może być używany dla dysków z danymi.
* Wymagana jest obsługa jądra służący do instalowania systemów plików funkcji zdefiniowanej przez użytkownika. Przy pierwszym uruchomieniu komputera na platformie Azure konfiguracji inicjowania obsługi administracyjnej jest przekazywany do maszyny Wirtualnej systemu Linux za pomocą nośnika sformatowany UDF, dołączonego do gościa. Agent systemu Linux platformy Azure musi mieć możliwość zainstalowania systemu plików funkcji zdefiniowanej przez użytkownika do odczytu konfiguracji i udostępnić Maszynie wirtualnej.
* Wersje jądra systemu Linux poniżej 2.6.37 nie obsługują NUMA w funkcji Hyper-V o dużym rozmiarze maszyny Wirtualnej. Ten problem wpływa głównie na starszą dystrybucji przy użyciu nadrzędnego Red Hat 2.6.32 jądra i ustalono w RHEL 6.6 (jądra-2.6.32 504). Komputerów z systemami niestandardowych jądra starsze niż 2.6.37 lub systemem RHEL jądra starsze niż 2.6.32-504 należy ustawić parametr rozruchowego `numa=off` na wiersza polecenia w grub.conf jądra. Aby uzyskać więcej informacji, zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować wymiany partycji na dysku systemu operacyjnego. Aby utworzyć plik wymiany na dysku zasobów można skonfigurować agenta systemu Linux.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB. Podczas konwersji z pierwotnych dysku VHD musi upewnij się, że rozmiar dysku pierwotnych jest wielokrotnością liczby 1MB przed konwersją. Więcej informacji można znaleźć w poniższych krokach.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalowanie modułów jądra bez funkcji Hyper-V
Azure działa na funkcji hypervisor Hyper-V, więc Linux wymaga zainstalowania niektórych modułów jądra, aby można było uruchomić na platformie Azure. Jeśli masz maszynę Wirtualną, która została utworzona poza funkcji Hyper-V, instalatorzy systemu Linux nie może zawierać sterowniki dla funkcji Hyper-V w początkowej ramdisk (initrd lub initramfs), chyba że wykryje, że jest on uruchomiony w środowisku funkcji Hyper-V. Przygotowywanie obrazu systemu Linux za pomocą różnych wirtualizacji systemu (tj. Virtualbox, KVM itp.), może być konieczne odbudowanie initrd, aby upewnić się, że co najmniej `hv_vmbus` i `hv_storvsc` modułów jądra są dostępne w początkowej ramdisk.  To znany problem z co najmniej na systemy oparte na nadrzędnego dystrybucji Red Hat.

Mechanizm odbudowywania obrazu initrd lub initramfs może się różnić w zależności od dystrybucji. Dokumentacja programu dystrybucji lub obsługę prawidłowe procedury.  Oto przykład sposobu odbudować przy użyciu initrd `mkinitrd` narzędzie:

Najpierw należy utworzyć kopię zapasową istniejącego obrazu initrd:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Następnie Odbuduj initrd z `hv_vmbus` i `hv_storvsc` modułów jądra:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Zmiana rozmiaru wirtualnych dysków twardych
Obrazy VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1MB.  Zwykle wirtualne dyski twarde utworzone za pomocą funkcji Hyper-V powinien już być wyrównane poprawnie.  Jeśli wirtualny dysk twardy nie jest wyrównany prawidłowo, może pojawić się komunikat o błędzie podobny do następującego podczas próby utworzenia *obrazu* z z wirtualnego dysku twardego:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Aby rozwiązać ten problem można zmienić rozmiar maszyny Wirtualnej przy użyciu konsoli Menedżera funkcji Hyper-V lub [wirtualnego dysku twardego zmiany rozmiaru](http://technet.microsoft.com/library/hh848535.aspx) polecenia cmdlet programu Powershell.  Jeśli nie zostały uruchomione w środowisku Windows następnie zaleca się qemu img umożliwia konwertowanie (w razie potrzeby), a następnie zmień rozmiar wirtualnego dysku twardego.

> [!NOTE]
> Jest znaną usterką w wersjach qemu img > = 2.2.1, których wynikiem jest nieprawidłowo sformatowany dysk VHD. Problem został rozwiązany w wersji 2.6 QEMU. Zaleca się qemu-img 2.2.0 lub starszego, lub po zaktualizowaniu 2.6 lub nowszy. Odwołanie: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Zmiana rozmiaru wirtualnego dysku twardego bezpośrednio za pomocą narzędzi takich jak `qemu-img` lub `vbox-manage` może spowodować rozruch wirtualnego dysku twardego.  Tak zaleca się pierwszy Konwertuj dysku VHD na obraz dysku RAW.  Obraz maszyny Wirtualnej został już utworzony jako obraz dysku surowego (domyślnie dla niektórych funkcji hypervisor, takich jak KVM) może pominąć ten krok:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Oblicz wymagany rozmiar obrazu dysku, aby upewnić się, że rozmiar wirtualnego jest wyrównany do 1MB.  Następujący skrypt powłoki bash może pomóc to.  Skrypt używa "`qemu-img info`" do określenia rozmiaru obrazu dysku wirtualnego, a następnie oblicza rozmiar, aby dalej 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Zmiana rozmiaru dysku raw, przy użyciu $rounded_size zgodnie z powyższym skryptu:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Teraz zamiany dysk NIESFORMATOWANY dysk VHD o stałym rozmiarze:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Lub z wersją qemu **2.6 +** obejmują `force_size` opcji:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Wymagania dotyczące jądra systemu Linux
Sterowniki usługi integracji systemu Linux (LIS) dla funkcji Hyper-V i platformy Azure są tworzone bezpośrednio do nadrzędnego jądra systemu Linux. Wiele dystrybucji, które obejmują najnowszej wersji jądra systemu Linux (tj. 3.x) zostały już te sterowniki, które są dostępne lub w inny sposób dostarczyć backported wersje tych sterowników z ich jądra.  Te sterowniki są stale aktualizowane w nadrzędnym jądra nowe poprawki i funkcje, więc jeśli to możliwe zaleca się uruchomienie [zatwierdzone dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zawierające te poprawki i aktualizacje.

Jeśli używasz wariant wersji Red Hat Enterprise Linux **6.0 6.3**, a następnie należy zainstalować najnowsze sterowniki usług LIS dla funkcji Hyper-V. Sterowniki można znaleźć [w tej lokalizacji](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Począwszy od RHEL **6.4 +** (i ich pochodnych) sterowniki LIS są już dołączone do jądra i dlatego żadnych pakietów instalacji dodatkowych nie są wymagane do uruchamiania tych systemów na platformie Azure.

Jeśli wymagana jest niestandardowe jądra, zalecane jest Użyj nowszej wersji jądra (tj. **3.8 +**). Dla tych dystrybucji lub dostawców, którzy mają własne jądra wysiłku będą musieli regularnie Poprawka usterki systemu sterowniki LIS z nadrzędnego jądra Twoje niestandardowe jądra.  Nawet jeśli już używasz stosunkowo najnowszej wersji jądra, jest zalecane do śledzenia dowolnego nadrzędnego poprawki LIS sterowniki i poprawka usterki systemu tych zgodnie z potrzebami. Jest dostępna w lokalizacji plików źródłowych sterownik LIS [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) pliku w drzewie źródła jądra systemu Linux:

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

W bardzo minimalny braku następujące poprawki być znane spowodować problemy w systemie Azure i dlatego te muszą być zawarte w jądra. Ta lista w żadnym wypadku nie jest kompletną lub zakończenie wszystkich dystrybucji:

* [ata_piix: odroczenie dysków sterowników funkcji Hyper-V domyślnie](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: konto dla pakietów w drodze w ścieżce RESETOWANIA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: unikaj użycia WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Wyłącz zapisu w tej samej macierzy RAID i sterowniki karty hosta wirtualnego](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: poprawka wyłuskania wskaźnika o wartości NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: błędy bufora pierścień może spowodować blokowanie operacji We/Wy](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochronę przed podwójnego wykonywania __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure agenta systemu Linux
[Agenta systemu Linux Azure](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (agenta waagent) wymagany prawidłowo obsługi maszyny wirtualnej systemu Linux na platformie Azure. Można pobrać najnowszą wersję, problemów plików lub przesyłania żądań ściągnięcia w [repozytorium GitHub agenta systemu Linux](https://github.com/Azure/WALinuxAgent).

* Agent systemu Linux jest publikowany w ramach licencji Apache 2.0. Wiele dystrybucji już Podaj obr. / min lub deb pakietów dla agenta, a więc w niektórych przypadkach to można instalować i aktualizowane przy minimalnym nakładzie pracy.
* Agent systemu Linux Azure wymaga Python v2.6 +.
* Agent wymaga również moduł pyasn1 języka python. Większości dystrybucji zapewniają jako osobny pakiet, który może zostać zainstalowany.
* W niektórych przypadkach agenta systemu Linux platformy Azure może nie być zgodna z NetworkManager. Wiele pakietów RPM/Deb dostarczonych przez dystrybucje skonfigurować NetworkManager jako konflikt pakietu agenta waagent i w związku z tym odinstaluje NetworkManager po zainstalowaniu pakietu agenta systemu Linux.
* Agent systemu Linux platformy Azure musi być powyżej minimalna obsługiwana wersja, zobacz ten artykuł, aby [szczegóły](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Wymagania ogólne systemu Linux

* Zmodyfikuj wiersza rozruchu jądra w CHODNIKÓW lub GRUB2, aby uwzględnić następujące parametry. Dzięki konsoli komunikaty są wysyłane do pierwszego portu szeregowego może pomóc Azure pomocy dotyczącej debugowanie problemów:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Oprócz powyższego, zaleca się *Usuń* następujących parametrów, jeśli istnieją:
  
        rhgb quiet crashkernel=auto
  
    Graficznego i cichy rozruchu nie są przydatne w środowisku chmury, gdy chcemy, aby wszystkie dzienniki, które mają być wysyłane do portu szeregowego. `crashkernel` Opcja może być skonfigurowany w razie potrzeby po lewej, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie Wirtualnej najmniej 128 MB, które mogą być problemy na mniejsze rozmiary maszyn wirtualnych.

* Instalowanie agenta programu Azure systemu Linux
  
    Agenta systemu Linux platformy Azure jest wymagany do obsługi obraz systemu Linux na platformie Azure.  Wiele dystrybucji zapewniają agenta jako pakiet RPM lub Deb (pakiet jest zwykle nazywane "WALinuxAgent" lub "walinuxagent").  Agenta można także zainstalować ręcznie, wykonując kroki opisane w [przewodnik agenta systemu Linux](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.

* Nie należy tworzyć zamiany miejsca na dysku systemu operacyjnego
  
    Agent systemu Linux platformy Azure mogą automatycznie konfigurować obszar wymiany przy użyciu dysku zasób lokalny, który jest dołączony do maszyny Wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk lokalny zasób *tymczasowego* na dysku i może opróżnić, gdy maszyna wirtualna jest anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) i zmodyfikuj odpowiednio w /etc/waagent.conf następujące parametry:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* Jako ostatni krok Uruchom następujące polecenia, aby anulowanie zastrzeżenia maszyny wirtualnej:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Na Virtualbox zostanie wyświetlony następujący błąd po uruchomieniu "agenta waagent-force - deprovision": `[Errno 5] Input/output error`. Ten komunikat o błędzie nie ma znaczenia krytycznego i można zignorować.
  > 
  > 

* Następnie należy wyłączyć maszynę wirtualną i przekazywanie wirtualnego dysku twardego na platformie Azure.

