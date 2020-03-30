---
title: Maszyna wirtualna systemu Windows nie może uruchomić się z powodu menedżera rozruchu systemu Windows
description: Ten artykuł zawiera kroki, aby rozwiązać problemy, w których Menedżer rozruchu systemu Windows uniemożliwia uruchamianie maszyny wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373351"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Nie można uruchomić maszyny Wirtualnej systemu Windows z powodu Menedżera rozruchu systemu Windows

Ten artykuł zawiera kroki, aby rozwiązać problemy, w których Menedżer rozruchu systemu Windows uniemożliwia uruchamianie maszyny wirtualnej platformy Azure (VM).

## <a name="symptom"></a>Objaw

Maszyna wirtualna jest zablokowany czeka na monit użytkownika i nie uruchamia się, chyba że ręcznie poinstruowany.

Podczas korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) do wyświetlania zrzutu ekranu maszyny Wirtualnej, zobaczysz, że zrzut ekranu wyświetla Menedżera rozruchu systemu Windows z *komunikatem Wybierz system operacyjny do uruchomienia lub naciśnij klawisz TAB, aby wybrać narzędzie:*.

Rysunek 1.
 
![Menedżer rozruchu systemu Windows z napisem "Wybierz system operacyjny do uruchomienia lub naciśnij klawisz TAB, aby wybrać narzędzie:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Przyczyna

Ten błąd jest spowodowany flagą BCD *displaybootmenu w Menedżerze rozruchu* systemu Windows. Gdy flaga jest włączona, Menedżer rozruchu systemu Windows monituje użytkownika, podczas procesu uruchamiania, aby wybrać program ładujący, który chce uruchomić, powodując opóźnienie rozruchu. Na platformie Azure ta funkcja może dodać do czasu potrzebny do uruchomienia maszyny Wirtualnej.

## <a name="solution"></a>Rozwiązanie

Omówienie procesu:

1. Skonfiguruj dla szybszego czasu rozruchu przy użyciu konsoli szeregowej.
2. Tworzenie i uzyskiwanie dostępu do maszyny wirtualnej naprawy.
3. Konfigurowanie dla szybszego czasu rozruchu na maszynie wirtualnej naprawy.
4. **Zalecane:** Przed przebudową maszyny Wirtualnej należy włączyć kolekcję konsoli szeregowej i zrzutu pamięci.
5. Odbuduj maszynę wirtualną.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Konfigurowanie szybszego czasu rozruchu przy użyciu konsoli szeregowej

Jeśli masz dostęp do konsoli szeregowej, istnieją dwa sposoby osiągnięcia szybszego czasu rozruchu. Zmniejsz czas oczekiwania *na wyświetlenie* lub całkowicie usuń flagę.

1. Postępuj zgodnie ze wskazówkami, aby uzyskać dostęp do [usługi Azure Serial Console dla systemu Windows,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) aby uzyskać dostęp do konsoli tekstowej.

   > [!NOTE]
   > Jeśli nie możesz uzyskać dostępu do konsoli szeregowej, przejdź dalej, [aby utworzyć i uzyskać dostęp do maszyny wirtualnej naprawy](#create-and-access-a-repair-vm).

2. **Opcja A:** Skrócenie czasu oczekiwania

   a. Czas oczekiwania jest domyślnie ustawiony na 30 sekund, ale można go zmienić na krótszy czas (np. 5 sekund).

   b. Aby dostosować wartość limitu czasu, użyj następującego polecenia w konsoli szeregowej:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opcja B**: Usuwanie flagi BCD

   a. Aby całkowicie uniemożliwić wyświetlenie monitu menu rozruchu, wprowadź następujące polecenie:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Jeśli nie można użyć konsoli szeregowej do skonfigurowania szybszego czasu rozruchu w powyższych krokach, możesz kontynuować następujące kroki. Teraz będziesz rozwiązywać problemy w trybie offline, aby rozwiązać ten problem.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Użyj [kroków 1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) aby przygotować maszynę wirtualną naprawy.
2. Użyj połączenia pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Konfigurowanie szybszego czasu rozruchu na maszynie wirtualnej naprawy

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Wprowadź następujące elementy, aby włączyć DisplayBootMenu:

   To polecenie należy używać dla **maszyn wirtualnych generacji 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   To polecenie należy używać dla **maszyn wirtualnych generacji 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Zastąp wszelkie symbole większe lub mniejsze niż symbole, a także tekst w nich zawarty, np > <.

3. Zmień wartość limitu czasu na 5 sekund:

   To polecenie należy używać dla **maszyn wirtualnych generacji 1:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   To polecenie należy używać dla **maszyn wirtualnych generacji 2:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Zastąp wszelkie symbole większe lub mniejsze niż symbole, a także tekst w nich zawarty, np > <.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: Przed przebudową maszyny wirtualnej należy włączyć zbieranie konsoli szeregowej i zrzutu pamięci

Aby włączyć zbieranie zrzutów pamięci i konsolę szeregową, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   Włącz konsolę szeregową

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Zastąp wszelkie symbole większe lub mniejsze niż symbole, a także tekst w nich zawarty, np > <.

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