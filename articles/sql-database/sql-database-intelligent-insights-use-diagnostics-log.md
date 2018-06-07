---
title: Inteligentnego szczegółowych informacji diagnostycznych dziennika wydajności — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Usługa inteligentnego Insights udostępnia dziennik diagnostyki problemów z wydajnością bazy danych SQL Azure
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: 3d1a9045ed89bd3e5714762add7cb9568c46e44f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648245"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Korzystanie z dziennika inteligentnego bazy danych SQL Azure Insights wydajności diagnostyki

Ta strona zawiera informacje na temat sposobu korzystania z bazy danych SQL Azure wydajności diagnostyki dziennika wygenerowane przez [Insights inteligentnego](sql-database-intelligent-insights.md), jego format, a w nim danych dla programowania niestandardowych wymaga. Możesz wysłać dziennika diagnostyki [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), lub rozwiązania innych firm dla niestandardowych DevOps funkcje alertów i raportów możliwości.

## <a name="log-header"></a>Nagłówek dziennika

Dziennika diagnostyki używa standardowego formatu JSON do wyjściowego ustalenia inteligentnego szczegółowych informacji. Właściwość dokładne kategorii do uzyskiwania dostępu do dziennika inteligentnego wgląd jest wartością stałą "SQLInsights".

Nagłówek dziennika typowe i składa się z sygnaturę czasową (TimeGenerated), która zawiera utworzenia wpisu. Zawiera identyfikator zasobów (ResourceId) odwołuje się do wpisu odnosi się do określonej bazy danych SQL. Kategoria (kategoria), poziom (poziom) i nazwa operacji (OperationName) są stałe wartości, których nie należy zmieniać właściwości. Wskazują, że wpis dziennika ma charakter informacyjny oraz że pochodzą z usługi inteligentnego Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Identyfikator problemu i bazy danych, których to dotyczy

Właściwości identyfikacji problem (issueId_d) umożliwia jednoznacznie śledzenia problemy z wydajnością, dopóki nie są one rozpoznane. Insights inteligentnego przestrzega każdego cyklu życia problem jako "Aktywny", "Sprawdzanie" lub "Ukończone". Kroki poszczególnych faz te stan inteligentnego Insights można zarejestrować wiele rekordów zdarzeń w dzienniku. Dla każdego z tych pozycji numer identyfikacyjny problem pozostanie unikatowy. Insights inteligentnego śledzi problemu przez cały cykl jej życia i generuje szczegółowe dane w dzienniku diagnostyki co 15 minut.

Po wykryciu problemu z wydajnością i dla tak długo, jak długo trwa on problem został zgłoszony jako "Aktywny" w obszarze właściwości stanu (status_s). Po skuteczność została osłabiona wykryty problem, ma zweryfikowane i raportowane jako "Sprawdzanie", w obszarze właściwości stanu (status_s). Jeśli problem nie jest już obecne, właściwość stanu (status_s) raport ten problem jako "Ukończone".

Wraz z Identyfikatorem problem dziennika diagnostyki raporty rozpoczęcia (intervalStartTime_t) i końcowych (intervalEndTme_t) sygnatury czasowe określonego zdarzenia związane z problem, który jest zgłaszany w dzienniku diagnostyki.

Właściwość puli elastycznej (elasticPoolName_s) wskazuje, które puli elastycznej bazy danych problemu należy. Jeśli bazy danych nie jest częścią puli elastycznej, ta właściwość nie ma wartości. Bazy danych, w którym został wykryty problem ujawnić we właściwości name (databaseName_s) bazy danych.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Wykryto problemy

Następna sekcja dziennika wydajności inteligentnego szczegółowe informacje zawiera problemy z wydajnością, które zostały wykryte przez wbudowane narzędzie analizy sztucznego. Wykryć ujawnić właściwości w dzienniku diagnostyki JSON. Wykrywane odmiany składają się z kategorię problemu, wpływ ten problem, zapytania, których to dotyczy i metryki. Właściwości wykryć może zawierać wiele problemów z wydajnością, które zostały wykryte.

Problemy z wydajnością wykryto są raportowane o następującej strukturze właściwości wykryć:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Wzorce wykrywalny wydajność i szczegóły, które są wyjściowych do dzienników diagnostycznych są zawarte w poniższej tabeli.

### <a name="detection-category"></a>Kategoria wykrywania

Właściwość category (kategoria) opisano kategorii wydajności wykrywalny wzorce. Zobacz poniższą tabelę dla wszystkich możliwych kategorii wydajności wykrywalny wzorce. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wydajnością bazy danych z informacjami dotyczącymi inteligentnego](sql-database-intelligent-insights-troubleshoot-performance.md).

W zależności od wydajności wykrytego problemu, szczegóły wyjściowych diagnostyczny pliku dziennika odpowiednio różnią się.

