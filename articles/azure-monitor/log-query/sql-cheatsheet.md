---
title: SQL do ściągawka dotycząca usługi Azure Monitor dziennika zapytań | Dokumentacja firmy Microsoft
description: Pomoc dla użytkowników, które już znasz z usług SQL Pisanie zapytań dzienników w usłudze Azure Monitor.
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
ms.openlocfilehash: a0233774deaffe25a8e59f79511a0031b1535ba4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425030"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>SQL do ściągawka dotycząca usługi Azure Monitor dziennika zapytań 

W poniższej tabeli ułatwiają użytkownikom, którzy są zaznajomieni z językiem SQL, aby dowiedzieć się, język zapytania Kusto pisania zapytań dzienników w usłudze Azure Monitor. Ma wygląd w wierszu polecenia języka T-SQL w rozwiązywaniu typowych scenariuszy i odpowiedniej wartości wyrażonej w zapytanie dziennika usługi Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL do usługi Azure Monitor

Opis                             |SQL Query                                                                                          |Zapytanie dziennika w usłudze Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Zaznacz wszystkie dane z tabeli            |`SELECT * FROM dependencies`                                                                       |<code>dependencies</code>
Wybrać określone kolumny z tabeli    |`SELECT name, resultCode FROM dependencies`                                                        |<code>dependencies <br>&#124; project name, resultCode</code>
Wybierz 100 rekordów z tabeli         |`SELECT TOP 100 * FROM dependencies`                                                               |<code>dependencies <br>&#124; take 100</code>
Obliczanie wartości null                         |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL`                                          |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Ciąg porównania: równości             |`SELECT * FROM dependencies WHERE name = "abcde"`                                                  |<code>dependencies <br>&#124; where name == "abcde"</code>
Ciąg porównania: podciąg            |`SELECT * FROM dependencies WHERE name like "%bcd%"`                                                   |<code>dependencies <br>&#124; where name contains "bcd"</code>
Ciąg porównania: symbol wieloznaczny             |`SELECT * FROM dependencies WHERE name like "abc%"`                                                |<code>dependencies <br>&#124; where name startswith "abc"</code>
Porównanie dat: ostatni dzień             |`SELECT * FROM dependencies WHERE timestamp > getdate()-1`                                         |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Porównanie dat: zakres dat             |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'`                 |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Wartość logiczna porównania                      |`SELECT * FROM dependencies WHERE !(success)`                                                      |<code>dependencies <br>&#124; where success == "False" </code>
Sortowanie                                    |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc`                                  |<code>dependencies <br>&#124; order by timestamp asc </code>
Odrębne                                |`SELECT DISTINCT name, type  FROM dependencies`                                                    |<code>dependencies <br>&#124; summarize by name, type </code>
Grupowanie i agregacji                   |`SELECT name, AVG(duration) FROM dependencies GROUP BY name`                                       |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliasów kolumn, rozszerzenie                  |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name`             |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Pierwszych n rekordów miary                |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc`        |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Unia                                   |`SELECT * FROM dependencies UNION SELECT * FROM exceptions`                                        |<code>union dependencies, exceptions</code>
Unia: z warunkami                  |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5`                |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join                                    |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Kolejne kroki

- Przejdź przez lekcji [Pisanie zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).
