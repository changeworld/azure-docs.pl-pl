---
title: Rozpoznawanie języka T-SQL różnice migracji usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Instrukcje języka Transact-SQL, które nie są w pełni obsługiwane w usłudze Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 4d3f27d48819a4bd997cbb62177f5aae4afc85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615393"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Rozstrzyganie różnic języka Transact-SQL podczas migracji do usługi SQL Database

Gdy [migracji bazy danych](sql-database-single-database-migrate.md) z programu SQL Server do usługi Azure SQL Server, użytkownik może stwierdzić, że baza danych wymaga pewnej reorganizacji przed można poddać migracji programu SQL Server. Ten artykuł zawiera wskazówki, które ułatwiają wykonywanie ponownego zaprojektowania i zrozumienie podstawowej przyczyny, dlaczego ponownego projektowania jest konieczna. Aby wykrywać niezgodności, należy użyć [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Omówienie

Większość funkcji języka Transact-SQL, których aplikacje są w pełni obsługiwane zarówno w przypadku programu Microsoft SQL Server, jak i usługi Azure SQL Database. Na przykład podstawowe elementy języka SQL, takich jak typy danych, operatory, ciąg, arytmetyczne, logiczne oraz funkcje kursora, działają tak samo programu SQL Server i bazy danych SQL. Istnieją, jednak niewielkie różnice języka T-SQL w DDL (języka definicji danych) i elementy DML (język edycji danych), w efekcie instrukcje języka T-SQL oraz zapytania, które są tylko częściowo obsługiwane (co omówiono w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składni, która nie jest obsługiwane na wszystkich, ponieważ usługa Azure SQL Database zaprojektowano w celu uniezależnić funkcje od bazy danych master i systemu operacyjnego. W efekcie większości działań na poziomie serwera nie mają zastosowania do bazy danych SQL. Instrukcje języka T-SQL i opcje nie są dostępne, skonfiguruj opcje poziomu serwera, składników systemu operacyjnego lub określania pliku konfiguracji systemu. Gdy wymagane są takie funkcje, właściwe rozwiązanie jest często dostępne w inny sposób funkcję z bazą danych SQL lub innej platformy Azure lub usługi.

Na przykład, wysokiej dostępności jest wbudowana w usłudze Azure SQL Database przy użyciu technologii, podobnie jak [zawsze włączonych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Instrukcje T-SQL dotyczące grup dostępności nie są obsługiwane przez usługę SQL Database i dynamicznych widoków zarządzania związane z zawsze włączonymi grupami dostępności również nie są obsługiwane.

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwane przez usługę SQL Database, zobacz [porównanie funkcji usługi Azure SQL Database](sql-database-features.md). Na liście na tej stronie uzupełnia artykuł z wytyczne i funkcje i koncentruje się na instrukcji języka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrukcje składni języka Transact-SQL z różnicami częściowe

Podstawowe instrukcji DDL (języka definicji danych) są dostępne, ale niektóre instrukcje języka DDL rozszerzenia związane z umieszczania dysku i nieobsługiwanych funkcji.

- Instrukcje CREATE i ALTER DATABASE mają ponad tuzina trzy opcje. Instrukcje zawierają umieszczania plików FILESTREAM i opcji brokera usług, które dotyczą tylko programu SQL Server. To może nie ma znaczenia, czy tworzyć bazy danych, przed przeprowadzeniem migracji, ale w przypadku migracji kodu T-SQL, który tworzy bazy danych należy porównać [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) przy użyciu składni SQL Server na [CREATE Bazy danych (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) się upewnić, że wszystkie opcje, które są używane są obsługiwane. Tworzenie bazy danych usługi Azure SQL Database ma również cel usługi i opcji funkcja elastyczne skalowanie, które mają zastosowanie tylko do bazy danych SQL.
- Instrukcje CREATE i ALTER TABLE mają FileTable opcje, których nie można używać w bazie danych SQL, ponieważ FILESTREAM nie jest obsługiwany.
- Tworzenie i ALTER instrukcje logowania są obsługiwane, ale baza danych SQL nie oferuje wszystkie opcje. Uczynić bardziej przenośny bazę danych, bazy danych SQL Database zaleca korzystanie z użytkowników zawartej bazy danych zamiast nazwy logowania, jeśli to możliwe. Aby uzyskać więcej informacji, zobacz [polecenia CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) i [kontrolowanie i udzielanie dostępu do bazy danych](sql-database-manage-logins.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Składnia języka Transact-SQL, które nie są obsługiwane w usłudze Azure SQL Database

Oprócz instrukcji języka Transact-SQL, związane z nieobsługiwanych funkcji opisanych w [porównanie funkcji usługi Azure SQL Database](sql-database-features.md), nie są obsługiwane następujące instrukcje i grupy instrukcji. W efekcie Jeśli bazy danych do zmigrowania używa dowolnej z następujących funkcji, ponownie inżynier z języka T-SQL, aby wyeliminować te funkcje języka T-SQL i instrukcji.

- Sortowanie obiektów systemu
- Dotyczące połączeń: Instrukcje punktu końcowego. Usługa SQL Database nie obsługuje uwierzytelniania systemu Windows, ale obsługuje podobne uwierzytelnianie usługi Azure Active Directory. Niektóre typy uwierzytelniania wymagają najnowszej wersji usługi SSMS. Aby uzyskać więcej informacji, zobacz artykuł [Connecting to SQL Database or SQL Data Warehouse By Using Azure Active Directory Authentication](sql-database-aad-authentication.md) (Nawiązywanie połączeń z usługami SQL Database lub SQL Data Warehouse przy użyciu uwierzytelniania usługi Azure Active Directory).
- Zapytania obejmujące wiele baz danych, korzystające z nazw trój- i czteroczęściowych. (Zapytania tylko do odczytu obejmujące wiele baz danych są obsługiwane za pomocą [zapytania elastycznej bazy danych](sql-database-elastic-query-overview.md)).
- Tworzenie łańcucha własności między wieloma bazami danych, ustawienie `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Zamiast tego użyj instrukcji „EXECUTE AS USER”.
- Szyfrowanie jest obsługiwane z wyjątkiem rozszerzonego zarządzania kluczami
- Obsługa zdarzeń: Zdarzenia, powiadomienia o zdarzeniach, powiadomienia o zapytaniach
- Położenie pliku: Składnia dotycząca rozmieszczenia pliku bazy danych, rozmiaru i plików bazy danych, które są zarządzane automatycznie przez program Microsoft Azure.
- Wysoka dostępność: Składnia dotycząca wysokiej dostępności, która jest zarządzana przez konto Microsoft Azure. Obejmuje to składnię kopii zapasowych, przywracania, funkcji Always On, dublowania bazy danych, wysyłania dziennika oraz trybów odzyskiwania.
- Czytnik dziennika: Składnia, która opiera się na odczytywaniu dziennika, która nie jest dostępna w bazie danych SQL: Wypchnij replikacji, przechwytywania zmian danych. Baza danych SQL Database może być subskrybentem artykułu replikacji wypychanej.
- Funkcje: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Sprzęt: Składnia związane z ustawieniami związanych ze sprzętem serwera: takich jak pamięć, wątki robocze, koligacje Procesora, flagi śledzenia. Użyj warstwy usług i zamiast tego obliczenia rozmiarów.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`i czteroczęściowe nazwy
- .NET Framework: Integracja środowiska CLR z programem SQL Server
- Wyszukiwanie semantyczne
- Poświadczenia serwera: Użyj [zakresu poświadczeń bazy danych](https://msdn.microsoft.com/library/mt270260.aspx) zamiast tego.
- Elementy w poziomie serwera: Role serwera `sys.login_token`. Instrukcje `GRANT`, `REVOKE` i `DENY` w odniesieniu do uprawnień na poziomie serwera nie są dostępne, chociaż niektóre zostały zastąpione przez uprawnienia na poziomie bazy danych. Niektóre przydatne dynamiczne widoki zarządzania na poziomie serwera mają swoje odpowiedniki na poziomie bazy danych.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opcje polecenia `sp_configure` i instrukcja `RECONFIGURE`. Niektóre opcje są dostępne po użyciu instrukcji [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent programu SQL Server: Składnia, która opiera się na bazie danych MSDB lub agencie programu SQL Server: alerty, operatory, centralne serwery zarządzania. Zamiast tego używaj skryptów, takich jak Azure PowerShell.
- Inspekcja programu SQL Server: Zamiast tego użyj inspekcji bazy danych SQL Database.
- Śledzenie programu SQL Server
- Flagi śledzenia: Niektóre elementy flag śledzenia zostały przeniesione do trybów zgodności.
- Debugowanie języka Transact-SQL
- Wyzwalacze: O zakresie serwera lub Wyzwalacze logowania
- `USE` Instrukcja: Aby zmienić kontekst bazy danych do innej bazy danych, musi wprowadzić nowe połączenie z nową bazą danych.

## <a name="full-transact-sql-reference"></a>Pełna dokumentacja języka Transact-SQL

Aby uzyskać więcej informacji dotyczących gramatyki języka Transact-SQL oraz przykłady jego użycia, zobacz [odwołania Transact-SQL (aparat bazy danych)](https://msdn.microsoft.com/library/bb510741.aspx) w dokumentacji SQL Server — książki Online.

### <a name="about-the-applies-to-tags"></a>Tagi „Applies to” (Dotyczy)

Dokumentacja języka Transact-SQL zawiera artykuły dotyczące wersji programu SQL Server 2008 do chwili obecnej. Poniżej tytułu artykułu jest ikony paska, wyświetlanie listy cztery platformy programu SQL Server i wyświetlono. Na przykład grupy dostępności zostały wprowadzone w programie SQL Server 2012.  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) artykułu wskazuje, że instrukcja ma zastosowanie do programu  **SQL Server (począwszy od 2012)**. Instrukcja nie dotyczy programu SQL Server 2008, SQL Server 2008 R2, usługi Azure SQL Database, programu Azure SQL Data Warehouse ani Parallel Data Warehouse.

W niektórych przypadkach temat artykułu może służyć w produkcie, ale występują niewielkie różnice między produktami. Różnice są wskazane w punkty środkowe w artykule zgodnie z potrzebami. W niektórych przypadkach temat artykułu może służyć w produkcie, ale występują niewielkie różnice między produktami. Różnice są wskazane w punkty środkowe w artykule zgodnie z potrzebami. Na przykład w artykule CREATE TRIGGER jest dostępna w bazie danych SQL. Ale **wszystkie SERVER** opcję wyzwalaczy na poziomie serwera, wskazuje, że nie można używać wyzwalaczy poziomu serwera w bazie danych SQL. Zamiast tego Użyj wyzwalaczy na poziomie bazy danych.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać listę funkcji, które są obsługiwane i nieobsługiwane przez usługę SQL Database, zobacz [porównanie funkcji usługi Azure SQL Database](sql-database-features.md). Na liście na tej stronie uzupełnia artykuł z wytyczne i funkcje i koncentruje się na instrukcji języka Transact-SQL.
