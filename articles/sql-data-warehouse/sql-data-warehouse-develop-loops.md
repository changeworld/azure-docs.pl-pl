---
title: Użycie pętli języka T-SQL w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Porady dotyczące przy użyciu języka T-SQL, pętli i zastępowanie kursory w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5aa26aeb27d962e6e6289a754ef57b49158b68db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439200"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Użycie pętli języka T-SQL w usłudze SQL Data Warehouse
Porady dotyczące przy użyciu języka T-SQL, pętli i zastępowanie kursory w usłudze Azure SQL Data Warehouse do opracowywania rozwiązań.

## <a name="purpose-of-while-loops"></a>Celem pętli

Usługa SQL Data Warehouse obsługuje [podczas](/sql/t-sql/language-elements/while-transact-sql) pętli wielokrotnego wykonania bloków instrukcji. Ta pętla WHILE składa się z tak długo, jak określone warunki zostały specjalnie wartość PRAWDA lub do momentu kod kończy pętli za pomocą słowa kluczowego podziału. Pętle są przydatne do zastępowania kursory zdefiniowany w języku SQL. Na szczęście prawie wszystkie kursorów, które zostały napisane w języku SQL są różne do przodu, tylko do odczytu. W związku z tym [a] pętli to doskonała alternatywa dla zastępowanie kursorów.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Zastępowanie kursory w usłudze SQL Data Warehouse
Jednak przed zagłębieniem się w head należy zapytać samodzielnie następujące pytania: "Można dopasować ten kursor do użycia na podstawie zestawu operations?." W wielu przypadkach odpowiedź jest tak i często najlepszym rozwiązaniem. Na podstawie zestawu często są wykonywane szybciej niż podejście iteracyjną, wiersz po wierszu.

Do przodu kursory tylko do odczytu można łatwo zastąpić z konstrukcji pętli. Poniżej przedstawiono prosty przykład. Ten przykładowy kod aktualizuje statystyki dla każdej tabeli w bazie danych. Przez Iterowanie tabel w pętli, każde polecenie wykonuje w sekwencji.

Najpierw utwórz tabelę tymczasową zawierającą liczbę unikatowych wierszach, używany do identyfikowania poszczególnych instrukcji:

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

Po drugie zainicjować zmienne wymagane do wykonywania pętli:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz pętli wykonanie jednej instrukcji w czasie:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec usunąć tabelę tymczasową utworzonego w pierwszym kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [omówienie programowania w usłudze](sql-data-warehouse-overview-develop.md).

