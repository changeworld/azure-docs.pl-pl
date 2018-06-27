---
title: Automatyzowanie replikacji zmian schematu w synchronizacji danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i automatyzowanie replikacji zmian schematu w synchronizacji danych SQL Azure.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a39e060708514fdca11a5d89858486b442a18309
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019591"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatyzowanie replikacji zmian schematu w synchronizacji danych SQL Azure

Synchronizacja danych SQL pozwala użytkownikom na synchronizowanie danych między bazami danych SQL Azure i lokalnego programu SQL Server w jednym kierunku lub w obu kierunkach. Jednym z bieżących ograniczeń synchronizacji danych SQL jest brak obsługi replikacji zmian schematu. Przy każdej zmianie schematu tabeli, należy zastosować zmiany wprowadzone ręcznie dla wszystkich punktów końcowych, w tym koncentratora i wszystkie elementy członkowskie, a następnie zaktualizuj schematu synchronizacji.

W tym artykule przedstawiono rozwiązanie automatycznie replikować zmiany schematu do wszystkich punktów końcowych synchronizacji danych SQL.
1. To rozwiązanie wymaga wyzwalacza DDL, aby śledzić zmiany schematu.
2. Wyzwalacz wstawia polecenia zmiany schematu tabeli śledzenia.
3. Śledzenie ta tabela jest synchronizowany do wszystkich punktów końcowych przy użyciu usługi synchronizacji danych.
4. Wyzwalacze DML po wstawieniu są używane do zastosowania zmian schematu w innych punktach końcowych.

W tym artykule używa instrukcji ALTER TABLE, na przykład zmiany schematu, ale to rozwiązanie działa także w przypadku innych typów zmian schematu.

