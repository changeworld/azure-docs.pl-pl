---
title: Kopia zapasowa Azure Podręcznik rozwiązywania problemów dotyczących maszyn wirtualnych programu SQL Server | Dokumentacja firmy Microsoft
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
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296211"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Rozwiązywanie problemów z kopii zapasowych programu SQL Server na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla ochrony na platformie Azure (wersja zapoznawcza), maszynach wirtualnych serwera SQL.

## <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Aby wyświetlić ograniczenia publicznej wersji zapoznawczej, zapoznaj się z artykułem [Utwórz kopię zapasową bazy danych programu SQL Server na platformie Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia programu SQL Server

Aby skonfigurować ochronę bazy danych programu SQL Server na maszynie wirtualnej **AzureBackupWindowsWorkload** rozszerzenia musi być zainstalowany na tej maszynie wirtualnej. Jeśli wystąpi błąd, **UserErrorSQLNoSysadminMembership**, oznacza to wystąpienie programu SQL nie ma wymaganych uprawnień tworzenia kopii zapasowej. Aby naprawić ten błąd, postępuj zgodnie z instrukcjami [ustawić uprawnień dla maszyn wirtualnych SQL spoza witryny marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami

Skorzystaj z informacji w poniższych tabelach rozwiązywania problemów i błędów napotkanych podczas ochrony programu SQL Server na platformie Azure.

## <a name="backup-failures"></a>Niepowodzenie wykonywania kopii zapasowej

Następujące tabele są zorganizowane według kodu błędu.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwala na żądany typ kopii zapasowej. Ten błąd może się zdarzyć w następujących sytuacjach: <br/><ul><li>Bazy danych przy użyciu modelu odzyskiwania prostego nie zezwala na wykonywanie kopii zapasowej dziennika.</li><li>Kopie zapasowe dziennika i różnice są niedozwolone dla wzorca bazy danych.</li></ul>Aby uzyskać więcej szczegółów, zobacz [modeli odzyskiwania SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentacji. | Jeśli tworzenie kopii zapasowej dziennika bazy danych w modelu odzyskiwania prostego nie powiedzie się, spróbuj wykonać jedną z następujących opcji:<ul><li>Jeśli baza danych jest w trybie odzyskiwania prostego, należy wyłączyć kopie zapasowe dziennika.</li><li>Użyj [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) Aby zmienić model odzyskiwania bazy danych do pełnego lub dziennikiem. </li><li> Jeśli nie chcesz zmienić model odzyskiwania, a masz standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignorować błąd. Kopie zapasowe pełne i różnicowe będzie działać na harmonogram. Kopie zapasowe dziennika zostanie pominięte, który jest oczekiwany w tym przypadku.</li></ul>Jeśli wzorzec bazy danych i skonfigurowano różnicowej lub dziennika kopii zapasowej, użyj dowolnej z następujących czynności:<ul><li>Użyj portalu, aby zmienić harmonogram zasad tworzenia kopii zapasowych w przypadku wzorca bazy danych, pełne.</li><li>Jeśli masz standardowych zasad, aby utworzyć kopię zapasową wielu baz danych, których nie można zmienić, zignorować błąd. Z pełnej kopii zapasowej będzie działać na harmonogram. Nie nastąpi kopii zapasowych zróżnicowanych lub dziennika, który jest oczekiwany w tym przypadku.</li></ul> |
| Operacja anulowana, ponieważ operacja powodująca konflikt jest już uruchomiona na tej samej bazy danych. | Zobacz [wpis w blogu o kopii zapasowej i przywracania ograniczenia](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) który uruchamiać jednocześnie.| [Umożliwia monitorowanie zadań tworzenia kopii zapasowej programu SQL Server Management Studio (SSMS).](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Gdy operacja powodująca konflikt nie powiedzie się, ponownie uruchom operację.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Bazy danych został usunięty lub zmieniono jego nazwę. | <ul><li>Sprawdź, jeśli bazy danych został przypadkowo usunięty lub zmieniono jego nazwę.</li><li>Jeśli przypadkowo usunięto bazy danych, aby kontynuować tworzenie kopii zapasowych, należy przywrócić bazę danych do oryginalnej lokalizacji.</li><li>Jeśli usunięte bazy danych i nie wymagają kolejnych kopii zapasowych, a następnie w magazynie usług odzyskiwania klikaj [kopii zapasowej Zatrzymaj z "Delete/Zachowaj dane"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dziennika został przerwany. | Baza danych lub maszyny Wirtualnej kopia zapasowa jest tworzona przy użyciu innej kopii zapasowej rozwiązania, które obcina łańcuch dziennika.|<ul><li>Sprawdź, czy inny tworzenia kopii zapasowych lub skryptu jest w użyciu. Jeśli tak, Zatrzymaj tworzeniem kopii zapasowych. </li><li>Jeśli wykonano kopię zapasową kopii zapasowej dziennika ad-hoc, wyzwolić pełnej kopii zapasowej, aby uruchomić nowy łańcuch dziennika. Dziennik zaplanowanych kopii zapasowych jak usługa Kopia zapasowa Azure automatycznie wyzwoli pełnej kopii zapasowej, aby rozwiązać ten problem jest wymagana żadna akcja.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Kopia zapasowa Azure nie jest w stanie nawiązać połączenia z wystąpieniem serwera SQL. | Kopia zapasowa Azure nie może połączyć się z wystąpieniem programu SQL. | Użyj dodatkowe szczegóły w menu błąd portalu Azure, aby zawęzić głównych przyczyn. Zapoznaj się [rozwiązywanie kopii zapasowej SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) Aby naprawić błąd.<br/><ul><li>Jeśli domyślne ustawienia SQL nie zezwalają na połączenia zdalne, Zmień ustawienia. Odwoływać się do poniższych łączy do zmiany ustawień.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy dotyczące logowania, można skorzystać z poniższych łączy, aby go rozwiązać:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Pierwsza pełna kopia zapasowa brakuje dla tego źródła danych. | Pełna kopia zapasowa jest Brak bazy danych. Dziennik i różnice nadrzędne kopii zapasowych do pełnej kopii zapasowej, dlatego pełnych kopii zapasowych należy podjąć przed wyzwalania różnicowej lub kopii zapasowych dziennika. | Wyzwalanie ad hoc pełnej kopii zapasowej.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można zająć kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Miejsce transakcyjne dziennika bazy danych jest pełny. | Aby rozwiązać ten problem, zapoznaj się [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Pełne i różnicowe kopie zapasowe replik pomocniczych zawsze na grupy dostępności nie jest obsługiwane. | <ul><li>Jeśli wyzwoleniu kopii zapasowych ad hoc wyzwolić tworzenie kopii zapasowych w węźle podstawowym.</li><li>Jeśli kopia zapasowa została zaplanowana przez zasady, upewnij się, że węzeł podstawowy jest zarejestrowany. Aby zarejestrować tego węzła, [wykonaj kroki, aby odnaleźć bazy danych programu SQL Server](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Przywróć błędów

Następujące kody błędów są wyświetlane, gdy zadanie przywracania zakończy się niepowodzeniem.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o tej samej nazwie już istnieje w lokalizacji docelowej | Przywracanie docelowej już bazę danych o tej samej nazwie.  | <ul><li>Zmień nazwę bazy danych docelowych</li><li>Możesz też użyć opcji force Zastąp w stronie przywracania</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ w bazie danych nie można przełączyć do trybu offline. | Podczas wykonywania przywracania, docelowa baza danych musi być przełączany w tryb offline. Kopia zapasowa Azure nie jest w stanie można wyświetlić danych w trybie offline. | Użyj dodatkowe szczegóły w menu błąd portalu Azure, aby zawęzić głównych przyczyn. Aby uzyskać więcej informacji, zobacz [dokumentacji programu SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można odnaleźć certyfikatu serwera z odciskiem palca w systemie docelowym. | Wzorzec bazy danych w wystąpieniu docelowym nie ma szyfrowania prawidłowy odcisk palca. | Importuj odcisku palca ważnego certyfikatu używane w wystąpieniu źródłowym do obiektu docelowego. |

## <a name="registration-failures"></a>Błędy rejestracji

Następujące kody błędów dotyczą błędami rejestracji.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można spełnić preferencji dotyczących kopii zapasowych dla SQL zawsze włączonej grupy dostępności, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są niedostępne. | <ul><li>Upewnij się, że wszystkie węzły, które są wymagane do wykonywania kopii zapasowych bazy danych są zarejestrowane i działa prawidłowo, a następnie spróbuj ponownie wykonać operację.</li><li>Zmień SQL zawsze włączonej grupy dostępności preferencji dotyczących kopii zapasowych.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| SQL server maszyny Wirtualnej jest albo zamykania i nie jest dostępny dla usługi Kopia zapasowa Azure. | Maszyna wirtualna jest zamknięta. | Upewnij się, że serwer SQL jest uruchomiony. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Kopia zapasowa Azure korzysta z agenta gościa maszyny Wirtualnej platformy Azure do wykonywania kopii zapasowej, ale agent gościa nie jest dostępny na serwerze docelowym. | Agent gościa nie jest włączona lub jest w złej kondycji | [Zainstaluj agenta gościa maszyny Wirtualnej](../virtual-machines/extensions/agent-windows.md) ręcznie. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o usłudze Azure Backup dla maszyn wirtualnych programu SQL Server (publicznej wersji zapoznawczej), zobacz [kopia zapasowa Azure dla maszyn wirtualnych SQL (publicznej wersji zapoznawczej)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).