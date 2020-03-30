---
title: Dziennik diagnostyki wydajności intelligent insights
description: Usługa Intelligent Insights udostępnia dziennik diagnostyczny problemów z wydajnością usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bb62b087451140261aee7aaa2fab0de14ea36283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209446"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Korzystanie z dziennika diagnostyki wydajności usługi Intelligent Insights Azure SQL Database

Ta strona zawiera informacje na temat korzystania z dziennika diagnostyki wydajności usługi Azure SQL Database generowane przez [intelligent insights](sql-database-intelligent-insights.md), jego format i dane, które zawiera dla potrzeb niestandardowych programistyczne. Ten dziennik diagnostyki można wysłać do [dzienników usługi Azure Monitor,](../azure-monitor/insights/azure-sql.md) [usługi Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)lub rozwiązania innej firmy dla niestandardowych funkcji alertów i raportowania DevOps.

## <a name="log-header"></a>Nagłówek dziennika

Dziennik diagnostyki używa standardowego formatu JSON do wyprowadzania wyników intelligent insights. Właściwość dokładnej kategorii dostępu do dziennika inteligentnej usługi Insights jest stałą wartością "SQLInsights".

Nagłówek dziennika jest wspólne i składa się z sygnatury czasowej (TimeGenerated), który pokazuje, kiedy wpis został utworzony. Zawiera również identyfikator zasobu (ResourceId), który odwołuje się do określonej bazy danych SQL, której dotyczy wpis. Kategoria (kategoria), poziom (poziom) i nazwa operacji (OperationName) są stałymi właściwościami, których wartości nie zmieniają się. Wskazują one, że wpis dziennika jest informacyjny i że pochodzi z intelligent insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Identyfikator problemu i baza danych, których dotyczy problem

Właściwość identyfikacji problemu (issueId_d) umożliwia jednoznaczne śledzenie problemów z wydajnością do czasu ich rozwiązania. Wiele rekordów zdarzeń w stanie raportowania dziennika tego samego problemu będzie współużytkować ten sam identyfikator problemu.

Wraz z identyfikatorem problemu dziennik diagnostyki zgłasza początkowe (intervalStartTime_t) i końcowe (intervalEndTme_t) sygnatury czasowe określonego zdarzenia związane z problemem, który jest zgłaszany w dzienniku diagnostyki.

