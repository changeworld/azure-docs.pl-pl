---
title: Usługi pulpitu zdalnego nie jest uruchamiana na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy związane z usługami pulpitu zdalnego, podczas nawiązywania połączenia z maszyną wirtualną | Dokumentacja firmy Microsoft
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989220"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Usługi pulpitu zdalnego nie jest uruchamiana na Maszynie wirtualnej platformy Azure

W tym artykule opisano, jak rozwiązywać problemy dotyczące łączenia się do maszyny wirtualnej (maszyny Wirtualnej platformy Azure), gdy usług pulpitu zdalnego (TermService) nie jest od lub nie można uruchomić.

>[!NOTE]
>Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager. Zalecamy użycie tego modelu w przypadku nowych wdrożeń zamiast przy użyciu klasycznego modelu wdrażania.

## <a name="symptoms"></a>Objawy

Podczas próby nawiązania połączenia z maszyną wirtualną, występują następujące scenariusze:

- Zrzut ekranu maszyny Wirtualnej pokazuje, że system operacyjny jest w pełni załadowany i Oczekiwanie na poświadczenia.
- Wszystkie aplikacje na maszynie Wirtualnej są działa zgodnie z oczekiwaniami i jest dostępny.
- Maszyna wirtualna odpowiada na żądania połączenia TCP na porcie Microsoft protokołu RDP (Remote Desktop) (domyślnie: 3389).
- Zostanie wyświetlony monit o poświadczenia podczas próby nawiązania połączenia RDP.

## <a name="cause"></a>Przyczyna

Ten problem występuje, ponieważ usług pulpitu zdalnego nie jest uruchomiona na maszynie wirtualnej. Przyczyną może zależeć od następujących scenariuszy:

- Usługa TermService została ustawiona na **wyłączone**.
- Usługa TermService jest uległa awarii lub zawiesza się.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj jednej z poniższych rozwiązań lub wypróbuj rozwiązania jedno po drugim:

### <a name="solution-1-using-the-serial-console"></a>Rozwiązanie 1: Przy użyciu konsoli szeregowej

1. Dostęp do [konsoli szeregowej](serial-console-windows.md) , wybierając **pomoc techniczna i rozwiązywanie problemów** > **Konsola szeregowa (wersja zapoznawcza)**. Jeśli ta funkcja jest włączona na maszynie Wirtualnej, możesz połączyć maszynę Wirtualną pomyślnie.

2. Utwórz nowy kanał dla wystąpienia CMD. Typ **CMD** uruchomić kanału można pobrać nazwy kanału.

3. Przełącz się do kanału, na którym uruchomiono wystąpienie CMD, w tym przypadku powinna być kanał 1.

   ```
   ch -si 1
   ```

4. Naciśnij klawisz **Enter** ponownie i wpisz prawidłową nazwę użytkownika i hasło (identyfikator lokalnego lub domeny) dla maszyny Wirtualnej.

5. Kwerenda o stan usługi TermService.

   ```
   sc query TermService
   ```

6. Jeśli stan usługi wskazuje **zatrzymane**, spróbuj uruchomić usługę.

   ```
   sc start TermService
   ```

7. Zapytanie usługi ponownie, aby upewnić się, że usługa została uruchomiona pomyślnie.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>Rozwiązanie 2: Przy użyciu maszyny Wirtualnej odzyskiwania, aby włączyć usługę

[Utwórz kopię zapasową dysku systemu operacyjnego](../windows/snapshot-copy-managed-disk.md) i [Dołącz dysk systemu operacyjnego do ratownictwa maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md). Następnie otwórz wystąpienie CMD z podwyższonym poziomem uprawnień i uruchom następujące skrypty na ratownictwa maszyny Wirtualnej:

>[!NOTE]
>Przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest Zamień F. ją z odpowiednią wartość w maszynie Wirtualnej. Po zakończeniu tej operacji Odłącz dysk od maszyny Wirtualnej odzyskiwania i [ponownie utworzyć maszynę Wirtualną](../windows/create-vm-specialized.md). Dotyczące rozwiązywania problemów, można użyć **1 rozwiązanie** ponieważ konsoli szeregowej zostały włączone.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.
