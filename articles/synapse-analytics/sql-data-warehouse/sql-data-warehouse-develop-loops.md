---
title: Korzystanie z pętli T-SQL
description: Porady dotyczące tworzenia rozwiązań przy użyciu pętli T-SQL i zastępowania kursorów w puli SQL Synapse.
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
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633476"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Korzystanie z pętli T-SQL w puli Synapse SQL

W tym artykule znajdują się wskazówki dotyczące tworzenia rozwiązań puli SQL przy użyciu pętli T-SQL i zastępowania kursorów.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

Puli SQL Synapse obsługuje [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pętli wielokrotnie wykonywania bloków instrukcji. Ta pętla WHILE jest kontynuowana tak długo, jak długo określone warunki są prawdziwe lub dopóki kod specjalnie kończy pętlę przy użyciu break słowa kluczowego.

Pętle są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są napisane w kodzie SQL są szybko do przodu, tylko do odczytu odmiany. Tak, PODCZAS pętli są doskonałą alternatywą dla wymiany kursorów.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Zastępowanie kursorów w puli SQL Synapse

Jednak przed nurkowaniem w głowie najpierw należy zadać sobie następujące pytanie: "Czy ten kursor może być przepisany do korzystania z operacji opartych na zestawach?"

W wielu przypadkach odpowiedź brzmi tak i często jest najlepszym podejściem. Operacja oparta na zestawie często wykonuje szybciej niż iteracyjne podejście wiersz po wierszu.

Kursory tylko do odczytu do przodu można łatwo zastąpić konstrukcją pętli. Poniższy przykład jest prosty. W tym przykładzie kodu aktualizuje statystyki dla każdej tabeli w bazie danych. Przez iteracji nad tabelami w pętli, każde polecenie wykonuje w sekwencji.

Najpierw utwórz tabelę tymczasową zawierającą unikatowy numer wiersza używany do identyfikowania poszczególnych instrukcji:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Po drugie, zainicjować zmienne wymagane do wykonania pętli:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz pętla nad instrukcjami wykonującymi je po jednym na raz:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec upuść tabelę tymczasową utworzoną w pierwszym kroku

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).
