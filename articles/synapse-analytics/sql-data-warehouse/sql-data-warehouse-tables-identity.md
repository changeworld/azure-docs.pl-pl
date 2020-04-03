---
title: Tworzenie kluczy zastępczych za pomocą funkcji TOŻSAMOŚCI
description: Zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d4a9880ed7ab26d0127026f49c0bc781cfc2a941
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586331"
---
# <a name="using-identity-to-create-surrogate-keys-in-synapse-sql-pool"></a>Tworzenie kluczy zastępczych w puli SQL Synapse za pomocą funkcji TOŻSAMOŚCI

Zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w puli SQL Synapse.

## <a name="what-is-a-surrogate-key"></a>Co to jest klucz zastępczy

Klucz zastępczy w tabeli to kolumna z unikatowym identyfikatorem dla każdego wiersza. Klucz nie jest generowany na podstawie danych tabeli. Modelarze danych lubią tworzyć klucze zastępcze na swoich tabelach podczas projektowania modeli magazynu danych. Można użyć identity właściwości, aby osiągnąć ten cel w prosty i skuteczny sposób bez wpływu na wydajność obciążenia.  

## <a name="creating-a-table-with-an-identity-column"></a>Tworzenie tabeli z kolumną TOŻSAMOŚĆ

Właściwość IDENTITY jest przeznaczona do skalowania w poziomie we wszystkich dystrybucjach w puli Synapse SQL bez wpływu na wydajność obciążenia. W związku z tym wdrożenie TOŻSAMOŚCI jest ukierunkowane na osiągnięcie tych celów.

Tabelę można zdefiniować jako zawierającą właściwość IDENTITY przy pierwszym utworzeniu tabeli przy użyciu składni podobnej do następującej instrukcji:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Następnie można `INSERT..SELECT` użyć do wypełniania tabeli.

W dalszej części tej sekcji przedstawiono niuanse implementacji, aby pomóc Ci lepiej je zrozumieć.  

### <a name="allocation-of-values"></a>Alokacja wartości

Właściwość IDENTITY nie gwarantuje kolejności, w której są przydzielane wartości zastępcze, co odzwierciedla zachowanie programu SQL Server i bazy danych SQL Azure. Jednak w puli SQL Synapse brak gwarancji jest bardziej wyraźny.

Poniższy przykład jest ilustracją:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
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

W poprzednim przykładzie dwa wiersze wylądowały w dystrybucji 1. Pierwszy wiersz ma wartość zastępczą 1 w kolumnie `C1`, a drugi wiersz ma wartość zastępczą 61. Obie te wartości zostały wygenerowane przez właściwość IDENTITY. Jednak alokacja wartości nie jest ciągła. To zachowanie jest celowe.

### <a name="skewed-data"></a>Pochylone dane

Zakres wartości dla typu danych są rozłożone równomiernie między dystrybucjami. Jeśli tabela rozproszona cierpi z powodu pochylonych danych, zakres wartości dostępnych dla typu danych może zostać wyczerpany przedwcześnie. Na przykład jeśli wszystkie dane kończy się w jednej dystrybucji, a następnie skutecznie tabela ma dostęp do tylko jeden sześćdziesiąt wartości wartości typu danych. Z tego powodu właściwość IDENTITY `INT` `BIGINT` jest ograniczona tylko do typów danych i.

### <a name="selectinto"></a>Wybierz.. Do

Gdy istniejąca kolumna TOŻSAMOŚCI jest zaznaczona w nowej tabeli, nowa kolumna dziedziczy właściwość TOŻSAMOŚCI, chyba że spełniony jest jeden z następujących warunków:

- Instrukcja SELECT zawiera sprzężenie.
- Wiele instrukcji SELECT są połączone przy użyciu UNION.
- Kolumna TOŻSAMOŚĆ jest wyświetlana więcej niż jeden raz na liście SELECT.
- Kolumna TOŻSAMOŚĆ jest częścią wyrażenia.

