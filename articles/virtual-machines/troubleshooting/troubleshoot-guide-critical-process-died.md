---
title: Błąd zatrzymania systemu Windows -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373364"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Błąd zatrzymania systemu Windows — #0x000000EF "Krytyczny proces zmarł"

Ten artykuł zawiera kroki, aby rozwiązać problemy, w przypadku gdy proces krytyczny umiera podczas rozruchu na maszynie Wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

Podczas korzystania z [diagnostyki rozruchu,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) aby wyświetlić zrzut ekranu maszyny Wirtualnej, zobaczysz, że zrzut ekranu wyświetla błąd *#0x000000EF* z komunikatem *Krytyczny proces zmarł*.

!["Komputer napotkał problem i musi zostać ponownie uruchomiony. Po prostu zbieramy informacje o błędach, a następnie możesz ponownie uruchomić komputer. (##% ukończone) Jeśli chcesz dowiedzieć się więcej, możesz później wyszukać w Internecie ten błąd: 0x00000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Przyczyna

Zazwyczaj jest to spowodowane krytycznym procesem systemowym, który nie działa podczas rozruchu. Możesz przeczytać więcej o krytycznych problemach z procesem w "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Omówienie procesu:

1. Tworzenie i uzyskiwanie dostępu do maszyny wirtualnej naprawy.
2. Napraw wszelkie uszkodzenia systemu operacyjnego.
3. **Zalecane:** Przed przebudową maszyny Wirtualnej należy włączyć kolekcję konsoli szeregowej i zrzutu pamięci.
4. Odbuduj maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Rozwiązywanie problemów będzie rozwiązywać w trybie offline, aby rozwiązać ten problem.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Użyj [kroków 1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z usługi Podłączanie pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

### <a name="fix-any-os-corruption"></a>Napraw wszelkie uszkodzenia systemu operacyjnego

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenie SFC (System File Checker):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Gdzie < boot disk drive > jest wolumin rozruchowy maszyny Wirtualnej naprawy (zazwyczaj "C:") i < uszkodzony dysk > będzie literą dysku dla dołączonego dysku z uszkodzonej maszyny Wirtualnej. Zastąp symbole większe niż / mniejsze, a także tekst w nich zawarty, np > <.

3. Następnie należy użyć [kroku 5 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby ponownie zmontować maszynę wirtualną i sprawdzić, czy jest uruchamiana.
4. Jeśli maszyna wirtualna nadal nie jest uruchamiana, kontynuuj zbieranie pliku zrzutu pamięci.

### <a name="collect-the-memory-dump-file"></a>Zbieranie pliku zrzutu pamięci

Jeśli problem będzie się powtarzał po uruchomieniu SFC, analiza pliku zrzutu pamięci będzie wymagana do określenia przyczyny problemu. Aby zebrać plik zrzutu pamięci, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Podłączanie dysku systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Użyj [kroków 1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) aby przygotować nową maszynę wirtualną naprawy.
2. Korzystanie z usługi Podłączanie pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Znajdź plik zrzutu i prześlij zgłoszenie pomocy technicznej

3. Na maszynie wirtualnej naprawy przejdź do folderu systemu Windows na podłączonym dysku systemu operacyjnego. Jeśli literą sterownika przypisaną do dołączonego dysku systemu operacyjnego jest *F*, należy przejść do *pliku F:\Windows*.
4. Zlokalizuj plik *memory.dmp,* a następnie [prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu pamięci.

   > [!NOTE]
   > Jeśli nie możesz znaleźć pliku zrzutu, wykonaj poniższe kroki, aby włączyć zbieranie zrzutów pamięci i konsolę szeregową, a następnie wróć do tej sekcji i powtórz kroki opisane w powyższym zadaniu, aby zebrać plik zrzutu pamięci.

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
