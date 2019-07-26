---
title: Używanie pętli T-SQL w Azure SQL Data Warehouse | Microsoft Docs
description: Porady dotyczące używania pętli T-SQL i zastępowania kursorów w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e27edcc1383a235fbdb9513066e69e2f680ea2f9
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479622"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Używanie pętli T-SQL w SQL Data Warehouse
Porady dotyczące używania pętli T-SQL i zastępowania kursorów w Azure SQL Data Warehouse na potrzeby tworzenia rozwiązań.

## <a name="purpose-of-while-loops"></a>Przeznaczenie pętli WHILE

SQL Data Warehouse obsługuje pętlę [while](/sql/t-sql/language-elements/while-transact-sql) do wielokrotnego wykonywania bloków instrukcji. Ta Pętla WHILE działa tak długo, jak określone warunki są prawdziwe lub do momentu, aż kod zakończy pętlę przy użyciu słowa kluczowego BREAK. Pętle są przydatne do zastępowania kursorów zdefiniowanych w kodzie SQL. Na szczęście prawie wszystkie kursory, które są zapisywane w kodzie SQL, są do przodu, tylko do odczytu. W związku z tym, pętle [WHILE] są świetną alternatywą dla zastępowania kursorów.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Zastępowanie kursorów w SQL Data Warehouse
Jednak przed przystąpieniem do głowy najpierw należy zadać sobie następujące pytanie: "Czy ten kursor można napisać ponownie, aby użyć operacji opartych na zestawie?". W wielu przypadkach odpowiedź ma wartość yes i jest często najlepszym rozwiązaniem. Operacja oparta na zestawie często wykonuje się szybciej niż iteracyjne, sekwencyjne wiersz po wierszu.

Szybkie przewijanie kursorów tylko do odczytu można łatwo zastąpić konstrukcja zapętlenia. Poniżej przedstawiono prosty przykład. Ten przykład kodu aktualizuje statystyki dla każdej tabeli w bazie danych. Przez iterację w tabelach w pętli, każde polecenie wykonuje sekwencję.

Najpierw utwórz tabelę tymczasową zawierającą unikatowy numer wiersza służący do identyfikowania poszczególnych instrukcji:

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

Następnie zainicjuj zmienne wymagane do wykonania pętli:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Teraz instrukcje pętli over wykonujeją je pojedynczo:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Na koniec Usuń tabelę tymczasową utworzoną w pierwszym kroku

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).

