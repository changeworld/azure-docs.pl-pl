---
title: Odzyskiwanie maszyn wirtualnych z systemem Linux przy użyciu chroot, w którym jest używany LVM (Logical Volume Manager) - maszyny wirtualne platformy Azure
description: Odzyskiwanie maszyn wirtualnych z systemem Linux za pomocą LVM.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684128"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Rozwiązywanie problemów z maszyną wirtualną systemu Linux, gdy nie ma dostępu do konsoli szeregowej platformy Azure, a układ dysku używa LVM (Logical Volume Manager)

Ten przewodnik rozwiązywania problemów jest korzystny dla scenariuszy, w których maszyna wirtualna z systemem Linux nie uruchamia się, ssh nie jest możliwe, a podstawowy układ systemu plików jest skonfigurowany za pomocą LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Tworzenie migawki nieudanej maszyny Wirtualnej

Zrób migawkę maszyny wirtualnej, których dotyczy problem. 

Migawka zostanie następnie dołączony do maszyny wirtualnej **ratownictwa.** Postępuj zgodnie z instrukcjami [tutaj,](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) jak zrobić **migawkę**.

## <a name="create-a-rescue-vm"></a>Tworzenie maszyny wirtualnej ratownictwa
Zazwyczaj zaleca się maszynę wirtualną ratunkową tej samej lub podobnej wersji systemu operacyjnego. Użyj tego samego **regionu** i **grupy zasobów** maszyny wirtualnej, której dotyczy problem

## <a name="connect-to-the-rescue-vm"></a>Połącz się z maszyną wirtualną ratunkową
Połącz za pomocą ssh do maszyny wirtualnej **ratownictwa.** Podnieś uprawnienia i stań się superużytkownikiem za pomocą

`sudo su -`

## <a name="attach-the-disk"></a>Podłącz dysk
Dołącz dysk do maszyny wirtualnej **ratownictwa** wykonane z migawki wykonanej wcześniej.

Azure portal -> wybierz **dyski >** **klasy ratowniczej** 