Jeśli którykolwiek z tych warunków jest spełniony, kolumna jest tworzona NIE NULL zamiast dziedziczenia właściwości IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

UTWÓRZ TABELĘ AS SELECT (CTAS) jest zgodne z tym samym zachowaniem programu SQL Server, które jest udokumentowane dla select.. Do. Jednak nie można określić właściwość TOŻSAMOŚCI w `CREATE TABLE` definicji kolumny części instrukcji. Nie można również użyć funkcji TOŻSAMOŚCI `SELECT` w części CTAS. Aby wypełnić tabelę, należy `CREATE TABLE` użyć do zdefiniowania `INSERT..SELECT` tabeli, po której następuje jej wypełnić.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Jawne wstawianie wartości do kolumny TOŻSAMOŚCI

Pula SQL synapsy obsługuje `SET IDENTITY_INSERT <your table> ON|OFF` składnię. Tej składni można użyć do jawnego wstawiania wartości do kolumny TOŻSAMOŚĆ.

Wielu modelarzy danych lubi używać wstępnie zdefiniowanych wartości ujemnych dla niektórych wierszy w ich wymiarach. Przykładem jest wiersz -1 lub "nieznany element członkowski".

Następny skrypt pokazuje, jak jawnie dodać ten wiersz przy użyciu funkcji SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Ładowanie danych

Obecność identity właściwości ma pewne implikacje do kodu ładowania danych. W tej sekcji wyróżniono niektóre podstawowe wzorce ładowania danych do tabel przy użyciu tożsamości.

Aby załadować dane do tabeli i wygenerować klucz zastępczy przy użyciu tożsamości, utwórz tabelę, a następnie użyj INSERT.. WYBIERZ lub WSTAW.. WARTOŚCI, aby wykonać obciążenie.

W poniższym przykładzie wyróżniono podstawowy wzorzec:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Nie można używać `CREATE TABLE AS SELECT` obecnie podczas ładowania danych do tabeli z kolumną TOŻSAMOŚCI.
>

Aby uzyskać więcej informacji na temat ładowania danych, zobacz [Projektowanie wyodrębniania, ładowania i przekształcania (ELT) dla puli sql synapse](design-elt-data-loading.md) i [najlepsze wskazówki dotyczące ładowania](guidance-for-loading-data.md).

## <a name="system-views"></a>Widoki systemowe

Można użyć widoku [katalogu sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) katalogu, aby zidentyfikować kolumnę, która ma właściwość TOŻSAMOŚCI.

Aby lepiej zrozumieć schemat bazy danych, w tym przykładzie pokazano, jak zintegrować plik sys.identity_column" z innymi widokami wykazu systemu:

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

- Gdy typ danych kolumny nie jest INT lub BIGINT
- Gdy kolumna jest również kluczem dystrybucji
- Gdy tabela jest tabelą zewnętrzną

Następujące powiązane funkcje nie są obsługiwane w puli języka SYNAPSE SQL:

- [TOŻSAMOŚĆ()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [Scope_identity](/sql/t-sql/functions/scope-identity-transact-sql)
- [Ident_current](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Typowe zadania

Ta sekcja zawiera przykładowy kod, którego można użyć do wykonywania typowych zadań podczas pracy z kolumnami TOŻSAMOŚCI.

Kolumna C1 jest tożsamości we wszystkich następujących zadań.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Znajdowanie najwyższej przydzielonej wartości tabeli

Funkcja `MAX()` służy do określania najwyższej wartości przydzielonej dla tabeli rozproszonej:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Znajdź materiał siewny i przyrost dla właściwości IDENTITY

Widoki wykazu służy do odnajdywanie wartości konfiguracji przyrostu tożsamości i materiału siewnego dla tabeli przy użyciu następującej kwerendy:

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

- [Omówienie tabeli](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [TWORZENIE TOŻSAMOŚCI TABELI (Transact-SQL) (właściwość)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
