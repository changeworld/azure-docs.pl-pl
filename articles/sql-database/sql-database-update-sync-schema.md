---
title: Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 11/14/2018
ms.openlocfilehash: 712ccfa71c85629111428a4e0c7acaea050942b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331358"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync

SQL Data Sync pozwala użytkownikom na synchronizowanie danych między bazami danych Azure SQL i dla lokalnego programu SQL Server w jednym kierunku lub w obu kierunkach. Jednym z bieżących ograniczeń SQL Data Sync jest brak obsługi replikacji zmian schematu. Za każdym razem, gdy zmienił się schemat tabeli, należy zastosować zmiany ręcznie dla wszystkich punktów końcowych, w tym Centrum i wszystkie elementy członkowskie, a następnie zaktualizować schematu synchronizacji.

W tym artykule przedstawiono rozwiązanie automatycznie replikować zmiany schematu do wszystkich punktów końcowych SQL Data Sync.
1. To rozwiązanie używa wyzwalacz DDL do śledzenia zmian schematu.
1. Wyzwalacz wstawia polecenia zmiany schematu tabeli śledzenia.
1. Ta tabela śledzenia jest synchronizowana z usługą wszystkich punktów końcowych przy użyciu usługi Data Sync.
1. Wyzwalacze DML po wstawieniu są używane, aby zastosować zmiany schematu w innych punktach końcowych.

W tym artykule używa instrukcji ALTER TABLE, na przykład zmiany schematu, ale to rozwiązanie działa także dla innych typów zmiany schematu.

