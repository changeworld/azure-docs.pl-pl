---
title: Przy użyciu pętli T-SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przy użyciu pętli T-SQL i zastępowanie kursory w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8d51c8f18d7c00d21fcc057efcda73e2a6b46cc7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598969"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Przy użyciu pętli T-SQL w usłudze SQL Data Warehouse
Porady dotyczące przy użyciu pętli T-SQL i zastępowanie kursory w usłudze Azure SQL Data Warehouse związane z opracowywaniem rozwiązań.

## <a name="purpose-of-while-loops"></a>Celem podczas pętle

Magazyn danych SQL obsługuje [podczas](/sql/t-sql/language-elements/while-transact-sql) pętli wielokrotnie wykonywania bloków instrukcji. Pętla WHILE kontynuuje dla tak długo, jak określone warunki są w szczególności wartość PRAWDA lub dopóki kod kończy pętlę za pomocą słowa kluczowego podziału. Pętle są przydatne do zastępowania kursory zdefiniowany w języku SQL. Na szczęście prawie wszystkie kursorów, które zostały napisane w języku SQL są różne do przodu, tylko do odczytu. W związku z tym [pętle są doskonałą alternatywę do zastępowania kursory a].

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Zastępowanie kursory w usłudze SQL Data Warehouse
Jednak przed rozpoczęciem pracy w head należy poprosić samodzielnie następujące zapytania: "można ulegną tego kursora do użycia na podstawie zestawu operations?." W wielu przypadkach odpowiedź tak i jest często najlepszym rozwiązaniem. Na podstawie zestawu często wykonywane szybciej niż przy użyciu metody iteracyjne, wiersz po wierszu.

Przewijanie kursory tylko do odczytu mogą łatwo zastępowane z konstrukcji pętli. Poniżej przedstawiono prosty przykład. Ten przykładowy kod aktualizuje statystyki dla każdej tabeli w bazie danych. Każde polecenie przez Iterowanie po tabel w pętli, wykonuje sekwencji.

Najpierw utwórz tabeli tymczasowej zawierającą liczbę unikatowych wiersza używany do identyfikowania poszczególnych instrukcji:

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

Po drugie zainicjować zmienne wymaganych do wykonania pętli:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz pętli instrukcje wykonywania jedną jednocześnie:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec porzucić tabeli tymczasowej utworzonego w pierwszym kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie tworzenia](sql-data-warehouse-overview-develop.md).

