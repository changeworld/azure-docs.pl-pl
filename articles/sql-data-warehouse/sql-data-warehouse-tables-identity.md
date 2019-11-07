---
title: Używanie tożsamości do tworzenia kluczy zastępczych
description: Zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0ee15b975b5513077b26cceeb80ea3fb8c02456b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692469"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Używanie tożsamości do tworzenia kluczy zastępczych w Azure SQL Data Warehouse

Zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>Co to jest klucz zastępczy

Klucz zastępczy w tabeli jest kolumną o unikatowym identyfikatorze dla każdego wiersza. Klucz nie jest generowany na podstawie danych tabeli. Modele danych, takie jak tworzenie kluczy zastępczych w tabelach, podczas projektowania modeli magazynu danych. Można użyć właściwości IDENTITY do osiągnięcia tego celu po prostu i efektywnie bez wpływu na wydajność ładowania.  

## <a name="creating-a-table-with-an-identity-column"></a>Tworzenie tabeli z kolumną tożsamości

Właściwość IDENTITY została zaprojektowana w celu skalowania w poziomie między wszystkimi dystrybucjami w hurtowni danych bez wpływu na wydajność ładowania. W związku z tym implementacja tożsamości jest ukierunkowana na osiągnięcie tych celów.

Można zdefiniować tabelę jako mającą właściwość IDENTITY podczas pierwszej tworzenia tabeli przy użyciu składni podobnej do następującej:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Następnie można użyć `INSERT..SELECT`, aby wypełnić tabelę.

W tym pozostałej części tej sekcji przedstawiono wszystkie szczegóły wdrożenia, aby ułatwić zrozumienie ich w pełni.  

### <a name="allocation-of-values"></a>Alokacja wartości

Właściwość IDENTITY nie gwarantuje kolejności, w której są przyliczane wartości zastępcze, co odzwierciedla zachowanie SQL Server i Azure SQL Database. Jednak w Azure SQL Data Warehouse nie ma więcej informacji na temat braku gwarancji.

Poniższy przykład jest ilustracją:

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

W poprzednim przykładzie dwa wiersze wyładowywane w dystrybucji 1. Pierwszy wiersz ma wartość zastępczą 1 w kolumnie `C1`, a drugi wiersz ma wartość zastępczą 61. Obie te wartości zostały wygenerowane przez właściwość IDENTITY. Jednak alokacja wartości nie jest ciągła. To zachowanie jest celowe.

### <a name="skewed-data"></a>Skośne dane

Zakres wartości dla typu danych jest równomiernie rozłożony przez dystrybucje. Jeśli tabela rozproszona pogorszy się od pochylonych danych, zakres wartości dostępnych dla tego elementu DataType może zostać przedwcześnie wyczerpany. Na przykład, jeśli wszystkie dane kończą się w ramach jednej dystrybucji, efektywnie tabela ma dostęp tylko do jednej sixtieth wartości typu danych. Z tego powodu właściwość IDENTITY jest ograniczona do `INT` i `BIGINT` tylko typy danych.

### <a name="selectinto"></a>Wybierz pozycję.. PRZEKSZTAŁCA

Po wybraniu istniejącej kolumny tożsamości w nowej tabeli, Nowa kolumna dziedziczy właściwość IDENTITY, chyba że jest spełniony jeden z następujących warunków:

- Instrukcja SELECT zawiera sprzężenie.
- Wielokrotne instrukcje SELECT są sprzężone przy użyciu UNION.
- Kolumna tożsamości jest wymieniona więcej niż jeden raz na liście wyboru.
- Kolumna tożsamości jest częścią wyrażenia.

Jeśli którykolwiek z tych warunków ma wartość true, kolumna zostanie utworzona nie NULL zamiast dziedziczyć właściwość IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE JAKO WYBRANE

CREATE TABLE jako SELECT (CTAS) ma takie samo zachowanie SQL Server, które zostało udokumentowane w przypadku WYBRANia.. Przekształca. Nie można jednak określić właściwości IDENTITY w definicji kolumny części `CREATE TABLE` instrukcji. Nie można również użyć funkcji IDENTITY w `SELECT` części CTAS. Aby wypełnić tabelę, należy użyć `CREATE TABLE` do zdefiniowania tabeli, a następnie `INSERT..SELECT` do jej wypełnienia.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Jawne wstawianie wartości do kolumny tożsamości

SQL Data Warehouse obsługuje składnię `SET IDENTITY_INSERT <your table> ON|OFF`. Możesz użyć tej składni, aby jawnie wstawić wartości do kolumny tożsamość.

Wiele modeli danych, takich jak użycie wstępnie zdefiniowanych wartości ujemnych dla niektórych wierszy w ich wymiarach. Przykładem jest wiersz-1 lub "nieznany element członkowski".

Następny skrypt pokazuje, jak jawnie dodać ten wiersz przy użyciu SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Ładowanie danych

Obecność właściwości IDENTITY ma pewne konsekwencje związane z kodem ładowania danych. W tej sekcji przedstawiono podstawowe wzorce dotyczące ładowania danych do tabel przy użyciu tożsamości.

Aby załadować dane do tabeli i wygenerować klucz zastępczy przy użyciu tożsamości, należy utworzyć tabelę, a następnie użyć instrukcji INSERT.. Wybierz lub Wstaw.. WARTOŚCI do wykonania ładowania.

Poniższy przykład wyróżnia wzorzec podstawowy:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Nie jest możliwe używanie `CREATE TABLE AS SELECT` obecnie podczas ładowania danych do tabeli z kolumną tożsamości.
>

Aby uzyskać więcej informacji na temat ładowania danych, zobacz [projektowanie wyodrębniania, ładowania i przekształcania (ELT) w celu Azure SQL Data Warehouse](design-elt-data-loading.md) i [ładowania najlepszych](guidance-for-loading-data.md)rozwiązań.

## <a name="system-views"></a>Widoki systemowe

Możesz użyć widoku wykazu [sys. identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) , aby zidentyfikować kolumnę, która ma właściwość Identity.

Aby lepiej zrozumieć schemat bazy danych, w tym przykładzie pokazano, jak zintegrować sys. identity_column ' z innymi widokami wykazu systemu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Ograniczenia

Nie można użyć właściwości IDENTITY:

- Gdy typem danych kolumny nie jest INT lub BIGINT
- Gdy kolumna jest również kluczem dystrybucji
- Gdy tabela jest tabelą zewnętrzną

Następujące powiązane funkcje nie są obsługiwane w SQL Data Warehouse:

- [TOŻSAMOŚĆ ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Typowe zadania

Ta sekcja zawiera przykładowy kod, którego można użyć do wykonywania typowych zadań podczas pracy z kolumnami tożsamości.

Kolumna C1 jest TOŻSAMOŚCIą we wszystkich następujących zadaniach.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Znajdź najwyższą przydzieloną wartość dla tabeli

Użyj funkcji `MAX()`, aby określić największą wartość przydzieloną dla rozproszonej tabeli:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Znajdź inicjator i przyrost dla właściwości IDENTITY

Widoków wykazu można użyć do odnajdywania wartości konfiguracji przyrostu i inicjatora tożsamości dla tabeli przy użyciu następującej kwerendy:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie tabel](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [TOŻSAMOŚĆ CREATE TABLE (Transact-SQL) (Właściwość)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [POLECENIE DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
