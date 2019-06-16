---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Agent
description: Rozwiązywanie problemów z instalowaniem i rejestrowaniem agenta usługi Azure Backup
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060205"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z Agent usług Microsoft Azure Recovery Services (MARS)

Poniżej przedstawiono sposób naprawić błędy, które można napotkać podczas konfiguracji, rejestracji, tworzenia kopii zapasowej i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zalecane jest wykonanie poniżej sprawdzania poprawności, przed rozpoczęciem Rozwiązywanie problemów z agentem usługi Microsoft Azure Recovery Services (MARS):

- [Upewnij się, że Agent usługi Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://aka.ms/AB-A4dp50)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). Jeśli to wymagane, ponownie uruchom i ponów operację
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://aka.ms/AB-AA4dwtt)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://aka.ms/AB-AA4dwtk)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](https://aka.ms/ScheduledBackupFailManualWorks)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, nieobsługiwane dyski i pliki z nieobsługiwanych atrybutów, które są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij się, że **zegar systemowy** chronionego systemu ma skonfigurowaną prawidłową strefę czasową <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz **ponownie zarejestrować serwer** w magazynie, wykonaj następujące czynności: <br>
  - Upewnij się, że agent został odinstalowany z serwera oraz usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- W przypadku kopii zapasowej offline upewnij się, że Azure PowerShell w wersji numer 3.7.0 jest zainstalowany na komputerze źródłowym i kopiowania, przed przystąpieniem do wykonywania operacji tworzenia kopii zapasowej w trybie offline
- [Kwestia, gdy agent usługi Kopia zapasowa jest uruchomiony na maszynie wirtualnej platformy Azure](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie**: Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub jest nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (IDENTYFIKATOR: 34513)

| Przyczyna | Zalecana akcja |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu może być uszkodzony lub mogło wygasnąć (czyli pobieranych więcej niż 48 godzin przed upływem terminu rejestracji)| Pobierz nowe poświadczenia z magazynu usługi Recovery Services w witrynie Azure portal (zobacz *kroku 6* w obszarze [ **pobierania agenta MARS** ](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) sekcji) i wykonywać poniżej: <ul><li> Jeśli masz już zainstalowane i zarejestrowane kopia zapasowa Microsoft Azure, otwórz konsolę MMC usługi Microsoft Azure Backup Agent i wybierz polecenie **Zarejestruj serwer** z okienka akcji, aby ukończyć rejestrację przy użyciu nowo pobranego poświadczenia <br/> <li> Jeśli nowa instalacja nie powiodła się. Spróbuj zainstalować ponownie, używając nowych poświadczeń</ul> **Uwaga**: Jeśli wiele plików poświadczenia magazynu zostaną pobrane wcześniej, tylko najnowsze pobrany plik jest prawidłowy w ciągu 48 godzin. Dlatego zalecane jest aby pobrać świeże nowy plik poświadczeń magazynu.
| **Serwer proxy/Zapora blokuje <br/>lub <br/>łączność z Internetem nie** <br/><br/> Jeśli Twoje maszyny lub serwer Proxy ma ograniczony dostęp do Internetu następnie bez listę wymaganych adresów URL rejestracja zakończy się niepowodzeniem.| Aby rozwiązać ten problem, wykonaj poniższe:<br/> <ul><li> Praca z zespołem INFORMATYCZNYM, aby upewnić się, że system ma łączność z Internetem<li> Jeśli nie masz serwera Proxy, upewnij się, opcja serwer proxy nie jest zaznaczona, podczas rejestracji agenta, sprawdź kroki ustawienia serwera proxy [tutaj](#verifying-proxy-settings-for-windows)<li> Jeśli masz serwer proxy/Zapora następnie pracę z zespołem sieci, aby upewnić się, że poniższych adresów URL i IP adresów mają dostęp<br/> <br> **Adresy URL**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**Adres IP**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>Spróbuj ponownie zarejestrować po wykonaniu powyższych kroków rozwiązywania problemów
| **Oprogramowanie antywirusowe blokuje** | Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, dodać reguły wykluczania niezbędne dla następujących plików ze skanowania antywirusowego: <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> Folder tymczasowy, domyślna lokalizacja to *C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> Folder bin na *C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>Dodatkowe zalecenia
- Przejdź do *C:/Windows/Temp* i sprawdź, czy istnieją więcej niż 60 000 lub 65 000 pliki z rozszerzeniem .tmp. Jeśli istnieją, należy usunąć te pliki
- Upewnij się, daty i godziny na komputerze jest zgodny z lokalnej strefy czasowej
- Upewnij się, [następujące](backup-configure-vault.md#verify-internet-access) witryny są dodawane do programu IE zaufanych witryn

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla Windows

- Pobierz **psexec** z [tutaj](https://docs.microsoft.com/sysinternals/downloads/psexec)
- Uruchom to `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:
- Spowoduje to uruchomienie *programu Internet Explorer* okna
- Przejdź do *narzędzia* -> *Opcje internetowe* -> *połączeń* -> *ustawienia sieci LAN*
- Sprawdź ustawienia serwera proxy dla *systemu* konta
- Jeśli żaden serwer proxy jest skonfigurowany, a znajdują się szczegóły serwera proxy, a następnie usuń szczegóły
-   Jeśli skonfigurowano serwer proxy i szczegóły serwera proxy są niepoprawne, upewnij się, *IP serwera Proxy* i *portu* szczegółowe informacje są poprawne
- Zamknij *programu Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń magazynu

| Szczegóły błędu | Zalecane akcje |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń magazynu. (IDENTYFIKATOR: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu przy użyciu innej przeglądarki lub wykonaj następujące czynności: <ul><li> Uruchamianie programu Internet Explorer, naciśnij klawisz F12. </li><li> Przejdź do **sieci** kartę, aby wyczyścić pamięć podręczną programu IE i pliki cookie </li> <li> Odśwież stronę<br>(OR)</li></ul> <li> Sprawdź, czy subskrypcja jest wyłączona/wygasły<br>(OR)</li> <li> Sprawdź, czy wszystkie reguły zapory blokuje pobieranie pliku poświadczeń magazynu <br>(OR)</li> <li> Upewnij się, że możesz mieć nie został on wyczerpany limit magazynu (50 maszyn magazynu)<br>(OR)</li>  <li> Upewnij się, użytkownik posiada wymagane uprawnienia usługi Azure Backup do pobierania poświadczeń magazynu i Zarejestruj serwer w magazynie, zobacz [artykułu](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| **Error** <br /><ol><li>*Agent usług Microsoft Azure Recovery Service nie może nawiązać połączenia z programem Kopia zapasowa Microsoft Azure. (IDENTYFIKATOR: 100050) ustawienia sieciowe i upewnij się, że jesteś w stanie połączyć się z Internetem*<li>*(407) Wymagane jest uwierzytelnianie serwera proxy* |Serwer proxy blokuje połączenia. |  <ul><li>Uruchom **IE** > **ustawienie** > **Opcje internetowe** > **zabezpieczeń**  >  **Internet**. Następnie wybierz pozycję **Poziom niestandardowy** i przewijanie, aż zostanie wyświetlony plik, Pobierz sekcji. Wybierz **Włącz**.<li>Może być również konieczne dodanie tych witryn w programie Internet Explorer [Zaufane witryny](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Zmień ustawienia, aby używać serwera proxy. Następnie szczegółowo Serwer proxy serwera.<li> Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwer proxy zezwalać na te [adresy URL](backup-configure-vault.md#verify-internet-access) i [adresu IP](backup-configure-vault.md#verify-internet-access). <li>Jeśli masz oprogramowanie antywirusowe zainstalowane na serwerze, Wyklucz następujące pliki ze skanowania oprogramowania antywirusowego. <ul><li>CBEngine.exe (instead of dpmra.exe).<li>CSC.exe (powiązanego z .NET Framework). Brak CSC.exe dla każdej wersji platformy .NET, który jest zainstalowany na serwerze. Wyklucz pliki CSC.exe, które są powiązane z wszystkich wersji programu .NET framework na tym serwerze. <li>Lokalizacja folderu lub pamięci podręcznej plików tymczasowych. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Folder bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| **Error** <br />*Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych. Aktywacja nie powiodła się całkowicie, ale hasło szyfrowania został zapisany w następującym pliku*. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfigurowania hasła jest uszkodzony.| Wyrejestruj serwer z magazynu i zarejestrować go ponownie nowe hasło.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie ukończona.

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|**Error** <br />*Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft*     | <li> Folder tymczasowy znajduje się na woluminie, który ma za mało miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony do innej lokalizacji. <li> Brakuje pliku OnlineBackup.KEK.         | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś pliki tymczasowe lokalizacja folderu lub pamięci podręcznej do woluminu z ilością wolnego miejsca równa 5 – 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [pytania dotyczące agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostały prawidłowo skonfigurowane

| Szczegóły błędu | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|**Error** <br />*Błąd 34506. Hasło szyfrowania zapisane na tym komputerze nie jest prawidłowo skonfigurowane*.    | <li> Folder tymczasowy znajduje się na woluminie, który ma za mało miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony do innej lokalizacji. <li> Brakuje pliku OnlineBackup.KEK.        | <li>Uaktualnianie do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usług MARS.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej do woluminu z wolne miejsce odpowiadające 5 – 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, skorzystaj z procedury opisanej w [pytania dotyczące agenta usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>*Domyślna lokalizacja dla tymczasowy folder lub ścieżka lokalizacji pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Tworzenie kopii zapasowych nie działają zgodnie z harmonogramem
Jeśli zaplanowane kopie zapasowe nie wyzwalane automatycznie, podczas ręcznego tworzenia kopii zapasowych działać bez problemów, spróbuj wykonać następujące czynności:

- Upewnij się, że harmonogram tworzenia kopii zapasowych systemu Windows Server nie powoduje konfliktu z platformy Azure harmonogram tworzenia kopii zapasowych plików i folderów.

- Upewnij się, stan kopii zapasowej Online jest ustawiony na **Włącz**. Aby sprawdzić stan wykonania poniżej:

  - Otwórz **harmonogram zadań** i rozwiń **Microsoft**i wybierz **kopii zapasowej Online**.
  - Kliknij dwukrotnie **Microsoft OnlineBackup**, a następnie przejdź do **wyzwalaczy** kartę.
  - Sprawdź, jeśli stan jest ustawiony na **włączone**. Jeśli nie, wybierz **Edytuj** > **włączone** pole wyboru i kliknij przycisk **OK**.

- Upewnij się, konto użytkownika, wybrany do uruchomienia tego zadania jest **systemu** lub **grupy administratorów lokalnych** na serwerze. Aby zweryfikować konto użytkownika, przejdź do **ogólne** i sprawdź **zabezpieczeń** opcje.

- Upewnij się, że program PowerShell 3.0 lub nowszej jest zainstalowane na serwerze. Aby sprawdzić wersję programu PowerShell, uruchom następujące polecenie i upewnij się, że *głównych* numer wersji jest równy lub większy niż 3.

  `$PSVersionTable.PSVersion`

- Upewnij się, następująca ścieżka jest częścią *PSMODULEPATH* zmiennej środowiskowej

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla *LocalMachine* jest ustawiony na ograniczone, polecenia cmdlet programu PowerShell, która powoduje uruchomienie zadania tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom następujące polecenia w trybie podniesionych uprawnień, aby sprawdzić i ustawić zasady wykonywania na albo *Unrestricted* lub *RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Upewnij się, nie ma żadnych brakujące lub uszkodzone **PowerShell** modułu **MSonlineBackup**. W przypadku, gdy jest dowolnym brakujący lub uszkodzony plik, aby rozwiązać ten problem, wykonaj poniższe:

  - Z dowolnej maszyny o agenta usług MARS, który działa prawidłowo, skopiuj folder MSOnlineBackup z *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* ścieżki.
  - Wklej ją w problematyczne maszyny w tej samej ścieżce *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* .
  - Jeśli **MSOnlineBackup** folder już istnieje na maszynie, wklej lub zastąpić pliki zawartości wewnątrz niego.


> [!TIP]
> Aby upewnić się, że zmiany są stosowane spójnie, uruchom ponownie serwer po wykonaniu powyższych kroków.


## <a name="troubleshoot-restore-issues"></a>Rozwiązywanie problemów przywracania

Usługa Azure Backup nie może pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. Może również komunikaty o błędach w procesie. Aby rozpocząć odzyskiwanie normalnie, wykonaj następujące kroki:

1.  Anuluj proces ciągłego instalacji, w przypadku, gdy została uruchomiona przez kilka minut.

2.  Zobacz, jeśli używasz najnowszej wersji agenta kopii zapasowej. Aby dowiedzieć się, wersji, na **akcje** okienku konsoli MARS wybierz **o Recovery Agent usług Microsoft Azure**. Upewnij się, że **wersji** liczba jest większa lub równa wyższa niż wersja, o których wspomniano w [w tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Najnowszą wersję możesz pobrać [tutaj](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Przejdź do **Menedżera urządzeń** > **kontrolery magazynu**, a następnie zlokalizuj **inicjatora iSCSI firmy Microsoft**. Jeśli możesz znaleźć go, przejdź bezpośrednio do kroku 7.

4.  Jeśli nie można zlokalizować Usługa inicjatora iSCSI firmy Microsoft, spróbuj znaleźć pozycji w obszarze **Menedżera urządzeń** > **kontrolery magazynu** o nazwie **nieznane urządzenie**, za pomocą Identyfikatora sprzętu **ROOT\ISCSIPRT**.

5.  Kliknij prawym przyciskiem myszy **nieznane urządzenie**i wybierz **aktualizacji sterowników**.

6.  Aktualizacja sterownika, wybierając opcję **wyszukiwanie automatycznie oprogramowania zaktualizowanym sterowniku**. Zakończenie aktualizacji należy zmienić **nieznane urządzenie** do **inicjatora iSCSI firmy Microsoft**, jak pokazano poniżej.

    ![Zrzut ekranu usługi Azure Backup Menedżera urządzeń, za pomocą kontrolerów magazynu wyróżniony](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Przejdź do **Menedżera zadań** > **usługi (lokalne)**  > **Usługa inicjatora iSCSI firmy Microsoft**.

    ![Zrzut ekranu usługi Azure Backup Menedżera zadań, za pomocą usługi (lokalne) wyróżniony](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę, wybierz opcję **zatrzymać**, a następnie ponownie kliknij prawym przyciskiem myszy i wybierz **Start**.

9.  Ponów próbę operacji odzyskiwania przy użyciu [ **natychmiastowe Przywracanie**](backup-instant-restore-capability.md).

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, należy ponownie uruchomić serwer lub klienta. Jeśli nie chcesz wykonać ponowny rozruch lub odzyskiwania nadal kończy się niepowodzeniem nawet po zakończeniu ponownego uruchomienia serwera, należy spróbować odzyskiwania z alternatywnej maszyny. Postępuj zgodnie z instrukcjami w [w tym artykule](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną
Jeśli nadal potrzebujesz pomocy, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej [sposób wykonywania kopii zapasowej systemu Windows Server przy użyciu agenta usługi Azure Backup](tutorial-backup-windows-server-to-azure.md).
* Jeśli chcesz przywrócić kopię zapasową, w tym artykule znajdziesz informacje dotyczące [przywracania plików na maszynę z systemem Windows](backup-azure-restore-windows-server.md).