> [!IMPORTANT]
> Firma Microsoft zaleca, przeczytaj ten artykuł dokładnie, szczególnie sekcje dotyczące [Rozwiązywanie problemów](#troubleshoot) i [inne zagadnienia](#other), przed rozpoczęciem implementacji Replikacja zmian schematu automatycznych w Środowisko usługi synchronizacji. Zaleca się również przeczytanie [synchronizowanie danych w wielu bazach danych w chmurze i lokalnych z usługą SQL Data Sync](sql-database-sync-data.md). Niektóre operacje bazy danych może spowodować uszkodzenie rozwiązania opisane w tym artykule. Znajomość dodatkowe domeny programu SQL Server i języka Transact-SQL może być konieczne rozwiązywania tych problemów.

![Automatyzowanie replikacji zmian schematu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurowanie replikacji zmian schematu automatycznych

### <a name="create-a-table-to-track-schema-changes"></a>Utwórz tabelę do śledzenia zmian schematu

Utwórz tabelę do śledzenia zmian schematu we wszystkich bazach danych w grupie synchronizacji:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ta tabela zawiera kolumnę tożsamości, aby śledzić kolejność zmiany schematu. Możesz dodać więcej pól logowania więcej informacji, jeśli to konieczne.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Tworzenie tabeli, aby śledzić historię zmian schematu

Dla wszystkich punktów końcowych Tworzenie tabeli, aby śledzić identyfikator polecenia zastosowane jako ostatnie zmiany schematu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Tworzenie wyzwalacza ALTER DDL tabeli w bazie danych, w którym zostały wprowadzone zmiany schematu

Utwórz wyzwalacz DDL operacji ALTER TABLE. Musisz utworzyć ten wyzwalacz w bazie danych, w którym zostały wprowadzone zmiany schematu. Aby uniknąć konfliktów, Zezwalaj tylko na zmiany schematu w jednej bazy danych w grupie synchronizacji.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Wyzwalacz wstawia rekord w tabeli dla każdego polecenia ALTER TABLE śledzenia zmian schematu. Ten przykład dodaje filtr, aby uniknąć replikowania zmian schematu wprowadzonych w ramach schematu **DataSync**, ponieważ są to najczęściej wykonywane przez usługę synchronizacji danych. Dodaj więcej filtrów, jeśli chcesz replikować niektórych rodzajów zmian schematu.

Można również dodać więcej wyzwalaczy, aby replikować innych typów zmiany schematu. Na przykład utworzyć CREATE_PROCEDURE, ALTER_PROCEDURE i DROP_PROCEDURE wyzwalaczy, aby replikować zmiany procedur składowanych.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Tworzenie wyzwalacza w innych punktach końcowych, aby zastosować zmiany schematu podczas wstawiania

Ten wyzwalacz wykonuje polecenia zmiany schematu, gdy jest zsynchronizowany z innymi punktami końcowymi. Musisz utworzyć ten wyzwalacz dla wszystkich punktów końcowych, z wyjątkiem tego, gdzie zostały wprowadzone zmiany schematu (czyli w bazie danych, w którym wyzwalacza DDL `AlterTableDDLTrigger` jest tworzony w poprzednim kroku).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Ten wyzwalacz jest uruchamiany po wstawiania i sprawdza, czy bieżące polecenie powinno być uruchomione dalej. Logika kodu zapewnia oświadczenia zmiana schematu zostanie pominięta, a wszystkie zmiany zostaną zastosowane, nawet jeśli wstawiania znajduje się poza kolejnością.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizuj zmiany schematu, tabela śledzenia do wszystkich punktów końcowych

Można synchronizować zmiany schematu, tabela śledzenia do wszystkich punktów końcowych przy użyciu istniejącej grupie synchronizacji lub Nowa grupa synchronizacji. Upewnij się, że zmiany w tabeli śledzenia mogą być synchronizowane do wszystkich punktów końcowych, zwłaszcza w przypadku, gdy używasz synchronizacji w jednym kierunku rozmieszczania zawartości śródwierszowej.

Nie są synchronizowane tabeli historii zmian schematu, ponieważ inny stan w różnych punktach końcowych przechowuje tej tabeli.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Zastosuj zmiany schematu w grupie synchronizacji

Tylko zmian schematu wprowadzonych w bazie danych, w której tworzona jest wyzwalacz DDL są replikowane. Zmian schematu wprowadzonych w innych bazach danych nie są replikowane.

Po zmiany schematu są replikowane do wszystkich punktów końcowych, należy wykonać dodatkowe czynności, aby zaktualizować schematu synchronizacji, aby rozpocząć lub zatrzymać synchronizację nowych kolumn.

#### <a name="add-new-columns"></a>Dodawanie nowych kolumn

1.  Wprowadź schemat zmiany.

1.  Należy unikać wszelkich zmian danych, gdy nowe kolumny są zaangażowane aż wykonaniu tego kroku, który tworzy wyzwalacz.

1.  Poczekaj, aż zmiany schematu są stosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych i dodać nową kolumnę do schematu synchronizacji.

1.  Dane w nowej kolumnie jest zsynchronizowany podczas następnej operacji synchronizacji.

#### <a name="remove-columns"></a>Usuwanie kolumn

1.  Usuń kolumny ze schematu synchronizacji. Synchronizacja danych zatrzymuje, synchronizowanie danych w tych kolumnach.

1.  Wprowadź schemat zmiany.

1.  Odśwież schemat bazy danych.

#### <a name="update-data-types"></a>Aktualizowanie typów danych

1.  Wprowadź schemat zmiany.

1.  Poczekaj, aż zmiany schematu są stosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych.

1.  Jeśli typy danych w nowym i starym nie są w pełni zgodne — na przykład w przypadku zmiany z `int` do `bigint` -synchronizacji mogą zakończyć się niepowodzeniem przed ukończeniem kroków, które utworzyć wyzwalacze. Synchronizacja zakończy się pomyślnie po ponowieniu próby.

#### <a name="rename-columns-or-tables"></a>Zmiana nazwy kolumn lub tabel

Zmienianie nazw kolumn lub tabel sprawia, że synchronizacja danych przestają działać. Utwórz nową tabelę lub kolumnę, wypełniania danymi, a następnie usuń starą tabelę lub kolumnę, zamiast zmieniania nazwy.

#### <a name="other-types-of-schema-changes"></a>Inne rodzaje zmian schematu

Dla innych typów zmiany schematu — na przykład tworzenie procedur składowanych lub porzuca indeks — Aktualizowanie schematu synchronizacji nie jest wymagane.

## <a name="troubleshoot"></a> Rozwiązywanie problemów z replikacją zmian schematu automatycznych

Logika replikacji, opisano w tym artykule przestaje działać w niektórych sytuacjach — na przykład, jeśli wprowadzono zmiany w lokalnej bazie danych, który nie jest obsługiwany w usłudze Azure SQL Database schematu. W takim przypadku Tabela śledzenia zmian schematu synchronizacji kończy się niepowodzeniem. Należy ręcznie rozwiązać ten problem:

1.  Wyłącz wyzwalacz DDL i uniknąć wprowadzania dodatkowych zmian schematu, dopóki ten problem zostanie rozwiązany.

1.  W bazie danych punktu końcowego, którym dzieje się ten problem należy wyłączyć wyzwalacza po Wstaw w punkcie końcowym, której nie można wprowadzić zmiany schematu. Ta akcja umożliwia polecenia zmiany schematu, można synchronizować.

1.  Wyzwalanie synchronizacji w celu synchronizacji tabeli śledzenia zmian schematu.

1.  W bazie danych punktu końcowego, gdzie problem się dzieje zapytania schematu zmienić tabeli historii, aby uzyskać identyfikator ostatniego polecenia zmiany zastosowane schematu.

1.  Wyślij zapytanie do tabeli, aby wyświetlić listę wszystkich poleceń o identyfikatorze większa niż wartość Identyfikatora, pobrać w poprzednim kroku śledzenia zmian schematu.

    a.  Ignoruj tych poleceń, których nie można wykonać w bazie danych punktu końcowego. Potrzebujesz poradzić sobie z niespójnością schematu. W razie niespójności ma wpływ na aplikację, należy przywrócić pierwotne zmiany schematu.

    b.  Ręcznie zastosować tych poleceń, które mają być stosowane.

1.  Zaktualizuj tabeli historii zmian schematu, a następnie ustaw identyfikator ostatniego zastosowanych do poprawnej wartości.

1.  Dokładnie sprawdź, czy schemat jest aktualny.

1.  Włącz ponownie wyzwalacz po Wstaw wyłączone w drugim kroku.

1.  Włącz ponownie wyzwalacz DDL wyłączone w pierwszym kroku.

Aby wyczyścić rekordy w tabeli śledzenia zmian schematu, należy użyć DELETE zamiast TRUNCATE. Nigdy nie reseed kolumny tożsamości w Tabela śledzenia za pomocą polecenie DBCC CHECKIDENT zmian schematu. Można utworzyć nowej zmiany schematu tabele śledzenia i zaktualizuj nazwę tabeli w wyzwalaczu DDL, jeśli bez dosiewów jest wymagana.

## <a name="other"></a> Inne zagadnienia

-   Użytkowników bazy danych, którzy konfigurują Centrum i elementów członkowskich baz danych muszą mieć wystarczające uprawnienia do wykonywania poleceń zmiany schematu.

-   Możesz dodać więcej filtrów w wyzwalaczu DDL można replikować tylko zmiany schematu w wybranych tabel lub operacji.

-   Zmiany schematu można tworzyć tylko w bazie danych, w której tworzona jest wyzwalacz DDL.

-   Jeśli zmiany wprowadzane w bazie danych programu SQL Server w środowisku lokalnym, upewnij się, że zmiana schematu jest obsługiwana w usłudze Azure SQL Database.

-   Jeśli w bazach danych innej niż baza danych, w którym utworzono wyzwalacz DDL zostaną wprowadzone zmiany schematu, zmiany nie są replikowane. Aby uniknąć tego problemu, można utworzyć wyzwalaczy DDL blokowanie zmian na inne punkty końcowe.

-   Jeśli musisz zmienić schemat tabeli śledzenia zmian schematu, wyłączyć wyzwalacza DDL, aby wprowadzić zmiany, a następnie ręcznie Zastosuj zmiany do wszystkich punktów końcowych. Aktualizowanie schematu w wyzwalaczu po Wstaw w tej samej tabeli nie działa.

-   Nie reseed kolumny tożsamości za pomocą polecenie DBCC CHECKIDENT.

-   Nie używaj TRUNCATE czyszczenia danych w tabeli śledzenia zmian schematu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [dzienniki monitora SQL Data Sync za pomocą usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)
