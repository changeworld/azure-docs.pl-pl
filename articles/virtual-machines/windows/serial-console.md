---
title: Konsoli szeregowej maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Konsola serial dwukierunkowych maszyn wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: e891e9c9fd87f370f0c98639ff0c6fc5b8cc81af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194407"
---
# <a name="virtual-machine-serial-console-preview"></a>Maszyna wirtualna konsoli szeregowej (wersja zapoznawcza) 


Konsoli szeregowej maszyny wirtualnej na platformie Azure zapewnia dostęp do konsoli tekstowych dla maszyn wirtualnych systemu Linux i Windows. To połączenie szeregowe jest port szeregowy COM1 maszyny wirtualnej i zapewnia dostęp do maszyny wirtualnej i nie są powiązane z siecią maszyny wirtualnej / stan systemu operacyjnego. Dostęp do konsoli szeregowej dla maszyny wirtualnej można uzyskać tylko za pośrednictwem portalu Azure obecnie i dozwolone tylko dla tych użytkowników, którzy mają współautora maszyny Wirtualnej lub powyżej dostęp do maszyny wirtualnej. 

> [!Note] 
> Podglądy są udostępniane użytkownikowi, pod warunkiem że wyrażasz zgodę na warunki użytkowania. Aby uzyskać więcej informacji zobacz [Microsoft Azure uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ta usługa jest obecnie w **publicznej wersji zapoznawczej** i dostęp do konsoli szeregowej dla maszyn wirtualnych jest dostępny dla globalnych regiony platformy Azure. W tym momencie konsoli szeregowej nie jest dostępne chmury Azure dla instytucji rządowych, platformy Azure w Niemczech i Chińskiej wersji platformy Azure.

 

## <a name="prerequisites"></a>Wymagania wstępne 

* Maszyna wirtualna musi mieć [diagnostyki rozruchu](boot-diagnostics.md) włączone 
* Konto, przy użyciu konsoli szeregowej musi mieć [roli współautora](../../role-based-access-control/built-in-roles.md) dla maszyny Wirtualnej i [diagnostyki rozruchu](boot-diagnostics.md) konta magazynu. 

## <a name="open-the-serial-console"></a>Otwieranie konsoli szeregowej
tylko jest dostępny za pośrednictwem konsoli szeregowej dla maszyn wirtualnych [portalu Azure](https://portal.azure.com). Poniżej przedstawiono kroki, aby uzyskać dostępu do konsoli szeregowej dla maszyn wirtualnych za pomocą portalu 

  1. Otwórz Azure portal
  2. W menu po lewej stronie wybierz maszyny wirtualne.
  3. Kliknij maszynę Wirtualną na liście. Zostanie otwarta strona — omówienie dla maszyny Wirtualnej.
  4. Przewiń w dół do obsługi i rozwiązywania problemów z sekcji, a następnie kliknij opcję konsoli szeregowej (wersja zapoznawcza). Nowe okienko z konsoli szeregowej będą otwierane i uruchomić połączenia.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>Wyłącz funkcję
Wyłączenie ustawienia diagnostyki rozruchu tej maszyny Wirtualnej dla określonej maszyny wirtualne można dezaktywować funkcji konsoli szeregowej.

## <a name="serial-console-security"></a>Zabezpieczenia konsoli szeregowej 

### <a name="access-security"></a>Zabezpieczenia dostępu 
Dostęp do konsoli szeregowej jest ograniczony do użytkowników, którzy mają [współautorzy wirtualna](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) lub powyżej dostęp do maszyny wirtualnej. Jeśli uwierzytelnianie wieloskładnikowe wymaga dzierżawę usługi AAD, dostęp do konsoli szeregowej będzie również konieczne MFA, zgodnie z jego dostęp odbywa się za pośrednictwem [portalu Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpieczenia kanału
Wszystkie dane są wysyłane Wstecz i określonymi zaszyfrowane w trakcie przesyłania.

### <a name="audit-logs"></a>Dzienniki inspekcji
Dostęp do konsoli szeregowej jest aktualnie zalogowany [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) dzienniki maszyny wirtualnej. Dostęp do tych dzienników są własnością i kontrolowane przez administratora maszyny wirtualnej platformy Azure.  

>[!CAUTION] 
Gdy żadne hasła dostępu do konsoli usługi nie powodują rejestrowania, jeśli polecenia uruchamiane w ramach konsoli zawierają lub output hasła, klucze tajne, nazwy użytkowników lub inne formy z osobiście informacji osobowych, te będą zapisywane diagnostyki rozruchu maszyny wirtualnej dzienniki, wraz ze wszystkich innych tekstu widoczne, jako część wdrożenia przewijania konsoli szeregowej kopię funkcji. Te dzienniki są cykliczne, a tylko osoby, które mają uprawnienia do odczytu do konta magazynu diagnostyki mają do nich dostępu, jednak zaleca się następujące najlepsze rozwiązanie polegające na przy użyciu pulpitu zdalnego dla wszystkich elementów, które może się wiązać kluczy tajnych i/lub dane osobowe. 

### <a name="concurrent-usage"></a>Współbieżne użycie
Jeśli użytkownik jest połączony z konsolą szeregową i inny użytkownik pomyślnie żąda dostępu do tej samej maszyny wirtualnej, pierwszy użytkownik zostanie odłączony, a drugi użytkownik połączonych w sposób podobnie pierwszy użytkownik, stały i opuszczania konsoli fizycznej i nowy Użytkownik obecne w dół.

>[!CAUTION] 
Oznacza to, że użytkownik, który zostanie rozłączona nie będą rejestrowane! Możliwość wymuszenia wylogowania po rozłączeniu (za pośrednictwem SIGHUP lub podobny mechanizm) jest nadal mapy drogowej. Dla systemu Windows jest automatyczne limitu czasu włączone w jej, jednak dla systemu Linux można skonfigurować ustawienia terminali limitu czasu. 


## <a name="accessing-serial-console-for-windows"></a>Uzyskiwanie dostępu do konsoli szeregowej dla systemu Windows 
Nowsze obrazów systemu Windows Server na platformie Azure mają [specjalnej konsoli administracyjnej](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) domyślnie włączone (jej). JEJ jest obsługiwany w wersjach systemu Windows server, ale nie jest dostępne w wersjach klientów (na przykład systemu Windows 10, Windows 8 lub Windows 7). Aby włączyć konsoli szeregowej dla maszyn wirtualnych systemu Windows utworzonych przy użyciu Feb2018 lub niższy obrazów użyj następujące czynności: 

1. Połącz się z maszyną wirtualną z systemem Windows za pośrednictwem pulpitu zdalnego
2. Z wiersza polecenia z uprawnieniami administracyjnymi uruchom następujące polecenia 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Wykonaj ponowny rozruch systemu dla konsoli jej włączenia

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Jeśli jej uruchomienie można włączyć w trybie offline, również 

1. Dołączenie dysku systemu windows ma jej skonfigurowany dla jako dysku danych do istniejącej maszyny Wirtualnej. 
2. Z wiersza polecenia z uprawnieniami administracyjnymi uruchom następujące polecenia 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Jak sprawdzić, czy jej jest włączony, czy nie 

Jeśli [jej] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nie włączono konsoli szeregowej nie będzie wyświetlany monit o jej. Go może wyświetlać informacje o kondycji maszyny Wirtualnej w niektórych przypadkach lub może być pusta.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Włączanie menu rozruchu do wyświetlenia w konsoli szeregowej 

Jeśli musisz włączyć moduł ładujący rozruchu systemu Windows monituje o do wyświetlenia w konsoli szeregowej, można dodać następujące dodatkowe opcje do moduł ładujący rozruchu systemu Windows.

1. Połącz się z maszyną wirtualną z systemem Windows za pośrednictwem pulpitu zdalnego
2. Z wiersza polecenia z uprawnieniami administracyjnymi, uruchom następujące polecenia 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Wykonaj ponowny rozruch systemu do włączenia menu rozruchu

> [!NOTE] 
> W tym punktu obsługę funkcji kluczy nie jest włączona, jeśli potrzebujesz zaawansowanych opcji rozruchu w systemie bcdedit/set {current} onetimeadvancedoptions, zobacz [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) więcej szczegółów

## <a name="windows-commands---cmd"></a>Polecenia systemu Windows - CMD 

Ta sekcja zawiera przykładowe polecenia do wykonywania typowych zadań w scenariuszach, w których konieczne może być jej umożliwia dostęp do maszyny Wirtualnej, takie jak konieczność Rozwiązywanie problemów z błędami połączenia RDP.

JEJ uwzględniony we wszystkich wersjach systemu Windows, od systemu Windows Server 2003, ale jest domyślnie wyłączona. Zależy od jej `sacdrv.sys` sterownik jądra `Special Administration Console Helper` usługi (`sacsvr`) i `sacsess.exe` procesu. Aby uzyskać więcej informacji, zobacz [narzędzia usług zarządzania awaryjnego i ustawienia](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

JEJ zezwala na łączenie się z systemu operacyjnego za pośrednictwem portu szeregowego. Podczas uruchamiania polecenia CMD z jej, `sacsess.exe` uruchamia `cmd.exe` w ramach Twojego systemu operacyjnego. Widać, że w zadaniu Menedżera Jeśli możesz RDP do maszyny Wirtualnej w tym samym wtedy, gdy są podłączone do jej za pomocą funkcji konsoli szeregowej. CMD dostęp za pośrednictwem jej jest taki sam `cmd.exe` są używane, gdy połączenie za pośrednictwem protokołu RDP. Te same polecenia i narzędzia są dostępne, łącznie z możliwością uruchamiania programu PowerShell z tego wystąpienia CMD. Główna różnica między jej i środowisko odzyskiwania systemu Windows (WinRE) w tym jej jest umożliwienie zarządzania systemu operacyjnego, w którym WinRE jest uruchamiany w innych, minimalny system operacyjny. Gdy maszyny wirtualne platformy Azure nie obsługują możliwość dostępu WinRE, korzystając z funkcji konsoli szeregowej, maszynach wirtualnych platformy Azure mogą być zarządzane za pośrednictwem jej.

Ponieważ jej jest ograniczony do buforu ekranu 80 x 24 z powrotem bez przewijania, Dodaj `| more` poleceń, aby wyświetlić dane wyjściowe o jedną stronę w czasie. Użyj `<spacebar>` do następnej strony, zobacz lub `<enter>` do następnej linii.  

`SHIFT+INSERT` jest Wklej skrót dla okna konsoli szeregowej.

Ze względu na jej buforu ekranu ograniczone dłużej polecenia może być łatwiejsze do wpisywania w edytorze tekstów lokalnych i następnie wklejone do jej.

### <a name="view-and-edit-windows-registry-settings"></a>Umożliwia wyświetlanie i edytowanie ustawień rejestru systemu Windows
#### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy włączono protokołu RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Drugi klucz (w obszarze \Policies) będzie istniało tylko, jeśli skonfigurowano ustawienie zasad grupy w odpowiednich.

#### <a name="enable-rdp"></a>Włącz protokół RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Drugi klucz (w obszarze \Policies) są wymagane tylko jeśli ustawienie zasad grupy w odpowiednich zostało skonfigurowane. Wartość będzie ulegną po następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.

### <a name="manage-windows-services"></a>Zarządzanie usługami systemu Windows

#### <a name="view-service-state"></a>Wyświetl stan usługi
`sc query termservice`
####  <a name="view-service-logon-account"></a>Konto logowania usługi widoku
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Skonfiguruj konto logowania usługi 
`sc config termservice obj= "NT Authority\NetworkService"`

Wymagany jest odstęp po znaku równości.
#### <a name="set-service-start-type"></a>Typ uruchomienia usługi zestawu
`sc config termservice start= demand` 

Wymagany jest odstęp po znaku równości. Start możliwe wartości to `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Definiowanie zależności usługi
`sc config termservice depend= RPCSS`

Wymagany jest odstęp po znaku równości.
#### <a name="start-service"></a>Uruchom usługę
`net start termservice`

lub

`sc start termservice`
#### <a name="stop-service"></a>Zatrzymywanie usługi
`net stop termservice`

lub

`sc stop termservice`
### <a name="manage-networking-features"></a>Zarządzanie funkcjami sieci
#### <a name="show-nic-properties"></a>Pokaż właściwości karty Sieciowej
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>Pokaż właściwości adresu IP
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>Pokazuje konfigurację protokołu IPSec
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Włącz kartę Sieciową
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Ustaw kartę Sieciową do użycia protokołu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Maszyny wirtualne platformy Azure zawsze powinna być skonfigurowana w system operacyjny do używania protokołu DHCP do uzyskiwania adresów IP gościa. Azure statyczne ustawienia IP nadal używa DHCP ma zostać przypisany statyczny adres IP do maszyny Wirtualnej.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Port ping  
Zainstaluj klienta programu telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testowanie łączności

`telnet bing.com 80`

Aby usunąć klienta programu telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Gdy jest ograniczone do metod, które są dostępne w systemie Windows domyślnie, programu PowerShell może być lepszym rozwiązaniem testowania łączności portu. Zobacz sekcję PowerShell poniżej przykłady.
#### <a name="test-dns-name-resolution"></a>Przetestowanie rozpoznawania nazw DNS
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>Pokaż reguły zapory systemu Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Wyłączenie Zapory systemu Windows
`netsh advfirewall set allprofiles state off`

Można to polecenie podczas rozwiązywania problemu tymczasowo wykluczenia zapory systemu Windows. Będzie go można włączyć po następnym ponownym uruchomieniu komputera lub gdy użytkownik enaable za pomocą poniższego polecenia. Nie zostanie zatrzymana usługa Zapora systemu Windows (MPSSVC) lub usługi aparat filtrowania Base (BFE) jako sposób wykluczenia zapory systemu Windows. Zatrzymanie MPSSVC lub BFE spowoduje wszystkich łączności blokowane.
#### <a name="enable-windows-firewall"></a>Włącz Zaporę systemu Windows
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Zarządzaj użytkownikami i grupami
#### <a name="create-local-user-account"></a>Tworzenie konta użytkownika lokalnego
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Dodawanie do lokalnej grupy użytkowników lokalnych
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone.
`net user <username> | find /i "active"`

Maszyny wirtualne platformy Azure, utworzone na podstawie uogólniony obraz będzie mieć konto administratora lokalnego, zmieniona na nazwę określoną podczas inicjowania obsługi maszyny Wirtualnej. Dlatego zazwyczaj nie będzie można ich `Administrator`.
#### <a name="enable-user-account"></a>Włącz konto użytkownika
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`net user <username>`

Przykładowe wiersze płynących z konta administratora lokalnego:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Widok grup lokalnych
`net localgroup`
### <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń systemu Windows
#### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń zapytań
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Zmień `/c:10` na żądaną liczbę zdarzeń, które zwracają lub przenieś go do zwrócenia wszystkich zdarzeń pasujących do wyników filtrowania.
#### <a name="query-event-log-by-event-id"></a>Dziennik zdarzeń zapytań przez identyfikator zdarzenia
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń zapytań przez identyfikator zdarzenia i dostawcy
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy dla ostatnich 24 godzinach
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` do wyszukiwania wstecz 7 dni zamiast 24 godziny.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń zapytań identyfikator zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Wyświetl lub usuń zainstalowanych aplikacji
#### <a name="list-installed-applications"></a>Wyświetlanie listy zainstalowanych aplikacji
`wmic product get Name,InstallDate | sort /r | more`

`sort /r` Sortuje malejąco według Data ułatwia co został niedawno zainstalowany instalacji. Użyj `<spacebar>` aby przejść do następnej strony danych wyjściowych lub `<enter>` można poprawić jeden wiersz.
#### <a name="uninstall-an-application"></a>Odinstalowywanie aplikacji
`wmic path win32_product where name="<name>" call uninstall`

Zastąp `<name>` o nazwie zwracane w poleceniu powyżej dla aplikacji do usunięcia.

### <a name="file-system-management"></a>Zarządzanie systemem plików
#### <a name="get-file-version"></a>Pobierz wersję pliku
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

W tym przykładzie zwraca wersję pliku wirtualnego sterownika karty Sieciowej, który jest netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
#### <a name="scan-for-system-file-corruption"></a>Skanowanie pod kątem uszkodzenie pliku systemowego
`sfc /scannow`

Zobacz też [naprawy w obrazie](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Skanowanie pod kątem uszkodzenie pliku systemowego
`dism /online /cleanup-image /scanhealth`

Zobacz też [naprawy w obrazie](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Uprawnienia do pliku eksportu do pliku tekstowego
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Zapisz uprawnienia do pliku do listy ACL plików
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Przywróć uprawnienia do pliku z listy ACL plików
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Ścieżka, korzystając z `/restore` musi być folder nadrzędny folder określony przy użyciu `/save`. W tym przykładzie `\RSA` jest nadrzędny `\MachineKeys` folder określony w `/save` w powyższym przykładzie.
#### <a name="take-ntfs-ownership-of-a-folder"></a>Przejmowanie na własność NTFS folderu
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Przyznaj uprawnienia NTFS do lokalizacji folderu
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Zarządzanie urządzeniami
#### <a name="remove-non-present-pnp-devices"></a>Usuń z systemem innym niż obecna urządzenia PNP
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>Zarządzanie zasadami grupy
#### <a name="force-group-policy-update"></a>Wymuszanie aktualizacji zasad grupy
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Dodatkowe zadania
#### <a name="show-os-version"></a>Pokaż wersja systemu operacyjnego
`ver`

lub 

`wmic os get caption,version,buildnumber /format:list`

lub 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Data instalacji systemu operacyjnego widoku
`systeminfo | find /i "original"`

lub 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Strefa czasowa widoku
`systeminfo | find /i "time zone"`

lub

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>Ponowne uruchomienie systemu Windows
`shutdown /r /t 0`

Dodawanie `/f` wymusi uruchomionej aplikacji bez ostrzeżenia użytkowników.
#### <a name="detect-safe-mode-boot"></a>Wykryj rozruch w trybie awaryjnym
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Polecenia systemu Windows — PowerShell

Aby uruchomić programu PowerShell w jej, po osiągnięciu wiersz polecenia, wpisz:

`powershell <enter>`

>[!CAUTION]
Usuń moduł PSReadLine z sesji programu PowerShell, przed uruchomieniem innych poleceń programu PowerShell. Jest to znany problem gdzie dodatkowe znaki mogą być wprowadzane w tekście wklejonych ze Schowka, jeśli PSReadLine działa w sesji programu PowerShell w jej.

Najpierw sprawdź, czy załadowano PSReadLine. Jest on załadowany domyślnie w systemie Windows Server 2016, Windows 10 i nowszych wersjach systemu Windows. Tylko jest obecne w starszych wersjach systemu Windows, jeśli została ona ręcznie zainstalowana. 

Jeśli to polecenie zwraca monit z żadnych danych wyjściowych, następnie moduł nie został załadowany i możesz kontynuować korzystanie z sesji programu PowerShell w jej normalnie.

`get-module psreadline`

Jeśli powyższe polecenie zwraca informacje o wersji modułu PSReadLine, uruchom następujące polecenie, aby zwolnić ją. To polecenie nie powoduje rozwiązania ani odinstalować moduł, go tylko zwalnia z bieżącej sesji programu PowerShell.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>Umożliwia wyświetlanie i edytowanie ustawień rejestru systemu Windows
#### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy włączono protokołu RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Drugi klucz (w obszarze \Policies) będzie istniało tylko, jeśli skonfigurowano ustawienie zasad grupy w odpowiednich.
#### <a name="enable-rdp"></a>Włącz protokół RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Drugi klucz (w obszarze \Policies) są wymagane tylko jeśli ustawienie zasad grupy w odpowiednich zostało skonfigurowane. Wartość będzie ulegną po następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.
### <a name="manage-windows-services"></a>Zarządzanie usługami systemu Windows
#### <a name="view-service-details"></a>Wyświetl szczegóły usługi
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` mogą być używane, ale nie zawiera konto logowania usługi. `Get-WmiObject win32-service` wykonuje.
#### <a name="set-service-logon-account"></a>Skonfiguruj konto logowania usługi
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Korzystając z konta usługi innych niż `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, lub `LocalSystem`, podaj hasło konta jako ostatni argument (ósmego) po nazwie konta.
#### <a name="set-service-startup-type"></a>Ustaw typ uruchamiania usługi
`set-service termservice -startuptype Manual`

`Set-service` akceptuje `Automatic`, `Manual`, lub `Disabled` typ uruchomienia.
#### <a name="set-service-dependencies"></a>Definiowanie zależności usługi
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Uruchom usługę
`start-service termservice`
#### <a name="stop-service"></a>Zatrzymywanie usługi
`stop-service termservice`
### <a name="manage-networking-features"></a>Zarządzanie funkcjami sieci
#### <a name="show-nic-properties"></a>Pokaż właściwości karty Sieciowej
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

lub 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` jest dostępna w 2012 +, do użytku 2008R2 `Get-WmiObject`.
#### <a name="show-ip-properties"></a>Pokaż właściwości adresu IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Włącz kartę Sieciową
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

lub

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` jest dostępna w 2012 +, do użytku 2008R2 `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Ustaw kartę Sieciową do użycia protokołu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` jest dostępna na 2012 +. W przypadku 2008R2 użyć `Get-WmiObject`. Maszyny wirtualne platformy Azure zawsze powinna być skonfigurowana w system operacyjny do używania protokołu DHCP do uzyskiwania adresów IP gościa. Azure statyczne ustawienia IP nadal używa DHCP ma zostać przypisany adres IP do maszyny Wirtualnej.
#### <a name="ping"></a>Ping
`test-netconnection`

lub

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez żadnych parametrów spróbuje wykonać polecenie ping `internetbeacon.msedge.net`. Jest ona dostępna na 2012 +. Użyj 2008R2 `Get-WmiObject` tak jak w drugim przykładzie.
#### <a name="port-ping"></a>Polecenie Ping do portu
`test-netconnection -ComputerName bing.com -Port 80`

lub

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` jest dostępna na 2012 +. Do użytku 2008R2 `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>Przetestowanie rozpoznawania nazw DNS
`resolve-dnsname bing.com` 

lub 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` jest dostępna na 2012 +. W przypadku 2008R2 użyć `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Pokaż reguły zapory systemu Windows według nazwy
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>Pokaż reguły zapory systemu Windows, port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

lub

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` jest dostępna na 2012 +. Użyj 2008R2 `hnetcfg.fwpolicy2` obiektu COM. 
#### <a name="disable-windows-firewall"></a>Wyłączenie Zapory systemu Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` jest dostępna na 2012 +. Użyj 2008R2 `netsh advfirewall` jako zawartymi w powyższej sekcji CMD.
### <a name="manage-users-and-groups"></a>Zarządzaj użytkownikami i grupami
#### <a name="create-local-user-account"></a>Tworzenie konta użytkownika lokalnego
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone.
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

lub 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` jest dostępna na 2012 +. W przypadku 2008R2 użyć `Get-WmiObject`. W tym przykładzie pokazano wbudowanego konta administratora lokalnego, który zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure, utworzone na podstawie uogólniony obraz będzie mieć konto administratora lokalnego, zmieniona na nazwę określoną podczas inicjowania obsługi maszyny Wirtualnej. Dlatego zazwyczaj nie będzie można ich `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Dodawanie do lokalnej grupy użytkowników lokalnych
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Włącz konto użytkownika lokalnego
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

W tym przykładzie umożliwia wbudowanego konta administratora lokalnego, który zawsze ma identyfikator SID `S-1-5-21-*-500`. Maszyny wirtualne platformy Azure, utworzone na podstawie uogólniony obraz będzie mieć konto administratora lokalnego, zmieniona na nazwę określoną podczas inicjowania obsługi maszyny Wirtualnej. Dlatego zazwyczaj nie będzie można ich `Administrator`.
#### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

lub 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` jest dostępna na 2012 +. W przypadku 2008R2 użyć `Get-WmiObject`. W tym przykładzie pokazano wbudowanego konta administratora lokalnego, który zawsze ma identyfikator SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Widok grup lokalnych
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` jest dostępna na 2012 +. W przypadku 2008R2 użyć `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>Zarządzanie dziennikiem zdarzeń systemu Windows
#### <a name="query-event-log-errors"></a>Błędy dziennika zdarzeń zapytań
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Zmień `/c:10` na żądaną liczbę zdarzeń, które zwracają lub przenieś go do zwrócenia wszystkich zdarzeń pasujących do wyników filtrowania.
#### <a name="query-event-log-by-event-id"></a>Dziennik zdarzeń zapytań przez identyfikator zdarzenia
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Dziennik zdarzeń zapytań przez identyfikator zdarzenia i dostawcy
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Dziennik zdarzeń zapytania według Identyfikatora zdarzenia i dostawcy dla ostatnich 24 godzinach
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` do wyszukiwania wstecz 7 dni zamiast 24 godziny. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Dziennik zdarzeń zapytań identyfikator zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Wyświetl lub usuń zainstalowanych aplikacji
#### <a name="list-installed-software"></a>Wyświetlanie listy zainstalowanych oprogramowania
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Odinstalowywanie oprogramowania
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Zarządzanie systemem plików
#### <a name="get-file-version"></a>Pobierz wersję pliku
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

W tym przykładzie zwraca informacje o wersji pliku wirtualnego sterownika karty Sieciowej o nazwie netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
#### <a name="download-and-extract-file"></a>Pobierać i wyodrębniać pliku
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ten przykład tworzy `c:\bin` folderu, następnie pobiera i wyodrębnia pakiet Sysinternals narzędzi do `c:\bin`.
### <a name="miscellaneous-tasks"></a>Dodatkowe zadania
#### <a name="show-os-version"></a>Pokaż wersja systemu operacyjnego
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Data instalacji systemu operacyjnego widoku
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Wyświetl czas działania systemu Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Zwraca czas działania jako `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, na przykład `49:16:48:00.00`. 
#### <a name="restart-windows"></a>Ponowne uruchomienie systemu Windows
`restart-computer`

Dodawanie `-force` wymusi uruchomionej aplikacji bez ostrzeżenia użytkowników.
### <a name="instance-metadata"></a>Wystąpienie metadanych

Metadane wystąpienia platformy Azure z można badać w sieci maszyny Wirtualnej platformy Azure, aby wyświetlić szczegółowe informacje, takie jak osType, lokalizacji, vmSize, vmId, nazwa, resourceGroupName, subscriptionId, elementu privateIpAddress i publicznego adresu IP.

Zapytanie dotyczące metadanych wystąpienia wymaga łączności sieciowej gościa dobrej kondycji, ponieważ sprawia, że wywołanie interfejsu REST za pośrednictwem hosta platformy Azure z usługą metadanych wystąpienia. Dlatego jeśli można zbadać metadanych wystąpienia, który informuje o gościa jest w stanie komunikować się za pośrednictwem sieci do usługi hostowanej platformy Azure.

Aby uzyskać więcej informacji, zobacz [usługi Azure wystąpienie metadanych](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Wystąpienie metadanych
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Typ systemu operacyjnego (wystąpienie metadane)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Lokalizacja (wystąpienie metadane)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Rozmiar (wystąpienie metadane)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>Identyfikator maszyny Wirtualnej (wystąpienie metadane)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Nazwa maszyny Wirtualnej (wystąpienie metadane)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Nazwa grupy zasobów (wystąpienie metadane)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>Identyfikator subskrypcji (wystąpienie metadane)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Tagi (wystąpienie metadane)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>Identyfikator grupy umieszczania (wystąpienie metadane)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Domena błędów platformy (wystąpienie metadane)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Domena aktualizacji platformy (wystąpienie metadane)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>Prywatny adres IP IPv4 (wystąpienie metadane)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>Adres IPv4 publicznego adresu IP (wystąpienie metadane)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adres podsieci IPv4 / prefiksu (wystąpienie metadanych)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>Adres IPv6 (wystąpienie metadanych)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>Adres MAC (wystąpienie metadane)
`$im.network.interface.macAddress`

## <a name="errors"></a>Błędy
Przejściowego charakter i ponowienie próby adresu połączenia te są większość błędów. Poniższej tabeli przedstawiono listę błędów i środki zaradcze 

Błąd                            |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla "<VMNAME>". Aby używać konsoli szeregowej, upewnij się, że diagnostyki rozruchu jest włączone dla tej maszyny Wirtualnej. | Upewnij się, że maszyna wirtualna ma [diagnostyki rozruchu](boot-diagnostics.md) włączone. 
Maszyna wirtualna jest w stanie zatrzymania, deallocated. Uruchom maszynę Wirtualną i ponów próbę połączenia konsoli szeregowej. | Maszyna wirtualna musi być w stanie uruchomionym, dostęp do konsoli szeregowej
Nie masz uprawnień wymaganych do używania konsoli szeregowej maszyny Wirtualnej. Upewnij się, musisz mieć co najmniej uprawnienia roli współautora maszyny Wirtualnej.| Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu "<STORAGEACCOUNTNAME>". Sprawdź, czy włączono diagnostyki rozruchu dla tej maszyny Wirtualnej i czy masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga określonych uprawnień dostępu. Zobacz [wymagania związane z dostępem](#prerequisites) Aby uzyskać więcej informacji

## <a name="known-issues"></a>Znane problemy 
Firma Microsoft są nadal w fazie Podgląd dostępu do konsoli szeregowej, firma Microsoft pracy za pośrednictwem znane problemy, poniżej znajdują się na liście z możliwe obejścia 

Problem                           |   Środki zaradcze 
:---------------------------------|:--------------------------------------------|
Nie jest dostępna opcja z konsoli szeregowej wystąpienia zestawu skali maszyny wirtualnej | W czasie w wersji zapoznawczej dostęp do konsoli szeregowej dla wystąpień zestawu skali maszyny wirtualnej nie jest obsługiwane.
Naciśnięcie klawisza enter po transparent połączenia nie są wyświetlane dziennika w wierszu | [Naciśnięcie wprowadź nie działa](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Tylko informacje o kondycji jest wyświetlany podczas nawiązywania połączenia z maszyną wirtualną systemu Windows| [Sygnały kondycji systemu Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Nie można wpisać w jej monit, jeśli włączone jest debugowanie jądra | RDP maszyny Wirtualnej i uruchom `bcdedit /debug {current} off` z wiersza polecenia o podniesionych uprawnieniach. Jeśli nie możesz RDP można zamiast tego dołączyć dysk systemu operacyjnego do innej maszyny Wirtualnej platformy Azure i zmodyfikuj go podczas podłączyć jako dysku danych przy użyciu `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, następnie zamienić ponownie dysk.

## <a name="frequently-asked-questions"></a>Często zadawane pytania 
**Q. Jak można wysłać opinię?**

A. Wyrazić swoją opinię jako rozwiązaniem problemu, przechodząc do https://aka.ms/serialconsolefeedback. Możesz też mniej (preferowane) Prześlij opinię za pośrednictwem azserialhelp@microsoft.com lub w kategorii maszyny wirtualnej http://feedback.azure.com 

**Q. Otrzymuję komunikat o błędzie "powodujące konflikt typu systemu operacyjnego"Windows"żądanego typu systemu operacyjnego Linux ma istniejące konsoli?**

A. Jest to znany problem można rozwiązać ten problem, po prostu otworzyć [powłoki chmury Azure](https://docs.microsoft.com/azure/cloud-shell/overview) w trybie bash i ponów próbę.

**Q. Nie mogę uzyskać dostępu do konsoli szeregowej, w którym plik sprawy pomocy technicznej?**

A. Ta funkcja podglądu jest objęte za pośrednictwem warunki dotyczące usługi Azure. Obsługa tej powinni obsłużyć kanałami wymienionych powyżej. 

## <a name="next-steps"></a>Kolejne kroki
* Jest również dostępny do konsoli szeregowej [Linux](../linux/serial-console.md) maszyny wirtualne
* Dowiedz się więcej o [bootdiagnostics](boot-diagnostics.md)
