---
title: Polecenia i programu PowerShell na maszynach wirtualnych Windows Azure | Dokumentacja firmy Microsoft
description: Jak używać poleceń programu PowerShell i CMD, w ramach SAC na maszynach wirtualnych Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: f286881341e527d3f01e57768cd48405c85a9a69
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710611"
---
# <a name="windows-commands---cmd-and-powershell"></a>Polecenia Windows - CMD i programu PowerShell

Ta sekcja zawiera przykładowe polecenia do wykonywania typowych zadań w scenariuszach, w których konieczne może być SAC umożliwia dostęp do maszyny Wirtualnej Windows, takie jak kiedy trzeba rozwiązać błędy połączeń RDP.

Konsola SAC od systemu Windows Server 2003 została uwzględniona we wszystkich wersjach systemu Windows, ale jest domyślnie wyłączona. Konsola SAC opiera się na `sacdrv.sys` sterownik jądra `Special Administration Console Helper` usługi (`sacsvr`), a `sacsess.exe` procesu. Aby uzyskać więcej informacji, zobacz [narzędzia usług zarządzania awaryjnego i ustawienia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

Konsola SAC pozwala połączyć się z Twojego systemu operacyjnego za pośrednictwem portu szeregowego. Podczas uruchamiania polecenia SAC, `sacsess.exe` uruchamia `cmd.exe` w ramach Twojego systemu operacyjnego. Aby zobaczyć, czy w ramach zadania Menedżera Jeśli protokołu RDP z maszyną wirtualną, w tym samym czasu możesz połączenie SAC za pośrednictwem funkcji konsoli szeregowej. Polecenie CMD, możesz uzyskać dostęp za pośrednictwem SAC jest taka sama `cmd.exe` użyć podczas połączenia za pośrednictwem protokołu RDP. Te same polecenia i narzędzia są dostępne, łącznie z możliwością uruchamiania środowiska PowerShell z tego wystąpienia CMD. Główna różnica między SAC i środowisko odzyskiwania Windows (WinRE) w tym SAC jest umożliwiające zarządzanie systemu operacyjnego, w którym WinRE rozruch w różnych i minimalny system operacyjny. Gdy maszyny wirtualne platformy Azure nie obsługują możliwość dostępu do środowiska WinRE, przy użyciu funkcji konsoli szeregowej maszyny wirtualne platformy Azure mogą być zarządzane za pośrednictwem SAC.

Ponieważ Konsola SAC jest ograniczona do buforu ekranu 80 x 24 z powrotem bez przewijania, Dodaj `| more` do poleceń, aby wyświetlić dane wyjściowe o jedną stronę w danym momencie. Użyj `<spacebar>` się następnej strony lub `<enter>` aby zobaczyć następny wiersz.  

`SHIFT+INSERT` jest Wklej skrót dla okna konsoli szeregowej.

Ze względu na buforu ekranu ograniczone przez SAC dłużej polecenia może być łatwiejsze do wpisywania w edytorze tekstów lokalnych i następnie wklejone do SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Wyświetlanie i edytowanie ustawień rejestru Windows
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy jest włączony protokół RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko, jeśli skonfigurowano ustawienie zasad grupy odpowiednie.

### <a name="enable-rdp"></a>Włącz protokół RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Drugi klucz (w obszarze \Policies) będą wymagane tylko w przypadku, jeśli ustawienie zasad grupy w odpowiednich zostało skonfigurowane. Wartość będzie przepisany przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.

## <a name="manage-windows-services"></a>Zarządzanie usługami Windows

### <a name="view-service-state"></a>Wyświetl stan usługi
`sc query termservice`
###  <a name="view-service-logon-account"></a>Konto logowania usługi widoku
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ustaw konto logowania usługi 
`sc config termservice obj= "NT Authority\NetworkService"`

Obszar jest wymagany po znaku równości.
### <a name="set-service-start-type"></a>Typ uruchomienia usługi zestawu
`sc config termservice start= demand` 

Obszar jest wymagany po znaku równości. Start możliwe wartości to `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Definiowanie zależności usługi
`sc config termservice depend= RPCSS`

Obszar jest wymagany po znaku równości.
### <a name="start-service"></a>Uruchom usługę
`net start termservice`

lub

`sc start termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`net stop termservice`

lub

`sc stop termservice`
## <a name="manage-networking-features"></a>Zarządzanie funkcjami sieci
### <a name="show-nic-properties"></a>Pokaż właściwości karty Sieciowej
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Pokaż właściwości adresu IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Pokaż konfiguracji protokołu IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Włącz kartę Sieciową
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ustaw kartę Sieciową do używania protokołu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Aby uzyskać więcej informacji na temat `netsh`, [tutaj](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Maszyny wirtualne platformy Azure zawsze powinna być skonfigurowana w systemie operacyjnym gościa do używania protokołu DHCP w celu uzyskania adresu IP. Azure statyczne ustawienia IP nadal używa protokołu DHCP oferowanie statyczny adres IP maszyny Wirtualnej.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port ping  
Instalowanie klienta usługi telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testowanie łączności

`telnet bing.com 80`

Aby usunąć klienta usługi telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Gdy jest ograniczony do metod, które są dostępne w Windows domyślnie, programu PowerShell może być lepszym rozwiązaniem do testowania połączeń przez porty. W sekcji programu PowerShell poniższych przykładów.
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Pokaż reguły zapory Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Wyłącz zaporę Windows
`netsh advfirewall set allprofiles state off`

Można użyć tego polecenia podczas rozwiązywania problemów z tymczasowo wyeliminuj zapory Windows. Będzie ono włączone przy następnym ponownym uruchomieniu lub po włączeniu go za pomocą poniższego polecenia. Usługa Windows zapory (MPSSVC) lub usługa Podstawowy aparat filtrowania (BFE) nie zostanie zatrzymana jako sposób wykluczenia zapory Windows. Zatrzymywanie MPSSVC lub BFE spowoduje łączności wszystkie blokowane.
### <a name="enable-windows-firewall"></a>Włącz zaporę Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Zarządzanie użytkownikami i grupami
### <a name="create-local-user-account"></a>Tworzenie konta użytkownika lokalnego
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Dodawanie użytkownika lokalnego do lokalnej grupy
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączona.
`net user <username> | find /i "active"`

Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będzie konta administratora lokalnego, zmieniona na nazwę określonego podczas aprowizowania maszyny Wirtualnej. Więc zazwyczaj nie zostanie on `Administrator`.
### <a name="enable-user-account"></a>Włącz konto użytkownika
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`net user <username>`

Przykładowe wiersze zainteresowania z konta administratora lokalnego:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń Windows
### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń zapytań
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Zmiana `/c:10` żądaną liczbę zdarzeń, które zwracają lub przenieść je do zwrócenia wszystkich zdarzeń pasujących do filtru.
### <a name="query-event-log-by-event-id"></a>Zapytanie dziennika zdarzeń wg Identyfikatora zdarzenia
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy dla ostatnich 24 godzinach
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` do wyszukiwania ponownie 7 dni, zamiast 24 godzin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Wyświetl lub Usuń zainstalowane aplikacje
### <a name="list-installed-applications"></a>Wyświetlanie listy zainstalowanych aplikacji
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` Sortuj malejąco według zainstalować daty, aby ułatwić Zobacz, co został niedawno zainstalowany. Użyj `<spacebar>` aby przejść do następnej strony w danych wyjściowych, lub `<enter>` Aby awansować jeden wiersz.
### <a name="uninstall-an-application"></a>Odinstalowywanie aplikacji
`wmic path win32_product where name="<name>" call uninstall`

Zastąp `<name>` o nazwie zwracane w poleceniu powyżej dla aplikacji do usunięcia.

## <a name="file-system-management"></a>Zarządzanie systemem plików
### <a name="get-file-version"></a>Pobierz wersję pliku
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

W tym przykładzie zwraca wersję pliku wirtualnego sterownika karty Sieciowej, do której jest netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji Windows.
### <a name="scan-for-system-file-corruption"></a>Skanowanie w poszukiwaniu uszkodzenie pliku systemowego
`sfc /scannow`

Zobacz też [napraw obrazu Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Skanowanie w poszukiwaniu uszkodzenie pliku systemowego
`dism /online /cleanup-image /scanhealth`

Zobacz też [napraw obrazu Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Eksportowanie pliku uprawnień do pliku tekstowego
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Zapisz plik uprawnień do pliku listy ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Przywróć plik uprawnienia z pliku listy ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Ścieżka, korzystając z `/restore` musi być folder nadrzędny w folderze określonym przy użyciu `/save`. W tym przykładzie `\RSA` jest elementem nadrzędnym `\MachineKeys` folder określony w `/save` w powyższym przykładzie.
### <a name="take-ntfs-ownership-of-a-folder"></a>Przejęcie na własność folderu systemu plików NTFS
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Udzielanie uprawnień systemu plików NTFS do lokalizacji folderu
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Zarządzanie urządzeniami
### <a name="remove-non-present-pnp-devices"></a>Usuń inne niż obecna urządzenia PNP
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
### <a name="view-os-install-date"></a>Data instalacji systemu operacyjnego widoku
`systeminfo | find /i "original"`

lub 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Strefa czasowa widoku
`systeminfo | find /i "time zone"`

lub

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Uruchom ponownie Windows
`shutdown /r /t 0`

Dodawanie `/f` wymusza zamknięcie bez ostrzeżenia użytkowników uruchomionych aplikacji.
### <a name="detect-safe-mode-boot"></a>Wykrywanie rozruch w trybie awaryjnym
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Polecenia Windows — program PowerShell

Aby uruchomić program PowerShell w SAC, po dojściu wiersz polecenia, wpisz:

`powershell <enter>`

> [!CAUTION]
> Usuń moduł PSReadLine z sesji programu PowerShell, przed uruchomieniem innych poleceń programu PowerShell. Jest to znany problem gdzie dodatkowe znaki mogą być wprowadzane w tekście wklejonych ze Schowka, jeśli PSReadLine jest uruchomiony w sesji programu PowerShell w SAC.

Najpierw sprawdź, czy PSReadLine jest załadowany. Jest on ładowany domyślnie w systemie Windows Server 2016, Windows 10 i nowszych wersjach systemu Windows. Tylko byłaby obecne w starszych wersjach Windows ręcznie je zainstalowano. 

Jeśli to polecenie zwróci do wiersza bez wpisu, następnie moduł nie został załadowany, i możesz kontynuować korzystanie z sesji programu PowerShell SAC, jak zwykle.

`get-module psreadline`

Jeśli powyższe polecenie zwróci wersję modułu PSReadLine, uruchom następujące polecenie, aby zwolnić ją. To polecenie nie powoduje rozwiązania ani odinstalowania modułu, ją tylko zwalnia z bieżącej sesji programu PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Wyświetlanie i edytowanie ustawień rejestru Windows
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy jest włączony protokół RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko, jeśli skonfigurowano ustawienie zasad grupy odpowiednie.
### <a name="enable-rdp"></a>Włącz protokół RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Drugi klucz (w obszarze \Policies) będą wymagane tylko w przypadku, jeśli ustawienie zasad grupy w odpowiednich zostało skonfigurowane. Wartość będzie przepisany przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.
## <a name="manage-windows-services"></a>Zarządzanie usługami Windows
### <a name="view-service-details"></a>Wyświetl szczegóły usługi
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` mogą być używane, ale nie obejmuje konto logowania usługi. `Get-WmiObject win32-service` wykonuje.
### <a name="set-service-logon-account"></a>Ustaw konto logowania usługi
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Korzystając z konta usługi inne niż `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, lub `LocalSystem`, określ hasło do konta jako ostatni argument (ósmego) po nazwie konta.
### <a name="set-service-startup-type"></a>Ustawić automatyczny typ uruchamiania usługi
`set-service termservice -startuptype Manual`

`Set-service` akceptuje `Automatic`, `Manual`, lub `Disabled` typ uruchomienia.
### <a name="set-service-dependencies"></a>Definiowanie zależności usługi
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Uruchom usługę
`start-service termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`stop-service termservice`
## <a name="manage-networking-features"></a>Zarządzanie funkcjami sieci
### <a name="show-nic-properties"></a>Pokaż właściwości karty Sieciowej
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

lub 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` jest dostępna w 2012 +, do użytku 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Pokaż właściwości adresu IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Włącz kartę Sieciową
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

lub

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` jest dostępna w 2012 +, do użytku 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Ustaw kartę Sieciową do używania protokołu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` jest dostępna w 2012 +. W przypadku 2008R2 użyj `Get-WmiObject`. Maszyny wirtualne platformy Azure zawsze powinna być skonfigurowana w systemie operacyjnym gościa do używania protokołu DHCP w celu uzyskania adresu IP. Azure statyczne ustawienia IP nadal używa protokołu DHCP można podać adres IP do maszyny Wirtualnej.
### <a name="ping"></a>Ping
`test-netconnection`

lub

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez żadnych parametrów spróbuje wykonać polecenie ping `internetbeacon.msedge.net`. Jest ona dostępna w 2012 +. W przypadku 2008R2 użyj `Get-WmiObject` jak w drugim przykładzie.
### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

lub

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` jest dostępna w 2012 +. Do użytku 2008R2 `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`resolve-dnsname bing.com` 

lub 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` jest dostępna w 2012 +. W przypadku 2008R2 użyj `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Pokaż reguły zapory Windows według nazw
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Pokaż reguły zapory Windows według portów
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

lub

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` jest dostępna w 2012 +. W przypadku 2008R2 użyj `hnetcfg.fwpolicy2` obiektu COM. 
### <a name="disable-windows-firewall"></a>Wyłącz zaporę Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` jest dostępna w 2012 +. W przypadku 2008R2 użyj `netsh advfirewall` jako odwołania w powyższej sekcji CMD.
## <a name="manage-users-and-groups"></a>Zarządzanie użytkownikami i grupami
### <a name="create-local-user-account"></a>Tworzenie konta użytkownika lokalnego
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączona.
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

lub 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` jest dostępna w 2012 +. W przypadku 2008R2 użyj `Get-WmiObject`. Ten przykład przedstawia konta wbudowanego konta administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będzie konta administratora lokalnego, zmieniona na nazwę określonego podczas aprowizowania maszyny Wirtualnej. Więc zazwyczaj nie zostanie on `Administrator`.
### <a name="add-local-user-to-local-group"></a>Dodawanie użytkownika lokalnego do lokalnej grupy
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Włącz konto użytkownika lokalnego
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Ten przykład włącza konta wbudowanego konta administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będzie konta administratora lokalnego, zmieniona na nazwę określonego podczas aprowizowania maszyny Wirtualnej. Więc zazwyczaj nie zostanie on `Administrator`.
### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

lub 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` jest dostępna w 2012 +. W przypadku 2008R2 użyj `Get-WmiObject`. Ten przykład przedstawia konta wbudowanego konta administratora lokalnego, które zawsze ma identyfikator SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` jest dostępna w 2012 +. W przypadku 2008R2 użyj `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń Windows
### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń zapytań
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Zmiana `/c:10` żądaną liczbę zdarzeń, które zwracają lub przenieść je do zwrócenia wszystkich zdarzeń pasujących do filtru.
### <a name="query-event-log-by-event-id"></a>Zapytanie dziennika zdarzeń wg Identyfikatora zdarzenia
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy dla ostatnich 24 godzinach
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` do wyszukiwania ponownie 7 dni, zamiast 24 godzin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Wyświetl lub Usuń zainstalowane aplikacje
### <a name="list-installed-software"></a>Wyświetlanie listy zainstalowanych oprogramowania
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstalowywanie oprogramowania
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Zarządzanie systemem plików
### <a name="get-file-version"></a>Pobierz wersję pliku
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

W tym przykładzie zwraca informacje o wersji pliku sterownika karty Sieciowej wirtualny nosi nazwę netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji Windows.
### <a name="download-and-extract-file"></a>Pobieranie i wyodrębnianie plików
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ten przykład tworzy `c:\bin` folderu, następnie pliki do pobrania i wyodrębnia Sysinternals zestawu narzędzi do `c:\bin`.
## <a name="miscellaneous-tasks"></a>Różne zadania
### <a name="show-os-version"></a>Pokaż wersję systemu operacyjnego
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Data instalacji systemu operacyjnego widoku
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Wyświetl czas pracy Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Zwraca czas pracy jako `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, na przykład `49:16:48:00.00`. 
### <a name="restart-windows"></a>Uruchom ponownie Windows
`restart-computer`

Dodawanie `-force` wymusza zamknięcie bez ostrzeżenia użytkowników uruchomionych aplikacji.
## <a name="instance-metadata"></a>Instance Metadata

Możesz zbadać metadane wystąpienia platformy Azure z w ramach Twojej maszyny Wirtualnej platformy Azure, aby wyświetlić szczegóły, takie jak osType, lokalizacji, vmSize, vmId, nazwa, resourceGroupName, identyfikator subskrypcji, privateIpAddress i publicznego adresu IP.

Podczas badania wystąpienie metadanych wymaga połączenia sieciowego gościa dobrej kondycji, ponieważ sprawia, że wywołanie interfejsu REST za pośrednictwem hosta platformy Azure do wystąpienia usługi metadanych. Dlatego jeśli jesteś w stanie wykonać zapytania dotyczącego metadanych wystąpienia, które informuje, że gościa jest w stanie komunikować się za pośrednictwem sieci do usługi hostowanej na platformie Azure.

Aby uzyskać więcej informacji, zobacz [Azure Instance Metadata service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Wystąpienie metadanych
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ systemu operacyjnego (Instance Metadata)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Lokalizacja (Instance Metadata)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Rozmiar (Instance Metadata)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>Identyfikator maszyny Wirtualnej (Instance Metadata)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nazwa maszyny Wirtualnej (Instance Metadata)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nazwa grupy zasobów (Instance Metadata)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Identyfikator subskrypcji (Instance Metadata)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tagi (Instance Metadata)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Identyfikator grupy umieszczania (Instance Metadata)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domena błędów platformy (Instance Metadata)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domena aktualizacji platformy (Instance Metadata)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Prywatny adres IP IPv4 (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Adres publiczny adres IP IPv4 (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adres podsieci IPv4 / prefiksu (wystąpienie metadanych)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Adres IP protokołu IPv6 (Instance Metadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adres MAC (Instance Metadata)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Następne kroki
* Znajduje się na stronie dokumentacji Windows głównych konsoli szeregowej [tutaj](serial-console-windows.md).
* Jest również dostępny dla konsoli szeregowej [Linux](serial-console-linux.md) maszyn wirtualnych.
* Dowiedz się więcej o [diagnostykę rozruchu](boot-diagnostics.md).