| Wzorce wykrywalny wydajności | Szczegóły wyjściowych |
| :------------------- | ------------------- |
| Osiągnięcia limitów zasobów | <li>Zasoby, których to dotyczy</li><li>Skróty zapytania</li><li>Procent zużycia zasobów</li> |
| Zwiększenie obciążenia | <li>Liczba zapytań, których wykonanie zwiększyć</li><li>Skróty zapytania zapytań z największy wkład zwiększenie obciążenia</li> |
| Wykorzystanie pamięci | <li>Pracownik pamięci.</li> |
| Blokowanie | <li>Wpływ na skróty zapytania</li><li>Blokowanie skrótów zapytania</li> |
| Zwiększona MAXDOP | <li>Skróty zapytania</li><li>Czasy oczekiwania CXP</li><li>Czas oczekiwania</li> |
| Pagelatch rywalizacji | <li>Zapytanie skrótów zapytania powodujące rywalizacji</li> |
| Brak indeksu | <li>Skróty zapytania</li> |
| Nowe zapytanie | <li>Zapytanie skrótu nowej kwerendy</li> |
| Statystyka nietypowe zachowanie podczas oczekiwania | <li>Typy nietypowe zachowanie podczas oczekiwania</li><li>Skróty zapytania</li><li>Czas oczekiwania na zapytania</li> |
| Bazy danych TempDB rywalizacji | <li>Zapytanie skrótów zapytania powodujące rywalizacji</li><li>Przypisanie zapytania do ogólnej bazy danych pagelatch rywalizacji czas oczekiwania [%]</li> |
| Brak jednostek DTU puli elastycznej | <li>Pula elastyczna</li><li>Górny używające jednostek dtu w warstwie bazy danych</li><li>Procent puli jednostek dtu w warstwie używane przez górny konsumenta</li> |
| Planowanie regresji | <li>Skróty zapytania</li><li>Dobry plan identyfikatorów</li><li>Nieprawidłowy plan identyfikatorów</li> |
| Zmiana wartości o zakresie bazy danych konfiguracji | <li>Zmiany w konfiguracji o zakresie bazy danych w porównaniu do wartości domyślnych</li> |
| Powolna klienta | <li>Skróty zapytania</li><li>Czas oczekiwania</li> |
| Obniżenie warstwy cenowej | <li>Tekst powiadomień</li> |

### <a name="impact"></a>Wpływ

Znaczenie (wpływ), że właściwość określa, ile wykryto zachowanie przyczyniły się do problemu, który ma bazy danych. Wpływ na zakresu od 1 do 3, 3 jako najwyższej udział, 2 jako średnią i 1, ponieważ udział najniższy. Wartość wpływu można użyć jako dane wejściowe dla alertów Automatyzacja niestandardowa, w zależności od określonych potrzeb. Zapytania dotyczące właściwości ryzyko (QueryHashes) zawierają listę zapytania wartości skrótu, których dotyczyły określonego wykrywania.

### <a name="impacted-queries"></a>Wpływ na zapytaniach

W następnej sekcji dziennika inteligentnego Insights zawiera informacje o określonego zapytania, których dotyczyły problemów z wydajnością wykrytego. Jako tablica obiektów osadzonych we właściwości impact_s ujawnienia informacji. Właściwość wpływ składa się z jednostek i metryki. Jednostki można znaleźć określonego zapytania (typ: kwerendy). Skrót unikatowy zapytania są ujawniane w obszarze właściwości value (wartość). Ponadto każda kwerenda ujawnione następuje metryki i wartości, które oznaczać problemu z wydajnością wykryte.

W poniższym przykładzie dziennika wykryto zwiększona czas trwania wykonywania zapytania z 0x9102EXZ4 wyznaczania wartości skrótu (metryka: DurationIncreaseSeconds). Wartość sekund 110 wskazuje, że tej kwerendy wykonano już 110 sekund. Ponieważ wiele zapytań mogą być wykrywane, w tej sekcji określonego dziennika może zawierać wiele wpisów zapytania.

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

Jednostki miary dla każdego metryki zgłoszone są przekazywane pod właściwości metryka (metryka) z możliwe wartości sekund, liczbę i procent. Wartość metryki zmierzone jest zgłaszany we właściwości (wartość).

Właściwość DurationIncreaseSeconds zapewnia jednostka miary w sekundach. Jednostki miary CriticalErrorCount jest liczba reprezentująca liczba błędów.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Zalecenia analizy i poprawy Przyczyna głównego

Ostatnia część dziennika wydajności inteligentnego Insights odnosi się do analiza automatycznych głównych przyczyn problemu spadek wydajności zidentyfikowane. Informacje wyświetlane w sposób wyrażania przyjaznych dla człowieka we właściwości głównego Przyczyna analizy (rootCauseAnalysis_s). Zalecenia dotyczące poprawy jakości znajdują się w dzienniku, jeśli jest to możliwe.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Można użyć dziennika wydajności inteligentnego szczegółowych informacji o [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) lub rozwiązanie innej firmy DevOps niestandardowe alerty i raporty dotyczące możliwości.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Insights inteligentnego](sql-database-intelligent-insights.md) pojęcia.
- Dowiedz się, jak [bazy danych SQL Azure Rozwiązywanie problemów z wydajnością z informacjami dotyczącymi inteligentnego](sql-database-intelligent-insights-troubleshoot-performance.md).
- Dowiedz się, jak [monitorować bazy danych SQL Azure za pomocą usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Dowiedz się, jak [zbierania i wykorzystywania danych dziennika z zasobów platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



