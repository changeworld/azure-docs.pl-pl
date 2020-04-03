---
title: Korzystanie z dynamicznego języka SQL
description: Porady dotyczące rozwiązań programistytycznych przy użyciu dynamicznego języka SQL w puli synapse SQL.
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
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619035"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dynamiczny SQL w puli SQL Synapse
W tym artykule znajdują się porady dotyczące rozwiązań programistytycznych przy użyciu dynamicznego języka SQL w puli SQL.

## <a name="dynamic-sql-example"></a>Dynamiczny przykład SQL

Podczas tworzenia kodu aplikacji dla puli SQL może być konieczne użycie dynamicznego języka SQL w celu dostarczania elastycznych, ogólnych i modułowych rozwiązań. Pula SQL nie obsługuje typów danych obiektów blob w tej chwili. 

Nie obsługujące typów danych obiektów blob może ograniczyć rozmiar ciągów, ponieważ typy danych obiektów blob obejmują zarówno typy varchar(max) i nvarchar(max). 

Jeśli używasz tych typów w kodzie aplikacji do tworzenia dużych ciągów, należy podzielić kod na fragmenty i zamiast tego użyć instrukcji EXEC.

Prosty przykład:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Jeśli ciąg jest krótki, można użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) jak zwykle.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny SQL nadal będą podlegać wszystkim regułom sprawdzania poprawności T-SQL.
> 
> 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

