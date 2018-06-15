---
title: Definiowanie typów danych — magazyn danych SQL Azure | Dokumenty Microsoft
description: Zalecenia dotyczące definiowania typów danych tabeli w magazynie danych programu SQL Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4d8a222a6d4cfa4138fe833fb4e9cc895dbc5f65
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523510"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabela typów danych w hurtowni danych programu SQL Azure
Zalecenia dotyczące definiowania typów danych tabeli w magazynie danych programu SQL Azure. 

## <a name="what-are-the-data-types"></a>Jakie są typy danych?

Magazyn danych SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizowanie długość wiersza
Minimalizowanie rozmiar typów danych o skraca długość wiersza, co prowadzi do poprawy wydajności zapytania. Użyj najmniejszą typu danych, który działa danych. 

- Unikaj definiowania kolumn znakowych o długości dużych domyślne. Na przykład jeśli wartość najdłuższym 25 znaków, następnie zdefiniuj kolumny jako VARCHAR(25). 
- Należy unikać używania [NVARCHAR] [NVARCHAR] wystarczy VARCHAR.
- Jeśli to możliwe, zamiast VARCHAR(MAX) lub NVARCHAR(MAX), albo użyć NVARCHAR(4000) lub VARCHAR(8000).

Jeśli używasz tabel zewnętrznych PolyBase załadować tabel zdefiniowanej długości wiersza tabeli nie może przekraczać 1 MB. Jeśli wiersz o zmiennej długości danych przekracza 1 MB, można załadować wiersza za pomocą narzędzia BCP, ale nie przy użyciu programu PolyBase.

## <a name="identify-unsupported-data-types"></a>Zidentyfikuj nieobsługiwane typy danych
W przypadku migracji z innej bazy danych SQL bazy danych, może wystąpić typy danych, które nie są obsługiwane w usłudze SQL Data Warehouse. Skorzystaj z tej kwerendy, aby odnaleźć nieobsługiwane typy danych w istniejącej schematu SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Rozwiązania problemu spowodowanego nieobsługiwane typy danych

Na poniższej liście przedstawiono typy danych nie obsługuje usługi SQL Data Warehouse, a udostępnia opis rozwiązań alternatywnych, których można użyć zamiast nieobsługiwane typy danych.

| Nieobsługiwany typ danych | Obejście problemu |
| --- | --- |
| [Geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Lokalizacja geograficzna](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Identyfikator hierarchii](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Podziel kolumnę na kilka jednoznacznie kolumn. |
| [Tabela](/sql/t-sql/data-types/table-transact-sql) |Konwertuj do tabel tymczasowych. |
| [Znacznik czasu](/sql/t-sql/data-types/date-and-time-types) |Przerobienie kodu w celu użycia [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) i [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkcji. Obsługiwane są tylko stałe jako domyślne, w związku z tym current_timestamp nie może być zdefiniowana jako ograniczenie domyślne. Jeśli zachodzi konieczność migracji wersji wartości wiersza z wpisywanych kolumny sygnatury czasowej, a następnie użyć [BINARNYM](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) lub [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) dla nie NULL, lub NULL, wiersz wersji wartości. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Przekonwertować typu danych natywnych, gdy jest to możliwe. |
| wartości domyślne | Wartości domyślne obsługuje literały i tylko stałe. |


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących tworzenia tabel, zobacz [omówienie tabeli](sql-data-warehouse-tables-overview.md).
