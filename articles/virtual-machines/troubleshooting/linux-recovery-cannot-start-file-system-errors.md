---
title: Rozwiązywanie problemów z uruchamianiem maszyny wirtualnej z systemem Linux z powodu błędów systemu plików | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842405"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Rozwiązywanie problemów z uruchamianiem maszyny wirtualnej w systemie Linux z powodu błędów systemu plików

Nie można połączyć się z maszyną wirtualną systemu Azure Linux przy użyciu bezpiecznej powłoki (SSH). Po uruchomieniu funkcji diagnostyki rozruchu w [witrynie Azure portal,](https://portal.azure.com/)zobaczysz wpisy dziennika, które przypominają następujące przykłady.

## <a name="examples"></a>Przykłady

Poniżej przedstawiono przykłady możliwych błędów.

### <a name="example-1"></a>Przykład 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Przykład 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Przykład 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Przykład 4 

Ten przykład jest spowodowany przez czysty fsck. W takim przypadku istnieją również dodatkowe dyski danych dołączone do maszyny Wirtualnej (/dev/sdc1 i /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Ten problem może wystąpić, jeśli system plików nie został zamknięty czysto lub problemy związane z magazynem. Problemy obejmują błędy sprzętowe lub programowe, problemy ze sterownikami lub programami, błędy zapisu itp. Zawsze ważne jest, aby mieć kopię zapasową krytycznych danych. Narzędzia, które opisują w tym artykule może pomóc odzyskać systemy plików, ale jest utrata danych może nadal wystąpić.

Linux ma kilka kontrolerów systemu plików dostępnych. Najczęściej spotykane dla dystrybucji na platformie Azure to: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)i [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, uruchom maszynę wirtualną w trybie awaryjnym za pomocą [konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) i użyj tego narzędzia do naprawy systemu plików. Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej lub nie działa, zobacz [sekcję Naprawij maszynę wirtualną w trybie offline](#repair-the-vm-offline) tego artykułu.

## <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

1. Połącz się z konsolą szeregową.

   > [!Note]
   > Aby uzyskać więcej informacji na temat korzystania z konsoli szeregi dla systemu Linux, zobacz:
   > * [Dostęp do trybu GRUB i trybu pojedynczego użytkownika za pomocą konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Używanie konsoli szeregowej do połączeń SysRq i NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Wybierz przycisk ikony zasilania, a następnie wybierz pozycję Uruchom ponownie maszynę wirtualną. (Jeśli konsola szeregowa nie jest włączona lub nie jest połączona pomyślnie, przycisk nie zostanie wyświetlony).

   ![OBRAZ](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Uruchom maszynę wirtualną w trybie awaryjnym.

4. Wprowadź hasło konta głównego, aby zalogować się w trybie awaryjnym.

5. Użyj xfs_repair z opcją -n, aby wykryć błędy w systemie plików. W poniższym przykładzie przyjęto założenie, że partycja systemowa to /dev/sda1. Wymień go na odpowiednią wartość dla maszyny Wirtualnej:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Uruchom następujące polecenie, aby naprawić system plików:

   ```
   xfs_repair /dev/sda1
   ```

7. Jeśli zostanie wyświetlony komunikat o błędzie "BŁĄD: System plików zawiera cenne zmiany metadanych w dzienniku, który musi zostać odtwoczony", utwórz katalog tymczasowy i zainstaluj system plików:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Jeśli nie można zainstalować dysku, uruchom polecenie xfs_repair z opcją -L (wymuszanie zerowania dziennika):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Następnie spróbuj zainstalować system plików. Jeśli dysk zostanie pomyślnie zamontowany, otrzymasz następujące dane wyjściowe:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Uruchom ponownie maszynę wirtualną, a następnie sprawdź, czy problem został rozwiązany.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. Podłącz dysk systemowy maszyny Wirtualnej jako dysk danych do odzyskiwania maszyny Wirtualnej (dowolnej działającej maszyny wirtualnej z systemem Linux). Aby to zrobić, można użyć [poleceń interfejsu wiersza polecenia](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) lub zautomatyzować konfigurowanie odzyskiwania maszyny Wirtualnej za pomocą [poleceń naprawy maszyny Wirtualnej](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Znajdź etykietę dysku podłączonego dysku systemowego. W takim przypadku zakładamy, że etykieta dołączonego dysku systemowego to /dev/sdc1. Wymień go na odpowiednią wartość dla maszyny Wirtualnej.

3. Użyj xfs_repair z opcją -n, aby wykryć błędy w systemie plików.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Uruchom następujące polecenie, aby naprawić system plików:

   ```
   xfs_repair /dev/sdc1
   ```

5. Jeśli zostanie wyświetlony komunikat o błędzie "BŁĄD: System plików zawiera cenne zmiany metadanych w dzienniku, który musi zostać odtwoczony", utwórz katalog tymczasowy i zainstaluj system plików:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Jeśli nie można zainstalować dysku, uruchom polecenie xfs_repair z opcją -L (wymuszanie zerowania dziennika):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Następnie spróbuj zainstalować system plików. Jeśli dysk zostanie pomyślnie zamontowany, otrzymasz następujące dane wyjściowe:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Odinstaluj i odłącz oryginalny wirtualny dysk twardy, a następnie utwórz maszynę wirtualną z oryginalnego dysku systemowego. Aby to zrobić, można użyć [poleceń interfejsu wiersza polecenia](troubleshoot-recovery-disks-linux.md) lub polecenia naprawy maszyny Wirtualnej, jeśli zostały [użyte](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) do utworzenia odzyskiwania maszyny Wirtualnej.

8. Sprawdź, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną systemu Linux, dołączając dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania za pomocą interfejsu wiersza polecenia 2.0 platformy Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Dołączanie dysku danych do maszyny Wirtualnej z systemem Linux za pomocą portalu](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

