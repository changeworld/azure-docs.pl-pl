---
title: Przydatne operatory w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Typowe funkcje używane dla różnych scenariuszy w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397720"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Przydatne operatory w kwerendach dziennika usługi Azure Monitor

W poniższej tabeli przedstawiono niektóre typowe funkcje do użycia w różnych scenariuszach w kwerendach dziennika usługi Azure Monitor.

## <a name="useful-operators"></a>Przydatne operatory

Kategoria                                |Odpowiednia funkcja analityczna
----------------------------------------|----------------------------------------
Aliasy zaznaczenia i kolumny            |`project`, `project-away`, `extend`
Tabele tymczasowe i stałe          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Operatory porównania i ciągów         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Typowe funkcje ciągu                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Typowe funkcje matematyczne                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analizowanie tekstu                            |`extract()`, `extractjson()`, `parse`, `split()`
Ograniczenie produkcji                         |`take`, `limit`, `top`, `sample`
Funkcje daty                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Grupowanie i agregacja                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Przyłącza się i związki zawodowe                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sortowanie, porządkowanie                             |`sort`, `order` 
Obiekt dynamiczny (JSON i tablica)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operatory logiczne                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Uczenie maszynowe                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Następne kroki

- Przejdź przez lekcję na [temat pisania zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).
