---
title: Azure SQL Analytics rozwiązanie w Azure Monitor | Microsoft Docs
description: Rozwiązanie Azure SQL Analytics ułatwia zarządzanie bazami danych Azure SQL Database
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 12/17/2018
ms.reviewer: carlrab
ms.openlocfilehash: 75e8cffea08c7db4526c647a32ed92be2d1779f1
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899125"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitoruj Azure SQL Database przy użyciu Azure SQL Analytics (wersja zapoznawcza)

![Symbol Azure SQL Analytics](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmurowego służące do monitorowania wydajności baz danych SQL Azure, pul elastycznych i wystąpień zarządzanych na dużą skalę i w wielu subskrypcjach za pośrednictwem jednego okienka Glass. Usługa ta zbiera i wizualizuje ważne metryki wydajności bazy danych Azure SQL Database dzięki wbudowanym funkcjom analizy w celu rozwiązywania problemów z wydajnością.

Korzystając z metryk zbieranych z rozwiązaniem, można tworzyć niestandardowe reguły monitorowania i alerty. Rozwiązanie pomaga identyfikować problemy w każdej warstwie stosu aplikacji. Używa metryk diagnostycznych platformy Azure wraz z Azure Monitor widokami do prezentowania danych o wszystkich bazach danych SQL Azure, elastycznych pulach i bazach danych w zarządzanych wystąpieniach w jednym Log Analytics obszarze roboczym. Azure Monitor pomaga zbierać, skorelować i wizualizować dane ze strukturą i bez struktury.

Aby dowiedzieć się więcej na temat korzystania z rozwiązania Azure SQL Analytics i dla typowych scenariuszy użycia, zobacz osadzony film wideo:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Połączone źródła

Azure SQL Analytics to rozwiązanie do monitorowania tylko w chmurze obsługujące przesyłanie strumieniowe danych telemetrycznych diagnostyki na potrzeby usługi Azure SQL Database: pojedyncze, w puli i zarządzane bazy danych wystąpień. Ponieważ rozwiązanie nie używa agentów do łączenia się z Azure Monitor, rozwiązanie nie obsługuje monitorowania SQL Server hostowanych lokalnie lub na maszynach wirtualnych, zobacz poniższą tabelę zgodności.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Ustawienia diagnostyki](../platform/diagnostic-settings.md) | **Tak** | Dane dotyczące metryk i dzienników usługi Azure są wysyłane do dzienników Azure Monitor bezpośrednio przez platformę Azure. |
| [Konto usługi Azure Storage](../platform/collect-azure-metrics-logs.md) | Nie | Azure Monitor nie odczytuje danych z konta magazynu. |
| [Agenci dla systemu Windows](../platform/agent-windows.md) | Nie | Bezpośrednie agenci systemu Windows nie są używani przez rozwiązanie. |
| [Agenci dla systemu Linux](../learn/quick-collect-linux-computer.md) | Nie | Bezpośredni agenci systemu Linux nie są używani przez rozwiązanie. |
| [System Center Operations Manager grupy zarządzania](../platform/om-agents.md) | Nie | Połączenie bezpośrednie od agenta Operations Manager do Azure Monitor nie jest używane przez rozwiązanie. |

## <a name="configuration"></a>Konfigurowanie
Aby dodać rozwiązanie Azure SQL Analytics (wersja zapoznawcza) do obszaru roboczego Log Analytics, Użyj procesu opisanego w temacie [Dodawanie rozwiązań Azure monitor z Galeria rozwiązań](../../azure-monitor/insights/solutions.md) .

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Skonfiguruj usługi Azure SQL Database, elastyczne pule i wystąpienia zarządzane, aby przesyłać strumieniowo dane telemetryczne diagnostyki

Po utworzeniu rozwiązania Azure SQL Analytics w obszarze roboczym należy **skonfigurować wszystkie** zasoby, które mają być monitorowane, aby przesyłać strumieniowo dane telemetryczne diagnostyki do rozwiązania. Postępuj zgodnie ze szczegółowymi instrukcjami na tej stronie:

