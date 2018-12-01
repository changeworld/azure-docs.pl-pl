---
title: Usługa Azure Backup przewodnik rozwiązywania problemów dla maszyn wirtualnych programu SQL Server | Dokumentacja firmy Microsoft
description: Informacje dotyczące rozwiązywania problemów do wykonywania kopii zapasowych maszyn wirtualnych programu SQL Server na platformie Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 3ad4afc740be01644145704679ee2674ebde3d07
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720732"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Rozwiązywanie problemów z kopii zapasowych programu SQL Server na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów w celu ochrony maszyn wirtualnych serwera SQL na platformie Azure (wersja zapoznawcza).

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Aby wyświetlić ograniczenia publicznej wersji zapoznawczej, zapoznaj się z artykułem [Utwórz kopię zapasową bazy danych programu SQL Server na platformie Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia programu SQL Server

Można skonfigurować ochrony dla bazy danych programu SQL Server na maszynie wirtualnej **AzureBackupWindowsWorkload** rozszerzenia musi być zainstalowany na tej maszynie wirtualnej. Jeśli wystąpi błąd, **UserErrorSQLNoSysadminMembership**, oznacza to wystąpienie programu SQL nie ma wymaganych uprawnień do tworzenia kopii zapasowej. Aby naprawić ten błąd, wykonaj kroki opisane w [ustawić uprawnienia dla maszyn wirtualnych SQL spoza witryny marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami

Skorzystaj z informacji w poniższych tabelach, aby rozwiązywać problemy i błędów napotkanych podczas chroni program SQL Server na platformie Azure.

## <a name="backup-failures"></a>Niepowodzeniami tworzenia kopii zapasowych

Poniższe tabele są uporządkowane według kodu błędu.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwalaj na kopie zapasowe żądanego typu. Ten błąd może się zdarzyć w następujących sytuacjach: <br/><ul><li>Bazę danych przy użyciu modelu odzyskiwania prostego nie zezwala na tworzenie kopii zapasowej dziennika.</li><li>Dziennika i różnicowe kopie zapasowe nie są dozwolone dla głównego bazy danych.</li></ul>Aby uzyskać więcej informacji, zobacz [modeli odzyskiwania SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentacji. | Jeśli kopia zapasowa dziennika bazy danych w modelu odzyskiwania prostego nie powiedzie się, spróbuj wykonać jedną z następujących opcji:<ul><li>Jeśli baza danych jest w trybie odzyskiwania prostego, należy wyłączyć kopie zapasowe dziennika.</li><li>Użyj [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) zmiany modelu odzyskiwania bazy danych na wartość Full lub dziennikiem. </li><li> Jeśli nie chcesz zmienić model odzyskiwania, a masz standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełnych i różnicowych kopii zapasowych będą działać na harmonogram. Kopie zapasowe dziennika zostanie pominięty, której oczekuje się, w tym przypadku.</li></ul>Jeśli wzorzec bazy danych i skonfigurowano różnicowej lub dziennika kopii zapasowej, użyj dowolnej z następujących czynności:<ul><li>Użyj portalu Aby zmienić harmonogram zasad tworzenia kopii zapasowej dla serwera głównego bazy danych, pełne.</li><li>W przypadku standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignoruj ten błąd. Pełnej kopii zapasowej będzie działać na harmonogram. Różnicowa lub dziennika kopii zapasowych nie wystąpi, której oczekuje się, w tym przypadku.</li></ul> |
| Operacja anulowana, ponieważ operacja powodująca konflikt była już uruchomiona na tej samej bazy danych. | Zobacz [wpis w blogu o kopii zapasowej i przywracanie ograniczenia](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) , działające równocześnie.| [Aby monitorować zadania tworzenia kopii zapasowej, należy użyć programu SQL Server Management Studio (SSMS).](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Gdy operacja powodująca konflikt nie powiedzie się, uruchom ponownie operację.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Baza danych, został usunięty lub zmieniono jego nazwę. | <ul><li>Sprawdź, jeśli baza danych została przypadkowo usunięty lub zmieniono ich nazwy.</li><li>Jeśli przypadkowo usunięto bazy danych, aby kontynuować tworzenie kopii zapasowych, należy przywrócić bazę danych do oryginalnej lokalizacji.</li><li>Jeśli usunięte bazy danych i nie potrzebujesz przyszłych kopie zapasowe, a następnie w magazynie usługi Recovery Services klikaj [zatrzymywanie tworzenia kopii zapasowych "Usuń/Zachowaj dane"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dzienników został przerwany. | Baza danych lub maszyna wirtualna kopia zapasowa jest tworzona przy użyciu innego rozwiązania tworzenia kopii zapasowych, które obcina łańcuch dziennika.|<ul><li>Sprawdź, czy innego rozwiązania tworzenia kopii zapasowych lub skryptu jest w użyciu. Jeśli tak, Zatrzymaj inne rozwiązania tworzenia kopii zapasowych. </li><li>Jeśli wykonano kopię zapasową kopii zapasowej dziennika zapytań ad-hoc, Wyzwól pełnej kopii zapasowej, aby rozpocząć nowy łańcuch dziennika. Dziennik zaplanowanego tworzenia kopii zapasowych ponieważ usługa Azure Backup automatycznie spowoduje wyzwolenie pełnej kopii zapasowej, aby rozwiązać ten problem jest wymagana żadna akcja.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup nie jest w stanie połączyć się z wystąpieniem serwera SQL. | Usługa Azure Backup nie może połączyć się z wystąpieniem programu SQL. | Użyj dodatkowe szczegóły w menu błędzie portalu platformy Azure, aby zawęzić głównych przyczyn. Zapoznaj się [rozwiązywania kopii zapasowej SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) Aby naprawić błąd.<br/><ul><li>Jeśli domyślne ustawienia programu SQL nie zezwalają na połączenia zdalne, należy zmienić ustawienia. Zobacz poniższe linki dotyczące zmieniania ustawień.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy podczas logowania, użyj poniższych linków, aby rozwiązać ten problem:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Pierwsza pełna kopia zapasowa brakuje dla tego źródła danych. | Pełna kopia zapasowa jest Brak bazy danych. Dziennik i różnicowe kopie zapasowe element nadrzędny do pełnej kopii zapasowej, dzięki czemu pełnych kopii zapasowych należy podjąć przed wyzwoleniem różnicowej lub kopie zapasowe dzienników. | Wyzwalanie ad hoc pełnej kopii zapasowej.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można przyjąć kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Przestrzeń dziennika transakcji bazy danych jest pełny. | Aby rozwiązać ten problem, zapoznaj się [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Repliki pomocnicze zawsze na grupy dostępności nie obsługują pełne i różnicowe kopie zapasowe. | <ul><li>Jeśli wyzwoleniu kopii zapasowej usługi ad-hoc wyzwalanie tworzenia kopii zapasowych w węźle podstawowym.</li><li>Jeśli kopia zapasowa została zaplanowana przez zasady, upewnij się, że węzeł podstawowy jest zarejestrowany. Aby zarejestrować węzeł, [postępuj zgodnie z instrukcjami, aby odnaleźć bazy danych programu SQL Server](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Błędy przywracania

Następujące kody błędów są wyświetlane podczas przywracania zadanie zakończy się niepowodzeniem.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o takiej samej nazwie już istnieje w lokalizacji docelowej | Przywracanie docelowej już bazę danych o takiej samej nazwie.  | <ul><li>Zmień nazwę docelowej bazy danych</li><li>Lub użyj opcji Zastąp życie na stronie przywracania</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ baza danych nie można przełączyć do trybu offline. | Podczas wykonywania przywracania, docelowa baza danych musi zostać przełączone do trybu offline. Usługa Azure Backup nie jest w stanie wyświetlić te dane w trybie offline. | Użyj dodatkowe szczegóły w menu błędzie portalu platformy Azure, aby zawęzić głównych przyczyn. Aby uzyskać więcej informacji, zobacz [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można odnaleźć certyfikatu serwera z odciskiem palca w elemencie docelowym. | Wzorzec bazy danych w wystąpieniu docelowym nie ma odcisku palca szyfrowania prawidłowe. | Zaimportuj prawidłowy odcisk palca używane w wystąpieniu źródłowym, do obiektu docelowego. |

## <a name="registration-failures"></a>Błędy rejestracji

Następujące kody błędów związanych z błędami rejestracji.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można spełnić preferencji kopii zapasowych dla zawsze włączonej grupy dostępności SQL, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są niedostępne. | <ul><li>Upewnij się, że wszystkie węzły, które są wymagane do wykonywania kopii zapasowych tej bazy danych są zarejestrowane i dobrej kondycji, a następnie spróbuj ponownie wykonać operację.</li><li>Zmiana zawsze włączonej grupy dostępności SQL preferencję tworzenia kopii zapasowych.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Program SQL server VM jest zamknięty lub niedostępny dla usługi Azure Backup. | Maszyna wirtualna jest zamknięta. | Upewnij się, że serwer SQL jest uruchomiony. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup używa agenta gościa maszyny Wirtualnej platformy Azure tworzy kopie zapasowe, ale agent gościa nie jest dostępne na serwerze docelowym. | Agent gościa nie jest włączona lub jest w złej kondycji | [Zainstaluj agenta gościa maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) ręcznie. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi Azure Backup dla maszyn wirtualnych programu SQL Server (publiczna wersja zapoznawcza), zobacz [usługi Azure Backup dla maszyn wirtualnych SQL (publiczna wersja zapoznawcza)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).