Właściwość puli elastycznej (elasticPoolName_s) wskazuje, do której puli elastycznej należy baza danych z problemem. Jeśli baza danych nie jest częścią puli elastycznej, ta właściwość nie ma wartości. Baza danych, w której wykryto problem, jest ujawniana we właściwości nazwa bazy danych (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Wykryte problemy

Następna sekcja dziennika wydajności inteligentnej analizy zawiera problemy z wydajnością, które zostały wykryte za pomocą wbudowanej sztucznej inteligencji. Wykrywania są ujawniane we właściwościach w dzienniku diagnostyki JSON. Te wykrywania składają się z kategorii problemu, wpływ problemu, kwerendy, których dotyczy problem i metryki. Właściwości wykrywania mogą zawierać wiele wykrytych problemów z wydajnością.

Wykryte problemy z wydajnością są zgłaszane z następującą strukturą właściwości wykrywania:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Wykrywalne wzorce wydajności i szczegóły, które są wyprowadzane do dziennika diagnostyki są dostępne w poniższej tabeli.

### <a name="detection-category"></a>Kategoria wykrywania

Właściwość kategoria (kategoria) opisuje kategorię wykrywalnych wzorców wydajności. Zobacz poniższą tabelę dla wszystkich możliwych kategorii wykrywalnych wzorców wydajności. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wydajnością bazy danych za pomocą aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

W zależności od wykrytego problemu z wydajnością szczegóły wyprowadzane w pliku dziennika diagnostyki różnią się odpowiednio.

| Wykrywalne wzorce wydajności | Szczegóły wysuwane |
| :------------------- | ------------------- |
| Osiąganie limitów zasobów | <li>Zasoby, których dotyczy problem</li><li>Skróty kwerendy</li><li>Procent zużycia zasobów</li> |
| Zwiększenie obciążenia pracą | <li>Liczba zapytań, których wykonanie wzrosło</li><li>Skróty zapytań z największym udziałem w zwiększeniu obciążenia</li> |
| Wykorzystanie pamięci | <li>Urzędnik pamięci</li> |
| Blokowanie | <li>Skróty kwerendy, których dotyczy problem</li><li>Blokowanie skrótów kwerendy</li> |
| Zwiększona wartość MAXDOP | <li>Skróty kwerendy</li><li>Czas oczekiwania CXP</li><li>Czas oczekiwania</li> |
| Rywalizacja pagelatch | <li>Skróty kwerendy powodujące rywalizację</li> |
| Brakujący indeks | <li>Skróty kwerendy</li> |
| Nowe zapytanie | <li>Skrót kwerendy nowych kwerend</li> |
| Niezwykła statystyka oczekiwania | <li>Nietypowe typy oczekiwania</li><li>Skróty kwerendy</li><li>Czas oczekiwania na kwerendę</li> |
| Rywalizacja o tempdb | <li>Skróty kwerendy powodujące rywalizację</li><li>Atrybucja kwerendy do ogólnego czasu oczekiwania rywalizacji o pagelatch bazy danych [%]</li> |
| Niedobór puli elastycznej DTU | <li>Elastyczna pula</li><li>Najlepsza baza danych zużywająca wyniki DTU</li><li>Procent puli DTU używanej przez najlepszego konsumenta</li> |
| Regresj planu | <li>Skróty kwerendy</li><li>Dobre identyfikatory planu</li><li>Nieprawidłowe identyfikatory planu</li> |
| Zmiana wartości konfiguracji o zakresie bazy danych | <li>Zmiany konfiguracji o zakresie bazy danych w porównaniu z wartościami domyślnymi</li> |
| Powolny klient | <li>Skróty kwerendy</li><li>Czas oczekiwania</li> |
| Obniżenie poziomu cen | <li>Powiadomienie tekstowe</li> |

### <a name="impact"></a>Wpływ

Właściwość wpływ (wpływ) opisuje, ile wykryte zachowanie przyczyniło się do problemu, który ma bazy danych. Wpływy wahają się od 1 do 3, z 3 jako najwyższy wkład, 2 jako umiarkowane i 1 jako najniższa składka. Wartość wpływu może służyć jako dane wejściowe dla automatyzacji alertów niestandardowych, w zależności od konkretnych potrzeb. Zapytania właściwości wpływ (QueryHashes) zapewniają listę skrótów kwerendy, które zostały naruszone przez określonego wykrywania.

### <a name="impacted-queries"></a>Zapytania, których dotyczyć

Następna sekcja dziennika inteligentnej usługi Insights zawiera informacje o określonych zapytaniach, których dotyczą wykryte problemy z wydajnością. Te informacje są ujawniane jako tablica obiektów osadzonych we właściwości impact_s. Właściwość impact składa się z jednostek i metryk. Jednostki odnoszą się do określonej kwerendy (Typ: Kwerenda). Unikatowy skrót kwerendy jest ujawniany w ramach właściwości value (Value). Ponadto po każdym z ujawnionych zapytań następuje metryka i wartość, które wskazują na wykryty problem z wydajnością.

W poniższym przykładzie dziennika wykryto kwerendę z hash 0x9102EXZ4, aby mieć zwiększony czas trwania wykonywania (Metric: DurationIncreaseSeconds). Wartość 110 sekund wskazuje, że wykonanie tej konkretnej kwerendy trwało 110 sekund dłużej. Ponieważ można wykryć wiele zapytań, ta określona sekcja dziennika może zawierać wiele wpisów kwerendy.

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

Jednostka miary dla każdej zgłoszonej metryki jest dostarczana w ramach właściwości metryka (metryka) z możliwymi wartościami sekund, liczby i wartości procentowej. Wartość zmierzonej metryki jest raportowana we właściwości value (value).

Właściwość DurationIncreaseSeconds zapewnia jednostkę miary w sekundach. CriticalErrorCount jednostki miary jest liczba, która reprezentuje liczbę błędów.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Zalecenia dotyczące analizy przyczyn źródłowych i ulepszeń

Ostatnia część dziennika wydajności inteligentnej analizy aplikacji dotyczy zautomatyzowanej analizy głównej przyczyny zidentyfikowanego problemu z obniżeniem wydajności. Informacje pojawiają się w słownictwa przyjaznego dla człowieka w właściwości analizy głównej przyczyny (rootCauseAnalysis_s). Zalecenia dotyczące poprawy są zawarte w dzienniku, jeśli to możliwe.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Dziennik wydajności intelligent insights można używać za pomocą [dzienników usługi Azure Monitor]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) lub rozwiązania innej firmy dla niestandardowych funkcji alertów i raportowania DevOps.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o pojęciach [dotyczących inteligentnej analizy.](sql-database-intelligent-insights.md)
- Dowiedz się, jak [rozwiązywać problemy z wydajnością usługi Azure SQL Database za pomocą aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Dowiedz się, jak [monitorować usługę Azure SQL Database przy użyciu usługi Azure SQL Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)
- Dowiedz się, jak [zbierać i wykorzystywać dane dziennika z zasobów platformy Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
