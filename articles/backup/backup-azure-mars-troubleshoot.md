---
title: Rozwiązywanie problemów z agentem usługi Azure Backup
description: Rozwiązywanie problemów z instalowaniem i rejestrowaniem agenta usługi Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434013"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z agentem usługi Microsoft Azure Recovery Services (MARS)

W tym artykule opisano, jak naprawić błędy, które można napotkać podczas konfiguracji, rejestracji, tworzenia kopii zapasowej i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zaleca się, sprawdź następujące przed rozpoczęciem rozwiązywania problemów z Microsoft agent usług Azure Recovery Services (MARS):

- [Upewnij się, agenta usług MARS jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Upewnij się, że połączenie sieciowe między agenta usług MARS i Azure](https://aka.ms/AB-A4dp50).
- Upewnij się, że MARS jest uruchomiona (w konsoli usługi). Jeśli zachodzi potrzeba, uruchom ponownie i spróbuj ponownie wykonać operację.
- [Upewnij się, 5 – 10% woluminu wolnego miejsca jest dostępna w lokalizacji folder tymczasowy](https://aka.ms/AB-AA4dwtt).
- [Sprawdź, czy inny proces lub program antywirusowy zakłóca to za pomocą usługi Azure Backup](https://aka.ms/AB-AA4dwtk).
- Jeśli zaplanowane niepowodzenia tworzenia kopii zapasowej, ale ręcznego tworzenia kopii zapasowych działa, zobacz [kopii zapasowych nie działają zgodnie z harmonogramem](https://aka.ms/ScheduledBackupFailManualWorks).
- Upewnij się, że system operacyjny ma najnowsze aktualizacje.
- [Upewnij się, nieobsługiwane dyski i pliki z nieobsługiwanych atrybutów, które są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Upewnij się, że zegar na chronionym system jest skonfigurowany do prawidłowej strefie czasowej.
- [Upewnij się, .NET Framework 4.5.2 lub nowszej jest zainstalowane na serwerze](https://www.microsoft.com/download/details.aspx?id=30653).
- Jeśli próbujesz ponownie zarejestrować serwer w magazynie:
  - Upewnij się, agent nie zostanie odinstalowany na serwerze i czy jest on usuwany z portalu.
  - Użyj tego samego hasła, początkowo użytej do zarejestrowania serwera.
- Dla kopii zapasowych w trybie offline upewnij się, że programu Azure PowerShell numer 3.7.0 jest zainstalowany na komputerze źródłowym, jak i komputerze kopiowania, przed rozpoczęciem tworzenia kopii zapasowej.
- Jeśli agenta kopii zapasowej jest uruchomiona na maszynie wirtualnej platformy Azure, zobacz [w tym artykule](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie**: Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (IDENTYFIKATOR: 34513)

| Przyczyna | Zalecane akcje |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu może być uszkodzony lub mogły wygasnąć. (Na przykład one mogły zostać pobrane więcej niż 48 godzin przed upływem terminu rejestracji.)| Pobierz nowe poświadczenia z magazynu usługi Recovery Services w witrynie Azure portal. (Zobacz krok 6 w [pobierania agenta MARS](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) sekcji.) Następnie wykonaj następujące kroki: <ul><li> Jeśli został już zainstalowany i zarejestrowany MARS, otwórz konsolę MMC agenta usługi Microsoft Azure Backup, a następnie wybierz pozycję **Zarejestruj serwer** w **akcje** okienko, aby ukończyć rejestrację przy użyciu nowego poświadczenia. <br/> <li> Jeśli nowa instalacja nie powiedzie się, spróbuj ponownie zainstalować za pomocą nowych poświadczeń.</ul> **Uwaga**: Jeśli pobrano pliki poświadczeń magazynu, tylko najnowszy plik jest ważny przez następnego 48 godzin. Zalecamy pobranie nowego pliku poświadczeń magazynu.
| **Serwer proxy/Zapora blokuje rejestracji** <br/>lub <br/>**Nie łączności z Internetem** <br/><br/> Jeśli Twoje maszyny lub serwer proxy ma ograniczony łączności z Internetem i nie zapewnić dostęp dla wymaganych adresów URL, rejestracja zakończy się niepowodzeniem.| Wykonaj następujące czynności:<br/> <ul><li> Praca z zespołem INFORMATYCZNYM, aby upewnić się, że system ma łączność z Internetem.<li> Jeśli nie masz serwera proxy, upewnij się, że opcja serwera proxy nie jest zaznaczone, po zarejestrowaniu agenta. [Sprawdź ustawienia serwera proxy](#verifying-proxy-settings-for-windows).<li> Jeśli masz serwer proxy/Zapora współpracować z zespołem sieci, aby upewnić się, te adresy URL i adresy IP mają dostęp:<br/> <br> **Adresy URL**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Adresy IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Spróbuj ponownie się zarejestrować po wykonaniu powyższych kroków rozwiązywania problemów.
| **Oprogramowanie antywirusowe blokuje rejestracji** | Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, należy dodać reguły wykluczania niezbędne do skanowania antywirusowego dla tych plików i folderów: <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Folder tymczasowy. Jego domyślna lokalizacja to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Folder bin na C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Dodatkowe zalecenia
- Przejdź do C:/Windows/Temp, a następnie sprawdź, czy istnieją więcej niż 60 000 lub 65 000 pliki z rozszerzeniem .tmp. Jeśli istnieją, należy usunąć te pliki.
- Upewnij się, na komputerze daty i godziny dopasowanie lokalnej strefy czasowej.
- Upewnij się, [tych witryn](backup-configure-vault.md#verify-internet-access) są dodawane do zaufanych witryn w przeglądarce Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla Windows

1. Pobierz narzędzia PsExec z [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) strony.
1. Uruchom `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` z wiersza polecenia z podwyższonym poziomem uprawnień.

   To polecenie zostanie otwarty program Internet Explorer.
1. Przejdź do **narzędzia** > **Opcje internetowe** > **połączeń** > **ustawienia sieci LAN**.
1. Sprawdź ustawienia serwera proxy dla konta system.
1. Jeśli żaden serwer proxy jest skonfigurowany, a znajdują się szczegóły serwera proxy, należy usunąć szczegółowe informacje.
1. Upewnij się, jeśli skonfigurowano serwer proxy, a szczegóły serwera proxy są niepoprawne, **IP serwera Proxy** i **portu** szczegółowe informacje są poprawne.
1. Zamknij program Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń magazynu

| Błąd   | Zalecane akcje |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń magazynu. (IDENTYFIKATOR: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu przy użyciu innej przeglądarki lub wykonaj następujące czynności: <ul><li> Uruchom program Internet Explorer. Wybierz F12. </li><li> Przejdź do **sieci** kartę i wyczyścić pamięć podręczną i pliki cookie. </li> <li> Odśwież stronę.<br></li></ul> <li> Sprawdź, czy subskrypcja jest wyłączona/wygasły.<br></li> <li> Sprawdź, czy wszystkie reguły zapory blokuje pliki do pobrania. <br></li> <li> Upewnij się, że nie został wyczerpany limit magazynu (50 maszyn magazynu).<br></li>  <li> Upewnij się, że użytkownik ma uprawnienia usługi Azure Backup, które są wymagane do pobierania poświadczeń magazynu i Zarejestruj serwer w magazynie. Zobacz [Use Role-Based kontroli dostępu do zarządzania punkty odzyskiwania usługi Azure Backup](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Błąd  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| <br /><ul><li>Agent usług Microsoft Azure Recovery Service nie może nawiązać połączenia z programem Kopia zapasowa Microsoft Azure. (IDENTYFIKATOR: 100050) ustawienia sieciowe i upewnij się, że jesteś w stanie połączyć się z Internetem.<li>Wymagane jest uwierzytelnianie serwera proxy (407). |Serwer proxy blokuje połączenia. |  <ul><li>W programie Internet Explorer przejdź do **narzędzia** > **Opcje internetowe** > **zabezpieczeń** > **Internet**. Wybierz **Poziom niestandardowy** i przewiń w dół do **pobierania pliku** sekcji. Wybierz **Włącz**.<p>Może być również konieczne Dodaj [adresami URL i IP](backup-configure-vault.md#verify-internet-access) do zaufanych witryn w przeglądarce Internet Explorer.<li>Zmień ustawienia, aby używać serwera proxy. Następnie szczegółowo Serwer proxy serwera.<li> Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwer proxy zezwalać na te [adresami URL i IP](backup-configure-vault.md#verify-internet-access). <li>Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, należy wyłączyć te pliki ze skanowania antywirusowego: <ul><li>CBEngine.exe (instead of dpmra.exe).<li>CSC.exe (powiązanego z .NET Framework). Brak CSC.exe dla każdej wersji systemu .NET Framework zainstalowana na serwerze. Wyklucz pliki CSC.exe dla wszystkich wersji programu .NET Framework na tym serwerze. <li>Pliki tymczasowe lokalizacja folderu lub pamięci podręcznej. <br>Domyślną lokalizacją tymczasowy folder lub ścieżka pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Folder bin na C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Błąd | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| <br />Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych. Aktywacja nie powiodła się całkowicie, ale hasło szyfrowania został zapisany w następującym pliku. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfigurowania hasła jest uszkodzony.| Wyrejestruj serwer z magazynu i zarejestruj go ponownie za pomocą nowego hasła.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie ukończona.

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|<br />Aktywacja nie została ukończona pomyślnie. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft.     | <li> Folder tymczasowy znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został przeniesiony niepoprawnie. <li> Brakuje pliku OnlineBackup.KEK.         | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś pliki tymczasowe lokalizacja folderu lub pamięci podręcznej do woluminu z ilością wolnego miejsca między 5% i 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostały prawidłowo skonfigurowane

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
| <br />Błąd 34506. Hasło szyfrowania zapisane na tym komputerze nie jest poprawnie skonfigurowany.    | <li> Folder tymczasowy znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został przeniesiony niepoprawnie. <li> Brakuje pliku OnlineBackup.KEK.        | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś pliki tymczasowe lokalizacja folderu lub pamięci podręcznej do woluminu z ilością wolnego miejsca między 5% i 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Tworzenie kopii zapasowych nie działają zgodnie z harmonogramem
Jeśli zaplanowane kopie zapasowe nie wyzwalane automatycznie, ale ręcznego tworzenia kopii zapasowych działała prawidłowo, spróbuj wykonać następujące czynności:

- Upewnij się, że harmonogram tworzenia kopii zapasowych w systemie Windows Server nie koliduje z platformy Azure harmonogram tworzenia kopii zapasowych plików i folderów.

- Upewnij się, stan kopii zapasowej online jest równa **Włącz**. Aby sprawdzić stan, wykonaj następujące czynności:

  1. W harmonogramie zadań, należy rozwinąć **Microsoft** i wybierz **kopii zapasowej Online**.
  1. Kliknij dwukrotnie **Microsoft OnlineBackup** i przejdź do **wyzwalaczy** kartę.
  1. Sprawdź, jeśli stan jest ustawiony na **włączone**. Jeśli nie, wybierz **Edytuj**, wybierz opcję **włączone**, a następnie wybierz pozycję **OK**.

- Upewnij się, konto użytkownika, wybrany do uruchomienia tego zadania jest **systemu** lub **grupy administratorów lokalnych** na serwerze. Aby zweryfikować konto użytkownika, przejdź do **ogólne** i sprawdź **zabezpieczeń** opcje.

- Upewnij się, że program PowerShell 3.0 lub nowszej jest zainstalowane na serwerze. Aby sprawdzić wersję programu PowerShell, uruchom następujące polecenie i upewnij się, że `Major` numer wersji to 3 lub nowszej:

  `$PSVersionTable.PSVersion`

- Upewnij się, ta ścieżka jest częścią `PSMODULEPATH` zmienną środowiskową:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla `LocalMachine` jest ustawiony na ograniczone, polecenia cmdlet programu PowerShell, która powoduje uruchomienie zadania tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom następujące polecenia w trybie podniesionych uprawnień, aby sprawdzić i ustawić zasady wykonywania na albo `Unrestricted` lub `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Upewnij się, że brak brakujących lub uszkodzonych plików MSOnlineBackup modułu programu PowerShell. W przypadku wszelkich brakujących lub uszkodzonych plików, wykonaj następujące czynności:

  1. Z dowolnym komputerze, który ma agenta usług MARS, który działa prawidłowo należy skopiować MSOnlineBackup folder z C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na komputerze z problematycznego Wklej skopiowane pliki w folderze o tej samej lokalizacji (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Jeśli istnieje już folder MSOnlineBackup na maszynie, Wklej pliki lub zastąpić istniejące pliki.


> [!TIP]
> Aby upewnić się, że zmiany są stosowane spójnie, uruchom ponownie serwer po wykonaniu powyższych kroków.


## <a name="troubleshoot-restore-problems"></a>Rozwiązywanie problemów przywracania

Usługa Azure Backup nie może pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. I otrzymać komunikaty o błędach w procesie. Aby rozpocząć odzyskiwanie normalnie, wykonaj następujące czynności:

1.  Anuluj proces instalacji, jeśli została uruchomiona przez kilka minut.

2.  Sprawdź, czy najnowszą wersję agenta kopii zapasowej. Aby sprawdzić wersję, na **akcje** okienku konsoli MARS wybierz **o Recovery Agent usług Microsoft Azure**. Upewnij się, że **wersji** liczba jest większa lub równa wyższa niż wersja, o których wspomniano w [w tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Wybierz ten link, aby [Pobierz najnowszą wersję](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Przejdź do **Menedżera urządzeń** > **kontrolery magazynu** i Znajdź **inicjatora iSCSI firmy Microsoft**. Jeśli możesz zlokalizować, przejdź bezpośrednio do kroku 7.

4.  Jeśli nie można zlokalizować Usługa inicjatora iSCSI firmy Microsoft, spróbuj znaleźć pozycji w obszarze **Menedżera urządzeń** > **kontrolery magazynu** o nazwie **nieznane urządzenie** za pomocą Identyfikatora sprzętu **ROOT\ISCSIPRT**.

5.  Kliknij prawym przyciskiem myszy **nieznane urządzenie** i wybierz **aktualizacji sterowników**.

6.  Aktualizacja sterownika, wybierając opcję **wyszukiwanie automatycznie oprogramowania zaktualizowanym sterowniku**. Należy zmienić tę aktualizację **nieznane urządzenie** do **inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu usługi Azure Backup Menedżera urządzeń, za pomocą kontrolerów magazynu wyróżniony](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Przejdź do **Menedżera zadań** > **usługi (lokalne)**  > **Usługa inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu usługi Azure Backup Menedżera zadań, za pomocą usługi (lokalne) wyróżniony](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę, a następnie wybierz **zatrzymać**. Następnie kliknij prawym przyciskiem myszy go ponownie i wybierz **Start**.

9.  Ponów próbę operacji odzyskiwania przy użyciu [natychmiastowe Przywracanie](backup-instant-restore-capability.md).

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, należy ponownie uruchomić serwer lub klienta. Jeśli nie chcesz ponownie uruchomić lub jeśli odzyskiwanie nadal kończy się niepowodzeniem nawet po ponownym uruchomieniu serwera, spróbuj [odzyskiwanie z innego komputera](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) uzyskać szybko rozwiązać problem.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej [sposób wykonywania kopii zapasowej systemu Windows Server za pomocą agenta usługi Kopia zapasowa Azure](tutorial-backup-windows-server-to-azure.md).
* Jeśli potrzebujesz przywrócić z kopii zapasowej, zobacz [Przywracanie plików na maszynę Windows](backup-azure-restore-windows-server.md).
