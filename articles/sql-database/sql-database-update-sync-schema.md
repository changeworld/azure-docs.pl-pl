---
title: Automatyzacja replikacji zmian schematu w synchronizacji danych SQL
description: Dowiedz się, jak zautomatyzować replikację zmian schematu w usłudze Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822431"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatyzacja replikacji zmian schematu w usłudze Azure SQL Data Sync

Sql Data Sync umożliwia użytkownikom synchronizowanie danych między bazami danych SQL platformy Azure i lokalnym programem SQL Server w jednym kierunku lub w obu kierunkach. Jednym z bieżących ograniczeń synchronizacji danych SQL jest brak obsługi replikacji zmian schematu. Za każdym razem, gdy zmieniasz schemat tabeli, należy ręcznie zastosować zmiany we wszystkich punktach końcowych, w tym w centrum i wszystkich członkach, a następnie zaktualizować schemat synchronizacji.

W tym artykule przedstawiono rozwiązanie do automatycznego replikowania zmian schematu do wszystkich punktów końcowych synchronizacji danych SQL.
1. To rozwiązanie używa wyzwalacza DDL do śledzenia zmian schematu.
1. Wyzwalacz wstawia polecenia zmiany schematu w tabeli śledzenia.
1. Ta tabela śledzenia jest synchronizowana ze wszystkimi punktami końcowymi przy użyciu usługi synchronizacja danych.
1. Wyzwalacze DML po wstawieniu są używane do stosowania zmian schematu w innych punktach końcowych.

W tym artykule użyto ALTER TABLE jako przykład zmiany schematu, ale to rozwiązanie działa również dla innych typów zmian schematu.

