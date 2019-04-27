---
title: KRYTYCZNE usługi nie powiodło się podczas uruchamiania na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędem "Krytyczne 0x0000005A usługi nie powiodło się", który występuje podczas uruchamiania | Dokumentacja firmy Microsoft
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
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: e828a8fc4211a0f0c4b53a9e18fa1c2fb6f6916b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593220"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows zawiera "Krytyczne usługi nie powiodło się" błękitny ekran podczas uruchamiania na Maszynie wirtualnej platformy Azure
W tym artykule opisano błąd "Krytyczne usługi nie powiodło się", które mogą wystąpić podczas rozruchu Windows maszyn wirtualnych (VM) w systemie Microsoft Azure. Zawiera on kroki rozwiązywania problemów, aby ułatwić rozwiązywanie problemów. 

> [!NOTE] 
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../../azure-resource-manager/resource-manager-deployment-model.md). W tym artykule opisano, przy użyciu modelu wdrażania usługi Resource Manager, w którym firma Microsoft zaleca używanie w przypadku nowych wdrożeń zamiast klasycznego modelu wdrażania.

## <a name="symptom"></a>Objaw 

Nie zaczyna się maszyny Wirtualnej z systemem Windows. Podczas ewidencjonowania zrzuty ekranu rozruchu [diagnostykę rozruchu](./boot-diagnostics.md), zobacz jeden z następujących komunikatów o błędach na niebieskim ekranie:

- "Komputer PC wystąpił problem i musi ponownie uruchomić. Można uruchomić ponownie. Aby uzyskać więcej informacji na temat tego problemu i poprawek można znaleźć http://windows.com/stopcode. Jeśli chcesz wywołać działu pomocy technicznej, nadaj im te informacje: Zatrzymaj wykonywanie kodu: BŁĄD KRYTYCZNY USŁUGI" 
- "Komputer PC wystąpił problem i musi ponownie uruchomić. Trwa zbieranie danych tylko niektóre informacje o błędzie, a następnie będzie możemy ponowne uruchomienie dla Ciebie. Jeśli chcesz dowiedzieć się więcej, możesz wyszukać w trybie online później ten błąd: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Przyczyna

Istnieją różne przyczyny błędów zatrzymania. Najbardziej typowe przyczyny są następujące:
- Problem ze sterownikiem
- Uszkodzeniem systemu plików lub ilości pamięci
- Aplikacja uzyskuje dostęp do zabronione sektora pamięci

## <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, [się z pomocą techniczną i Prześlij plik zrzutu](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), która pomoże nam szybsze zdiagnozować problem lub spróbuj następujących rozwiązań samodzielnej pomocy.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania

1. Utworzenie migawki dysku systemu operacyjnego, których to dotyczy maszyny wirtualnej służy do przechowywania kopii zapasowych. Aby uzyskać więcej informacji, zobacz [Tworzenie migawki dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemu operacyjnego do maszyny Wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md). 
3. Należy ustanowić połączenie pulpitu zdalnego do maszyny Wirtualnej odzyskiwania.

### <a name="enable-dump-logs-and-serial-console"></a>Włącz dzienniki zrzutu i konsoli szeregowej

Dziennika zrzutu i [konsoli szeregowej](./serial-console-windows.md) pomogą nam w celu rozwiązania problemu.

Aby włączyć dzienniki zrzutu i konsoli szeregowej, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujący skrypt:

    W tym skrypcie przyjęto założenie, że litery dysku, która jest przypisana do dołączonym dysku systemu operacyjnego jest F. Należy zamienić go na odpowiednią wartość dla maszyny Wirtualnej.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Zastąp niepodpisanych sterowników

1. Na maszynie Wirtualnej odzyskiwania, uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień. To polecenie ustawia parametry zainfekowanego dysku systemu operacyjnego, można uruchomić w trybie awaryjnym, przy następnym rozruchu:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Na przykład jeśli dysk systemu operacyjnego, który można dołączyć dysku F, uruchom następujące polecenie:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Odłącz dysk systemu operacyjnego, a następnie ponownie Dołącz dysk systemu operacyjnego, do których to dotyczy maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md). Maszyna wirtualna zostanie uruchomiony w trybie awaryjnym. Jeśli nadal występuje ten błąd, przejdź do kroku opcjonalne.
3. Otwórz **Uruchom** pole, a następnie uruchom **weryfikatora** , aby uruchomić narzędzie Menedżer weryfikatora sterowników.
4. Wybierz **automatycznie wybiera niepodpisanych sterowników**, a następnie kliknij przycisk **dalej**.
5. Uzyskasz listę pliki sterownika, które są bez znaku. Należy pamiętać nazw plików.
6. Skopiuj te same wersje tych plików z działającej maszyny Wirtualnej, a następnie zastąpić te pliki bez znaku. 

7. Usuń ustawienia Bezpieczny rozruch:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Uruchom ponownie maszynę wirtualną. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Opcjonalnie: Analizowanie dzienników zrzutu w trybie zrzutu awarii

Aby analizować dzienniki zrzutu samodzielnie, wykonaj następujące kroki:

1. Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania.
2. Na dołączonym dysku systemu operacyjnego, przejdź do **\windows\system32\config**. Skopiuj wszystkie pliki do przechowywania kopii zapasowych w przypadku, gdy wymagane będzie wycofanie.
3. Rozpocznij **Edytora rejestru** (regedit.exe).
4. Wybierz **HKEY_LOCAL_MACHINE** klucza. W menu, wybierz **pliku** > **Załaduj gałąź rejestru**.
5. Przejdź do **\windows\system32\config\SYSTEM** folder na dołączonym dysku systemu operacyjnego. Nazwa gałęzi, wprowadź **BROKENSYSTEM**. Nowej gałęzi rejestru jest wyświetlana w obszarze **HKEY_LOCAL_MACHINE** klucza.
6. Przejdź do **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** i wprowadź następujące zmiany:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Wybierz **BROKENSYSTEM**. W menu, wybierz opcję **pliku** > **Zwolnij gałąź rejestru**.
8.  Modyfikowanie ustawień BCD do rozruchu w trybie debugowania. Uruchom następujące polecenia w wierszu polecenia z podwyższonym poziomem uprawnień:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Odłącz dysk systemu operacyjnego, a następnie ponownie Dołącz dysk systemu operacyjnego, do których to dotyczy maszyny Wirtualnej](troubleshoot-recovery-disks-portal-windows.md).
10. Rozruch maszyny Wirtualnej, aby zobaczyć, jeśli przedstawia analiza zrzutu. Znajdź plik, w której nie można załadować. Należy zastąpić ten plik przy użyciu pliku z działającej maszyny Wirtualnej. 

    Oto przykład analiza zrzutu. Możesz zobaczyć, że **błąd** znajduje się na filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Po pracy i normalny rozruch maszyny Wirtualnej, należy usunąć ustawienia zrzutu awarii:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
