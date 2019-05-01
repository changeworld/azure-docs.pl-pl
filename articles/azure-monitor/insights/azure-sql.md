---
title: Rozwiązanie SQL Analytics na platformie Azure w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Rozwiązanie SQL Analytics na platformie Azure ułatwia zarządzanie bazami danych Azure SQL
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 03f330f429be583d3a400eb1ee00875f7c305e74
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64923013"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics (wersja zapoznawcza)

![Symbol usługi Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Funkcja analizy usługi Azure SQL jest zaawansowanych chmurze rozwiązanie do monitorowania wydajności baz danych, pul elastycznych i wystąpienia zarządzane przez usługę Azure SQL na dużą skalę i w ramach wielu subskrypcji za pomocą jedną taflę szkła do monitorowania. Jego zbiera i wizualizuje ważne metryki wydajności bazy danych Azure SQL Database dzięki wbudowanym funkcjom analizy dla Rozwiązywanie problemów z wydajnością.

Za pomocą metryk, które są zbierane za pomocą rozwiązania, można utworzyć niestandardowe reguły monitorowania i alertów. To rozwiązanie pomaga zidentyfikować problemy w każdej warstwie stosu aplikacji. Aby przedstawić dane dotyczące wszystkich usługi Azure SQL bazy danych, pul elastycznych i baz danych w wystąpieniach zarządzanych w jeden obszar roboczy usługi Log Analytics używa metryki diagnostycznych platformy Azure wraz z widokami usługi Azure Monitor. Usługa Azure Monitor pomaga gromadzić, korelować i wizualizować dane ze strukturą i bez struktury.

Praktyczne omówienie na temat korzystania z rozwiązania Azure SQL Analytics i typowe scenariusze użycia Zobacz osadzone wideo:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Połączone źródła

Usługa Azure SQL Analytics to w chmurze, tylko do monitorowania rozwiązania pomocnicze przesyłania strumieniowego dane diagnostyczne i telemetryczne baz danych Azure SQL: pojedynczej puli i zarządzane wystąpienie bazy danych. Jako rozwiązanie używa agentów do nawiązać połączenie z usługi Azure Monitor, rozwiązanie nie obsługują monitorowanie programu SQL Server hostowanego lokalnie lub na maszynach wirtualnych, zobacz w poniższej tabeli zgodności.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Diagnostyka Azure](../platform/collect-azure-metrics-logs.md) | **Tak** | Danych metryk i dzienników platformy Azure są wysyłane do usługi Azure Monitor dzienników bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](../platform/collect-azure-metrics-logs.md) | Nie | Usługa Azure Monitor nie odczytać danych z konta magazynu. |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Nie | Agentów bezpośrednich Windows nie są używane przez to rozwiązanie. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Bezpośredni agenci dla systemu Linux nie są używane przez to rozwiązanie. |
| [Grupa zarządzania programu System Center Operations Manager](../platform/om-agents.md) | Nie | Bezpośrednie połączenie agenta programu Operations Manager do usługi Azure Monitor nie jest używana przez to rozwiązanie. |

## <a name="configuration"></a>Konfigurowanie
Użyj procesu opisanego w [rozwiązań Dodaj usługi Azure Monitor z galerii rozwiązań](../../azure-monitor/insights/solutions.md) można dodać rozwiązania Azure SQL Analytics (wersja zapoznawcza) do obszaru roboczego usługi Log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Konfigurowanie baz danych SQL Azure, pul elastycznych i wystąpienia zarządzane przez usługę do dane diagnostyczne i telemetryczne strumienia

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym, musisz **Skonfiguruj każdy** zasoby, które chcesz monitorować, aby przesyłać strumieniowo jego dane diagnostyczne i telemetryczne do rozwiązania. Wykonaj szczegółowe instrukcje na tej stronie:

