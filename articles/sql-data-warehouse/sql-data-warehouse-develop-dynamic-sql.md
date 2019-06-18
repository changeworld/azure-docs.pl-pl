---
title: Za pomocą dynamiczny język SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące używania dynamiczny język SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861825"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamiczny język SQL w usłudze SQL Data Warehouse
Porady dotyczące używania dynamiczny język SQL w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="dynamic-sql-example"></a>Przykład dynamiczny język SQL

Podczas tworzenia kodu aplikacji dla usługi SQL Data Warehouse, może być konieczne Użyj dynamiczny język sql, aby dostarczać rozwiązania, elastyczna, ogólnego i moduły. Usługa SQL Data Warehouse nie obsługuje typów danych obiektów blob w tym momencie. Nie obsługuje typów danych obiektów blob może być ograniczenie rozmiaru Twoimi ciągami, ponieważ typy danych obiektów blob obejmują typy varchar(max) i nvarchar(max). Jeśli używasz tych typów w kodzie aplikacji do tworzenia dużych ciągów, musisz podział kodu na fragmenty i zamiast tego użyj instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótkim, możesz użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) w zwykły sposób.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny język SQL będą nadal podlegać wszystkie reguły sprawdzania poprawności TSQL.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

