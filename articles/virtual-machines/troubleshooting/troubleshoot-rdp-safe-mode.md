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
ms.openlocfilehash: 14cd43f7bd7965b755eca14e5914c64e2ec8e044
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981289"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nie można nawiązać połączenia RDP z maszyny Wirtualnej, ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym

W tym artykule pokazano, jak rozwiązać problem, w której nie można dołączyć do platformy Azure Windows Virtual Machines (VMs), ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/management/deployment-models.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Nie można wprowadzać z połączeniem RDP lub inne połączenia (na przykład HTTP) do maszyny Wirtualnej na platformie Azure, ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym. Podczas ewidencjonowania zrzucie ekranu [diagnostykę rozruchu](../troubleshooting/boot-diagnostics.md) w witrynie Azure portal można napotkać wykonać normalnego rozruchu maszyny Wirtualnej, że interfejs sieciowy nie jest dostępna:

![Obraz dotyczący inferce sieci w trybie awaryjnym](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Przyczyna

Usługa RDP nie jest w trybie awaryjnym. Tylko system podstawowych programów i usług są ładowane, gdy maszyna wirtualna jest uruchamiany w trybie awaryjnym. Dotyczy to dwie różne wersje trybu awaryjnego, które są "Minimalny Bezpieczny rozruch" i "Bezpieczny rozruch z łącznością".


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy utworzyć migawkę dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, Użyj sterowania Serial, aby skonfigurować maszynę Wirtualną do rozruchu w trybie normalnym lub [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline) za pomocą maszyny Wirtualnej odzyskiwania.

### <a name="use-serial-control"></a>Korzystanie z kontroli szeregowej

1. Połączyć się z [konsoli szeregowej i otwórz wystąpienie CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Jeśli na maszynie Wirtualnej nie włączono konsoli szeregowej, zobacz [napraw maszynę Wirtualną w tryb offline](#repair-the-vm-offline).
2. Sprawdzanie danych konfiguracji rozruchu:

        bcdedit /enum

    Jeśli maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym, zobaczą dodatkowe flagi, w obszarze **moduł ładujący rozruchu Windows** sekcję o nazwie **tryb awaryjny**. Jeśli nie widzisz **tryb awaryjny** Flaga, maszyna wirtualna nie jest w trybie awaryjnym. W tym artykule nie ma zastosowania do danego scenariusza.

    **Tryb awaryjny** flagi może się pojawić, z następującymi wartościami:
   - Minimalny
   - Network (Sieć)

     W jednej z tych dwóch trybów RDP nie zostanie uruchomiona. W związku z tym poprawki pozostaje taki sam.

     ![Obraz dotyczący flagi trybu awaryjnego](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Usuń **safemoade** flagę, aby maszyna wirtualna zostanie uruchomiony w trybie normalnym:

        bcdedit /deletevalue {current} safeboot

4. Sprawdzanie danych konfiguracji rozruchu, aby upewnić się, że **tryb awaryjny** flaga zostanie usunięty:

        bcdedit /enum

5. Uruchom ponownie maszynę Wirtualną, a następnie sprawdź, czy problem został rozwiązany.

### <a name="repair-the-vm-offline"></a>Napraw maszynę Wirtualną w tryb offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](../windows/troubleshoot-recovery-disks-portal.md).
2. Rozpocznij połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.
3. Upewnij się, że dysk jest oznaczone jako **Online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku, która jest przypisana do dołączonym dysku systemu operacyjnego.

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

    Jeśli maszyna wirtualna jest skonfigurowana do rozruchu w trybie awaryjnym, zobaczą dodatkowe flagi, w obszarze **moduł ładujący rozruchu Windows** sekcję o nazwie **tryb awaryjny**. Jeśli flaga **SAFEBOOT** nie jest widoczna, ten artykuł nie dotyczy Twojego scenariusza.

    ![Obraz przedstawiający identyfikator rozruchu](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Usuń flagę **SAFEBOOT** , aby maszyna wirtualna przejdzie w tryb normalny:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Sprawdzanie danych konfiguracji rozruchu, aby upewnić się, że **tryb awaryjny** flaga zostanie usunięty:

        bcdedit /store F:\boot\bcd /enum
5. [Odłącz dysk systemu operacyjnego i ponowne utworzenie maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md). Następnie sprawdź, czy problem został rozwiązany.
