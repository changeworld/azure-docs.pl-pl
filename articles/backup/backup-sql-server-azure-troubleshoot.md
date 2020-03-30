---
title: Rozwiązywanie problemów z tworzeniem kopii zapasowej bazy danych programu SQL Server
description: Rozwiązywanie problemów z informacjami dotyczącymi tworzenia kopii zapasowych baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408620"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej bazy danych programu SQL Server przy użyciu usługi Azure Backup

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z bazami danych programu SQL Server uruchomionymi na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji na temat procesu tworzenia kopii zapasowej i ograniczeń, zobacz [Informacje o kopii zapasowej programu SQL Server na maszynach wirtualnych platformy Azure](sql-support-matrix.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia programu SQL Server

Aby skonfigurować ochronę bazy danych programu SQL Server na maszynie wirtualnej, należy zainstalować rozszerzenie **AzureBackupWindowsWorkload** na tej maszynie wirtualnej. Jeśli wystąpi błąd **UserErrorSQLNoSysadminMembership**, oznacza to, że wystąpienie programu SQL Server nie ma wymaganych uprawnień do tworzenia kopii zapasowych. Aby naprawić ten błąd, wykonaj czynności opisane w sekcji [Ustawianie uprawnień maszyny Wirtualnej](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Rozwiązywanie problemów z odnajdowaniem i konfigurowaniem

Po utworzeniu i skonfigurowaniu magazynu usług odzyskiwania odnajdywanie baz danych i konfigurowanie kopii zapasowej jest procesem dwuetapowym.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Podczas konfiguracji kopii zapasowej, jeśli maszyna wirtualna SQL i jej wystąpienia nie są widoczne w **punktach DB odnajdowania w maszynach wirtualnych** i **konfigurowanie kopii zapasowej** (patrz powyższy obraz) upewnij się, że:

### <a name="step-1-discovery-dbs-in-vms"></a>Krok 1: Discovery DB w maszynach wirtualnych

- Jeśli maszyna wirtualna nie jest wymieniona na liście odnalezionej maszyny Wirtualnej, a także nie jest zarejestrowana dla kopii zapasowej SQL w innym magazynie, wykonaj kroki [tworzenia kopii zapasowej programu Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Krok 2: Konfigurowanie kopii zapasowej

- Jeśli magazyn, w którym maszyna wirtualna SQL jest zarejestrowana w tym samym magazynie używanym do ochrony baz danych, wykonaj kroki [Konfigurowanie kopii zapasowej.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Jeśli maszyna wirtualna SQL musi być zarejestrowana w nowym magazynie, musi być wyrejestrowana ze starego magazynu.  Wyrejestrowanie maszyny Wirtualnej SQL z magazynu wymaga, aby wszystkie chronione źródła danych były chronione, a następnie można usunąć dane kopii zapasowej. Usuwanie kopii zapasowej danych jest operacją destrukcyjną.  Po przejrzeniu i podjętych wszystkich środków ostrożności, aby wyrejestrować maszynę wirtualną SQL, a następnie zarejestrować tę samą maszynę wirtualną z nowym magazynem i ponowić próbę wykonania kopii zapasowej.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych i odzyskiwaniem  

Czasami mogą wystąpić losowe błędy w operacjach tworzenia kopii zapasowych i przywracania lub te operacje mogą utknąć. Może to być spowodowane programami antywirusowymi na maszynie Wirtualnej. Najlepszym rozwiązaniem jest zaproponowanie następujących kroków:

1. Wyklucz następujące foldery ze skanowania antywirusowego:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Zamień `C:\` na literę *usługi SystemDrive*.

1. Wyklucz następujące trzy procesy uruchomione w ramach maszyny Wirtualnej ze skanowania antywirusowego:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. SQL oferuje również kilka wskazówek dotyczących pracy z programami antywirusowymi. Zobacz [ten artykuł,](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) aby uzyskać szczegółowe informacje.

## <a name="error-messages"></a>Komunikaty o błędach

### <a name="backup-type-unsupported"></a>Typ kopii zapasowej nieobsługiwał

| Ważność | Opis | Możliwe przyczyny | Zalecana akcja |
|---|---|---|---|
| Ostrzeżenie | Bieżące ustawienia tej bazy danych nie obsługują niektórych typów kopii zapasowych znajdujących się w skojarzonych zasadach. | <li>Tylko pełna operacja tworzenia kopii zapasowej bazy danych może być wykonana w głównej bazie danych. Nie jest możliwe tworzenie kopii zapasowych różnicowych ani dziennika transakcji. </li> <li>Każda baza danych w prostym modelu odzyskiwania nie zezwala na tworzenie kopii zapasowych dzienników transakcji.</li> | Zmodyfikuj ustawienia bazy danych w taki sposób, aby wszystkie typy kopii zapasowych w zasadach były obsługiwane. Możesz też zmienić bieżące zasady, aby uwzględnić tylko obsługiwane typy kopii zapasowych. W przeciwnym razie nieobsługiwały typy kopii zapasowych zostaną pominięte podczas zaplanowanej kopii zapasowej lub zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem dla kopii zapasowej na żądanie.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwala na żądany typ kopii zapasowej. Błąd może się zdarzyć w następujących sytuacjach: <br/><ul><li>Baza danych, która używa prostego modelu odzyskiwania nie zezwala na tworzenie kopii zapasowych dziennika.</li><li>Różnicowe i dziennikowe kopie zapasowe nie są dozwolone dla głównej bazy danych.</li></ul>Aby uzyskać więcej informacji, zobacz dokumentację [modeli odzyskiwania programu SQL Server.](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Jeśli kopia zapasowa dziennika nie powiedzie się dla bazy danych w prostym modelu odzyskiwania, spróbuj użyć jednej z następujących opcji:<ul><li>Jeśli baza danych jest w trybie odzyskiwania proste, wyłącz kopie zapasowe dziennika.</li><li>Użyj [dokumentacji programu SQL Server,](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) aby zmienić model odzyskiwania bazy danych na pełny lub zbiorczy. </li><li> Jeśli nie chcesz zmienić modelu odzyskiwania i masz standardowe zasady do utworzenia kopii zapasowej wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełne i różnicowe kopie zapasowe będą działać zgodnie z harmonogramem. Kopie zapasowe dziennika zostaną pominięte, co jest oczekiwane w tym przypadku.</li></ul>Jeśli jest to główna baza danych i skonfigurowano kopię zapasową różnicową lub dziennika, wykonaj jedną z następujących czynności:<ul><li>Użyj portalu, aby zmienić harmonogram zasad tworzenia kopii zapasowych dla głównej bazy danych, do pełnej.</li><li>Jeśli masz standardowe zasady do utworzenia kopii zapasowej wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełna kopia zapasowa będzie działać zgodnie z harmonogramem. Różnicowe lub dziennik kopii zapasowych nie nastąpi, co jest oczekiwane w tym przypadku.</li></ul> |
| Operacja anulowana jako operacja powodująca konflikt była już uruchomiona w tej samej bazie danych. | Zobacz wpis w [blogu dotyczący ograniczeń tworzenia kopii zapasowych i przywracania,](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) które są uruchamiane jednocześnie.| [Program SQL Server Management Studio (SSMS) służy do monitorowania zadań tworzenia kopii zapasowych.](manage-monitor-sql-database-backup.md) Po awarii operacji powodującej konflikt, uruchom ponownie operację.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Baza danych została usunięta lub zmieniona. | Sprawdź, czy baza danych została przypadkowo usunięta lub zmieniona.<br/><br/> Jeśli baza danych została przypadkowo usunięta, aby kontynuować tworzenie kopii zapasowych, przywróć bazę danych do oryginalnej lokalizacji.<br/><br/> Jeśli usunięto bazę danych i nie potrzebujesz przyszłych kopii zapasowych, w przechowalni usług odzyskiwania wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej** za pomocą **funkcji Zachowaj dane kopii zapasowej** lub Usuń dane kopii **zapasowej**. Aby uzyskać więcej informacji, zobacz [Zarządzanie bazami danych programu SQL Server i monitorowanie ich kopii zapasowej](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dzienników został przerwany. | Kopia zapasowa bazy danych lub maszyny Wirtualnej jest kopią zapasową za pomocą innego rozwiązania do tworzenia kopii zapasowych, które obcina łańcuch dziennika.|<ul><li>Sprawdź, czy używane jest inne rozwiązanie do tworzenia kopii zapasowych lub skrypt. Jeśli tak, zatrzymaj inne rozwiązanie do tworzenia kopii zapasowych. </li><li>Jeśli kopia zapasowa była kopia zapasowa dziennika na żądanie, wyzwolić pełną kopię zapasową, aby rozpocząć nowy łańcuch dzienników. W przypadku zaplanowanych kopii zapasowych dziennika nie jest wymagana żadna akcja, ponieważ usługa Azure Backup automatycznie wyzwoli pełną kopię zapasową, aby rozwiązać ten problem.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup nie może połączyć się z wystąpieniem SQL. | Usługa Azure Backup nie może połączyć się z wystąpieniem programu SQL Server. | Użyj dodatkowych szczegółów w menu błędu witryny Azure Portal, aby zawęzić główne przyczyny. Aby rozwiązać ten problem z tworzeniem kopii zapasowych SQL, należy zapoznać się z [rozwiązaniem problemu z tworzeniem kopii zapasowych](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) SQL.<br/><ul><li>Jeśli domyślne ustawienia SQL nie zezwalają na połączenia zdalne, zmień ustawienia. Zobacz następujące artykuły, aby uzyskać informacje na temat zmiany ustawień:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy z logowaniem, użyj tych łączy, aby je rozwiązać:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Pierwsza pełna kopia zapasowa brakuje dla tego źródła danych. | Brakuje pełnej kopii zapasowej bazy danych. Dziennik i różnicowe kopie zapasowe są rodzicami pełnej kopii zapasowej, więc pamiętaj, aby wykonać pełne kopie zapasowe przed wyzwoleniem różnicowych lub dziennika kopii zapasowych. | Wyzwalanie pełnej kopii zapasowej na żądanie.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można wykonać kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Miejsce dziennika transakcyjnego bazy danych jest zapełniony. | Aby rozwiązać ten problem, zapoznaj się z [dokumentacją programu SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o tej samej nazwie już istnieje w lokalizacji docelowej | Miejsce docelowe przywracania obiektu docelowego ma już bazę danych o tej samej nazwie.  | <ul><li>Zmień nazwę docelowej bazy danych.</li><li>Możesz też użyć opcji zastępowanie siły na stronie przywracania.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ bazy danych nie można było przełączyć w tryb offline. | Podczas przywracania docelowej bazy danych musi zostać przesunięta w tryb offline. Usługa Azure Backup nie może przewieźć tych danych w tryb offline. | Użyj dodatkowych szczegółów w menu błędu witryny Azure Portal, aby zawęzić główne przyczyny. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją programu SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można odnaleźć certyfikatu serwera z odciskem palca na docelowych. | Główna baza danych w wystąpieniu docelowym nie ma prawidłowego odcisku palca szyfrowania. | Zaimportuj prawidłowy odcisk palca certyfikatu używany w wystąpieniu źródłowym do wystąpienia docelowego. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Kopia zapasowa dziennika używana do odzyskiwania zawiera zmiany zarejestrowane zbiorczo. Nie można jej użyć do zatrzymania w dowolnym punkcie w czasie, zgodnie z wytycznymi SQL. | Gdy baza danych jest w trybie odzyskiwania rejestrowane zbiorczo, dane między transakcji rejestrowane zbiorczo i następnej transakcji dziennika nie można odzyskać. | Wybierz inny punkt w czasie odzyskiwania. [Dowiedz się więcej](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można działać zgodnie z preferencjami kopii zapasowych dla zawsze włączonej grupy dostępności SQL, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są nieosiągalne. | <ul><li>Upewnij się, że wszystkie węzły wymagane do wykonywania kopii zapasowych tej bazy danych są zarejestrowane i w dobrej kondycji, a następnie ponów próbę wykonania operacji.</li><li>Zmień preferencje tworzenia kopii zapasowej dla grupy dostępności programu SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Maszyna wirtualna serwera SQL jest zamykana i nie jest dostępna dla usługi Azure Backup. | Maszyna wirtualna jest zamknięta. | Upewnij się, że wystąpienie programu SQL Server jest uruchomione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup używa agenta gościa maszyny Wirtualnej platformy Azure do wykonywania kopii zapasowej, ale agent gościa nie jest dostępny na serwerze docelowym. | Agent gościa nie jest włączony lub jest w złej kondycji. | [Zainstaluj agenta gościa maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) ręcznie. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Zamiar automatycznej ochrony został usunięty lub nie jest już prawidłowy. | Po włączeniu automatycznej ochrony w wystąpieniu programu SQL **Server, Konfigurowanie zadań kopii zapasowej** są uruchamiane dla wszystkich baz danych w tym wystąpieniu. Jeśli wyłączysz automatyczną ochronę podczas uruchamiania zadań, zadania **w toku** zostaną anulowane z tym kodem błędu. | Włącz automatyczną ochronę po raz kolejny, aby chronić wszystkie pozostałe bazy danych. |

### <a name="clouddosabsolutelimitreached"></a>ChmuraDosAbsoluteLimitReached

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja jest zablokowana, ponieważ osiągnięto limit liczby operacji dozwolonych w ciągu 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu dla operacji w ciągu 24 godzin pojawia się ten błąd. <br> Na przykład: Jeśli maszkrobla dla liczby zadań konfigurowania kopii zapasowej, które mogą być wyzwalane dziennie i spróbuj skonfigurować kopię zapasową na nowy element, zostanie wyświetlony ten błąd. | Zazwyczaj ponowienie próby wykonania operacji po 24 godzinach rozwiązuje ten problem. Jeśli jednak problem będzie się powtarzał, możesz skontaktować się z pomocą techniczną firmy Microsoft, aby uzyskać pomoc.

### <a name="clouddosabsolutelimitreachedwithretry"></a>ChmuraDosAbsoluteLimitReachedWithRetry

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja jest zablokowana, ponieważ przechowalnia osiągnęła maksymalny limit dla takich operacji dozwolonych w ciągu 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu dla operacji w ciągu 24 godzin pojawia się ten błąd. Ten błąd zwykle występuje, gdy istnieją operacje na dużą skalę, takie jak modyfikowanie zasad lub automatycznej ochrony. W przeciwieństwie do clouddosAbsoluteLimitReached, nie ma wiele można zrobić, aby rozwiązać ten stan, w rzeczywistości usługa Azure Backup ponowi próbę operacji wewnętrznie dla wszystkich elementów, o których mowa.<br> Na przykład: Jeśli masz dużą liczbę źródeł danych chronionych za pomocą zasad i spróbuj zmodyfikować tę zasadę, spowoduje to skonfigurowanie zadań ochrony dla każdego z chronionych elementów i czasami może osiągnąć maksymalny limit dozwolony dla takich operacji dziennie.| Usługa Azure Backup automatycznie ponowi próbę wykonania tej operacji po 24 godzinach.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Maszyna wirtualna nie może skontaktować się z usługą Azure Backup z powodu problemów z łącznością z Internetem. | Maszyna wirtualna wymaga łączności wychodzącej z usługami Azure Backup Service, Azure Storage lub Azure Active Directory.| — Jeśli używasz sieciowej sieciowej sieciowej do ograniczania łączności, należy użyć tagu usługi AzureBackup, aby umożliwiać dostęp wychodzący do usługi Azure Backup Service, usługi Azure Storage lub Usługi Azure Active Directory. Wykonaj następujące [kroki,](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) aby udzielić dostępu.<br>- Upewnij się, że usługa DNS rozwiązuje punkty końcowe platformy Azure.<br>- Sprawdź, czy maszyna wirtualna znajduje się za modułem równoważenia obciążenia blokującym dostęp do Internetu. Przypisując publiczny adres IP do maszyn wirtualnych, odnajdowanie będzie działać.<br>- Sprawdź, czy nie ma zapory / antywirusowe / proxy, który blokuje połączenia do powyższych trzech usług docelowych.

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed uruchomieniem operacji ponownego rejestrowania należy sprawdzić co najmniej jeden z następujących objawów:

* Wszystkie operacje (takie jak tworzenie kopii zapasowych, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie Wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDqueueMsg**.
* Jeśli obszar **Stan kopii zapasowej** elementu kopii zapasowej jest wyświetlany jako **niedoścignialny,** wyklucz wszystkie inne przyczyny, które mogą spowodować ten sam stan:

  * Brak uprawnień do wykonywania operacji związanych z tworzeniem kopii zapasowych na maszynie Wirtualnej.
  * Zamknięcie maszyny Wirtualnej, więc kopie zapasowe nie mogą mieć miejsca.
  * Problemy z siecią.

   ![ponowna rejestracja maszyny wirtualnej](./media/backup-azure-sql-database/re-register-vm.png)



* W przypadku grupy Dostępność zawsze włączone kopie zapasowe zaczęły się niepowodzeniem po zmianie preferencji tworzenia kopii zapasowej lub po przebazce awaryjnej.

Objawy te mogą wystąpić z co najmniej jednego z następujących powodów:

* Rozszerzenie zostało usunięte lub odinstalowane z portalu.
* Rozszerzenie zostało odinstalowane z **Panelu sterowania** na maszynie Wirtualnej w obszarze **Odinstalowywanie lub zmienianie programu**.
* Maszyna wirtualna została przywrócona w czasie za pomocą przywracania dysku w miejscu.
* Maszyna wirtualna została zamknięta na dłuższy okres, więc konfiguracja rozszerzenia na nim wygasła.
* Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.
* Jeden z węzłów grupy dostępności nie otrzymał pełnej konfiguracji kopii zapasowej. Może się tak zdarzyć, gdy grupa dostępności jest zarejestrowana w przechowalni lub gdy zostanie dodany nowy węzeł.

W poprzednich scenariuszach zaleca się wyzwolenie operacji ponownego rejestrowania na maszynie Wirtualnej. Zobacz [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) instrukcje dotyczące wykonywania tego zadania w programie PowerShell.

## <a name="size-limit-for-files"></a>Limit rozmiaru dla plików

Całkowity rozmiar ciągu plików zależy nie tylko od liczby plików, ale także od ich nazw i ścieżek. Dla każdego pliku bazy danych pobierz nazwę pliku logicznego i ścieżkę fizyczną. Możesz użyć tej kwerendy SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Teraz ułóż je w następującym formacie:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Oto przykład:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Jeśli rozmiar ciągu zawartości przekracza 20 000 bajtów, pliki bazy danych są przechowywane w inny sposób. Podczas odzyskiwania nie będzie można ustawić docelowej ścieżki pliku do przywrócenia. Pliki zostaną przywrócone do domyślnej ścieżki SQL dostarczonej przez program SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Zastępowanie domyślnej docelowej ścieżki pliku przywracania

Ścieżkę pliku przywracania docelowego można zastąpić podczas operacji przywracania, umieszczając plik JSON zawierający mapowanie pliku bazy danych do docelowej ścieżki przywracania. Utwórz `database_name.json` plik i umieść go w lokalizacji *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*.

Zawartość pliku powinna być w tym formacie:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Oto przykład:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

W poprzedniej zawartości można uzyskać logiczną nazwę pliku bazy danych przy użyciu następującej kwerendy SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Ten plik należy umieścić przed wyzwoleniem operacji przywracania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup dla maszyn wirtualnych programu SQL Server (publiczna wersja zapoznawcza), zobacz [Usługa Azure Backup dla maszyn wirtualnych SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
