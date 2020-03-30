---
title: Błąd rozruchu – "to nie jest dysk rozruchowy"
description: Ten artykuł zawiera kroki rozwiązywania problemów, w których dysk nie jest uruchamiany na maszynie wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300981"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Błąd rozruchu — nie jest to dysk rozruchowy

Ten artykuł zawiera kroki, aby rozwiązać problemy, w przypadku gdy dysk nie jest uruchamiany na maszynie wirtualnej platformy Azure (VM).

## <a name="symptoms"></a>Objawy

Podczas korzystania z [diagnostyki rozruchu,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) aby wyświetlić zrzut ekranu maszyny Wirtualnej, zobaczysz, że zrzut ekranu wyświetla monit z komunikatem "To nie jest dysk rozruchowy. Włóż dyskietkę startową i naciśnij dowolny klawisz, aby spróbować ponownie...'.

   Rysunek 1.

   ![Rysunek 1 pokazuje komunikat *"To nie jest dysk rozruchowy. Włóż dyskietkę startową i naciśnij dowolny klawisz, aby spróbować ponownie..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Przyczyna

Ten komunikat o błędzie oznacza, że proces rozruchu systemu operacyjnego nie może zlokalizować aktywnej partycji systemowej. Ten błąd może również oznaczać brakujące odwołanie w magazynie danych konfiguracji rozruchu (BCD), uniemożliwiając mu lokalizowanie partycji systemu Windows.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Omówienie procesu

1. Tworzenie i uzyskiwanie dostępu do maszyny wirtualnej naprawy.
2. Ustaw stan partycji na Aktywny.
3. Napraw partycję dysku.
4. **Zalecane:** Przed przebudową maszyny Wirtualnej należy włączyć kolekcję konsoli szeregowej i zrzutu pamięci.
5. Odbuduj oryginalną maszynę wirtualną.

   > [!NOTE]
   > W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Rozwiązywanie tego problemu będzie rozwiązywać problemy w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Użyj kroków 1-3 [poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z usługi Podłączanie pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

### <a name="set-partition-status-to-active"></a>Ustawianie stanu partycji na aktywny

Maszyny wirtualne generacji 1 powinny najpierw sprawdzić, czy partycja systemu operacyjnego, w której znajduje się magazyn BCD, jest oznaczona jako *aktywna.* Jeśli masz maszynę wirtualną generacji 2, przejdź do fix [partycji dysku,](#fix-the-disk-partition)jak flaga *stan* został przestarzały w nowszej generacji.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień *(cmd.exe).*
2. Wprowadź *diskpart,* aby uruchomić narzędzie DISKPART.
3. Wprowadź *dysk listy,* aby wyświetlić listę dysków w systemie i zidentyfikować dołączony dysk VHD systemu operacyjnego.
4. Po zlokalizowaniu dołączonego dysku OS VHD wprowadź *dysk sel #,* aby wybrać dysk.  Zobacz Rysunek 2, gdzie dysk 1 jest podłączonym OS VHD.

   Rysunek 2

   ![Rysunek 2 przedstawia okno *DISKPART*pokazujące wyjście polecenia dysku listy, dysk 0 i dysk 1 wyświetlane w tabeli.  Pokazuje również wyjście polecenia sel disk 1, dysk 1 jest wybranym dyskiem](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Po wybraniu dysku wprowadź *partycję listy,* aby wyświetlić listę partycji wybranego dysku.
6. Po zidentyfikowaniu partycji rozruchowej wprowadź *partycję sel #,* aby wybrać partycję.  Zazwyczaj rozmiar partycji rozruchowej wynosi około 350 MB.  Zobacz Rysunek 3, gdzie partycja 1 jest partycją rozruchową.

   Rysunek 3

   ![Rysunek 3 przedstawia okno *DISKPART* z wyjściem polecenia *list partycji*. Partycja 1 i partycja 2 są wyświetlane w tabeli. Pokazuje również dane wyjściowe polecenia *sel partition 1*, gdy partycja 1 jest wybranym dyskiem.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Wprowadź "partycję szczegółów", aby sprawdzić stan partycji. Patrz Rysunek 4, gdzie partycja jest *aktywna: Nie*lub Rysunek 5, gdzie partycja jest "Aktywna: Tak".

   Rysunek 4

   ![Rysunek 4 przedstawia okno *DISKPART* z wyjściem polecenia *partycja szczegółów*, gdy partycja 1 jest ustawiona na *Aktywny: Nie*](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Rysunek 5

   ![Rysunek 5 przedstawia okno *DISKPART* z wyjściem polecenia *partycja szczegółów*, gdy partycja 1 jest ustawiona na *Active: Tak*.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Jeśli partycja nie jest **aktywna,** wprowadź *aktywne,* aby zmienić flagę *Active.*
9. Sprawdź, czy zmiana stanu została wykonana poprawnie, wpisując *partycję szczegółów*.

   Rysunek 6

   ![Rysunek 6 przedstawia okno diskpart z wyjściem *partycja szczegółów*, gdy partycja 1 jest ustawiona na *Aktywny: Tak*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Wprowadź *wyjście,* aby zamknąć narzędzie DISKPART i zapisać zmiany konfiguracji.

### <a name="fix-the-disk-partition"></a>Naprawianie partycji dysku

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień (cmd.exe).
2. Użyj następującego polecenia, aby uruchomić *program CHKDSK* na dysku(-ach) i naprawić błędy:

   `chkdsk <DRIVE LETTER>: /f`

   Dodanie opcji polecenia '/f spowoduje naprawienie błędów na dysku. Pamiętaj, aby <DRIVE LETTER> zastąpić ją literą dołączonego OS VHD.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: Przed przebudową maszyny wirtualnej należy włączyć zbieranie konsoli szeregowej i zrzutu pamięci

Aby włączyć zbieranie zrzutów pamięci i konsolę szeregową, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   Włącz konsolę szeregową

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Sprawdź, czy wolne miejsce na dysku systemu operacyjnego jest tak samo jak rozmiar pamięci (RAM) na maszynie wirtualnej.

   Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać się do dowolnego dysku danych dołączonego do maszyny Wirtualnej, który ma wystarczającą ilość wolnego miejsca. Aby zmienić lokalizację, zastąp "%SystemRoot%" literą dysku (na przykład "F:") dysku danych w poniższych poleceniach.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Sugerowana konfiguracja umożliwiająca wysyp systemu operacyjnego

**Obciążenie uszkodzonego dysku systemu operacyjnego:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Włącz na ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Włącz na ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Rozładuj uszkodzony dysk systemu operacyjnego:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Odbuduj oryginalną maszynę wirtualną

Użyj [kroku 5 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby ponownie zmontować maszynę wirtualną.
