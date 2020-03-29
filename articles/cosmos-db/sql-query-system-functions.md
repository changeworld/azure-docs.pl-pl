---
title: Funkcje systemowe w języku kwerend usługi Azure Cosmos DB
description: Dowiedz się więcej o wbudowanych i zdefiniowanych przez użytkownika funkcjach systemu SQL w usłudze Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870534"
---
# <a name="system-functions-azure-cosmos-db"></a>Funkcje systemowe (Usługa Azure Cosmos DB)

 Usługa Cosmos DB udostępnia wiele wbudowanych funkcji SQL. Kategorie wbudowanych funkcji są wymienione poniżej.  
  
|Grupa funkcji|Opis|Operacje|  
|--------------|-----------------|-----------------| 
|[Funkcje tablicy](sql-query-array-functions.md)|Funkcje tablicy wykonują operację na wartości wejściowej tablicy i zwracają wartość numeryczną, logiczną lub tablicową. | [ARRAY_CONCAT](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH,](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funkcje daty i godziny](sql-query-date-time-functions.md)|Funkcje daty i godziny umożliwiają uzyskanie bieżącej daty i godziny UTC w dwóch formularzach; numeryczny sygnatura czasowa, którego wartością jest epoka Uniksa w milisekundach lub jako ciąg zgodny z formatem ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Funkcje matematyczne](sql-query-mathematical-functions.md)|Funkcje matematyczne wykonują obliczenia, zwykle oparte na wartościach wejściowych, które są dostarczane jako argumenty, i zwracają wartość liczbową. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [sufit](sql-query-ceiling.md), COS , [łóżeczko](sql-query-cot.md), [stopnie](sql-query-degrees.md), [exp](sql-query-exp.md), [podłoga](sql-query-floor.md), [dziennik](sql-query-log.md), [log10](sql-query-log10.md), [pi](sql-query-pi.md), [moc](sql-query-power.md), [radiany](sql-query-radians.md), [RAND](sql-query-rand.md), [okrągły](sql-query-round.md), [znak](sql-query-sign.md), [grzech](sql-query-sin.md), [sqrt](sql-query-sqrt.md), [kwadrat](sql-query-square.md), [tan](sql-query-tan.md), [trunc](sql-query-trunc.md) [COS](sql-query-cos.md) |
|[Funkcje przestrzenne](sql-query-spatial-functions.md)|Funkcje przestrzenne wykonują operację na wartości wejściowej obiektu przestrzennego i zwracają wartość liczbową lub logiczną. | [ST_DISTANCE](sql-query-st-distance.md) [, ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID,](sql-query-st-isvalid.md) [ST_ISVALIDDETAILED,](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) |
|[Funkcje ciągu](sql-query-string-functions.md)|Funkcje ciągu wykonują operację na wartości wejściowej ciągu i zwracają wartość ciągu, numeryczną lub logiczną. | [CONCAT](sql-query-concat.md), [ZAWIERA](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEWO](sql-query-left.md), [Długość](sql-query-length.md), [DOLNA](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [ZAMIEŃ](sql-query-replace.md), [REPLIKA,](sql-query-replicate.md) [ODWROTNIE](sql-query-reverse.md), [PRAWO](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [PODOBSING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funkcje kontroli typów](sql-query-type-checking-functions.md)|Funkcje kontroli typów pozwalają sprawdzić typ wyrażenia w zapytaniach języka SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL,](sql-query-is-bool.md) [IS_DEFINED,](sql-query-is-defined.md) [IS_NULL,](sql-query-is-null.md) [IS_NUMBER,](sql-query-is-number.md) [IS_OBJECT,](sql-query-is-object.md) [IS_PRIMITIVE,](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Wbudowane funkcje i funkcje zdefiniowane przez użytkownika (UDF)

Jeśli obecnie używasz funkcji zdefiniowanej przez użytkownika (UDF), dla której jest teraz dostępna wbudowana funkcja, odpowiednia wbudowana funkcja będzie szybsza i bardziej wydajna.

## <a name="built-in-versus-ansi-sql-functions"></a>Wbudowane funkcje SQL w porównaniu z ANSI

Główną różnicą między funkcjami usługi Cosmos DB a funkcjami SQL ANSI jest to, że funkcje usługi Cosmos DB są zaprojektowane do pracy z danymi bezkonstrukcji i schematu mieszanego. Na przykład jeśli brakuje właściwości lub ma wartość nieliczbową, taką jak `unknown`, element jest pomijany zamiast zwracania błędu.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Funkcje tablicy](sql-query-array-functions.md)
- [Funkcje daty i godziny](sql-query-date-time-functions.md)
- [Funkcje matematyczne](sql-query-mathematical-functions.md)
- [Funkcje przestrzenne](sql-query-spatial-functions.md)
- [Funkcje ciągu](sql-query-string-functions.md)
- [Funkcje kontroli typów](sql-query-type-checking-functions.md)
- [Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md)
- [Agregacje](sql-query-aggregates.md)
