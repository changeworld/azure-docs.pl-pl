---
title: Rozpoznawanie T-SQL różnice migracji Azure SQL Database | Dokumentacja firmy Microsoft
description: Instrukcje języka Transact-SQL, które nie są w pełni obsługiwane w usłudze Azure SQL Database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: e89c863ac50a8b906b388c505f444cd60fdbaad3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649078"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Rozstrzyganie różnic języka Transact-SQL podczas migracji z bazą danych SQL   
Gdy [Migrowanie bazy danych](sql-database-cloud-migrate.md) z programu SQL Server do serwera SQL Azure, użytkownik może stwierdzić, że baza danych wymaga niektórych reorganizacji zanim można dokonać migracji programu SQL Server. Ten artykuł zawiera wskazówki, które ułatwiają wykonywanie ponownego projektowania, a także opis podstawowych powodów, dlaczego ponownego projektowania jest konieczna. Aby wykryć niezgodności, użyj [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Przegląd
Większość funkcji języka Transact-SQL, które używają aplikacji są w pełni obsługiwane zarówno w przypadku programu Microsoft SQL Server, jak i bazy danych SQL Azure. Na przykład podstawowe składniki SQL typów danych, operatory, ciąg, operacje arytmetyczne, logiczna, i funkcje kursora działają tak samo w programie SQL Server i bazy danych SQL. Istnieją, jednak niewielkie różnice T-SQL w pliku DDL (języka definicji danych) i elementy DML (język edycji danych), co zapewnia instrukcje T-SQL i zapytań, które są obsługiwane tylko częściowo (co omówiono w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składnię, która nie jest obsługiwana w ogóle, ponieważ baza danych SQL Azure jest przeznaczona do izolowania funkcji z zależności w bazie danych master i systemu operacyjnego. W efekcie większości działań na poziomie serwera nie mają zastosowania do bazy danych SQL. Instrukcje T-SQL i opcje nie są dostępne, jeśli skonfiguruj opcje na poziomie serwera, składników systemu operacyjnego, lub określ plik konfiguracji systemu. Jeśli takie możliwości są wymagane, właściwym rozwiązaniem często jest dostępny w inny sposób funkcji z bazy danych SQL lub innej platformy Azure lub usługi. 

Na przykład wysokiej dostępności jest oparty na platformie Azure, dlatego nie jest konieczne konfigurowanie zawsze włączone, (mimo że można skonfigurować aktywna replikacja geograficzna szybsze odzyskiwania w przypadku awarii). Tak instrukcje T-SQL dotyczące grup dostępności nie są obsługiwane przez usługę SQL Database i dynamicznych widoków zarządzania związane z zawsze włączone nie również są obsługiwane.

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwane przez bazę danych SQL, zobacz [porównanie funkcji bazy danych SQL Azure](sql-database-features.md). Na liście na tej stronie stanowi uzupełnienie tego artykułu wskazówki i funkcje, a koncentruje się na instrukcji języka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrukcje składni języka Transact-SQL z częściowa różnic
Core instrukcji DDL (języka definicji danych) są dostępne, ale niektóre instrukcji DDL rozszerzenia odnoszące się do umieszczania dysku i nieobsługiwane funkcje. 

- Instrukcje CREATE i ALTER DATABASE ma ponad dwanaście trzy opcje. Instrukcje zawierają umieszczania plików FILESTREAM i opcje brokera usług, które dotyczą tylko programu SQL Server. To może niezależnie od tego, czy tworzenie baz danych, przed przeprowadzeniem migracji, ale w przypadku migracji kod T-SQL, który tworzy bazy danych należy porównać [CREATE DATABASE (baza danych SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) przy użyciu składni SQL Server na [Utwórz Bazy danych (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) się upewnić, że wszystkie opcje używane są obsługiwane. Tworzenie bazy danych dla bazy danych SQL Azure ma również cel usługi i opcje elastycznego skalowania, które są stosowane tylko do bazy danych SQL.
- Instrukcje CREATE i ALTER TABLE ma FileTable opcje, których nie można używać w bazie danych SQL, ponieważ FILESTREAM nie jest obsługiwany.
- Tworzenie i ALTER login instrukcje są obsługiwane, ale baza danych SQL nie oferuje wszystkie opcje. Aby wprowadzić przenośną bazy danych, baza danych SQL zachęca za pomocą użytkowników zawartej bazy danych zamiast nazwy logowania, jeśli to możliwe. Aby uzyskać więcej informacji, zobacz [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) i [kontrolowanie i udzielanie dostępu do bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Składnia języka Transact-SQL nie jest obsługiwane w bazie danych SQL Azure   
Oprócz związane z nieobsługiwane funkcje opisane w instrukcji Transact-SQL [porównanie funkcji bazy danych SQL Azure](sql-database-features.md), nie są obsługiwane następujące instrukcje i grup instrukcji. Tak Jeśli bazy danych do migracji używa żadnego z następujących funkcji, ponownie opracowywać Twojej T-SQL Aby wyeliminować te funkcje T-SQL i instrukcje.

- Sortowanie obiektów systemu
- Połączenie związane z: instrukcje punktu końcowego. Usługa SQL Database nie obsługuje uwierzytelniania systemu Windows, ale obsługuje podobne uwierzytelnianie usługi Azure Active Directory. Niektóre typy uwierzytelniania wymagają najnowszej wersji usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Nawiązywanie połączeń z usługami SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory).
- Zapytania obejmujące wiele baz danych, korzystające z nazw trój- i czteroczęściowych. (Zapytania tylko do odczytu obejmujące wiele baz danych są obsługiwane za pomocą [zapytania elastycznej bazy danych](sql-database-elastic-query-overview.md)).
- Tworzenie łańcucha własności między wieloma bazami danych, ustawienie `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Zamiast tego użyj instrukcji „EXECUTE AS USER”.
- Szyfrowanie jest obsługiwane z wyjątkiem rozszerzonego zarządzania kluczami
- Obsługi zdarzeń: Zdarzeń, powiadomień o zdarzeniach, powiadomienia kwerendy
- Położenie pliku: Składnia związane z położenie pliku bazy danych, rozmiar i pliki bazy danych, które są automatycznie zarządzane przez Microsoft Azure.
- Wysoka dostępność: Składnia związane z wysoką dostępność, która jest zarządzana za pomocą konta Microsoft Azure. Obejmuje to składnię kopii zapasowych, przywracania, funkcji Always On, dublowania bazy danych, wysyłania dziennika oraz trybów odzyskiwania.
- Czytnik dziennika: składnię, która opiera się czytnik dziennika, który nie jest dostępny w bazie danych SQL: Push replikacji, przechwytywania zmian danych. Baza danych SQL Database może być subskrybentem artykułu replikacji wypychanej.
- Funkcje: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Sprzęt: Składnia związane z ustawieniami związane ze sprzętem serwera: takich jak pamięć wątków roboczych, koligacji procesora CPU, flagi śledzenia. Zamiast tego użyj poziomów usług.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`i czteroczęściową nazwy
- .NET framework: CLR integracji z programem SQL Server
- Wyszukiwanie semantyczne
- Poświadczenia serwera: Użyj [poświadczeń o zakresie bazy danych](https://msdn.microsoft.com/library/mt270260.aspx) zamiast tego.
- Elementy w poziomie serwera: ról serwera, `sys.login_token`. Instrukcje `GRANT`, `REVOKE` i `DENY` w odniesieniu do uprawnień na poziomie serwera nie są dostępne, chociaż niektóre zostały zastąpione przez uprawnienia na poziomie bazy danych. Niektóre przydatne dynamiczne widoki zarządzania na poziomie serwera mają swoje odpowiedniki na poziomie bazy danych.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opcje polecenia `sp_configure` i instrukcja `RECONFIGURE`. Niektóre opcje są dostępne po użyciu instrukcji [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: Składnię, która opiera się na agenta programu SQL Server lub bazie danych MSDB: alerty, operatory, centralnego zarządzania serwerami. Zamiast tego używaj skryptów, takich jak Azure PowerShell.
- SQL Server audit: Użyj bazy danych SQL zamiast inspekcji.
- Śledzenie programu SQL Server
- Flagi śledzenia: niektóre elementy flagi śledzenia zostały przeniesione do trybów zgodności.
- Debugowanie języka Transact-SQL
- Wyzwalacze: wyzwalacze zakresu serwera lub wyzwalacze logowania
- Instrukcja `USE`: aby zmienić kontekst bazy danych na inną bazę danych, należy wprowadzić nowe połączenie z nową bazą danych.

## <a name="full-transact-sql-reference"></a>Pełna dokumentacja języka Transact-SQL
Aby uzyskać więcej informacji dotyczących gramatyki języka Transact-SQL oraz przykłady jego użycia, zobacz artykuł [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) (Dokumentacja języka Transact-SQL (aparat bazy danych)) w dokumentacji SQL Server — książki online. 

### <a name="about-the-applies-to-tags"></a>Tagi „Applies to” (Dotyczy)
Dokumentacja języka Transact-SQL zawiera artykuły dotyczące wersji programu SQL Server 2008 bieżącej. Pod tytuł artykułu jest ikony paska, wyświetlanie cztery platformy programu SQL Server i wskazujący zastosowania. Na przykład grupy dostępności zostały wprowadzone w programie SQL Server 2012. [Utwórz GRUPĘ AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) artykułu wskazuje, że instrukcja dotyczy **programu SQL Server (począwszy od 2012)**. Instrukcja nie dotyczy programu SQL Server 2008, SQL Server 2008 R2, usługi Azure SQL Database, programu Azure SQL Data Warehouse ani Parallel Data Warehouse.

W niektórych przypadkach można temat artykułu w produkcie, ale występują niewielkie różnice między produktów. Wskazano różnice w punkty środkowe w artykule zależnie od potrzeb. W niektórych przypadkach można temat artykułu w produkcie, ale występują niewielkie różnice między produktów. Wskazano różnice w punkty środkowe w artykule zależnie od potrzeb. Na przykład artykuł CREATE TRIGGER jest dostępny w bazie danych SQL. Ale **wszystkie SERVER** opcji wyzwalaczy na poziomie serwera, wskazuje, że wyzwalaczy poziomu serwera nie może być używane w bazie danych SQL. Zamiast tego Użyj wyzwalaczy na poziomie bazy danych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwane przez bazę danych SQL, zobacz [porównanie funkcji bazy danych SQL Azure](sql-database-features.md). Na liście na tej stronie stanowi uzupełnienie tego artykułu wskazówki i funkcje, a koncentruje się na instrukcji języka Transact-SQL.

