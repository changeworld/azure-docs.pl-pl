---
title: Rozwiązywanie różnic T-SQL-migracja
description: Instrukcje języka Transact-SQL, które nie są w pełni obsługiwane w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208817"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Rozwiązywanie różnic transact-SQL podczas migracji do bazy danych SQL

Podczas [migracji bazy danych](sql-database-single-database-migrate.md) z programu SQL Server do programu Azure SQL Server można stwierdzić, że baza danych wymaga pewnego ponownego inżynierii przed sql server można migrować. Ten artykuł zawiera wskazówki, które pomogą Ci zarówno w wykonywaniu tej re-engineering i zrozumienia podstawowych powodów, dla których re-engineering jest konieczne. Aby wykryć niezgodności, użyj [Asystenta migracji danych (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Omówienie

Większość funkcji Transact-SQL, których używają aplikacje, jest w pełni obsługiwana zarówno w programie Microsoft SQL Server, jak i w bazie danych SQL Azure. Na przykład podstawowe składniki SQL, takie jak typy danych, operatory, ciąg, arytmetyka, logiczne i funkcje kursora, działają identycznie w sql server i bazy danych SQL. Istnieje jednak kilka różnic T-SQL w DDL (język definicji danych) i DML (język manipulowania danymi) elementy w wyniku instrukcji T-SQL i kwerend, które są tylko częściowo obsługiwane (które omówimy w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składnia, która nie jest obsługiwana w ogóle, ponieważ usługa Azure SQL Database jest przeznaczona do izolowania funkcji od zależności od głównej bazy danych i systemu operacyjnego. W związku z tym większość działań na poziomie serwera są nieodpowiednie dla bazy danych SQL. Instrukcje i opcje T-SQL nie są dostępne, jeśli konfigurują opcje na poziomie serwera, składniki systemu operacyjnego lub określają konfigurację systemu plików. Gdy takie możliwości są wymagane, odpowiednia alternatywa jest często dostępna w inny sposób z bazy danych SQL lub z innej funkcji lub usługi platformy Azure.

Na przykład wysoka dostępność jest wbudowana w usługę Azure SQL Database przy użyciu technologii podobnej do [grup dostępności zawsze włączone.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) Instrukcje T-SQL związane z grupami dostępności nie są obsługiwane przez bazę danych SQL, a dynamiczne widoki zarządzania związane z grupami zawsze na dostępności również nie są obsługiwane.

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwały przez bazę danych SQL, zobacz [Porównanie funkcji usługi Azure SQL Database](sql-database-features.md). Lista na tej stronie uzupełnia, że wytyczne i funkcje artykułu i koncentruje się na instrukcje Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrukcje składni Transact-SQL z częściowymi różnicami

Podstawowe instrukcje DDL (język definicji danych) są dostępne, ale niektóre instrukcje DDL mają rozszerzenia związane z rozmieszczeniem dysku i nieobsługiwałem funkcji.

- Instrukcje CREATE i ALTER DATABASE mają ponad trzy tuziny opcji. Instrukcje obejmują umieszczanie plików, FILESTREAM i opcje brokera usług, które dotyczą tylko programu SQL Server. Może to nie mieć znaczenia, jeśli tworzysz bazy danych przed migracją, ale jeśli migrujesz kod T-SQL, który tworzy bazy danych, należy porównać [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) ze składnią programu SQL Server w [bazie danych CREATE (SQL Server Transact-SQL),](https://msdn.microsoft.com/library/ms176061.aspx) aby upewnić się, że wszystkie opcje, których używasz, są obsługiwane. TWORZENIE BAZY DANYCH dla usługi Azure SQL Database ma również opcje skali celowej usługi i elastycznej skali, które mają zastosowanie tylko do bazy danych SQL.
- Instrukcje CREATE i ALTER TABLE mają opcje FileTable, których nie można używać w bazie danych SQL, ponieważ filestream nie jest obsługiwany.
- Instrukcje logowania CREATE i ALTER są obsługiwane, ale baza danych SQL nie oferuje wszystkich opcji. Aby baza danych była bardziej przenośna, baza danych SQL zachęca do używania użytkowników zawartej bazy danych zamiast logowania, gdy tylko jest to możliwe. Aby uzyskać więcej informacji, zobacz [TWORZENIE/ZMIENIANIE LOGOWANIA](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) i [Zarządzanie loginami i użytkownikami](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Składnia Transact-SQL nie jest obsługiwana w bazie danych SQL usługi Azure

Oprócz instrukcji Transact-SQL związanych z nieobsługiwał funkcje opisane w [usłudze Azure SQL Database porównanie funkcji,](sql-database-features.md)następujące instrukcje i grupy instrukcji, nie są obsługiwane. W związku z tym jeśli baza danych do migracji używa dowolnej z następujących funkcji, ponownie zaprojektować T-SQL, aby wyeliminować te funkcje i instrukcje T-SQL.

- Sortowanie obiektów systemu
- Powiązane z połączeniem: instrukcje punktu końcowego. Usługa SQL Database nie obsługuje uwierzytelniania systemu Windows, ale obsługuje podobne uwierzytelnianie usługi Azure Active Directory. Niektóre typy uwierzytelniania wymagają najnowszej wersji usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Nawiązywanie połączeń z usługami SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory).
- Zapytania obejmujące wiele baz danych, korzystające z nazw trój- i czteroczęściowych. (Zapytania tylko do odczytu obejmujące wiele baz danych są obsługiwane za pomocą [zapytania elastycznej bazy danych](sql-database-elastic-query-overview.md)).
- Tworzenie łańcucha własności między wieloma bazami danych, ustawienie `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Zamiast tego użyj instrukcji „EXECUTE AS USER”.
- Szyfrowanie jest obsługiwane z wyjątkiem rozszerzonego zarządzania kluczami
- Eventing: zdarzenia, powiadomienia o zdarzeniach, powiadomienia o kwerendach
- Umieszczanie plików: Składnia związana z umieszczaniem plików bazy danych, rozmiarem i plikami bazy danych, które są automatycznie zarządzane przez platformę Microsoft Azure.
- Wysoka dostępność: składnia związana z wysoką dostępnością, która jest zarządzana za pośrednictwem konta Microsoft Azure. Obejmuje to składnię kopii zapasowych, przywracania, funkcji Always On, dublowania bazy danych, wysyłania dziennika oraz trybów odzyskiwania.
- Czytnik dzienników: Składnia, która opiera się na czytniku dzienników, który nie jest dostępny w bazie danych SQL: Replikacja wypychania, zmienianie przechwytywania danych. Baza danych SQL Database może być subskrybentem artykułu replikacji wypychanej.
- Funkcje: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Sprzęt: Składnia związana z ustawieniami serwera związanymi ze sprzętem: takie jak pamięć, wątki robocze, koligacja procesora, flagi śledzenia. Zamiast tego użyj warstw usług i rozmiarów obliczeń.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`, i nazwy czteroczęściowe
- .NET Framework: integracja programu CLR z programem SQL Server
- Wyszukiwanie semantyczne
- Poświadczenia serwera: zamiast tego użyj [poświadczeń o zakresie bazy danych.](https://msdn.microsoft.com/library/mt270260.aspx)
- Elementy na poziomie serwera: `sys.login_token`role serwera, . Instrukcje `GRANT`, `REVOKE` i `DENY` w odniesieniu do uprawnień na poziomie serwera nie są dostępne, chociaż niektóre zostały zastąpione przez uprawnienia na poziomie bazy danych. Niektóre przydatne dynamiczne widoki zarządzania na poziomie serwera mają swoje odpowiedniki na poziomie bazy danych.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opcje polecenia `sp_configure` i instrukcja `RECONFIGURE`. Niektóre opcje są dostępne po użyciu instrukcji [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent programu SQL Server: Składnia, która opiera się na agencie programu SQL Server lub bazie danych MSDB: alerty, operatory, centralne serwery zarządzania. Zamiast tego używaj skryptów, takich jak Azure PowerShell.
- Inspekcja programu SQL Server: zamiast tego użyj inspekcji bazy danych SQL.
- Śledzenie programu SQL Server
- Flagi śledzenia: niektóre elementy flagi śledzenia zostały przeniesione do trybów zgodności.
- Debugowanie języka Transact-SQL
- Wyzwalacze: wyzwalacze zakresu serwera lub wyzwalacze logowania
- Instrukcja `USE`: aby zmienić kontekst bazy danych na inną bazę danych, należy wprowadzić nowe połączenie z nową bazą danych.

## <a name="full-transact-sql-reference"></a>Pełna dokumentacja języka Transact-SQL

Aby uzyskać więcej informacji na temat gramatyki, użycia i przykładów transakcji-SQL, zobacz [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) w programie SQL Server Books Online.

### <a name="about-the-applies-to-tags"></a>Tagi „Applies to” (Dotyczy)

Odwołanie do transact-SQL zawiera artykuły związane z programem SQL Server w wersjach od 2008 do chwili obecnej. Pod tytułem artykułu znajduje się pasek ikon, zawierający listę czterech platform PROGRAMU SQL Server i wskazujący na możliwość zastosowania. Na przykład grupy dostępności zostały wprowadzone w programie SQL Server 2012. Artykuł [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) wskazuje, że instrukcja dotyczy programu **SQL Server (począwszy od 2012 r.).** Instrukcja nie dotyczy programu SQL Server 2008, SQL Server 2008 R2, usługi Azure SQL Database, programu Azure SQL Data Warehouse ani Parallel Data Warehouse.

W niektórych przypadkach ogólny przedmiot artykułu może być stosowany w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w punktach środkowych w artykule, stosownie do przypadku. W niektórych przypadkach ogólny przedmiot artykułu może być stosowany w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w punktach środkowych w artykule, stosownie do przypadku. Na przykład artykuł CREATE TRIGGER jest dostępny w bazie danych SQL. Ale opcja **ALL SERVER** dla wyzwalaczy na poziomie serwera wskazuje, że wyzwalacze na poziomie serwera nie mogą być używane w bazie danych SQL. Zamiast tego użyj wyzwalaczy na poziomie bazy danych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwały przez bazę danych SQL, zobacz [Porównanie funkcji usługi Azure SQL Database](sql-database-features.md). Lista na tej stronie uzupełnia, że wytyczne i funkcje artykułu i koncentruje się na instrukcje Transact-SQL.
