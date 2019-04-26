---
title: Definiowanie typów danych — Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zalecenia dotyczące definiowania typów danych tabeli w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: aab51c3dc66a1486e8ad7ced55425a2b49c7dea1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60401769"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Typy danych tabeli w usłudze Azure SQL Data Warehouse
Zalecenia dotyczące definiowania typów danych tabeli w usłudze Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jakie są typy danych?

Usługa SQL Data Warehouse obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizuj długość wiersza
Minimalizacja rozmiaru typy danych skraca długość wiersza, co prowadzi do lepszej wydajności. Użyj najmniejszego typu danych, który działa dla swoich danych. 

- Unikaj definiowania kolumn znaków jako kolumn o długości dużych domyślne. Na przykład jeśli Najdłuższa wartość ma 25 znaków, należy zdefiniować typ kolumny jako VARCHAR(25). 
- Należy unikać używania [NVARCHAR] [NVARCHAR], jeśli potrzebujesz tylko VARCHAR.
- Jeśli to możliwe, zamiast VARCHAR(MAX) lub NVARCHAR(MAX), albo użyć NVARCHAR(4000) lub VARCHAR(8000).

Jeśli używasz tabel zewnętrznych PolyBase można załadować tabel, określona długość wiersza tabeli nie może przekraczać 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, należy załadować wiersza za pomocą narzędzia BCP, ale nie przy użyciu technologii PolyBase.

## <a name="identify-unsupported-data-types"></a>Identyfikowanie nieobsługiwane typy danych
Jeśli migrujesz bazy danych z innej bazy danych SQL, może wystąpić typy danych, które nie są obsługiwane w usłudze SQL Data Warehouse. Skorzystaj z tej kwerendy, aby odnaleźć nieobsługiwane typy danych w istniejących schematu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Obejścia nieobsługiwane typy danych

Na poniższej liście przedstawiono typy danych, SQL Data Warehouse nie obsługuje i udostępnia alternatywne, które można użyć zamiast nieobsługiwane typy danych.

| Nieobsługiwany typ danych | Obejście |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Lokalizacja geograficzna](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Identyfikator hierarchii](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Podziel kolumnę na kilku silnie typizowanych kolumn. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konwertuj do tabel tymczasowych. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Przeanalizować kod w celu użycia [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) i [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkcji. Obsługiwane są tylko stałe jako domyślne, dlatego current_timestamp nie może być zdefiniowana jako domyślnego ograniczenia. Jeśli zajdzie potrzeba migracji wartości wersji wierszy z kontrolą typów kolumnę sygnatur czasowych, należy użyć [BINARNE](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) lub [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) dla nie wartość NULL lub wartość NULL wiersz wartości wersji. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Konwertowanie do typu danych w trybie macierzystym, gdy jest to możliwe. |
| Wartości domyślne | Wartości domyślne obsługuje literałów i stałych tylko. |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabel](sql-data-warehouse-tables-overview.md).
