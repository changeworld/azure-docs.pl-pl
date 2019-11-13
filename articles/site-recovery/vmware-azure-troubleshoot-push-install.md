---
title: Rozwiązywanie problemów z instalacją wypychaną usługi mobilności za pomocą Azure Site Recovery f
description: Rozwiązywanie problemów z instalacją usług mobilności podczas włączania replikacji na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953773"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Rozwiązywanie problemów z instalacją wypychaną usługi mobilności 

Instalacja usługi mobilności to kluczowy krok podczas włączania replikacji. Powodzenie tego kroku zależy wyłącznie od wymagań wstępnych dotyczących spotkań i pracy z obsługiwanymi konfiguracjami. Najczęstsze problemy występujące podczas instalacji usługi mobilności są następujące:

* [Błędy poświadczeń/uprawnień](#credentials-check-errorid-95107--95108)
* [Błędy logowania](#login-failures-errorid-95519-95520-95521-95522)
* [Błędy łączności](#connectivity-failure-errorid-95117--97118)
* [Błędy udostępniania plików i drukarek](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Błędy usługi WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nieobsługiwane systemy operacyjne](#unsupported-operating-systems)
* [Nieobsługiwane konfiguracje rozruchu](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Błędy instalacji usługi VSS](#vss-installation-failures)
* [Nazwa urządzenia w konfiguracji GRUB zamiast identyfikatora UUID urządzenia](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Wolumin LVM](#lvm-support-from-920-version)
* [Ostrzeżenia dotyczące ponownego uruchamiania](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Po włączeniu replikacji Azure Site Recovery próbuje wypychania instalacji agenta usługi mobilności na maszynie wirtualnej. W ramach tego serwer konfiguracji próbuje nawiązać połączenie z maszyną wirtualną i skopiować agenta. Aby włączyć pomyślną instalację, postępuj zgodnie ze wskazówkami rozwiązywania problemów krok po kroku przedstawionych poniżej.

## <a name="credentials-check-errorid-95107--95108"></a>Sprawdzanie poświadczeń (ErrorID: 95107 & 95108)

* Sprawdź, czy konto użytkownika wybrane podczas włączania replikacji jest **prawidłowe, dokładne**.
* Azure Site Recovery wymaga konta **głównego** lub konta użytkownika z **uprawnieniami administratora** do przeprowadzenia instalacji wypychanej. W przeciwnym razie instalacja wypychana zostanie zablokowana na maszynie źródłowej.
  * W przypadku systemu Windows (**błąd 95107**) należy sprawdzić, czy konto użytkownika ma dostęp administracyjny na maszynie źródłowej.
  * Jeśli nie korzystasz z konta domeny, musisz wyłączyć kontrolę dostępu użytkowników zdalnych na komputerze lokalnym.
    * Aby wyłączyć kontrolę dostępu użytkowników zdalnych, w obszarze HKEY_LOCAL_MACHINE klucz rejestru \SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, Dodaj nową wartość DWORD: LocalAccountTokenFilterPolicy. Ustaw wartość na 1. Aby wykonać ten krok, uruchom następujące polecenie w wierszu polecenia:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * W przypadku systemu Linux (**błąd 95108**) należy wybrać konto główne do pomyślnej instalacji agenta mobilności. Ponadto usługi SFTP powinny być uruchomione. Aby włączyć podsystem SFTP i uwierzytelnianie hasłem w pliku sshd_config:
    1. Zaloguj się jako katalog główny.
    2. Przejdź do pliku/etc/ssh/sshd_config, Znajdź wiersz zaczynający się od PasswordAuthentication.
    3. Usuń komentarz z wiersza i zmień wartość na tak.
    4. Znajdź wiersz zaczynający się od podsystemu i usuń znaczniki komentarza z wiersza.
    5. Uruchom ponownie usługę SSHD.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi w [tym miejscu](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Błąd niewystarczających uprawnień (ErrorID: 95517)

Gdy użytkownik wybrany do zainstalowania agenta mobilności nie ma uprawnień administratora, serwer konfiguracji/serwer przetwarzania skalowalnego w poziomie nie będzie mógł skopiować oprogramowania agenta mobilności na maszynie źródłowej. Dlatego ten błąd jest wynikiem błędu odmowy dostępu. Upewnij się, że konto użytkownika ma uprawnienia administratora.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi w [tym miejscu](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Błąd niewystarczających uprawnień (ErrorID: 95518)

Jeśli ustanowienie relacji zaufania domeny między domeną podstawową i stacją roboczą nie powiedzie się podczas próby zalogowania się na maszynie źródłowej, Instalacja agenta mobilności nie powiedzie się z powodu błędu o IDENTYFIKATORze 95518 Upewnij się, że konto użytkownika używane do instalowania agenta mobilności ma uprawnienia administracyjne do logowania się za pomocą domeny podstawowej maszyny źródłowej.

Jeśli chcesz zmodyfikować poświadczenia wybranego konta użytkownika, postępuj zgodnie z instrukcjami podanymi w [tym miejscu](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Niepowodzenia logowania (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Poświadczenia konta użytkownika zostały wyłączone (ErrorID: 95519)

Konto użytkownika wybrane podczas włączania replikacji zostało wyłączone. Aby włączyć konto użytkownika, zapoznaj się z artykułem [tutaj](https://aka.ms/enable_login_user) lub uruchom następujące polecenie, *zastępując tekstową nazwę użytkownika wartością* rzeczywistą.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Poświadczenia zostały zablokowane z powodu wielu nieudanych prób zalogowania (ErrorID: 95520)

Wiele nieudanych prób uzyskania dostępu do maszyny spowoduje zablokowanie konta użytkownika. Przyczyną błędu może być:

* Poświadczenia podane podczas instalacji konfiguracji są nieprawidłowe lub
* Konto użytkownika wybrane podczas włączania replikacji jest nieprawidłowe

Zmodyfikuj wybrane poświadczenia, postępując zgodnie z instrukcjami podanymi w [tym miejscu](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) , a następnie spróbuj ponownie wykonać operację po pewnym czasie.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Serwery logowania nie są dostępne na maszynie źródłowej (ErrorID: 95521)

Ten błąd występuje, gdy serwery logowania nie są dostępne na maszynie źródłowej. Niedostępność serwerów logowania spowoduje niepowodzenie żądania logowania, a tym samym nie można zainstalować agenta mobilności. W celu pomyślnego zalogowania się upewnij się, że serwery logowania są dostępne na maszynie źródłowej i uruchom usługę logowania. Aby uzyskać szczegółowe instrukcje, zobacz artykuł KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) ERR MSG: nie ma obecnie dostępnych serwerów logowania.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Usługa logowania nie jest uruchomiona na maszynie źródłowej (ErrorID: 95522)

Usługa logowania nie jest uruchomiona na maszynie źródłowej i spowodowała niepowodzenie żądania logowania. Dlatego nie można zainstalować agenta mobilności. Aby rozwiązać ten problem, upewnij się, że usługa logowania jest uruchomiona na maszynie źródłowej w celu pomyślnego zalogowania. Aby uruchomić usługę logowania, uruchom polecenie "net start Logon" z wiersza polecenia lub Uruchom usługę "NetLogon" w Menedżerze zadań.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Niepowodzenie łączności (ErrorID: 95117 & 97118)**

Serwer konfiguracji/serwer przetwarzania skalowalnego w poziomie próbuje nawiązać połączenie ze źródłową maszyną wirtualną w celu zainstalowania agenta mobilności. Ten błąd występuje, gdy maszyna źródłowa jest nieosiągalna z powodu problemów z łącznością sieciową. Aby rozwiązać ten problem,

* Upewnij się, że można wysłać polecenie ping do maszyny źródłowej z serwera konfiguracji. Jeśli podczas włączania replikacji wybrano serwer przetwarzania skalowalny w poziomie, upewnij się, że można wysłać polecenie ping do maszyny źródłowej z serwera przetwarzania.
  * W wierszu polecenia serwera źródłowego Użyj programu Telnet, aby wysłać polecenie ping do serwera konfiguracji/skalowalnego w poziomie serwera przetwarzania z portem https (135), jak pokazano poniżej, aby sprawdzić, czy występują problemy z łącznością sieciową lub problemy z blokowaniem portów zapory.

     `telnet <CS/ scale-out PS IP address> <135>`
* Ponadto w przypadku **maszyny wirtualnej z systemem Linux**
  * Sprawdź, czy są zainstalowane najnowsze pakiety OpenSSH, OpenSSH-Server i OpenSSL.
  * Sprawdź i upewnij się, że Secure Shell (SSH) jest włączony i jest uruchomiony na porcie 22.
  * Usługa SFTP powinna być uruchomiona. Aby włączyć podsystem SFTP i uwierzytelnianie hasła w pliku sshd_config,
    * Zaloguj się jako katalog główny.
    * Przejdź do pliku/etc/ssh/sshd_config, Znajdź wiersz zaczynający się od PasswordAuthentication.
    * Usuń komentarz z wiersza i zmień wartość na tak
    * Znajdź wiersz zaczynający się od podsystemu i usuń znaczniki komentarza z wiersza
    * Uruchom ponownie usługę SSHD.
* Próba nawiązania połączenia nie powiodła się w przypadku braku prawidłowej odpowiedzi po upływie określonego czasu lub nawiązanie połączenia nie powiodło się, ponieważ podłączony host nie odpowiedział.
* Może to być problem związany z łącznością/siecią/domeną. Może to być również spowodowane przez Rozwiązywanie problemów z rozpoznawaniem nazw DNS lub wyczerpaniem portów TCP. Sprawdź, czy w Twojej domenie występują jakieś znane problemy.

## <a name="connectivity-failure-errorid-95523"></a>Niepowodzenie łączności (ErrorID: 95523)

Ten błąd występuje, gdy sieć, w której znajduje się maszyna źródłowa, nie została znaleziona lub mogła zostać usunięta lub nie jest już dostępna. Jedynym sposobem na rozwiązanie tego błędu jest upewnienie się, że sieć istnieje.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Sprawdzenie usług udostępniania plików i drukarek (ErrorID: 95105 & 95106)

Po sprawdzeniu łączności Sprawdź, czy na maszynie wirtualnej jest włączona usługa udostępniania plików i drukarek. Te ustawienia są wymagane do kopiowania agenta mobilności na maszynę źródłową.

W przypadku **systemu windows 2008 R2 i wcześniejszych wersji**,

* Aby włączyć udostępnianie plików i drukarek za poorednictwem zapory systemu Windows,
  * Otwórz Panel sterowania — > System i zabezpieczenia — > Zapora systemu Windows — > w lewym okienku kliknij pozycję Ustawienia zaawansowane — > kliknij pozycję Reguły ruchu przychodzącego w drzewie konsoli.
  * Zlokalizuj reguły udostępniania plików i drukarek (NB — sesja-in) i udostępnianie plików i drukarek (ruch przychodzący SMB). Dla każdej reguły kliknij prawym przyciskiem myszy regułę, a następnie kliknij polecenie **Włącz regułę**.
* Aby włączyć udostępnianie plików zasady grupy,
  * Przejdź do menu Start, wpisz GPMC. msc i Search.
  * W okienku nawigacji otwórz następujące foldery: lokalne zasady komputera, Konfiguracja użytkownika, Szablony administracyjne, składniki systemu Windows i udostępnianie sieciowe.
  * W okienku szczegółów kliknij dwukrotnie pozycję **uniemożliwiaj użytkownikom udostępnianie plików w ramach ich profilu**. Aby wyłączyć ustawienie zasady grupy i umożliwić użytkownikowi udostępnianie plików, kliknij pozycję wyłączone. Kliknij przycisk OK, aby zapisać zmiany. Aby dowiedzieć się więcej, zobacz [Włączanie lub wyłączanie udostępniania plików przy użyciu zasady grupy](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

W przypadku **nowszych wersji**postępuj zgodnie z instrukcjami podanymi w temacie [Instalowanie usługi mobilności na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych](vmware-azure-install-mobility-service.md) , aby włączyć udostępnianie plików i drukarek.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Sprawdzanie konfiguracji Instrumentacja zarządzania Windows (WMI) (kod błędu: 95103)

Po sprawdzeniu usług plików i drukarek Włącz usługę WMI dla profilów prywatnych, publicznych i domeny za pomocą zapory. Te ustawienia są wymagane do ukończenia zdalnego wykonywania na maszynie źródłowej. Aby włączyć,

* Przejdź do panelu sterowania, kliknij pozycję Zabezpieczenia, a następnie kliknij pozycję Zapora systemu Windows.
* Kliknij pozycję Zmień ustawienia, a następnie kliknij kartę Wyjątki.
* W oknie wyjątki zaznacz pole wyboru dla Instrumentacja zarządzania Windows (WMI), aby włączyć ruch usługi WMI przez zaporę. 

Ruch usługi WMI można także włączyć przez zaporę w wierszu polecenia. Użyj poniższego polecenia `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Inne artykuły dotyczące rozwiązywania problemów z usługą WMI można znaleźć w następujących artykułach.

* [Podstawowe testowanie WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Rozwiązywanie problemów z usługą WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Rozwiązywanie problemów przy użyciu skryptów WMI i usług WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nieobsługiwane systemy operacyjne

Inną najbardziej typową przyczyną niepowodzenia może być nieobsługiwany system operacyjny. Upewnij się, że korzystasz z obsługiwanej wersji systemu operacyjnego/jądra w celu pomyślnej instalacji usługi mobilności. Należy unikać używania prywatnych poprawek.
Aby wyświetlić listę systemów operacyjnych i wersje jądra obsługiwane przez Azure Site Recovery, zapoznaj się z [dokumentem dotyczącym macierzy pomocy technicznej](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nieobsługiwane konfiguracje dysków rozruchowych (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Woluminy rozruchowe i systemowe nie są tym samym dyskiem (ErrorID: 95309)

Przed 9,20 wersja, partycje rozruchowe i systemowe na różnych dyskach były nieobsługiwaną konfiguracją. W [wersji 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana. Użyj najnowszej wersji, aby uzyskać pomoc techniczną.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Dysk rozruchowy jest niedostępny (ErrorID: 95310)

Nie można chronić maszyny wirtualnej bez dysku rozruchowego. Ma to na celu zapewnienie płynnego odzyskiwania maszyny wirtualnej podczas pracy w trybie failover. Brak dysku rozruchowego powoduje niepowodzenie rozruchu komputera po przejściu w tryb failover. Upewnij się, że maszyna wirtualna zawiera dysk rozruchowy, a następnie spróbuj ponownie wykonać operację. Należy również pamiętać, że wiele dysków rozruchowych na tym samym komputerze nie jest obsługiwana.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Na maszynie źródłowej znajduje się wiele dysków rozruchowych (ErrorID: 95311)

Maszyna wirtualna z wieloma dyskami rozruchowymi nie jest [obsługiwaną konfiguracją](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partycja systemowa na wielu dyskach (ErrorID: 95313)

Przed 9,20 wersja, partycja główna lub wolumin określony na wielu dyskach była nieobsługiwaną konfiguracją. W [wersji 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ta konfiguracja jest obsługiwana. Użyj najnowszej wersji, aby uzyskać pomoc techniczną.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Włączenie ochrony nie powiodło się, ponieważ nazwa urządzenia wymieniona w konfiguracji GRUB zamiast identyfikatora UUID (ErrorID: 95320)

**Możliwa przyczyna:** </br>
Pliki konfiguracji GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" lub "/etc/default/grub") mogą zawierać wartość parametrów **root** i **Resume** jako rzeczywiste nazwy urządzeń zamiast UUID. Site Recovery zezwala na metodę UUID, ponieważ nazwa urządzeń może ulec zmianie po ponownym uruchomieniu maszyny wirtualnej, ponieważ maszyna wirtualna może nie zostać utworzona z tą samą nazwą w przypadku przejścia w tryb failover, co spowodowało problemy. Na przykład: </br>


- Poniższy wiersz pochodzi z pliku GRUB **/Boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** powitalny = ciche ciche showopts*


- Następujący wiersz pochodzi z pliku GRUB **/boot/grub/menu.lst**
  *jądra/Boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** powitalnego = ciche crashkernel = 256M-: 128M showopts VGA = 0x314*

Jeśli zobaczysz pogrubiony ciąg powyżej, GRUB ma rzeczywiste nazwy urządzeń dla parametrów "root" i "resume" zamiast identyfikatora UUID.
 
**Jak naprawić:**<br>
Nazwy urządzeń należy zamienić na odpowiedni identyfikator UUID.<br>


1. Znajdź identyfikator UUID urządzenia, wykonując polecenie "blkid \<Device Name >". Na przykład:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Teraz Zastąp nazwę urządzenia identyfikatorem UUID w formacie takim jak "root = UUID =\<UUID >". Na przykład jeśli zamienimy nazwy urządzeń z identyfikatorem UUID dla certyfikatu głównego i wznowienia wymienionego powyżej w plikach "/Boot/grub2/grub.cfg", "/Boot/grub2/grub.cfg" lub "/etc/default/grub:, a następnie linie w plikach wyglądają jak. <br>
   *jądro/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433B-431b-9ca1-4dd2f6f74f6b** powitalny = cichy crashkernel = 256M-: 128M showopts VGA = 0x314*
3. Ponownie uruchom ochronę

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalacja usługi mobilności została ukończona z ostrzeżeniem o ponownym uruchomieniu (ErrorID: 95265 & 95266)

Usługa mobilności Site Recovery ma wiele składników, z których jeden jest nazywany sterownikiem filtru. Sterownik filtru jest ładowany do pamięci systemowej tylko w momencie ponownego uruchomienia systemu. Oznacza to, że poprawki sterownika filtru mogą być zrealizowane tylko w przypadku załadowania nowego sterownika filtru; może się to zdarzyć tylko w momencie ponownego uruchomienia systemu.

**Należy zauważyć** , że jest to ostrzeżenie, a istniejąca replikacja będzie działała nawet po nowej aktualizacji agenta. Możesz wybrać opcję ponownego uruchomienia w dowolnym momencie, aby uzyskać korzyści wynikające z nowego sterownika filtru, ale jeśli nie uruchomisz ponownie starego sterownika filtru, będzie nadal działać. W związku z tym po aktualizacji bez ponownego uruchomienia, oprócz sterownika filtru, **korzyści z innych ulepszeń i poprawek w usłudze mobilności są realizowane**. Dlatego nie jest konieczne ponowne uruchomienie komputera po każdym uaktualnieniu. Aby uzyskać informacje o tym, kiedy jest wymagany ponowny rozruch, należy ustawić [ponowny rozruch maszyny źródłowej po sekcji uaktualnienie agenta mobilności](https://aka.ms/v2a_asr_reboot) w artykule aktualizacje usługi w Azure Site Recovery.

> [!TIP]
>Aby poznać najlepsze rozwiązania dotyczące planowania uaktualnień w oknie obsługi, zapoznaj się z tematem [Obsługa najnowszych wersji systemu operacyjnego/jądra](https://aka.ms/v2a_asr_upgrade_practice) w artykule aktualizacje usługi w Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Obsługa LVM w wersji 9,20

Przed 9,20 wersja LVM była obsługiwana tylko w przypadku dysków z danymi. /Boot powinna znajdować się na partycji dysku, a nie na woluminie LVM.

[Wersja 9,20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) [dysku systemu operacyjnego w systemie LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) jest obsługiwana. Użyj najnowszej wersji, aby uzyskać pomoc techniczną.

## <a name="insufficient-space-errorid-95524"></a>Niewystarczająca ilość miejsca (ErrorID: 95524)

Gdy Agent mobilności jest kopiowany do maszyny źródłowej, wymagany jest co najmniej 100 MB wolnego miejsca. Upewnij się, że maszyna źródłowa ma wymaganą ilość wolnego miejsca, a następnie spróbuj ponownie wykonać operację.

## <a name="vss-installation-failures"></a>Błędy instalacji usługi VSS

Instalacja usługi VSS jest częścią instalacji agenta mobilności. Ta usługa jest używana w procesie generowania punktów odzyskiwania spójnych z aplikacjami. Błędy podczas instalacji usługi VSS mogą wystąpić z wielu powodów. Aby zidentyfikować dokładne błędy, zapoznaj się z **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Poniżej przedstawiono kilka typowych błędów i kroków rozwiązywania.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Błąd VSS-2147023170 [0x800706BE]-Exit Code 511

Ten problem jest najczęściej widoczny w przypadku blokowania przez oprogramowanie antywirusowe operacji Azure Site Recovery usług. Aby rozwiązać ten problem:

1. Wyklucz wszystkie foldery wymienione [tutaj](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Postępuj zgodnie z wytycznymi opublikowanymi przez dostawcę oprogramowania antywirusowego w celu odblokowania rejestracji biblioteki DLL w systemie Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Błąd usługi VSS 7 [0x7] — kod zakończenia 511

Jest to błąd czasu wykonywania i jest spowodowany niewystarczającą ilością pamięci do zainstalowania usługi VSS. Należy zwiększyć ilość miejsca na dysku na pomyślne ukończenie tej operacji.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Błąd VSS-2147023824 [0x80070430]-Exit Code 517

Ten błąd występuje, gdy Azure Site Recovery usługi VSS Provider została [oznaczona do usunięcia](https://msdn.microsoft.com/library/ms838153.aspx). Spróbuj ręcznie zainstalować usługę VSS na maszynie źródłowej, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Błąd VSS-2147023841 [0x8007041F]-Exit Code 512

Ten błąd występuje, gdy Azure Site Recovery baza danych usługi VSS Provider jest [zablokowana](https://msdn.microsoft.com/library/ms833798.aspx). Spróbuj ręcznie zainstalować usługę VSS na maszynie źródłowej, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

W razie awarii Sprawdź, czy którykolwiek z programów antywirusowych lub innych usług nie jest w stanie "Uruchamianie". Może to spowodować zablokowanie usług bazy danych. Wystąpił błąd podczas instalowania dostawcy usługi VSS. Upewnij się, że żadna usługa nie jest w stanie "Start", a następnie ponów próbę wykonania powyższej operacji.

### <a name="vss-exit-code-806"></a>Kod zakończenia usługi VSS 806

Ten błąd występuje, gdy konto użytkownika używane do instalacji nie ma uprawnień do wykonania polecenia CSScript. Podaj odpowiednie uprawnienia do konta użytkownika, aby wykonać skrypt, a następnie spróbuj ponownie wykonać operację.

### <a name="other-vss-errors"></a>Inne błędy usługi VSS

Spróbuj ręcznie zainstalować usługę dostawcy VSS na maszynie źródłowej, uruchamiając następujący wiersz polecenia

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Błąd VSS — 0x8004E00F

Ten błąd jest zazwyczaj napotykany podczas instalacji agenta mobilności z powodu problemów z modelem DCOM, a model DCOM jest w stanie krytycznym.

Aby określić przyczynę błędu, należy wykonać poniższą procedurę.

**Sprawdzanie dzienników instalacji**

1. Otwórz dziennik instalacji znajdujący się pod adresem c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. Obecność następującego błędu wskazuje na ten problem:

    Trwa wyrejestrowywanie istniejącej aplikacji...  Tworzenie obiektu katalogu pobieranie kolekcji aplikacji 

    Porn

    - Kod błędu:-2147164145 [0x8004E00F]
    - Kod zakończenia: 802

Aby rozwiązać ten problem:

Skontaktuj się z [zespołem platformy Microsoft Windows](https://aka.ms/Windows_Support) , aby uzyskać pomoc w rozwiązywaniu problemu z modelem DCOM.

Po rozwiązaniu problemu z modelem DCOM ponownie zainstaluj Azure Site Recovery dostawcę usługi VSS przy użyciu następującego polecenia:
 
**C:\Program Files (x86) \Microsoft Azure Site Recovery\agent > "C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd**
  
Jeśli spójność aplikacji nie jest krytyczna dla wymagań związanych z odzyskiwaniem po awarii, można ominąć instalację dostawcy usługi VSS. 

Aby obejść Azure Site Recovery instalację dostawcy usługi VSS i ręcznie zainstalować dostawcę usług VSS Azure Site Recovery po instalacji:

1. Zainstaluj usługę mobilności. 
   > [!Note]
   > 
   > Instalacja zakończy się niepowodzeniem w kroku "po konfiguracji instalacji". 
2. Aby pominąć instalację usługi VSS:
   1. Otwórz katalog instalacji usługi mobilności Azure Site Recovery znajdujący się w lokalizacji:
   
      C:\Program Files (x86) \Microsoft Azure Site Recovery\agent
   2. Zmodyfikuj Azure Site Recovery skrypty instalacji dostawcy usługi VSS **nMageVSSProvider_Install** i **InMageVSSProvider_Uninstall. cmd** , aby zawsze kończyły się powodzeniem, dodając następujące wiersze:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Ponownie uruchom instalację agenta mobilności ręcznie. 
4. Po pomyślnym zakończeniu instalacji i przeniesieniu do następnego kroku **Skonfiguruj**, Usuń wiersze, które zostały dodane.
5. Aby zainstalować dostawcę usługi VSS, Otwórz wiersz polecenia jako administrator i uruchom następujące polecenie:
   
    **C:\Program Files (x86) \Microsoft Azure Site Recovery\agent >. \ InMageVSSProvider_Install. cmd**

9. Sprawdź, czy dostawca usługi VSS ASR jest zainstalowany jako usługa w usługach systemu Windows i Otwórz konsolę MMC usługi składnika, aby sprawdzić, czy jest wyświetlany Dostawca usługi VSS ASR.
10. Jeśli instalacja dostawcy usługi VSS kończy się niepowodzeniem, należy skontaktować się z CX, aby rozwiązać błędy uprawnień w CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Instalacja dostawcy VSS nie powiodła się z powodu włączenia usługi klastrowania na komputerze niebędącym klastrem

Ten problem powoduje niepowodzenie instalacji agenta mobilności Azure Site Recovery w trakcie instalacji dostawcy VSS usługi ASAzure Site Recovery z powodu problemu z modelem COM+, który uniemożliwia instalację dostawcy usługi VSS.
 
### <a name="to-identify-the-issue"></a>Aby zidentyfikować problem

W dzienniku znajdującym się na serwerze konfiguracji w lokalizacji C:\ProgramData\ASRSetupLogs\UploadedLogs\<Data/godzina > UA_InstallLogFile. log zostanie wyszukany następujący wyjątek:

Model COM+ nie może skontaktować się z Distributed Transaction Coordinatorem firmy Microsoft (wyjątek z HRESULT: 0x8004E00F)

Aby rozwiązać ten problem:

1.  Sprawdź, czy maszyna jest maszyną nieklastrowaną i czy składniki klastra nie są używane.
3.  Jeśli składniki nie są używane, Usuń składniki klastra z komputera.

## <a name="drivers-are-missing-on-the-source-server"></a>Na serwerze źródłowym brakuje sterowników

Jeśli instalacja agenta mobilności nie powiedzie się, Sprawdź dzienniki w obszarze C:\ProgramData\ASRSetupLogs, aby ustalić, czy w niektórych zestawach kontroli brakuje niektórych wymaganych sterowników.
 
Aby rozwiązać ten problem:
  
1. Korzystając z edytora rejestru, takiego jak regedit. msc, Otwórz Rejestr.
2. Otwórz HKEY_LOCAL_MACHINE węzeł \SYSTEM.
3. W węźle SYSTEM Zlokalizuj zestawy kontrolek.
4. Otwórz każdy zestaw kontrolek i sprawdź, czy są obecne następujące sterowniki systemu Windows:

   - Napęd
   - Vmbus
   - storflt
   - storvsc
   - Intelide
 
Zainstaluj ponownie wszystkie brakujące sterowniki.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak](vmware-azure-tutorial.md) skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych VMware.
