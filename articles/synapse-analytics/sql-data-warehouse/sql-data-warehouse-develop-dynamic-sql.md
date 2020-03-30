---
title: Korzystanie z dynamicznego języka SQL
description: Porady dotyczące używania dynamicznego języka SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350551"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamiczny SQL w magazynie danych SQL
Porady dotyczące używania dynamicznego języka SQL w usłudze Azure SQL Data Warehouse do tworzenia rozwiązań.

## <a name="dynamic-sql-example"></a>Dynamiczny przykład SQL

Podczas tworzenia kodu aplikacji dla usługi SQL Data Warehouse może być konieczne użycie dynamicznego sql, aby pomóc w dostarczaniu elastycznych, ogólnych i modułowych rozwiązań. Usługa SQL Data Warehouse nie obsługuje typów danych obiektów blob w tej chwili. Nie obsługujące typów danych obiektów blob może ograniczyć rozmiar ciągów, ponieważ typy danych obiektów blob obejmują zarówno typy varchar(max) i nvarchar(max). Jeśli użyto tych typów w kodzie aplikacji do tworzenia dużych ciągów, należy podzielić kod na fragmenty i zamiast tego użyć instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótki, można użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jak zwykle.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny SQL nadal będą podlegać wszystkim regułom sprawdzania poprawności TSQL.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

