---
title: Rozwiązywanie problemów z instalacją wypychaną usługi mobilności za pomocą usługi Azure Site Recovery
description: Rozwiązywanie problemów z błędami instalacji usług mobilności podczas włączania replikacji do odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656323"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Rozwiązywanie problemów z instalacją wypychania usługi mobilności

Instalacja usługi mobilności jest kluczowym krokiem, aby włączyć replikację. Powodzenie tego kroku zależy od spełnienia wymagań wstępnych i pracy z obsługiwanych konfiguracji. Najczęstsze błędy, które mogą wystąpić podczas instalacji usługi mobilności, są spowodowane:

* [Błędy poświadczeń/uprawnień](#credentials-check-errorid-95107--95108)
* [Błędy logowania](#login-failures-errorid-95519-95520-95521-95522)
* [Błędy łączności](#connectivity-failure-errorid-95117--97118)
* [Błędy udostępniania plików i drukarek](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Awarie instrumentacji zarządzania windowsem (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nieobsługiwane systemy operacyjne](#unsupported-operating-systems)
* [Nieobsługiwały konfiguracje rozruchu](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Błędy instalacji usługi kopiowania woluminów w tle (VSS)](#vss-installation-failures)
* [Nazwa urządzenia w konfiguracji GRUB zamiast identyfikatora UUID urządzenia](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Wolumin menedżera woluminów logicznych (LVM)](#lvm-support-from-920-version)
* [Ostrzeżenia o ponownym uruchomieniu](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Po włączeniu replikacji usługa Azure Site Recovery próbuje zainstalować agenta usługi mobilności na maszynie wirtualnej (VM). W ramach tego procesu serwer konfiguracji próbuje połączyć się z maszyną wirtualną i skopiować agenta. Aby umożliwić pomyślną instalację, postępuj zgodnie ze wskazówkami dotyczącymi rozwiązywania problemów krok po kroku.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdzanie poświadczeń (identyfikator błędu: 95107 & 95108)

Sprawdź, czy konto użytkownika wybrane podczas włączania replikacji jest prawidłowe i dokładne. Usługa Azure Site Recovery wymaga konta **głównego** lub konta użytkownika z **uprawnieniami administratora** do przeprowadzenia instalacji wypychanej. W przeciwnym razie instalacja wypychania zostanie zablokowana na komputerze źródłowym.

W systemie Windows **(błąd 95107)** sprawdź, czy konto użytkownika ma dostęp administracyjny na komputerze źródłowym przy użyciu konta lokalnego lub konta domeny. Jeśli nie używasz konta domeny, musisz wyłączyć kontrolę dostępu użytkownika zdalnego na komputerze lokalnym.

* Aby ręcznie dodać klucz rejestru wyłącza kontrolkę dostępu użytkownika zdalnego:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Dodaj nowy `DWORD`:`LocalAccountTokenFilterPolicy`
  * Ustaw wartość na`1`

* Aby dodać klucz rejestru, w wierszu polecenia uruchom następujące polecenie:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Dla Linuksa **(błąd 95108**), należy wybrać konto **główne** dla pomyślnej instalacji agenta usługi Mobilności. Ponadto powinny być uruchomione usługi protokołu SFTP (SFTP). Aby włączyć podsystem SFTP i uwierzytelnianie hasłem w pliku _sshd_config:_

1. Zaloguj się jako użytkownik **root**.
1. Przejdź do _pliku /etc/ssh/sshd_config_, znajdź wiersz, `PasswordAuthentication`który zaczyna się od .
1. Odkomentuj wiersz i zmień `yes`wartość na .
1. Znajdź wiersz, który `Subsystem`zaczyna się od , i odkomentuj linię.
1. Uruchom `sshd` ponownie usługę.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Niewystarczające uprawnienia błąd (ErrorID: 95517)

Jeśli użytkownik wybrany do zainstalowania agenta mobilności nie ma uprawnień administratora, serwer konfiguracji/serwer procesów skalowanych w poziomie nie będzie mógł skopiować oprogramowania agenta mobilności na komputer źródłowy. Ten błąd jest wynikiem niepowodzenia odmowy dostępu. Upewnij się, że konto użytkownika ma uprawnienia administratora.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Niewystarczające uprawnienia błąd (ErrorID: 95518)

Gdy ustanowienie relacji zaufania domeny między domeną podstawową a stacją roboczą nie powiedzie się podczas próby zalogowania się na komputerze źródłowym, instalacja agenta mobilności kończy się niepowodzeniem z identyfikatorem błędu 95518. Upewnij się, że konto użytkownika używane do zainstalowania agenta mobilności ma uprawnienia administracyjne do logowania się za pośrednictwem domeny podstawowej komputera źródłowego.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Błędy logowania (Identyfikator błędu: 95519, 95520, 95521, 95522)

W tej sekcji opisano komunikaty o błędach poświadczeń i logowania.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Poświadczenia konta użytkownika zostały wyłączone (Identyfikator błędu: 95519)

Konto użytkownika wybrane podczas włączania replikacji zostało wyłączone. Aby włączyć konto użytkownika, zapoznaj się z [tym artykułem](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) lub uruchom następujące polecenie, zastępując _tekstową nazwę użytkownika_ rzeczywistą nazwą użytkownika.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Poświadczenia zablokowane z powodu wielu nieudanych prób logowania (Identyfikator błędu: 95520)

Wiele nieudanych ponownych prób, aby uzyskać dostęp do komputera, zablokuje konto użytkownika. Awaria może być spowodowana:

* Poświadczenia podane podczas konfiguracji są niepoprawne.
* Konto użytkownika wybrane podczas włączania replikacji jest nieprawidłowe.

Zmodyfikuj wybrane poświadczenia, postępując zgodnie z [tymi instrukcjami](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) i ponów próbę wykonania operacji.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Serwery logowania nie są dostępne na komputerze źródłowym (Identyfikator błędu: 95521)

Ten błąd występuje, gdy serwery logowania nie są dostępne na komputerze źródłowym. Jeśli serwery logowania nie są dostępne, żądania logowania nie powiodą się i nie można zainstalować agenta mobilności. Aby uzyskać pomyślne logowanie, upewnij się, że serwery logowania są dostępne na komputerze źródłowym i uruchom usługę Netlogon. Aby uzyskać więcej informacji, zobacz [Scenariusze logowania systemu Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Usługa logowania nie jest uruchomiona na komputerze źródłowym (Identyfikator błędu: 95522)

Usługa logowania nie jest uruchomiona na komputerze źródłowym i spowodowała awarię żądania logowania. Nie można zainstalować agenta mobilności. Aby rozwiązać ten problem, użyj jednej z `Netlogon` następujących metod, aby uruchomić usługę na komputerze źródłowym:

* Aby uruchomić `Netlogon` usługę z wiersza polecenia, uruchom polecenie `net start Netlogon`.
* Z Menedżera zadań `Netlogon` uruchom usługę.

## <a name="connectivity-failure-errorid-95117--97118"></a>Awaria łączności (identyfikator błędu: 95117 & 97118)

Serwer konfiguracji/serwer procesów skalowanych w poziomie próbuje połączyć się ze źródłową maszyną wirtualną w celu zainstalowania agenta mobilności. Ten błąd występuje, gdy komputer źródłowy nie jest osiągalny, ponieważ występują problemy z łącznością sieciową.

Aby rozwiązać ten błąd:

* Upewnij się, że można ping komputera źródłowego z serwera konfiguracji. Jeśli wybrano serwer procesów skalowanych w poziomie podczas włączania replikacji, upewnij się, że można pingować komputer źródłowy z serwera przetwarzania.

* Z wiersza polecenia komputera `Telnet` serwera źródłowego służy do pingowania serwera konfiguracji lub serwera procesów skalowanych w poziomie na porcie HTTPS 135, jak pokazano w poniższym poleceniu. To polecenie sprawdza, czy występują problemy z łącznością sieciową lub blokowaniem portu zapory.

  `telnet <CS/ scale-out PS IP address> <135>`

* Ponadto dla maszyny Wirtualnej z systemem Linux:
  * Sprawdź, czy są zainstalowane najnowsze pakiety OpenSSH, OpenSSH Server i OpenSSL.
  * Sprawdź i upewnij się, że secure shell (SSH) jest włączona i działa na porcie 22.
  * Usługi SFTP powinny być uruchomione. Aby włączyć uwierzytelnianie podsystemu SFTP i hasła w pliku _sshd_config:_

    1. Zaloguj się jako użytkownik **root**.
    1. Przejdź do _pliku /etc/ssh/sshd_config,_ znajdź wiersz, `PasswordAuthentication`który zaczyna się od pliku .
    1. Odkomentuj wiersz i zmień `yes`wartość na .
    1. Znajdź wiersz, który `Subsystem`zaczyna się od , i odkomentuj linię
    1. Uruchom `sshd` ponownie usługę.

* Próba połączenia mogła zakończyć się niepowodzeniem, jeśli po pewnym czasie nie ma odpowiednich odpowiedzi lub nawiązane połączenie nie powiodło się, ponieważ połączony host nie odpowiedział.
* Może to być problem związany z łącznością/siecią/domeną. Może to być również spowodowane rozwiązaniem problemu z rozpoznawaniem nazw DNS lub problemem wyczerpania portów TCP. Sprawdź, czy w Twojej domenie nie występują takie znane problemy.

## <a name="connectivity-failure-errorid-95523"></a>Błąd łączności (identyfikator błędu: 95523)

Ten błąd występuje, gdy sieć, w którym znajduje się komputer źródłowy, nie została znaleziona, mogła zostać usunięta lub nie jest już dostępna. Jedynym sposobem rozwiązania problemu jest upewnienie się, że sieć istnieje.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzanie usług udostępniania plików i drukarek (identyfikator błędu: 95105 & 95106)

Po sprawdzeniu łączności sprawdź, czy usługa udostępniania plików i drukarek jest włączona na maszynie wirtualnej. Te ustawienia są wymagane do skopiowania agenta mobilności na komputerze źródłowym.

W systemie **Windows 2008 R2 i wcześniejszych wersjach:**

* Aby włączyć udostępnianie plików i drukarek za pośrednictwem Zapory systemu Windows,
  1. Otwórz **system panelu sterowania** > i**zaporę systemu Windows****zabezpieczeń** > . W lewym okienku wybierz pozycję **Ustawienia zaawansowane Reguły** > **przychodzące** w drzewie konsoli.
  1. Lokalizuj reguły Udostępnianie plików i drukarek (NB-Session-In) oraz Udostępnianie plików i drukarek (SMB-In).
  1. Dla każdej reguły kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Włącz regułę**.

* Aby włączyć udostępnianie plików w zasadach grupy:
  1. Przejdź do ekranu `gpmc.msc` **startowego**, wpisz i wyszukaj.
  1. W okienku nawigacji otwórz następujące foldery:**Szablony** > **administracyjne** > konfiguracji użytkowników **zasad** > komputera lokalnego —**udostępnianie sieci****składników** > systemu Windows .
  1. W okienku szczegółów kliknij dwukrotnie pozycję **Uniemożliwij użytkownikom udostępnianie plików w ich profilu**.

     Aby wyłączyć ustawienie Zasad grupy i włączyć możliwość udostępniania plików przez użytkownika, **wybierz**wyłączone .

  1. Aby zapisać zmiany, wybierz pozycję **OK**.

  Aby dowiedzieć się więcej, zobacz [Włączanie lub wyłączanie udostępniania plików w zasadach grupy](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Aby włączyć udostępnianie plików i drukarek w nowszych wersjach systemu Windows lub Linux, postępuj zgodnie z instrukcjami w aplikacji Zainstaluj usługę mobilności w [celu odzyskiwania po awarii maszyn wirtualnych i serwerów fizycznych](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Sprawdzanie konfiguracji instrumentacji zarządzania windowsem (WMI) (kod błędu: 95103)

Po sprawdzeniu usług plików i drukarek włącz usługę WMI dla profilów prywatnych, publicznych i domen za pośrednictwem zapory. Te ustawienia są wymagane do ukończenia zdalnego wykonywania na komputerze źródłowym.

Aby włączyć usługę WMI:

1. Przejdź do **usługi Zabezpieczenia Panelu sterowania** > **Security** i wybierz **pozycję Zapora systemu Windows**.
1. Wybierz **pozycję Zmień ustawienia,** a następnie wybierz kartę **Wyjątki.**
1. W oknie **Wyjątki** zaznacz pole wyboru Instrumentacja zarządzania Windows (WMI), aby włączyć ruch WMI za pośrednictwem zapory.

Ruch WMI można również włączyć przez zaporę za pomocą wiersza polecenia za pomocą następującego polecenia:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Inne artykuły dotyczące rozwiązywania problemów z węgorzy można znaleźć w następujących artykułach.

* [Podstawowe testy WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z wimi](/windows/win32/wmisdk/wmi-troubleshooting)
* [Rozwiązywanie problemów ze skryptami WMI i usługami WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nieobsługiwane systemy operacyjne

Innym częstym powodem awarii może być z powodu nieobsługiwał system operacyjny. Aby pomyślnie zinstalowację usługi mobilności, można użyć obsługiwanego systemu operacyjnego i wersji jądra. Unikaj używania prywatnych poprawek.

Aby wyświetlić listę systemów operacyjnych i wersji jądra obsługiwanych przez usługę Azure Site Recovery, zobacz [dokument macierzy pomocy technicznej](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nieobsługiwały konfiguracje dysków rozruchowych (identyfikator błędu: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Partycje rozruchowe i systemowe / woluminy nie są tym samym dyskiem (ErrorID: 95309)

Przed wersją 9.20 partycje/woluminy rozruchowe i systemowe na różnych dyskach były nieobsługiwały. Począwszy od [wersji 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)ta konfiguracja jest obsługiwana.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Dysk rozruchowy jest niedostępny (Identyfikator błędu: 95310)

Nie można chronić maszyny wirtualnej bez dysku rozruchowego. Dysk rozruchowy zapewnia płynne odzyskiwanie maszyny wirtualnej podczas operacji pracy awaryjnej. Brak dysku rozruchowego powoduje niepowodzenie rozruchu komputera po przemienniu w pracy awaryjnej. Upewnij się, że maszyna wirtualna zawiera dysk rozruchowy i ponów próbę wykonania operacji. Ponadto wiele dysków rozruchowych na tym samym komputerze nie są obsługiwane.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Wiele dysków rozruchowych obecnych na komputerze źródłowym (Identyfikator błędu: 95311)

Maszyna wirtualna z wieloma dyskami rozruchowymi nie jest [obsługiwaną konfiguracją.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partycja systemowa na wielu dyskach (Identyfikator błędu: 95313)

Przed wersją 9.20 partycja główna lub konfiguracja woluminu na wielu dyskach była nieobsługiwała konfiguracji. Począwszy od [wersji 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)ta konfiguracja jest obsługiwana.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Włącz ochronę nie powiodło się jako nazwa urządzenia wymieniona w konfiguracji GRUB zamiast UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Możliwa przyczyna

Pliki konfiguracyjne Grand Unified Bootloader (GRUB) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, lub _/etc/default/grub_) mogą zawierać wartość **dla parametrów root** i **wznowić** jako rzeczywiste nazwy urządzeń zamiast uniwersalnie unikatowego identyfikatora (UUID). Odzyskiwanie lokacji nakazuje podejście UUID, ponieważ nazwy urządzeń mogą ulec zmianie podczas ponownego uruchamiania maszyny Wirtualnej. Na przykład maszyna wirtualna może nie być w trybie online o tej samej nazwie w trybie failover i powoduje problemy.

Przykład:

- Następujący wiersz pochodzi z pliku GRUB _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Następujący wiersz pochodzi z pliku GRUB _/boot/grub/menu.lst:_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> Wiersze GRUB zawierają rzeczywiste nazwy urządzeń dla **parametrów głównego** i **wznowić,** a nie UUID.

### <a name="how-to-fix"></a>Jak naprawić

Nazwy urządzeń należy zamienić na odpowiednie identyfikatory UUID.

1. Znajdź identyfikator UUID urządzenia, wykonując `blkid \<device name>`polecenie .

   Przykład:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Teraz zastąp nazwę urządzenia jego UUID w formacie takim jak `root=UUID=\<UUID>`. Na przykład, jeśli zamienimy nazwy urządzeń na UUID dla parametru root i resume wymienionego w plikach _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_lub _/etc/default/grub,_ linie w plikach wyglądają następująco:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Uruchom ponownie ochronę.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Zainstaluj usługę mobilności zakończono z ostrzeżeniem o ponownym uruchomieniu komputera (ErrorID: 95265 & 95266)

Usługa Site Recovery Mobility ma wiele składników, z których jeden nosi nazwę sterownika filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko podczas ponownego uruchomienia systemu. Poprawki sterownika filtru można zrealizować tylko wtedy, gdy nowy sterownik filtru jest ładowany w momencie ponownego uruchomienia systemu.

> [!IMPORTANT]
> Jest to ostrzeżenie i istniejąca replikacja będzie działać nawet po aktualizacji nowego agenta. Możesz ponownie uruchomić komputer w dowolnym momencie, aby uzyskać korzyści z nowego sterownika filtru, ale jeśli nie uruchomisz ponownie, stary sterownik filtru nadal działa. Tak więc, po aktualizacji bez ponownego uruchomienia, z wyjątkiem sterownika filtra, **korzyści z innych ulepszeń i poprawek w usłudze mobilności stają się realizowane**. Chociaż zalecane, nie jest obowiązkowe, aby ponownie uruchomić po każdym uaktualnieniu. Aby uzyskać informacje o tym, kiedy ponowne uruchomienie komputera jest obowiązkowe, ustaw sekcję [Ponowne uruchamianie po uaktualnieniu usługi mobilności](service-updates-how-to.md#reboot-after-mobility-service-upgrade) w aktualizacji usługi w usłudze Azure Site Recovery.

> [!TIP]
>Aby uzyskać najlepsze rozwiązania dotyczące planowania uaktualnień w oknie konserwacji, zobacz [pomoc techniczna dla najnowszych aktualizacji systemu operacyjnego/jądra](service-updates-how-to.md#support-for-latest-operating-systemskernels) w usłudze w usłudze Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Obsługa LVM od wersji 9.20

Przed wersją 9.20 menedżer woluminów logicznych (LVM) był obsługiwany tylko dla dysków danych. Partycja `/boot` powinna znajdować się na partycji dysku, a nie na woluminie LVM.

Począwszy od [wersji 9.20,](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)obsługiwany jest [dysk systemu operacyjnego w LVM.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="insufficient-space-errorid-95524"></a>Za mało miejsca (Identyfikator błędu: 95524)

Gdy agent mobilności jest kopiowany na komputerze źródłowym, wymagane jest co najmniej 100 MB wolnego miejsca. Upewnij się, że na komputerze źródłowym jest wymagana ilość wolnego miejsca i ponów próbę wykonania operacji.

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi kopiowania woluminów w tle (VSS) jest częścią instalacji agenta mobilności. Ta usługa jest używana w procesie generowania spójnych punktów odzyskiwania aplikacji. Błędy podczas instalacji usługi VSS mogą wystąpić z wielu powodów. Aby zidentyfikować dokładne błędy, zobacz _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Niektóre typowe błędy i kroki rozwiązywania problemów są wyróżnione w poniższej sekcji.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd usługi VSS -2147023170 [0x800706BE] - kod zakończenia 511

Ten problem jest najczęściej widoczny, gdy oprogramowanie antywirusowe blokuje operacje usług Azure Site Recovery.

Aby rozwiązać ten problem:

1. Przejrzyj listę [wykluczeń folderów z programu antywirusowego](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Postępuj zgodnie ze wskazówkami opublikowanymi przez dostawcę oprogramowania antywirusowego, aby odblokować rejestrację biblioteki DLL w systemie Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd usługi VSS 7 [0x7] - kod zakończenia 511

Ten błąd jest błąd środowiska uruchomieniowego, który jest spowodowany, ponieważ nie ma wystarczającej ilości pamięci do zainstalowania usługi VSS. Zwiększ miejsce na dysku w celu pomyślnego ukończenia tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd usługi VSS -2147023824 [0x80070430] - kod zakończenia 517

Ten błąd występuje, gdy usługa dostawcy usługi usługi Azure Site Recovery VSS provider jest [oznaczona do usunięcia](/previous-versions/ms838153(v=msdn.10)). Spróbuj zainstalować usługę VSS ręcznie na komputerze źródłowym, uruchamiając następujące polecenie:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd usługi VSS -2147023841 [0x8007041F] - kod zakończenia 512

This error occurs when Azure Site Recovery VSS Provider service database is [locked](/previous-versions/ms833798(v=msdn.10)). Spróbuj zainstalować usługę VSS ręcznie na komputerze źródłowym, uruchamiając następujące polecenie z wiersza polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

W przypadku awarii sprawdź, czy jakikolwiek program antywirusowy lub inne usługi utknęły w stanie **początkowym.** Proces w stanie **początkowym** może zachować blokadę usług bazy danych. Doprowadzi to do błędów w instalacji dostawcy usługi VSS. Upewnij się, że żadna usługa nie jest w stanie **początkowym,** a następnie ponów próbę powyższej operacji.

### <a name="vss-exit-code-806"></a>Kod zakończenia usługi VSS 806

Ten błąd występuje, gdy konto użytkownika używane do instalacji `CSScript` nie ma uprawnień do wykonania polecenia. Podaj niezbędne uprawnienia do konta użytkownika, aby wykonać skrypt i ponowić próbę wykonania operacji.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy usługi VSS na komputerze źródłowym, uruchamiając następujące polecenie z wiersza polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Błąd usługi VSS - 0x8004E00F

Ten błąd zazwyczaj występuje podczas instalacji agenta `DCOM` mobilności `DCOM` z powodu problemów i jest w stanie krytycznym.

Poniższa procedura służy do określenia przyczyny błędu.

### <a name="examine-the-installation-logs"></a>Sprawdzanie dzienników instalacji

1. Otwórz dziennik instalacji znajdujący się pod adresem _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Obecność następującego błędu wskazuje ten problem:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Aby rozwiązać ten problem:

Skontaktuj się z [zespołem platformy Microsoft Windows,](https://aka.ms/Windows_Support) aby uzyskać pomoc w rozwiązaniu problemu dcom.

Po rozwiązaniu problemu z kontrolerem DCOM należy ręcznie zainstalować dostawcę usługi Azure Site Recovery VSS przy użyciu następującego polecenia z wiersza polecenia:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Jeśli spójność aplikacji nie jest krytyczna dla wymagań odzyskiwania po awarii, można pominąć instalację dostawcy usługi VSS.

Aby ominąć instalację dostawcy usługi Azure Site Recovery VSS i ręcznie zainstalować dostawcę usługi Azure Site Recovery VSS po instalacji:

1. Zainstaluj usługę mobilności. Instalacja zakończy się niepowodzeniem na etapie: **Po instalacji konfiguracji**.
1. Aby ominąć instalację usługi VSS:
   1. Otwórz katalog instalacji usługi azure site recovery mobility service znajdujący się pod adresem:

      _C:\Pliki programów (x86)\Odzyskiwanie witryny Microsoft Azure\agent_

   1. Zmodyfikuj skrypty instalacyjne dostawcy usługi Azure Site Recovery VSS _InMageVSSProvider_Install_ i _InMageVSSProvider_Uninstall.cmd,_ aby zawsze odnieść sukces, dodając następujące wiersze:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Wykonaj ręczną instalację agenta mobilności.
1. Gdy instalacja zakończy się pomyślnie i przejdzie do następnego kroku, **Skonfiguruj**, usuń dodane wiersze.
1. Aby zainstalować dostawcę usługi VSS, otwórz wiersz polecenia jako administrator i uruchom następujące polecenie:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Sprawdź, czy dostawca usługi VSS usługi Azure Site Recovery jest zainstalowany jako usługa w usługach systemu Windows. Otwórz program MMC usługi składników, aby potwierdzić, że dostawca usługi VSS znajduje się na liście.
1. Jeśli instalacja dostawcy usługi VSS nadal kończy się niepowodzeniem, należy pracować z pomocą techniczną w celu rozwiązania problemów z uprawnieniami w interfejsie programowania aplikacji kryptograficznych (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalacja dostawcy usługi VSS kończy się niepowodzeniem, ponieważ usługa klastrowania jest włączona na komputerze nieklastrowym

Ten problem powoduje, że instalacja agenta mobilności usługi Azure Site Recovery nie powiodła się podczas instalacji dostawcy usługi Azure Site Recovery VSS. Błąd jest, ponieważ istnieje problem `COM+` z, który uniemożliwia instalację dostawcy usługi VSS.

### <a name="to-identify-the-issue"></a>Aby zidentyfikować problem

W dzienniku znajdującym się na serwerze konfiguracyjnym w _miejscu:\<C:\ProgramData\ASRSetupLogs\UploadedLogs data-godzina>UA_InstallLogFile.log_ znajdziesz następujący wyjątek:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Aby rozwiązać ten problem:

1. Sprawdź, czy ten komputer jest komputerem spoza klastra i czy składniki klastra nie są używane.
1. Jeśli składniki nie są używane, usuń składniki klastra z komputera.

## <a name="drivers-are-missing-on-the-source-server"></a>Na serwerze źródłowym brakuje sterowników

Jeśli instalacja agenta mobilności nie powiedzie się, sprawdź dzienniki w obszarze _C:\ProgramData\ASRSetupLogs,_ aby ustalić, czy w niektórych zestawach sterowania brakuje niektórych wymaganych sterowników.

Aby rozwiązać ten problem:

1. Za pomocą edytora `regedit.msc`rejestru, takich jak , otwórz rejestr.
1. Otwórz `HKEY_LOCAL_MACHINE\SYSTEM` węzeł.
1. W `SYSTEM` węźle zlokalizuj zestawy formantów.
1. Otwórz każdy zestaw sterowania i sprawdź, czy występują następujące sterowniki systemu Windows:

   * Atapi
   * Vmbus
   * Storflt ( Storflt )
   * Storvsc ( Storvsc )
   * Intelide ( Intelide )

1. Zainstaluj ponownie brakujące sterowniki.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](vmware-azure-tutorial.md) o konfigurowaniu odzyskiwania po awarii dla maszyn wirtualnych VMware.
