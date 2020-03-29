---
title: USŁUGA KRYTYCZNA NIE POWIODŁA SIĘ podczas uruchamiania maszyny Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązać problem z błędem "0x0000005A-CRITICAL SERVICE FAILED" który występuje podczas uruchamiania | Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921457"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>System Windows pokazuje "USŁUGA KRYTYCZNA NIE POWIODŁA SIĘ" na niebieskim ekranie podczas uruchamiania maszyny Wirtualnej platformy Azure
W tym artykule opisano błąd "KRYTYCZNA USŁUGA NIE POWIODŁA SIĘ", który może wystąpić podczas uruchamiania maszyny wirtualnej systemu Windows (VM) na platformie Microsoft Azure. Zawiera kroki rozwiązywania problemów, aby pomóc rozwiązać problemy. 


## <a name="symptom"></a>Objaw 

Maszyna wirtualna systemu Windows nie uruchamia się. Po sprawdzeniu zrzutów ekranu rozruchu w [diagnostyce rozruchu](./boot-diagnostics.md)na niebieskim ekranie zostanie wyświetlony jeden z następujących komunikatów o błędach:

- "Komputer napotkał problem i musi zostać ponownie uruchomiony. Można ponownie uruchomić komputer. Aby uzyskać więcej informacji na temat https://windows.com/stopcodetego problemu i możliwych poprawek, odwiedź stronę . Jeśli zadzwonisz do osoby pomocy technicznej, podaj im te informacje: Kod zatrzymania: KRYTYCZNA USŁUGA NIE POWIODŁA SIĘ" 
- "Komputer napotkał problem i musi zostać ponownie uruchomiony. Zbieramy tylko informacje o błędach, a następnie uruchomimy ponownie dla Ciebie. Jeśli chcesz dowiedzieć się więcej, możesz później wyszukać w Internecie ten błąd: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Przyczyna

Istnieją różne przyczyny błędów zatrzymania. Najczęstsze przyczyny to:
- Problem ze sterownikiem
- Uszkodzony plik systemowy lub pamięć
- Aplikacja uzyskuje dostęp do zakazanego sektora pamięci

## <a name="solution"></a>Rozwiązanie 

Aby rozwiązać ten problem, [skontaktuj się z pomocą techniczną i prześlij plik zrzutu,](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)który pomoże nam szybciej zdiagnozować problem, lub wypróbuj następujące rozwiązanie samopomocy.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. Wykonaj migawkę dysku systemu operacyjnego maszyny wirtualnej, którego dotyczy problem, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](./troubleshoot-recovery-disks-portal-windows.md). 
3. Ustanawianie połączenia pulpitu zdalnego z maszyną wirtualną odzyskiwania.

### <a name="enable-dump-logs-and-serial-console"></a>Włączanie dzienników zrzutów i konsoli szeregowych

Dziennik zrzutu i [konsola szeregowa](./serial-console-windows.md) pomogą nam w dalszym rozwiązywaniu problemów.

Aby włączyć dzienniki zrzutów i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (uruchom jako administrator).
2. Uruchom następujący skrypt:

    W tym skrypcie zakładamy, że litera dysku, która jest przypisana do dołączonego dysku systemu operacyjnego, to F. Należy zastąpić go odpowiednią wartością dla maszyny Wirtualnej.

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

### <a name="replace-the-unsigned-drivers"></a>Wymień niepodpisane sterowniki

1. Na maszynie wirtualnej odzyskiwania uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień. To polecenie ustawia dysk systemu operacyjnego, którego dotyczy problem, aby rozpocząć pracę w trybie awaryjnym przy następnym rozruchu:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Jeśli na przykład podłączony dysk systemu operacyjnego to dysk F, uruchom następujące polecenie:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Odłącz dysk systemu operacyjnego, a następnie ponownie podłącz dysk systemu operacyjnego do maszyny wirtualnej, na którą dotyczy problem.](troubleshoot-recovery-disks-portal-windows.md) Maszyna wirtualna uruchomi się w trybie awaryjnym. Jeśli nadal wystąpi błąd, przejdź do kroku opcjonalnego.
3. Otwórz pole **Uruchom** i uruchom **weryfikator,** aby uruchomić narzędzie Menedżer weryfikatora sterowników.
4. Wybierz **pozycję Automatycznie wybieraj niepodpisane sterowniki,** a następnie kliknij przycisk **Dalej**.
5. Otrzymasz listę plików sterowników, które są niepodpisane. Zapamiętaj nazwy plików.
6. Skopiuj te same wersje tych plików z działającej maszyny Wirtualnej, a następnie zastąp te niepodpisane pliki. 

7. Usuń ustawienia bezpiecznego rozruchu:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Uruchom ponownie maszynę wirtualną. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Opcjonalnie: analizowanie dzienników zrzutu w trybie awarii zrzutu

Aby samodzielnie przeanalizować dzienniki zrzutu, wykonaj następujące kroki:

1. Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania.
2. Na podłączonym dysku systemu operacyjnego przejdź do **folderu \windows\system32\config**. Skopiuj wszystkie pliki jako kopię zapasową w przypadku, gdy wymagane jest wycofanie.
3. Uruchom **Edytor rejestru** (regedit.exe).
4. Wybierz **HKEY_LOCAL_MACHINE** klucz. W menu wybierz polecenie**Gałąź załadunku** **pliku** > .
5. Przejdź do folderu **\windows\system32\config\SYSTEM** na dołączonym dysku systemu operacyjnego. Aby uzyskać nazwę gałęzi, wprowadź **BROKENSYSTEM**. Nowy gałąź rejestru jest wyświetlany pod kluczem **HKEY_LOCAL_MACHINE.**
6. Przejdź do **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** i wprowadzać następujące zmiany:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Wybierz **BROKENSYSTEM**. Z menu wybierz polecenie**Rozładuj** **plik** > gałęzi .
8.  Zmodyfikuj konfigurację BCD, aby uruchomić go w tryb debugowania. Uruchom następujące polecenia z wiersza polecenia z podwyższonym poziomem uprawnień:

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
9. [Odłącz dysk systemu operacyjnego, a następnie ponownie podłącz dysk systemu operacyjnego do maszyny wirtualnej, na którą dotyczy problem.](troubleshoot-recovery-disks-portal-windows.md)
10. Uruchom maszynę wirtualną, aby sprawdzić, czy pokazuje analizę zrzutu. Znajdź plik, który nie może się załadować. Musisz zastąpić ten plik plikiem z działającej maszyny Wirtualnej. 

    Poniżej przedstawiono przykład analizy zrzutu. Widać, że **błąd** znajduje się na filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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

11. Gdy maszyna wirtualna działa i uruchamia się normalnie, usuń ustawienia awarii zrzutu:

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