- Włącz Diagnostyka Azure na potrzeby usługi Azure SQL Database, aby [przesyłać dane telemetryczne diagnostyki do Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

Powyższa Strona zawiera również instrukcje dotyczące włączania obsługi monitorowania wielu subskrypcji platformy Azure z jednego obszaru roboczego Azure SQL Analytics jako pojedynczego okienka.

## <a name="using-the-solution"></a>Użycie rozwiązania

Po dodaniu rozwiązania do obszaru roboczego kafelek Azure SQL Analytics zostanie dodany do obszaru roboczego i zostanie wyświetlony w obszarze przegląd. Wybierz łącze Wyświetl podsumowanie, aby załadować zawartość kafelka.

![Kafelek podsumowania Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Po załadowaniu kafelek pokazuje liczbę baz danych usługi Azure SQL, pul elastycznych, wystąpień zarządzanych i baz danych w zarządzanych wystąpieniach, z których rozwiązanie otrzymuje dane telemetryczne diagnostyki.

![Kafelek Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Rozwiązanie zapewnia dwa osobne widoki — jeden do monitorowania baz danych SQL Azure i pul elastycznych, a także drugi widok do monitorowania wystąpienia zarządzanego oraz baz danych w zarządzanych wystąpieniach.

Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla baz danych i pul elastycznych usługi Azure SQL, kliknij górną część kafelka. Aby wyświetlić pulpit nawigacyjny monitorowania Azure SQL Analytics dla wystąpienia zarządzanego i bazy danych w wystąpieniu zarządzanym, kliknij dolną część kafelka.

### <a name="viewing-azure-sql-analytics-data"></a>Wyświetlanie danych Azure SQL Analytics

Pulpit nawigacyjny zawiera przegląd wszystkich baz danych, które są monitorowane przy użyciu różnych perspektyw. Aby różne perspektywy działały, należy włączyć odpowiednie metryki lub dzienniki w zasobach SQL, aby były przesyłane strumieniowo do Log Analytics obszaru roboczego.

Należy pamiętać, że jeśli niektóre metryki lub dzienniki nie są przesyłane strumieniowo do Azure Monitor, kafelki w rozwiązaniu nie są wypełniane informacjami o monitorowaniu.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Widok Azure SQL Database i elastycznej puli

Po wybraniu Azure SQL Analytics kafelka bazy danych zostanie wyświetlony pulpit nawigacyjny monitorowanie.

![Przegląd Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview.png)

Wybranie dowolnego kafelka spowoduje otwarcie raportu przechodzenia do szczegółów w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport przechodzenia do szczegółów.

![Limity czasu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics.png)

Każda perspektywa w tym widoku zawiera podsumowanie dotyczące subskrypcji, serwera, elastycznej puli i poziomu bazy danych. Ponadto każda perspektywa pokazuje perspektywę specyficzną dla raportu po prawej stronie. Wybieranie subskrypcji, serwera, puli lub bazy danych z listy kontynuuje przechodzenie do szczegółów.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Wystąpienie zarządzane i bazy danych w widoku wystąpienia zarządzanego

Po wybraniu Azure SQL Analytics kafelka dla baz danych zostanie wyświetlony pulpit nawigacyjny monitorowanie.

![Przegląd Azure SQL Analytics](./media/azure-sql/azure-sql-sol-overview-mi.png)

Wybranie dowolnego kafelka spowoduje otwarcie raportu przechodzenia do szczegółów w określonej perspektywie. Po wybraniu perspektywy zostanie otwarty raport przechodzenia do szczegółów.

Wybór widoku wystąpienia zarządzanego, zawiera szczegółowe informacje dotyczące użycia wystąpienia zarządzanego, baz danych, które zawiera, oraz dane telemetryczne dla zapytań wykonywanych w ramach wystąpienia.

![Limity czasu Azure SQL Analytics](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektywy

W poniższej tabeli przedstawiono perspektywy obsługiwane przez dwie wersje pulpitu nawigacyjnego, jedną dla usługi Azure SQL Database i pul elastycznych, a druga dla wystąpienia zarządzanego.

| Perspektywy | Opis | Obsługa SQL Database i pul elastycznych | Obsługa wystąpienia zarządzanego |
| --- | ------- | ----- | ----- |
| Zasób według typu | Perspektywa, która zlicza wszystkie monitorowane zasoby. | Tak | Tak |
| Szczegółowe informacje | Zapewnia hierarchiczne przechodzenie do szczegółów w Intelligent Insights wydajności. | Tak | Tak |
| Błędy | Zawiera hierarchiczne przechodzenie do szczegółów błędów SQL, które wystąpiły w bazach danych. | Tak | Tak |
| Limity czasu | Zawiera hierarchiczne przekroczenia limitów czasu SQL, które wystąpiły w bazach danych. | Tak | Nie |
| Bloki | Zawiera hierarchiczne przechodzenie do szczegółów w blokach SQL, które wystąpiły w bazach danych. | Tak | Nie |
| Oczekiwanie na bazę danych | Zawiera hierarchiczne przechodzenie do szczegółów na poziomie bazy danych. Zawiera podsumowanie łącznego czasu oczekiwania i czasu oczekiwania na typ oczekiwania. |Tak | Tak |
| Czas trwania zapytania | Zawiera hierarchiczne przechodzenie do szczegółów w statystyce wykonywania zapytania, takich jak czas trwania zapytania, użycie procesora CPU, użycie operacji we/wy danych, użycie operacji we/wy dziennika. | Tak | Tak |
| Oczekiwanie na zapytanie | Zawiera hierarchiczne przechodzenie do szczegółów w celu uwzględnienia statystyk oczekiwania na zapytanie według kategorii oczekiwania. | Tak | Tak |

### <a name="intelligent-insights-report"></a>Raport Intelligent Insights

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) pozwala wiedzieć, co dzieje się z wydajnością wszystkich baz danych SQL Azure. Wszystkie zebrane Intelligent Insights można wizualizować i uzyskiwać do nich dostęp za pomocą perspektywy wglądu w szczegółowe dane.

![Azure SQL Analytics szczegółowych informacji](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Raporty puli elastycznej i bazy danych

Zarówno pule elastyczne, jak i bazy danych SQL mają własne konkretne raporty pokazujące wszystkie dane zbierane dla zasobu w określonym czasie.

![Baza danych Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Elastyczna Pula usługi Azure SQL](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Raporty zapytań

Przez czas trwania zapytania i zapytanie czekają na perspektywy, można skorelować wydajność dowolnego zapytania za pomocą raportu zapytania. Ten raport porównuje wydajność zapytań między różnymi bazami danych i ułatwia lokalizowanie baz danych, które wykonują wybrane zapytanie w zależności od tego, które są powolne.

![Zapytania Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Uprawnienia

Aby korzystać z Azure SQL Analytics, użytkownicy muszą mieć przyznane minimalne uprawnienia roli czytelnik na platformie Azure. Ta rola nie zezwala jednak użytkownikom na wyświetlanie tekstu zapytania ani wykonywanie żadnych akcji dostrajania automatycznego. Bardziej ograniczając role na platformie Azure, które zezwalają na używanie rozwiązania do pełnego zakresu, są właścicielami, współautor, współautor bazy danych SQL lub współautorem SQL Server. Warto również rozważyć utworzenie roli niestandardowej w portalu z określonymi uprawnieniami wymaganymi tylko Azure SQL Analytics i bez dostępu do zarządzania innymi zasobami.

### <a name="creating-a-custom-role-in-portal"></a>Tworzenie roli niestandardowej w portalu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uznając, że niektóre organizacje wymuszają ścisłe kontrole uprawnień na platformie Azure, znajdź następujący skrypt programu PowerShell umożliwiający utworzenie roli niestandardowej "operator monitorowania SQL Analytics" w Azure Portal z minimalnymi uprawnieniami do odczytu i zapisu wymaganymi przez Użyj Azure SQL Analytics do jego pełnego zakresu.

Zastąp element "{Subscription}" w poniższym skrypcie IDENTYFIKATORem subskrypcji platformy Azure i uruchom skrypt zalogowany jako rola właściciela lub współautora na platformie Azure.

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

Po utworzeniu nowej roli Przypisz tę rolę każdemu użytkownikowi, który będzie musiał udzielić uprawnień niestandardowych do korzystania z Azure SQL Analytics.

## <a name="analyze-data-and-create-alerts"></a>Analizowanie danych i tworzenie alertów

Analiza danych w Azure SQL Analytics opiera się na [log Analytics języku](../log-query/get-started-queries.md) dla niestandardowych zapytań i raportowania. Znajdź opis dostępnych danych zebranych z zasobu bazy danych na potrzeby niestandardowych zapytań w [dostępnych metrykach i dziennikach](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Automatyczne alerty w rozwiązaniu są oparte na pisaniu Log Analytics kwerendy, która wyzwala alert po spełnieniu warunku. Znajdź poniżej kilka przykładów na Log Analytics zapytaniach, na których można skonfigurować alerty w rozwiązaniu.

### <a name="creating-alerts-for-azure-sql-database"></a>Tworzenie alertów dla Azure SQL Database

Możesz łatwo [tworzyć alerty](../platform/alerts-metric.md) z danymi pochodzącymi z zasobów Azure SQL Database. Oto kilka przydatnych [zapytań dzienników](../log-query/log-query-overview.md) , których można użyć w przypadku alertu dziennika:

#### <a name="high-cpu-on-azure-sql-database"></a>Wysoki procesor CPU w Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Wymagania wstępne związane z konfigurowaniem tego alertu polega na tym, że monitorowane bazy danych przesyłają Podstawowe metryki do rozwiązania.
> - Zastąp wartość Metricname cpu_percent z dtu_consumption_percent, aby uzyskać w zamian wysokie wyniki jednostek DTU.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Duże użycie procesora na Azure SQL Database elastycznych pul

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Wymagania wstępne związane z konfigurowaniem tego alertu polega na tym, że monitorowane bazy danych przesyłają Podstawowe metryki do rozwiązania.
> - Zastąp wartość Metricname cpu_percent z dtu_consumption_percent, aby uzyskać w zamian wysokie wyniki jednostek DTU.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Magazyn Azure SQL Database w średniej powyżej 95% w ciągu ostatnich 1 HR

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
> - Wymagania wstępne związane z konfigurowaniem tego alertu polega na tym, że monitorowane bazy danych przesyłają Podstawowe metryki do rozwiązania.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w niektórych bazach danych. Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w definicji time_range.
> - Dane wyjściowe to lista zasobów bazy danych, które znajdują się powyżej storage_threshold w definicji time_range.

#### <a name="alert-on-intelligent-insights"></a>Alert dotyczący usługi Intelligent Insights

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
> - Wymagania wstępne dotyczące konfigurowania tego alertu to, że monitorowane bazy danych przesyłają do rozwiązania dziennik diagnostyki SQLInsights.
> - To zapytanie wymaga skonfigurowania reguły alertu tak, aby uruchamiała się z tą samą częstotliwością jak alert_run_interval, aby uniknąć zduplikowanych wyników. Reguła powinna być skonfigurowana w taki sposób, aby nie wyłączać alertu, gdy istnieją wyniki (> 0 wyników) z zapytania.
> - Dostosuj alert_run_interval, aby określić zakres czasu, aby sprawdzić, czy warunek wystąpił w bazach danych skonfigurowanych do przesyłania strumieniowego dziennika SQLInsights do rozwiązania.
> - Dostosuj insights_string, aby przechwytywać dane wyjściowe analizy głównej przyczyny usługi Insights. Jest to ten sam tekst wyświetlany w interfejsie użytkownika rozwiązania, którego możesz użyć z istniejących szczegółowych informacji. Możesz też użyć poniższego zapytania, aby zobaczyć tekst wszystkich szczegółowych informacji generowanych w ramach subskrypcji. Użyj danych wyjściowych zapytania, aby zebrać unikatowe ciągi do konfigurowania alertów w usłudze Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Tworzenie alertów dla wystąpienia zarządzanego

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
> - Przed skonfigurowaniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats do rozwiązania.
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
> - Przed skonfigurowaniem tego alertu w monitorowanym wystąpieniu zarządzanym jest włączone przesyłanie strumieniowe dziennika ResourceUsageStats do rozwiązania.
> - To zapytanie wymaga skonfigurowania reguły alertu do wyzwolenia alertu, gdy istnieją wyniki (> 0 wyników) z zapytania, co oznacza, że warunek istnieje w wystąpieniu zarządzanym. Dane wyjściowe są średnim zużyciem użycia procesora CPU w określonym okresie w wystąpieniu zarządzanym.

### <a name="pricing"></a>Cennik

Gdy rozwiązanie jest bezpłatne do użycia, dane telemetryczne diagnostyki przekraczają wolne jednostki przystosowane do pozyskiwania danych w każdym miesiącu, zobacz [log Analytics Cennik](https://azure.microsoft.com/pricing/details/monitor). Bezpłatne jednostki pozyskiwania danych zapewniają bezpłatne monitorowanie kilku baz danych miesięcznie. Należy pamiętać, że bardziej aktywne bazy danych z większymi obciążeniami zwiększają ilości danych i bezczynnych baz danych. Możesz łatwo monitorować użycie pozyskiwania danych w rozwiązaniu, wybierając obszar roboczy OMS w menu nawigacji Azure SQL Analytics, a następnie wybierając pozycję użycie i szacowane koszty.

## <a name="next-steps"></a>Następne kroki

- Użyj [zapytań dzienników](../log-query/log-query-overview.md) w Azure monitor, aby wyświetlić szczegółowe dane SQL platformy Azure.
- [Utwórz własne pulpity nawigacyjne](../learn/tutorial-logs-dashboards.md) pokazujące dane usługi Azure SQL.
- [Utwórz alerty](../platform/alerts-overview.md) w przypadku wystąpienia określonych zdarzeń SQL platformy Azure.
