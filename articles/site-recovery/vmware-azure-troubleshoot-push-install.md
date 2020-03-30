---
title: Rozwiązywanie problemów z instalacją wypychaną usługi mobilności za pomocą usługi Azure Site Recovery f
description: Rozwiązywanie problemów z błędami instalacji usług mobilności podczas włączania replikacji do odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953773"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Rozwiązywanie problemów z instalacją wypychania usługi mobilności 

Instalacja usługi mobilności jest kluczowym krokiem podczas włączania replikacji. Powodzenie tego kroku zależy wyłącznie od spełnienia wymagań wstępnych i pracy z obsługiwanych konfiguracji. Najczęstsze błędy, z którymi napotykasz podczas instalacji usługi mobilności, to:

* [Błędy poświadczeń/uprawnień](#credentials-check-errorid-95107--95108)
* [Błędy logowania](#login-failures-errorid-95519-95520-95521-95522)
* [Błędy łączności](#connectivity-failure-errorid-95117--97118)
* [Błędy udostępniania plików i drukarek](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Awarie w ucho](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nieobsługiwały się systemy operacyjne](#unsupported-operating-systems)
* [Nieobsługiwane konfiguracje rozruchu](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Błędy instalacji usługi VSS](#vss-installation-failures)
* [Nazwa urządzenia w konfiguracji GRUB zamiast identyfikatora UUID urządzenia](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Wolumin LVM](#lvm-support-from-920-version)
* [Ostrzeżenia o ponownym uruchomieniu](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Po włączeniu replikacji usługa Azure Site Recovery próbuje wypchnąć agenta usługi mobilności instalacji na maszynie wirtualnej. W ramach tego serwera konfiguracji próbuje połączyć się z maszyną wirtualną i skopiować agenta. Aby umożliwić pomyślną instalację, postępuj zgodnie ze wskazówkami dotyczącymi rozwiązywania problemów krok po kroku podanymi poniżej.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdzanie poświadczeń (identyfikator błędu: 95107 & 95108)

* Sprawdź, czy konto użytkownika wybrane podczas włączania replikacji jest **prawidłowe, dokładne**.
* Usługa Azure Site Recovery wymaga konta **ROOT** lub konta użytkownika z **uprawnieniami administratora** do przeprowadzenia instalacji wypychanej. W przeciwnym razie instalacja wypychania zostanie zablokowana na komputerze źródłowym.
  * W przypadku systemu Windows **(błąd 95107)** sprawdź, czy konto użytkownika ma dostęp administracyjny, lokalny lub domenowy, na komputerze źródłowym.
  * Jeśli nie używasz konta domeny, musisz wyłączyć kontrolę dostępu użytkownika zdalnego na komputerze lokalnym.
    * Aby wyłączyć zdalną kontrolę dostępu użytkownika, w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key, dodaj nowy klucz rejestru DWORD: LocalAccountTokenFilterPolicy. Jako wartość wpisz 1. Aby wykonać ten krok, uruchom następujące polecenie z wiersza polecenia:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Dla Linuksa **(błąd 95108**), należy wybrać konto główne dla pomyślnej instalacji agenta mobilności. Ponadto usługi SFTP powinny być uruchomione. Aby włączyć uwierzytelnianie podsystemu SFTP i hasła w pliku sshd_config:
    1. Zaloguj się jako użytkownik root.
    2. Przejdź do pliku /etc/ssh/sshd_config, znajdź wiersz, który zaczyna się od PasswordAuthentication.
    3. Odkomentuj wiersz i zmień wartość na tak.
    4. Znajdź wiersz, który zaczyna się od podsystemu i odkomentuj wiersz.
    5. Uruchom ponownie usługę sshd.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Niewystarczające uprawnienia błąd (ErrorID: 95517)

Jeśli użytkownik wybrany do zainstalowania agenta mobilności nie ma uprawnień administratora, serwer konfiguracji/serwer procesów skalowanych w poziomie nie będzie mógł kopiować oprogramowania agenta mobilności na komputerze źródłowym. Tak więc ten błąd jest wynikiem niepowodzenia odmowy dostępu. Upewnij się, że konto użytkownika ma uprawnienia administratora.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Niewystarczające uprawnienia błąd (ErrorID: 95518)

Gdy ustanowienie relacji zaufania domeny między domeną podstawową a stacją roboczą nie powiedzie się podczas próby zalogowania się na komputerze źródłowym, instalacja agenta mobilności kończy się niepowodzeniem z identyfikatorem błędu 95518. Upewnij się więc, że konto użytkownika używane do instalowania agenta mobilności ma uprawnienia administracyjne do logowania się za pośrednictwem domeny podstawowej komputera źródłowego.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi [tutaj](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Błędy logowania (Identyfikator błędu: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Poświadczenia konta użytkownika zostały wyłączone (Identyfikator błędu: 95519)

Konto użytkownika wybrane podczas włączania replikacji zostało wyłączone. Aby włączyć konto użytkownika, zapoznaj się z artykułem [tutaj](https://aka.ms/enable_login_user) lub uruchom następujące polecenie, zastępując *nazwę użytkownika* tekstowego rzeczywistą nazwą użytkownika.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Poświadczenia zablokowane z powodu wielu nieudanych prób logowania (Identyfikator błędu: 95520)

Wiele nieudanych ponownych prób, aby uzyskać dostęp do komputera, zablokuje konto użytkownika. Awaria może być spowodowana:

* Poświadczenia podane podczas konfiguracji są niepoprawne LUB
* Nieprawidłowe jest konto użytkownika wybrane podczas włączania replikacji

Tak, zmodyfikuj poświadczenia wybrane, postępując zgodnie z instrukcjami podanymi [w tym miejscu](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) i ponów próbę wykonania operacji po pewnym czasie.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Serwery logowania nie są dostępne na komputerze źródłowym (Identyfikator błędu: 95521)

Ten błąd występuje, gdy serwery logowania nie są dostępne na komputerze źródłowym. Niedostępność serwerów logowania doprowadzi do awarii żądania logowania, a tym samym nie można zainstalować agenta mobilności. Aby pomyślnie zalogować się, upewnij się, że serwery logowania są dostępne na komputerze źródłowym i uruchom usługę Logowania. Aby uzyskać szczegółowe instrukcje, zobacz KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: Obecnie nie ma dostępnych serwerów logowania.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Usługa logowania nie jest uruchomiona na komputerze źródłowym (Identyfikator błędu: 95522)

Usługa logowania nie jest uruchomiona na komputerze źródłowym i spowodowała niepowodzenie żądania logowania. Nie można więc zainstalować agenta mobilności. Aby rozwiązać, upewnij się, że usługa logowania jest uruchomiona na komputerze źródłowym dla pomyślnego logowania. Aby uruchomić usługę logowania, uruchom polecenie "net start Logon" z wiersza polecenia lub uruchom usługę "NetLogon" z Menedżera zadań.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Awaria łączności (identyfikator błędu: 95117 & 97118)**

Serwer konfiguracji/ serwer procesów skalowanych w poziomie próbuje połączyć się ze źródłową maszyną wirtualną w celu zainstalowania agenta mobilności. Ten błąd występuje, gdy komputer źródłowy nie jest osiągalny z powodu problemów z łącznością sieciową. Aby rozwiązać problem,

* Upewnij się, że można ping komputera źródłowego z serwera konfiguracji. Jeśli podczas włączania replikacji wybrano serwer procesów skalowanych w poziomie, upewnij się, że można pingować komputer źródłowy z serwera przetwarzania.
  * Z wiersza polecenia komputera serwera źródłowego użyj telnetu do pingowania serwera konfiguracji/ skalowano w poziomie serwera procesów z portem https (135), jak pokazano poniżej, aby sprawdzić, czy występują problemy z łącznością sieciową lub blokowanie portów zapory.

     `telnet <CS/ scale-out PS IP address> <135>`
* Dodatkowo dla **maszyn wirtualnych z systemem Linux**,
  * Sprawdź, czy zainstalowane są najnowsze pakiety openssh, openssh i openssl.
  * Sprawdź i upewnij się, że secure shell (SSH) jest włączona i działa na porcie 22.
  * Usługi SFTP powinny być uruchomione. Aby włączyć uwierzytelnianie podsystemu SFTP i hasła w pliku sshd_config,
    * Zaloguj się jako użytkownik root.
    * Przejdź do pliku /etc/ssh/sshd_config, znajdź wiersz, który zaczyna się od PasswordAuthentication.
    * Odkomentuj wiersz i zmień wartość na tak
    * Znajdź wiersz, który zaczyna się od podsystemu, i odkomentuj wiersz
    * Uruchom ponownie usługę sshd.
* Próba połączenia mogła zakończyć się niepowodzeniem, jeśli po pewnym czasie nie ma odpowiedniej odpowiedzi lub nawiązane połączenie nie powiodło się, ponieważ połączony host nie odpowiedział.
* Może to być problem związany z łącznością/siecią/domeną. Może to być również spowodowane problemem rozpoznawania nazw DNS lub wyczerpaniem portu TCP. Sprawdź, czy w Twojej domenie nie występują takie znane problemy.

## <a name="connectivity-failure-errorid-95523"></a>Błąd łączności (identyfikator błędu: 95523)

Ten błąd występuje, gdy sieć, w której znajduje się komputer źródłowy, nie została znaleziona lub mogła zostać usunięta lub nie jest już dostępna. Jedynym sposobem rozwiązania problemu jest zapewnienie istnienia sieci.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzanie usług udostępniania plików i drukarek (identyfikator błędu: 95105 & 95106)

Po sprawdzeniu łączności sprawdź, czy usługa udostępniania plików i drukarek jest włączona na maszynie wirtualnej. Te ustawienia są wymagane do skopiowania agenta mobilności na komputerze źródłowym.

W przypadku **systemu Windows 2008 R2 i wcześniejszych wersji**

* Aby włączyć udostępnianie plików i drukarek za pośrednictwem Zapory systemu Windows,
  * Otwórz panel sterowania -> System i > Zapora systemu Windows -> w lewym okienku, kliknij pozycję Ustawienia zaawansowane - > kliknij pozycję Reguły przychodzące w drzewie konsoli.
  * Lokalizuj reguły Udostępnianie plików i drukarek (NB-Session-In) oraz Udostępnianie plików i drukarek (SMB-In). Dla każdej reguły kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Włącz regułę**.
* Aby włączyć udostępnianie plików w zasadach grupy,
  * Przejdź do ekranu startowego, wpisz gpmc.msc i wyszukaj.
  * W okienku nawigacji otwórz następujące foldery: Zasady komputera lokalnego, Konfiguracja użytkownika, Szablony administracyjne, Składniki systemu Windows i Udostępnianie sieci.
  * W okienku szczegółów kliknij dwukrotnie pozycję **Uniemożliwij użytkownikom udostępnianie plików w ich profilu**. Aby wyłączyć ustawienie zasad grupy i włączyć możliwość udostępniania plików przez użytkownika, kliknij pozycję Wyłączone. Kliknij przycisk OK, aby zapisać zmiany. Aby dowiedzieć się więcej, zobacz [Włączanie lub wyłączanie udostępniania plików w zasadach grupy](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

W przypadku **nowszych wersji**postępuj zgodnie z instrukcjami zawartymi w aplikacji Zainstaluj usługę mobilności w [celu odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych,](vmware-azure-install-mobility-service.md) aby włączyć udostępnianie plików i drukarek.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Sprawdzanie konfiguracji instrumentacji zarządzania windowsem (WMI) (kod błędu: 95103)

Po sprawdzeniu usług plików i drukarek włącz usługę WMI dla profilów prywatnych, publicznych i domen za pośrednictwem zapory. Te ustawienia są wymagane do ukończenia zdalnego wykonywania na komputerze źródłowym. Aby włączyć,

* Przejdź do Panelu sterowania, kliknij pozycję Zabezpieczenia, a następnie kliknij pozycję Zapora systemu Windows.
* Kliknij pozycję Zmień ustawienia, a następnie kliknij kartę Wyjątki.
* W oknie Wyjątki zaznacz pole wyboru Instrumentacja zarządzania Windows (WMI), aby włączyć ruch WMI za pośrednictwem zapory. 

Można również włączyć ruch WMI przez zaporę w wierszu polecenia. Użyj następującego polecenia`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Inne artykuły dotyczące rozwiązywania problemów z węgorzy można znaleźć w następujących artykułach.

* [Podstawowe testy WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z wimi](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Rozwiązywanie problemów ze skryptami WMI i usługami WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nieobsługiwały się systemy operacyjne

Innym najczęstszym powodem awarii może być z powodu nieobsługiconego systemu operacyjnego. Upewnij się, że korzystasz z obsługiwanej wersji systemu operacyjnego/jądra, aby pomyślnie zinstalować usługę mobilności. Unikaj używania łatki prywatnej.
Aby wyświetlić listę systemów operacyjnych i wersji jądra obsługiwanych przez usługę Azure Site Recovery, zapoznaj się z naszym [dokumentem macierzy pomocy technicznej](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nieobsługiwały konfiguracje dysków rozruchowych (identyfikator błędu: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Partycje rozruchowe i systemowe / woluminy nie są tym samym dyskiem (ErrorID: 95309)

Przed wersją 9.20 partycje rozruchowe i systemowe na różnych dyskach były nieobsługiwały konfiguracji. Od [wersji 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)ta konfiguracja jest obsługiwana. Użyj najnowszej wersji dla tej pomocy technicznej.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Dysk rozruchowy jest niedostępny (Identyfikator błędu: 95310)

Nie można chronić maszyny wirtualnej bez dysku rozruchowego. Ma to na celu zapewnienie płynnego odzyskiwania maszyny wirtualnej podczas pracy awaryjnej. Brak dysku rozruchowego powoduje niepowodzenie rozruchu komputera po przemienniu w pracy awaryjnej. Upewnij się, że maszyna wirtualna zawiera dysk rozruchowy i ponów próbę wykonania operacji. Należy również pamiętać, że wiele dysków rozruchowych na tym samym komputerze nie jest obsługiwanych.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Wiele dysków rozruchowych obecnych na komputerze źródłowym (Identyfikator błędu: 95311)

Maszyna wirtualna z wieloma dyskami rozruchowymi nie jest [obsługiwaną konfiguracją](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partycja systemowa na wielu dyskach (Identyfikator błędu: 95313)

Przed wersją 9.20 partycja główna lub wolumin ułożony na wielu dyskach była nieobsługiconą konfiguracją. Od [wersji 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)ta konfiguracja jest obsługiwana. Użyj najnowszej wersji dla tej pomocy technicznej.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Włącz ochronę nie powiodło się jako nazwa urządzenia wymieniona w konfiguracji GRUB zamiast UUID (ErrorID: 95320)

**Możliwa przyczyna:** </br>
Pliki konfiguracyjne GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" lub "/etc/default/grub") mogą zawierać wartość **dla parametrów głównego** i **wznowić** jako rzeczywiste nazwy urządzeń zamiast UUID. Odzyskiwanie lokacji nakazuje podejście UUID, ponieważ nazwa urządzeń może ulec zmianie podczas ponownego uruchamiania maszyny Wirtualnej, ponieważ maszyna wirtualna może nie wymyślić tej samej nazwy w trybie failover, co powoduje problemy. Przykład: </br>


- Poniższy wiersz pochodzi z pliku GRUB **/boot/grub2/grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **resume=/dev/sda1** splash=ciche ciche showopts*


- Następujący wiersz pochodzi z pliku GRUB **/boot/grub/menu.lst**
  *jądra /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Jeśli obserwujesz pogrubiony ciąg powyżej, GRUB ma rzeczywiste nazwy urządzeń dla parametrów "root" i "resume" zamiast UUID.
 
**Jak naprawić:**<br>
Nazwy urządzeń należy zamienić na odpowiednie identyfikatory UUID.<br>


1. Znajdź identyfikator UUID urządzenia, wykonując polecenie "nazwa \<urządzenia blkid>". Przykład:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Teraz zastąp nazwę urządzenia jego identyfikatorem UUID w\<formacie "root=UUID= UUID>". Na przykład, jeśli zastąpimy nazwy urządzeń UUID dla parametru root i resume wymienionego powyżej w plikach "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" lub "/etc/default/grub: wtedy linie w plikach wyglądają tak. <br>
   *jądro /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=cichy crashkernel=256M-:128M showopts vga=0x314*
3. Ponowne ponowne uruchomienie ochrony

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Zainstaluj usługę mobilności zakończono z ostrzeżeniem o ponownym uruchomieniu komputera (ErrorID: 95265 & 95266)

Usługa mobilności odzyskiwania witryny ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko w momencie ponownego uruchomienia systemu. Oznacza to, że poprawki sterownika filtru mogą być realizowane tylko po załadowaniu nowego sterownika filtru; co może się zdarzyć tylko w momencie ponownego uruchomienia systemu.

**Należy pamiętać,** że jest to ostrzeżenie i istniejąca replikacja będzie działać nawet po aktualizacji nowego agenta. Możesz ponownie uruchomić komputer w dowolnym momencie, aby uzyskać korzyści z nowego sterownika filtru, ale jeśli nie uruchomisz ponownie starego sterownika filtru, nadal działa. Tak więc, po aktualizacji bez ponownego uruchomienia, oprócz sterownika **filtra, korzyści z innych ulepszeń i poprawek w usłudze mobilności zostaje zrealizowany**. Tak więc, choć zalecane, nie jest obowiązkowe ponowne uruchomienie po każdym uaktualnieniu. Aby uzyskać informacje o tym, kiedy ponowne uruchomienie komputera jest obowiązkowe, ustaw sekcję [Ponowne uruchamianie komputera źródłowego po uaktualnieniu agenta mobilności](https://aka.ms/v2a_asr_reboot) w aktualizacji usługi w usłudze Azure Site Recovery.

> [!TIP]
>Aby uzyskać najlepsze rozwiązania dotyczące planowania uaktualnień w oknie konserwacji, zobacz [pomoc techniczna dla najnowszych wersji systemu operacyjnego/jądra](https://aka.ms/v2a_asr_upgrade_practice) w aktualizacjach usługi w usłudze Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Obsługa LVM od wersji 9.20

Przed wersją 9.20 lvm był obsługiwany tylko dla dysków z danymi. /boot powinien znajdować się na partycji dysku i nie być woluminem LVM.

Od [wersji 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)obsługiwany jest [dysk systemu operacyjnego na LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) Użyj najnowszej wersji dla tej pomocy technicznej.

## <a name="insufficient-space-errorid-95524"></a>Za mało miejsca (Identyfikator błędu: 95524)

Gdy agent mobilności jest kopiowany na komputerze źródłowym, wymagane jest co najmniej 100 MB wolnego miejsca. Upewnij się więc, że komputer źródłowy wymaga wolnego miejsca i ponów próbę wykonania operacji.

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi VSS jest częścią instalacji agenta mobilności. Ta usługa jest używana w procesie generowania spójnych między aplikacjami punktów odzyskiwania. Błędy podczas instalacji usługi VSS mogą wystąpić z wielu powodów. Aby zidentyfikować dokładne błędy, zapoznaj się **z c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Kilka typowych błędów i kroki rozwiązywania problemów są wyróżnione w poniższej sekcji.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd usługi VSS -2147023170 [0x800706BE] - kod zakończenia 511

Ten problem jest najczęściej widoczne, gdy oprogramowanie antywirusowe blokuje operacje usług Azure Site Recovery. Aby rozwiązać ten problem:

1. Wyklucz wszystkie foldery wymienione [tutaj](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Postępuj zgodnie z wytycznymi opublikowanymi przez dostawcę antywirusowego, aby odblokować rejestrację biblioteki DLL w systemie Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd usługi VSS 7 [0x7] - kod zakończenia 511

Jest to błąd środowiska uruchomieniowego i jest spowodowane niewystarczającą ilością pamięci do zainstalowania usługi VSS. Upewnij się, aby zwiększyć miejsce na dysku dla pomyślnego zakończenia tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd usługi VSS -2147023824 [0x80070430] - kod zakończenia 517

Ten błąd występuje, gdy usługa dostawcy usługi usługi Azure Site Recovery VSS provider jest [oznaczona do usunięcia](https://msdn.microsoft.com/library/ms838153.aspx). Spróbuj zainstalować usługę VSS ręcznie na komputerze źródłowym, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd usługi VSS -2147023841 [0x8007041F] - kod zakończenia 512

This error occurs when Azure Site Recovery VSS Provider service database is [locked](https://msdn.microsoft.com/library/ms833798.aspx). Spróbuj zainstalować usługę VSS ręcznie na komputerze źródłowym, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

W przypadku awarii sprawdź, czy jakikolwiek program antywirusowy lub inne usługi utknęły w stanie "Uruchamianie". Może to zachować blokadę usług bazy danych. Doprowadzi to do błędów w instalacji dostawcy usługi VSS. Upewnij się, że żadna usługa nie jest w stanie "Uruchamianie", a następnie ponów próbę powyższej operacji.

### <a name="vss-exit-code-806"></a>Kod zakończenia usługi VSS 806

Ten błąd występuje, gdy konto użytkownika używane do instalacji nie ma uprawnień do wykonania polecenia CSScript. Podaj niezbędne uprawnienia do konta użytkownika, aby wykonać skrypt i ponowić próbę wykonania operacji.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy usługi VSS na komputerze źródłowym, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Błąd usługi VSS - 0x8004E00F

Ten błąd jest zazwyczaj napotkane podczas instalacji agenta mobilności z powodu problemów w DCOM i DCOM jest w stanie krytycznym.

Poniższa procedura służy do określenia przyczyny błędu.

**Sprawdzanie dzienników instalacji**

1. Otwórz dziennik instalacji znajdujący się pod adresem c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Obecność następującego błędu wskazuje ten problem:

    Wyrejestrowanie istniejącej aplikacji...  Tworzenie obiektu katalogu Pobierz kolekcję Aplikacji 

    Błąd:

    - Kod błędu: -2147164145 [0x8004E00F]
    - Kod zakończenia: 802

Aby rozwiązać ten problem:

Skontaktuj się z [zespołem platformy Microsoft Windows,](https://aka.ms/Windows_Support) aby uzyskać pomoc w rozwiązaniu problemu dcom.

Po rozwiązaniu problemu z kontrolerem DCOM należy ręcznie zainstalować dostawcę usługi Azure Site Recovery VSS za pomocą następującego polecenia:
 
**C:\Pliki programów (x86)\Odzyskiwanie witryny platformy Microsoft Azure\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Jeśli spójność aplikacji nie jest krytyczna dla wymagań odzyskiwania po awarii, można pominąć instalację dostawcy usługi VSS. 

Aby ominąć instalację dostawcy usługi Azure Site Recovery VSS i ręcznie zainstalować dostawcę usługi Azure Site Recovery VSS po instalacji:

1. Zainstaluj usługę mobilności. 
   > [!Note]
   > 
   > Instalacja zakończy się niepowodzeniem w kroku "Po instalacji konfiguracji". 
2. Aby ominąć instalację usługi VSS:
   1. Otwórz katalog instalacji usługi azure site recovery mobility service znajdujący się pod adresem:
   
      C:\Pliki programów (x86)\Odzyskiwanie witryny Microsoft Azure\agent
   2. Zmodyfikuj skrypty instalacyjne dostawcy usługi Azure Site Recovery VSS **nMageVSSProvider_Install** i **InMageVSSProvider_Uninstall.cmd,** aby zawsze odnieść sukces, dodając następujące wiersze:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Uruchom ponownie instalację agenta mobilności ręcznie. 
4. Gdy instalacja zakończy się pomyślnie i przejdzie do następnego kroku, **Skonfiguruj**, usuń dodane wiersze.
5. Aby zainstalować dostawcę usługi VSS, otwórz wiersz polecenia jako administrator i uruchom następujące polecenie:
   
    **C:\Pliki programów (x86)\Odzyskiwanie witryny microsoft azure\agent> .\InMageVSSProvider_Install.cmd**

9. Sprawdź, czy dostawca ASR VSS jest zainstalowany jako usługa w usługach systemu Windows i otwórz program MMC usługi składnikowej, aby sprawdzić, czy dostawca ASR VSS znajduje się na liście.
10. Jeśli instalacja dostawcy usługi VSS nadal kończy się niepowodzeniem, należy pracować z cx, aby rozwiązać błędy uprawnień w capi2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalacja dostawcy usługi VSS kończy się niepowodzeniem z powodu włączenia usługi klastrowania na komputerze nieklastrowym

Ten problem powoduje, że instalacja agenta mobilności usługi Azure Site Recovery nie powiodło się podczas kroku instalacji dostawcy asazure site recoveryr vss z powodu problemu z com+, który uniemożliwia instalację dostawcy usługi VSS.
 
### <a name="to-identify-the-issue"></a>Aby zidentyfikować problem

W dzienniku znajdującym się na serwerze konfiguracyjnym w miejscu:\<C:\ProgramData\ASRSetupLogs\UploadedLogs date-time>UA_InstallLogFile.log znajduje się następujący wyjątek:

Com+ nie może rozmawiać z koordynatorem transakcji rozproszonych firmy Microsoft (wyjątek od HRESULT: 0x8004E00F)

Aby rozwiązać ten problem:

1.  Sprawdź, czy ten komputer jest maszyną niebędącą klastrem i czy składniki klastra nie są używane.
3.  Jeśli składniki nie są używane, usuń składniki klastra z urządzenia.

## <a name="drivers-are-missing-on-the-source-server"></a>Brak sterowników na serwerze źródłowym

Jeśli instalacja agenta mobilności nie powiedzie się, sprawdź dzienniki w obszarze C:\ProgramData\ASRSetupLogs, aby ustalić, czy w niektórych zestawach sterowania brakuje niektórych wymaganych sterowników.
 
Aby rozwiązać ten problem:
  
1. Za pomocą edytora rejestru, takiego jak regedit.msc, otwórz rejestr.
2. Otwórz węzeł HKEY_LOCAL_MACHINE\SYSTEM.
3. W węźle SYSTEM zlokalizuj zestawy formantów.
4. Otwórz każdy zestaw sterowania i sprawdź, czy występują następujące sterowniki systemu Windows:

   - Atapi
   - Vmbus
   - Storflt ( Storflt )
   - Storvsc ( Storvsc )
   - intelide
 
Zainstaluj ponownie brakujące sterowniki.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.
