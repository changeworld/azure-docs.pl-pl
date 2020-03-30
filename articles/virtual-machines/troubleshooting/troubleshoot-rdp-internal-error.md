---
title: Błąd wewnętrzny występuje podczas nawiązywać połączenia RDP z maszynami wirtualnymi platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wewnętrznymi błędami protokołu RDP na platformie Microsoft Azure.| Dokumenty firmy Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266925"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>An internal error occurs when you try to connect to an Azure VM through Remote Desktop (Podczas próby połączenia z maszyną wirtualną platformy Azure za pośrednictwem pulpitu zdalnego występuje błąd wewnętrzny)

W tym artykule opisano błąd, który może wystąpić podczas próby nawiązania połączenia z maszyną wirtualną (VM) na platformie Microsoft Azure.


## <a name="symptoms"></a>Objawy

Nie można połączyć się z maszyną wirtualną platformy Azure przy użyciu protokołu pulpitu zdalnego (RDP). Połączenie utknie w sekcji "Konfigurowanie pilota" lub zostanie wyświetlony następujący komunikat o błędzie:

- Błąd wewnętrzny protokołu RDP
- Wystąpił błąd wewnętrzny
- Nie można podłączyć tego komputera do komputera zdalnego. Spróbuj połączyć się ponownie. Jeśli problem będzie się powtarzał, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci


## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z następujących powodów:

- Nie można uzyskać dostępu do lokalnych kluczy szyfrowania RSA.
- Protokół TLS jest wyłączony.
- Certyfikat jest uszkodzony lub wygasł.

## <a name="solution"></a>Rozwiązanie

Przed wykonać następujące kroki, należy wykonać migawkę dysku systemu operacyjnego maszyny Wirtualnej, którego dotyczy problem jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [Migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline,](#repair-the-vm-offline) dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny wirtualnej odzyskiwania.


### <a name="use-serial-control"></a>Użyj kontrolki szeregowego

Połącz się z [konsolą szeregową i otwórz wystąpienie programu PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie Wirtualnej, przejdź do sekcji naprawy maszyny [Wirtualnej w trybie offline.](#repair-the-vm-offline)

#### <a name="step-1-check-the-rdp-port"></a>Krok: 1 Sprawdź port RDP

1. W wystąpieniu programu PowerShell użyj [funkcji NETSTAT,](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) aby sprawdzić, czy port 8080 jest używany przez inne aplikacje:

        Netstat -anob |more
2. Jeśli plik Termservice.exe używa portu 8080, przejdź do kroku 2. Jeśli inna usługa lub aplikacja inna niż Termservice.exe używa portu 8080, wykonaj następujące kroki:

    1. Zatrzymaj usługę dla aplikacji korzystającej z usługi 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Uruchom usługę terminala:

            Start-Service -Name Termservice

2. Jeśli nie można zatrzymać aplikacji lub jeśli ta metoda nie ma zastosowania do ciebie, zmień port dla protokołu RDP:

    1. Zmień port:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Ustaw zaporę dla nowego portu:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Zaktualizuj grupę zabezpieczeń sieci dla nowego portu](../../virtual-network/security-overview.md) w porcie RDP portalu Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Krok 2: Ustawianie poprawnych uprawnień do certyfikatu z podpisem własnym rdp

1.  W wystąpieniu programu PowerShell uruchom następujące polecenia jeden po drugim, aby odnowić certyfikat z podpisem autorydzymr.

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Jeśli nie można odnowić certyfikatu przy użyciu tej metody, spróbuj zdalnie odnowić certyfikat z podpisem autorydzym rdp:

    1. Z działającej maszyny Wirtualnej, która ma łączność z maszyną wirtualną, która występuje problemy, wpisz **mmc** w polu **Uruchom,** aby otworzyć microsoft management console.
    2. W menu **Plik** wybierz polecenie **Dodaj/Usuń przystawkę,** wybierz pozycję **Certyfikaty**, a następnie wybierz polecenie **Dodaj**.
    3. Wybierz **pozycję Konta komputera**, wybierz pozycję Inny **komputer**, a następnie dodaj adres IP problemu maszyny Wirtualnej.
    4. Przejdź do folderu **Pulpit zdalny\Certyfikaty,** kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz polecenie **Usuń**.
    5. W wystąpieniu programu PowerShell z konsoli szeregowej uruchom ponownie usługę konfiguracji pulpitu zdalnego:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Zresetuj uprawnienie do folderu MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Uruchom ponownie maszynę wirtualną, a następnie spróbuj uruchomić połączenie pulpitu zdalnego z maszyną wirtualną. Jeśli błąd nadal występuje, przejdź do następnego kroku.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Krok 3: Włącz wszystkie obsługiwane wersje TLS

Klient RDP używa protokołu TLS 1.0 jako protokołu domyślnego. Można to jednak zmienić na TLS 1.1, który stał się nowym standardem. Jeśli TLS 1.1 jest wyłączona na maszynie wirtualnej, połączenie zakończy się niepowodzeniem.
1.  W wystąpieniu CMD włącz protokół TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Aby zapobiec zastępowaniu zmian przez zasady usługi AD, należy tymczasowo zatrzymać aktualizację zasad grupy:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Uruchom ponownie maszynę wirtualną, aby zmiany zostały wprowadzone. Jeśli problem został rozwiązany, uruchom następujące polecenie, aby ponownie włączyć zasady grupy:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Jeśli zmiana zostanie przywrócona, oznacza to, że w domenie firmy istnieje zasada usługi Active Directory. Musisz zmienić tę zasadę, aby uniknąć ponownego wystąpienia tego problemu.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Podłącz dysk systemu operacyjnego do odzyskiwania maszyny Wirtualnej](../windows/troubleshoot-recovery-disks-portal.md).
2. Po dołączeniu dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zwróć uwagę na literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3. Uruchom połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania.

#### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutu i konsolę szeregową

Aby włączyć dziennik zrzutu i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom następujący skrypt:

    W tym skrypcie zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Resetowanie uprawnień do folderu MachineKeys

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom następujący skrypt. W tym skrypcie zakładamy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Włącz wszystkie obsługiwane wersje protokołu TLS

1.  Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień **(Uruchom jako administrator)** i uruchom następujące polecenia. Poniższy skrypt zakłada, że litera sterownika jest przypisana do dołączonego dysku systemu operacyjnego jest F. Zamień tę literę dysku na odpowiednią wartość dla maszyny Wirtualnej.
2.  Sprawdź, który protokół TLS jest włączony:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Jeśli klucz nie istnieje lub jego wartość wynosi **0,** włącz protokół, uruchamiając następujące skrypty:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Włącz NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Odłącz dysk systemu operacyjnego i ponownie stwórz maszynę wirtualną,](../windows/troubleshoot-recovery-disks-portal.md)a następnie sprawdź, czy problem został rozwiązany.





