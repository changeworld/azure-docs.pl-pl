---
title: Nie można dołączyć do usługi Azure Virtual Machines zdalnie ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym nie protokołu RDP z maszyną wirtualną, ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym. | Dokumentacja firmy Microsoft
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918210"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nie można nawiązać połączenia RDP z maszyny Wirtualnej, ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym

W tym artykule pokazano, jak rozwiązać problem, w której nie można dołączyć do platformy Azure Windows Virtual Machines (VMs), ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym.


## <a name="symptoms"></a>Objawy

Nie można wprowadzać z połączeniem RDP lub inne połączenia (na przykład HTTP) do maszyny Wirtualnej na platformie Azure, ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym. Po sprawdzeniu zrzutu ekranu w [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md) w Azure Portal można zobaczyć, że maszyna wirtualna jest uruchamiana normalnie, ale interfejs sieciowy nie jest dostępny:

![Obraz dotyczący inferce sieci w trybie awaryjnym](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Przyczyna

Usługa RDP nie jest w trybie awaryjnym. Tylko system podstawowych programów i usług są ładowane, gdy maszyna wirtualna jest uruchamiany w trybie awaryjnym. Dotyczy to dwie różne wersje trybu awaryjnego, które są "Minimalny Bezpieczny rozruch" i "Bezpieczny rozruch z łącznością".


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć kontrolki serial, aby skonfigurować maszynę wirtualną do uruchamiania w trybie normalnym lub [naprawić maszynę wirtualną w trybie offline](#repair-the-vm-offline) przy użyciu maszyny wirtualnej odzyskiwania.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połącz się z [konsolą szeregową i Otwórz wystąpienie programu Cmd](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, zobacz [Naprawa maszyny wirtualnej w trybie offline](#repair-the-vm-offline).
2. Sprawdzanie danych konfiguracji rozruchu:

        bcdedit /enum

    Jeśli maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym, w sekcji **modułu ładującego rozruchu systemu Windows** o nazwie **SAFEBOOT**zostanie wyświetlona dodatkowa flaga. Jeśli flaga **SAFEBOOT** nie jest widoczna, maszyna wirtualna nie jest w trybie awaryjnym. W tym artykule nie ma zastosowania do danego scenariusza.

    Flaga **SAFEBOOT** może się pojawić z następującymi wartościami:
   - Minimalny
   - Network

     W jednej z tych dwóch trybów RDP nie zostanie uruchomiona. W związku z tym poprawki pozostaje taki sam.

     ![Obraz dotyczący flagi trybu awaryjnego](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Usuń flagę **safemoade** , aby maszyna wirtualna przejdzie w tryb normalny:

        bcdedit /deletevalue {current} safeboot

4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **SAFEBOOT** została usunięta:

        bcdedit /enum

5. Uruchom ponownie maszynę Wirtualną, a następnie sprawdź, czy problem został rozwiązany.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.
3. Upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Włącz dziennik zrzutu i konsoli szeregowej (opcjonalnie)

Dziennik zrzutu i konsoli szeregowej pomogą nam w celu rozwiązania problemu, jeśli problem nie jest rozpoznawany przez to rozwiązanie, w tym artykule.

Aby włączyć dziennik zrzutu i konsoli szeregowej, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom następujący skrypt:

    W tym skrypcie przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest F. Zastąp tę literę dysku z odpowiednią wartością dla maszyny Wirtualnej.

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurowanie systemu Windows w celu rozruchu w trybie normalnym

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Sprawdź dane konfiguracji rozruchu. W poniższych poleceniach przyjęto założenie, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.

        bcdedit /store F:\boot\bcd /enum
    Zanotuj nazwę identyfikatora partycji, która ma folder **\Windows** . Domyślnie nazwa identyfikatora to "default".

    Jeśli maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym, w sekcji **modułu ładującego rozruchu systemu Windows** o nazwie **SAFEBOOT**zostanie wyświetlona dodatkowa flaga. Jeśli flaga **SAFEBOOT** nie jest widoczna, ten artykuł nie dotyczy Twojego scenariusza.

    ![Obraz przedstawiający identyfikator rozruchu](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Usuń flagę **SAFEBOOT** , aby maszyna wirtualna przejdzie w tryb normalny:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Sprawdź dane konfiguracji rozruchu, aby upewnić się, że flaga **SAFEBOOT** została usunięta:

        bcdedit /store F:\boot\bcd /enum
5. [Odłącz dysk systemu operacyjnego i Utwórz ponownie maszynę wirtualną](../windows/troubleshoot-recovery-disks-portal.md). Następnie sprawdź, czy problem został rozwiązany.
