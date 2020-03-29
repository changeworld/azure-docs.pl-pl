---
title: Arkusz ściągawki zapytania dziennika sql do usługi Azure Monitor | Dokumenty firmy Microsoft
description: Pomoc dla użytkowników zaznajomionych z SQL podczas pisania zapytań dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c76ab145fd2fdd077075b345ecac9c6a473f2369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365193"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Arkusz ściągawki zapytania dziennika sql do usługi Azure Monitor 

Poniższa tabela pomaga użytkownikom, którzy są zaznajomieni z SQL, aby dowiedzieć się języka zapytań Kusto do pisania zapytań dziennika w usłudze Azure Monitor. Zapoznaj się z poleceniem T-SQL w celu rozwiązania typowych scenariuszy i równoważnego w kwerendzie dziennika usługi Azure Monitor.

## <a name="sql-to-azure-monitor"></a>Monitor SQL do platformy Azure

Opis                             |Kwerenda SQL                                                                                          |Zapytanie dziennika usługi Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Zaznaczanie wszystkich danych z tabeli            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Wybieranie określonych kolumn z tabeli    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Wybieranie 100 rekordów z tabeli         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Ocena zerowa                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Porównanie ciągów: równość             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Porównanie ciągów: podciąg            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Porównanie ciągów: symbol wieloznaczny             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Porównanie dat: ostatnie 1 dzień             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Porównanie dat: zakres dat             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Porównanie logiczne                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Sortowanie                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Grupowanie, agregacja                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliasy kolumn, Rozszerzanie                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Najlepsze n rekordy według miary                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Unia                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unia: z warunkami                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Następne kroki

- Przejdź przez lekcje dotyczące [pisania zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).
