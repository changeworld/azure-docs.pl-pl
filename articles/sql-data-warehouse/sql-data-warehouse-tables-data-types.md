---
title: Definiowanie typów danych — Azure SQL Data Warehouse | Microsoft Docs
description: Zalecenia dotyczące definiowania typów danych tabeli w Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61bb977271186699b0a72389e1538573f978c56b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479368"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Typy danych tabeli w Azure SQL Data Warehouse
Zalecenia dotyczące definiowania typów danych tabeli w Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Jakie są typy danych?

SQL Data Warehouse obsługuje najczęściej używane typy danych. Aby zapoznać się z listą obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizuj długość wiersza
Minimalizacja rozmiaru typów danych skraca długość wiersza, co prowadzi do lepszej wydajności zapytań. Użyj najmniejszego typu danych, który działa dla danych. 

- Należy unikać definiowania kolumn znaków o dużej długości domyślnej. Na przykład jeśli najdłuższa wartość to 25 znaków, zdefiniuj kolumnę jako VARCHAR (25). 
- Należy unikać używania [NVARCHAR] [NVARCHAR], gdy wymagany jest tylko VARCHAR.
- Jeśli to możliwe, należy użyć parametru NVARCHAR (4000) lub VARCHAR (8000) zamiast typu NVARCHAR (MAX) lub VARCHAR (MAX).

Jeśli używasz wielobazowych tabel zewnętrznych do ładowania tabel, określona długość wiersza tabeli nie może przekroczyć 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, można załadować wiersz za pomocą narzędzia BCP, ale nie z bazą danych.

## <a name="identify-unsupported-data-types"></a>Zidentyfikuj nieobsługiwane typy danych
W przypadku migrowania bazy danych z innej bazy danych SQL mogą wystąpić typy danych, które nie są obsługiwane w SQL Data Warehouse. Użyj tego zapytania, aby odnaleźć nieobsługiwane typy danych w istniejącym schemacie SQL.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Obejścia dla nieobsługiwanych typów danych

Na poniższej liście przedstawiono typy danych, które nie są obsługiwane przez SQL Data Warehouse i dają alternatywy, których można użyć zamiast nieobsługiwanych typów danych.

| Nieobsługiwany typ danych | Obejście |
| --- | --- |
| [geometrii](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[liczby](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geograficzne](/sql/t-sql/spatial-geography/spatial-types-geography) |[liczby](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) (4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[liczby](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Podziel kolumnę na kilka kolumn o jednoznacznie określonym typie. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konwertuj na tabele tymczasowe. |
| [znacznik czasu](/sql/t-sql/data-types/date-and-time-types) |Przepracuj kod, aby użyć [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) i funkcji [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) . Tylko stałe są obsługiwane jako wartości domyślne, dlatego nie można zdefiniować CURRENT_TIMESTAMP jako ograniczenia domyślnego. Jeśli konieczne jest przeprowadzenie migracji wartości wersji wiersza z kolumny o określonym typie sygnatury [](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)czasowej, użyj wartości binarnych (8) lub [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8), aby uzyskać wartość niezerową lub null. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Przekonwertuj z powrotem na natywny typ danych, gdy jest to możliwe. |
| wartości domyślne | Wartości domyślne obsługują tylko literały i stałe. |


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabeli](sql-data-warehouse-tables-overview.md).
