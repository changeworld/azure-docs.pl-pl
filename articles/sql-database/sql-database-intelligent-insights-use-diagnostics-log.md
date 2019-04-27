---
title: Intelligent Insights wydajności dziennik diagnostyczny — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Intelligent Insights zawiera dziennik diagnostyczny w usłudze Azure SQL Database problemów z wydajnością
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 264d4cfc6b09813f34501a0e51d3100f4d2bce78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703170"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Korzystanie z dziennika diagnostyki wydajności Intelligent Insights usługi Azure SQL Database

Ta strona zawiera informacje na temat sposobu korzystania z usługi Azure SQL Database dziennika diagnostyki wydajności wygenerowane przez [Intelligent Insights](sql-database-intelligent-insights.md), jego format i danych zawiera potrzeby niestandardowe programowanie. Możesz wysłać ten dziennik diagnostyczny do [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-sql.md), [usługi Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), lub rozwiązań innych firm, dla niestandardowych metodyki DevOps, alertów i raportów możliwości.

## <a name="log-header"></a>Nagłówek dziennika

Dziennik diagnostyczny używa standardowego formatu JSON w danych wyjściowych ustalenia Intelligent Insights. Właściwość dokładnie kategorii do uzyskiwania dostępu do dziennik funkcji Intelligent Insights jest wartością stałą "SQLInsights".

Nagłówek dziennika często i składa się z sygnatura czasowa (TimeGenerated), która pokazuje, kiedy został utworzony wpis. Zawiera on również identyfikator zasobu (identyfikator zasobu), która odwołuje się do wpisu odnosi się do określonej bazy danych SQL. Category (kategoria), poziom (poziom) i nazwa operacji (OperationName) są stałe wartości, których nie należy zmieniać właściwości. Wskazują, że wpis dziennika ma charakter informacyjny i że pochodzi on z Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Identyfikator problemu i bazy danych, których to dotyczy

Właściwości identyfikacji problemu (issueId_d) umożliwia jednoznacznie śledzenia do momentu rozwiązania problemów z wydajnością. Wiele rekordów zdarzeń w dzienniku zgłaszające stan ten sam problem współużytkują ten sam identyfikator problemu.

Wraz z identyfikator problemu dziennik diagnostyczny raporty rozpoczęcia (intervalStartTime_t) i sygnatury czasowe zakończenia (intervalEndTme_t) określonego zdarzenia związane z problemu zgłoszonego w dzienniku diagnostyki.

Właściwości puli elastycznej (elasticPoolName_s) wskazuje, które puli elastycznej bazy danych z problemem, o których należy. Jeśli baza danych nie jest częścią puli elastycznej, ta właściwość nie ma wartości. Bazy danych, w którym wystąpił problem został wykryty są ujawniane we właściwości nazwy (databaseName_s) bazy danych.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Wykryte problemy

Następna sekcja dziennik wydajności Intelligent Insights zawiera problemy z wydajnością, które zostały wykryte za pomocą wbudowanej sztucznej inteligencji. Wykrywanie są udostępniane w oknie właściwości w ramach dziennik diagnostyczny JSON. Wykrywane odmiany składają się z kategorii problemu, wpływ ten problem, zapytania, których dotyczy problem i metryki. Właściwości wykrycia może zawierać wiele problemów z wydajnością, które zostały wykryte.

Problemy z wydajnością wykryte są zgłaszane o następującej strukturze właściwości wykrywania:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Wzorce wydajności wykrywalny i szczegółowe informacje, które są zwrócone do dzienników diagnostycznych znajdują się w poniższej tabeli.

### <a name="detection-category"></a>Kategoria wykrywania

Właściwość category (kategoria) zawiera opis kategorii wydajności wykrywalny wzorców. Zobacz poniższą tabelę dla wszystkich możliwych kategorii wydajności wykrywalny wzorców. Aby uzyskać więcej informacji, zobacz [bazy danych Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md).

W zależności od problem z wydajnością wykryte, szczegółowe informacje zwrócone w diagnostyce pliku dziennika różnią się odpowiednio.

