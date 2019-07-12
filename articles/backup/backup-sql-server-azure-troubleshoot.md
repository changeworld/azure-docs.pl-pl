---
title: Rozwiązywanie problemów z kopii zapasowej bazy danych programu SQL Server przy użyciu usługi Azure Backup | Dokumentacja firmy Microsoft
description: Informacje dotyczące rozwiązywania problemów do wykonywania kopii zapasowych baz danych SQL Server uruchomiony na maszynach wirtualnych Azure z usługą Azure Backup.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704848"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Rozwiązywanie problemów z kopii zapasowej bazy danych programu SQL Server przy użyciu usługi Azure Backup

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla baz danych programu SQL Server uruchomionej na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji na temat procesu tworzenia kopii zapasowej i ograniczeń, zobacz [kopii zapasowych programu SQL Server, na maszynach wirtualnych Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia programu SQL Server

Aby skonfigurować ochronę dla bazy danych programu SQL Server na maszynie wirtualnej, należy zainstalować **AzureBackupWindowsWorkload** rozszerzenia na tej maszynie wirtualnej. Jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, oznacza to, wystąpienia programu SQL Server nie ma wymaganych uprawnień do tworzenia kopii zapasowej. Aby naprawić ten błąd, wykonaj kroki opisane w [maszyn wirtualnych zestawu uprawnień](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>Komunikaty o błędach

### <a name="backup-type-unsupported"></a>Nieobsługiwany typ kopii zapasowej

| severity | Opis | Możliwe przyczyny | Zalecana akcja |
|---|---|---|---|
| Ostrzeżenie | Bieżące ustawienia dla tej bazy danych nie obsługuje niektórych typów kopii zapasowych w skojarzonych zasad. | <li>Tylko operacja tworzenia kopii zapasowej pełnej bazy danych można wykonać w bazie danych master. Różnicowa kopia zapasowa ani kopii zapasowej dziennika transakcji nie jest możliwe. </li> <li>Wszystkie bazy danych w modelu odzyskiwania prostego nie zezwala na przeznaczonego na kopie zapasowe dzienników transakcji.</li> | Zmodyfikuj ustawienia bazy danych w taki sposób, że wszystkie typy kopii zapasowych w ramach zasad są obsługiwane. Możesz też zmienić bieżące zasady w celu uwzględnienia tylko obsługiwane typy kopii zapasowych. W przeciwnym razie nieobsługiwane typy kopii zapasowych zostaną pominięte podczas zaplanowanej kopii zapasowej lub kopii zapasowej ad-hoc zadania tworzenia kopii zapasowej zakończy się niepowodzeniem.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwalaj na kopie zapasowe żądanego typu. Ten błąd może się zdarzyć w następujących sytuacjach: <br/><ul><li>Bazy danych, który jest używany model odzyskiwania prostego nie zezwala na tworzenie kopii zapasowej dziennika.</li><li>Dziennika i różnicowe kopie zapasowe nie są dozwolone w bazie danych master.</li></ul>Aby uzyskać więcej informacji, zobacz [modeli odzyskiwania programu SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentacji. | Jeśli kopia zapasowa dziennika bazy danych w modelu odzyskiwania prostego nie powiedzie się, spróbuj wykonać jedną z następujących opcji:<ul><li>Jeśli baza danych jest w trybie odzyskiwania prostego, należy wyłączyć kopie zapasowe dziennika.</li><li>Użyj [dokumentacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) Aby zmienić model odzyskiwania bazy danych na wartość full lub niepełnym dziennikiem. </li><li> Jeśli nie chcesz zmienić model odzyskiwania, a masz standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełnych i różnicowych kopii zapasowych będą działać na harmonogram. Kopie zapasowe dziennika zostanie pominięty, której oczekuje się, w tym przypadku.</li></ul>Jeśli bazę danych master i skonfigurowano różnicowej lub dziennika kopii zapasowej, należy użyć jednej z następujących czynności:<ul><li>Użyj portalu, aby zmienić harmonogram zasad tworzenia kopii zapasowej bazy danych master, na wartość full.</li><li>W przypadku standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełnej kopii zapasowej będzie działać na harmonogram. Różnicowa lub dziennika kopii zapasowych nie wystąpi, której oczekuje się, w tym przypadku.</li></ul> |
| Operacja anulowana, ponieważ operacja powodująca konflikt była już uruchomiona na tej samej bazy danych. | Zobacz [wpis w blogu o ograniczeniach i przywracania kopii zapasowych](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , działające równocześnie.| [Użyj programu SQL Server Management Studio (SSMS) do monitorowania zadań tworzenia kopii zapasowej](manage-monitor-sql-database-backup.md). Po niepowodzeniu operacji powodującej konflikt operacji ponownego uruchamiania.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Baza danych, został usunięty lub zmieniono jego nazwę. | Sprawdź, jeśli baza danych została przypadkowo usunięty lub zmieniono ich nazwy.<br/><br/> Jeśli przypadkowo usunięto bazy danych, aby kontynuować tworzenie kopii zapasowych, należy przywrócić bazę danych do oryginalnej lokalizacji.<br/><br/> Jeśli usunięte bazy danych, a następnie wybierz przyszłych kopie zapasowe, a następnie w magazynie usługi Recovery Services nie muszą **Zatrzymaj kopię zapasową** z **Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Aby uzyskać więcej informacji, zobacz [baz danych programu SQL Server kopii zapasowej zarządzania i monitorowania](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dzienników został przerwany. | Baza danych lub maszyna wirtualna kopia zapasowa jest tworzona przy użyciu innego rozwiązania tworzenia kopii zapasowych, które obcina łańcuch dziennika.|<ul><li>Sprawdź, czy innego rozwiązania tworzenia kopii zapasowych lub skryptu jest w użyciu. Jeśli tak, Zatrzymaj inne rozwiązania tworzenia kopii zapasowych. </li><li>Jeśli wykonano kopię zapasową kopii zapasowej dziennika ad-hoc, Wyzwól pełnej kopii zapasowej, aby rozpocząć nowy łańcuch dziennika. Dziennik zaplanowanego tworzenia kopii zapasowych ponieważ usługa Azure Backup automatycznie spowoduje wyzwolenie pełnej kopii zapasowej, aby rozwiązać ten problem jest wymagana żadna akcja.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup nie jest w stanie połączyć się z wystąpieniem serwera SQL. | Usługa Azure Backup nie może połączyć się z wystąpieniem programu SQL Server. | Użyj dodatkowe szczegóły menu błędzie portalu platformy Azure, aby zawęzić głównych przyczyn. Zapoznaj się [rozwiązywania kopii zapasowej SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) Aby naprawić błąd.<br/><ul><li>Jeżeli domyślne ustawienia programu SQL nie zezwalają na połączenia zdalne, należy zmienić ustawienia. Zobacz następujące artykuły, aby uzyskać informacje na temat zmiany ustawień:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy podczas logowania, należy użyć poniższych linków, aby to naprawić:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Pierwsza pełna kopia zapasowa brakuje dla tego źródła danych. | Pełna kopia zapasowa jest Brak bazy danych. Dziennik i różnicowe kopie zapasowe są rodziców do pełnej kopii zapasowej, dlatego upewnij się, twórz pełne kopie zapasowe przed wyzwoleniem różnicowej lub kopie zapasowe dzienników. | Wyzwalanie ad-hoc pełnej kopii zapasowej.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można przyjąć kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Przestrzeń dziennika transakcji bazy danych jest pełny. | Aby rozwiązać ten problem, zapoznaj się [dokumentacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o takiej samej nazwie już istnieje w lokalizacji docelowej | Przywracanie docelowej już bazę danych o takiej samej nazwie.  | <ul><li>Zmień nazwę docelowej bazy danych.</li><li>Możesz też użyć opcji nadpisywania życie na stronie przywracania.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ bazy danych nie można było przełączyć w tryb offline. | Gdy wykonujesz przywrócenie docelowej bazy danych musi zostać przełączone do trybu offline. Usługa Azure Backup nie można przełączyć danych w trybie offline. | Użyj dodatkowe szczegóły menu błędzie portalu platformy Azure, aby zawęzić głównych przyczyn. Aby uzyskać więcej informacji, zobacz [dokumentacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można odnaleźć certyfikatu serwera z odciskiem palca w elemencie docelowym. | Bazy danych master w wystąpieniu docelowym nie ma odcisku palca szyfrowania prawidłowe. | Zaimportuj prawidłowy odcisk palca używane w wystąpieniu źródłowym, do obiektu docelowego. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Kopia zapasowa dziennika używana do odzyskiwania zawiera zmiany zarejestrowane zbiorczo. Nie można jej użyć do zatrzymania w dowolnym punkcie w czasie, zgodnie z wytycznymi SQL. | Gdy baza danych jest w trybie odzyskiwania z niepełnym dziennikiem, nie można odzyskać danych między dalej transakcji dziennika i z niepełnym dziennikiem transakcji. | Wybierz inny punkt w czasie do odzyskania. [Dowiedz się więcej](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można działać zgodnie z preferencjami kopii zapasowych dla zawsze włączonej grupy dostępności SQL, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są niedostępne. | <ul><li>Upewnij się, wszystkie węzły, które są wymagane do wykonywania kopii zapasowych tej bazy danych są zarejestrowane i działa prawidłowo i spróbuj wykonać operację ponownie.</li><li>Zmień preferencję tworzenia kopii zapasowych dla grupy dostępności programu SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Program SQL server VM jest zamknięty lub niedostępny dla usługi Azure Backup. | Maszyna wirtualna jest zamknięta. | Upewnij się, że wystąpienie programu SQL Server jest uruchomiona. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup używa agenta gościa maszyny Wirtualnej platformy Azure tworzy kopie zapasowe, ale agent gościa nie jest dostępne na serwerze docelowym. | Agent gościa nie jest włączona lub ma niepoprawny stan. | [Zainstaluj agenta gościa maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) ręcznie. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Automatycznej ochrony został usunięty lub jest już prawidłowy. | Po włączeniu automatycznej ochrony wystąpienia programu SQL Server, **konfigurowania kopii zapasowej** zadania są uruchamiane dla wszystkich baz danych w tym wystąpieniu. Po wyłączeniu automatycznej ochrony podczas wykonywania zadań, a następnie **w toku** są anulowane zadania przy użyciu tego kodu błędu. | Włącz automatyczną ochronę ponownie lepiej chronić wszystkie pozostałe bazy danych. |

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Sprawdź, czy co najmniej jeden z następujących objawów przed wyzwoleniem operacji ponownej rejestracji:

* Wszystkie operacje (takie jak Kopia zapasowa, Przywróć i skonfiguruj kopię zapasową) kończą się niepowodzeniem na maszynie Wirtualnej przy użyciu jednego z następujących kody błędów: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* **Stan kopii zapasowej** obszaru dla elementu kopii zapasowej jest wyświetlane **nieosiągalny**. Wyeliminuj wszystkich przyczyn, które może skutkować tym samym stanie:

  * Brak uprawnień do wykonywania operacji związanych z kopii zapasowej na maszynie Wirtualnej  
  * Zamknij maszynę Wirtualną, dzięki czemu kopie zapasowe nie może mieć miejsce
  * Problemy z siecią  

  ![Stan "Nieosiągalny" ponowne zarejestrowanie maszyny Wirtualnej](./media/backup-azure-sql-database/re-register-vm.png)

* W przypadku zawsze włączonej grupy dostępności kopie zapasowe pracę, kończy się niepowodzeniem po zmianie preferencję tworzenia kopii zapasowych lub po przejściu w tryb failover.

Tych objawów może pojawić się dla co najmniej jednej z następujących powodów:

* Rozszerzenie został usunięty lub odinstalowany z poziomu portalu. 
* Rozszerzenie została odinstalowana z **Panelu sterowania** na maszynie Wirtualnej w obszarze **Odinstaluj lub zmień Program**.
* Maszyna wirtualna została przywrócona Wstecz w czasie za pomocą przywracania dysku w miejscu.
* Maszyny Wirtualnej został zamknięty przez dłuższy czas, dlatego konfiguracja rozszerzenia może on wygasł.
* Maszyna wirtualna została usunięta, a innej maszyny Wirtualnej została utworzona o takiej samej nazwie i w tej samej grupie zasobów usuniętej maszyny Wirtualnej.
* Jednym z węzłów grupy dostępności nie nadeszła kompletna konfiguracja kopii zapasowej. Może to nastąpić, gdy grupa dostępności jest zarejestrowany w magazynie lub po dodaniu nowego węzła.

W powyższych scenariuszy zaleca się, że użytkownik zainicjuje operację ponownej rejestracji na maszynie Wirtualnej. Na razie ta opcja jest dostępna wyłącznie za pośrednictwem programu PowerShell.

## <a name="size-limit-for-files"></a>Limit rozmiaru plików

Ciąg łączny rozmiar plików zależy od tego, nie tylko na liczbie plików, ale także na ich nazwy i ścieżki. Dla każdego pliku bazy danych Uzyskaj logicznej nazwy pliku i ścieżka fizyczna. Można użyć tej kwerendy SQL:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Teraz rozmieszczanie ich w następującym formacie:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Oto przykład:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Jeśli rozmiar ciągu zawartości przekracza 20 000 bajtów, pliki bazy danych są przechowywane inaczej. Podczas odzyskiwania nie można ustawić ścieżkę pliku docelowego dla przywracania. Pliki zostaną przywrócone w domyślnej ścieżce SQL dostępnych w programie SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Zastąp domyślna ścieżka pliku docelowego przywracania

Ścieżka pliku docelowego przywracania można zastąpić podczas operacji przywracania, umieszczając plik w formacie JSON, który zawiera mapowania pliku bazy danych do ścieżki docelowej przywracania. Tworzenie `database_name.json` plik i umieść go w lokalizacji *C:\Program Files\Azure obciążenia Backup\bin\plugins\SQL*.

Zawartość pliku powinna być w następującym formacie:
```
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

```
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

W poprzednim zawartości można uzyskać nazwę logicznego pliku bazy danych za pomocą następującego zapytania SQL:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Ten plik powinien być umieszczony przed wyzwoleniem operacji przywracania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup dla maszyn wirtualnych programu SQL Server (publiczna wersja zapoznawcza), zobacz [usługi Azure Backup dla maszyn wirtualnych SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
