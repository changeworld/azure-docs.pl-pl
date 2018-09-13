---
title: Rozwiązanie SQL Analytics na platformie Azure w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie SQL Analytics na platformie Azure ułatwia zarządzanie bazami danych Azure SQL
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: na
ms.openlocfilehash: b8d8acda4ff1dee0643227c3fa2375c634c1b4a4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717424"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorowanie usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics (wersja zapoznawcza)

![Symbol usługi Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Usługa Azure SQL Analytics to w chmurze rozwiązanie do monitorowania wydajności bazy danych Azure SQL, pul elastycznych i wystąpienia zarządzane przez usługę na dużą skalę i w ramach wielu subskrypcji do monitorowania. Jego zbiera i wizualizuje ważne metryki wydajności bazy danych Azure SQL Database dzięki wbudowanym funkcjom analizy dla Rozwiązywanie problemów z wydajnością.

Za pomocą metryk, które są zbierane za pomocą rozwiązania, można utworzyć niestandardowe reguły monitorowania i alertów. To rozwiązanie pomaga zidentyfikować problemy w każdej warstwie stosu aplikacji. Aby przedstawić dane dotyczące wszystkich usługi Azure SQL bazy danych, pul elastycznych i baz danych w wystąpieniach zarządzanych w jeden obszar roboczy usługi Log Analytics używa metryki diagnostycznych platformy Azure wraz z widokami usługi Log Analytics. Usługa log Analytics pomaga gromadzić, korelować i wizualizować dane ze strukturą i bez struktury.

Obecnie to rozwiązanie w wersji zapoznawczej obsługuje maksymalnie 200 000 baz danych Azure SQL i 5000 pule elastyczne SQL danego obszaru roboczego.

Praktyczne omówienie na temat korzystania z rozwiązania Azure SQL Analytics i typowe scenariusze użycia Zobacz osadzone wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Połączone źródła

Usługa Azure SQL Analytics to tylko monitorowanie rozwiązań pomocnicze przesyłania strumieniowego dane diagnostyczne i telemetryczne dla baz danych Azure SQL, pul elastycznych i wystąpienia zarządzane przez usługę w chmurze. Zgodnie z agentów nie korzysta się z usługą Log Analytics, rozwiązanie nie obsługują monitorowanie maszyn wirtualnych lub lokalnych serwerach SQL, zobacz w poniższej tabeli zgodności.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| **[Diagnostyka Azure](log-analytics-azure-storage.md)** | **Tak** | Danych metryk i dzienników platformy Azure są wysyłane do usługi Log Analytics bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Usługa log Analytics nie odczytać danych z konta magazynu. |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie | Agentów bezpośrednich Windows nie są używane przez to rozwiązanie. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Bezpośredni agenci dla systemu Linux nie są używane przez to rozwiązanie. |
| [Grupy zarządzania SCOM](log-analytics-om-agents.md) | Nie | Bezpośrednie połączenie agenta programu SCOM do usługi Log Analytics nie jest używana przez to rozwiązanie. |

## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby dodać rozwiązania Azure SQL Analytics do swojego obszaru roboczego.

1. Dodawanie rozwiązania do usługi Azure SQL Analytics do swojego obszaru roboczego z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. W witrynie Azure portal kliknij pozycję **+ Utwórz zasób**, a następnie wyszukaj **usługi Azure SQL Analytics**.  
    ![Monitorowanie i zarządzanie](./media/log-analytics-azure-sql/monitoring-management.png)
3. Wybierz **usługi Azure SQL Analytics (wersja zapoznawcza)** z listy
4. W **usługi Azure SQL Analytics (wersja zapoznawcza)** obszaru, kliknij przycisk **Utwórz**.  
    ![Tworzenie](./media/log-analytics-azure-sql/portal-create.png)
5. W **Utwórz nowe rozwiązanie** obszaru, Utwórz nową lub wybierz istniejący obszar roboczy, który chcesz dodać do rozwiązania, a następnie kliknij przycisk **Utwórz**.  
    ![Dodaj do obszaru roboczego](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Konfigurowanie bazy danych Azure SQL i pule elastyczne umożliwiające dane diagnostyczne i telemetryczne strumienia

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym, aby monitorować wydajność bazy danych Azure SQL i/lub pul elastycznych, konieczne będzie **Skonfiguruj każdy** bazy danych SQL Azure i zasobów puli elastycznej mają do monitorowania, aby przesyłać strumieniowo jego dane diagnostyczne i telemetryczne do rozwiązania.

- Włącz diagnostykę platformy Azure dla bazy danych Azure SQL i pul elastycznych i [ich konfigurowania pod kątem wysyłają dane do usługi Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Aby skonfigurować wiele subskrypcji platformy Azure

Aby obsługiwać wiele subskrypcji, należy użyć skryptu programu PowerShell z [włączyć usługi Azure rejestrowanie metryki dla zasobów przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Podaj identyfikator zasobu obszaru roboczego jako parametr, podczas wykonywania skryptu do wysyłania danych diagnostycznych z zasobów w jednej subskrypcji platformy Azure do obszaru roboczego w innej subskrypcji platformy Azure.

**Przykład**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania do obszaru roboczego do obszaru roboczego zostanie dodany Kafelek usługi Azure SQL Analytics, a zostanie on wyświetlony na Przegląd. Kafelek pokazuje liczbę baz danych Azure SQL i pule elastyczne Azure SQL, połączone rozwiązania.

![Kafelek usługi Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych usługi Azure SQL Analytics

Kliknij pozycję **usługi Azure SQL Analytics** Kafelek, aby otworzyć pulpit nawigacyjny usługi Azure SQL Analytics. Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Do pracy z różnych perspektyw należy włączyć odpowiednie metryk lub dzienników na zasobów SQL, aby być przesłana strumieniowo do obszaru roboczego usługi Azure Log Analytics.

![Usługi Azure SQL Analytics — Przegląd](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Wybranie jakiegokolwiek Kafelki, otwiera raport Przechodzenie do określonego punktu widzenia. Po wybraniu punktu widzenia zostanie otwarty raport Przechodzenie do szczegółów.

![Przekroczenia limitu czasu usługi Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Każda Perspektywa zawiera podsumowania dotyczące subskrypcji, serwera, pula elastyczna i poziomu bazy danych. Ponadto każda Perspektywa zawiera perspektywę specyficzne dla raportu po prawej stronie. Wybierając z listy subskrypcję, serwera, puli lub bazy danych nadal Przechodzenie do szczegółów.

| Perspektywa | Opis |
| --- | --- |
| Zasób według typu | Perspektywa, który zlicza wszystkie zasoby, które są monitorowane. Przechodzenie do szczegółów zawiera podsumowanie liczby jednostek DTU i GB metryki. |
| Insights | Udostępnia hierarchiczny Przechodzenie do szczegółów w szczegółowe informacje. Dowiedz się więcej o usłudze intelligent insights. |
| Błędy | Udostępnia hierarchiczny Przechodzenie do szczegółów do błędów SQL, które wystąpiły w bazach danych. |
| Limity czasu | Udostępnia hierarchiczny Przechodzenie do szczegółów do przekroczenia limitu czasu SQL, które wystąpiły w bazach danych. |
| Bloki | Udostępnia hierarchiczny Przechodzenie do szczegółów na blokowanie SQL, które wystąpiły w bazach danych. |
| Oczekiwania bazy danych | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania SQL na poziomie bazy danych. Zawiera podsumowania całkowity czas oczekiwania oraz czas oczekiwania na typ oczekiwania. |
| Czas trwania zapytania | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyk wykonywania zapytań, np. czas trwania zapytania, użycie procesora CPU, we/wy danych użycia, obciążenie We/Wy dziennika. |
| Oczekiwania zapytań | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania zapytań według kategorii oczekiwania. |

### <a name="intelligent-insights-report"></a>Intelligent Insights raportu

Usługa Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) umożliwia sprawdzenie, co się dzieje z wydajność bazy danych. Wszystkie Intelligent Insights zbierane można zwizualizować i dostępne za pośrednictwem perspektywy szczegółowych informacji.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Elastycznej puli i bazy danych raportów

Elastyczne pule i bazy danych ma swoje własne konkretne raporty pokazujące wszystkie dane, które są zbierane dla zasobu w określonym czasie.

![Usługa Azure SQL Database analizy](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Elastyczna Pula usługi Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytania

Czas trwania zapytania i zapytania w tym czasie czeka perspektyw można skorelować wydajność każdego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność kwerend w różnych bazach danych i ułatwia identyfikowanie baz danych, które wykonują wybranego zapytania również i te, które działają wolno.

![Zapytania analityczne w usłudze Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenia alertów

Możesz z łatwością [tworzyć alerty](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) przy użyciu danych pochodzących z zasobów usługi Azure SQL Database. Poniżej przedstawiono niektóre przydatne [wyszukiwanie w dzienniku](log-analytics-log-searches.md) zapytań, które można używać z alertu dziennika:

*Wysokie użycie procesora CPU w usłudze Azure SQL Database*

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

*Wysokie użycie procesora CPU o pulach elastycznych usługi Azure SQL Database*

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

*Usługa Azure storage bazy danych SQL w średnia powyżej 95% w ostatniej 1 godziny*

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

*Alert po wystąpieniu Intelligent insights*

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

## <a name="next-steps"></a>Kolejne kroki

- Użyj [wyszukiwań w dziennikach](log-analytics-log-searches.md) w usłudze Log Analytics, aby wyświetlić szczegółowe dane usługi Azure SQL.
- [Tworzenie własnych pulpitów nawigacyjnych](log-analytics-dashboards.md) Pokazywanie danych Azure SQL.
- [Tworzenie alertów](log-analytics-alerts.md) po wystąpieniu określonych zdarzeń usługi Azure SQL.