| Wzorce wydajności wykrywalny | Szczegółowe informacje zwrócone |
| :------------------- | ------------------- |
| Osiągnięcia limitów zasobów | <li>Zasoby, których to dotyczy</li><li>Skróty zapytania</li><li>Procent zużycia zasobów</li> |
| Wzrost obciążenia | <li>Liczba zapytań, których wykonanie wzrosła</li><li>Skróty zapytania zapytań za pomocą największy wkład wzrost obciążenia</li> |
| Wykorzystanie pamięci | <li>Pracownik pamięci.</li> |
| Blokowanie | <li>Wpływ na skróty zapytania</li><li>Blokowanie skróty zapytania</li> |
| Zwiększona MAXDOP | <li>Skróty zapytania</li><li>Czasy oczekiwania CXP</li><li>Czas oczekiwania</li> |
| Pagelatch rywalizacji o zasoby | <li>Zapytanie skróty zapytań powoduje rywalizacji o zasoby</li> |
| Brakujący indeks | <li>Skróty zapytania</li> |
| Nowe zapytanie | <li>Zapytania skrótu nowych zapytań</li> |
| Statystyka nietypowe oczekiwania | <li>Typy nietypowe oczekiwania</li><li>Skróty zapytania</li><li>Czas oczekiwania zapytania</li> |
| Bazy danych TempDB rywalizacji o zasoby | <li>Zapytanie skróty zapytań powoduje rywalizacji o zasoby</li><li>Zapytanie: uznanie autorstwa do ogólnej bazy danych pagelatch rywalizacji czasu oczekiwania [%]</li> |
| Mała liczba jednostek DTU w puli elastycznej | <li>Pula elastyczna</li><li>Najważniejsze korzystające z jednostek DTU bazy danych</li><li>Wartość procentowa puli używane przez konsumentów górną jednostek DTU</li> |
| Planowanie regresji | <li>Skróty zapytania</li><li>Dobry plan identyfikatorów</li><li>Nieprawidłowy plan identyfikatorów</li> |
| Zmiana wartości w zakresie bazy danych konfiguracji | <li>Zmiany w zakresie bazy danych konfiguracji w porównaniu do wartości domyślnych</li> |
| Powolne klienta | <li>Skróty zapytania</li><li>Czas oczekiwania</li> |
| Obniżenie warstwy cenowej | <li>Powiadomienie tekstowe</li> |

### <a name="impact"></a>Wpływ

Wpływ (wpływ na), że właściwość określa, ile wykryte zachowanie przyczyniły się do problemu, które występują w bazie danych. Wpływ na środowisko w zakresie od 1 do 3, 3 jako największym wkładem pracy, 2, jak Średni i 1 jako udział najniższy. Wartość wpływu można użyć jako danych wejściowych dla alertów Automatyzacja niestandardowa, w zależności od określonych potrzeb. Zapytania dotyczące właściwości których to dotyczy (QueryHashes) zawierają listę zapytanie skróty, które miały wpływ określonego wykrywania.

### <a name="impacted-queries"></a>Zapytania, na które ma to wpływ

Następna sekcja dziennik Intelligent Insights zawiera informacje dotyczące określonego zapytania, które miały wpływ problemy z wydajnością wykryte. Jako tablica obiektów osadzonych we właściwości impact_s ujawnienia informacji. Właściwość wpływ składa się z jednostek i metryki. Jednostki dotyczą określone zapytanie (typ: Zapytanie). Skrót unikatowy zapytania są ujawniane w obszarze właściwości value (wartość). Ponadto każda kwerenda ujawnione następuje metrykę i wartości, które wskazują problem z wydajnością wykryte.

W poniższym przykładzie dziennika wykryto mają większą czas trwania wykonywania zapytania przez łączenie 0x9102EXZ4 wyznaczania wartości skrótu (metryki: DurationIncreaseSeconds). Wartość sekund 110 wskazuje, że to zapytanie określonego zajęło wykonanie już 110 sekund. Ponieważ wykrycia wielu zapytań w tej sekcji określonego dziennika może zawierać wiele wpisów zapytania.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Metryki

Jednostkę miary dla każdego metryki zgłaszane jest udostępniony w ramach właściwości metryki (metryczne) możliwych wartości w ciągu kilku sekund, liczba i procent. Wartość mierzone metryki jest zgłaszany w właściwości value (wartość).

Właściwość DurationIncreaseSeconds zapewnia jednostka miary w ciągu kilku sekund. CriticalErrorCount jednostka miary jest liczbą, która reprezentuje liczby błędów.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Zalecenia analizy i poprawy jakości obsługi przyczyny głównej

Ostatnia część dziennika wydajności Intelligent Insights odnoszą się do analizy automatycznych głównych przyczyn zidentyfikowanego problemu z wydajnością obniżenie wydajności. Informacje wyświetlane w sposób wyrażania przyjaznego dla człowieka, we właściwości analysis (rootCauseAnalysis_s) przyczyn głównych. Zalecenia dotyczące poprawy znajdują się w dzienniku, jeśli jest to możliwe.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Można użyć dziennika wydajności Intelligent Insights za pomocą [dzienniki usługi Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) lub rozwiązań innych firm, dla niestandardowych metodyki DevOps, alertów i możliwości raportowania.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Intelligent Insights](sql-database-intelligent-insights.md) pojęcia.
- Dowiedz się, jak [usługi Azure SQL Database Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md).
- Dowiedz się, jak [monitorowania usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Dowiedz się, jak [zbieranie i używanie dane dzienników z zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



