---
title: Do tworzenia kluczy Surogat - Azure SQL Data Warehouse przy użyciu tożsamości | Dokumentacja firmy Microsoft
description: Zalecenia i przykłady dotyczące do tworzenia kluczy zastępczego w tabelach w usłudze Azure SQL Data Warehouse przy użyciu właściwości tożsamości.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: ab028705f5af7c37017d2e697240b7d3436f5f71
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526988"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Do tworzenia kluczy dwuskładnikowego w usłudze Azure SQL Data Warehouse przy użyciu tożsamości
Zalecenia i przykłady dotyczące do tworzenia kluczy zastępczego w tabelach w usłudze Azure SQL Data Warehouse przy użyciu właściwości tożsamości.

## <a name="what-is-a-surrogate-key"></a>Co to jest klucz Surogat?
Zastępcza klucza w tabeli jest kolumna z unikatowym identyfikatorem dla każdego wiersza. Klucz nie jest generowana z danych tabeli. Do tworzenia kluczy Surogat na ich tabel, podczas projektowania modeli magazynu danych, takich jak Modelarze danych. Właściwość IDENTITY można używać na osiągnięcie tego celu po prostu i efektywnie bez wpływu na wydajność obciążenia.  

## <a name="creating-a-table-with-an-identity-column"></a>Tworzenie tabeli zawierającej kolumnę tożsamości
Właściwość IDENTITY jest przeznaczona do skalują poza wszystkie dystrybucje w magazynie danych bez wpływu na wydajność obciążenia. W związku z tym implementacja tożsamości jest zorientowany osiągnięcie tych celów. 

Można zdefiniować tabelę jako mający właściwość IDENTITY podczas tworzenia tabeli za pomocą składni, która jest podobna do następującej instrukcji:

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

Następnie można użyć `INSERT..SELECT` do wypełnienia tabeli.

Ta pozostałej części tej sekcji omówiono wszystkie szczegóły implementacji, aby ułatwić zrozumienie ich dokładniejszego.  

### <a name="allocation-of-values"></a>Alokacja wartości
Właściwość IDENTITY nie gwarantuje kolejność, w którym są przydzielane wartości Surogat, odzwierciedla zachowanie programu SQL Server i bazy danych SQL Azure. Jednak w usłudze Azure SQL Data Warehouse, braku gwarancji jest większa. 

Poniższy przykład jest ilustracji:

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

Dwa wiersze w powyższym przykładzie jest dystrybucji 1. Pierwszy wiersz zawiera wartość dwuskładnikowego 1 w kolumnie `C1`, a drugi wiersz zawiera wartość zastępcza 61. Obie te wartości zostały wygenerowane przez właściwość tożsamości. Jednak alokacji wartości nie jest ciągły. To zachowanie jest celowe.

### <a name="skewed-data"></a>Spowodowałoby zafałszowanie danych 
Zakres wartości dla typu danych są rozmieszczone równomiernie w obrębie dystrybucje. Jeśli tabela rozproszona odczuwa spowodowałoby zafałszowanie danych, dostępna na typ danych wartości z zakresu można wyczerpane przedwcześnie. Na przykład jeśli wszystkie dane kończy się w jednym dystrybucji, następnie efektywnie tabeli ma dostęp do tylko jednej szóstej wartości typu danych. Z tego powodu właściwość IDENTITY jest ograniczona do `INT` i `BIGINT` tylko typy danych.

### <a name="selectinto"></a>WYBIERZ... DO
Zaznaczenie istniejącej kolumny tożsamości do nowej tabeli nowej kolumny, która dziedziczy właściwości tożsamości, chyba że jest spełniony jeden z następujących warunków:
- Instrukcja SELECT zawiera sprzężenia.
- Wiele instrukcji "SELECT" są sprzęgane przy użyciu UNION.
- Kolumna tożsamości jest wymieniona więcej niż jeden raz na liście wyboru.
- Kolumna tożsamości jest częścią wyrażenia typu.
    
