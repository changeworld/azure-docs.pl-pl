---
title: Rozwiązywanie problemów z łącznością z usługą Microsoft Azure SQL Database | Microsoft Docs
description: Rozwiązywanie problemów z łącznością z usługą Microsoft Azure SQL Database
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 3717b36e37f7d9cca206b5a9e8e3cded50393748
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974424"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Rozwiązywanie problemów z łącznością z usługą Microsoft Azure SQL Database

Gdy nawiązywanie połączenia z usługą Azure SQL Database nie powiedzie się, pojawią się komunikaty o błędach. Problemy z połączeniami mogą być spowodowane ponownym skonfigurowaniem bazy danych SQL Azure, ustawieniami zapory, przekroczeniem limitu czasu połączenia lub nieprawidłowymi informacjami logowania. Ponadto w przypadku osiągnięcia maksymalnego limitu dla niektórych zasobów Azure SQL Database nie można nawiązać połączenia z Azure SQL Database.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Błąd 40613: baza danych < x > na serwerze < y > jest obecnie niedostępna

**Szczegóły błędu**

``40613: Database <DBname> on server <server name> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '<Tracing ID>'.``

Aby rozwiązać ten problem:

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://status.azure.com/status) pod kątem znanych awarii. 
2. Jeśli nie ma żadnych znanych przestojów, przejdź do [witryny sieci Web pomocy technicznej Microsoft Azure](http://azure.microsoft.com/support/options) , aby otworzyć zgłoszenie do pomocy technicznej.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z błędem "baza danych na serwerze jest obecnie niedostępna"](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-common-connection-issues#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Wystąpił błąd związany z siecią lub wystąpieniem podczas ustanawiania połączenia z SQL Server

Problem występuje, ponieważ aplikacja nie może nawiązać połączenia z serwerem.

Aby rozwiązać ten problem, wykonaj kroki opisane w sekcji poniżej, zatytułowane **kroki rozwiązywania typowych problemów z połączeniami**.

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Serwer nie został odnaleziony lub był niedostępny (błędy 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Błąd 26: Wystąpił błąd podczas lokalizowania określonego serwera/wystąpienia

**Szczegóły błędu**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Błąd 40: nie można otworzyć połączenia z SQL Server

**Szczegóły błędu**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Błąd 10053: Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera.

**Szczegóły błędu**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Te problemy występują, ponieważ aplikacja nie może nawiązać połączenia z serwerem.

Aby rozwiązać ten problem, wykonaj kroki opisane w sekcji poniżej, zatytułowane **kroki rozwiązywania typowych problemów z połączeniami**.

## <a name="cannot-connect-to--servername--due-to-firewall-issues"></a>Nie można nawiązać połączenia z serwerem < ServerName > ze względu na problemy z zaporą

### <a name="error-40615-cannot-connect-to--servername-"></a>Błąd 40615: nie można nawiązać połączenia z serwerem < ServerName >

Aby rozwiązać ten problem, [Skonfiguruj ustawienia zapory na SQL Database przy użyciu Azure Portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Błąd 5: nie można nawiązać połączenia z serwerem < ServerName >

Aby rozwiązać ten problem, upewnij się, że port 1433 jest otwarty dla połączeń wychodzących wszystkich zapór między klientem a Internetem. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory systemu Windows w celu zezwalania na dostęp SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-login-to-the-server-errors-18456-40531"></a>Nie można zalogować się na serwerze (błędy 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Nie można zalogować użytkownika "< nazwy użytkownika >"

**Szczegóły błędu**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Aby rozwiązać ten problem, skontaktuj się z administratorem usługi w celu podania prawidłowej nazwy użytkownika i hasła SQL.

Procedura dodawania nazwy logowania przez administratora usługi zwykle obejmuje następujące czynności:

1. Zaloguj się do serwera przy użyciu programu SQL Server Management Studio (SSMS).
2. Sprawdź, czy nazwa logowania jest wyłączona, za pomocą następującego zapytania SQL:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Jeśli odpowiadająca nazwa jest wyłączona, włącz ją przy użyciu następującej instrukcji: 

   ```
   Alter login <User name> enable
   ```

4. Jeśli nazwa użytkownika logowania SQL nie istnieje, utwórz ją za pomocą programu SSMS:

   1. Kliknij dwukrotnie pozycję **Zabezpieczenia**, aby ją rozwinąć. 
   2. Kliknij prawym przyciskiem myszy pozycję **Nazwy logowania**, a następnie wybierz polecenie **Nowa nazwa logowania**. 
   3. W wygenerowanym skrypcie z symbolami zastępczymi możesz edytować i uruchomić następujące zapytanie SQL:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Kliknij dwukrotnie pozycję **Baza danych**. 
6. Wybierz bazę danych, do której chcesz przyznać uprawnienia użytkownikowi.
7. Kliknij dwukrotnie pozycję **Zabezpieczenia**. 
8. Kliknij prawym przyciskiem myszy pozycję **Użytkownicy**, a następnie wybierz polecenie **Nowy użytkownik**. 
9. W wygenerowanym skrypcie z symbolami zastępczymi możesz edytować i uruchomić następujące zapytanie SQL: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > Można również użyć `sp_addrolemember` do mapowania określonych użytkowników do określonych ról bazy danych. 

Aby uzyskać więcej informacji, zobacz [Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Błędy upłynął limit czasu połączenia

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): upłynął limit czasu połączenia.

**Szczegóły błędu**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): upłynął limit czasu.

**Szczegóły błędu**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: nie można otworzyć podstawowego dostawcy.

**Szczegóły błędu**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nie można nawiązać połączenia z < > nazwy serwera. ' '

**Szczegóły błędu**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Te wyjątki mogą wystąpić z powodu problemów z połączeniem lub zapytaniem. Aby potwierdzić, że ten błąd jest spowodowany problemami z łącznością, zapoznaj się z sekcją poniżej zatytułowaną upewnij się, **czy błąd jest spowodowany problemem z łącznością**:

Przekroczono limit czasu połączenia, ponieważ aplikacja nie może nawiązać połączenia z serwerem. Aby rozwiązać ten problem, wykonaj kroki opisane w sekcji poniżej, zatytułowane **kroki rozwiązywania typowych problemów z połączeniami**.

## <a name="transient-errors-errors-40197-40545"></a>Błędy przejściowe (błędy 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Błąd 40197: usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie. Kod błędu < kod >

Ten problem występuje z powodu przejściowego błędu napotkanego podczas ponownej konfiguracji/przejścia w tryb failover w zapleczu.

Aby rozwiązać ten problem, poczekaj chwilę, a następnie ponów próbę. Przypadek pomocy technicznej nie jest wymagany, chyba że problem pozostaje trwały.

Najlepszym rozwiązaniem jest upewnienie się, że logika ponawiania jest na miejscu. Aby uzyskać więcej informacji o logice ponowień, zobacz [Rozwiązywanie błędów przejściowych i błędy połączeń w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-due-to-hitting-some-system-defined-limit"></a>Połączenie zostało przerwane z powodu przekroczenia limitu zdefiniowanego przez system

### <a name="error-10928-resource-id-d"></a>Błąd 10928: Identyfikator zasobu:% d.

**Szczegóły błędu**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Aby obejść ten problem, wypróbuj jedną z następujących metod:

* Sprawdź, czy są wykonywane długotrwałe zapytania:

  > [!NOTE]
  > Jest to minimalistyczny podejście, które może niekoniecznie rozwiązać problem.

  1. Sprawdź widok [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , aby wyświetlić wszystkie żądania blokowania, wykonując następujące zapytanie SQL:

     ```
     SELECT * FROM dm_exec_requests
     ```

  2. Określ **bufor wejściowy** dla bloku głównego.
  3. Dostrajanie zapytania bloku nagłówkowego.

    Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Jeśli baza danych konsekwentnie osiągnie swój limit, niezależnie od blokowania i długotrwałych zapytań, należy rozważyć uaktualnienie do jednej z nowych wersji w wersji zapoznawczej (na przykład [wersji Standard lub Premium](https://azure.microsoft.com/pricing/details/sql-database/)).

Aby uzyskać więcej informacji na temat opcji cennika SQL Database, zapoznaj się z [Azure SQL Database cennikiem](https://azure.microsoft.com/pricing/details/sql-database/single/).

Aby uzyskać więcej informacji na temat dynamicznych widoków zarządzania, zobacz [dynamiczne zarządzanie dynamicznymi widokami](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Aby uzyskać więcej informacji o tym komunikacie o błędzie, zobacz [SQL Database limitów zasobów dla Azure SQL Database Server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Błąd 10929: Identyfikator zasobu: 1.

**Szczegóły błędu**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Aby uzyskać więcej informacji na temat tego błędu, zobacz [komunikaty o błędach dotyczące SQL Database programów klienckich](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)

### <a name="error-40501-the-service-is-currently-busy"></a>Błąd 40501: usługa jest obecnie zajęta

**Szczegóły błędu**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Jest to błąd ograniczania aparatu, wskazujący, że limity zasobów są przekraczane.

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [limity zasobów serwera bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544--the-database-has-reached-its-size-quota"></a>Błąd 40544: osiągnięto limit przydziału rozmiaru bazy danych

**Szczegóły błędu**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ten błąd występuje, gdy osiągnięto limit przydziału rozmiaru bazy danych.

Poniższe kroki mogą pomóc w rozwiązaniu problemu lub zapewnieniu dodatkowych opcji, które można wziąć pod uwagę.

1. Sprawdź bieżący rozmiar bazy danych przy użyciu pulpitu nawigacyjnego w Azure Portal.

   > [!NOTE]
   > Aby określić, które tabele zużywają najwięcej miejsca i potencjalne kandydaci do oczyszczenia, można użyć następującego zapytania SQL:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Jeśli bieżący rozmiar nie przekracza maksymalnego rozmiaru obsługiwanego przez daną wersję, można użyć polecenia ALTER DATABASE, aby zwiększyć ustawienie rozmiaru. 
3. Jeśli rozmiar bazy danych przekracza maksymalny obsługiwany rozmiar wersji, można wykonać jedną z następujących czynności:
   1. Wykonaj normalne działania oczyszczania bazy danych (czyszcząc niepożądane dane przy użyciu obcinania lub usuwania itp.) lub Przenieś dane przy użyciu usług SSIS, bcp itp.)
   2. Utwórz partycję lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. 
   
   *  Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) i [skalowanie zasobów puli elastycznej](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Błąd 40549: sesja została zakończona, ponieważ masz długotrwałą transakcję.

**Szczegóły błędu**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Jeśli ten komunikat o błędzie wystąpi wielokrotnie, spróbuj wykonać następujące kroki, aby rozwiązać ten problem: 

1. Sprawdź widok sys. DM _exec_requests, aby wyświetlić wszystkie otwarte sesje, które mają wysoką wartość dla kolumny total_elapsed_time, wykonując następujący skrypt SQL:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Określ bufor wejściowy dla zapytania, które jest długotrwałe. 
3. Dostosuj zapytanie.

Należy również rozważyć przetwarzanie wsadowe zapytań. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Błąd 40551: sesja została przerwana z powodu nadmiernego użycia bazy danych TEMPDB.

**Szczegóły błędu**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Aby obejść ten problem, wykonaj następujące kroki:

1. Zmień zapytania, aby zmniejszyć użycie tymczasowego obszaru tabeli. 
2. Usuń obiekty tymczasowe, gdy nie są już potrzebne. 
3. Obcinanie tabel lub usuwanie nieużywanych tabel.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Błąd 40552: sesja została przerwana z powodu nadmiernego użycia miejsca w dzienniku transakcji.

**Szczegóły błędu**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Aby rozwiązać ten problem, wykonaj następujące metody: 

* Problem występuje z powodu operacji INSERT, Update lub DELETE. Spróbuj zmniejszyć liczbę wierszy, które są bezpośrednio obsługiwane przez zaimplementowanie operacji wsadowych lub dzielenia na wiele mniejszych transakcji.
* Problem występuje z powodu operacji ponownego kompilowania indeksu. Upewnij się, że przestrzegasz następującej formuły: liczba wierszy, których dotyczy tabela * (średni rozmiar pola, które jest aktualizowane w bajtach + 80) < 2 GB

  > [!NOTE]
  > W przypadku ponownego kompilowania indeksu średni rozmiar aktualizowanego pola powinien być zastępowany przez średni rozmiar indeksu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Błąd 40553: sesja została przerwana z powodu nadmiernego użycia pamięci.

**Szczegóły błędu**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Aby obejść ten problem, spróbuj zoptymalizować zapytanie.

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Nie można otworzyć bazy danych "Master" żądanej przez nazwę logowania. Logowanie nie powiodło się.

Ten problem występuje, ponieważ konto nie ma uprawnień dostępu do bazy danych Master. Jednak domyślnie program SQL Server Management Studio (SSMS) próbuje nawiązać połączenie z bazą danych Master.

Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Na ekranie logowania programu SSMS kliknij pozycję **Opcje**, a następnie kliknij pozycję **Właściwości połączenia**. 
2. W obszarze **Połącz z bazą danych**wpisz domyślną nazwę bazy danych użytkownika jako domyślną bazę danych logowania, a następnie kliknij przycisk **Połącz**.

   ![Cannot-Open-Database-Master. png](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-due-to-a-connectivity-issue"></a>Sprawdź, czy błąd jest spowodowany problemem z łącznością

Aby sprawdzić, czy błąd jest spowodowany problemem z łącznością, przejrzyj ślad stosu dla ramek, które pokazują wywołania, aby otworzyć połączenie podobne do następujących (należy zwrócić uwagę na odwołanie do klasy **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Gdy wystąpi wyjątek spowodowany problemami z kwerendą, zobaczysz stos wywołań podobny do poniższego (należy zwrócić uwagę na odwołanie do klasy **SqlCommand** ). W tych scenariuszach [Dostosuj swoje zapytania](http://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Dodatkowe wskazówki dotyczące dostrajania wydajności można znaleźć w następujących tematach:

* [Jak zarządzać indeksami i statystykami SQL platformy Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ręczne dostrajanie wydajności zapytań w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitorowanie wydajności Azure SQL Database przy użyciu dynamicznych widoków zarządzania](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Działanie magazynu zapytań w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Kroki rozwiązywania typowych problemów z połączeniem

1. Upewnij się, że adres IP protokołu TCP jest włączony jako protokół klienta na serwerze aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołów klienta](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na serwerach aplikacji, na których nie ma zainstalowanych narzędzi SQL Server, sprawdź, czy adres IP protokołu TCP jest włączony, uruchamiając **cliconfig. exe** (Narzędzie sieci klienta SQL Server). 
2. Sprawdź parametry połączenia aplikacji, aby upewnić się, że jest prawidłowo skonfigurowana. Na przykład upewnij się, że parametry połączenia określają prawidłowy port (1433) oraz w pełni kwalifikowaną nazwę serwera.
Zobacz [pobieranie SQL Server informacji o połączeniu](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Spróbuj zwiększyć **limit czasu**połączenia. Firma Microsoft zaleca użycie limitu czasu połączenia wynoszącego co najmniej 30 sekund. 
4. Przetestuj łączność między serwerem aplikacji i bazą danych Azure SQL Database przy użyciu programu [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), pliku UDL, polecenia ping i programu Telnet. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) i [Diagnostyka w przypadku problemów z łącznością](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Krok rozwiązywania problemów umożliwia również podpróbę przetestowania łączności na innym komputerze klienckim.

5. Najlepszym rozwiązaniem jest upewnienie się, że logika ponawiania jest na miejscu. Aby uzyskać więcej informacji o logice ponowień, zobacz [Rozwiązywanie błędów przejściowych i błędy połączeń w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Jeśli poprzednie kroki nie rozwiążą problemu, spróbuj zebrać więcej danych i skontaktować się z pomocą techniczną. Jeśli aplikacja jest usługą w chmurze, Włącz rejestrowanie. Ten krok zwraca sygnaturę czasową UTC błędu. Ponadto usługa SQL Azure zwraca identyfikator śledzenia. [Usługi pomocy technicznej firmy Microsoft](http://azure.microsoft.com/support/options/) mogą korzystać z tych informacji. 

Aby uzyskać więcej informacji na temat włączania rejestrowania, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Powiązane dokumenty**

* [Architektura łączności usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Azure SQL Database i kontrola dostępu do sieci hurtowni danych](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)