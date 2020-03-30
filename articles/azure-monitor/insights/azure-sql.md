---
title: Rozwiązanie usługi Azure SQL Analytics w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Rozwiązanie usługi Azure SQL Analytics ułatwia zarządzanie bazami danych SQL platformy Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275466"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorowanie bazy danych SQL azure przy użyciu usługi Azure SQL Analytics (Wersja zapoznawcza)

![Symbol usługi Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Usługa Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmury do monitorowania wydajności wszystkich baz danych SQL platformy Azure na dużą skalę i w wielu subskrypcjach w jednym widoku. Usługa Azure SQL Analytics zbiera i wizualizuje kluczowe metryki wydajności za pomocą wbudowanej analizy rozwiązywania problemów z wydajnością.

Korzystając z tych zebranych metryk, można utworzyć niestandardowe reguły monitorowania i alerty. Usługa Azure SQL Analytics ułatwia identyfikowanie problemów w każdej warstwie stosu aplikacji. Używa metryki diagnostyki platformy Azure wraz z widokami usługi Azure Monitor do prezentowania danych dotyczących wszystkich baz danych SQL platformy Azure w jednym obszarze roboczym usługi Log Analytics. Usługa Azure Monitor ułatwia zbieranie, korelowanie i wizualizowanie danych strukturalnych i nieustrukturyzowanych.

Aby zapoznać się z praktycznym omówieniem korzystania z rozwiązania usługi Azure SQL Analytics i typowych scenariuszy użycia, zobacz osadzony klip wideo:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Połączone źródła

Usługa Azure SQL Analytics to rozwiązanie do monitorowania tylko w chmurze obsługujące przesyłanie strumieniowe danych telemetrycznych diagnostyki dla wszystkich baz danych SQL platformy Azure. Ponieważ usługa Azure SQL Analytics nie używa agentów do łączenia się z usługą Azure Monitor, nie obsługuje monitorowania programu SQL Server hostowanego lokalnie ani na maszynach wirtualnych.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Ustawienia diagnostyki](../platform/diagnostic-settings.md) | **Tak** | Dane metryki i dzienniki platformy Azure są wysyłane do dzienników usługi Azure Monitor bezpośrednio przez platformę Azure. |
| [Konto magazynu platformy Azure](../platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Monitor nie odczytuje danych z konta magazynu. |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Nie | Bezpośredni agenci systemu Windows nie są używane przez usługę Azure SQL Analytics. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Bezpośredni agenci systemu Linux nie są używane przez usługę Azure SQL Analytics. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Nie | Bezpośrednie połączenie z agentem programu Operations Manager z usługą Azure Monitor nie jest używane przez usługę Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opcje usługi Azure SQL Analytics

W poniższej tabeli przedstawiono obsługiwane opcje dla dwóch wersji pulpitu nawigacyjnego usługi Azure SQL Analytics, jednej dla pojedynczych i puli baz danych i pul elastycznych, a drugiej dla zarządzanych wystąpień i baz danych wystąpień.

| Opcja usługi Azure SQL Analytics | Opis | Obsługa pojedynczej i puli baz danych i pul elastycznych | Obsługa bazy danych wystąpień zarządzanych i wystąpień |
| --- | ------- | ----- | ----- |
| Zasób według typu | Perspektywa, która zlicza wszystkie monitorowane zasoby. | Tak | Tak |
| Insights | Zapewnia hierarchiczne przechodzenie do szczegółów inteligentnego wglądu w wydajność. | Tak | Tak |
| Errors | Zawiera hierarchiczne przechodzenie do szczegółów błędów SQL, które miały miejsce w bazach danych. | Tak | Tak |
| Limity czasu | Zapewnia hierarchiczne przechodzenie do szczegółów w dół do limitów czasu SQL, które miały miejsce w bazach danych. | Tak | Nie |
| Blokady | Zapewnia hierarchiczne przechodzenie do szczegółów w dół do blokowania SQL, które miały miejsce w bazach danych. | Tak | Nie |
| Baza danych czeka | Zawiera hierarchiczne przechodzenie do szczegółów statystyk oczekiwania SQL na poziomie bazy danych. Zawiera podsumowania całkowitego czasu oczekiwania i czasu oczekiwania według typu oczekiwania. |Tak | Nie |
| Czas trwania kwerendy | Zapewnia hierarchiczne przechodzenie do szczegółów statystyk wykonywania kwerend, takich jak czas trwania kwerendy, użycie procesora CPU, użycie we/wy danych, użycie we/wy dziennika. | Tak | Tak |
| Query waits (Czas oczekiwania na zapytania) | Zawiera hierarchiczne przechodzenie do szczegółów statystyk oczekiwania zapytania według kategorii oczekiwania. | Tak | Tak |

## <a name="configuration"></a>Konfigurowanie

Użyj procesu opisanego w [add Azure Monitor solutions from the Solutions Gallery,](../../azure-monitor/insights/solutions.md) aby dodać usługę Azure SQL Analytics (Preview) do obszaru roboczego usługi Log Analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurowanie baz danych SQL platformy Azure do przesyłania strumieniowego danych telemetrycznych diagnostyki

Po utworzeniu rozwiązania usługi Azure SQL Analytics w obszarze roboczym należy **skonfigurować każdy** zasób, który chcesz monitorować, aby przesyłać strumieniowo jego dane telemetryczne diagnostyczne do usługi Azure SQL Analytics. Postępuj zgodnie ze szczegółowymi instrukcjami na tej stronie:

- Włącz diagnostykę platformy Azure dla bazy danych SQL platformy Azure, aby [przesyłać strumieniowo dane telemetryczne diagnostyki do usługi Azure SQL Analytics.](../../sql-database/sql-database-metrics-diag-logging.md)

Powyższa strona zawiera również instrukcje dotyczące włączania obsługi wielu subskrypcji platformy Azure z jednego obszaru roboczego usługi Azure SQL Analytics jako pojedynczego okienka szkła.

## <a name="using-azure-sql-analytics"></a>Korzystanie z usługi Azure SQL Analytics

Po dodaniu usługi Azure SQL Analytics do obszaru roboczego kafelka usługi Azure SQL Analytics jest dodawany do obszaru roboczego i pojawia się w przeglądzie. Wybierz pozycję Wyświetl łącze Podsumowanie, aby załadować zawartość kafelka.

![Kafelek podsumowania usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po załadowaniu kafelek pokazuje liczbę pojedynczych i puli baz danych, pul elastycznych, wystąpień zarządzanych i zarządzanych baz danych wystąpień, z których usługa Azure SQL Analytics otrzymuje dane telemetryczne diagnostyki.

![Kafelek usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Usługa Azure SQL Analytics udostępnia dwa oddzielne widoki — jeden do monitorowania pojedynczych baz danych i puli baz danych i pul elastycznych, a drugi do monitorowania wystąpień zarządzanych i baz danych wystąpień.

Aby wyświetlić pulpit nawigacyjny monitorowania usługi Azure SQL Analytics dla pojedynczych i pul danych i pul elastycznych, kliknij górną część kafelka. Aby wyświetlić pulpit nawigacyjny monitorowania usługi Azure SQL Analytics dla wystąpień zarządzanych i baz danych wystąpień, kliknij dolną część kafelka.

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych usługi Azure SQL Analytics

Pulpit nawigacyjny zawiera omówienie wszystkich baz danych, które są monitorowane za pomocą różnych perspektyw. Aby różne perspektywy do pracy, należy włączyć odpowiednie metryki lub dzienniki zasobów SQL, które mają być przesyłane strumieniowo do obszaru roboczego usługi Log Analytics.

Jeśli niektóre metryki lub dzienniki nie są przesyłane strumieniowo do usługi Azure Monitor, kafelki w usłudze Azure SQL Analytics nie są wypełniane informacjami o monitorowaniu.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Pojedyncze i połączone bazy danych oraz widok pul elastycznych

Po wybraniu kafelka usługi Azure SQL Analytics dla bazy danych wyświetlany jest pulpit nawigacyjny monitorowania.

![Omówienie usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Wybierając dowolny z kafelków, otwiera raport drążenia w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport drążenia.

![Limity czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Każda perspektywa w tym widoku zawiera podsumowania na poziomie subskrypcji, serwera, puli elastycznej i bazy danych. Ponadto każda perspektywa pokazuje perspektywę specyficzną dla raportu po prawej stronie. Wybranie subskrypcji, serwera, puli lub bazy danych z listy jest kontynuowane w dół.

### <a name="managed-instance-and-instances-databases-view"></a>Widok baz danych wystąpienia zarządzanego i wystąpień

Po wybraniu kafelka usługi Azure SQL Analytics dla baz danych zostanie wyświetlony pulpit nawigacyjny monitorowania.

![Omówienie usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Wybierając dowolny z kafelków, otwiera raport drążenia w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport drążenia.

Wybranie widoku wystąpienia zarządzanego, pokazuje szczegóły dotyczące wykorzystania wystąpienia zarządzanego, baz danych, które zawiera, i danych telemetrycznych na kwerendy wykonywane w całym wystąpieniu.

![Limity czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Raport Intelligent Insights

Usługa Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) informuje o tym, co dzieje się z wydajnością wszystkich baz danych SQL platformy Azure. Wszystkie zebrane inteligentne informacje mogą być wizualizowane i dostępne za pośrednictwem punktu widzenia aplikacji Insights.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pule elastyczne i raporty bazy danych

Pule elastyczne i bazy danych mają własne określone raporty, które pokazują wszystkie dane, które są zbierane dla zasobu w określonym czasie.

![Baza danych usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Pula elastyczna SQL platformy Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty kwerend

Za pośrednictwem czasu trwania kwerendy i kwerendy czeka perspektywy, można skorelować wydajność dowolnej kwerendy za pośrednictwem raportu kwerendy. Ten raport porównuje wydajność kwerendy w różnych bazach danych i ułatwia określenie baz danych, które wykonują wybrane zapytanie dobrze w porównaniu z tymi, które są powolne.

![Zapytania usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Uprawnienia

Aby korzystać z usługi Azure SQL Analytics, użytkownicy muszą mieć minimalne uprawnienia roli czytelnika na platformie Azure. Ta rola nie zezwala jednak użytkownikom na wyświetlanie tekstu kwerendy ani wykonywanie żadnych akcji automatycznego dostrajania. Bardziej permisywne role na platformie Azure, które umożliwiają korzystanie z usługi Azure SQL Analytics w najszerszym zakresie, to Właściciel, Współautor, Współautor bazy danych SQL lub WSPÓŁAUTOR PROGRAMU SQL Server. Można również rozważyć utworzenie roli niestandardowej w portalu z określonymi uprawnieniami wymaganymi tylko do korzystania z usługi Azure SQL Analytics i bez dostępu do zarządzania innymi zasobami.

### <a name="creating-a-custom-role-in-portal"></a>Tworzenie roli niestandardowej w portalu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uznając, że niektóre organizacje wymuszają ścisłe kontrolki uprawnień na platformie Azure, znajdź następujący skrypt programu PowerShell umożliwiający utworzenie niestandardowej roli "Operator monitorowania analizy SQL" w witrynie Azure portal z minimalnymi uprawnieniami do odczytu i zapisu wymaganymi do usługi Azure SQL Analytics w najszerszym zakresie.

Zastąp "{SubscriptionId}" w poniższym skrypcie identyfikatorem subskrypcji platformy Azure i wykonaj skrypt zalogowany jako rola właściciela lub współautora na platformie Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Po utworzeniu nowej roli przypisz tę rolę każdemu użytkownikowi, który musi udzielić uprawnień niestandardowych do korzystania z usługi Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenie alertów

Analiza danych w usłudze Azure SQL Analytics jest [oparta](../log-query/get-started-queries.md) na języku usługi Log Analytics dla niestandardowych zapytań i raportowania. Znajdź opis dostępnych danych zebranych z zasobu bazy danych do wykonywania zapytań niestandardowych w [dostępnych metrykach i dziennikach](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatyczne alerty w usłudze Azure SQL Analytics jest na podstawie pisania kwerendy usługi Log Analytics, która wyzwala alert po spełnieniu warunku. Poniżej znajduje się kilka przykładów zapytań usługi Log Analytics, na których można skonfigurować alerty w usłudze Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Tworzenie alertów dla bazy danych SQL usługi Azure

Alerty można łatwo [tworzyć](../platform/alerts-metric.md) z danymi pochodzącymi z zasobów usługi Azure SQL Database. Oto kilka przydatnych [zapytań dziennika,](../log-query/log-query-overview.md) których można używać z alertem dziennika:

#### <a name="high-cpu-on-azure-sql-database"></a>Wysoki procesor w bazie danych SQL platformy Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu polega na tym, że monitorowane bazy danych przesyłają strumieniowo podstawowe metryki do usługi Azure SQL Analytics.
> - Zastąp wartość MetricName cpu_percent dtu_consumption_percent, aby uzyskać wysokie wyniki DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Wysoka wydajność procesora w elastycznych pulach usługi Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu polega na tym, że monitorowane bazy danych przesyłają strumieniowo metryki podstawowe do usługi Azure SQL Analytics.
> - Zastąp wartość MetricName cpu_percent dtu_consumption_percent, aby uzyskać wysokie wyniki DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Magazyn bazy danych SQL azure średnio powyżej 95% w ciągu ostatnich 1 godz.

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu polega na tym, że monitorowane bazy danych przesyłają strumieniowo podstawowe metryki do usługi Azure SQL Analytics.
> - Ta kwerenda wymaga reguły alertu, aby skonfigurować w celu wypalania alertu, gdy istnieją wyniki (> 0 wyników) z kwerendy, oznaczając, że warunek istnieje w niektórych bazach danych. Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w time_range zdefiniowanych.
> - Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w time_range zdefiniowanych.

#### <a name="alert-on-intelligent-insights"></a>Alerty dotyczące inteligentnych spostrzeżeń

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu jest, że monitorowane bazy danych strumienia SQLInsights diagnostyki dziennika do usługi Azure SQL Analytics.
> - Ta kwerenda wymaga reguły alertu, która ma być skonfigurowana do uruchamiania z taką samą częstotliwością jak alert_run_interval, aby uniknąć zduplikowanych wyników. Reguła powinna być skonfigurowana do wypalania alertu, gdy istnieją wyniki (> 0 wyników) z kwerendy.
> - Dostosuj alert_run_interval, aby określić zakres czasu, aby sprawdzić, czy wystąpił warunek w bazach danych skonfigurowanych do przesyłania strumieniowego dziennika SQLInsights do usługi Azure SQL Analytics.
> - Dostosuj insights_string, aby przechwycić dane wyjściowe tekstu analizy głównej przyczyny aplikacji Insights. Jest to ten sam tekst wyświetlany w interfejsie użytkownika usługi Azure SQL Analytics, którego można użyć na podstawie istniejących szczegółowych informacji. Alternatywnie można użyć poniższej kwerendy, aby wyświetlić tekst wszystkich aplikacji Insights generowanych w ramach subskrypcji. Użyj danych wyjściowych kwerendy do zbierania różnych ciągów do konfigurowania alertów w aplikacji Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Tworzenie alertów dla wystąpień zarządzanych

#### <a name="managed-instance-storage-is-above-90"></a>Magazyn wystąpienia zarządzanego jest powyżej 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu jest to, że monitorowane wystąpienie zarządzane ma przesyłanie strumieniowe dziennika ResourceUsageStats włączone do usługi Azure SQL Analytics.
> - Ta kwerenda wymaga reguły alertu, która ma zostać skonfigurowana w celu wypalania alertu, gdy istnieją wyniki (> 0) z kwerendy, oznaczające, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe to zużycie procentowe magazynu w wystąpieniu zarządzanym.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Średnie zużycie procesora w wystąpieniu zarządzanym w ciągu ostatnich 1 godz.

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Wstępne wymaganie konfigurowania tego alertu jest to, że monitorowane wystąpienie zarządzane ma przesyłanie strumieniowe dziennika ResourceUsageStats włączone do usługi Azure SQL Analytics.
> - Ta kwerenda wymaga reguły alertu, która ma zostać skonfigurowana w celu wypalania alertu, gdy istnieją wyniki (> 0) z kwerendy, oznaczające, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe to średnie zużycie procentowe wykorzystania procesora CPU w zdefiniowanym okresie w wystąpieniu zarządzanym.

### <a name="pricing"></a>Cennik

Podczas korzystania z usługi Azure SQL Analytics, zużycie danych telemetrycznych diagnostyki powyżej bezpłatnych jednostek pozyskiwania danych przydzielanych co miesiąc ma zastosowanie, zobacz [Ceny usługi Log Analytics](https://azure.microsoft.com/pricing/details/monitor). Wolne jednostki pozyskiwania danych umożliwiają bezpłatne monitorowanie kilku baz danych każdego miesiąca. Bardziej aktywne bazy danych z większymi obciążeniami pozyskiwania większej ilości danych w porównaniu z bezczynnymi bazami danych. Możesz łatwo monitorować zużycie pozyskiwania danych w usłudze Azure SQL Analytics, wybierając obszar roboczy usługi OMS w menu nawigacyjnym usługi Azure SQL Analytics, a następnie wybierając użycie i koszty szacunkowe.

## <a name="next-steps"></a>Następne kroki

- Użyj [zapytań dziennika](../log-query/log-query-overview.md) w usłudze Azure Monitor, aby wyświetlić szczegółowe dane SQL platformy Azure.
- [Tworzenie własnych pulpitów nawigacyjnych](../learn/tutorial-logs-dashboards.md) z danymi SQL platformy Azure.
- [Tworzenie alertów](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń SQL platformy Azure.
