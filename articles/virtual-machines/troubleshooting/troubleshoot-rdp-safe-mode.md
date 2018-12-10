---
title: Nie można dołączyć do usługi Azure Virtual Machines zdalnie ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym nie protokołu RDP z maszyną wirtualną, ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym. | Dokumentacja firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 097b7efd7643e3b8450284d19e13a428dfd48ac2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138861"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nie można nawiązać połączenia RDP z maszyny Wirtualnej, ponieważ maszyna wirtualna jest uruchamiany w trybie awaryjnym

W tym artykule pokazano, jak rozwiązać problem, w której nie można dołączyć do platformy Azure Windows Virtual Machines (VMs), ponieważ maszyna wirtualna jest skonfigurowana do uruchamiania w trybie awaryjnym.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

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
    - Sieć

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

#### Configure the Windows to boot into normal mode

1. Open an elevated command prompt session (**Run as administrator**).
2. Check the boot configuration data. In the following commands, we assume that the drive letter that is assigned to the attached OS disk is F. Replace this drive letter with the appropriate value for your VM.

        bcdedit /store F:\boot\bcd /enum
    Take note of the Identifier name of the partition that has the **\windows** folder. By default, the  Identifier name is "Default".

    If the VM is configured to boot into Safe Mode, you will see an extra flag under the **Windows Boot Loader** section called **safeboot**. If you do not see the **safeboot** flag, this article does not apply to your scenario.

    ![The image about boot Identifier](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remove the **safeboot** flag, so the VM will boot into normal mode:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Check the boot configuration data to make sure that the **safeboot** flag is removed:

        bcdedit /store F:\boot\bcd /enum
5. [Detach the OS disk and recreate the VM](../windows/troubleshoot-recovery-disks-portal.md). Then check whether the issue is resolved.
