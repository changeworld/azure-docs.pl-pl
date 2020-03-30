---
title: Rozwiązywanie problemów z uruchamianiem maszyny Wirtualnej systemu Linux z powodu błędów fstab | Dokumenty firmy Microsoft
description: Wyjaśnia, dlaczego linux VM nie można uruchomić i jak rozwiązać problem.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351153"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Rozwiązywanie problemów z uruchamianiem maszyny Wirtualnej systemu Linux z powodu błędów fstab

Nie można połączyć się z maszyną wirtualną systemu Azure Linux (VM) przy użyciu połączenia bezpiecznej powłoki (SSH). Po uruchomieniu funkcji [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) w [witrynie Azure portal,](https://portal.azure.com/)zobaczysz wpisy dziennika, które przypominają następujące przykłady:

## <a name="examples"></a>Przykłady

Poniżej przedstawiono przykłady możliwych błędów.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Przykład 1: Dysk jest montowany przez identyfikator SCSI zamiast uniwersalnego unikatowego identyfikatora (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Przykład 2: W centos brakuje niepodłączanego urządzenia

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Przykład 3: Maszyna wirtualna nie może się uruchomić z powodu błędnej konfiguracji fstab lub ponieważ dysk nie jest już dołączony

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Przykład 4: Wpis dziennika szeregowego pokazuje niepoprawny UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Ten problem może wystąpić, jeśli składnia tabeli systemów plików (fstab) jest niepoprawna lub jeśli wymagany dysk danych, który jest mapowany do wpisu w pliku "/etc/fstab", nie jest dołączony do maszyny Wirtualnej.

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, uruchom maszynę wirtualną w trybie awaryjnym przy użyciu konsoli szeregowej dla maszyn wirtualnych platformy Azure. Następnie użyj narzędzia, aby naprawić system plików. Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do sekcji [Napraw maszynę wirtualną w trybie offline.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

### <a name="using-single-user-mode"></a>Korzystanie z trybu pojedynczego użytkownika

1. Połącz się [z konsolą szeregową](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Korzystanie z konsoli szeregowej w [trybie pojedynczego użytkownika w trybie pojedynczego użytkownika](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. Po uruchomieniu maszyny wirtualnej w trybie pojedynczego użytkownika. Użyj ulubionego edytora tekstu, aby otworzyć plik fstab. 

   ```
   # nano /etc/fstab
   ```

4. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest zainstalowany po uruchomieniu maszyny Wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie man fstab. Aby rozwiązać problem niepowodzenia uruchamiania, przejrzyj każdy wiersz, aby upewnić się, że jest poprawny zarówno w strukturze, jak i w zawartości.

   > [!Note]
   > * Pola w każdym wierszu są oddzielone kartami lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak liczbowy (#) jako pierwszy znak są komentarze. Skomentowane wiersze mogą pozostać w pliku fstab, ale nie zostaną przetworzone. Zalecamy komentowanie wierszy fstab, których nie masz pewności, zamiast usuwać wiersze.
   > * Aby maszyna wirtualna odzyskać i uruchomić, partycje systemu plików powinny być tylko wymagane partycje. Maszyna wirtualna może wystąpić błędy aplikacji dotyczące dodatkowych partycji komentowane. Jednak maszyna wirtualna powinna zostać uruchomiony bez dodatkowych partycji. Później możesz odkomentować wszystkie skomentowane wiersze.
   > * Zaleca się instalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie man blkid.
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zaleca się użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie, aby kontynuować po wystąpieniu błędów na partycjach, które nie są wymagane do uruchomienia maszyny Wirtualnej.

5. Zmień lub skomentuj wszelkie niepoprawne lub niepotrzebne wiersze w pliku fstab, aby umożliwić poprawne uruchomienie maszyny Wirtualnej.

6. Zapisz zmiany w pliku fstab.

7. Uruchom ponownie maszynę wirtualną za pomocą polecenia poniżej.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Można również użyć polecenia "ctrl+x", które również uruchomiłoby ponownie maszynę wirtualną.


8. Jeśli wpisy komentarz lub poprawka powiodła się, system powinien osiągnąć monit bash w portalu. Sprawdź, czy można połączyć się z maszyną wirtualną.

### <a name="using-root-password"></a>Korzystanie z hasła głównego

1. Połącz się [z konsolą szeregową](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Zaloguj się do systemu przy użyciu lokalnego użytkownika i hasła.

   > [!Note]
   > Nie można użyć klucza SSH, aby zalogować się do systemu w konsoli szeregowej.

3. Poszukaj błędu, który wskazuje, że dysk nie został zainstalowany. W poniższym przykładzie system próbował dołączyć dysk, który nie był już obecny:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Połącz się z maszyną wirtualną przy użyciu hasła głównego (maszyny wirtualne oparte na czerwonym kapeluszu).

5. Użyj ulubionego edytora tekstu, aby otworzyć plik fstab. Po zamontowaniu dysku uruchom następujące polecenie dla nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest zainstalowany po uruchomieniu maszyny Wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie man fstab. Aby rozwiązać problem niepowodzenia uruchamiania, przejrzyj każdy wiersz, aby upewnić się, że jest poprawny zarówno w strukturze, jak i w zawartości.

   > [!Note]
   > * Pola w każdym wierszu są oddzielone kartami lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak liczbowy (#) jako pierwszy znak są komentarze. Skomentowane wiersze mogą pozostać w pliku fstab, ale nie zostaną przetworzone. Zalecamy komentowanie wierszy fstab, których nie masz pewności, zamiast usuwać wiersze.
   > * Aby maszyna wirtualna odzyskać i uruchomić, partycje systemu plików powinny być tylko wymagane partycje. Maszyna wirtualna może wystąpić błędy aplikacji dotyczące dodatkowych partycji komentowane. Jednak maszyna wirtualna powinna zostać uruchomiony bez dodatkowych partycji. Później możesz odkomentować wszystkie skomentowane wiersze.
   > * Zaleca się instalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie man blkid.
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zaleca się użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie, aby kontynuować po wystąpieniu błędów na partycjach, które nie są wymagane do uruchomienia maszyny Wirtualnej.

7. Zmień lub skomentuj wszelkie niepoprawne lub niepotrzebne wiersze w pliku fstab, aby umożliwić poprawne uruchomienie maszyny Wirtualnej.

8. Zapisz zmiany w pliku fstab.

9. Uruchom ponownie maszynę wirtualną.

10. Jeśli wpisy komentarz lub poprawka powiodła się, system powinien osiągnąć monit bash w portalu. Sprawdź, czy można połączyć się z maszyną wirtualną.

11. Sprawdź punkty instalacji podczas testowania wszelkich zmian fstab, uruchamiając polecenie mount -a. Jeśli nie ma błędów, punkty instalacji powinny być dobre.

## <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. Podłącz dysk systemowy maszyny Wirtualnej jako dysk danych do odzyskiwania maszyny Wirtualnej (dowolnej działającej maszyny wirtualnej z systemem Linux). Aby to zrobić, można użyć [poleceń interfejsu wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) lub zautomatyzować konfigurowanie odzyskiwania maszyny Wirtualnej za pomocą [poleceń naprawy maszyny Wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Po zamontowaniu dysku systemowego jako dysku danych na maszynie wirtualnej odzyskiwania, wykonaj z powrotem plik fstab przed wprowadzeniem zmian, a następnie wykonaj następne kroki, aby poprawić plik fstab.

3.    Poszukaj błędu, który wskazuje, że dysk nie został zainstalowany. W poniższym przykładzie system próbował dołączyć dysk, który nie był już obecny:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Połącz się z maszyną wirtualną przy użyciu hasła głównego (maszyny wirtualne oparte na czerwonym kapeluszu).

5. Użyj ulubionego edytora tekstu, aby otworzyć plik fstab. Po zamontowaniu dysku uruchom następujące polecenie dla nano. Upewnij się, że pracujesz nad plikiem fstab, który znajduje się na dysku zainstalowanym, a nie na pliku fstab, który znajduje się na maszynie wirtualnej ratownictwa.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Przejrzyj wymienione systemy plików. Każdy wiersz w pliku fstab wskazuje system plików, który jest zainstalowany po uruchomieniu maszyny Wirtualnej. Aby uzyskać więcej informacji na temat składni pliku fstab, uruchom polecenie man fstab. Aby rozwiązać problem niepowodzenia uruchamiania, przejrzyj każdy wiersz, aby upewnić się, że jest poprawny zarówno w strukturze, jak i w zawartości.

   > [!Note]
   > * Pola w każdym wierszu są oddzielone kartami lub spacjami. Puste wiersze są ignorowane. Wiersze, które mają znak liczbowy (#) jako pierwszy znak są komentarze. Skomentowane wiersze mogą pozostać w pliku fstab, ale nie zostaną przetworzone. Zalecamy komentowanie wierszy fstab, których nie masz pewności, zamiast usuwać wiersze.
   > * Aby maszyna wirtualna odzyskać i uruchomić, partycje systemu plików powinny być tylko wymagane partycje. Maszyna wirtualna może wystąpić błędy aplikacji dotyczące dodatkowych partycji komentowane. Jednak maszyna wirtualna powinna zostać uruchomiony bez dodatkowych partycji. Później możesz odkomentować wszystkie skomentowane wiersze.
   > * Zaleca się instalowanie dysków danych na maszynach wirtualnych platformy Azure przy użyciu identyfikatora UUID partycji systemu plików. Na przykład uruchom następujące polecenie:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Aby określić identyfikator UUID systemu plików, uruchom polecenie blkid. Aby uzyskać więcej informacji na temat składni, uruchom polecenie man blkid. Należy zauważyć, że dysk, który chcesz odzyskać jest teraz zainstalowany na nowej maszynie wirtualnej. Chociaż identyfikatory UUI powinny być spójne, identyfikatory partycji urządzenia (na przykład "/dev/sda1") różnią się na tej maszynie wirtualnej. Partycje systemu plików oryginalnej nieudanej maszyny Wirtualnej, które znajdują się na niesystemowej dysku VHD, nie są dostępne dla odzyskiwania maszyny Wirtualnej [przy użyciu poleceń interfejsu wiersza polecenia.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)
   > * Opcja nofail pomaga upewnić się, że maszyna wirtualna jest uruchamiana, nawet jeśli system plików jest uszkodzony lub system plików nie istnieje podczas uruchamiania. Zaleca się użycie opcji nofail w pliku fstab, aby umożliwić uruchamianie, aby kontynuować po wystąpieniu błędów na partycjach, które nie są wymagane do uruchomienia maszyny Wirtualnej.

7. Zmień lub skomentuj wszelkie niepoprawne lub niepotrzebne wiersze w pliku fstab, aby umożliwić poprawne uruchomienie maszyny Wirtualnej.

8. Zapisz zmiany w pliku fstab.

9. Uruchom ponownie maszynę wirtualną lub odbuduj oryginalną maszynę wirtualną.

10. Jeśli wpisy komentarz lub poprawka powiodła się, system powinien osiągnąć monit bash w portalu. Sprawdź, czy można połączyć się z maszyną wirtualną.

11. Sprawdź punkty instalacji podczas testowania wszelkich zmian fstab, uruchamiając polecenie mount -a. Jeśli nie ma błędów, punkty instalacji powinny być dobre.

12. Odinstaluj i odłącz oryginalny wirtualny dysk twardy, a następnie utwórz maszynę wirtualną z oryginalnego dysku systemowego. Aby to zrobić, można użyć [poleceń interfejsu wiersza polecenia](troubleshoot-recovery-disks-linux.md) lub polecenia naprawy maszyny Wirtualnej, jeśli zostały [użyte](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) do utworzenia odzyskiwania maszyny Wirtualnej.

13. Po ponownym utworzeniu maszyny Wirtualnej i można połączyć się z nią za pośrednictwem SSH, należy podjąć następujące akcje:
    * Przejrzyj dowolną z linii fstab, które zostały zmienione lub skomentowane podczas odzyskiwania.
    * Upewnij się, że używasz UUID i nofail opcji odpowiednio.
    * Przetestuj wszelkie zmiany fstab przed ponownym uruchomieniem maszyny Wirtualnej. Aby to zrobić, należy użyć następującego polecenia:``$ sudo mount -a``
    * Utwórz dodatkową kopię poprawionego pliku fstab do użycia w przyszłych scenariuszach odzyskiwania.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną systemu Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania za pomocą interfejsu wiersza polecenia 2.0 platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Rozwiązywanie problemów z maszyną wirtualną z systemem Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