- Włącz diagnostykę platformy Azure dla usługi Azure SQL database do [strumieniowo dane diagnostyczne i telemetryczne do usługi Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Strony zawiera również instrukcje na temat włączania obsługi monitorowania wiele subskrypcji platformy Azure z jednego obszaru roboczego usługi Azure SQL Analytics jako jedną taflę szkła.

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania do obszaru roboczego do obszaru roboczego zostanie dodany Kafelek usługi Azure SQL Analytics, a zostanie on wyświetlony na Przegląd. Wybierz łącze Wyświetl podsumowanie, aby załadować zawartość kafelka.

![Kafelek podsumowania usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po załadowaniu Kafelek pokazuje liczbę baz danych, pul elastycznych, wystąpienia zarządzane przez usługę i baz danych Azure SQL w wystąpieniach zarządzanych, które odbiera dane diagnostyczne i telemetryczne z rozwiązania.

![Kafelek usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Rozwiązanie zawiera dwa osobne widoki — jeden dla monitorowania baz danych SQL Azure i elastycznych pul i innych widokach monitorowania wystąpienia zarządzanego i baz danych w wystąpieniach zarządzanych.

Aby wyświetlić pulpit nawigacyjny monitorowania usługi Azure SQL Analytics dla baz danych SQL Azure i pul elastycznych, kliknij w górnej części kafelka. Aby wyświetlić pulpit nawigacyjny monitorowania dla wystąpienia zarządzanego i baz danych w wystąpieniu zarządzanym usługi Azure SQL Analytics, kliknij przycisk w dolnej części kafelka.

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych usługi Azure SQL Analytics

Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Do pracy z różnych perspektyw należy włączyć odpowiednie metryk lub dzienników na zasobów SQL, aby być przesłana strumieniowo do obszaru roboczego usługi Log Analytics.

Należy zwrócić uwagę na to, czy w przypadku niektórych metryk lub dzienników nie są przesyłane strumieniowo do usługi Azure Monitor, Kafelki w rozwiązaniu nie są wypełniane przy użyciu informacje o monitorowaniu.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Usługa Azure SQL Database i widok puli elastycznej

Po wybraniu kafelka usługi Azure SQL Analytics dla bazy danych jest wyświetlany pulpit nawigacyjny monitorowania.

![Usługi Azure SQL Analytics — Przegląd](./media/azure-sql/azure-sql-sol-overview.png)

Wybranie jakiegokolwiek Kafelki, otwiera raport Przechodzenie do określonego punktu widzenia. Po wybraniu punktu widzenia zostanie otwarty raport Przechodzenie do szczegółów.

![Przekroczenia limitu czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Każda Perspektywa, w tym widoku zawiera podsumowania dotyczące subskrypcji, serwera, pula elastyczna i poziomu bazy danych. Ponadto każda Perspektywa zawiera perspektywę specyficzne dla raportu po prawej stronie. Wybierając z listy subskrypcję, serwera, puli lub bazy danych nadal Przechodzenie do szczegółów.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Wyświetl wystąpienia zarządzanego i baz danych w wystąpieniu zarządzanym

Po wybraniu kafelka usługi Azure SQL Analytics dla baz danych jest wyświetlany pulpit nawigacyjny monitorowania.

![Usługi Azure SQL Analytics — Przegląd](./media/azure-sql/azure-sql-sol-overview-mi.png)

Wybranie jakiegokolwiek Kafelki, otwiera raport Przechodzenie do określonego punktu widzenia. Po wybraniu punktu widzenia zostanie otwarty raport Przechodzenie do szczegółów.

Wybraniu widoku wystąpienia zarządzanego, przedstawia szczegółowe informacje na wykorzystanie wystąpienia zarządzanego, baz danych, które zawiera i danych telemetrycznych na zapytania wykonywane w różnych wystąpienia.

![Przekroczenia limitu czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektywy

Poniżej tabeli przedstawiono perspektywy obsługiwane w przypadku dwóch wersji pulpitu nawigacyjnego, co dla usługi Azure SQL bazy danych i elastycznych pul i jeden z nich dla wystąpienia zarządzanego.

| Perspektywa | Opis | Obsługa bazy danych SQL i elastyczne pule | Obsługa wystąpienia zarządzanego |
| --- | ------- | ----- | ----- |
| Zasób według typu | Perspektywa, który zlicza wszystkie zasoby, które są monitorowane. | Yes | Yes |
| Insights | Udostępnia hierarchiczny Przechodzenie do szczegółów do inteligentnego wgląd w wydajność. | Yes | Yes |
| Błędy | Udostępnia hierarchiczny Przechodzenie do szczegółów do błędów SQL, które wystąpiły w bazach danych. | Yes | Yes |
| Limity czasu | Udostępnia hierarchiczny Przechodzenie do szczegółów do przekroczenia limitu czasu SQL, które wystąpiły w bazach danych. | Yes | Nie |
| Blokowanie | Udostępnia hierarchiczny Przechodzenie do szczegółów na blokowanie SQL, które wystąpiły w bazach danych. | Yes | Nie |
| Oczekiwania bazy danych | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania SQL na poziomie bazy danych. Zawiera podsumowania całkowity czas oczekiwania oraz czas oczekiwania na typ oczekiwania. |Yes | Yes |
| Czas trwania zapytania | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyk wykonywania zapytań, np. czas trwania zapytania, użycie procesora CPU, we/wy danych użycia, obciążenie We/Wy dziennika. | Yes | Yes |
| Czas oczekiwania przez zapytanie | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania zapytań według kategorii oczekiwania. | Yes | Yes |

### <a name="intelligent-insights-report"></a>Intelligent Insights raportu

Usługa Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) umożliwia sprawdzenie, co się dzieje z wydajnością wszystkich baz danych Azure SQL. Wszystkie Intelligent Insights zbierane można zwizualizować i dostępne za pośrednictwem perspektywy szczegółowych informacji.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastycznej puli i bazy danych raportów

Elastyczne pule i bazy danych SQL ma własne szczegółowymi raportami, które są pokazywane wszystkie dane, które są zbierane dla zasobu w określonym czasie.

![Usługa Azure SQL Database analizy](./media/azure-sql/azure-sql-sol-database.png)

![Elastyczna Pula usługi Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytania

Czas trwania zapytania i zapytania w tym czasie czeka perspektyw można skorelować wydajność każdego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność kwerend w różnych bazach danych i ułatwia identyfikowanie baz danych, które wykonują wybranego zapytania również i te, które działają wolno.

![Zapytania analityczne w usłudze Azure SQL](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Uprawnienia

Aby korzystać z usługi Azure SQL Analytics, użytkownicy muszą mieć uprawnienia minimalne uprawnienia roli Czytelnik na platformie Azure. Tej roli, jednak nie umożliwiają użytkownikom wyświetlić tekst zapytania lub wykonać wszelkie dostrajanie akcje automatyczne. Mniej ograniczająca ról na platformie Azure, które zezwalają na użycie rozwiązania w najszerszym zakresie są właścicielem, współautorem, współautor bazy danych SQL lub Współautor serwera SQL Server. Można również należy rozważyć utworzenie niestandardowej roli w portalu z określonymi uprawnieniami tylko musieli używać usługi Azure SQL Analytics, bez dostępu do zarządzania innych zasobów.

### <a name="creating-a-custom-role-in-portal"></a>Tworzenie roli niestandardowej w portalu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Rozpoznawanie, że niektóre organizacje wymuszanie kontroli ograniczeniami uprawnień na platformie Azure, znajdź następujący skrypt programu PowerShell umożliwiające tworzenie roli niestandardowej "SQL Analytics operatora monitorowania" w witrynie Azure portal z minimalnymi uprawnienia odczytu i zapisu wymagane do Użyj usługi Azure SQL Analytics, aby możliwie największego rozmiaru.

W powyższej ścieżce zastąpić "{SubscriptionId}" poniższego skryptu za pomocą Identyfikatora subskrypcji platformy Azure i uruchom skrypt zalogować się jako roli współautora lub właściciela na platformie Azure.

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
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Po utworzeniu nowej roli, należy przypisać tę rolę do każdego użytkownika, który należy udzielić uprawnień niestandardowych, aby korzystać z usługi Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenia alertów

Analiza danych w usłudze Azure SQL Analytics opiera się na [języka usługi Log Analytics](../log-query/get-started-queries.md) niestandardowych zapytań i raportowania. Znajdź opis dostępnych danych zebranych z zasobów bazy danych niestandardowych podczas wykonywania zapytań w [metryk i dzienników dostępnych](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatyczne alerty w rozwiązaniu opiera się na piśmie zapytanie usługi Log Analytics, która wyzwoli alert, pod warunkiem spełnienia. Znajdź poniższe przykłady zapytań usługi Log Analytics po alerty, które można skonfigurować w rozwiązaniu.

### <a name="creating-alerts-for-azure-sql-database"></a>Tworzenie alertów dla usługi Azure SQL Database

Możesz z łatwością [tworzyć alerty](../platform/alerts-metric.md) przy użyciu danych pochodzących z zasobów usługi Azure SQL Database. Poniżej przedstawiono niektóre przydatne [rejestrowania zapytań](../log-query/log-query-overview.md) używanego przy użyciu alertu dzienników:

#### <a name="high-cpu-on-azure-sql-database"></a>Wysokie użycie procesora CPU w usłudze Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Wymaganie wstępne konfigurowania ten alert jest tym monitorowane bazy danych strumienia metrykę diagnostyki (opcja "Wszystkich metryk") do rozwiązania.
> - Zastąp cpu_percent wartość MetricName dtu_consumption_percent zamiast tego uzyskać wysoki wyników jednostek DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Wysokie użycie procesora CPU o pulach elastycznych usługi Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Wymaganie wstępne konfigurowania ten alert jest tym monitorowane bazy danych strumienia metrykę diagnostyki (opcja "Wszystkich metryk") do rozwiązania.
> - Zastąp cpu_percent wartość MetricName dtu_consumption_percent zamiast tego uzyskać wysoki wyników jednostek DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Usługa Azure storage bazy danych SQL w średnia powyżej 95% w ostatniej 1 godziny

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
> - Wymaganie wstępne konfigurowania ten alert jest tym monitorowane bazy danych strumienia metrykę diagnostyki (opcja "Wszystkich metryk") do rozwiązania.
> - To zapytanie wymaga regułę alertu do konfigurowane tak, aby wyzwolić alert, gdy istnieje wyniki (liczba wyników > 0) z kwerendy oznaczający, że z warunkiem istnieje w niektórych bazach danych. Dane wyjściowe znajduje się lista zasobów bazy danych, które znajdują się powyżej storage_threshold w ramach time_range zdefiniowane.
> - Dane wyjściowe znajduje się lista zasobów bazy danych, które znajdują się powyżej storage_threshold w ramach time_range zdefiniowane.

#### <a name="alert-on-intelligent-insights"></a>Alert po wystąpieniu Intelligent insights

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
> - Wymaganie wstępne konfigurowania ten alert jest ten dziennik diagnostyczny monitorowane bazy danych strumienia SQLInsights do rozwiązania.
> - To zapytanie wymaga regułę alertu można skonfigurować do uruchamiania z taką samą częstotliwością, alert_run_interval, aby uniknąć duplikowania. Reguły należy skonfigurować tak, aby wyzwolić alert, gdy istnieje wyniki (liczba wyników > 0) z kwerendy.
> - Dostosuj alert_run_interval, aby określić zakres czasu, aby sprawdzić, czy warunek pojawił się na baz danych skonfigurowanych do strumieniowe przesyłanie dzienników SQLInsights do rozwiązania.
> - Dostosuj insights_string do przechwytywania danych wyjściowych tekst analizy przyczyny głównej szczegółowych informacji. Jest to ten sam tekst wyświetlany w Interfejsie użytkownika rozwiązania, które można użyć istniejącej usługi insights. Alternatywnie można użyć poniższe zapytanie tekst wszystkie szczegółowe informacje wygenerowane w ramach Twojej subskrypcji. Zdobycia różne ciągi konfigurowania alertów dotyczących szczegółowe informacje, należy użyć danych wyjściowych zapytania.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Tworzenie alertów dla wystąpienia zarządzanego

#### <a name="managed-instance-storage-is-above-90"></a>Magazyn jest ponad 90% wystąpienie zarządzane

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Wymaganie wstępne konfigurowania tego alertu polega na tym, że monitorowane wystąpienia zarządzanego ma przesyłania strumieniowego dziennika ResourceUsageStats włączony do rozwiązania.
> - To zapytanie wymaga regułę alertu do konfigurowane tak, aby wyzwolić alert, gdy istnieje wyniki (liczba wyników > 0) z kwerendy oznaczający, że z warunkiem istnieje w wystąpieniu zarządzanym. Dane wyjściowe to wykorzystania procent magazynu na wystąpieniu zarządzanym.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Zarządzane zużycie średnie wystąpienia Procesora jest powyżej 95% w ostatniej 1 godziny

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Wymaganie wstępne konfigurowania tego alertu polega na tym, że monitorowane wystąpienia zarządzanego ma przesyłania strumieniowego dziennika ResourceUsageStats włączony do rozwiązania.
> - To zapytanie wymaga regułę alertu do konfigurowane tak, aby wyzwolić alert, gdy istnieje wyniki (liczba wyników > 0) z kwerendy oznaczający, że z warunkiem istnieje w wystąpieniu zarządzanym. Dane wyjściowe są średnie użycie wartości procentowej użycia Procesora w określonym przedziale czasu na wystąpieniu zarządzanym.

### <a name="pricing"></a>Cennik

W trakcie można używać bezpłatnie rozwiązanie ma zastosowanie użycie dane diagnostyczne i telemetryczne powyżej bezpłatne jednostki pozyskiwanie danych przydzielone każdego miesiąca, zobacz [cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/monitor). Bezpłatne jednostki pozyskiwanie danych, pod warunkiem włączyć, bezpłatne monitorowanie kilka baz danych każdego miesiąca. Należy pamiętać, że aktywnych baz danych o większych obciążeń pozyskiwania większej ilości danych i baz danych w stanie bezczynności. Możesz łatwo monitorować swoje użycie pozyskiwania danych w rozwiązaniu, wybierając obszar roboczy pakietu OMS w menu nawigacji usługi Azure SQL Analytics, a następnie wybierając użycie i szacowane koszty.

## <a name="next-steps"></a>Kolejne kroki

- Użyj [rejestrowania zapytań](../log-query/log-query-overview.md) w usłudze Azure Monitor, aby wyświetlić szczegółowe dane usługi Azure SQL.
- [Tworzenie własnych pulpitów nawigacyjnych](../learn/tutorial-logs-dashboards.md) Pokazywanie danych Azure SQL.
- [Tworzenie alertów](../platform/alerts-overview.md) po wystąpieniu określonych zdarzeń usługi Azure SQL.
