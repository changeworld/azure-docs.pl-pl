---
title: Klucze podstawowe, obce i unikatowe
description: Obsługa ograniczeń tabeli w puli sql synapse w usłudze Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f97163d02836442430037e18439bcf0724046332
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990773"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Klucz podstawowy, klucz obcy i unikatowy klucz w puli SQL Synapse

Dowiedz się więcej o ograniczeniach tabeli w puli sql synapse, w tym klucza podstawowego, klucza obcego i klucza unikatowego.

## <a name="table-constraints"></a>Ograniczenia tabeli

Puli SQL Synapse obsługuje te ograniczenia tabeli: 
- Klucz podstawowy jest obsługiwany tylko wtedy, gdy są używane nieklastrowane i niewymnaczone.    
- Ograniczenie UNIQUE jest obsługiwane tylko z nieegzekwowane jest używany.

Aby uzyskać składnię, zaznacz pole [wyboru ZMIEŃ TABELĘ](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) i [UTWÓRZ TABELĘ](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Ograniczenie KLUCZA OBCEGO nie jest obsługiwane w puli języka SQL Synapse.  


## <a name="remarks"></a>Uwagi

Posiadanie klucza podstawowego i/lub klucza unikatowego umożliwia aparat puli Synapse SQL do generowania optymalnego planu wykonania kwerendy.  Wszystkie wartości w kolumnie klucza podstawowego lub kolumnie unikatowego ograniczenia powinny być unikatowe.

Po utworzeniu tabeli z kluczem podstawowym lub unikatowym ograniczeniem w puli Synapse SQL użytkownicy muszą upewnić się, że wszystkie wartości w tych kolumnach są unikatowe.  Naruszenie tego może spowodować kwerendy do zwrócenia niedokładne wyniki.  W tym przykładzie pokazano, jak kwerenda może zwrócić niedokładny wynik, jeśli klucz podstawowy lub kolumna ograniczeń unikatowych zawiera zduplikowane wartości.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Przykłady

Tworzenie tabeli puli Synapse SQL z kluczem podstawowym: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Utwórz tabelę puli Synapse SQL z unikatowym ograniczeniem:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu tabel dla puli SQL Synapse następnym krokiem jest załadowanie danych do tabeli. Aby zapoznać się z samouczkiem ładowania, zobacz [Ładowanie danych do puli SQL Synapse](load-data-wideworldimportersdw.md).
