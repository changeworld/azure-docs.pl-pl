---
title: Nie można połączyć się zdalnie z maszynami wirtualnymi platformy Azure, ponieważ maszyna wirtualna uruchamia się w trybie awaryjnym | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym nie można rdp do maszyny Wirtualnej, ponieważ maszyna wirtualna jest uruchamiana w trybie awaryjnym.| Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918210"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nie można rdp do maszyny Wirtualnej, ponieważ maszyna wirtualna uruchamia się w trybie awaryjnym

W tym artykule pokazano, jak rozwiązać problem, w którym nie można połączyć się z maszynami wirtualnymi systemu Azure Windows (Maszyny wirtualne), ponieważ maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym.


## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia RDP lub innych połączeń (takich jak HTTP) do maszyny Wirtualnej na platformie Azure, ponieważ maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym. Po sprawdzeniu zrzutu ekranu w [diagnostyce rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal, może się okazać, że maszyna wirtualna uruchamia się normalnie, ale interfejs sieciowy nie jest dostępny:

![Obraz przedstawiający wyrastąpienie sieci w trybie awaryjnym](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Przyczyna

Usługa RDP nie jest dostępna w trybie awaryjnym. Tylko podstawowe programy i usługi systemowe są ładowane, gdy maszyna wirtualna uruchamia się w trybie awaryjnym. Dotyczy to dwóch różnych wersji trybu awaryjnego, które są "Bezpieczny rozruch minimalny" i "Bezpieczny rozruch z łącznością".


## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj kontrolki szeregowej, aby skonfigurować maszynę wirtualną do rozruchu w trybie normalnym lub [naprawić maszynę wirtualną](#repair-the-vm-offline) w trybie offline przy użyciu maszyny Wirtualnej odzyskiwania.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowego

1. Połącz się z [konsolą szeregową i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [naprawianie maszyny wirtualnej w trybie offline](#repair-the-vm-offline).
2. Sprawdź dane konfiguracji rozruchu:

        bcdedit /enum

    Jeśli maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym, w sekcji **Program ładujący rozruch systemu Windows** zostanie wyświetlona dodatkowa flaga o nazwie **safeboot**. Jeśli flaga **safeboot** nie jest widoczna w trybie awaryjnym. Ten artykuł nie dotyczy scenariusza.

    Flaga **safeboot** może pojawić się z następującymi wartościami:
   - Minimalny
   - Network (Sieć)

     W żadnym z tych dwóch trybów prow nie zostanie uruchomiony. W związku z tym poprawka pozostaje taka sama.

     ![Obraz przedstawiający flagę Trybu awaryjnego](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Usuń flagę **safemoade,** więc maszyna wirtualna zostanie uruchomiony w trybie normalnym:

        bcdedit /deletevalue {current} safeboot

4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **safeboot** została usunięta:

        bcdedit /enum

5. Uruchom ponownie maszynę wirtualną, a następnie sprawdź, czy problem został rozwiązany.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.
3. Upewnij się, że dysk jest oflagowany jako **w trybie online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Włącz dziennik zrzutu i konsolę szeregową (opcjonalnie)

Dziennik zrzutu i konsoli szeregowej pomoże nam zrobić dalsze rozwiązywanie problemów, jeśli problem nie może zostać rozwiązany przez rozwiązanie w tym artykule.

Aby włączyć dziennik zrzutu i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom następujący skrypt:

    W tym skrypcie zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurowanie systemu Windows do rozruchu w trybie normalnym

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Sprawdź dane konfiguracji rozruchu. W poniższych poleceniach zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.

        bcdedit /store F:\boot\bcd /enum
    Zanotuj nazwę identyfikatora partycji z folderem **\windows.** Domyślnie nazwa identyfikatora to "Domyślna".

    Jeśli maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym, w sekcji **Program ładujący rozruch systemu Windows** zostanie wyświetlona dodatkowa flaga o nazwie **safeboot**. Jeśli nie widzisz **flagi safeboot,** ten artykuł nie ma zastosowania do scenariusza.

    ![Obraz dotyczący identyfikatora rozruchu](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Usuń flagę **safeboot,** aby maszyna wirtualna została wystartowana w trybie normalnym:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **safeboot** została usunięta:

        bcdedit /store F:\boot\bcd /enum
5. [Odłącz dysk systemu operacyjnego i ponownie stwórz maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md). Następnie sprawdź, czy problem został rozwiązany.
