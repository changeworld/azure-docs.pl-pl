---
title: Klucze podstawowe, obce i unikatowe
description: Obsługa ograniczeń tabeli w analizie SQL w usłudze Azure Synapse Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198137"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Klucz podstawowy, klucz obcy i unikatowy klucz w usłudze SQL Analytics

Dowiedz się więcej o ograniczeniach tabel w analizie SQL, w tym klucz podstawowy, klucz obcy i unikatowy klucz.

## <a name="table-constraints"></a>Ograniczenia tabeli 
Program SQL Analytics obsługuje następujące ograniczenia tabeli: 
- KLUCZ podstawowy jest obsługiwany tylko w przypadku, gdy są używane obiekty nieklastrowane i niewymuszone.    
- Ograniczenie UNIQUE jest obsługiwane tylko przez niewymuszone użycie.   

Ograniczenie klucza obcego nie jest obsługiwane w usłudze SQL Analytics.  

## <a name="remarks"></a>Uwagi
Posiadanie klucza podstawowego i/lub unikatowego klucza umożliwia aparatowi usługi SQL Analytics generowanie optymalnego planu wykonywania zapytania.  Wszystkie wartości w kolumnie klucza podstawowego lub unikatowej kolumnie ograniczenia powinny być unikatowe. 

Po utworzeniu tabeli przy użyciu klucza podstawowego lub ograniczenia UNIQUE w analizie SQL użytkownicy muszą upewnić się, że wszystkie wartości w tych kolumnach są unikatowe.  Naruszenie, które może spowodować zwrócenie nieprawidłowego wyniku zapytania.  Ten przykład pokazuje, jak zapytanie może zwracać niedokładne wyniki, jeśli klucz podstawowy lub kolumna ograniczenia UNIQUE zawiera zduplikowane wartości.  

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
Utwórz tabelę analityczną SQL z kluczem podstawowym: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Utwórz tabelę analityczną SQL z unikatowym ograniczeniem:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu tabel dla bazy danych SQL Analytics następnym krokiem jest załadowanie danych do tabeli. Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do baz danych usługi SQL Analytics](load-data-wideworldimportersdw.md).
