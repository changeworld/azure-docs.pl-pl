---
title: Klucze podstawowe, obce i unikatowe
description: Obsługa ograniczeń tabeli w Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 8f3102425c6f984df0f50bc05eeb6f9a5e66d3dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685490"
---
# <a name="primary-key-foreign-key-and-unique-key-in-azure-sql-data-warehouse"></a>Klucz podstawowy, klucz obcy i unikatowy klucz w Azure SQL Data Warehouse

Dowiedz się więcej na temat ograniczeń tabeli w Azure SQL Data Warehouse, w tym klucz podstawowy, klucz obcy i unikatowy klucz.

## <a name="table-constraints"></a>Ograniczenia tabeli 
Azure SQL Data Warehouse obsługuje następujące ograniczenia tabeli: 
- KLUCZ podstawowy jest obsługiwany tylko w przypadku, gdy są używane obiekty nieklastrowane i niewymuszone.    
- Ograniczenie UNIQUE jest obsługiwane tylko przez niewymuszone użycie.   

Ograniczenie klucza obcego nie jest obsługiwane w Azure SQL Data Warehouse.  

## <a name="remarks"></a>Uwagi
Posiadanie klucza podstawowego i/lub unikatowego klucza pozwala aparatowi magazynu danych generować optymalny plan wykonywania zapytania.  Wszystkie wartości w kolumnie klucza podstawowego lub unikatowej kolumnie ograniczenia powinny być unikatowe. 

Po utworzeniu tabeli z kluczem podstawowym lub ograniczeniem unikatowym w usłudze Azure Data Warehouse użytkownicy muszą upewnić się, że wszystkie wartości w tych kolumnach są unikatowe.  Naruszenie, które może spowodować zwrócenie nieprawidłowego wyniku zapytania.  Ten przykład pokazuje, jak zapytanie może zwracać niedokładne wyniki, jeśli klucz podstawowy lub kolumna ograniczenia UNIQUE zawiera zduplikowane wartości.  

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
Tworzenie tabeli magazynu danych z kluczem podstawowym: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Utwórz tabelę magazynu danych z unikatowym ograniczeniem:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Następne kroki

Następnym krokiem po utworzeniu tabel dla hurtowni danych jest załadowanie danych do tabeli. Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do SQL Data Warehouse](load-data-wideworldimportersdw.md).
