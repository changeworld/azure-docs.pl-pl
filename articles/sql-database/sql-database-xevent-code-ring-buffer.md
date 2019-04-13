---
title: Bufor cykliczny systemu XEvent kodu dla usługi SQL Database | Dokumentacja firmy Microsoft
description: Zawiera przykładowy kod języka Transact-SQL, łatwe i szybkie utworzona przy użyciu obiektu docelowego bufor cykliczny w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: bb493fc0a9d3a9173ef4faf17b3cdd4e3781a557
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526167"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Kod docelowy buforu dla rozszerzonych zdarzeń w bazie danych SQL cyklicznego

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcesz kompletny przykład kodu dla szybkiego Najprostszym sposobem przechwytywania i przekazuje informacje dla zdarzeń rozszerzonych podczas testu. Najprostszym miejsce docelowe dla danych zdarzeń rozszerzonych jest [docelowej bufor cykliczny](https://msdn.microsoft.com/library/ff878182.aspx).

W tym temacie przedstawiono przykładowy kod języka Transact-SQL, który:

1. Tworzy tabelę z danymi, aby zademonstrować za pomocą.
2. To znaczy tworzy sesję dla istniejących zdarzeń rozszerzonych **sqlserver.sql_statement_starting**.
   
   * Zdarzenie jest ograniczona do instrukcji SQL, zawierające określony ciąg aktualizacji: **instrukcji takich jak "% aktualizacji tabEmployee %"**.
   * Wybiera wysyłać dane wyjściowe zdarzenia docelowego typu bufor cykliczny, a mianowicie **package0.ring_buffer**.
3. Uruchamia sesję zdarzeń.
4. Problemy z kilku prostych instrukcji SQL UPDATE.
5. Generuje instrukcję SQL SELECT, aby pobrać dane wyjściowe zdarzenia z bufor cykliczny.
   
   * **sys.dm_xe_database_session_targets** i innych dynamicznych widoków zarządzania (DMV) są łączone.
6. Zatrzymuje sesji zdarzeń.
7. Porzuca element docelowy bufor cykliczny, aby zwolnić jego zasoby.
8. Odrzuca sesji zdarzeń i tabeli pokaz.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Wszystkie bazy danych, które można utworzyć tabeli w.
  
  * Opcjonalnie możesz [tworzenie **AdventureWorksLT** demonstracyjna baza danych](sql-database-get-started.md) w ciągu kilku minut.
* SQL Server Management Studio (ssms.exe), najlepiej jej najnowszej miesięcznych aktualizacji wersji. 
  Możesz pobrać najnowszą ssms.exe od:
  
  * Temat zatytułowany [pobierania programu SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Bezpośredni link do pobierania.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Przykład kodu

Bardzo drobnych modyfikacji Poniższy przykładowy kod bufor cykliczny może działać w usłudze Azure SQL Database lub programu Microsoft SQL Server. Różnica polega na obecności węzła "_bazy danych" nazwę niektóre dynamicznych widoków zarządzania (DMV), używany w klauzuli FROM w kroku 5. Na przykład:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Zawartość buforu cyklicznego

Użyliśmy ssms.exe, aby uruchomić przykładowy kod.

Aby wyświetlić wyniki, firma Microsoft kliknięto komórce pod nagłówkiem kolumny **target_data_XML**.

Następnie w okienku wyników możemy kliknięto przycisk komórce pod nagłówkiem kolumny **target_data_XML**. Kliknij ten przycisk utworzyć inną kartę pliku w ssms.exe w którym zawartość komórki wynik był wyświetlany jako XML.

Dane wyjściowe są wyświetlane w poniższy blok. Wygląda na to długie, ale jest tylko dwóch  **\<zdarzeń >** elementów.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Zwolnij zasoby zajmowane przez Twoje bufor cykliczny

Po ukończeniu swojej bufor cykliczny, można ją usunąć i zwolnić jego zasoby, wydawania **ALTER** podobnie do następującego:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definicja sesji zdarzeń jest zaktualizowane, ale nie został porzucony. Później możesz dodać inne wystąpienie bufor cykliczny do sesji zdarzeń:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Więcej informacji

Jest podstawowym tematu dla rozszerzonych zdarzeń w usłudze Azure SQL Database:

* [Rozszerzony uwagi dotyczące zdarzeń, w usłudze SQL Database](sql-database-xevent-db-diff-from-svr.md), która różni się znacząco niektóre aspekty rozszerzonych zdarzeń, które różnią się od usługi Azure SQL Database a baza danych programu Microsoft SQL Server.

Inne tematy przykładowy kod dla rozszerzonych zdarzeń są dostępne poniższe linki. Jednak musi regularnie sprawdzać dowolnego przykładu, aby zobaczyć, czy plik jest przeznaczony dla programu Microsoft SQL Server i usługi Azure SQL Database. Następnie można zdecydować, czy drobne zmiany są potrzebne do uruchomienia przykładu.

* Przykład kodu służącego do usługi Azure SQL Database: [Kod docelowy pliku zdarzenia dla rozszerzonych zdarzeń w bazie danych SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
