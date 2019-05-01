---
title: Rozwiązywanie problemów z błędami instalacji wypychanej usługi mobilności, podczas włączania replikacji na potrzeby odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z błędami instalacji usługi mobilności, podczas włączania replikacji na potrzeby odzyskiwania po awarii
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 58c09c71aad2b6244f6e2f3d144c033665932f50
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925574"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności

Instalacja usługi mobilności jest krokiem podczas włączania replikacji. Powodzenie ten krok zależy wyłącznie spełnienie wymagań wstępnych i pracą z nimi przy użyciu obsługiwanych konfiguracji. Są najbardziej typowych błędów, stosowanych podczas instalacji usługi mobilności ze względu na:

* [Poświadczenie/uprawnień błędy](#credentials-check-errorid-95107--95108)
* [Niepowodzenia logowania](#login-failures-errorid-95519-95520-95521-95522)
* [Błędy łączności](#connectivity-failure-errorid-95117--97118)
* [Błędy udostępniania plików i drukarek](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Błędy usługi WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nieobsługiwanych systemów operacyjnych](#unsupported-operating-systems)
* [Nieobsługiwane konfiguracje rozruchu](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Błędy instalacji usługi VSS](#vss-installation-failures)
* [Nazwa urządzenia w konfiguracji programu GRUB zamiast identyfikatora UUID urządzenia](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Wolumin LVM](#lvm-support-from-920-version)
* [Ponowne uruchomienie ostrzeżenia](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Po włączeniu replikacji usługa Azure Site Recovery próbuje wypchnąć zainstalować agenta usługi mobilności na maszynie wirtualnej. W ramach tego serwera konfiguracji próbuje nawiązywanie połączenia z maszyną wirtualną i kopiowanie agenta. Aby umożliwić pomyślną instalację, postępuj zgodnie z wskazówki dotyczące rozwiązywania problemów krok po kroku przedstawionych poniżej.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdź poświadczenia (identyfikator błędu: 95107 & 95108)

* Sprawdź, czy konto użytkownika podczas. Włączanie replikacji **prawidłowy, dokładne**.
* Usługa Azure Site Recovery wymaga **głównego** konto lub konto użytkownika za pomocą **uprawnień administratora** do przeprowadzenia instalacji wypychanej. W przeciwnym wypadku zostanie zablokowane instalację wypychaną na maszynie źródłowej.
  * For Windows (**błąd 95107**), sprawdź, czy konto użytkownika ma dostęp administracyjny lokalnego lub domeny, na maszynie źródłowej.
  * Jeśli nie używasz konta domeny, należy wyłączyć kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.
    * Aby wyłączyć kontrolę dostępu użytkowników zdalnych, w kluczu rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Dodaj nową wartość typu DWORD: LocalAccountTokenFilterPolicy. Ustaw wartość na 1. Aby wykonać ten krok, uruchom następujące polecenie w wierszu polecenia:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Dla systemu Linux (**błąd 95108**), musisz wybrać konto główne dla pomyślnej instalacji agenta mobilności. Ponadto usługi SFTP powinny być uruchomione. Aby włączyć SFTP podsystem i uwierzytelnianie hasłem w pliku sshd_config:
    1. Zaloguj się jako użytkownik główny.
    2. Przejdź do pliku /etc/ssh/sshd_config i znajdź wiersz, który rozpoczyna się od PasswordAuthentication.
    3. Usuń znaczniki komentarza i zmień wartość na Tak.
    4. Znajdź wiersz, który rozpoczyna się od podsystemu, a następnie usuń znaczniki komentarza.
    5. Uruchom ponownie usługę sshd.

Jeśli chcesz zmodyfikować poświadczeń konta wybranego użytkownika, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Niewystarczające uprawnienia błąd (identyfikator błędu: 95517)

Po użytkownik, o których zainstalowano agenta mobilności nie ma uprawnień administratora, konfiguracji proces serwera/skalowalnego w poziomie serwer nie może skopiować oprogramowanie agenta mobilności na maszynie źródłowej. Tak ten błąd jest wynikiem błędu odmowy dostępu. Upewnij się, że konto użytkownika ma uprawnienia administratora.

Jeśli chcesz zmodyfikować poświadczeń konta wybranego użytkownika, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Niewystarczające uprawnienia błąd (identyfikator błędu: 95518)

W przypadku awarii podczas próby zarejestrowania się na maszynie źródłowej ustanowienia relacji zaufania domeny, między domeny głównej i stacji roboczej instalacji agenta mobilności kończy się niepowodzeniem 95518. Identyfikator błędu. Tak upewnij się, że konto użytkownika używane do instalowania agenta mobilności ma uprawnienia administracyjne, aby zalogować się przy użyciu domeny podstawowej maszyny źródłowej.

Jeśli chcesz zmodyfikować poświadczeń konta wybranego użytkownika, postępuj zgodnie z instrukcjami [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Niepowodzenia logowania (identyfikator błędu: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Poświadczenia konta użytkownika zostały wyłączone (identyfikator błędu: 95519)

Wyłączono konto użytkownika, podczas włączania replikacji. Aby włączyć konto użytkownika, można znaleźć w artykule [tutaj](https://aka.ms/enable_login_user) lub uruchom następujące polecenie, zastępując tekst *username* nazwą rzeczywistego użytkownika.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Poświadczenia zablokowane z powodu wielu nieudanych prób logowania (identyfikator błędu: 95520)

Wiele zakończonych niepowodzeniem ponownych prób działań mających na celu dostęp maszyny zostanie zablokowane konta użytkownika. Błędu mogą być następujące:

* Poświadczenia podane podczas instalacji konfiguracji są nieprawidłowe lub
* Konto użytkownika, podczas włączania replikacji jest nieprawidłowy

Dlatego modyfikować poświadczeń wybranego przez zgodnie z instrukcjami [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) i spróbuj ponownie wykonać operację po pewnym czasie.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Serwerów logowania nie są dostępne na maszynie źródłowej (identyfikator błędu: 95521)

Ten błąd występuje, gdy serwerów logowania nie są dostępne na maszynie źródłowej. Niedostępności serwerów logowania doprowadzi do błędu żądanie logowania i w związku z tym nie można zainstalować agenta mobilności. Pomyślne logowanie upewnij się, że serwery logowania są dostępne na maszynie źródłowej, a następnie uruchom usługę logowania. Szczegółowe instrukcje można znaleźć w temacie KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Brak dostępnych obecnie bez logowania serwerów.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Usługa logowania nie jest uruchomiona na maszynie źródłowej (identyfikator błędu: 95522)

Usługa logowania nie jest uruchomiona na maszynie źródłowej i powodowała błąd żądanie logowania. Dlatego nie można zainstalować agenta mobilności. Aby rozwiązać problem, upewnij się, że usługa logowania jest uruchomiona na maszynie źródłowej dla pomyślnego logowania. Aby uruchomić usługę logowania, uruchom polecenie "net start logowania" w wierszu polecenia lub uruchom usługę "NetLogon" z poziomu Menedżera zadań.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Błąd łączności (identyfikator błędu: 95117 & 97118)**

Serwer konfiguracji / serwera przetwarzania skalowalnego w poziomie próbuje połączyć się ze źródłem maszynę Wirtualną, aby zainstalować agenta mobilności. Ten błąd występuje, gdy maszyna źródłowa jest nieosiągalna ze względu na problemy z połączeniem sieciowym. Aby rozwiązać problem,

* Upewnij się, że możesz wysłać polecenie ping do komputera źródłowego z serwera konfiguracji. Jeśli serwera przetwarzania skalowalnego w poziomie zostały wybrane podczas. Włączanie replikacji, upewnij się, że jesteś w stanie wysłać polecenie ping z serwera przetwarzania maszyna źródłowa.
  * Z wiersza polecenia komputera serwera źródłowego, użyj Telnet, aby wykonać polecenie ping do serwera konfiguracji / skalowalny w poziomie serwera przetwarzania za pomocą portu https (135), jak pokazano poniżej, aby zobaczyć, jeśli istnieją problemy z połączeniem sieciowym lub problemy z blokowaniem portów zapory.

     `telnet <CS/ scale-out PS IP address> <135>`
* Ponadto w przypadku **maszyny Wirtualnej systemu Linux**,
  * Sprawdź, czy są zainstalowane najnowsze pakiety openssh, openssh-server i openssl.
  * Sprawdź i upewnij się, że Secure Shell (SSH) jest włączona i działa na porcie 22.
  * SFTP usługi powinny być uruchomione. Aby włączyć SFTP podsystem i uwierzytelnianie hasłem w pliku sshd_config
    * Zaloguj się jako użytkownik główny.
    * Przejdź do pliku /etc/ssh/sshd_config i znajdź wiersz, który rozpoczyna się od PasswordAuthentication.
    * Usuń znaczniki komentarza i zmień wartość na tak
    * Znajdź wiersz, który rozpoczyna się od podsystemu i usuń znaczniki komentarza
    * Uruchom ponownie usługę sshd.
* Próba połączenia może nie powiodło się jeśli nie będzie odpowiednie odpowiedzi po upływie określonego czasu lub ustanowionego połączenia nie powiodło się, ponieważ połączony host nie odpowiedział.
* Łączność / / domeny sieciowej może być problemu związanego z. Można również ze względu na nazwę DNS, rozwiązywania problemów lub problem wyczerpanie portów TCP. Sprawdź, czy istnieją znane problemy w domenie.

## <a name="connectivity-failure-errorid-95523"></a>Błąd łączności (identyfikator błędu: 95523)

Ten błąd występuje, gdy w sieci, w której znajduje się na maszynie źródłowej nie można odnaleźć lub została usunięta lub nie jest już dostępna. Jest jedynym sposobem, aby naprawić błąd, zapewniając, że sieć istnieje.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzanie usług udostępniania plików i drukarek (identyfikator błędu: 95105 & 95106)

Po sprawdzenie łączności Sprawdź, czy na maszynie wirtualnej włączono usługi udostępniania plików i drukarek. Te ustawienia są wymagane, aby skopiować agenta mobilności na maszynie źródłowej.

Aby uzyskać **windows 2008 R2 i wcześniejsze wersje**,

* Aby włączyć udostępnianie plików i drukarek przez zaporę Windows
  * Otwórz panel sterowania -> System i Zabezpieczenia -> zapory Windows -> w okienku po lewej stronie, kliknij przycisk Zaawansowane ustawienia -> w drzewie konsoli kliknij pozycję reguły ruchu przychodzącego.
  * Znajdź reguły pliku i udostępnianie drukarki (sesji NB — ruch przychodzący) i udostępnianie plików i drukarek (ruch przychodzący SMB). Dla każdej reguły, kliknij prawym przyciskiem myszy regułę, a następnie kliknij przycisk **Włącz regułę**.
* Aby włączyć udostępnianie za pomocą zasad grupy plików
  * Przejdź do ekranu startowego, wpisz polecenie gpmc.msc i wyszukiwania.
  * W okienku nawigacji otwórz następujące foldery: Lokalne zasady komputera, Konfiguracja użytkownika, Szablony administracyjne, Windows, składników i udostępnianie w sieci.
  * W okienku szczegółów kliknij dwukrotnie **uniemożliwić użytkownikom udostępnianie plików w swoim profilu**. Aby wyłączyć ustawienia zasad grupy, a następnie włącz możliwość jego udostępniania plików, kliknij przycisk wyłączone. Kliknij przycisk OK, aby zapisać zmiany. Aby dowiedzieć się więcej, zobacz [włączyć lub wyłączyć udostępnianie plików za pomocą zasad grupy](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Aby uzyskać **nowsze wersje**, postępuj zgodnie z instrukcjami podanymi w [zainstalować usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych](vmware-azure-install-mobility-service.md) umożliwiające udostępnianie plików i drukarek.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Sprawdzenie konfiguracji Instrumentacji zarządzania Windows (WMI) (kod błędu: 95103)

Po sprawdzić usług plików i drukarek, należy włączyć usługi WMI dla profilów prywatnej, publicznej i domeny przez zaporę. Te ustawienia są wymagane do ukończenia zdalne wykonywanie kodu na maszynie źródłowej. Aby włączyć,

* Przejdź do panelu sterowania, kliknij pozycję zabezpieczenia, a następnie kliknij zaporę Windows.
* Kliknij przycisk Zmień ustawienia, a następnie kliknij kartę Wyjątki.
* W oknie wyjątki zaznacz pole wyboru dla Windows Management Instrumentation (WMI) aby umożliwić ruch usługi WMI przez zaporę. 

Można również włączyć ruch usługi WMI przez zaporę, w tym celu w wierszu polecenia. Użyj następującego polecenia `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Inne artykuły dotyczące rozwiązywania problemów WMI można znaleźć w następujących artykułach.

* [Podstawowe testy usługi WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z usługą WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Rozwiązywanie problemów ze skryptami WMI i usługi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nieobsługiwanych systemów operacyjnych

Inny najbardziej typową przyczyną błędu może być spowodowany nieobsługiwany system operacyjny. Upewnij się, że używasz obsługiwanej wersji jądra systemu operacyjnego/pomyślną instalację usługi mobilności. Należy unikać użycia prywatnych poprawki.
Aby wyświetlić listę systemów operacyjnych i wersji jądra obsługiwanych przez usługę Azure Site Recovery, zobacz nasze [dokumencie macierz obsługi](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Obsługiwane konfiguracje dysków rozruchowych (identyfikator błędu: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Partycje rozruchowe i systemowe woluminy nie są tego samego dysku (identyfikator błędu: 95309)

Przed 9.20 partycje wersji, rozruchowy i systemowy / woluminy na dyskach inną was nieobsługiwaną konfigurację. Z [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana. Obsługa jest możliwa, należy używać najnowszej wersji.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Nie jest dyskiem rozruchowym (identyfikator błędu: 95310)

Nie można chronić maszyny wirtualnej bez dysku rozruchowego. To, aby zapewnić sprawne odzyskiwanie maszyny wirtualnej podczas operacji trybu failover. Brak dysku rozruchowego powoduje niepowodzenie Przeprowadź rozruch komputera, po włączeniu trybu failover. Upewnij się, że maszyna wirtualna zawiera dysk rozruchowy, a następnie spróbuj ponownie wykonać operację. Należy również zauważyć, że wiele dysków rozruchowych na tym samym komputerze nie jest obsługiwany.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Wiele dysków rozruchowych znajduje się na maszynie źródłowej (identyfikator błędu: 95311)

Nie jest maszyną wirtualną z wieloma dyskami rozruchowymi [obsługiwana konfiguracja](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partycja systemowa na wielu dyskach (identyfikator błędu: 95313)

Przed wersją 9.20 główny partycji lub woluminie, na wielu dyskach był nieobsługiwaną konfigurację. Z [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana. Obsługa jest możliwa, należy używać najnowszej wersji.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Włączanie ochrony nie powiodło się, jak nazwa urządzenia, wymienione w konfiguracji programu GRUB zamiast identyfikatora UUID (identyfikator błędu: 95320)

**Możliwa przyczyna:** </br>
Pliki konfiguracji programu GRUB ("/ boot/grub/menu.lst", "/ boot/grub/grub.cfg", "/ boot/grub2/grub.cfg" lub "/ etc/domyślne/chodników") może zawierać wartości dla parametrów **głównego** i **wznowić** jako nazwy rzeczywistego urządzenia zamiast identyfikatora UUID. Usługa Site Recovery określającemu UUID podejście, jak nazwa urządzenia mogą ulec zmianie między ponowny rozruch maszyny wirtualnej jako maszyny Wirtualnej może nie wróć w górę o takiej samej nazwie w trybie failover powodujące problemy. Na przykład: </br>


- Następujący wiersz jest z pliku programu GRUB **/boot/grub2/grub.cfg**. <br>
  *Linux /boot/vmlinuz-3.12.49-11-default **główny = / dev/sda2** ${extra_cmdline} **wznowić = / dev/sda1** splash = dyskretnej showopts cichy*


- Następujący wiersz jest z pliku programu GRUB **/boot/grub/menu.lst**
   */boot/vmlinuz-3.0.101-63-default jądra **główny = / dev/sda2** **wznowić = / dev/sda1 ** splash = dyskretnej crashkernel = 256M-:128M showopts vga = 0x314*

Jeśli zauważysz bold ciągu powyżej, program GRUB zawiera rzeczywistego urządzenia nazwy parametrów "root" i "resume" zamiast identyfikatora UUID.
 
**Jak naprawić:**<br>
Nazwy urządzeń należy zastąpić je klasą odpowiedni identyfikator UUID.<br>


1. Znajdź identyfikator UUID urządzenia, wykonując polecenie "blkid \<nazwa urządzenia >". Na przykład:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Teraz Zastąp nazwy urządzenia za pomocą jego identyfikatora UUID w formacie, takich jak "główny = UUID =\<UUID >". Na przykład, jeśli firma Microsoft Zastąp nazwy urządzenia o identyfikatorze UUID dla głównego i wznowić parametrów wymienionych powyżej w plikach "/ boot/grub2/grub.cfg", "/ boot/grub2/grub.cfg" lub "/ etc/domyślne/chodników: wierszy w plikach wyglądać. <br>
   *jądra /boot/vmlinuz-3.0.101-63-default **główny = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **wznowić = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash = dyskretnej crashkernel = 256M-:128M showopts vga = 0x314*
3. Ponownie uruchom ochronę ponownie

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalowanie usługi mobilności Ukończono z ostrzeżeniem o ponownym uruchomieniu (identyfikator błędu: 95265 & 95266)

Usługę mobilności usługi Site Recovery ma wiele składników, z których jedno nosi nazwę sterownika filtru. Sterownik filtru pobiera ładowane do pamięci systemowej tylko na czas ponownego uruchomienia systemu. Oznacza to, że poprawki sterownika filtru tylko można realizować po załadowaniu nowego sterownika filtru; który może się zdarzyć tylko w momencie ponownego uruchomienia systemu.

**Należy pamiętać,** to ostrzeżenie, która istniejącą mapowanie replikację będzie działać nawet po zakończeniu nowa aktualizacja agenta. Można ponownie uruchomić w dowolnym momencie w celu uzyskania korzyści z nowego sterownika filtru, ale użytkownik nie należy ponownie uruchomić stare przechowuje sterownika filtru na temat pracy. Tak, po aktualizacji bez ponownego uruchomienia, oprócz sterownik filtru **zalety innych ulepszeń i poprawek w ramach usługi mobilności pobiera rzędu milionów dolarów**. Dlatego, chociaż jest to zalecane, nie jest wymagane ponowne uruchomienie po każdym uaktualnieniu. Informacje na temat gdy wymagane jest ponowne uruchomienie można ustawić [ponowne uruchomienie maszyny źródłowej po uaktualnieniu agenta mobilności ](https://aka.ms/v2a_asr_reboot) sekcji w aktualizacji usługi w usłudze Azure Site Recovery.

> [!TIP]
>Aby uzyskać najlepsze rozwiązania dotyczące planowania uaktualnienia podczas okna obsługi, zobacz [Obsługa najnowszej wersji systemu operacyjnego/jądra](https://aka.ms/v2a_asr_upgrade_practice) w aktualizacji usługi w usłudze Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Obsługa LVM 9.20 wersji

Przed wersją 9.20 LVM była obsługiwana dla tylko dysków z danymi. / Boot powinny znajdować się na partycji dysku i nie być woluminem LVM.

Z [9.20 wersji](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [dysku systemu operacyjnego na LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) jest obsługiwana. Obsługa jest możliwa, należy używać najnowszej wersji.

## <a name="insufficient-space-errorid-95524"></a>Brak wystarczającej ilości miejsca (identyfikator błędu: 95524)

Jeśli agenta mobilności jest kopiowany na maszynie źródłowej, wymagana jest co najmniej 100 MB wolnego miejsca. Tak upewnij się, że maszyna źródłowa ma wymagana ilość wolnego miejsca i spróbuj ponownie wykonać operację.

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi VSS jest częścią instalacji agenta mobilności. Ta usługa jest używana podczas generowania punktów odzyskiwania zapewniających spójność aplikacji. Błędy podczas instalacji usługi VSS, może wystąpić z kilku powodów. Aby zidentyfikować dokładną błędy, zapoznaj się **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Kilka typowych błędów i kroki rozwiązania zostaną wyróżnione w poniższej sekcji.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd VSS-2147023170 [0x800706BE] - kod zakończenia 511

Ten problem występuje najczęściej, gdy oprogramowanie antywirusowe blokuje operacje usługi Azure Site Recovery. Aby rozwiązać ten problem:

1. Wyklucz wszystkie foldery, o których wspomniano [tutaj](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Postępuj zgodnie z wytycznymi opublikowanych przez dostawcą oprogramowania antywirusowego w taki sposób, aby odblokować rejestracja biblioteki DLL w Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd VSS 7 [0x7] - kod zakończenia 511

To jest błąd w czasie wykonywania i wynika z powodu braku pamięci, aby zainstalować usługę VSS. Upewnij się, że Zwiększ ilość miejsca na dysku w przypadku pomyślnego wykonania tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd VSS-2147023824 [0x80070430] - kod zakończenia 517

Ten błąd występuje, gdy usługa dostawcy usługi VSS usługi Azure Site Recovery jest [oznaczone do usunięcia](https://msdn.microsoft.com/library/ms838153.aspx). Spróbuj ręcznie zainstalować usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd VSS-2147023841 [0x8007041F] - 512. kod zakończenia

Ten błąd występuje, gdy baza danych usługi Dostawca VSS usługi Azure Site Recovery jest [zablokowane](https://msdn.microsoft.com/library/ms833798.aspx). Spróbuj ręcznie zainstalować usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Kod 806 zakończenia usługi VSS

Ten błąd występuje, gdy konto użytkownika używane do instalacji nie ma uprawnień do wykonania polecenia CSScript. Zapewniają wystarczających uprawnień do konta użytkownika do wykonywania skryptu i spróbuj ponownie wykonać operację.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy usługi VSS na maszynie źródłowej, uruchamiając następujące polecenie w wierszu

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Błąd VSS - 0x8004E00F

Ten błąd zazwyczaj można napotkać podczas instalacji agenta mobilności ze względu na problemy w modelu DCOM i DCOM jest w stanie krytycznym.

Użyj poniższej procedury w celu ustalenia przyczyny błędu.

**Sprawdź dzienniki instalacji**

1. Otwórz dziennik instalacji znajdujący się w c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Obecność następującego błędu wskazuje ten problem:

    Wyrejestrowywanie istniejącej aplikacji...  Tworzenie obiektu katalogu pobieranie kolekcji aplikacji 

    BŁĄD:

    - Kod błędu:-2147164145 [0x8004E00F]
    - Kod zakończenia: 802

Aby rozwiązać ten problem:

Skontaktuj się z pomocą [zespół platformy Microsoft Windows](https://aka.ms/Windows_Support) uzyskać pomoc w rozwiązywaniu problemu modelu DCOM.

Po usunięciu problemu modelu DCOM, należy ponownie zainstalować dostawcę usługi Azure Site Recovery VSS ręcznie przy użyciu następującego polecenia:
 
**C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent > "C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Jeśli spójność aplikacji nie jest krytyczna dla wymagań dotyczących odzyskiwania po awarii, można pominąć instalację dostawcy usługi VSS. 

Aby pominąć instalację dostawcy usługi VSS usługi Azure Site Recovery i ręcznie zainstalować dostawcę usługi VSS usługi Azure Site Recovery po instalacji:

1. Instalowanie usługi mobilności. 
   > [!Note]
   > 
   > Instalacja zakończy się niepowodzeniem na etapie "Poinstalacyjne configuration". 
2. Aby pominąć instalację usługi VSS:
   1. Otwórz znajdujący się w katalogu instalacji usługi mobilności Azure Site Recovery:
   
      C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent
   2. Zmodyfikowanie skryptów instalacji dostawcy usługi VSS usługi Azure Site Recovery **nMageVSSProvider_Install** i **InMageVSSProvider_Uninstall.cmd** zawsze została wykonana pomyślnie, dodając następujące wiersze:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Ręcznie uruchom ponownie instalację agenta mobilności. 
4. Jeśli instalacja zakończy się pomyślnie i przechodzi do następnego kroku **Konfiguruj**, Usuń wiersze dodane.
5. Aby zainstalować dostawcę usługi VSS, otwórz wiersz polecenia jako Administrator i uruchom następujące polecenie:
   
    **C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. Sprawdź, czy dostawca VSS usługi ASR jest zainstalowany jako usługi w usługach Windows i Otwórz program MMC usługi składnika, aby sprawdzić, czy dostawca VSS usługi ASR jest wyświetlana.
10. Jeśli instalowanie dostawcy usługi VSS w dalszym ciągu zakończyć się niepowodzeniem i pracować z CX, aby naprawić błędy uprawnień w CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalacja dostawcy VSS nie powiedzie się z powodu usługa klastrowania jest włączony na maszynie bez klastra

Ten problem powoduje niepowodzenie podczas wykonywania kroku instalacji dostawcy usługi VSS RecoveryR witryny ASAzure ze względu na problem z modelu COM +, która zapobiega instalacji dostawcy usługi VSS instalacji agenta mobilności Azure Site Recovery.
 
### <a name="to-identify-the-issue"></a>Aby zidentyfikować problem

W dzienniku znajduje się na serwerze konfiguracji w C:\ProgramData\ASRSetupLogs\UploadedLogs\<daty / godziny > UA_InstallLogFile.log, można znaleźć następującego wyjątku:

COM + nie może komunikować się z Microsoft Distributed Transaction Coordinator (wyjątek od HRESULT: 0x8004E00F)

Aby rozwiązać ten problem:

1.  Sprawdź, czy ten komputer jest maszyny bez klastra i czy składniki klastra nie są używane.
3.  Jeśli składniki nie są używane, należy usunąć składniki klastra, na tej maszynie.

## <a name="drivers-are-missing-on-the-source-server"></a>Sterowniki są nieobecne na serwerze źródłowym

W przypadku niepowodzenia instalacji agenta mobilności, sprawdź dzienniki w obszarze C:\ProgramData\ASRSetupLogs Aby ustalić, czy niektóre sterowniki wymagane w niektórych zestawach sterowania.
 
Aby rozwiązać ten problem:
  
1. Za pomocą Edytora rejestru, takie jak regedit.msc Otwórz rejestr.
2. Otwórz węzeł HKEY_LOCAL_MACHINE\SYSTEM.
3. W węźle SYSTEM zlokalizować formantu zestawów.
4. Otwórz każdy zestaw formantów i sprawdź, czy występują następujące sterowniki Windows:

   - ATAPI
   - Vmbus
   - storflt
   - storvsc
   - Intelide
 
Ponownie zainstaluj wszystkie brakujące sterowniki.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.
