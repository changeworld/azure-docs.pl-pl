---
title: Za pomocą dynamicznej SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące używania dynamicznych SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 260c8b69cbe783c2cf18e40669fe742867ab133d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamiczne SQL w usłudze SQL Data Warehouse
Porady dotyczące używania dynamicznych SQL w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="dynamic-sql-example"></a>Przykład dynamiczne SQL

Podczas tworzenia kodu aplikacji dla usługi SQL Data Warehouse, konieczne może korzystać z dynamicznych sql w celu udostępnienia elastyczne, generycznych i moduły rozwiązania. Usługi SQL Data Warehouse nie obsługuje typów danych obiektów blob w tym momencie. Nie obsługuje typu danych obiektu blob może limit rozmiaru z ciągów, ponieważ typy danych obiektów blob obejmują typy zarówno varchar(max), jak i nvarchar(max). Tworzenie dużych ciągów w przypadku użycia tych typów w kodzie aplikacji, musisz przerwać kod na fragmenty i zamiast tego użyj instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótkim, możesz użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalnego.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny SQL będą nadal podlegać wszystkie reguły sprawdzania poprawności TSQL.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

