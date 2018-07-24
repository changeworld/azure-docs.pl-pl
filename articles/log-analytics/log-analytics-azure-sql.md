---
title: Rozwiązanie SQL Analytics na platformie Azure w usłudze Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie SQL Analytics na platformie Azure ułatwia zarządzanie bazami danych Azure SQL
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 440e16416b8567178c61c3d6ce2155e0e331521c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216329"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitor Azure SQL Database przy użyciu usługi Azure SQL Analytics (wersja zapoznawcza)

![Symbol usługi Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Usługa Azure SQL Analytics to w chmurze rozwiązanie do monitorowania wydajności baz danych SQL Azure na dużą skalę z przekraczaniem wielu pul elastycznych i subskrypcji do monitorowania. Jego zbiera i wizualizuje ważne metryki wydajności bazy danych Azure SQL Database dzięki wbudowanym funkcjom analizy wydajności, rozwiązywanie problemów z na górze. 

Za pomocą metryk, które są zbierane za pomocą rozwiązania, można utworzyć niestandardowe reguły monitorowania i alertów. To rozwiązanie pomaga zidentyfikować problemy w każdej warstwie stosu aplikacji. Metryki diagnostycznych platformy Azure wraz z widokami usługi Log Analytics używa do prezentowania danych dotyczących wszystkich baz danych Azure SQL Database i elastycznych pul w jeden obszar roboczy usługi Log Analytics. Usługa log Analytics pomaga gromadzić, korelować i wizualizować dane ze strukturą i bez struktury.

Obecnie to rozwiązanie w wersji zapoznawczej obsługuje maksymalnie 150 000 baz danych SQL Azure i 5000 pule elastyczne SQL danego obszaru roboczego.

Praktyczne omówienie na temat korzystania z rozwiązania Azure SQL Analytics i typowe scenariusze użycia Zobacz osadzone wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Połączone źródła

Usługa Azure SQL Analytics to monitorowanie rozwiązań pomocnicze przesyłanie strumieniowe dane diagnostyczne i telemetryczne do bazy danych SQL Azure i pul elastycznych w chmurze. Jak połączyć się z usługą Log Analytics nie używa agentów, rozwiązanie nie obsługuje łączność z Windows, Linux lub zasobów programu SCOM, zobacz w poniższej tabeli zgodności.

| Połączone źródło | Pomoc techniczna | Opis |
| --- | --- | --- |
| **[Diagnostyka Azure](log-analytics-azure-storage.md)** | **Tak** | Danych metryk i dzienników platformy Azure są wysyłane do usługi Log Analytics bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Usługa log Analytics nie odczytuje dane z konta magazynu. |
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

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Konfigurowanie bazy danych SQL Azure i pul elastycznych do strumienia dane diagnostyczne i telemetryczne

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym, aby monitorować wydajność bazy danych SQL Azure i/lub pul elastycznych, konieczne będzie **Skonfiguruj każdy** bazy danych SQL Azure i zasobów puli elastycznej mają do monitorowania, aby przesyłać strumieniowo jego dane diagnostyczne i telemetryczne do rozwiązania.

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
| Blokowanie | Udostępnia hierarchiczny Przechodzenie do szczegółów na blokowanie SQL, które wystąpiły w bazach danych. |
| Oczekiwania bazy danych | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania SQL na poziomie bazy danych. Zawiera podsumowania całkowity czas oczekiwania oraz czas oczekiwania na typ oczekiwania. |
| Czas trwania zapytania | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyk wykonywania zapytań, np. czas trwania zapytania, użycie procesora CPU, we/wy danych użycia, obciążenie We/Wy dziennika. |
| Czas oczekiwania przez zapytanie | Udostępnia hierarchiczny Przechodzenie do szczegółów do statystyki oczekiwania zapytań według kategorii oczekiwania. |

### <a name="intelligent-insights-report"></a>Intelligent Insights raportu

Usługa Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) umożliwia sprawdzenie, co się dzieje z wydajność bazy danych. Wszystkie Intelligent Insights zbierane można zwizualizować i dostępne za pośrednictwem perspektywy szczegółowych informacji.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Raporty elastycznej puli i bazy danych

Elastyczne pule i bazy danych ma swoje własne szczegółowymi raportami, które są pokazywane wszystkie dane, które są zbierane dla zasobu w określonym czasie.

![Usługa Azure SQL Database analizy](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Usługi Azure SQL Analytics elastycznej puli](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytania

Czas trwania zapytania i zapytania w tym czasie czeka perspektyw można skorelować wydajność każdego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność kwerend w różnych bazach danych i ułatwia identyfikowanie baz danych, które wykonują wybranego zapytania również i te, które działają wolno.

![Zapytania analityczne w usłudze Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenia alertów

Możesz z łatwością [tworzyć alerty](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) przy użyciu danych pochodzących z zasobów usługi Azure SQL Database. Poniżej przedstawiono niektóre przydatne [wyszukiwanie w dzienniku](log-analytics-log-searches.md) zapytań, które można używać z alertu dziennika:



*Wysoki poziom jednostek DTU na bazę danych Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Wysoki poziom jednostek DTU w puli elastycznej bazy danych Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Kolejne kroki

- Użyj [wyszukiwań w dziennikach](log-analytics-log-searches.md) w usłudze Log Analytics, aby wyświetlić szczegółowe dane usługi Azure SQL.
- [Tworzenie własnych pulpitów nawigacyjnych](log-analytics-dashboards.md) Pokazywanie danych Azure SQL.
- [Tworzenie alertów](log-analytics-alerts.md) po wystąpieniu określonych zdarzeń usługi Azure SQL.
