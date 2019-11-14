---
title: Rozwiązywanie problemów z kopią zapasową bazy danych SQL Server przy użyciu Azure Backup | Microsoft Docs
description: Informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych SQL Server baz danych działających na maszynach wirtualnych platformy Azure z Azure Backup.
ms.reviewer: anuragm
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: e600fdb882294d14bb9f9216ac8d621ba5254170
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074727"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Rozwiązywanie problemów z kopiami zapasowymi SQL Server Database przy użyciu Azure Backup

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z bazami danych SQL Server uruchomionymi na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji na temat procesu i ograniczeń tworzenia kopii zapasowej, zobacz [Informacje o SQL Server kopii zapasowych na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia SQL Server

Aby skonfigurować ochronę bazy danych SQL Server na maszynie wirtualnej, należy zainstalować rozszerzenie **AzureBackupWindowsWorkload** na tej maszynie wirtualnej. Jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, oznacza to, że wystąpienie SQL Server nie ma wymaganych uprawnień do tworzenia kopii zapasowych. Aby naprawić ten błąd, wykonaj kroki opisane w sekcji [Ustawianie uprawnień maszyny wirtualnej](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>Komunikaty o błędach

### <a name="backup-type-unsupported"></a>Nieobsługiwany typ kopii zapasowej

| Ważność | Opis | Możliwe przyczyny | Zalecana akcja |
|---|---|---|---|
| Ostrzeżenie | Bieżące ustawienia dla tej bazy danych nie obsługują niektórych typów kopii zapasowych zawartych w skojarzonych zasadach. | <li>Na bazie danych Master można wykonać tylko operację pełnej kopii zapasowej bazy danych. Nie jest możliwa różnicowa kopia zapasowa ani kopia zapasowa dziennika transakcji. </li> <li>Żadna baza danych w modelu odzyskiwania prostego nie zezwala na tworzenie kopii zapasowych dzienników transakcji.</li> | Zmodyfikuj ustawienia bazy danych, aby były obsługiwane wszystkie typy kopii zapasowych w ramach zasad. Lub Zmień bieżące zasady tak, aby zawierały tylko obsługiwane typy kopii zapasowych. W przeciwnym razie nieobsługiwane typy kopii zapasowych zostaną pominięte podczas zaplanowanej kopii zapasowej lub zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem dla kopii zapasowej na żądanie.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwala na żądany typ kopii zapasowej. Ten błąd może wystąpić w następujących sytuacjach: <br/><ul><li>Baza danych, która korzysta z modelu odzyskiwania prostego, nie zezwala na wykonywanie kopii zapasowych dziennika.</li><li>Różnice w kopiach zapasowych i dziennikach nie są dozwolone dla bazy danych Master.</li></ul>Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [modeli odzyskiwania SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Jeśli nie można utworzyć kopii zapasowej dziennika bazy danych w modelu odzyskiwania prostego, wypróbuj jedną z następujących opcji:<ul><li>Jeśli baza danych działa w trybie odzyskiwania prostego, wyłącz kopie zapasowe dzienników.</li><li>Skorzystaj z [dokumentacji SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) , aby zmienić model odzyskiwania bazy danych na pełny lub zbiorczo zarejestrowany. </li><li> Jeśli nie chcesz zmieniać modelu odzyskiwania i masz standardowe zasady tworzenia kopii zapasowych wielu baz danych, których nie można zmienić, zignoruj błąd. Pełne i różnicowe kopie zapasowe będą działały zgodnie z harmonogramem. Kopie zapasowe dziennika zostaną pominięte, co jest oczekiwane w tym przypadku.</li></ul>Jeśli jest to baza danych Master i została skonfigurowana różnicowa lub kopia zapasowa dziennika, wykonaj jedną z następujących czynności:<ul><li>Użyj portalu, aby zmienić harmonogram zasad tworzenia kopii zapasowej bazy danych Master na pełny.</li><li>Jeśli masz standardowe zasady tworzenia kopii zapasowych wielu baz danych, których nie można zmienić, zignoruj błąd. Pełna kopia zapasowa będzie działała zgodnie z harmonogramem. Różnicowe kopie zapasowe lub dzienniki nie będą wykonywane, co jest oczekiwane w tym przypadku.</li></ul> |
| Operacja została anulowana, ponieważ w tej samej bazie danych była już uruchomiona operacja powodująca konflikt. | Zobacz [wpis w blogu dotyczący ograniczeń kopii zapasowych i przywracania](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , które są uruchamiane współbieżnie.| [Użyj SQL Server Management Studio (SSMS), aby monitorować zadania tworzenia kopii zapasowej](manage-monitor-sql-database-backup.md). Po niepowodzeniu operacji powodującej konflikt należy ponownie uruchomić operację.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Baza danych została usunięta lub zmieniono jej nazwę. | Sprawdź, czy baza danych została przypadkowo usunięta lub czy zmieniono jej nazwę.<br/><br/> Jeśli baza danych została przypadkowo usunięta, aby kontynuować tworzenie kopii zapasowych, Przywróć bazę danych do oryginalnej lokalizacji.<br/><br/> Jeśli baza danych została usunięta i nie są potrzebne przyszłe kopie zapasowe, w magazynie Recovery Services wybierz pozycję **Zatrzymaj tworzenie kopii** zapasowej z **zachowaniem Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Aby uzyskać więcej informacji, zobacz [Zarządzanie kopiami zapasowymi baz danych SQL Server i monitorowanie ich](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dzienników został przerwany. | Kopia zapasowa bazy danych lub maszyny wirtualnej jest tworzona za pomocą innego rozwiązania do tworzenia kopii zapasowych, która obcina łańcuch dzienników.|<ul><li>Sprawdź, czy jest używane inne rozwiązanie do tworzenia kopii zapasowej lub skrypt. Jeśli tak, Zatrzymaj inne rozwiązanie do tworzenia kopii zapasowych. </li><li>Jeśli kopia zapasowa była kopią zapasową dziennika na żądanie, wyzwól pełną kopię zapasową w celu uruchomienia nowego łańcucha dzienników. W przypadku zaplanowanych kopii zapasowych dzienników nie jest wymagana żadna akcja, ponieważ usługa Azure Backup automatycznie wyzwala pełną kopię zapasową w celu rozwiązania tego problemu.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Azure Backup nie może nawiązać połączenia z wystąpieniem programu SQL Server. | Azure Backup nie może nawiązać połączenia z wystąpieniem SQL Server. | Skorzystaj z dodatkowych szczegółów w menu błąd Azure Portal, aby zawęzić główne przyczyny. Zapoznaj się z artykułem [Rozwiązywanie problemów z usługą SQL Backup](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) , aby naprawić błąd.<br/><ul><li>Jeśli domyślne ustawienia SQL nie zezwalają na połączenia zdalne, należy zmienić ustawienia. Zapoznaj się z następującymi artykułami, aby uzyskać informacje na temat zmiany ustawień:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy z logowaniem, Skorzystaj z tych linków, aby je naprawić:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Brak pierwszej pełnej kopii zapasowej dla tego źródła danych. | Brak pełnej kopii zapasowej bazy danych. Dzienniki i różnicowe kopie zapasowe są nadrzędne dla pełnej kopii zapasowej, dlatego należy wykonać pełne kopie zapasowe przed zainicjowaniem różnicowej lub kopii zapasowej dziennika. | Wyzwól pełną kopię zapasową na żądanie.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można wykonać kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Miejsce w dzienniku transakcyjnym bazy danych jest pełne. | Aby rozwiązać ten problem, zapoznaj się z [dokumentacją SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o tej samej nazwie już istnieje w lokalizacji docelowej | Docelowy docelowy przywracania ma już bazę danych o tej samej nazwie.  | <ul><li>Zmień nazwę docelowej bazy danych.</li><li>Można też użyć opcji Wymuszaj zastępowanie na stronie przywracanie.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ bazy danych nie można było przełączyć w tryb offline. | Gdy wykonujesz przywracanie, docelowa baza danych musi zostać przełączona w tryb offline. Azure Backup nie można przenieść tych danych do trybu offline. | Skorzystaj z dodatkowych szczegółów w menu błąd Azure Portal, aby zawęzić główne przyczyny. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją programu SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można znaleźć certyfikatu serwera z odciskiem palca w elemencie docelowym. | Baza danych Master w wystąpieniu docelowym nie ma prawidłowego odcisku palca szyfrowania. | Zaimportuj prawidłowy odcisk palca certyfikatu użyty w wystąpieniu źródłowym do wystąpienia docelowego. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Kopia zapasowa dziennika używana do odzyskiwania zawiera zmiany zarejestrowane zbiorczo. Nie można jej użyć do zatrzymania w dowolnym punkcie w czasie, zgodnie z wytycznymi SQL. | Gdy baza danych jest w trybie odzyskiwania z rejestrowaniem zbiorczym, dane między transakcją zarejestrowaną zbiorczo a następną transakcją dziennika nie mogą zostać odzyskane. | Wybierz inny punkt w czasie do odzyskania. [Dowiedz się więcej](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można działać zgodnie z preferencjami kopii zapasowych dla zawsze włączonej grupy dostępności SQL, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są nieosiągalne. | <ul><li>Upewnij się, że wszystkie węzły wymagane do wykonywania kopii zapasowych tej bazy danych są zarejestrowane w dobrej kondycji, a następnie spróbuj ponownie wykonać operację.</li><li>Zmień preferencję tworzenia kopii zapasowej SQL Server dla grupy dostępności zawsze włączone.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Maszyna wirtualna programu SQL Server jest zamykana i nie jest dostępna dla usługi Azure Backup. | Maszyna wirtualna jest wyłączona. | Upewnij się, że wystąpienie SQL Server jest uruchomione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup używa agenta gościa maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej, ale agent gościa nie jest dostępny na serwerze docelowym. | Agent gościa nie jest włączony lub jest w złej kondycji. | Ręcznie [Zainstaluj agenta gościa maszyny wirtualnej](../virtual-machines/extensions/agent-windows.md) . |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Zamiar ochrony autoprotection został usunięty lub nie jest już prawidłowy. | Po włączeniu autoochrony w wystąpieniu SQL Server **Skonfiguruj zadania tworzenia kopii zapasowej** uruchomione dla wszystkich baz danych w tym wystąpieniu. Jeśli wyłączysz funkcję autoochrony podczas działania zadań, zadania **w toku** są anulowane przy użyciu tego kodu błędu. | Ponownie Włącz ochronę autoochrony, aby chronić wszystkie pozostałe bazy danych. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja została zablokowana, ponieważ osiągnięto limit liczby operacji dozwolonych w ciągu 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu operacji w okresie 24-godzinnym ten błąd jest dostępny. <br> Na przykład: Jeśli osiągnięto limit liczby zadań konfigurowania kopii zapasowych, które mogą zostać wyzwolone dziennie, a użytkownik spróbuje skonfigurować kopię zapasową dla nowego elementu, zostanie wyświetlony ten błąd. | Zazwyczaj ponowna próba wykonania operacji po 24 godzinach rozwiązuje ten problem. Jeśli jednak problem będzie się powtarzać, możesz skontaktować się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja została zablokowana, ponieważ magazyn osiągnął limit maksymalny dla takich operacji dozwolony w okresie 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu operacji w okresie 24-godzinnym ten błąd jest dostępny. Ten błąd zazwyczaj występuje, gdy istnieją operacje na skalę, takie jak modyfikacja zasad lub ochrona automatyczne. W przeciwieństwie do przypadków CloudDosAbsoluteLimitReached, nie istnieje wiele możliwości rozwiązania tego stanu, w rzeczywistości usługa Azure Backup ponowi próbę wykonania operacji wewnętrznie dla wszystkich elementów, których to dotyczy.<br> Na przykład: Jeśli masz dużą liczbę źródeł danych chronionych przy użyciu zasad i podjęto próbę zmodyfikowania tych zasad, zostanie wyzwolone skonfigurowanie zadań ochrony dla każdego z chronionych elementów i czasami może wystąpić maksymalny limit dozwolony dla takich operacji dziennie.| Usługa Azure Backup automatycznie ponowi próbę wykonania tej operacji po 24 godzinach.

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed wyzwoleniem operacji ponownego rejestrowania Sprawdź co najmniej jeden z następujących objawów:

* Wszystkie operacje (takie jak wykonywanie kopii zapasowej, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent** , **WorkloadExtensionDidntDequeueMsg**.
* Obszar **stanu kopii** zapasowej dla elementu kopii zapasowej jest **nieosiągalny**. Wykorzystaj wszystkie inne przyczyny, które mogą spowodować wystąpienie tego samego stanu:

  * Brak uprawnień do wykonywania operacji związanych z kopiami zapasowymi na maszynie wirtualnej  
  * Zamykanie maszyny wirtualnej, dlatego nie można wykonać kopii zapasowych
  * Problemy z siecią  

  ![Stan "nieosiągalny" w ponownym rejestrowaniu maszyny wirtualnej](./media/backup-azure-sql-database/re-register-vm.png)

* W przypadku zawsze włączonych grup dostępności kopie zapasowe kończą się niepowodzeniem po zmianie preferencji tworzenia kopii zapasowej lub po przejściu w tryb failover.

Te objawy mogą wystąpić z następujących powodów:

* Rozszerzenie zostało usunięte lub odinstalowane z portalu.
* Rozszerzenie zostało odinstalowane z **Panelu sterowania** na maszynie wirtualnej w celu **odinstalowania lub zmiany programu**.
* Maszyna wirtualna została przywrócona z powrotem w czasie za pomocą przywracania z dysku w miejscu.
* Maszyna wirtualna została zamknięta przez dłuższy czas, więc konfiguracja rozszerzenia wygasła.
* Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.
* Jeden z węzłów grupy dostępności nie otrzymał pełnej konfiguracji kopii zapasowej. Taka sytuacja może wystąpić, gdy grupa dostępności zostanie zarejestrowana w magazynie lub dodany nowy węzeł.

W powyższych scenariuszach zalecamy wyzwolenie operacji ponownego zarejestrowania na maszynie wirtualnej. Zobacz [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) , aby uzyskać instrukcje dotyczące wykonywania tego zadania w programie PowerShell.

## <a name="size-limit-for-files"></a>Limit rozmiaru plików

Łączny rozmiar ciągu plików zależy nie tylko od liczby plików, ale również na ich nazwach i ścieżkach. Dla każdego pliku bazy danych Pobierz nazwę pliku logicznego i ścieżkę fizyczną. Możesz użyć tej kwerendy SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Teraz Rozmieść je w następującym formacie:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Oto przykład:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Jeśli rozmiar ciągu zawartości przekracza 20 000 bajtów, pliki bazy danych są przechowywane inaczej. Podczas odzyskiwania nie będzie można ustawić docelowej ścieżki pliku dla przywracania. Pliki zostaną przywrócone do domyślnej ścieżki SQL dostarczonej przez SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Zastąp domyślną ścieżkę pliku docelowego przywracania

Docelową ścieżkę pliku przywracania można zastąpić podczas operacji przywracania, umieszczając plik JSON zawierający mapowanie pliku bazy danych do docelowej ścieżki przywracania. Utwórz plik `database_name.json` i umieść go w lokalizacji *C:\Program Files\Azure obciążenie Backup\bin\plugins\SQL*.

Zawartość pliku powinna mieć następujący format:

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

W poprzedniej zawartości można pobrać logiczną nazwę pliku bazy danych za pomocą następującego zapytania SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Ten plik powinien zostać umieszczony przed wywołaniem operacji przywracania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup dla SQL Server maszyn wirtualnych (publiczna wersja zapoznawcza), zobacz [Azure Backup dla maszyn wirtualnych SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
