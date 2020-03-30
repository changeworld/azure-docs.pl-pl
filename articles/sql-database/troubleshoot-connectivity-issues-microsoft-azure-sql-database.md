---
title: Rozwiązywanie typowych problemów z połączeniem z bazą danych SQL
description: Zawiera kroki rozwiązywania problemów z połączeniem usługi Azure SQL Database i rozwiązywania innych problemów specyficznych dla bazy danych SQL
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208782"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Rozwiązywanie problemów z łącznością i innych błędów w usłudze Microsoft Azure SQL Database

Gdy nawiązywanie połączenia z usługą Azure SQL Database nie powiedzie się, pojawią się komunikaty o błędach. Te problemy z połączeniem mogą być spowodowane przez ponowną konfigurację usługi Azure SQL Database, ustawienia zapory, limit czasu połączenia, nieprawidłowe informacje logowania lub niestosowanie najlepszych rozwiązań i wskazówek dotyczących projektowania podczas procesu [projektowania aplikacji.](sql-database-develop-overview.md) Ponadto jeśli osiągnięto maksymalny limit niektórych zasobów usługi Azure SQL Database, nie można połączyć się z usługą Azure SQL Database.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Komunikaty o błędach błędów przejściowych (40197, 40613 i inne)

Infrastruktura platformy Azure ma możliwość dynamicznego ponownego konfigurowania serwerów w przypadku wystąpienia dużych obciążeń w usłudze SQL Database.  To dynamiczne zachowanie może powodować, że program kliencki utraci połączenie z usługą SQL Database. Ten rodzaj błędu jest nazywany *usterką przejściową*. Zdarzenia ponownej konfiguracji bazy danych występują z powodu planowanego zdarzenia (na przykład uaktualnienia oprogramowania) lub zdarzenia nieplanowanego (na przykład awarii procesu lub równoważenia obciążenia). Większość zdarzeń ponownej konfiguracji są zazwyczaj krótkotrwałe i powinny być zakończone w mniej niż 60 sekund, co najwyżej. Jednak te zdarzenia mogą czasami trwać dłużej, aby zakończyć, na przykład gdy duża transakcja powoduje długotrwałe odzyskiwanie. W poniższej tabeli wymieniono różne błędy przejściowe, które aplikacje mogą odbierać podczas łączenia się z bazą danych SQL

### <a name="list-of-transient-fault-error-codes"></a>Lista kodów błędów błędów przejściowych


| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 4060 |16 |Nie można otworzyć bazy danych "%.&#x2a;ls" żądanej przez login. Logowanie nie powiodło się. Aby uzyskać więcej informacji, zobacz [Błędy 4000 do 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Usługa napotkała błąd przetwarzania żądania. Spróbuj ponownie. Kod błędu %d.<br/><br/>Ten błąd jest odbierany, gdy usługa jest wyłączona z powodu uaktualnień oprogramowania lub sprzętu, awarii sprzętu lub innych problemów z przebłażeniem awaryjnym. Kod błędu (%d) osadzony w komunikacie o błędzie 40197] zawiera dodatkowe informacje o rodzaju awarii lub pracy awaryjnej, która wystąpiła. Niektóre przykłady kodów błędów są osadzone w komunikacie o błędzie 40197 to 40020, 40143, 40166 i 40540.<br/><br/>Ponowne połączenie z serwerem bazy danych SQL automatycznie łączy użytkownika z w dobrej kondycji kopii bazy danych. Aplikacja musi przechwytywać błąd 40197, rejestrować osadzony kod błędu (%d) w komunikacie w celu rozwiązywania problemów i próbować ponownie połączyć się z bazą danych SQL, dopóki zasoby nie będą dostępne, a połączenie zostanie nawiązane ponownie. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Usługa jest obecnie zajęta. Ponów próbę żądania po 10 sekundach. Identyfikator zdarzenia: %ls. Kod: %d. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Baza danych "%.&#x2a;ls" na serwerze '%.&#x2a;ls' nie jest obecnie dostępna. Ponów próbę połączenia później. Jeśli problem będzie się powtarzał, skontaktuj się z działem obsługi klienta i podaj im identyfikator śledzenia sesji o "%.&#x2a;ls".<br/><br/> Ten błąd może wystąpić, jeśli istnieje już istniejące dedykowane połączenie administratora (DAC) ustanowione do bazy danych. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Nie można przetworzyć żądania. Za mało zasobów do przetwarzania żądania.<br/><br/>Usługa jest obecnie zajęta. Ponów próbę żądania później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Nie można przetworzyć żądania utworzenia lub aktualizacji. Zbyt wiele operacji tworzenia lub aktualizacji w toku dla subskrypcji "%ld".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań tworzenia lub aktualizacji subskrypcji lub serwera. Żądania są obecnie blokowane w celu optymalizacji zasobów. Kwerenda [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla oczekujących operacji. Poczekaj, aż oczekujące żądania utworzenia lub aktualizacji zostaną zakończone, lub usuń jedno z oczekujących żądań i spróbuj ponownie poprosić później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Nie można przetworzyć żądania. Zbyt wiele operacji w toku dla subskrypcji "%ld".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań dla tej subskrypcji. Żądania są obecnie blokowane w celu optymalizacji zasobów. Zapytanie [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) dla stanu operacji. Poczekaj, aż oczekujące żądania zostaną zakończone, lub usuń jedno z oczekujących żądań i spróbuj ponownie poprosić później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Zalogowanie się do odczytu wtórnego nie powiodło się z powodu długiego oczekiwania na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika nie jest dostępna do logowania, ponieważ brakuje wersji wiersza dla transakcji, które były w locie, gdy replika została poddana recyklingowi. Problem można rozwiązać, cofając lub zatwierdzając aktywne transakcje w replice podstawowej. Wystąpienia tego warunku można zminimalizować, unikając długich transakcji zapisu na podstawowym. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki mające na celu rozwiązanie problemów z łącznością przejściową

1. Sprawdź [pulpit nawigacyjny usługi platformy Microsoft Azure pod](https://azure.microsoft.com/status) kątem wszelkich znanych awarii, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje, które łączą się z usługą w chmurze, takich jak Azure SQL Database należy oczekiwać okresowych zdarzeń ponownej konfiguracji i zaimplementować logikę ponawiania obsługi tych błędów zamiast napawania się nimi jako błędy aplikacji dla użytkowników. 
3. Ponieważ baza danych zbliża się do limitów zasobów, może wydawać się przejściowy problem z łącznością. Zobacz [Limity zasobów](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Jeśli problemy z łącznością nadal lub jeśli czas trwania, dla którego aplikacja napotka błąd przekracza 60 sekund lub jeśli widzisz wiele wystąpień błędu w danym dniu, złóż żądanie pomocy technicznej platformy Azure, wybierając **pozycję Uzyskaj pomoc techniczną** w witrynie pomocy technicznej platformy [Azure.](https://azure.microsoft.com/support/options)

#### <a name="implementing-retry-logic"></a>Implementowanie logiki ponawiania prób
Zdecydowanie zaleca się, że program kliencki ma logikę ponawiania próby, dzięki czemu można przywrócić połączenie po podaniu czasu błędu przejściowego, aby poprawić się.  Zalecamy opóźnienie o 5 sekund przed pierwszym ponowieniem próby. Ponowna próba po opóźnieniu krótszym niż 5 sekund może przytłaczać usługę w chmurze. Dla każdej kolejnej próby opóźnienie powinno rosnąć wykładniczo, maksymalnie do 60 sekund.

Aby uzyskać przykłady kodu logiki ponawiania próby, zobacz:
- [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Aby uzyskać dodatkowe informacje na temat obsługi błędów przejściowych w przeglądzie aplikacji
* [Rozwiązywanie problemów z błędami połączenia przejściowego z bazą danych SQL](sql-database-connectivity-issues.md)

Omówienie *okresu blokowania* dla klientów korzystających z ADO.NET jest dostępne w programie SQL Server [Connection Pooling (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Wystąpił błąd związany z siecią lub wystąpieniem podczas ustanawiania połączenia z serwerem bazy danych SQL

Ten problem występuje, jeśli aplikacja nie może połączyć się z serwerem.

Aby rozwiązać ten problem, spróbuj wykonać czynności (w przedstawionej kolejności) w sekcji [Kroki, aby rozwiązać typowe problemy z połączeniem.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Nie znaleziono serwera/wystąpienia lub nie było dostępne (błędy 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Błąd 26: Określono błąd lokalizowania serwera

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Błąd 40: Nie można otworzyć połączenia z serwerem

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Błąd 10053: Wystąpił błąd na poziomie transportu podczas odbierania wyników z serwera

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Te problemy występują, jeśli aplikacja nie może połączyć się z serwerem.

Aby rozwiązać te problemy, spróbuj wykonać kroki (w przedstawionej kolejności) w sekcji [Kroki, aby rozwiązać typowe problemy z połączeniem.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Nie można połączyć się z serwerem z powodu problemów z zaporą

### <a name="error-40615-cannot-connect-to--servername-"></a>Błąd 40615: Nie można połączyć się z < nazwa_ serwera >

Aby rozwiązać ten problem, [należy skonfigurować ustawienia zapory w bazie danych SQL za pośrednictwem witryny Azure portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Błąd 5: Nie można połączyć się z < >

Aby rozwiązać ten problem, upewnij się, że port 1433 jest otwarty dla połączeń wychodzących na wszystkich zaporach między klientem a Internetem.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie Zapory systemu Windows w celu umożliwienia dostępu do programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nie można zalogować się do serwera (błędy 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Nie powiodło się logowanie użytkownika "< nazwa użytkownika >"

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Aby rozwiązać ten problem, skontaktuj się z administratorem usługi, aby podać prawidłową nazwę użytkownika i hasło programu SQL Server.

Zazwyczaj administrator usługi może wykonać następujące kroki, aby dodać poświadczenia logowania:

1. Zaloguj się do serwera przy użyciu programu SQL Server Management Studio (SSMS).
2. Uruchom następującą kwerendę SQL, aby sprawdzić, czy nazwa logowania jest wyłączona:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Jeśli odpowiadająca nazwa jest wyłączona, włącz ją przy użyciu następującej instrukcji:

   ```sql
   Alter login <User name> enable
   ```

4. Jeśli nazwa użytkownika logowania SQL nie istnieje, utwórz ją, wykonując następujące kroki:

   1. W pliku SSMS kliknij dwukrotnie pozycję **Zabezpieczenia,** aby ją rozwinąć.
   2. Kliknij prawym przyciskiem myszy pozycję **Nazwy logowania**, a następnie wybierz polecenie **Nowa nazwa logowania**.
   3. W wygenerowanym skrypcie z symbolami zastępczymi edytuj i uruchom następującą kwerendę SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Kliknij dwukrotnie pozycję **Baza danych**.
6. Wybierz bazę danych, której chcesz udzielić użytkownikowi uprawnienia.
7. Kliknij dwukrotnie pozycję **Zabezpieczenia**.
8. Kliknij prawym przyciskiem myszy pozycję **Użytkownicy**, a następnie wybierz polecenie **Nowy użytkownik**.
9. W wygenerowanym skrypcie z symbolami zastępczymi edytuj i uruchom następującą kwerendę SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Można również `sp_addrolemember` użyć do mapowania określonych użytkowników do określonych ról bazy danych.

Aby uzyskać więcej informacji, zobacz [Zarządzanie bazami danych i logowaniami w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Wygasłe błędy limitu czasu połączenia

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Limit czasu połączenia wygasł

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Limit czasu wygasł

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Dostawca bazowy nie powiódł się na open

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nie można połączyć się z < nazwą serwera >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Wyjątki te mogą wystąpić z powodu problemów z połączeniem lub kwerendą. Aby potwierdzić, że ten błąd jest spowodowany przez problemy z łącznością, zobacz [Potwierdź, czy błąd jest spowodowany przez problem z łącznością](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Limity czasu połączenia występują, ponieważ aplikacja nie może połączyć się z serwerem. Aby rozwiązać ten problem, spróbuj wykonać czynności (w przedstawionej kolejności) w sekcji [Kroki, aby rozwiązać typowe problemy z połączeniem.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Błędy zarządzania zasobami

### <a name="error-10928-resource-id-d"></a>Błąd 10928: Identyfikator zasobu: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Aby obejść ten problem, wypróbuj jedną z następujących metod:

* Sprawdź, czy istnieją długotrwałe kwerendy.

  > [!NOTE]
  > Jest to minimalistyczne podejście, które może nie rozwiązać problemu.

1. Uruchom następującą kwerendę SQL, aby sprawdzić widok [sys.dm_exec_requests,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) aby wyświetlić wszelkie żądania blokowania:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Określ **bufor wejściowy** dla blokera głowicy.
3. Dostroić kwerendę blokera głowicy.

   Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [Czy moje zapytanie działa poprawnie w chmurze?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Jeśli baza danych konsekwentnie osiągnie swój limit pomimo adresowania blokowania i długotrwałych zapytań, rozważ uaktualnienie do wersji z większą licznymi [wersjami](https://azure.microsoft.com/pricing/details/sql-database/)zasobów).

Aby uzyskać więcej informacji na temat dynamicznych widoków zarządzania, zobacz [Widoki dynamicznego zarządzania systemem](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Aby uzyskać więcej informacji na temat limitów bazy danych, zobacz [Limity zasobów bazy danych SQL dla serwera usługi Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Błąd 10929: Identyfikator zasobu: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Błąd 40501: Usługa jest obecnie zajęta

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Jest to błąd ograniczania aparatu, wskazanie, że limity zasobów są przekroczone.

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [Limity zasobów serwera bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Błąd 40544: Baza danych osiągnęła przydział rozmiaru

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ten błąd występuje, gdy baza danych osiągnęła swój przydział rozmiaru.

Poniższe kroki mogą pomóc w obejściu problemu lub zapewnić dodatkowe opcje:

1. Sprawdź bieżący rozmiar bazy danych przy użyciu pulpitu nawigacyjnego w witrynie Azure portal.

   > [!NOTE]
   > Aby określić, które tabele zużywają najwięcej miejsca i dlatego są potencjalnymi kandydatami do oczyszczania, uruchom następującą kwerendę SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Jeśli bieżący rozmiar nie przekracza maksymalnego rozmiaru obsługiwanego dla twojej wersji, można użyć alter database, aby zwiększyć ustawienie MAXSIZE.
3. Jeśli baza danych przekroczyła już maksymalny obsługiwany rozmiar dla twojej wersji, spróbuj wykonać co najmniej jeden z następujących kroków:

   * Wykonaj normalne działania oczyszczania bazy danych. Na przykład oczyścić niechciane dane za pomocą obcięć/usunąć lub przenieść dane za pomocą SQL Server Integration Services (SSIS) lub narzędzie programu kopiowania zbiorczego (bcp).
   * Partycji lub usunąć dane, indeksy upuszczania lub zapoznaj się z dokumentacją dla możliwych rozwiązań.
   * Aby uzyskać skalowanie bazy danych, zobacz [Skalowanie zasobów pojedynczej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) i [Skalowanie zasobów puli elastycznej](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Błąd 40549: Sesja została zakończona, ponieważ masz długotrwałą transakcję

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Jeśli ten błąd jest wielokrotnie napotkany, spróbuj rozwiązać ten problem, wykonując następujące kroki:

1. Sprawdź widok sys.dm_exec_requests, aby wyświetlić otwarte sesje, które mają wysoką wartość dla kolumny total_elapsed_time. Wykonaj tę kontrolę, uruchamiając następujący skrypt SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Określ bufor wejściowy dla długotrwałej kwerendy.
3. Dostrajanie kwerendy.

Należy również rozważyć wsadowanie zapytań. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Jak używać przetwarzania wsadowego w celu zwiększenia wydajności aplikacji bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [Czy moje zapytanie działa poprawnie w chmurze?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Błąd 40551: Sesja została zakończona z powodu nadmiernego użycia bazy danych TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Aby obejść ten problem, wykonaj następujące kroki:

1. Zmień kwerendy, aby zmniejszyć tymczasowe użycie miejsca w tabeli.
2. Upuść obiekty tymczasowe, gdy nie są już potrzebne.
3. Obcinanie tabel lub usuwanie nieużywanych tabel.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Błąd 40552: Sesja została zakończona z powodu nadmiernego użycia miejsca w dzienniku transakcji

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Aby rozwiązać ten problem, wypróbuj poniższe metody:

* Problem może wystąpić z powodu operacji wstawiania, aktualizowania lub usuwania.
Spróbuj zmniejszyć liczbę wierszy, które są obsługiwane natychmiast przez implementowanie przetwarzania wsadowego lub dzielenia na wiele mniejszych transakcji.
* Problem może wystąpić z powodu operacji przebudowy indeksu. Aby obejść ten problem, upewnij się, że liczba wierszy, których dotyczy tabela * (średni rozmiar pola, które jest aktualizowane w bajtach + 80) < 2 gigabajtów (GB).

  > [!NOTE]
  > W przypadku przebudowy indeksu średni rozmiar zaktualizowanego pola powinien zostać zastąpiony średnim rozmiarem indeksu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Błąd 40553: Sesja została zakończona z powodu nadmiernego użycia pamięci

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Aby obejść ten problem, spróbuj zoptymalizować kwerendę.

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [Czy moje zapytanie działa poprawnie w chmurze?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela dodatkowych komunikatów o błędach ładu zasobów

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 10928 |20 |Identyfikator zasobu: %d. Limit %s dla bazy danych wynosi %d i został osiągnięty. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL dla pojedynczych i puli baz danych](sql-database-resource-limits-database-server.md).<br/><br/>Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobu jego rozwiązania, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Identyfikator zasobu: %d. Minimalna gwarancja %s to %d, maksymalny limit to %d, a bieżące użycie bazy danych to %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż %d dla tej bazy danych. Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów serwera bazy danych](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [Limity oparte na jednostce DTU dla pojedynczych baz danych](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md). <br/>W przeciwnym razie spróbuj ponownie później. |
| 40544 |20 |Baza danych osiągnęła swój przydział rozmiaru. Partycji lub usunąć dane, indeksy upuszczania lub zapoznaj się z dokumentacją dla możliwych rozwiązań. Aby uzyskać skalowanie bazy danych, zobacz [Skalowanie zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) i [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Sesja jest zakończona, ponieważ masz długotrwałą transakcję. Spróbuj skrócić transakcję. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Jak używać przetwarzania wsadowego w celu zwiększenia wydajności aplikacji bazy danych SQL](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Sesja została zakończona, ponieważ nabyła zbyt wiele blokad. Spróbuj odczytać lub zmodyfikować mniej wierszy w jednej transakcji. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Jak używać przetwarzania wsadowego w celu zwiększenia wydajności aplikacji bazy danych SQL](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Sesja została zakończona z powodu `TEMPDB` nadmiernego użycia. Spróbuj zmodyfikować kwerendę, aby zmniejszyć tymczasowe użycie miejsca w tabeli.<br/><br/>Jeśli używasz obiektów tymczasowych, oszczędzaj `TEMPDB` miejsce w bazie danych, upuszczając obiekty tymczasowe, gdy nie są już potrzebne przez sesję. Aby uzyskać więcej informacji na temat użycia bazy danych tempdb w bazie danych SQL, zobacz [Baza danych Tempdb w bazie danych SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sesja została zakończona z powodu nadmiernego użycia miejsca w dzienniku transakcji. Spróbuj zmodyfikować mniej wierszy w jednej transakcji. Aby uzyskać informacje na temat przetwarzania wsadowego, zobacz [Jak używać przetwarzania wsadowego w celu zwiększenia wydajności aplikacji bazy danych SQL](sql-database-use-batching-to-improve-performance.md).<br/><br/>Jeśli wykonujesz wstawia `bcp.exe` zbiorcze `System.Data.SqlClient.SqlBulkCopy` za pomocą `-b batchsize` narzędzia `BatchSize` lub klasy, spróbuj użyć opcji lub, aby ograniczyć liczbę wierszy skopiowanych do serwera w każdej transakcji. Jeśli przebudowujesz indeks z `ALTER INDEX` instrukcją, spróbuj `REBUILD WITH ONLINE = ON` użyć tej opcji. Aby uzyskać informacje na temat rozmiarów dzienników transakcji dla modelu zakupu w yt, zobacz: <br/>&bull;&nbsp; [Limity oparte na wynikach wirtualnych dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [Limity oparte na pulach elastycznych oparte na pulach](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Sesja została zakończona z powodu nadmiernego użycia pamięci. Spróbuj zmodyfikować zapytanie, aby przetworzyć mniejszą liczbę wierszy.<br/><br/>Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie Transact-SQL zmniejsza wymagania dotyczące pamięci zapytania. Aby uzyskać skalowanie bazy danych, zobacz [Skalowanie zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) i [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Błędy puli elastycznej

Następujące błędy są związane z tworzeniem i używaniem pul elastycznych:

| Kod błędu | Ważność | Opis | Działania naprawcze |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Pula elastyczna osiągnęła limit magazynowania. Użycie magazynu dla puli elastycznej nie może przekraczać (%d) MB. Próba zapisania danych w bazie danych po osiągnięciu limitu magazynowania puli elastycznej. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na platformach vCore dla basenów elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Należy rozważyć zwiększenie jednostek DTU i/lub dodanie magazynu do puli elastycznej, jeśli to możliwe, aby zwiększyć limit magazynu, zmniejszyć magazyn używany przez poszczególne bazy danych w puli elastycznej lub usunąć bazy danych z puli elastycznej. Aby uzyskać skalowanie puli elastycznej, zobacz [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Minimalna gwarancja %s to %d, maksymalny limit to %d, a bieżące użycie bazy danych to %d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż %d dla tej bazy danych. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;&nbsp; [Limity oparte na UDU dla basenów elastycznych](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Limity oparte na platformach vCore dla basenów elastycznych](sql-database-vcore-resource-limits-elastic-pools.md). <br/> W przeciwnym razie spróbuj ponownie później. DTU / vCore min na bazę danych; DTU / vCore max na bazę danych. Całkowita liczba równoczesnych pracowników (żądań) we wszystkich bazach danych w puli elastycznej próbował przekroczyć limit puli. |Należy rozważyć zwiększenie DTU lub vCores puli elastycznej, jeśli to możliwe, aby zwiększyć jego limit procesu roboczego lub usunąć bazy danych z puli elastycznej. |
| 40844 | 16 |Baza danych "%ls" na serwerze "%ls" jest bazą danych edycji "%ls" w puli elastycznej i nie może mieć ciągłej relacji kopiowania.  |Nie dotyczy |
| 40857 | 16 |Nie znaleziono puli elastycznej dla serwera: '%ls', nazwa puli elastycznej: '%ls'. Określona pula elastyczna nie istnieje na określonym serwerze. | Podaj prawidłową nazwę puli elastycznej. |
| 40858 | 16 |Pula elastyczna '%ls' już istnieje na serwerze: '%ls'. Określona pula elastyczna już istnieje na określonym serwerze bazy danych SQL. | Podaj nową nazwę puli elastycznej. |
| 40859 | 16 |Pula elastyczna nie obsługuje warstwy usług "%ls". Określona warstwa usług nie jest obsługiwana w przypadku inicjowania obsługi administracyjnej puli elastycznej. |Podaj poprawną wersję lub pozostaw warstwę usługi pustą, aby użyć domyślnej warstwy usług. |
| 40860 | 16 |Kombinacja "%ls" i cel usługi "%ls" jest nieprawidłowa. Pula elastyczna i warstwa usług można określić razem tylko wtedy, gdy typ zasobu jest określony jako "ElasticPool". |Określ prawidłową kombinację puli elastycznej i warstwy usług. |
| 40861 | 16 |Wydanie bazy danych '%. *ls' nie może być inny niż warstwa usługi puli elastycznej, która jest "%.* ls". Wersja bazy danych różni się od warstwy usługi puli elastycznej. |Nie należy określać wersji bazy danych, która różni się od warstwy usługi puli elastycznej.  Należy zauważyć, że nie trzeba określać wersji bazy danych. |
| 40862 | 16 |Nazwa puli elastycznej musi być określona, jeśli określono cel usługi puli elastycznej. Cel usługi puli elastycznej nie jednoznacznie zidentyfikować puli elastycznej. |Określ nazwę puli elastycznej, jeśli używasz celu usługi puli elastycznej. |
| 40864 | 16 |DTU dla puli elastycznej musi być co najmniej (%d) DTU dla warstwy usług "%.*ls". Próba ustawienie DTU dla puli elastycznej poniżej minimalnego limitu. |Ponów próbę ustawienia DTU dla puli elastycznej na co najmniej minimalny limit. |
| 40865 | 16 |DTU dla puli elastycznej nie może przekraczać (%d) DTU dla warstwy usług "%.*ls". Próba ustawienie DTU dla puli elastycznej powyżej maksymalnego limitu. |Ponów próbę ustawienia DTU dla puli elastycznej nie większą niż maksymalny limit. |
| 40867 | 16 |Maksymalna wartość DTU na bazę danych musi być co najmniej (%d) dla warstwy usług "%.*ls". Próba skonfigurowania maksymalnej wartości DTU na bazę danych poniżej obsługiwanego limitu. | Należy rozważyć użycie warstwy usługi puli elastycznej, która obsługuje żądane ustawienie. |
| 40868 | 16 |Maksymalna wartość DTU na bazę danych nie może przekraczać (%d) dla warstwy usług "%.*ls". Próba skonfigurowania maksymalnej wartości DTU na bazę danych poza obsługiwanym limitem. | Należy rozważyć użycie warstwy usługi puli elastycznej, która obsługuje żądane ustawienie. |
| 40870 | 16 |Wartość DTU min na bazę danych nie może przekraczać (%d) dla warstwy usług "%.*ls". Próba skonfigurowania min DTU na bazę danych poza obsługiwanym limitem. | Należy rozważyć użycie warstwy usługi puli elastycznej, która obsługuje żądane ustawienie. |
| 40873 | 16 |Liczba baz danych (%d) i DTU min na bazę danych (%d) nie może przekraczać DTU puli elastycznej (%d). Próba określenia dtu min dla baz danych w puli elastycznej, która przekracza DTU puli elastycznej. | Należy rozważyć zwiększenie DTU puli elastycznej lub zmniejszyć dtu min na bazę danych lub zmniejszyć liczbę baz danych w puli elastycznej. |
| 40877 | 16 |Nie można usunąć puli elastycznej, chyba że nie zawiera żadnych baz danych. Pula elastyczna zawiera jedną lub więcej baz danych i dlatego nie można usunąć. |Usuń bazy danych z puli elastycznej, aby ją usunąć. |
| 40881 | 16 |Pula elastyczna '%.*ls' osiągnęła limit liczby baz danych.  Limit liczby baz danych dla puli elastycznej nie może przekraczać (%d) dla puli elastycznej z procesorami DTU (%d). Próba utworzenia lub dodania bazy danych do puli elastycznej po osiągnięciu limitu liczby baz danych puli elastycznej. | Należy rozważyć zwiększenie DTU puli elastycznej, jeśli to możliwe, aby zwiększyć limit bazy danych lub usunąć bazy danych z puli elastycznej. |
| 40889 | 16 |Nie można zmniejszyć limitu DTU lub magazynu dla puli elastycznej "%.*ls", ponieważ nie zapewniłoby to wystarczającej ilości miejsca do magazynowania dla jego baz danych. Próba zmniejszenia limitu magazynowania puli elastycznej poniżej jego użycia magazynu. | Należy rozważyć zmniejszenie użycia magazynu poszczególnych baz danych w puli elastycznej lub usunąć bazy danych z puli w celu zmniejszenia jego jednostek DTU lub limit magazynu. |
| 40891 | 16 |Wartość DTU min na bazę danych (%d) nie może przekraczać maksymalnej wartości DTU na bazę danych (%d). Próba skonfigurowania min DTU na bazę danych wyższej niż maksymalna wartość DTU na bazę danych. |Upewnij się, że wartość DTU min na bazy danych nie przekracza maksymalnej liczby danych DTU na bazę danych. |
| TBD | 16 |Rozmiar magazynu dla pojedynczej bazy danych w puli elastycznej nie może przekraczać maksymalnego rozmiaru dozwolonego przez pulę elastyczną warstwy usługi "%.*ls". Maksymalny rozmiar bazy danych przekracza maksymalny rozmiar dozwolony przez warstwę usług puli elastycznej. |Ustaw maksymalny rozmiar bazy danych w granicach maksymalnego rozmiaru dozwolonego przez warstwę usług puli elastycznej. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Nie można otworzyć bazy danych "master" wymagane przez login. Logowanie nie powiodło się

Ten problem występuje, ponieważ konto nie ma uprawnień dostępu do głównej bazy danych. Ale domyślnie SQL Server Management Studio (SSMS) próbuje połączyć się z główną bazą danych.

Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Na ekranie logowania ssms wybierz pozycję **Opcje**, a następnie wybierz pozycję **Właściwości połączenia**.
2. W polu **Połącz z bazą danych** wprowadź domyślną nazwę bazy danych użytkownika jako domyślną bazę danych logowania, a następnie wybierz pozycję **Połącz**.

   ![Connection properties (Właściwości połączenia)](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Sprawdzanie, czy błąd jest spowodowany przez problem z łącznością

Aby sprawdzić, czy błąd jest spowodowany przez problem z łącznością, przejrzyj śledzenie stosu dla ramek, które pokazują wywołania otwarcia połączenia, takie jak następujące (zwróć uwagę na odwołanie do klasy **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Gdy wyjątek jest wyzwalany przez problemy z kwerendami, można zauważyć stos wywołań, który jest podobny do następującego (należy zwrócić uwagę na odwołanie do **klasy SqlCommand).** W tej sytuacji [dostroić zapytania](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Aby uzyskać dodatkowe wskazówki dotyczące wydajności dostrajania, zobacz następujące zasoby:

* [Jak zachować indeksy i statystyki SQL platformy Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Ręczne dostosowywanie wydajności kwerendy w bazie danych SQL usługi Azure](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Monitorowanie wydajności usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Obsługa magazynu zapytań w bazie danych SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Steps to fix common connection issues (Kroki umożliwiające rozwiązywanie typowych problemów z połączeniem)

1. Upewnij się, że protokół TCP/IP jest włączony jako protokół klienta na serwerze aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołów klientów](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na serwerach aplikacji, na których nie zainstalowano narzędzi programu SQL Server, sprawdź, czy protokół TCP/IP jest włączony przez uruchomienie **pliku cliconfg.exe** (narzędzie SIECI KLIENTA PROGRAMU SQL Server).
2. Sprawdź parametry połączenia aplikacji, aby upewnić się, że jest poprawnie skonfigurowany. Na przykład upewnij się, że parametry połączenia określają poprawny port (1433) i w pełni kwalifikowaną nazwę serwera.
Zobacz: [Odbieranie informacji o połączeniu programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Spróbuj zwiększyć wartość limitu czasu połączenia. Zalecamy użycie limitu czasu połączenia co najmniej 30 sekund.
4. Przetestuj łączność między serwerem aplikacji a bazą danych SQL azure przy użyciu [programu SQL Server management Studio (SSMS),](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)pliku UDL, pingu lub telnetu. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością programu SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) i [diagnostyka problemów z łącznością](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Jako krok rozwiązywania problemów można również przetestować łączność na innym komputerze klienckim.

5. Najlepszym rozwiązaniem, upewnij się, że logika ponawiania jest w miejscu. Aby uzyskać więcej informacji na temat logiki ponawiania prób, zobacz [Rozwiązywanie problemów z błędami przejściowymi i błędami połączenia z bazą danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Jeśli te kroki nie rozwiążą problemu, spróbuj zebrać więcej danych, a następnie skontaktuj się z pomocą techniczną. Jeśli aplikacja jest usługą w chmurze, włącz rejestrowanie. Ten krok zwraca sygnaturę czasową UTC błędu. Ponadto sql azure zwraca identyfikator śledzenia. [Usługi obsługi klienta firmy Microsoft](https://azure.microsoft.com/support/options/) mogą korzystać z tych informacji.

Aby uzyskać więcej informacji na temat włączania rejestrowania, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Następne kroki

* [Architektura łączności SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Kontrolki dostępu do bazy danych i magazynu danych usługi Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
