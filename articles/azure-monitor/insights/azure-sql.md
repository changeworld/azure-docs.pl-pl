---
title: Azure SQL Analytics rozwiązanie w Azure Monitor | Microsoft Docs
description: Rozwiązanie SQL Analytics na platformie Azure ułatwia zarządzanie bazami danych Azure SQL
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275466"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics (wersja zapoznawcza)

![Symbol usługi Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmurowego służące do monitorowania wydajności wszystkich baz danych SQL platformy Azure na dużą skalę i wielu subskrypcjach w jednym widoku. Azure SQL Analytics gromadzi i wizualizuje kluczowe metryki wydajności dzięki wbudowanej analizie na potrzeby rozwiązywania problemów z wydajnością.

Korzystając z tych zebranych metryk, można tworzyć niestandardowe reguły monitorowania i alerty. Azure SQL Analytics pomaga identyfikować problemy w każdej warstwie stosu aplikacji. Używa metryk diagnostycznych platformy Azure wraz z Azure Monitor widokami do prezentowania danych o wszystkich bazach danych Azure SQL w jednym Log Analytics obszarze roboczym. Azure Monitor pomaga zbierać, skorelować i wizualizować dane ze strukturą i bez struktury.

Praktyczne omówienie na temat korzystania z rozwiązania Azure SQL Analytics i typowe scenariusze użycia Zobacz osadzone wideo:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Połączone źródła

Azure SQL Analytics to rozwiązanie do monitorowania tylko w chmurze obsługujące przesyłanie strumieniowe danych telemetrycznych diagnostycznych dla wszystkich baz Azure SQL Database. Ponieważ Azure SQL Analytics nie używa agentów do łączenia się z Azure Monitor, nie obsługuje monitorowania SQL Server hostowanych lokalnie lub na maszynach wirtualnych.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Ustawienia diagnostyki](../platform/diagnostic-settings.md) | **Tak** | Dane dotyczące metryk i dzienników usługi Azure są wysyłane do dzienników Azure Monitor bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](../platform/collect-azure-metrics-logs.md) | Nie | Azure Monitor nie odczytuje danych z konta magazynu. |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Nie | Bezpośredni agenci systemu Windows nie są używani przez Azure SQL Analytics. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Agenci bezpośredniego systemu Linux nie są używani przez Azure SQL Analytics. |
| [System Center Operations Manager grupy zarządzania](../platform/om-agents.md) | Nie | Połączenie bezpośrednie od agenta Operations Manager do Azure Monitor nie jest używane przez Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opcje Azure SQL Analytics

Poniższa tabela zawiera listę obsługiwanych opcji dla dwóch wersji pulpitu nawigacyjnego Azure SQL Analytics, jednej dla baz danych i pul elastycznych, a druga dla wystąpień zarządzanych i baz danych wystąpień.

| Opcja Azure SQL Analytics | Opis | Obsługa jednej i puli baz danych i pul elastycznych | Obsługa zarządzanych wystąpień i wystąpień bazy danych |
| --- | ------- | ----- | ----- |
| Zasób według typu | Perspektywa, który zlicza wszystkie zasoby, które są monitorowane. | Yes | Yes |
| Insights | Udostępnia hierarchiczny Przechodzenie do szczegółów do inteligentnego wgląd w wydajność. | Yes | Yes |
| Błędy | Udostępnia hierarchiczny Przechodzenie do szczegółów do błędów SQL, które wystąpiły w bazach danych. | Yes | Yes |
| Limity czasu | Udostępnia hierarchiczny Przechodzenie do szczegółów do przekroczenia limitu czasu SQL, które wystąpiły w bazach danych. | Yes | Nie |
| Blokowanie | Udostępnia hierarchiczny Przechodzenie do szczegółów na blokowanie SQL, które wystąpiły w bazach danych. | Yes | Nie |
| Oczekiwania bazy danych | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania SQL na poziomie bazy danych. Zawiera podsumowania całkowity czas oczekiwania oraz czas oczekiwania na typ oczekiwania. |Yes | Nie |
| Czas trwania zapytania | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyk wykonywania zapytań, np. czas trwania zapytania, użycie procesora CPU, we/wy danych użycia, obciążenie We/Wy dziennika. | Yes | Yes |
| Czas oczekiwania przez zapytanie | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania zapytań według kategorii oczekiwania. | Yes | Yes |

## <a name="configuration"></a>Konfiguracja

Aby dodać Azure SQL Analytics (wersja zapoznawcza) do obszaru roboczego Log Analytics, należy użyć procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](../../azure-monitor/insights/solutions.md) .

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Konfigurowanie baz danych usługi Azure SQL Database do telemetrii diagnostyki przesyłania strumieniowego

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym należy **skonfigurować wszystkie** zasoby, które mają być monitorowane, aby przesyłać strumieniowo dane telemetryczne diagnostyki do Azure SQL Analytics. Wykonaj szczegółowe instrukcje na tej stronie:

