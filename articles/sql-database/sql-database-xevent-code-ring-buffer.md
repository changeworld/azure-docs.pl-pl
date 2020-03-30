---
title: Kod bufora pierścieniowego XEvent
description: Zawiera przykładowy kod Transact-SQL, który jest łatwy i szybki za pomocą docelowego buforu pierścieniowego w bazie danych SQL azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: ad98b61d6339388551af93671b3d4d892942f4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213967"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Kod docelowy buforu pierścieniowego dla zdarzeń rozszerzonych w bazie danych SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcesz kompletny przykład kodu dla najprostszego szybkiego sposobu przechwytywania i raportowania informacji dla rozszerzonego zdarzenia podczas testu. Najłatwiejszym celem dla rozszerzonych danych zdarzeń jest [cel buforu pierścieniowego](https://msdn.microsoft.com/library/ff878182.aspx).

W tym temacie przedstawiono przykładowy kod Transact-SQL, który:

1. Tworzy tabelę z danymi do wykazania.
2. Tworzy sesję dla istniejącego zdarzenia rozszerzonego, a mianowicie **sqlserver.sql_statement_starting**.
   
   * Zdarzenie jest ograniczone do instrukcji SQL, które zawierają określony ciąg aktualizacji: **instrukcja LIKE '%UPDATE tabEmaployee%'**.
   * Wybiera, aby wysłać dane wyjściowe zdarzenia do obiektu docelowego typu Bufor pierścieniowy, a mianowicie **package0.ring_buffer**.
3. Rozpoczyna sesję wydarzenia.
4. Wydaje kilka prostych instrukcji SQL UPDATE.
5. Wystawia instrukcję SQL SELECT w celu pobrania danych wyjściowych zdarzeń z buforu pierścieniowego.
   
   * **sys.dm_xe_database_session_targets** i inne dynamiczne widoki zarządzania (DMV) są przyłączane.
6. Zatrzymuje sesję wydarzenia.
7. Porzuca docelowy bufor pierścieniowy, aby zwolnić jego zasoby.
8. Porzuca sesję wydarzenia i tabelę demonstracyjną.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Dowolna baza danych, w której można utworzyć tabelę.
  
  * Opcjonalnie można [utworzyć **adventureworkslt** demonstracji bazy danych](sql-database-get-started.md) w ciągu kilku minut.
* SQL Server Management Studio (ssms.exe), najlepiej jego najnowsza miesięczna wersja aktualizacji. 
  Możesz pobrać najnowsze ssms.exe z:
  
  * Temat zatytułowany [Pobierz program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Bezpośredni link do pobrania.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Przykład kodu

Przy bardzo nieznaczne modyfikacje następujące przykładowy kod buforu pierścieniowego można uruchomić na bazie danych SQL Azure lub microsoft SQL Server. Różnica polega na obecności węzła "_database" w nazwie niektórych dynamicznych widoków zarządzania (DMV), używanych w klauzuli FROM w kroku 5. Przykład:

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

## <a name="ring-buffer-contents"></a>Zawartość buforu pierścieniowego

Użyliśmy ssms.exe do uruchomienia przykładu kodu.

Aby wyświetlić wyniki, kliknięliśmy komórkę pod nagłówkiem kolumny **target_data_XML**.

Następnie w okienku wyników kliknięliśmy komórkę pod nagłówkiem kolumny **target_data_XML**. To kliknięcie utworzyło inną kartę pliku w pliku ssms.exe, w której wyświetlana była zawartość komórki wynikowej jako kod XML.

Dane wyjściowe są wyświetlane w następującym bloku. Wygląda długo, ale to ** \<** tylko dwa zdarzenia>elementami.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Zwolnij zasoby znajdujące się w buforze pierścieniowym

Po zakończeniu pracy z buforem pierścieniowym można go usunąć i zwolnić jego zasoby wydające **ALTER,** jak poniżej:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definicja sesji zdarzenia jest aktualizowana, ale nie porzucona. Później można dodać kolejne wystąpienie buforu pierścieniowego do sesji wydarzenia:

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

Podstawowym tematem zdarzeń rozszerzonych w usłudze Azure SQL Database jest:

* [Rozszerzone zagadnienia zdarzeń w bazie danych SQL](sql-database-xevent-db-diff-from-svr.md), która kontrastuje niektóre aspekty zdarzeń rozszerzonych, które różnią się między usługą Azure SQL Database a programem Microsoft SQL Server.

Inne przykładowe tematy kodu dla zdarzeń rozszerzonych są dostępne pod następującymi łączami. Jednak należy rutynowo sprawdzić dowolny przykład, aby zobaczyć, czy przykładowe obiekty docelowe Microsoft SQL Server w porównaniu do bazy danych SQL Azure. Następnie można zdecydować, czy do uruchomienia próbki są potrzebne niewielkie zmiany.

* Przykładowy kod dla bazy danych SQL usługi Azure: [kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w bazie danych SQL](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
