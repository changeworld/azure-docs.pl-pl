---
title: Tabele tymczasowe
description: Podstawowe wskazówki dotyczące używania tabel tymczasowych w Azure SQL Data Warehouse, z uwzględnieniem zasad tabel tymczasowych na poziomie sesji.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 23a5825a32c602f70aff1d9f577ce13d3e9f2260
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685437"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabele tymczasowe w SQL Data Warehouse
Ten artykuł zawiera podstawowe wskazówki dotyczące używania tabel tymczasowych i wyróżniania zasad tabel tymczasowych na poziomie sesji. Korzystając z informacji podanych w tym artykule, można modularyzacji swój kod, ulepszając i ułatwiając konserwację kodu.

## <a name="what-are-temporary-tables"></a>Co to są tabele tymczasowe?
Tabele tymczasowe są przydatne podczas przetwarzania danych — zwłaszcza podczas przekształcania, gdzie pośrednie wyniki są przejściowe. W SQL Data Warehouse tabeli tymczasowej istnieją na poziomie sesji.  Są one widoczne tylko dla sesji, w której zostały utworzone i są automatycznie usuwane po wylogowaniu się tej sesji.  Tabele tymczasowe oferują korzyść wydajności, ponieważ ich wyniki są zapisywane w lokalnym, a nie w magazynie zdalnym.

## <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej
Tabele tymczasowe są tworzone przez utworzenie prefiksu nazwy tabeli z `#`.  Na przykład:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Tabele tymczasowe można również utworzyć za pomocą `CTAS` przy użyciu dokładnie tego samego podejścia:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS` jest zaawansowanym poleceniem i ma zaawansowaną zaletę użycia przestrzeni dzienników transakcji. 
> 
> 

## <a name="dropping-temporary-tables"></a>Usuwanie tabel tymczasowych
Po utworzeniu nowej sesji nie powinny istnieć tabele tymczasowe.  Jednakże w przypadku wywołania tej samej procedury składowanej, która tworzy tymczasową o tej samej nazwie, aby upewnić się, że instrukcje `CREATE TABLE` powiodło się po pomyślnym uruchomieniu prostej kontroli przed wystąpieniem `DROP` można użyć, jak w poniższym przykładzie:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Aby zapewnić spójność kodowania, dobrym sposobem jest użycie tego wzorca dla tabel i tabel tymczasowych.  Dobrym pomysłem jest również używanie `DROP TABLE` do usuwania tabel tymczasowych po zakończeniu pracy z nimi w kodzie.  W opracowywaniu procedury składowanej, często widzisz polecenia upuszczania powiązane razem na końcu procedury, aby upewnić się, że te obiekty zostały oczyszczone.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Kod Modularizing
Ponieważ tabele tymczasowe mogą być widoczne w dowolnym miejscu w sesji użytkownika, można wykorzystać ją do modularyzacji kodu aplikacji.  Na przykład następująca procedura składowana generuje kod DDL, aby zaktualizować wszystkie statystyki w bazie danych według nazwy statystycznej.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Na tym etapie jedyną akcją, która miała miejsce, jest utworzenie procedury składowanej, która generuje tabelę tymczasową #stats_ddl, przy użyciu instrukcji języka DDL.  Ta procedura składowana opuszcza #stats_ddl, jeśli już istnieje, aby upewnić się, że nie kończy się niepowodzeniem w przypadku uruchomienia więcej niż raz w ramach sesji.  Jednak ponieważ nie ma `DROP TABLE` na końcu procedury składowanej, po zakończeniu procedury składowanej opuści ona utworzoną tabelę, aby można ją było odczytać poza procedurą składowaną.  W SQL Data Warehouse, w przeciwieństwie do innych baz danych SQL Server, można użyć tabeli tymczasowej poza procedurą, która ją utworzyła.  SQL Data Warehouse tabel tymczasowych można używać w **dowolnym miejscu** w ramach sesji. Może to prowadzić do bardziej modularnego i możliwego do zarządzania kodu, jak w poniższym przykładzie:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Ograniczenia tabeli tymczasowej
SQL Data Warehouse powoduje nałożenie kilku ograniczeń podczas implementowania tabel tymczasowych.  Obecnie obsługiwane są tylko tabele tymczasowe w zakresie sesji.  Globalne tabele tymczasowe nie są obsługiwane.  Ponadto nie można tworzyć widoków w tabelach tymczasowych.  Tabele tymczasowe można tworzyć tylko za pomocą rozkładu mieszania lub działania okrężnego.  Replikowana tymczasowa dystrybucja tabel nie jest obsługiwana. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tworzenia tabel, zobacz [Omówienie tabeli](sql-data-warehouse-tables-overview.md).

