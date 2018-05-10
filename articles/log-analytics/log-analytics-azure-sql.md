---
title: Azure rozwiązania analizy SQL w Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązania analizy SQL Azure ułatwia zarządzanie bazami danych Azure SQL.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorowanie bazy danych SQL Azure przy użyciu analiza SQL Azure (wersja zapoznawcza) w analizy dzienników

![Symbol SQL Analytics Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Rozwiązanie analiza SQL Azure w Azure Log Analytics zbiera i wizualizuje ważne metryki wydajności usługi SQL Azure. Przy użyciu metryk, które należy zebrać z rozwiązania, można utworzyć niestandardowe reguły monitorowania i alertów. Można monitorować bazy danych SQL Azure i metryki elastycznej puli w wielu subskrypcji platformy Azure i elastyczne pule i ich wizualizacji. Rozwiązanie pomaga również w celu identyfikowania problemów w każdej warstwie stosu aplikacji.  Używa [diagnostyki Azure metryki](log-analytics-azure-storage.md) wraz z widoków analizy dzienników do prezentowania danych dotyczących wszystkich baz danych Azure SQL i pul elastycznych w jednym obszarze roboczym analizy dzienników.

Obecnie to rozwiązanie w wersji zapoznawczej obsługuje maksymalnie 150 000 baz danych SQL Azure i 5000 SQL pule elastyczne według obszaru roboczego.

Rozwiązanie analiza SQL Azure, takich jak inne dostępne dla analizy dzienników pomaga monitorować i otrzymywanie powiadomień o kondycji zasobów platformy Azure — w takim przypadku baza danych SQL Azure. Baza danych SQL Azure Microsoft to usługa skalowalne relacyjnej bazy danych, który umożliwia znanych funkcji programu SQL Server podobny do aplikacji działających w chmurze Azure. Analizy dzienników umożliwia zbieranie, skorelowania i wizualizacji danych strukturalnych i bez struktury.

Omówienie praktyczne przy użyciu rozwiązania analizy SQL Azure oraz typowe scenariusze użytkowania Zobacz osadzonego wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Połączone źródła

Rozwiązania analizy SQL Azure nie używa agentów się połączyć z usługą analizy dzienników.

W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Pomoc techniczna | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie | Bezpośrednie agentów systemu Windows nie są używane przez to rozwiązanie. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Bezpośrednie agentów systemu Linux nie są używane przez to rozwiązanie. |
| [Grupa zarządzania programu SCOM](log-analytics-om-agents.md) | Nie | Bezpośrednie połączenie z agenta programu SCOM Log Analytics nie jest używany przez rozwiązanie. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Analiza dzienników nie odczytuje dane z konta magazynu. |
| [Diagnostyka Azure](log-analytics-azure-storage.md) | Yes | Metryka i dziennika danych Azure są wysyłane do analizy dzienników bezpośrednio przez platformę Azure. |

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć jedną dla [wolnego](https://azure.microsoft.com/free/).
- Obszar roboczy analizy dzienników. Można użyć istniejącego, lub możesz [Utwórz nową](log-analytics-quick-create-workspace.md) przed rozpoczęciem korzystania z tego rozwiązania.
- Włącz diagnostyki Azure dla baz danych Azure SQL i pule elastyczne i [je skonfigurować do wysyłania danych do analizy dzienników](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Konfigurowanie

Wykonaj poniższe kroki, aby dodać do swojego obszaru roboczego rozwiązania analizy SQL Azure.

1. Dodaj rozwiązanie analiza SQL Azure do swojego obszaru roboczego z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. W portalu Azure kliknij **Utwórz zasób** > **monitorowanie i zarządzanie**.  
    ![Monitorowanie i zarządzanie](./media/log-analytics-azure-sql/monitoring-management.png)
3. W **monitorowanie i zarządzanie** listy kliknij **zobaczyć wszystkie**.
4. W **zalecane** kliknij **więcej**, a następnie na liście nowy Znajdź **analiza SQL Azure (wersja zapoznawcza)** i wybierz go.  
    ![Rozwiązania analizy SQL Azure](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. W **analiza SQL Azure (wersja zapoznawcza)** obszaru, kliknij przycisk **Utwórz**.  
    ![Tworzenie](./media/log-analytics-azure-sql/portal-create.png)
6. W **Utwórz nowe rozwiązanie** obszaru obszar roboczy, który ma zostać dodany do rozwiązania, a następnie kliknij przycisk **Utwórz**.  
    ![Dodaj do obszaru roboczego](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Aby skonfigurować wiele subskrypcji platformy Azure

Aby obsługiwać wiele subskrypcji, należy użyć skryptu programu PowerShell z [rejestrowania metryki zasobów Azure włączyć przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Podaj identyfikator zasobu obszaru roboczego jako parametr podczas wykonywania skryptu do wysyłania danych diagnostycznych z zasobów w jedną subskrypcją platformy Azure do obszaru roboczego w innej subskrypcji platformy Azure.

**Przykład**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania do swojego obszaru roboczego kafelka analiza SQL Azure jest dodawany do obszaru roboczego i wyświetlone w omówieniu. Kafelek pokazuje liczbę baz danych Azure SQL i Azure SQL pule elastyczne podłączonymi do rozwiązania.

![Kafelek SQL Analytics Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych analiz SQL Azure

Polecenie **analiza SQL Azure** Kafelek, aby otworzyć pulpitu nawigacyjnego Azure SQL Analytics. Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Dla różnych perspektyw pracę należy włączyć odpowiednie metryki lub dzienniki na zasobów SQL, aby być przesłana strumieniowo do obszaru roboczego analizy dzienników Azure.

![Omówienie Analytics Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Wybranie dowolnego elementu Kafelki, zostanie otwarty raport przechodzenia do określonych perspektywy. Po wybraniu perspektywy raport przechodzenia jest otwarty.

![Limity czasu Analytics Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Dla każdej perspektywy umożliwia podsumowania subskrypcji, serwer puli elastycznej i poziom bazy danych. Ponadto każdy perspektywy zawiera perspektywę specyficzne dla raportu po prawej stronie. Wybierając z listy subskrypcję, serwera, puli lub bazy danych nadal rozwijanie szczegółów.

| Perspektywa | Opis |
| --- | --- |
| Zasób według typu | Perspektywa obliczającej wszystkie zasoby, które są monitorowane. Przechodzenie do szczegółów zawiera podsumowanie metryki DTU i GB. |
| Insights | Udostępnia hierarchiczna przechodzenia do inteligentnego szczegółowych informacji. Dowiedz się więcej o inteligentnego szczegółowych informacji. |
| Błędy | Zapewnia hierarchiczna przechodzenia do błędów SQL, które wystąpiły w bazach danych. |
| Limity czasu | Udostępnia hierarchiczna przechodzenia do przekroczenia limitu czasu SQL, które wystąpiły w bazach danych. |
| Blokowanie | Udostępnia hierarchiczna przechodzenia do blokowanie SQL, które wystąpiły w bazach danych. |
| Czeka bazy danych | Udostępnia hierarchiczna przechodzenia do SQL oczekiwania statystyk na poziomie bazy danych. Zawiera podsumowania łączny czas oczekiwania i czas oczekiwania na typ oczekiwania. |
| czas trwania kwerendy | Udostępnia hierarchiczna przechodzenia do statystyk wykonywania zapytań, takich jak czas trwania kwerendy, użycie procesora CPU, użycia we/wy danych, użycie dziennika we/wy. |
| Kwerenda | Udostępnia hierarchiczna przechodzenia do statystyki oczekiwania zapytania według kategorii oczekiwania. |

### <a name="intelligent-insights-report"></a>Raport Insights inteligentnego

Baza danych SQL Azure [Insights inteligentnego](../sql-database/sql-database-intelligent-insights.md) informuje Cię o tym, co dzieje z wydajność bazy danych. Wszystkie zebrane inteligentnego szczegółowe informacje można wizualizowane i dostępne za pośrednictwem perspektywy szczegółowych informacji.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Raporty dotyczące puli elastycznej i bazy danych

Zarówno pule elastyczne, jak i bazy danych ma swoje własne szczegółowymi raportami, w których są wyświetlane wszystkie dane, które są zbierane dla zasobu w określonym czasie.

![Baza danych Azure SQL analityka](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pula elastyczna Analytics Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytania

Za pomocą zapytania czeka perspektyw i czas trwania kwerendy można skorelować wydajności wszelkie zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność zapytań w różnych baz danych i ułatwia identyfikowanie baz danych, które wybrane zapytanie również i tych, które są przetwarzane wolno wykonywać.

![Zapytania analityczne Azure SQL](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analizuj dane i tworzyć alerty

Możesz z łatwością [tworzyć alerty](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) o dane pochodzące z zasobów bazy danych SQL Azure. Oto niektóre przydatne [wyszukiwania dziennika](log-analytics-log-searches.md) zapytania, których można użyć alert w dzienniku:



*Wysoka DTU na bazę danych Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Wysoka jednostek DTU w puli elastycznej bazy danych Azure SQL*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Kolejne kroki

- Użyj [dziennik wyszukiwania](log-analytics-log-searches.md) w analizy dzienników w celu wyświetlenia szczegółowych danych Azure SQL.
- [Tworzenie własnych pulpity nawigacyjne](log-analytics-dashboards.md) przedstawiający danych Azure SQL.
- [Tworzenie alertów](log-analytics-alerts.md) po wystąpieniu określonych zdarzeń Azure SQL.