Jeśli jeden z tych warunków jest prawdziwy, kolumna jest tworzony NOT NULL zamiast dziedziczenia właściwości tożsamości.

### <a name="create-table-as-select"></a>UTWÓRZ TABLE AS SELECT
Tworzenie tabeli jako wybierz (CTAS) następuje tego samego zachowania programu SQL Server jest udokumentowany wybierz pozycję... DO. Jednak nie można określić właściwości tożsamości w definicji kolumny `CREATE TABLE` element instrukcji. Ponadto nie można używać funkcji IDENTITY w `SELECT` częścią CTAS. Aby wypełnić tabeli, należy użyć `CREATE TABLE` do definiowania tabeli, a następnie `INSERT..SELECT` aby wypełnić go.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Jawnie wstawianie wartości do kolumny tożsamości 
Magazyn danych SQL obsługuje `SET IDENTITY_INSERT <your table> ON|OFF` składni. Ta składnia umożliwia jawnie wstawić wartości w kolumnie tożsamości.

Aby użyć wstępnie zdefiniowanych wartości ujemnych dla niektórych wierszy wymiarami, takich jak wiele Modelarze danych. Przykładem jest wiersza "Nieznany element członkowski" lub wartość -1. 

Skrypt dalej pokazano, jak jawnie dodać ten wiersz za pomocą USTAWIĆ atrybut IDENTITY_INSERT:

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

Obecność właściwości tożsamości implikacje niektórych kodu ładowania danych. W tej sekcji opisano niektóre z wzorców podstawowych ładowania danych do tabel za pomocą tożsamości. 

Aby załadować dane do tabeli i generowanie klucza dwuskładnikowego przy użyciu tożsamości, należy utworzyć tabeli, a następnie użyć INSERT... Wybierz lub Wstaw... WARTOŚCI do wykonania obciążenia.

Poniższy przykład prezentuje podstawowy wzorzec:
 
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

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Nie jest możliwe tworzenie tabeli WYBIER AS "obecnie, podczas ładowania danych do tabeli z kolumną tożsamości.
> 

Aby uzyskać więcej informacji dotyczące ładowania danych, zobacz [projektowania wyodrębnić, obciążenia i przekształcenie (ELT) dla usługi Azure SQL Data Warehouse](design-elt-data-loading.md) i [ładowanie najlepsze rozwiązania w zakresie](guidance-for-loading-data.md).


## <a name="system-views"></a>Widoki systemowe
Można użyć [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) widoku, aby określić kolumny, która ma właściwość IDENTITY w katalogu.

Aby lepiej zrozumieć schemat bazy danych, w tym przykładzie pokazano, jak zintegrować sys.identity_column "z innymi widokami katalog systemu:

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
- Jeśli typ danych kolumny nie jest INT lub BIGINT
- Jeśli kolumna jest również dystrybucji kluczy
- Jeśli tabela jest tabeli zewnętrznej 

Następujące funkcje pokrewne nie są obsługiwane w usłudze SQL Data Warehouse:

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [ATRYBUTU IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)
- [POLECENIE DBCC CHECK_IDENT()](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)

## <a name="common-tasks"></a>Typowe zadania

Ta sekcja zawiera niektóre przykładowy kod służący do wykonywania typowych zadań, podczas pracy z kolumn tożsamości. 

Kolumna C1 jest tożsamość w następujące zadania.
 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Znajdź największa wartość przydzielonego dla tabeli
Użyj `MAX()` funkcji, aby ustalić najwyższą wartość przydzielone dla tabeli rozproszonych:

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Znajdź początkowej i wartości przyrostu dla właściwości tożsamości
Widokach katalogów służy do odnajdywania inkrementacji i inicjatora konfiguracji wartości tożsamości dla tabeli za pomocą następującej kwerendy: 

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

* Aby dowiedzieć się więcej na temat tworzenia tabel, zobacz [omówienie tabeli] [omówienie].  