- Włącz Diagnostyka Azure na potrzeby usługi Azure SQL Database, aby [przesyłać dane telemetryczne diagnostyki do Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Strony zawiera również instrukcje na temat włączania obsługi monitorowania wiele subskrypcji platformy Azure z jednego obszaru roboczego usługi Azure SQL Analytics jako jedną taflę szkła.

## <a name="using-azure-sql-analytics"></a>Używanie Azure SQL Analytics

Po dodaniu Azure SQL Analytics do obszaru roboczego kafelek Azure SQL Analytics zostanie dodany do obszaru roboczego i zostanie wyświetlony w temacie Omówienie. Wybierz łącze Wyświetl podsumowanie, aby załadować zawartość kafelka.

![Kafelek podsumowania Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po załadowaniu kafelek pokazuje liczbę baz danych i pul elastycznych, wystąpień zarządzanych oraz baz danych wystąpienia zarządzanego, z których Azure SQL Analytics otrzymuje dane telemetryczne diagnostyki.

![Kafelek usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics oferuje dwa osobne widoki — jeden do monitorowania pojedynczych baz danych i baz danych w puli oraz pul elastycznych, a także drugi widok służący do monitorowania wystąpień zarządzanych i baz danych wystąpień.

Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla jednej i puli baz danych i pul elastycznych, kliknij górną część kafelka. Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla wystąpień zarządzanych i baz danych wystąpień, kliknij dolną część kafelka.

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych usługi Azure SQL Analytics

Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Aby różne perspektywy działały, należy włączyć odpowiednie metryki lub dzienniki w zasobach SQL, aby były przesyłane strumieniowo do Log Analytics obszaru roboczego.

Jeśli niektóre metryki lub dzienniki nie są przesyłane strumieniowo do Azure Monitor, kafelki w Azure SQL Analytics nie są wypełnione informacjami monitorowania.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Widok pojedynczych baz danych i pul elastycznych

Po wybraniu kafelka usługi Azure SQL Analytics dla bazy danych jest wyświetlany pulpit nawigacyjny monitorowania.

![Usługi Azure SQL Analytics — Przegląd](./media/azure-sql/azure-sql-sol-overview.png)

Wybranie jakiegokolwiek Kafelki, otwiera raport Przechodzenie do określonego punktu widzenia. Po wybraniu punktu widzenia zostanie otwarty raport Przechodzenie do szczegółów.

![Przekroczenia limitu czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Każda perspektywa w tym widoku zawiera podsumowania na poziomach subskrypcji, serwera, elastycznej puli i bazy danych. Ponadto każda Perspektywa zawiera perspektywę specyficzne dla raportu po prawej stronie. Wybierając z listy subskrypcję, serwera, puli lub bazy danych nadal Przechodzenie do szczegółów.

### <a name="managed-instance-and-instances-databases-view"></a>Widok baz danych wystąpienia zarządzanego i wystąpienia

Po wybraniu kafelka usługi Azure SQL Analytics dla baz danych jest wyświetlany pulpit nawigacyjny monitorowania.

![Usługi Azure SQL Analytics — Przegląd](./media/azure-sql/azure-sql-sol-overview-mi.png)

Wybranie jakiegokolwiek Kafelki, otwiera raport Przechodzenie do określonego punktu widzenia. Po wybraniu punktu widzenia zostanie otwarty raport Przechodzenie do szczegółów.

Po wybraniu widoku wystąpienia zarządzanego program wyświetli szczegóły dotyczące użycia wystąpienia zarządzanego, baz danych, które zawiera, oraz dane telemetryczne dla zapytań wykonywanych w tym wystąpieniu.

![Przekroczenia limitu czasu usługi Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights raportu

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) pozwala wiedzieć, co dzieje się z wydajnością wszystkich baz danych SQL Azure. Wszystkie Intelligent Insights zbierane można zwizualizować i dostępne za pośrednictwem perspektywy szczegółowych informacji.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pule elastyczne i raporty bazy danych

W przypadku pul elastycznych i baz danych są używane własne raporty pokazujące wszystkie dane zbierane dla zasobu w określonym czasie.

![Usługa Azure SQL Database analizy](./media/azure-sql/azure-sql-sol-database.png)

![Elastyczna Pula usługi Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytania

Przez czas trwania zapytania i zapytanie czekają na perspektywy, można skorelować wydajność dowolnego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność kwerend w różnych bazach danych i ułatwia identyfikowanie baz danych, które wykonują wybranego zapytania również i te, które działają wolno.

![Zapytania analityczne w usłudze Azure SQL](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Uprawnienia

Aby korzystać z usługi Azure SQL Analytics, użytkownicy muszą mieć uprawnienia minimalne uprawnienia roli Czytelnik na platformie Azure. Tej roli, jednak nie umożliwiają użytkownikom wyświetlić tekst zapytania lub wykonać wszelkie dostrajanie akcje automatyczne. Bardziej ograniczając role na platformie Azure, które zezwalają na używanie Azure SQL Analytics do pełnego zakresu są właścicielami, współautorem, współautorem bazy danych SQL lub współautorem SQL Server. Można również należy rozważyć utworzenie niestandardowej roli w portalu z określonymi uprawnieniami tylko musieli używać usługi Azure SQL Analytics, bez dostępu do zarządzania innych zasobów.

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

Analiza danych w Azure SQL Analytics opiera się na [log Analytics języku](../log-query/get-started-queries.md) dla niestandardowych zapytań i raportowania. Znajdź opis dostępnych danych zebranych z zasobu bazy danych na potrzeby niestandardowych zapytań w [dostępnych metrykach i dziennikach](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatyczne generowanie alertów w Azure SQL Analytics opiera się na pisaniu Log Analytics kwerendy, która wyzwala alert po spełnieniu warunku. Znajdź poniżej kilka przykładów na Log Analytics zapytaniach, na których można skonfigurować alerty w Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Tworzenie alertów dla usługi Azure SQL Database

Możesz łatwo [tworzyć alerty](../platform/alerts-metric.md) z danymi pochodzącymi z zasobów Azure SQL Database. Oto kilka przydatnych [zapytań dzienników](../log-query/log-query-overview.md) , których można użyć w przypadku alertu dziennika:

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
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
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
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
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
>
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają Podstawowe metryki do Azure SQL Analytics.
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
>
> - Przed rozpoczęciem konfigurowania tego alertu jest to, że monitorowane bazy danych SQLInsightsą dziennik diagnostyczny przesyłania strumieniowego do Azure SQL Analytics.
> - To zapytanie wymaga regułę alertu można skonfigurować do uruchamiania z taką samą częstotliwością, alert_run_interval, aby uniknąć duplikowania. Reguły należy skonfigurować tak, aby wyzwolić alert, gdy istnieje wyniki (liczba wyników > 0) z kwerendy.
> - Dostosuj alert_run_interval, aby określić zakres czasu, aby sprawdzić, czy warunek wystąpił w bazach danych skonfigurowanych do przesyłania strumieniowego dziennika SQLInsights do Azure SQL Analytics.
> - Dostosuj insights_string do przechwytywania danych wyjściowych tekst analizy przyczyny głównej szczegółowych informacji. Jest to ten sam tekst wyświetlany w interfejsie użytkownika Azure SQL Analytics, którego możesz użyć z istniejących szczegółowych informacji. Alternatywnie można użyć poniższe zapytanie tekst wszystkie szczegółowe informacje wygenerowane w ramach Twojej subskrypcji. Zdobycia różne ciągi konfigurowania alertów dotyczących szczegółowe informacje, należy użyć danych wyjściowych zapytania.

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
> - Przed ustawieniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe to użycie procentowe magazynu dla wystąpienia zarządzanego.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Średnie użycie procesora CPU wystąpienia zarządzanego jest powyżej 95% w ciągu ostatnich 1 HR

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
> - Przed ustawieniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats Azure SQL Analytics.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe są średnim zużyciem użycia procesora CPU w określonym okresie w wystąpieniu zarządzanym.

### <a name="pricing"></a>Ceny

Gdy usługa Azure SQL Analytics jest bezpłatna, użycie danych telemetrycznych diagnostyki powyżej bezpłatnych jednostek, które są przystosowane do pozyskiwania w każdym miesiącu, znajduje się w temacie [log Analytics Cennik](https://azure.microsoft.com/pricing/details/monitor). Bezpłatne jednostki pozyskiwanie danych, pod warunkiem włączyć, bezpłatne monitorowanie kilka baz danych każdego miesiąca. Więcej aktywnych baz danych z większymi obciążeniami zwiększają ilości danych i bezczynnych baz danych. Możesz łatwo monitorować użycie pozyskiwania danych w Azure SQL Analytics, wybierając obszar roboczy OMS w menu nawigacji Azure SQL Analytics, a następnie wybierając pozycję użycie i szacowane koszty.

## <a name="next-steps"></a>Następne kroki

- Użyj [zapytań dzienników](../log-query/log-query-overview.md) w Azure monitor, aby wyświetlić szczegółowe dane SQL platformy Azure.
- [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) pokazujące dane usługi Azure SQL.
- [Utwórz alerty](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń SQL platformy Azure.
