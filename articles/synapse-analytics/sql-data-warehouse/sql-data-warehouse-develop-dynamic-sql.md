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
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633525"
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

Jeśli ciąg jest krótki, można użyć [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) jak zwykle.

> [!NOTE]
> Instrukcje wykonywane jako dynamiczny SQL nadal będą podlegać wszystkim regułom sprawdzania poprawności T-SQL.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