> [!IMPORTANT]
> Zalecamy przeczytanie tego artykułu, szczególnie sekcje zawierają informacje o [Rozwiązywanie problemów](#troubleshooting) i [innych kwestii](#other), przed rozpoczęciem implementacji Replikacja zmian schematu automatycznych w środowiska synchronizacji. Zaleca się również przeczytanie [synchronizacji danych między wieloma bazami danych chmury i lokalnych z synchronizacji danych SQL](sql-database-sync-data.md). Niektóre operacje bazy danych mogą być dzielone rozwiązania opisane w tym artykule. Może być konieczna znajomość dodatkowe domeny programu SQL Server i języka Transact-SQL rozwiązać te problemy.

![Automatyzowanie replikacji zmian schematu](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurowanie replikacji zmian schematu automatycznych

### <a name="create-a-table-to-track-schema-changes"></a>Tworzenie tabeli w celu śledzenia zmian schematu

Utwórz tabelę do śledzenia zmian schematu w wszystkie bazy danych w grupie synchronizacji:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ta tabela ma kolumnę tożsamości, aby śledzić kolejność zmiany schematu. Można dodać pól do rejestrowania więcej informacji, jeśli to konieczne.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Tworzenie tabeli w celu śledzenia historii zmian schematu

Dla wszystkich punktów końcowych należy utworzyć tabelę do śledzenia identyfikator polecenia zmiany schematu zastosowane jako ostatnie.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Utworzyć wyzwalacza ALTER TABLE DDL w bazie danych, w którym zostały wprowadzone zmiany schematu

Utworzyć wyzwalacza DDL dla operacji ALTER TABLE. Należy utworzyć tego wyzwalacza w bazie danych, w którym zostały wprowadzone zmiany schematu. Aby uniknąć konfliktów, Zezwalaj tylko na zmiany schematu w jednej bazy danych w grupie synchronizacji.

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

Wyzwalacz wstawia rekordu w tabeli dla każdego polecenia ALTER TABLE śledzenia zmian schematu. W tym przykładzie dodaje filtr, aby uniknąć replikowanie zmian schematu w ramach schematu **DataSync**, ponieważ są to najczęściej przez usługę synchronizacji danych. Dodaj więcej filtrów, jeśli chcesz replikować określone typy zmian schematu.

Można również dodać więcej wyzwalaczy replikacji innych typów zmian schematu. Na przykład można utworzyć wyzwalaczy CREATE_PROCEDURE, ALTER_PROCEDURE i DROP_PROCEDURE do replikowania zmian do procedur składowanych.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Tworzenie wyzwalacza dla innych punktów końcowych w celu zastosowania zmian schematu podczas wstawiania

Wyzwalacz wykonuje polecenia zmiany schematu, gdy jest zsynchronizowany z innymi punktami końcowymi. Należy utworzyć tego wyzwalacza dla wszystkich punktów końcowych, z wyjątkiem tego, gdzie zostały wprowadzone zmiany schematu (oznacza to, w bazie danych, gdzie wyzwolenia DDL `AlterTableDDLTrigger` jest utworzony w poprzednim kroku).

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

Wyzwalacz uruchamia po wstawieniu i sprawdza, czy bieżące polecenie należy uruchamiać dalej. Logika kodu gwarantuje, że nie instrukcję zmiany schematu zostało pominięte, i wszystkie zmiany zostaną zastosowane, nawet jeśli wstawiania znajduje się poza kolejnością.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizowanie zmian schematu Tabela śledzenia do wszystkich punktów końcowych

Można zsynchronizować zmiany schematu Tabela śledzenia do wszystkich punktów końcowych przy użyciu istniejącej grupy synchronizacji lub nowej grupy synchronizacji. Upewnij się, że zmiany w tabeli śledzenia można synchronizować do wszystkich punktów końcowych, zwłaszcza w przypadku, gdy używasz synchronizacji w jednym kierunku.

Nie Synchronizuj tabeli historii zmian schematu, ponieważ ta tabela zachowuje stan różne na różnych punktów końcowych.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Zastosuj zmiany schematu w grupie synchronizacji

Tylko schemat zmian w bazie danych, gdzie jest tworzona wyzwalacza DDL są replikowane. Zmiany schematu w innych bazach danych nie są replikowane.

Po zmiany schematu są replikowane do wszystkich punktów końcowych, należy wykonać dodatkowe czynności, aby zaktualizować schemat synchronizacji, aby uruchomić lub zatrzymać synchronizację nowych kolumn.

#### <a name="add-new-columns"></a>Dodaj nowe kolumny.

1.  Należy zmienić schemat.

2.  Unikaj wszelkie zmiany danych, gdzie są zaangażowane nowe kolumny, dopóki nie przeprowadzisz krok, który tworzy wyzwalacz.

3.  Poczekaj, aż zmiany schematu są stosowane do wszystkich punktów końcowych.

4.  Odśwież schemat bazy danych, a następnie dodaj nową kolumnę do schematu synchronizacji.

5.  Dane w nowej kolumnie jest zsynchronizowany podczas następnej operacji synchronizacji.

#### <a name="remove-columns"></a>Usuwanie kolumn

1.  Usunąć kolumny z schematu synchronizacji. Synchronizacja danych zatrzymuje synchronizowania danych w tych kolumnach.

2.  Należy zmienić schemat.

3.  Odśwież schemat bazy danych.

#### <a name="update-data-types"></a>Aktualizowanie typów danych

1.  Należy zmienić schemat.

2.  Poczekaj, aż zmiany schematu są stosowane do wszystkich punktów końcowych.

3.  Odśwież schemat bazy danych.

4.  Jeśli w nowym i starym typy danych nie są całkowicie zgodnej — na przykład w przypadku zmiany z `int` do `bigint` -synchronizacji mogą zakończyć się niepowodzeniem przed ukończeniem kroków, które utworzyć wyzwalaczy. Synchronizacja zakończy się powodzeniem po ponowieniu próby.

#### <a name="rename-columns-or-tables"></a>Zmiana nazwy kolumn i tabel

Zmiana nazwy kolumn i tabel sprawia, że synchronizacja danych przestają działać. Tworzenie nowej tabeli lub kolumny, uzupełniania danych, a następnie usuń stare tabeli lub kolumny zamiast zmieniania nazwy.

#### <a name="other-types-of-schema-changes"></a>Inne zmiany schematu

Dla innych typów zmian schematu — na przykład tworzenie procedur składowanych lub porzuca indeks — Aktualizowanie schematu synchronizacji nie jest wymagane.

## <a name="troubleshoot"></a> Rozwiązywanie problemów z replikacją zmiany schematu automatycznych

Logika replikacji opisane w tym artykule przestaje działać w niektórych sytuacjach — na przykład, jeśli schemat zmian w bazie danych lokalnych, który nie jest obsługiwany w bazie danych SQL Azure. W takim przypadku synchronizowanie Tabela śledzenia zmian schematu nie powiedzie się. Należy ręcznie rozwiązać ten problem:

1.  Wyłącz wyzwalacza DDL i unikać wprowadzania dodatkowych zmian schematu, dopóki ten problem zostanie rozwiązany.

2.  W bazie danych punktu końcowego, gdy wykonywane jest problem należy wyłączyć wyzwalacza AFTER INSERT w punkcie końcowym, której nie można wprowadzić zmiany schematu. Dzięki temu można synchronizować polecenia zmiany schematu.

3.  Wyzwalanie synchronizacji do synchronizacji tabeli śledzenia zmian schematu.

4.  W bazie danych punktu końcowego, gdy wykonywane jest problem zapytanie schematu zmienić tabeli historii można uzyskać Identyfikatora ostatniego polecenia zmiany schematu zastosowane.

5.  Wyślij zapytanie do tabeli, aby wyświetlić listę wszystkich poleceń o identyfikatorze większa niż wartość Identyfikatora się, że pobierana w poprzednim kroku śledzenia zmian schematu.

    a.  Ignoruj tych poleceń, których nie można wykonać w bazie danych punktu końcowego. Należy uwzględniać niespójnością schematu. Jeśli aplikacja ma wpływ na niespójności, należy przywrócić pierwotne zmiany schematu.

    b.  Ręcznie zastosować te polecenia, które powinny być stosowane.

6.  Aktualizowanie tabeli historii zmian schematu i Ustaw identyfikator ostatniego zastosowanego do poprawnej wartości.

7.  Sprawdź, czy schemat jest aktualny.

8.  Włącz ponownie wyzwalacz AFTER INSERT wyłączone w drugim kroku.

9.  Włącz ponownie wyzwalacza DDL wyłączone w pierwszym kroku.

Jeśli chcesz wyczyścić rekordy w tabeli śledzenia zmian schematu, zamiast TRUNCATE DELETE. Nigdy nie ponowne umieszczanie kolumny tożsamości w śledzenia tabeli za pomocą polecenia DBCC CHECKIDENT zmiany schematu. Można utworzyć nowej zmiany schematu tabelę śledzenia i zaktualizuj nazwę tabeli w wyzwalacza DDL, jeśli bez dosiewów jest wymagana.

## <a name="other"></a> Inne zagadnienia

-   Bazy danych użytkowników, którzy skonfuguruj koncentratora i element członkowski musi być wystarczające uprawnienia do wykonania polecenia zmiany schematu.

-   Możesz dodać więcej filtrów w wyzwalacza DDL replikowanie tylko zmiany schematu w wybranych tabelach lub operacji.

-   Zmiany schematu można tworzyć tylko w bazie danych, gdzie jest tworzona wyzwalacza DDL.

-   W przypadku wprowadzania zmian w bazie danych programu SQL Server na lokalnym, upewnij się, że zmiany schematu jest obsługiwana w bazie danych SQL Azure.

-   Jeśli wprowadzono zmiany schematu w bazach danych innych niż bazy danych, w której utworzono wyzwalacza DDL, zmiany nie są replikowane. Aby uniknąć tego problemu, można utworzyć wyzwalaczy DDL, aby zablokować zmiany dla innych punktów końcowych.

-   Jeśli musisz zmienić schemat tabeli śledzenia zmian schematu, wyłączyć wyzwalacza DDL, aby wprowadzić zmiany, a następnie ręcznie zastosować zmiany do wszystkich punktów końcowych. Aktualizowanie schematu w wyzwalacz AFTER INSERT w tej samej tabeli nie działa.

-   Nie ponowne umieszczanie kolumny tożsamości za pomocą polecenie DBCC CHECKIDENT.

-   Nie używaj TRUNCATE, aby wyczyścić dane w tabeli śledzenia zmian schematu.
