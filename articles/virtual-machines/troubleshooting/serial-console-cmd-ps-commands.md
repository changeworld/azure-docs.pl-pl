---
title: Obiekt CMD i program PowerShell na maszynach wirtualnych z systemem Windows platformy Azure | Dokumenty firmy Microsoft
description: Jak używać poleceń cmd i programu PowerShell w sac na maszynach wirtualnych systemu Windows platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167028"
---
# <a name="windows-commands---cmd-and-powershell"></a>Polecenia systemu Windows — CMD i PowerShell

Ta sekcja zawiera przykładowe polecenia do wykonywania typowych zadań w scenariuszach, w których może być konieczne użycie usługi SAC w celu uzyskania dostępu do maszyny Wirtualnej systemu Windows, na przykład gdy trzeba rozwiązywać problemy z błędami połączenia RDP.

Usługa SAC została uwzględniona we wszystkich wersjach systemu Windows od czasu systemu Windows Server 2003, ale jest domyślnie wyłączona. SAC polega `sacdrv.sys` na sterowniku `Special Administration Console Helper` jądra, usłudze (`sacsvr`) i procesie. `sacsess.exe` Aby uzyskać więcej informacji, zobacz [Narzędzia i ustawienia usług zarządzania awaryjnego](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC umożliwia podłączenie do uruchomionego systemu operacyjnego za pośrednictwem portu szeregowego. Po uruchomieniu CMD z `sacsess.exe` `cmd.exe` SAC, uruchamia się w uruchomionym os. Widać, że w Menedżerze zadań, jeśli RDP do maszyny wirtualnej w tym samym czasie są połączone z SAC za pośrednictwem funkcji konsoli szeregowej. Cmd, do którego uzyskujesz `cmd.exe` dostęp za pośrednictwem SAC, jest taki sam, jak podczas połączenia za pośrednictwem protokołu RDP. Wszystkie te same polecenia i narzędzia są dostępne, w tym możliwość uruchamiania programu PowerShell z tego wystąpienia CMD. Jest to zasadnicza różnica między SAC i Środowiska odzyskiwania systemu Windows (WinRE) w tym SAC jest umożliwienie zarządzania systemem operacyjnym, gdzie WinRE uruchamia się w innym, minimalny system operacyjny. Podczas gdy maszyny wirtualne platformy Azure nie obsługują możliwości dostępu do usługi WinRE, dzięki funkcji konsoli szeregowej maszyny wirtualne platformy Azure można zarządzać za pośrednictwem usługi SAC.

Ponieważ sac jest ograniczona do buforu ekranu 80x24 bez przewijania do tyłu, dodaj `| more` do poleceń, aby wyświetlić dane wyjściowe po jednej stronie naraz. Użyj, `<spacebar>` aby wyświetlić `<enter>` następną stronę lub wyświetlić następny wiersz.

`SHIFT+INSERT`to skrót do wklejanych okien konsoli szeregowej.

Ze względu na ograniczony bufor ekranu SAC dłuższe polecenia mogą być łatwiejsze do wpisania w lokalnym edytorze tekstu, a następnie wklejone do sac.

## <a name="view-and-edit-windows-registry-settings"></a>Wyświetlanie i edytowanie ustawień rejestru systemu Windows
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy protokół RDP jest włączony
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko wtedy, gdy skonfigurowane jest odpowiednie ustawienie zasad grupy.

### <a name="enable-rdp"></a>Włączanie protokołu RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

Drugi klucz (w obszarze \Policies) będzie potrzebny tylko wtedy, gdy skonfigurowano odpowiednie ustawienie zasad grupy. Wartość zostanie przepisana przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.

## <a name="manage-windows-services"></a>Zarządzanie usługami systemu Windows

### <a name="view-service-state"></a>Wyświetl stan usługi
`sc query termservice`
###  <a name="view-service-logon-account"></a>Wyświetlanie konta logowania usługi
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ustawianie konta logowania do usługi
`sc config termservice obj= "NT Authority\NetworkService"`

Po znaku równości wymagana jest przestrzeń.
### <a name="set-service-start-type"></a>Ustawianie typu uruchomienia usługi
`sc config termservice start= demand`

Po znaku równości wymagana jest przestrzeń. Możliwe wartości `boot`początkowe obejmują `disabled` `delayed-auto`, `system`, `auto` `demand`, , .
### <a name="set-service-dependencies"></a>Ustawianie zależności usługi
`sc config termservice depend= RPCSS`

Po znaku równości wymagana jest przestrzeń.
### <a name="start-service"></a>Uruchom usługę
`net start termservice`

lub

`sc start termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`net stop termservice`

lub

`sc stop termservice`
## <a name="manage-networking-features"></a>Zarządzanie funkcjami sieciowymi
### <a name="show-nic-properties"></a>Pokaż właściwości karty sieciowej
`netsh interface show interface`
### <a name="show-ip-properties"></a>Pokaż właściwości IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Pokaż konfigurację protokołu IPSec
`netsh nap client show configuration`
### <a name="enable-nic"></a>Włącz kartę sieciową
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ustawianie karty sieciowej do używania protokołu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Aby uzyskać `netsh`więcej informacji na temat , [kliknij tutaj](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Maszyny wirtualne platformy Azure powinny być zawsze skonfigurowane w systemu operacyjnego gościa do korzystania z usługi DHCP w celu uzyskania adresu IP. Statyczne ustawienie ip platformy Azure nadal używa usługi DHCP do nadadawania statycznego adresu IP maszynie wirtualnej.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Ping portów
Zainstaluj klienta telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testowanie łączności

`telnet bing.com 80`

Aby usunąć klienta telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Jeśli domyślnie jest ograniczona do metod dostępnych w systemie Windows, program PowerShell może być lepszym rozwiązaniem do testowania łączności z portami. Zobacz sekcję programu PowerShell poniżej, aby uzyskać przykłady.
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Pokaż regułę Zapory systemu Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Wyłączanie Zapory systemu Windows
`netsh advfirewall set allprofiles state off`

Tego polecenia można użyć podczas rozwiązywania problemów, aby tymczasowo wykluczyć Zaporę systemu Windows. Zostanie on włączony przy następnym ponownym uruchomieniu lub po włączeniu go za pomocą poniższego polecenia. Nie należy przerywać usługi Zapory systemu Windows (MPSSVC) ani usługi BFE (Base Filtering Engine) jako sposobu na wykluczenie Zapory systemu Windows. Zatrzymanie mpssvc lub BFE spowoduje zablokowanie całej łączności.
### <a name="enable-windows-firewall"></a>Włączanie Zapory systemu Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Zarządzanie użytkownikami i grupami
### <a name="create-local-user-account"></a>Tworzenie lokalnego konta użytkownika
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Dodawanie użytkownika lokalnego do grupy lokalnej
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone
`net user <username> | find /i "active"`

Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego na nazwę określoną podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Więc to zwykle `Administrator`nie będzie .
### <a name="enable-user-account"></a>Włączanie konta użytkownika
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Wyświetlanie właściwości konta użytkownika
`net user <username>`

Przykładowe wiersze zainteresowania z lokalnego konta administratora:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń systemu Windows
### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń kwerendy
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Zmień `/c:10` żądaną liczbę zdarzeń, aby powrócić, lub przenieś ją, aby zwrócić wszystkie zdarzenia pasujące do filtru.
### <a name="query-event-log-by-event-id"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia i dostawcy
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia i dostawcy w ciągu ostatnich 24 godzin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj, `604800000` aby spojrzeć wstecz 7 dni zamiast 24 godzin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia, dostawcy i danych zdarzeń w ciągu ostatnich 7 dni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Wyświetlanie lub usuwanie zainstalowanych aplikacji
### <a name="list-installed-applications"></a>Lista zainstalowanych aplikacji
`wmic product get Name,InstallDate | sort /r | more`

Rodzaje `sort /r` malejąco według daty instalacji, aby ułatwić zobaczyć, co zostało niedawno zainstalowane. Służy `<spacebar>` do przechodzenia do następnej `<enter>` strony danych wyjściowych lub do przechodzenia do jednego wiersza.
### <a name="uninstall-an-application"></a>Odinstalowywanie aplikacji
`wmic path win32_product where name="<name>" call uninstall`

Zamień `<name>` na nazwę zwróconą w powyższym poleceniu aplikacji, którą chcesz usunąć.

## <a name="file-system-management"></a>Zarządzanie systemem plików
### <a name="get-file-version"></a>Pobierz wersję pliku
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

W tym przykładzie zwraca wersję pliku sterownika wirtualnej karty sieciowej, który jest netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
### <a name="scan-for-system-file-corruption"></a>Skanowanie w poszukiwaniu uszkodzenia pliku systemowego
`sfc /scannow`

Zobacz też [Naprawa obrazu systemu Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Skanowanie w poszukiwaniu uszkodzenia pliku systemowego
`dism /online /cleanup-image /scanhealth`

Zobacz też [Naprawa obrazu systemu Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Eksportowanie uprawnień do pliku tekstowego
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Zapisywanie uprawnień do plików ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Przywracanie uprawnień do plików z pliku ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Ścieżka podczas `/restore` korzystania musi być folderem nadrzędnym folderu określonego podczas korzystania z programu `/save`. W tym `\RSA` przykładzie jest `\MachineKeys` elementem nadrzędnym folderu określonego w powyższym przykładzie. `/save`
### <a name="take-ntfs-ownership-of-a-folder"></a>Przejęcie na własność folderu ntfs
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Udzielanie uprawnień NTFS do folderu cyklicznie
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Zarządzanie urządzeniami
### <a name="remove-non-present-pnp-devices"></a>Usuwanie nieobserwujących urządzeń PNP
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Zarządzanie zasadami grupy
### <a name="force-group-policy-update"></a>Wymuszanie aktualizacji zasad grupy
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Różne zadania
### <a name="show-os-version"></a>Pokaż wersję systemu operacyjnego
`ver`

lub

`wmic os get caption,version,buildnumber /format:list`

lub

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Wyświetl datę instalacji systemu operacyjnego
`systeminfo | find /i "original"`

lub

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Wyświetl strefę czasową
`systeminfo | find /i "time zone"`

lub

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Uruchom ponownie system Windows
`shutdown /r /t 0`

Dodanie `/f` wymusi uruchamianie aplikacji do zamknięcia bez ostrzeżenia użytkowników.
### <a name="detect-safe-mode-boot"></a>Wykrywanie rozruchu w trybie awaryjnym
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Polecenia systemu Windows — Program PowerShell

Aby uruchomić program PowerShell w programie SAC, po osiągnięciu monitu CMD wpisz:

`powershell <enter>`

> [!CAUTION]
> Usuń moduł PSReadLine z sesji programu PowerShell przed uruchomieniem innych poleceń programu PowerShell. Znany jest problem polegający na wprowadzeniu dodatkowych znaków w tekście wklejonym ze schowka, jeśli psreadline jest uruchomiony w sesji programu PowerShell w programie SAC.

Najpierw sprawdź, czy psreadline jest załadowany. Jest ładowany domyślnie w systemach Windows Server 2016, Windows 10 i nowszych wersjach systemu Windows. Byłby obecny tylko we wcześniejszych wersjach systemu Windows, gdyby został zainstalowany ręcznie.

Jeśli to polecenie powróci do monitu bez danych wyjściowych, moduł nie został załadowany i można kontynuować korzystanie z sesji programu PowerShell w sac jako normalne.

`get-module psreadline`

Jeśli powyższe polecenie zwraca wersję modułu PSReadLine, uruchom następujące polecenie, aby ją zwolnić. To polecenie nie usuwa ani nie odinstalowuje modułu, ale zwalnia go tylko z bieżącej sesji programu PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Wyświetlanie i edytowanie ustawień rejestru systemu Windows
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy protokół RDP jest włączony
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko wtedy, gdy skonfigurowane jest odpowiednie ustawienie zasad grupy.
### <a name="enable-rdp"></a>Włączanie protokołu RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Drugi klucz (w obszarze \Policies) będzie potrzebny tylko wtedy, gdy skonfigurowano odpowiednie ustawienie zasad grupy. Wartość zostanie przepisana przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.
## <a name="manage-windows-services"></a>Zarządzanie usługami systemu Windows
### <a name="view-service-details"></a>Wyświetlanie szczegółów usługi
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`może być używany, ale nie obejmuje konta logowania do usługi. `Get-WmiObject win32-service`Czy.
### <a name="set-service-logon-account"></a>Ustawianie konta logowania do usługi
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

W przypadku korzystania z `NT AUTHORITY\LocalService` `NT AUTHORITY\NetworkService`konta `LocalSystem`usługi innego niż , lub , należy określić hasło konta jako ostatni (ósmy) argument po nazwie konta.
### <a name="set-service-startup-type"></a>Ustawianie typu uruchamiania usługi
`set-service termservice -startuptype Manual`

`Set-service`akceptuje `Automatic`, `Manual`lub `Disabled` dla typu uruchamiania.
### <a name="set-service-dependencies"></a>Ustawianie zależności usługi
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Uruchom usługę
`start-service termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`stop-service termservice`
## <a name="manage-networking-features"></a>Zarządzanie funkcjami sieciowymi
### <a name="show-nic-properties"></a>Pokaż właściwości karty sieciowej
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

lub

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`jest dostępny w 2012+, do użytku `Get-WmiObject`2008R2 .
### <a name="show-ip-properties"></a>Pokaż właściwości IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Włącz kartę sieciową
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

lub

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`jest dostępny w 2012+, do użytku `Get-WmiObject`2008R2 .
### <a name="set-nic-to-use-dhcp"></a>Ustawianie karty sieciowej do używania protokołu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`jest dostępna w 2012 r.+. Do użytku `Get-WmiObject`2008R2 . Maszyny wirtualne platformy Azure powinny być zawsze skonfigurowane w systemu operacyjnego gościa do korzystania z usługi DHCP w celu uzyskania adresu IP. Statyczne ustawienie ip platformy Azure nadal używa usługi DHCP do nadadawania adresu IP maszynie wirtualnej.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> Polecenie cmdlet write-progress może nie działać z tym poleceniem. Jako zagrożenie, można uruchomić `$ProgressPreference = "SilentlyContinue"` w programie PowerShell, aby wyłączyć pasek postępu.

lub

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`bez żadnych parametrów spróbuje ping `internetbeacon.msedge.net`. Jest on dostępny na 2012+. Dla 2008R2 `Get-WmiObject` używać jak w drugim przykładzie.
### <a name="port-ping"></a>Ping portów
`test-netconnection -ComputerName bing.com -Port 80`

lub

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`jest dostępna w 2012 r.+. Do użytku w 2008 R2`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`resolve-dnsname bing.com`

lub

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`jest dostępna w 2012 r.+. Do użytku `System.Net.DNS`2008R2 .
### <a name="show-windows-firewall-rule-by-name"></a>Pokaż regułę zapory systemu Windows według nazwy
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Pokaż regułę zapory systemu Windows według portu
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

lub

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`jest dostępna w 2012 r.+. W 2008R2 `hnetcfg.fwpolicy2` użyj obiektu COM.
### <a name="disable-windows-firewall"></a>Wyłączanie zapory systemu Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`jest dostępna w 2012 r.+. W przypadku stosowania w 2008 `netsh advfirewall` R2, jak przywołuje się w powyższej sekcji CMD.
## <a name="manage-users-and-groups"></a>Zarządzanie użytkownikami i grupami
### <a name="create-local-user-account"></a>Tworzenie lokalnego konta użytkownika
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

lub

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`jest dostępna w 2012 r.+. Do użytku `Get-WmiObject`2008R2 . W tym przykładzie pokazano wbudowane konto administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego na nazwę określoną podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Więc to zwykle `Administrator`nie będzie .
### <a name="add-local-user-to-local-group"></a>Dodawanie użytkownika lokalnego do grupy lokalnej
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Włączanie lokalnego konta użytkownika
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

W tym przykładzie włącza wbudowane konto administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego na nazwę określoną podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Więc to zwykle `Administrator`nie będzie .
### <a name="view-user-account-properties"></a>Wyświetlanie właściwości konta użytkownika
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

lub

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`jest dostępna w 2012 r.+. Do użytku `Get-WmiObject`2008R2 . W tym przykładzie pokazano wbudowane konto administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`jest dostępna w 2012 r.+. Do użytku `Get-WmiObject`2008R2 .
## <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń systemu Windows
### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń kwerendy
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Zmień `/c:10` żądaną liczbę zdarzeń, aby powrócić, lub przenieś ją, aby zwrócić wszystkie zdarzenia pasujące do filtru.
### <a name="query-event-log-by-event-id"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia i dostawcy
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia i dostawcy w ciągu ostatnich 24 godzin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj, `604800000` aby spojrzeć wstecz 7 dni zamiast 24 godzin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń kwerendy według identyfikatora zdarzenia, dostawcy i danych zdarzeń w ciągu ostatnich 7 dni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Wyświetlanie lub usuwanie zainstalowanych aplikacji
### <a name="list-installed-software"></a>Lista zainstalowanego oprogramowania
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstaluj oprogramowanie
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Zarządzanie systemem plików
### <a name="get-file-version"></a>Pobierz wersję pliku
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

W tym przykładzie zwraca wersję pliku sterownika wirtualnej karty sieciowej o nazwie netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
### <a name="download-and-extract-file"></a>Pobieranie i wyodrębnianie pliku
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

W tym `c:\bin` przykładzie tworzy folder, a następnie pobiera i wyodrębnia `c:\bin`zestaw narzędzi Sysinternals do .
## <a name="miscellaneous-tasks"></a>Różne zadania
### <a name="show-os-version"></a>Pokaż wersję systemu operacyjnego
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Wyświetl datę instalacji systemu operacyjnego
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Wyświetlanie czasu pracy systemu Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Zwraca czas `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`pracy bez `49:16:48:00.00`przestojów jako , na przykład .
### <a name="restart-windows"></a>Uruchom ponownie system Windows
`restart-computer`

Dodanie `-force` wymusi uruchamianie aplikacji do zamknięcia bez ostrzeżenia użytkowników.
## <a name="instance-metadata"></a>Metadane wystąpienia

Można kwerendy metadanych wystąpienia platformy Azure z poziomu maszyny Wirtualnej platformy Azure, aby wyświetlić szczegóły, takie jak osType, Lokalizacja, vmSize, vmId, nazwa, resourceGroupName, subscriptionId, privateIpAddress i publicIpAddress.

Wykonywanie zapytań metadanych wystąpienia wymaga dobrej łączności sieciowej gościa, ponieważ wywołuje REST za pośrednictwem hosta platformy Azure do usługi metadanych wystąpienia. Więc jeśli jesteś w stanie kwerendy metadanych wystąpienia, który informuje, że gość jest w stanie komunikować się za pośrednictwem sieci do usługi hostowanej na platformie Azure.

Aby uzyskać więcej informacji, zobacz [usługa metadanych wystąpienia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadane wystąpienia
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ systemu operacyjnego (metadane wystąpienia)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Lokalizacja (metadane wystąpienia)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Rozmiar (metadane wystąpienia)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>Identyfikator maszyny Wirtualnej (metadane wystąpienia)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nazwa maszyny Wirtualnej (metadane wystąpienia)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nazwa grupy zasobów (metadane wystąpienia)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Identyfikator subskrypcji (metadane wystąpienia)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tagi (metadane wystąpienia)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Identyfikator grupy umieszczania (metadane wystąpienia)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domena błędów platformy (metadane wystąpienia)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domena aktualizacji platformy (metadane wystąpienia)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Prywatny adres IP IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Publiczny adres IP protokołu IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adres / prefiks podsieci IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Adres IP IPv6 (metadane wystąpienia)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adres MAC (metadane wystąpienia)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Następne kroki
* Główna konsola szeregowa Okna dokumentacja strona znajduje się [tutaj](serial-console-windows.md).
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych [z systemem Linux.](serial-console-linux.md)
* Dowiedz się więcej o [diagnostyce rozruchu](boot-diagnostics.md).
