---
title: Tabele tymczasowe w usłudze SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Podstawowe wskazówki dotyczące korzystania z tabel tymczasowych i wyróżnienia zasadami poziomu tabel tymczasowych sesji.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 32830039c62f7ff68137e704b2562269fd4ad2c7
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466118"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabele tymczasowe w usłudze SQL Data Warehouse
Ten artykuł zawiera podstawowe wskazówki dotyczące korzystania z tabel tymczasowych i wyróżnienie zasadami poziomu tabel tymczasowych sesji. Korzystając z informacji w tym artykule mogą pomóc w modularyzacji kodu, zwiększanie prostotę konserwacji kodu i możliwość ponownego wykorzystania.

## <a name="what-are-temporary-tables"></a>Co to są tabele tymczasowe?
Tabele tymczasowe są przydatne podczas przetwarzania danych — szczególnie podczas przekształcania, których wyniki pośrednie są przejściowy. W usłudze SQL Data Warehouse tabele tymczasowe istnieją na poziomie sesji.  One są widoczne tylko dla sesji, w którym zostały utworzone i są automatycznie usuwane po wylogowaniu tej sesji.  Tabele tymczasowe oferują korzyści wydajności, ponieważ ich wyniki są zapisywane w lokalnym, a nie Magazyn zdalny.

## <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej
Tabele tymczasowe są tworzone przez poprzedzania ich nazwą tabeli za pomocą `#`.  Na przykład:

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

Tabele tymczasowe można również utworzyć przy użyciu `CTAS` przy użyciu tej samej metody:

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
> `CTAS` to polecenie Zaawansowane i ma tę zaletę jest efektywne przy jego użyciu miejsca w dzienniku transakcji. 
> 
> 

## <a name="dropping-temporary-tables"></a>Usuwanie tabel tymczasowych
Po utworzeniu nowej sesji powinna istnieć nie tabel tymczasowych.  Jednak jeśli wywołujesz tej samej procedury składowanej, który tworzy tymczasową o takiej samej nazwie, aby upewnić się, że Twoje `CREATE TABLE` instrukcje zostały wykonane pomyślnie prostą kontrolę istnienie wstępnego za pomocą `DROP` może służyć jak w poniższym przykładzie:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Do kodowania spójności, jest dobrym rozwiązaniem, należy używać tego wzorca dla tabel i tabelach tymczasowych.  Warto również dobry pomysł, aby użyć `DROP TABLE` do usunięcia tabel tymczasowych po zakończeniu z nimi w kodzie.  Podczas tworzenia procedury składowanej jest częściej można zobaczyć, że poleceń drop ze sobą powiązane na końcu procedury, aby upewnić się, że te obiekty są czyszczone.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kodu
Ponieważ tabele tymczasowe są widoczne dowolne miejsce w sesji użytkownika, może zostać wykorzystana ułatwiające modularyzacji kodu aplikacji.  Na przykład następującą procedurę składowaną generuje DDL, aby zaktualizować wszystkie statystyki w bazie danych o nazwie Statystyka.

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

Na tym etapie jedyną akcją, która jest przeprowadzana jest tworzenie procedury składowanej, która generuje tabelę tymczasową stats_ddl #, za pomocą instrukcji DDL.  Tę procedurę składowaną porzuca #stats_ddl, jeśli istnieje już upewnij się, że nie niepowodzeniem, jeśli więcej niż jeden raz w ramach sesji.  Jednakże ponieważ ma nie `DROP TABLE` na końcu procedury składowanej, po ukończeniu działania procedury składowanej, instalacja pozostawia utworzonej tabeli, aby mogły zostać odczytane poza procedury składowanej.  W usłudze SQL Data Warehouse w przeciwieństwie do innych baz danych programu SQL Server, jest możliwość użycia tabeli tymczasowej poza procedury, której został utworzony.  Tabele tymczasowe SQL Data Warehouse może służyć **dowolnym** wewnątrz sesji. Może to prowadzić do więcej kodu modułowego i łatwe w zarządzaniu, jak w poniższym przykładzie:

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
Usługa SQL Data Warehouse nakłada kilka ograniczeń podczas implementowania tabel tymczasowych.  Obecnie tylko sesji tabele tymczasowe w zakresie są obsługiwane.  Globalne tabele tymczasowe są nieobsługiwane.  Ponadto widoki nie można utworzyć na tabelach tymczasowych.  Tabele tymczasowe można tworzyć tylko za pomocą dystrybucji skrótów i działanie okrężne.  Tabeli tymczasowej replikowane dystrybucji nie jest obsługiwane. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat tworzenia tabel, zobacz [Omówienie tabel](sql-data-warehouse-tables-overview.md).