![Tworzenie dysku](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Wypełnij pola. Przypisz nazwę do nowego dysku, wybierz tę samą grupę zasobów co migawka, dotyczy maszyn wirtualnych i maszyna wirtualna programu Rescue.

**Typem źródłowym** jest **migawka** .
**Migawka źródło** jest nazwą **migawki** wcześniej utworzone.

![tworzenie dysku 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Utwórz punkt instalacji dla dołączonego dysku.

`mkdir /rescue`

Uruchom polecenie **fdisk -l,** aby sprawdzić, czy dysk migawki został dołączony i wyświetl listę wszystkich dostępnych urządzeń i partycji

`fdisk -l`

Większość scenariuszy, dołączony dysk migawki będzie postrzegany jako **/dev/sdc** wyświetlające dwie partycje **/dev/sdc1** i **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Wskazuje **\*** partycję rozruchową, obie partycje mają być zainstalowane.

Uruchom polecenie **lsblk,** aby zobaczyć LVMs dotyczy maszyny Wirtualnej

`lsblk`

![Biegnij lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Sprawdź, czy są wyświetlane lvm z maszyny wirtualnej, których dotyczy problem.
Jeśli nie, użyj poniższych poleceń, aby je włączyć i ponownie uruchomić **lsblk**.
Przed kontynuowaniem upewnij się, że lvms z dołączonego dysku są widoczne.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Znajdź ścieżkę, aby zainstalować wolumin logiczny zawierający partycję / (root). Posiada pliki konfiguracyjne, takie jak /etc/default/grub

W tym przykładzie, biorąc dane wyjściowe z poprzedniego polecenia **lsblk** **rootvg-rootlv** jest poprawna **lv główny** do zainstalowania i może być używany w następnym poleceniu.

Dane wyjściowe następnego polecenia pokażą ścieżkę do zamontowania dla **głównego** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv ( Rootlv )](./media/chroot-logical-volume-manager/locate-rootlv.png)

Przejdź do montażu tego urządzenia w katalogu /rescue

`mount /dev/rootvg/rootlv /rescue`

Zamontuj partycję z **flagą Rozruchu ustawioną** na /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Sprawdź, czy systemy plików podłączonego dysku są teraz poprawnie zamontowane za pomocą polecenia **LSBLK**

![Biegnij lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

lub polecenie **df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Uzyskanie dostępu do chroot

Uzyskaj dostęp **do chroot,** który pozwoli Ci wykonać różne poprawki, istnieją niewielkie różnice dla każdej dystrybucji Linuksa.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Jeśli wystąpił błąd, taki jak:

**chroot: nie można uruchomić polecenia '/bin/bash': Brak takiego pliku lub katalogu**

próba zamontowania woluminu logicznego **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Podczas wykonywania poleceń w środowisku **chroot** należy pamiętać, że są one uruchamiane na dołączonym dysku systemu operacyjnego, a nie na lokalnej **maszynie wirtualnej ratownictwa.** 

Polecenia mogą być używane do instalowania, usuwania i aktualizowania oprogramowania. Rozwiązywanie problemów z maszynami wirtualnymi w celu naprawienia błędów.


Wykonaj polecenie lsblk i /rescue jest teraz / i ![/rescue/boot jest /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Wykonywanie poprawek

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Przykład 1 - konfigurowanie maszyny Wirtualnej do rozruchu z innego jądra

Typowym scenariuszem jest wymuszenie rozruchu maszyny Wirtualnej z poprzedniego jądra, ponieważ bieżące zainstalowane jądro mogło ulec uszkodzeniu lub uaktualnienie nie zostało poprawnie zakończone.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*walkthrough*

Polecenie **grep** zawiera listę jąder, o których **wiadomo.**
![Jądra](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv lista** wyświetla, które jądro ![zostanie załadowany przy następnym rozruchu jądra domyślne](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** służy do zmiany na ![inny zestaw grub2 jądra](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** lista wyświetla, które jądro ![zostanie załadowany przy następnym rozruchu Nowe jądro](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** przebudowuje grub.cfg przy ![użyciu wersji wymaganych Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Przykład 2 - pakiety uaktualnień

Nieudane uaktualnienie jądra może spowodować, że maszyna wirtualna nie będzie startowana.
Zamontuj wszystkie woluminy logiczne, aby umożliwić usuwanie lub ponowne instalowanie pakietów

Uruchom polecenie **lvs,** aby sprawdzić, które **telewizory** są dostępne do montażu, każda maszyna wirtualna, która została zmigrowana lub pochodzi od innego dostawcy chmury, będzie się różnić w konfiguracji.

Wyjdź ze środowiska **chroot** zamontować wymaganą **LV**

![Zaawansowane](./media/chroot-logical-volume-manager/advanced.png)

Teraz ponownie uzyskaj dostęp do środowiska **chroot,** uruchamiając

`chroot /rescue`

Wszystkie pojazdy powinny być widoczne jako zamontowane przegrody

![Zaawansowane](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Zapytanie o zainstalowane **jądro**

![Zaawansowane](./media/chroot-logical-volume-manager/rpm-kernel.png)

W razie potrzeby usunąć lub uaktualnić **jądro**
![Zaawansowane](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Przykład 3 - włącz konsolę szeregową
Jeśli dostęp do konsoli szeregowej platformy Azure nie był możliwy, sprawdź parametry konfiguracji GRUB dla maszyny Wirtualnej systemu Linux i popraw je. Szczegółowe informacje można znaleźć [w tym doc](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Przykład 4 - ładowanie jądra z problematyczną objętością wymiany LVM

Maszyna wirtualna może zakończyć się niepowodzeniem i wpada do monitu **dracut.**
Więcej szczegółów na temat awarii można znaleźć za pomocą konsoli szeregowej platformy Azure lub przejść do diagnostyki rozruchu usługi Azure portal -> -> dziennik szeregowy


Błąd podobny do tego może występować:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub.cfg jest skonfigurowany w tym przykładzie do ładowania LV o nazwie **rd.lvm.lv=VG/SwapVol** i maszyna wirtualna nie może zlokalizować tego. Ta linia pokazuje, jak jądro jest ładowane odwołując się do LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Usuń naruszające LV z konfiguracji /etc/default/grub i odbuduj grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Wyjdź z chroot i zamienić dysk systemu operacyjnego

Po naprawieniu problemu należy przystąpić do odinstalowania i odłączania dysku od maszyny wirtualnej ratunkowej, co pozwoli na zamianę go z dyskiem systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Odłącz dysk od maszyny wirtualnej programu rescue i wykonaj zamianę dysku.

Wybierz maszynę wirtualną z **dysku** portalu i wybierz **opcję Odłącz**
![dysk Odłączyć](./media/chroot-logical-volume-manager/detach-disk.png) 

Zapisz zmiany ![Zapisz odłączenie](./media/chroot-logical-volume-manager/save-detach.png) 

Dysk będzie teraz dostępny, umożliwiając zamianę go z oryginalnym dyskiem systemu operacyjnego maszyny wirtualnej, którego dotyczy problem.

Przejdź w portalu Azure do nieudanej maszyny Wirtualnej -> i wybierz dysk wymiany dysku dysku
![dysku**Zamień** **dyski**](./media/chroot-logical-volume-manager/swap-disk.png) 

Wypełnij pola **Wybierz dysk** jest dysk migawki tylko odłączony w poprzednim kroku. Wymagana jest również nazwa maszyny Wirtualnej, na które dotyczy problem, a następnie wybierz przycisk **OK**

![Nowy dysk systemu operacyjnego](./media/chroot-logical-volume-manager/new-osdisk.png) 

Jeśli maszyna wirtualna jest uruchomiona zamiana dysku zamknie go, uruchom ponownie maszynę wirtualną po zakończeniu operacji wymiany dysku.


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o

 [Konsola szeregowa platformy Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Tryb pojedynczego użytkownika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
