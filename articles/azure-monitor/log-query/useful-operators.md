---
title: Operatory przydatne w usłudze Azure Monitor rejestrowania zapytań | Dokumentacja firmy Microsoft
description: Typowe funkcje na potrzeby różnych scenariuszy, w usłudze Azure Monitor rejestrowania zapytań.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424142"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Operatory przydatne w zapytaniach dzienników usługi Azure Monitor

W poniższej tabeli przedstawiono niektóre typowe funkcje na potrzeby różnych scenariuszy w zapytaniach dzienników usługi Azure Monitor.

## <a name="useful-operators"></a>Przydatne operatory

Category                                |Funkcja analizy odpowiednie
----------------------------------------|----------------------------------------
Wybór i kolumny aliasów            |`project`, `project-away`, `extend`
Tabele tymczasowe i stałe          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Porównywanie i operatory ciągów         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Typowe funkcje ciągów                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Typowe funkcje matematyczne                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analiza tekstu                            |`extract()`, `extractjson()`, `parse`, `split()`
Ograniczanie danych wyjściowych                         |`take`, `limit`, `top`, `sample`
Funkcje daty                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Grupowanie i agregacji                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Sprzężenia i unie                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sortowania, kolejność                             |`sort`, `order` 
Obiekt dynamiczny (JSON i array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operatory logiczne                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Uczenie maszynowe                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Kolejne kroki

- Przejdź przez lekcji [Pisanie zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).
