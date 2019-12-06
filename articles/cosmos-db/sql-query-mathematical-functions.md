---
title: Funkcje matematyczne w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcjach matematycznych w Azure Cosmos DB, aby wykonać obliczenia na podstawie wartości wejściowych, które są podane jako argumenty, i zwrócić wartość liczbową.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873271"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funkcje matematyczne (Azure Cosmos DB)  

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową.

Można uruchamiać zapytania takie jak Poniższy przykład:

```sql
    SELECT VALUE ABS(-4)
```

Wynik:

```json
    [4]
```

## <a name="functions"></a>Functions

Następujące obsługiwane wbudowane funkcje matematyczne wykonują obliczenia, zazwyczaj na podstawie argumentów wejściowych i zwracają wyrażenie liczbowe.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[LIMIT](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[STOPNI](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[DZIENNIK](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[ZASILANIA](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[LOGOWANIE](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[KWADRAT](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC —](sql-query-trunc.md)||  
  
Wszystkie funkcje matematyczne, z wyjątkiem RAND, to funkcje deterministyczne. Oznacza to, że zwracają te same wyniki za każdym razem, gdy są wywoływane przy użyciu określonego zestawu wartości wejściowych.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do Azure Cosmos DB](introduction.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
