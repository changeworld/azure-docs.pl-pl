---
title: Rozwiązywanie problemów z agentem usługi Azure Backup
description: W tym artykule dowiesz się, jak rozwiązać problem z instalacją i rejestracją agenta usługi Azure Backup.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 24169356600c25e664221af397051bb0fec3e459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673091"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z agentem usług odzyskiwania platformy Microsoft Azure (MARS)

W tym artykule opisano sposób rozwiązywania błędów, które mogą być widoczne podczas konfiguracji, rejestracji, tworzenia kopii zapasowych i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów z agentem usług Azure Recovery Services (MARS) należy sprawdzić następujące kwestie:

- [Upewnij się, że agent MARS jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Upewnij się, że masz łączność sieciową między agentem MARS a platformą Azure.](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że mars jest uruchomiony (w konsoli usługi). Jeśli zajdzie taka potrzeba, uruchom ponownie i ponów próbę wykonania operacji.
- [Upewnij się, że w folderze magazynu dostępne jest od 5% do 10% wolnego miejsca na woluminie.](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe zakłóca](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)działanie usługi Azure Backup .
- Jeśli zaplanowana kopia zapasowa nie powiedzie się, ale ręczna kopia zapasowa działa, zobacz [Kopie zapasowe nie działają zgodnie z harmonogramem](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule).
- Upewnij się, że system operacyjny ma najnowsze aktualizacje.
- [Upewnij się, że nieobsługiwały dyski i pliki z nieobsługiwałymi atrybutami są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Upewnij się, że zegar w chronionym systemie jest skonfigurowany do poprawnej strefy czasowej.
- [Upewnij się, że program .NET Framework 4.5.2 lub nowszy jest zainstalowany na serwerze.](https://www.microsoft.com/download/details.aspx?id=30653)
- Jeśli próbujesz ponownie zarejestrować serwer w przechowalni:
  - Upewnij się, że agent został odinstalowany na serwerze i że został usunięty z portalu.
  - Użyj tego samego hasła, który był początkowo używany do rejestracji serwera.
- W przypadku kopii zapasowych w trybie offline przed uruchomieniem kopii zapasowej należy upewnić się, że program Azure PowerShell 3.7.0 jest zainstalowany zarówno na komputerze źródłowym, jak i na komputerze kopii zapasowej.
- Jeśli agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure, zobacz [ten artykuł](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie:** Nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (ID: 34513)

| Przyczyna | Zalecane działania |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu mogą być uszkodzone lub mogły wygasnąć. (Na przykład mogły zostać pobrane ponad 48 godzin przed rejestracją).| Pobierz nowe poświadczenia z magazynu usług odzyskiwania w witrynie Azure portal. (Zobacz krok 6 w sekcji [Pobieranie agenta MARS).](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) Następnie wykonaj następujące kroki, stosownie do przypadku: <ul><li> Jeśli masz już zainstalowany i zarejestrowany mars, otwórz konsolę programu MMC programu Microsoft Azure Backup Agent, a następnie wybierz pozycję **Zarejestruj serwer** w okienku **Akcje,** aby zakończyć rejestrację przy użyciu nowych poświadczeń. <br/> <li> Jeśli nowa instalacja nie powiedzie się, spróbuj ponownie zainstalować przy użyciu nowych poświadczeń.</ul> **Uwaga:** Jeśli pobrano wiele plików poświadczeń magazynu, tylko najnowszy plik jest ważny przez następne 48 godzin. Zaleca się pobranie nowego pliku poświadczeń magazynu.
| **Serwer proxy/zapora blokuje rejestrację** <br/>lub <br/>**Brak połączenia z Internetem** <br/><br/> Jeśli komputer lub serwer proxy ma ograniczoną łączność z Internetem i nie zapewnisz dostępu do niezbędnych adresów URL, rejestracja zakończy się niepowodzeniem.| Wykonaj następujące kroki:<br/> <ul><li> Współpracuj z zespołem IT, aby zapewnić, że system ma łączność z Internetem.<li> Jeśli nie masz serwera proxy, upewnij się, że opcja serwera proxy nie jest zaznaczona podczas rejestracji agenta. [Sprawdź ustawienia serwera proxy](#verifying-proxy-settings-for-windows).<li> Jeśli masz serwer zapory/serwera proxy, skontaktuj się z zespołem sieciowym, aby upewnić się, że te adresy URL i adresy IP mają dostęp:<br/> <br> **adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Adresy IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Spróbuj zarejestrować się ponownie po wykonaniu powyższych kroków rozwiązywania problemów.<br></br> Jeśli łączysz się za pośrednictwem usługi Azure ExpressRoute, upewnij się, że ustawienia są skonfigurowane zgodnie z opisem w [pomocy technicznej usługi Azure ExpressRoute.](backup-support-matrix-mars-agent.md#azure-expressroute-support)
| **Oprogramowanie antywirusowe blokuje rejestrację** | Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, dodaj do skanowania antywirusowego niezbędne reguły wykluczenia w poszukiwaniu tych plików i folderów: <br/><ul> <li> CBengine.exe <li> Csc.exe<li> Folder zarysowania. Jego domyślną lokalizacją jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Folder pojemnika w witrynie C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Zalecenia dodatkowe

- Przejdź do C:/Windows/Temp i sprawdź, czy istnieje więcej niż 60 000 lub 65 000 plików z rozszerzeniem .tmp. Jeśli tak, usuń te pliki.
- Upewnij się, że data i godzina urządzenia są zgodne z lokalną strefą czasową.
- Upewnij się, że [te witryny](install-mars-agent.md#verify-internet-access) są dodawane do zaufanych witryn w programie Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla systemu Windows

1. Pobierz PsExec ze strony [Sysinternals.](https://docs.microsoft.com/sysinternals/downloads/psexec)
1. Uruchom `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` z wiersza polecenia z podwyższonym poziomem uprawnień.

   To polecenie spowoduje otwarcie programu Internet Explorer.
1. Przejdź do**opcji** >  **Narzędzia** > internetowe Ustawienia**sieci LAN****Połączenia** > .
1. Sprawdź ustawienia serwera proxy dla konta systemowego.
1. Jeśli nie skonfigurowano serwera proxy i podano szczegóły serwera proxy, usuń szczegóły.
1. Jeśli serwer proxy jest skonfigurowany, a szczegóły serwera proxy są niepoprawne, upewnij się, że szczegóły adresu IP i **portu** **serwera proxy** są poprawne.
1. Zamknij program Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń przechowalni

| Błąd   | Zalecane działania |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń przechowalni. (ID: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu przy użyciu innej przeglądarki lub wykonaj następujące czynności: <ul><li> Uruchom program Internet Explorer. Wybierz F12. </li><li> Przejdź do karty **Sieć** i wyczyść pamięć podręczną i pliki cookie. </li> <li> Odśwież stronę.<br></li></ul> <li> Sprawdź, czy subskrypcja jest wyłączona/wygasła.<br></li> <li> Sprawdź, czy jakakolwiek reguła zapory blokuje pobieranie. <br></li> <li> Upewnij się, że limit nie został wyczerpany w przechowalni (50 maszyn na przechowalnię).<br></li>  <li> Upewnij się, że użytkownik ma uprawnienia usługi Azure Backup, które są wymagane do pobrania poświadczeń magazynu i zarejestrowania serwera w magazynie. Zobacz [Zarządzanie punktami odzyskiwania kopii zapasowej usługi Azure za pomocą kontroli dostępu opartej na rolach.](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Błąd  | Możliwa przyczyna | Zalecane działania |
| ---     | ---     | ---    |
| <br /><ul><li>Agent usługi odzyskiwania platformy Microsoft Azure nie może połączyć się z pomocą kopii zapasowej platformy Microsoft Azure. (ID: 100050) Sprawdź ustawienia sieciowe i upewnij się, że możesz połączyć się z Internetem.<li>(407) Wymagane uwierzytelnianie serwera proxy. |Serwer proxy blokuje połączenie. |  <ul><li>W programie Internet Explorer przejdź do **pozycji Narzędzia** > **opcje** > internetowe**Zabezpieczenia** > **Internetu**. Wybierz **pozycję Poziom niestandardowy** i przewiń w dół do sekcji **Pobieranie pliku.** Wybierz pozycję **Włącz**.<p>Może być również dodanie [adresów URL i adresów IP](install-mars-agent.md#verify-internet-access) do zaufanych witryn w programie Internet Explorer.<li>Zmień ustawienia, aby używać serwera proxy. Następnie podaj szczegóły serwera proxy.<li> Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te [adresy URL i adresy IP.](install-mars-agent.md#verify-internet-access) <li>Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, wyklucz te pliki ze skanowania antywirusowego: <ul><li>CBEngine.exe (zamiast dpmra.exe).<li>CSC.exe (związane z .NET Framework). Istnieje csc.exe dla każdej wersji programu .NET Framework zainstalowanej na serwerze. Wyklucz pliki CSC.exe dla wszystkich wersji programu .NET Framework na serwerze, którego dotyczy problem. <li>Folder odzdrapania lub lokalizacja pamięci podręcznej. <br>Domyślną lokalizacją folderu scratch lub ścieżki pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Folder pojemnika w witrynie C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Błąd | Możliwe przyczyny | Zalecane działania |
| ---     | ---     | ---    |
| <br />Nie można ustawić klucza szyfrowania dla bezpiecznych kopii zapasowych. Aktywacja nie powiodła się całkowicie, ale hasło szyfrowania zostało zapisane w następującym pliku. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfiguracji hasło zostało uszkodzone.| Wyrejestruj serwer z przechowalni i zarejestruj go ponownie za pomocą nowego hasła.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie zakończona

| Błąd  | Możliwe przyczyny | Zalecane działania |
|---------|---------|---------|
|<br />Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft.     | <li> Folder scratch znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder zarysowania został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup.KEK.         | <li>Uaktualnienie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta MARS.<li>Przenieś folder magazynu lub lokalizację pamięci podręcznej na wolumin z wolnym miejscem, który wynosi od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, zapoznaj się z czynnościami [typowymi pytaniami dotyczącymi tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślną lokalizacją folderu scratch lub ścieżki pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostało prawidłowo skonfigurowane

| Błąd  | Możliwe przyczyny | Zalecane działania |
|---------|---------|---------|
| <br />Błąd 34506. Hasło szyfrowania przechowywane na tym komputerze nie jest poprawnie skonfigurowane.    | <li> Folder scratch znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder zarysowania został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup.KEK.        | <li>Uaktualnienie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta MARS.<li>Przenieś folder magazynu lub lokalizację pamięci podręcznej na wolumin z wolnym miejscem, który wynosi od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, zapoznaj się z czynnościami [typowymi pytaniami dotyczącymi tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślną lokalizacją folderu scratch lub ścieżki pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Kopie zapasowe nie są uruchamiane zgodnie z harmonogramem

Jeśli zaplanowane kopie zapasowe nie są wyzwalane automatycznie, ale ręczne kopie zapasowe działają poprawnie, spróbuj wykonać następujące czynności:

- Upewnij się, że harmonogram tworzenia kopii zapasowych systemu Windows Server nie jest w konflikcie z harmonogramem tworzenia kopii zapasowych plików i folderów platformy Azure.

- Upewnij się, że stan kopii zapasowej online jest ustawiony na **Włącz**. Aby zweryfikować stan, wykonaj następujące kroki:

  1. W obszarze Harmonogram zadań rozwiń pozycję **Microsoft** i wybierz pozycję **Kopia zapasowa online**.
  1. Kliknij dwukrotnie pozycję **Microsoft-OnlineBackup** i przejdź do karty **Wyzwalacze.**
  1. Sprawdź, czy stan jest ustawiony na **Włączone**. Jeśli tak nie jest, wybierz pozycję **Edytuj,** wybierz **pozycję Włączone**, a następnie wybierz przycisk **OK**.

- Upewnij się, że konto użytkownika wybrane do uruchomienia zadania to grupa **SYSTEM** lub **Administratorzy lokalni** na serwerze. Aby zweryfikować konto użytkownika, przejdź do karty **Ogólne** i sprawdź opcje **zabezpieczeń.**

- Upewnij się, że program PowerShell 3.0 lub nowsza jest zainstalowany na serwerze. Aby sprawdzić wersję programu PowerShell, uruchom `Major` to polecenie i sprawdź, czy numer wersji wynosi 3 lub więcej:

  `$PSVersionTable.PSVersion`

- Upewnij się, że `PSMODULEPATH` ta ścieżka jest częścią zmiennej środowiskowej:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasada wykonywania programu `LocalMachine` PowerShell `restricted`jest ustawiona na polecenie , polecenie cmdlet programu PowerShell wyzwalające zadanie tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom te polecenia w trybie podwyższonego poziomu, aby `Unrestricted` `RemoteSigned`sprawdzić i ustawić zasady wykonywania na jedną lub:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Upewnij się, że nie brakuje lub uszkodzone pliki modułu Programu PowerShell MSOnlineBackup. Jeśli brakuje lub uszkodzone pliki, wykonaj następujące kroki:

  1. Z dowolnego komputera, na którego ma działającego poprawnie agenta MARS, skopiuj folder MSOnlineBackup z folderu C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na problematycznym komputerze wklej skopiowane pliki w tej samej lokalizacji folderu (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Jeśli na komputerze znajduje się już folder MSOnlineBackup, wklej pliki do niego lub zastąp istniejące pliki.

> [!TIP]
> Aby upewnić się, że zmiany są stosowane spójnie, uruchom ponownie serwer po wykonaniu powyższych kroków.

## <a name="troubleshoot-restore-problems"></a>Rozwiązywanie problemów z przywracaniem

Usługa Azure Backup może nie pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. I mogą pojawić się komunikaty o błędach podczas procesu. Aby rozpocząć normalne odzyskiwanie, wykonaj następujące kroki:

1. Anuluj proces instalacji, jeśli jest uruchomiony przez kilka minut.

2. Sprawdź, czy masz najnowszą wersję agenta kopii zapasowej. Aby sprawdzić wersję, w okienku **Akcje** konsoli MARS wybierz pozycję **Informacje o programie Microsoft Azure Recovery Services Agent**. Upewnij się, że numer **wersji** jest równy lub wyższy niż wersja wymieniona w [tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Wybierz to łącze, aby [pobrać najnowszą wersję](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Przejdź do**kontrolerów magazynu** **Menedżera** > urządzeń i znajdź **inicjatora iSCSI firmy Microsoft**. Jeśli go znajdziesz, przejdź bezpośrednio do kroku 7.

4. Jeśli nie możesz zlokalizować usługi inicjatora iSCSI firmy Microsoft, spróbuj znaleźć wpis w obszarze > **Kontrolery magazynu** **Menedżera urządzeń**o nazwie **Nieznane urządzenie** o identyfikatorze sprzętowym **ROOT\ISCSIPRT**.

5. Kliknij prawym przyciskiem myszy **pozycję Nieznane urządzenie** i wybierz pozycję **Aktualizuj oprogramowanie sterownika**.

6. Zaktualizuj sterownik, wybierając opcję **Wyszukaj automatycznie zaktualizowane oprogramowanie sterownika**. Ta aktualizacja powinna zmienić **nieznane urządzenie** na **inicjatora iSCSI firmy Microsoft:**

    ![Zrzut ekranu przedstawiający usługę Azure Backup Device Manager z wyróżnionymi kontrolerami magazynu](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Przejdź do usługi**inicjatora iSCSI** **usługi Menedżera** > zadań **(lokalna)** > firmy Microsoft:

    ![Zrzut ekranu przedstawiający usługę Azure Backup Task Manager z wyróżnioną pojedonową połączoną połączoną połączoną połączoną z usługami (lokalnymi)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę i wybierz pozycję **Zatrzymaj**. Następnie kliknij go ponownie prawym przyciskiem myszy i wybierz polecenie **Start**.

9. Ponów próbę odzyskania za pomocą [narzędzia Natychmiastowe przywracanie](backup-instant-restore-capability.md).

Jeśli odzyskiwanie nadal nie powiedzie się, uruchom ponownie serwer lub klienta. Jeśli nie chcesz ponownie uruchomić lub jeśli odzyskiwanie nadal kończy się niepowodzeniem nawet po ponownym uruchomieniu serwera, spróbuj [odzyskać z innego komputera](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Rozwiązywanie problemów z pamięcią podręczną

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem, jeśli folder pamięci podręcznej (nazywany również folderem magazynu) jest niepoprawnie skonfigurowany, brakuje wymagań wstępnych lub ma ograniczony dostęp.

### <a name="prerequisites"></a>Wymagania wstępne

Aby operacje agenta MARS powiodły się, folder pamięci podręcznej musi być zgodny z poniższymi wymaganiami:

- [Upewnij się, że w folderze zdrapki jest dostępne od 5% do 10% wolnego miejsca na woluminie](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Upewnij się, że lokalizacja folderu zarysowania jest prawidłowa i dostępna](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Upewnij się, że atrybuty plików w folderze pamięci podręcznej są obsługiwane](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Upewnij się, że przydzielone miejsce do przechowywania kopii w tle jest wystarczające dla procesu tworzenia kopii zapasowej](#increase-shadow-copy-storage)
- [Upewnij się, że nie ma żadnych innych procesów (np. oprogramowania antywirusowego) ograniczających dostęp do folderu pamięci podręcznej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zwiększenie pamięci masowej kopii w tle

Operacje tworzenia kopii zapasowej mogą zakończyć się niepowodzeniem, jeśli nie ma wystarczającej ilości miejsca do przechowywania kopii w tle wymagane do ochrony źródła danych. Aby rozwiązać ten problem, zwiększ miejsce do magazynowania kopii w tle na chronionym woluminie przy użyciu vssadmin, jak pokazano poniżej:

- Sprawdź bieżącą przestrzeń magazynu w tle z wiersza polecenia z podwyższonym poziomem uprawnień:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zwiększ przestrzeń pamięci w tle za pomocą poniższego polecenia:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Inny proces lub oprogramowanie antywirusowe blokujące dostęp do folderu pamięci podręcznej

Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, dodaj do skanowania antywirusowego niezbędne reguły wykluczenia w poszukiwaniu tych plików i folderów:  

- Folder zarysowania. Jego domyślną lokalizacją jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- Folder pojemnika w witrynie C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- Csc.exe

## <a name="common-issues"></a>Typowe problemy

W tej sekcji opisano typowe błędy, które można napotkać podczas korzystania z agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Komunikat o błędzie | Zalecana akcja |
-- | --
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do sumy kontrolnej kopii zapasowej przechowywanej w lokalizacji tymczasowej | Aby rozwiązać ten problem, wykonaj poniższe czynności i uruchom ponownie serwer <br/> - [Sprawdź, czy istnieje program antywirusowy lub inne procesy blokujące pliki lokalizacji zdrapki](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja zarysowania jest prawidłowa i dostępna dla mars agenta.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Komunikat o błędzie | Zalecana akcja |
-- | --
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do lokalizacji tymczasowej w celu zainicjowania wirtualnego dysku twardego | Aby rozwiązać ten problem, wykonaj poniższe czynności i uruchom ponownie serwer <br/> - [Sprawdź, czy istnieje program antywirusowy lub inne procesy blokujące pliki lokalizacji zdrapki](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja zarysowania jest prawidłowa i dostępna dla mars agenta.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace (Przestrzeń Dyskowa SalLow

Komunikat o błędzie | Zalecana akcja |
-- | --
Kopia zapasowa nie powiodła się z powodu niewystarczającej ilości woluminu, w którym znajduje się folder scratch | Aby rozwiązać ten problem, sprawdź poniższe kroki i ponów próbę wykonania operacji:<br/>- [Upewnij się, że agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Weryfikowanie i rozwiązywanie problemów z pamięcią masową, które mają wpływ na miejsce w zakresie tworzenia kopii zapasowych](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Komunikat o błędzie | Zalecana akcja |
-- | --
Nie można odnaleźć zmian w pliku. Taka sytuacja może mieć różne przyczyny. Ponów próbę wykonania operacji | Aby rozwiązać ten problem, sprawdź poniższe kroki i ponów próbę wykonania operacji:<br/> - [Upewnij się, że agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Weryfikowanie i rozwiązywanie problemów z pamięcią masową, które mają wpływ na miejsce w zakresie tworzenia kopii zapasowych](#prerequisites)

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [tworzenia kopii zapasowych systemu Windows Server za pomocą agenta usługi Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Jeśli chcesz przywrócić kopię zapasową, zobacz [przywracanie plików na komputerze z systemem Windows](backup-azure-restore-windows-server.md).
