---
title: Definiowanie typów danych
description: Zalecenia dotyczące definiowania typów danych tabeli w puli języka SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5b3d24232c26bb4d483d360eb593bc361190ccfe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631223"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Typy danych tabeli w puli SQL Synapse
W tym artykule znajdują się zalecenia dotyczące definiowania typów danych tabeli w puli SQL. 

## <a name="supported-data-types"></a>Obsługiwane typy danych

Pula SQL obsługuje najczęściej używane typy danych. Aby uzyskać listę obsługiwanych typów danych, zobacz [typy danych](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) w instrukcji CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimalizowanie długości wiersza
Minimalizowanie rozmiaru typów danych skraca długość wiersza, co prowadzi do lepszej wydajności kwerendy. Użyj najmniejszego typu danych, który działa dla danych. 

- Unikaj definiowania kolumn znakowych o dużej długości domyślnej. Jeśli na przykład najdłuższa wartość wynosi 25 znaków, zdefiniuj kolumnę jako VARCHAR(25). 
- Unikaj stosowania [NVARCHAR,](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) gdy potrzebujesz tylko varcharu.
- Jeśli to możliwe, użyj NVARCHAR(4000) lub VARCHAR(8000) zamiast NVARCHAR(MAX) lub VARCHAR(MAX).

Jeśli do ładowania tabel używasz tabel zewnętrznych PolyBase, zdefiniowana długość wiersza tabeli nie może przekraczać 1 MB. Gdy wiersz z danymi o zmiennej długości przekracza 1 MB, można załadować wiersz z BCP, ale nie z PolyBase.

## <a name="identify-unsupported-data-types"></a>Identyfikowanie nieobsługiwało typów danych
Jeśli przeprowadzasz migrację bazy danych z innej bazy danych SQL, możesz znaleźć typy danych, które nie są obsługiwane w puli SQL. Użyj następującej kwerendy, aby wykryć nieobsługiwały typy danych w istniejącym schemacie SQL:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Obejścia nieobsługiwały typy danych

Na poniższej liście przedstawiono typy danych, które nie obsługuje puli SQL i zawiera przydatne alternatywy dla nieobsługiwałych typów danych.

| Nieobsługiwał typ danych | Obejście |
| --- | --- |
| [Geometrii](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Podziel kolumnę na kilka kolumn silnie wpisanych. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Konwertuj na tabele tymczasowe. |
| [Sygnatury czasowej](/sql/t-sql/data-types/date-and-time-types) |Przerób kodu do użycia [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) i [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) funkcji. Tylko stałe są obsługiwane jako wartości domyślne, więc current_timestamp nie można zdefiniować jako ograniczenia domyślnego. Jeśli chcesz przeprowadzić migrację wartości wersji wiersza z kolumny wpisanej sygnaturą czasową, użyj [binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) lub [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) dla wartości wersji wiersza NOT NULL lub NULL. |
| [Xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [typ zdefiniowany przez użytkownika](/sql/relational-databases/native-client/features/using-user-defined-types) |Konwertuj z powrotem na natywny typ danych, jeśli to możliwe. |
| wartości domyślne | Wartości domyślne obsługują tylko literały i stałe. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz [Omówienie tabel .](sql-data-warehouse-tables-overview.md)
