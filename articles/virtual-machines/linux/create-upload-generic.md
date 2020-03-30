---
title: Tworzenie i przesyłanie dysku VHD dla systemu Linux
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: f700dec6486bad9e7024d7c908a70dd0ff2b342c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066757"
---
# <a name="information-for-non-endorsed-distributions"></a>Informacje dotyczące dystrybucji nieobjętych do zatwierdzenia

Umowa SLA platformy Azure ma zastosowanie do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy używana jest jedna z [zatwierdzonych dystrybucji.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) W przypadku tych zatwierdzonych dystrybucji wstępnie skonfigurowane obrazy systemu Linux są dostarczane w portalu Azure Marketplace.

* [Linux na platformie Azure — zatwierdzone dystrybucje](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Obsługa obrazów systemu Linux na platformie Microsoft Azure](https://support.microsoft.com/kb/2941892)

Wszystkie dystrybucje uruchomione na platformie Azure mają szereg wymagań wstępnych. Ten artykuł nie może być wyczerpujący, ponieważ każda dystrybucja jest inna. Nawet jeśli spełniasz wszystkie poniższe kryteria, może być konieczne znaczne dostosowanie systemu Linux, aby działał poprawnie.

Zalecamy rozpoczęcie pracy z jednym z [systemów Linux na platformie Azure Endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Poniższe artykuły pokazują, jak przygotować różne zatwierdzone dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

* **[Dystrybucje oparte na centos](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

W tym artykule skupiono się na ogólnych wskazówkach dotyczących uruchamiania dystrybucji systemu Linux na platformie Azure.

## <a name="general-linux-installation-notes"></a>Ogólne uwagi dotyczące instalacji systemu Linux
* Format wirtualnego dysku twardego funkcji Hyper-V (VHDX) nie jest obsługiwany na platformie Azure, a tylko *stały dysk VHD.*  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet [Convert-VHD.](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) Jeśli używasz VirtualBox, wybierz **stały rozmiar,** a nie domyślny (przydzielony dynamicznie) podczas tworzenia dysku.
* Platforma Azure obsługuje maszyny wirtualne Gen1 (rozruch BIOS) & Gen2 (rozruch UEFI).
* Maksymalny rozmiar dla VHD wynosi 1023 GB.
* Podczas instalacji systemu Linux zaleca się użycie partycji standardowych, a nie Menedżera woluminów logicznych (LVM), który jest domyślny dla wielu instalacji. Przy użyciu partycji standardowych można uniknąć konfliktów nazwy LVM z sklonowanych maszyn wirtualnych, szczególnie jeśli dysk systemu operacyjnego jest kiedykolwiek dołączony do innej identycznej maszyny Wirtualnej do rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi.
* Konieczne jest wsparcie jądra do montażu systemów plików UDF. Przy pierwszym uruchomieniu na platformie Azure konfiguracja inicjowania obsługi administracyjnej jest przekazywana do maszyny Wirtualnej systemu Linux przy użyciu nośnika w formacie UDF, który jest dołączony do gościa. Agent systemu Azure Linux musi zainstalować system plików UDF, aby odczytać jego konfigurację i aprowizować maszynę wirtualną.
* Wersje jądra linuksa starsze niż 2.6.37 nie obsługują numa na Hyper-V z większymi rozmiarami maszyn wirtualnych. Ten problem dotyczy przede wszystkim starszych dystrybucji przy użyciu jądra Red Hat 2.6.32 i został naprawiony w red hat enterprise linux (RHEL) 6.6 (jądro-2.6.32-504). Systemy z niestandardowymi jądrami starszymi niż 2.6.37 lub jądrami opartymi na RHEL starszymi niż 2.6.32-504 muszą ustawić parametr `numa=off` rozruchowy w wierszu polecenia jądra w grub.conf. Aby uzyskać więcej informacji, zobacz [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Nie konfiguruj partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów, zgodnie z opisem w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją, zgodnie z opisem w poniższych krokach.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalowanie modułów jądra bez funkcji Hyper-V
Platforma Azure działa na funkcji hypervisor funkcji Hyper-V, więc system Linux wymaga pewnych modułów jądra do uruchomienia na platformie Azure. Jeśli masz maszynę wirtualną, która została utworzona poza hiper-V, instalatory systemu Linux mogą nie zawierać sterowników funkcji Hyper-V w początkowym ramdisk (initrd lub initramfs), chyba że maszyna wirtualna wykryje, że jest uruchomiona w środowisku Hyper-V. Podczas korzystania z innego systemu wirtualizacji (takich jak VirtualBox, KVM, i tak dalej) do przygotowania obrazu Linuksa, może być konieczne odbudować initrd tak, że co najmniej hv_vmbus i hv_storvsc moduły jądra są dostępne na początkowym ramdisk.  Ten znany problem dotyczy systemów opartych na dystrybucji Red Hat i ewentualnie innych.

Mechanizm przebudowy obrazu initrd lub initramfs może się różnić w zależności od rozkładu. Zapoznaj się z dokumentacją dystrybucji lub wsparciem dla właściwej procedury.  Oto jeden z przykładów przebudowy initrd za pomocą `mkinitrd` narzędzia:

1. Po utworzeniu kopii zapasowej istniejącego obrazu initrd:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Odbuduj initrd za pomocą modułów jądra hv_vmbus i hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Zmiana rozmiaru identyfikatorów VHD
Obrazy VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB.  Zazwyczaj VHD utworzone przy użyciu funkcji Hyper-V są poprawnie wyrównane.  Jeśli dysk VHD nie jest poprawnie wyrównany, podczas próby utworzenia obrazu z dysku VHD może zostać wyświetlony komunikat o błędzie podobny do następującego.

* VHD http:\//\<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd ma nieobsługiwaną wirtualną wielkość 21475270656 bajtów. Rozmiar musi być liczbą całkowita (w MB).

W takim przypadku zmienić rozmiar maszyny Wirtualnej przy użyciu konsoli Menedżera funkcji Hyper-V lub polecenia cmdlet [Programu PowerShell Resize-VHD.](https://technet.microsoft.com/library/hh848535.aspx)  Jeśli nie korzystasz w środowisku systemu Windows, zalecamy użycie `qemu-img` do konwersji (w razie potrzeby) i zmiany rozmiaru dysku VHD.

> [!NOTE]
> Istnieje znany błąd w wersjach [qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) >= 2.2.1, który powoduje nieprawidłowo sformatowany VHD. Problem został rozwiązany w QEMU 2.6. Zalecamy użycie `qemu-img` 2.2.0 lub niższej lub 2.6 lub wyższej.
> 

1. Zmiana rozmiaru dysku VHD bezpośrednio za `qemu-img` `vbox-manage` pomocą narzędzi, takich jak lub może spowodować unbootable VHD.  Zalecamy najpierw przekonwertowanie dysku VHD na obraz dysku RAW.  Jeśli obraz maszyny Wirtualnej został utworzony jako obraz dysku RAW (domyślnie dla niektórych hipernadzorców, takich jak KVM), można pominąć ten krok.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Oblicz wymagany rozmiar obrazu dysku, tak aby rozmiar wirtualny był wyrównany do 1 MB.  Poniższy skrypt powłoki `qemu-img info` bash używa do określenia wirtualnego rozmiaru obrazu dysku, a następnie oblicza rozmiar do następnego 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Zmienić rozmiar dysku `$rounded_size` nieprzetworzonego w sposób określony powyżej.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Teraz przekonwertuj dysk RAW z powrotem na dysk VHD o stałym rozmiarze.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Lub, z qemu w wersji 2.6 +, dołącz `force_size` opcję.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Wymagania dotyczące jądra Linuksa

Sterowniki usług integracji systemu Linux (LIS) dla funkcji Hyper-V i platformy Azure są współtworzone bezpośrednio do jądra systemu Linux. Wiele dystrybucji, które zawierają najnowszą wersję jądra Linuksa (na przykład 3.x) mają te sterowniki już dostępne lub w inny sposób zapewniają backported wersje tych sterowników z ich jądra.  Sterowniki te są stale aktualizowane w jądrze nadrzędnym z nowymi poprawkami i funkcjami, więc jeśli to możliwe, zalecamy uruchomienie [zatwierdzonej dystrybucji,](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) która zawiera te poprawki i aktualizacje.

Jeśli używasz wariantu Red Hat Enterprise Linux w wersji od 6.0 do 6.3, musisz zainstalować [najnowsze sterowniki LIS dla funkcji Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Począwszy od RHEL 6.4+ (i pochodnych) sterowniki LIS są już dołączone do jądra, a więc nie są potrzebne żadne dodatkowe pakiety instalacyjne.

Jeśli wymagane jest niestandardowe jądro, zalecamy najnowszą wersję jądra (na przykład 3.8+). W przypadku dystrybucji lub dostawców, którzy utrzymują własne jądro, musisz regularnie wożać sterowniki LIS z jądra nadrzędnego do niestandardowego jądra.  Nawet jeśli używasz już stosunkowo nowej wersji jądra, zalecamy śledzenie wszelkich poprawek w sterownikach LIS i ich backport w razie potrzeby. Lokalizacje plików źródłowych sterownika LIS są określone w pliku [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) w drzewie źródłowym jądra Linuksa:
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
W jądrze muszą znajdować się następujące poprawki. Ta lista nie może być ukończona dla wszystkich dystrybucji.

* [ata_piix: domyślnie odraczają dyski do sterowników funkcji Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Uwzględnianie pakietów w tranzycie w ścieżce RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: unikać używania WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Wyłącz write same dla sterowników RAID i wirtualnego hosta karty](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: poprawka wyłuskania wskaźnika NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: Awarie buforu pierścieniowego mogą spowodować zamrożenie we/wy](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochrona przed podwójną realizacją __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent systemu Azure linux
[Agent systemu Azure Linux](../extensions/agent-linux.md) `waagent` udostępnia maszynę wirtualną systemu Linux na platformie Azure. Możesz uzyskać najnowszą wersję, problemy z plikami lub przesłać żądania ściągnięcia w [repozytorium Linux Agent GitHub](https://github.com/Azure/WALinuxAgent).

* Agent Linuksa jest wydany na licencji Apache 2.0. Wiele dystrybucji już zapewniają pakiety RPM lub .deb dla agenta, a te pakiety można łatwo zainstalować i zaktualizować.
* Agent systemu Azure Linux wymaga języka Python w wersji 2.6+.
* Agent wymaga również modułu python-pyasn1. Większość dystrybucji zapewnia ten moduł jako oddzielny pakiet do zainstalowania.
* W niektórych przypadkach agent systemu Azure Linux może nie być zgodny z networkmanagerem. Wiele pakietów RPM/deb dostarczanych przez dystrybucje konfiguruje networkmanager jako konflikt z pakietem waagent. W takich przypadkach spowoduje to odinstalowanie programu NetworkManager podczas instalowania pakietu agenta systemu Linux.
* Agent systemu Azure Linux musi znajdować się na poziomie lub powyżej [minimalnej obsługiwanej wersji.](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="general-linux-system-requirements"></a>Ogólne wymagania systemowe systemu Linux

1. Zmodyfikuj linię rozruchową jądra w GRUB lub GRUB2, aby uwzględnić następujące parametry, tak aby wszystkie komunikaty konsoli były wysyłane do pierwszego portu szeregowego. Te komunikaty mogą pomóc pomocy technicznej platformy Azure w debugowaniu wszelkich problemów.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Zaleca się również *usunięcie* następujących parametrów, jeśli istnieją.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Graficzny i cichy rozruch nie jest przydatny w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie Wirtualnej o co najmniej 128 MB, co może być problematyczne dla mniejszych rozmiarów maszyn wirtualnych.

1. Zainstaluj agenta systemu Azure Linux.
  
    Agent systemu Azure Linux jest wymagany do inicjowania obsługi administracyjnej obrazu systemu Linux na platformie Azure.  Wiele dystrybucji zapewnia agenta jako pakiet RPM lub .deb (pakiet jest zwykle nazywany WALinuxAgent lub walinuxagent).  Agent można również zainstalować ręcznie, wykonując kroki opisane w [Przewodniku po agentach systemu Linux](../extensions/agent-linux.md).

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Ta konfiguracja jest zwykle domyślna.

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.
  
    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (krok 2 powyżej) zmodyfikuj następujące parametry w /etc/waagent.conf w razie potrzeby.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Na Virtualbox może pojawić się `waagent -force -deprovision` następujący `[Errno 5] Input/output error`błąd po uruchomieniu, który mówi . Ten komunikat o błędzie nie jest krytyczny i można go zignorować.

* Zamknij maszynę wirtualną i przekaż dysk VHD na platformę Azure.

