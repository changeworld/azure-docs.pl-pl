---
title: Korzystanie z pętli T-SQL
description: Porady dotyczące korzystania z pętli T-SQL i zastępowania kursorów w usłudze Azure SQL Data Warehouse w celu opracowywania rozwiązań.
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351591"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Korzystanie z pętli T-SQL w magazynie danych SQL
Porady dotyczące korzystania z pętli T-SQL i zastępowania kursorów w usłudze Azure SQL Data Warehouse w celu opracowywania rozwiązań.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

Usługa SQL Data Warehouse obsługuje [while](/sql/t-sql/language-elements/while-transact-sql) pętli wielokrotnie wykonywania bloków instrukcji. Ta pętla WHILE jest kontynuowana tak długo, jak długo określone warunki są prawdziwe lub dopóki kod specjalnie kończy pętlę przy użyciu break słowa kluczowego. Pętle są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są napisane w kodzie SQL są szybko do przodu, tylko do odczytu odmiany. W związku z tym [WHILE] pętle są doskonałą alternatywą dla zastępowania kursorów.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Zastępowanie kursorów w magazynie danych SQL
Jednak przed nurkowaniem w głowie najpierw należy zadać sobie następujące pytanie: "Czy ten kursor może być przepisany do korzystania z operacji opartych na zestawach?". W wielu przypadkach odpowiedź brzmi tak i często jest najlepszym podejściem. Operacja oparta na zestawie często wykonuje szybciej niż iteracyjne podejście wiersz po wierszu.

Kursory tylko do odczytu do przodu można łatwo zastąpić konstrukcją pętli. Poniżej znajduje się prosty przykład. W tym przykładzie kodu aktualizuje statystyki dla każdej tabeli w bazie danych. Przez iteracji nad tabelami w pętli, każde polecenie wykonuje w sekwencji.

Najpierw utwórz tabelę tymczasową zawierającą unikatowy numer wiersza używany do identyfikowania poszczególnych instrukcji:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz pętla nad instrukcjami wykonującymi je po jednym na raz:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec upuść tabelę tymczasową utworzoną w pierwszym kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej wskazówek dotyczących rozwoju, zobacz [omówienie rozwoju](sql-data-warehouse-overview-develop.md).

