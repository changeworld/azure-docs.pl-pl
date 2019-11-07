---
title: Korzystanie z dynamicznego języka SQL
description: Porady dotyczące korzystania z dynamicznego języka SQL w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 87320128537a235e8047a2f826b0e59c08aef76b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692841"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamiczne SQL w SQL Data Warehouse
Porady dotyczące korzystania z dynamicznego języka SQL w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.

## <a name="dynamic-sql-example"></a>Dynamiczny przykład SQL

Podczas tworzenia kodu aplikacji dla SQL Data Warehouse może być konieczne użycie dynamicznego języka SQL w celu zapewnienia elastycznych, ogólnych i modularnych rozwiązań. W tej chwili SQL Data Warehouse nie obsługuje typów danych obiektów BLOB. Nieobsługiwane typy danych obiektów BLOB mogą ograniczać rozmiar ciągów, ponieważ typy danych obiektów BLOB zawierają zarówno typy varchar (max), jak i nvarchar (max). Jeśli w kodzie aplikacji użyto tych typów do kompilowania dużych ciągów, należy podzielić kod na fragmenty i zamiast tego użyć instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótki, możesz użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jako normalnego.

> [!NOTE]
> Instrukcje wykonane jako dynamiczny kod SQL będą nadal podlegać wszystkim regułom walidacji TSQL.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

