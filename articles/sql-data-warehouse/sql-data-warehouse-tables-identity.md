---
title: Aby utworzyć klucze zastępczy — Azure SQL Data Warehouse przy użyciu tożsamości | Dokumentacja firmy Microsoft
description: Zalecenia i przykłady dotyczące tworzenia kluczy zastępczych dla tabel w usłudze Azure SQL Data Warehouse za pomocą właściwości tożsamości.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: be7d1f7d04b759a5d60ba26d391d19d1bc57f325
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154312"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Aby utworzyć klucze zastępczy w usłudze Azure SQL Data Warehouse przy użyciu tożsamości

Zalecenia i przykłady dotyczące tworzenia kluczy zastępczych dla tabel w usłudze Azure SQL Data Warehouse za pomocą właściwości tożsamości.

## <a name="what-is-a-surrogate-key"></a>Co to jest klucz zastępczy

Klucz zastępczy w tabeli jest kolumną o unikatowym identyfikatorze dla każdego wiersza. Klucz nie jest generowana z danych tabeli. Aby utworzyć klucze zastępczy na ich tabel, podczas projektowania modeli magazynu danych, takich jak modelujące dane. Właściwość tożsamości umożliwia osiągnięcie tego celu po prostu i skutecznie bez wpływu na wydajność obciążenia.  

## <a name="creating-a-table-with-an-identity-column"></a>Tworzenie tabeli z kolumną IDENTITY

Właściwość IDENTITY jest przeznaczony do skalowania we wszystkich dystrybucjach w magazynie danych bez wpływu na wydajność obciążenia. Dlatego implementacja tożsamości jest opracowane głównie pod kątem osiągnięcie tych celów.

Można zdefiniować tabelę jako mające właściwość tożsamości, podczas tworzenia tabeli za pomocą składni, która jest podobna do następującej instrukcji:

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

Następnie można użyć `INSERT..SELECT` do wypełniania tabeli.

Ta dalszej części tej sekcji wyróżnia wszystkie szczegóły implementacji, aby lepiej zrozumieć je dokładniej.  

### <a name="allocation-of-values"></a>Alokacja wartości

Właściwość tożsamości nie gwarantuje kolejności, w którym wartości zastępcze są przydzielane, w której odzwierciedla zachowanie programu SQL Server i usługi Azure SQL Database. Jednak w usłudze Azure SQL Data Warehouse i braku gwarancji jest większa.

Poniższy przykład jest ilustrację:

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

W powyższym przykładzie dwa wiersze otwarta dystrybucji 1. Pierwszy wiersz zawiera wartość zastępcza 1 w kolumnie `C1`, a drugi wiersz zawiera wartość zastępcza 61. Obie te wartości zostały wygenerowane przez właściwość tożsamości. Jednak alokacji wartości nie jest ciągły. To zachowanie jest celowe.

### <a name="skewed-data"></a>Niesymetryczne dane

Zakres wartości dla typu danych zostały rozmieszczone równomiernie między dystrybucjami. Jeśli tabelę z dystrybucją odczuwa niesymetryczne dane, zakres wartości, które są dostępne na typ danych może wyczerpać przedwcześnie. Na przykład jeśli wszystkie dane będą istnieć w jednym dystrybucję, następnie efektywnie tabeli ma dostęp do tylko jednej szóstej wartości o typie danych. Z tego powodu właściwość IDENTITY jest ograniczona do `INT` i `BIGINT` tylko typy danych.

### <a name="selectinto"></a>WYBIERZ... DO

Wybranie istniejącej kolumny tożsamości do nowej tabeli nowej kolumny, która dziedziczy właściwości tożsamości, o ile nie jest spełniony jeden z następujących warunków:

- Instrukcja SELECT zawiera instrukcję join.
- Wiele instrukcji "SELECT" są sprzęgane przy użyciu Unii.
- W kolumnie tożsamości znajduje się więcej niż jeden raz na liście wyboru.
- Kolumna tożsamości jest częścią wyrażenia.

Jeśli jeden z tych warunków jest spełniony, kolumna jest tworzony NOT NULL zamiast dziedziczy właściwości tożsamości.

### <a name="create-table-as-select"></a>UTWÓRZ TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) jest zgodna takie samo zachowanie programu SQL Server, który opisano w wybierz... DO. Jednak nie można określić właściwość tożsamości w definicji kolumny `CREATE TABLE` element instrukcji. Ponadto nie można używać funkcji IDENTITY w `SELECT` wchodzi w skład CTAS. Aby wypełnić tabelę, należy użyć `CREATE TABLE` do definiowania tabeli, a następnie `INSERT..SELECT` wypełnić.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Jawnie wstawiania wartości do kolumny tożsamości

Usługa SQL Data Warehouse obsługuje `SET IDENTITY_INSERT <your table> ON|OFF` składni. Można użyć tej składni jawnie wstawiania wartości do kolumny tożsamości.

Wiele modelujące dane, takie jak używać wstępnie zdefiniowanych wartości ujemnych dla niektórych wierszy w wymiarami. Przykładem jest wiersza "Nieznany element członkowski" lub wartość -1.

Następny skrypt pokazuje, jak jawnie dodać tego wiersza przy użyciu USTAWIONY atrybut IDENTITY_INSERT:

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

Obecność właściwości tożsamości ma pewne skutki dla kodu ładowania danych. W tej sekcji przedstawiono niektóre podstawowe wzorce do ładowania danych do tabel przy użyciu tożsamości.

Aby załadować dane do tabeli i wygenerować klucz zastępczy przy użyciu tożsamości, utworzyć tabelę, a następnie użyj INSERT... Wybierz lub Wstaw... WARTOŚCI do wykonywania obciążenia.

W poniższym przykładzie wyróżniono podstawowy wzorzec:

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
> Nie jest możliwe użycie `CREATE TABLE AS SELECT` obecnie, podczas ładowania danych do tabeli z kolumną tożsamości.
>

Aby uzyskać więcej informacji na temat ładowania danych, zobacz [projektowania wyodrębnianie, ładowania i przekształcania (ELT) dla usługi Azure SQL Data Warehouse](design-elt-data-loading.md) i [najlepsze rozwiązania dotyczące ładowania](guidance-for-loading-data.md).

## <a name="system-views"></a>Widoki systemowe

Możesz użyć [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalogu widoku, aby określić kolumny, która ma właściwość tożsamości.

Aby pomóc Ci lepiej zrozumieć schemat bazy danych, w tym przykładzie pokazano, jak zintegrować sys.identity_column "z innymi widokami katalog systemu:

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

Nie można użyć właściwości tożsamości:

- Gdy typ danych kolumny nie jest INT lub BIGINT
- Jeśli kolumna jest również klucza dystrybucji
- Jeśli tabela jest tabeli zewnętrznej

Następujące funkcje powiązane nie są obsługiwane w usłudze SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Typowe zadania

W tej sekcji przedstawiono przykładowy kod służących do wykonywania typowych zadań, podczas pracy z kolumn tożsamości.

Kolumna C1 to tożsamość w następujące zadania.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Znajdź najwyższą wartość przydzielonego dla tabeli

Użyj `MAX()` funkcję, aby określić najwyższą wartość przydzielone dystrybuowanej tabeli:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Znajdź początkowej i wartości przyrostu dla właściwości tożsamości

Widoków wykazu służy do odnajdywania inkrementacji i inicjatora konfiguracji wartości tożsamości dla tabeli przy użyciu następującej kwerendy:

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

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie tabel](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Tworzenie tożsamości TABLE (Transact-SQL) (właściwość)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [POLECENIE DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