> [!IMPORTANT]
> Zaleca się, aby przeczytać ten artykuł uważnie, zwłaszcza sekcje dotyczące [rozwiązywania problemów](#troubleshoot) i [inne zagadnienia,](#other)przed rozpoczęciem wdrażania automatycznej replikacji zmiany schematu w środowisku synchronizacji. Zaleca się również [odczytanie danych synchronizacji w wielu chmurach i lokalnych bazach danych za pomocą programu SQL Data Sync.](sql-database-sync-data.md) Niektóre operacje bazy danych może przerwać rozwiązanie opisane w tym artykule. Do rozwiązania tych problemów może być wymagana dodatkowa znajomość domeny programu SQL Server i Transact-SQL.

![Automatyzacja replikacji zmian schematu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurowanie automatycznej replikacji zmiany schematu

### <a name="create-a-table-to-track-schema-changes"></a>Tworzenie tabeli do śledzenia zmian schematu

Utwórz tabelę do śledzenia zmian schematu we wszystkich bazach danych w grupie synchronizacji:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ta tabela zawiera kolumnę tożsamości do śledzenia kolejności zmian schematu. W razie potrzeby można dodać więcej pól, aby rejestrować więcej informacji.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Tworzenie tabeli do śledzenia historii zmian schematu

We wszystkich punktach końcowych utwórz tabelę do śledzenia identyfikatora ostatnio zastosowanego polecenia zmiany schematu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Tworzenie wyzwalacza DDL tabeli ALTER w bazie danych, w której wprowadzane są zmiany schematu

Utwórz wyzwalacz DDL dla operacji ALTER TABLE. Wystarczy utworzyć ten wyzwalacz w bazie danych, w której są wprowadzane zmiany schematu. Aby uniknąć konfliktów, zezwalaj tylko na zmiany schematu w jednej bazie danych w grupie synchronizacji.

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

Wyzwalacz wstawia rekord do tabeli śledzenia zmian schematu dla każdego polecenia ALTER TABLE. W tym przykładzie dodaje filtr, aby uniknąć replikowania zmian schematu wprowadzonych w ramach programu **DataSync**, ponieważ są one najprawdopodobniej wprowadzone przez usługę synchronizacji danych. Dodaj więcej filtrów, jeśli chcesz replikować tylko niektóre typy zmian schematu.

Można również dodać więcej wyzwalaczy do replikowania innych typów zmian schematu. Na przykład utwórz CREATE_PROCEDURE, ALTER_PROCEDURE i DROP_PROCEDURE wyzwalacze w celu replikacji zmian w procedurach przechowywanych.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Tworzenie wyzwalacza w innych punktach końcowych w celu zastosowania zmian schematu podczas wstawiania

Ten wyzwalacz wykonuje polecenie zmiany schematu, gdy jest synchronizowane z innymi punktami końcowymi. Należy utworzyć ten wyzwalacz na wszystkich punktach końcowych, z wyjątkiem tego, w którym są wprowadzane `AlterTableDDLTrigger` zmiany schematu (czyli w bazie danych, w której wyzwalacz DDL jest tworzony w poprzednim kroku).

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

Ten wyzwalacz jest uruchamiany po wstawieniu i sprawdza, czy bieżące polecenie powinno zostać uruchomione dalej. Logika kodu zapewnia, że instrukcja zmiany schematu nie jest pomijana, a wszystkie zmiany są stosowane, nawet jeśli wstawianie jest nie w porządku.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizowanie tabeli śledzenia zmian schematu ze wszystkimi punktami końcowymi

Tabelę śledzenia zmian schematu można zsynchronizować ze wszystkimi punktami końcowymi przy użyciu istniejącej grupy synchronizacji lub nowej grupy synchronizacji. Upewnij się, że zmiany w tabeli śledzenia mogą być synchronizowane ze wszystkimi punktami końcowymi, zwłaszcza gdy używasz synchronizacji jednokierunkowej.

Nie synchronizuj tabeli historii zmian schematu, ponieważ ta tabela zachowuje inny stan w różnych punktach końcowych.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Stosowanie zmian schematu w grupie synchronizacji

Replikowane są tylko zmiany schematu wprowadzone w bazie danych, w której tworzony jest wyzwalacz DDL. Zmiany schematu wprowadzone w innych bazach danych nie są replikowane.

Po zmianie schematu są replikowane do wszystkich punktów końcowych, należy również podjąć dodatkowe kroki, aby zaktualizować schemat synchronizacji, aby rozpocząć lub zatrzymać synchronizację nowych kolumn.

#### <a name="add-new-columns"></a>Dodawanie nowych kolumn

1.  Zmień schemat.

1.  Unikaj wszelkich zmian danych, w których nowe kolumny są zaangażowane, dopóki nie zostanie ukończony krok, który tworzy wyzwalacz.

1.  Poczekaj, aż zmiany schematu zostaną zastosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych i dodaj nową kolumnę do schematu synchronizacji.

1.  Dane w nowej kolumnie są synchronizowane podczas następnej operacji synchronizacji.

#### <a name="remove-columns"></a>Usuwanie kolumn

1.  Usuń kolumny ze schematu synchronizacji. Synchronizacja danych zatrzymuje synchronizację danych w tych kolumnach.

1.  Zmień schemat.

1.  Odśwież schemat bazy danych.

#### <a name="update-data-types"></a>Aktualizowanie typów danych

1.  Zmień schemat.

1.  Poczekaj, aż zmiany schematu zostaną zastosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych.

1.  Jeśli nowe i stare typy danych nie są w `int` pełni `bigint` zgodne — na przykład, jeśli zmienisz z do - synchronizacja może zakończyć się niepowodzeniem przed zakończeniem kroków, które tworzą wyzwalacze. Synchronizacja powiedzie się po ponowieniu próby.

#### <a name="rename-columns-or-tables"></a>Zmienianie nazwy kolumn lub tabel

Zmiana nazwy kolumn lub tabel powoduje, że synchronizacja danych przestaje działać. Utwórz nową tabelę lub kolumnę, wypełnij dane, a następnie usuń starą tabelę lub kolumnę zamiast zmiany nazwy.

#### <a name="other-types-of-schema-changes"></a>Inne rodzaje zmian schematu

W przypadku innych typów zmian schematu — na przykład tworzenie procedur przechowywanych lub upuszczanie indeksu — aktualizowanie schematu synchronizacji nie jest wymagane.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Rozwiązywanie problemów z automatyczną replikacją zmian schematu

Logika replikacji opisana w tym artykule przestaje działać w niektórych sytuacjach — na przykład, jeśli w lokalnej bazie danych została wyeksblakowana, która nie jest obsługiwana w usłudze Azure SQL Database. W takim przypadku synchronizacja tabeli śledzenia zmian schematu kończy się niepowodzeniem. Musisz rozwiązać ten problem ręcznie:

1.  Wyłącz wyzwalacz DDL i unikaj dalszych zmian schematu, dopóki problem nie zostanie rozwiązany.

1.  W bazie danych punktu końcowego, w której występuje problem, należy wyłączyć wyzwalacz AFTER INSERT w punkcie końcowym, w którym nie można dokonać zmiany schematu. Ta akcja umożliwia synchronizację polecenia zmiany schematu.

1.  Synchronizacja wyzwalacza w celu zsynchronizowania tabeli śledzenia zmian schematu.

1.  W bazie danych punktu końcowego, w której występuje problem, kwerendy tabeli historii zmiany schematu, aby uzyskać identyfikator ostatniego zastosowanego polecenia zmiany schematu.

1.  Kwerenda tabeli śledzenia zmiany schematu, aby wyświetlić listę wszystkich poleceń o identyfikatorze większym niż wartość identyfikatora pobrana w poprzednim kroku.

    a.  Ignoruj te polecenia, których nie można wykonać w bazie danych punktów końcowych. Należy radzić sobie z niespójności schematu. Przywróć zmiany oryginalnego schematu, jeśli niespójność wpływa na aplikację.

    b.  Ręcznie zastosuj te polecenia, które powinny być stosowane.

1.  Zaktualizuj tabelę historii zmian schematu i ustaw ostatnio zastosowany identyfikator na poprawną wartość.

1.  Sprawdź, czy schemat jest aktualny.

1.  Ponownie włącz spust AFTER INSERT wyłączony w drugim kroku.

1.  Ponownie włącz wyzwalacz DDL wyłączony w pierwszym kroku.

Jeśli chcesz wyczyścić rekordy w tabeli śledzenia zmian schematu, użyj polecenia USUŃ zamiast OBCINĄCZYĆ. Nigdy nie reseed kolumny tożsamości w tabeli śledzenia zmiany schematu przy użyciu DBCC CHECKIDENT. Można utworzyć nowe tabele śledzenia zmian schematu i zaktualizować nazwę tabeli w wyzwalaczu DDL, jeśli wymagane jest ponowne siedlenie.

## <a name="other-considerations"></a><a name="other"></a>Inne względy

-   Użytkownicy bazy danych, którzy konfigurują bazy danych centrum i członków, muszą mieć wystarczające uprawnienia do wykonywania poleceń zmiany schematu.

-   Można dodać więcej filtrów w wyzwalaczu DDL, aby tylko replikować zmiany schematu w wybranych tabelach lub operacjach.

-   Zmiany schematu można wprowadzać tylko w bazie danych, w której tworzony jest wyzwalacz DDL.

-   Jeśli wprowadzasz zmiany w lokalnej bazie danych programu SQL Server, upewnij się, że zmiana schematu jest obsługiwana w bazie danych SQL Azure.

-   Jeśli zmiany schematu są wprowadzane w bazach danych innych niż baza danych, w której tworzony jest wyzwalacz DDL, zmiany nie są replikowane. Aby uniknąć tego problemu, można utworzyć wyzwalacze DDL, aby zablokować zmiany w innych punktach końcowych.

-   Jeśli chcesz zmienić schemat tabeli śledzenia zmian schematu, wyłącz wyzwalacz DDL przed dokonaniem zmiany, a następnie ręcznie zastosuj zmianę do wszystkich punktów końcowych. Aktualizowanie schematu w wyzwalaczu AFTER INSERT w tej samej tabeli nie działa.

-   Nie reseed kolumny tożsamości przy użyciu DBCC CHECKIDENT.

-   Nie używaj TRUNCATE do czyszczenia danych w tabeli śledzenia zmian schematu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor — [monitorowanie synchronizacji danych SQL za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)
