---
title: Rejestrowanie diagnostyczne i metryki usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć diagnostykę w usłudze Azure SQL Database do przechowywania informacji na temat wykorzystania zasobów i statystyk wykonywania zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: fe53dd4419c06d376a1cc46db0d2621ccbc06f23
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548645"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metryki usługi Azure SQL Database i rejestrowania diagnostycznego

W tym temacie dowiesz się, jak skonfigurować rejestrowanie dane diagnostyczne i telemetryczne usługi Azure SQL Database za pomocą witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST usługi Azure Monitor i szablon usługi Azure Resource Manager. Te funkcje diagnostyki może służyć do pomiaru wykorzystania zasobów i statystyk wykonywania zapytań. 

Pojedyncze bazy danych, bazy danych w puli w elastycznej puli i bazy danych wystąpienia w można wystąpienia zarządzanego przesyłanie strumieniowe dzienników metryki i Diagnostyka ułatwiają monitorowanie wydajności. Można skonfigurować bazę danych do przesłania użycia zasobów, pracowników i sesji oraz łączność z jedną z następujących zasobów platformy Azure:

- **Usługa Azure SQL Analytics**: Aby uzyskać inteligentnego monitorowania baz danych Azure SQL, które zawiera raporty dotyczące wydajności, alerty i zalecenia dotyczące ograniczania ryzyka.
- **Usługa Azure Event Hubs**: zintegrować telemetrycznych usługi SQL Database za pomocą Twojego niestandardowego rozwiązania do monitorowania lub potokami.
- **Usługa Azure Storage**: w celu archiwizowania ogromnych ilości danych telemetrycznych za ułamek ceny.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

Aby uzyskać więcej informacji na temat kategorii metryk i dzienników, obsługiwane przez różne usługi platformy Azure zobacz:

- [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Przegląd dzienników diagnostyki platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Ten artykuł zawiera wskazówki ułatwiające włączenie dane diagnostyczne i telemetryczne dla baz danych, pul elastycznych i wystąpienia zarządzanego Azure SQL. Również może pomóc Ci zrozumieć, jak skonfigurować usługi Azure SQL Analytics jako narzędzia do monitorowania w celu wyświetlania dane diagnostyczne i telemetryczne bazy danych.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Włączanie rejestrowania dane diagnostyczne i telemetryczne

Można włączyć i zarządzać metryki i Diagnostyka danych telemetrycznych rejestrowanie przy użyciu jednej z następujących metod:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST usługi Azure Monitor
- Szablon usługi Azure Resource Manager

Po włączeniu, metryki i Diagnostyka, rejestrowanie, należy określić obiekt docelowy zasobów platformy Azure zbieranie danych telemetrycznych diagnostyki. Dostępne opcje to:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Można uaktywniać nowego zasobu platformy Azure, lub wybierz istniejący zasób. Po wybraniu zasobu za pomocą **ustawień diagnostycznych** opcji, należy określić dane, które mają być zbierane.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Obsługiwane rejestrowania diagnostycznego dla baz danych Azure SQL i wystąpienie bazy danych

Włącz metryki i Diagnostyka rejestrowania w bazach danych SQL — nie są włączone domyślnie.

Można skonfiguruj baz danych Azure SQL i wystąpienie bazy danych, aby zebrać następujące dane diagnostyczne i telemetryczne:

| Monitorowanie telemetrii dla baz danych | Pojedynczą bazę danych i obsługi technicznej baza danych w puli | Wystąpienie obsługi bazy danych |
| :------------------- | ----- | ----- |
| [Wszystkie metryki](#all-metrics): Zawiera procent jednostek DTU/użycia procesora CPU, limit jednostek DTU/procesora CPU, fizycznych procent odczytanych danych, dzienników zapisu procent, Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu i procent użycia magazynu XTP. | Yes | Nie |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Zawiera informacje dotyczące zapytań środowiska uruchomieniowego statystyki, takie jak użycie procesora CPU i Statystyki czasu trwania zapytań. | Yes | Yes |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Zawiera informacje o statystyki oczekiwania zapytań (co zapytań oczekiwany) są takie procesora CPU, DZIENNIKÓW i blokowanie. | Yes | Yes |
| [Błędy](#errors-dataset): Zawiera informacje na temat błędów SQL w bazie danych. | Yes | Yes |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Zawiera informacje o ile czasu bazy danych poświęcony na oczekiwanie na oczekiwania różnych typów. | Yes | Nie |
| [Limity czasu](#time-outs-dataset): Zawiera informacje dotyczące limitów czasu w bazie danych. | Yes | Nie |
| [Bloki](#blockings-dataset): Zawiera informacje o blokowaniu zdarzeń w bazie danych. | Yes | Nie |
| [Zakleszczenie](#deadlocks-dataset): Zawiera informacje o zdarzeniach zakleszczenie w bazie danych. | Yes | Nie |
| [AutomaticTuning](#automatic-tuning-dataset): Zawiera informacje dotyczące automatycznego zalecenia dotyczące dostrajania bazy danych. | Yes | Nie |
| [SQLInsights](#intelligent-insights-dataset): Zawiera inteligentne wgląd w wydajność bazy danych. Aby dowiedzieć się więcej, zobacz [Intelligent Insights](sql-database-intelligent-insights.md). | Yes | Yes |

> [!IMPORTANT]
> Pule elastyczne i zarządzanych wystąpień przez mają swoje własne dane oddzielne diagnostyczne i telemetryczne z baz danych, które zawierają. Jest to należy zwrócić uwagę jako dane diagnostyczne i telemetryczne skonfigurowano oddzielnie dla każdej z tych zasobów, zgodnie z opisem poniżej.

> [!NOTE]
> Dzienniki inspekcji zabezpieczeń i SQLSecurityAuditEvents nie można włączyć przy użyciu ustawień diagnostycznych bazy danych (chociaż wyświetlanie na ekranie). Aby włączyć strumieniowe przesyłanie dzienników inspekcji, zobacz [konfigurowania inspekcji dla bazy danych](sql-database-auditing.md#subheading-2), i [inspekcji dzienników w dzienniki usługi Azure Monitor i Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Azure Portal

Możesz użyć **ustawień diagnostycznych** menu dla każdego pojedynczego puli lub wystąpienia bazy danych w witrynie Azure portal skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne. Ponadto telemetrii diagnostyczne można również skonfigurować osobno dla kontenerów bazy danych: pule elastyczne i wystąpienia zarządzanego. Można ustawić następujące miejsc docelowych, aby przesyłać strumieniowo dane diagnostyczne i telemetryczne: Dzienniki usługi Azure Storage, Azure Event Hubs i Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla pul elastycznych

   ![Ikona puli elastycznej](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Można wybrać zasób puli elastycznej zebrać następujące dane telemetryczne diagnostyki:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Elastyczna pula** | [Wszystkie metryki](sql-database-metrics-diag-logging.md#all-metrics) zawiera procent eDTU/użycia procesora CPU, limit jednostek eDTU/procesora CPU, fizycznych procent odczytanych danych, dzienników zapisu procent, procent sesji, procent pracowników, magazynu, procent użycia magazynu, limit przestrzeni dyskowej i procent użycia magazynu XTP. |

Aby skonfigurować, przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla pul elastycznych i baz danych w elastycznej puli, musisz oddzielnie skonfigurować **zarówno** z następujących czynności:

- Włączanie przesyłania strumieniowego z dane diagnostyczne i telemetryczne dla puli elastycznej, **i**
- Włączanie przesyłania strumieniowego z dane diagnostyczne i telemetryczne dla każdej bazy danych w puli elastycznej

Jest to spowodowane pula elastyczna jest kontenerem bazy danych przy użyciu własnej telemetrii są oddzielone od danych telemetrycznych poszczególnych baz danych.

Aby włączyć przesyłanie strumieniowe dane diagnostyczne i telemetryczne w przypadku zasobów puli elastycznej, wykonaj następujące kroki:

1. Przejdź do **puli elastycznej** zasobów w witrynie Azure portal.
1. Wybierz **ustawień diagnostycznych**.
1. Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** edytować poprzedniego ustawienia.

   ![Włącz diagnostykę dla pul elastycznych](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Wprowadź nazwę ustawienia do własnych celów.
1. Wybierz zasób docelowy dla przesyłania strumieniowego danych diagnostycznych: **Archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**.
1. Dla usługi log analytics wybierz **Konfiguruj** i Utwórz nowy obszar roboczy, wybierając **+ Utwórz nowy obszar roboczy**, lub wybierz istniejący obszar roboczy.
1. Zaznacz pole wyboru dla puli elastycznej dane diagnostyczne i telemetryczne: **AllMetrics**.
   ![Skonfigurować diagnostykę dla pul elastycznych](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Wybierz pozycję **Zapisz**.
1. Ponadto skonfigurować, przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla każdej bazy danych w puli elastycznej, którą chcesz monitorować, wykonując kroki opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania dane diagnostyczne i telemetryczne dla puli elastycznej, również należy skonfigurować dane diagnostyczne i telemetryczne dla każdej bazy danych w puli elastycznej, zgodnie z opisem poniżej. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla pojedynczej bazy danych lub bazę danych w puli elastycznej

   ![Ikona bazy danych SQL](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Aby włączyć przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla jednego lub pulami baz danych, wykonaj następujące kroki:

1. Przejdź do usługi Azure **bazy danych SQL** zasobów.
1. Wybierz **ustawień diagnostycznych**.
1. Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** edytować poprzedniego ustawienia.
   - Możesz utworzyć maksymalnie trzy równoległych połączeń dane diagnostyczne i telemetryczne strumienia.
   - Wybierz **+ Dodaj ustawienie diagnostyczne** skonfigurować transmisję strumieniową równoległego danych diagnostycznych do wielu zasobów.

   ![Włącz diagnostykę dla pojedynczego, puli lub wystąpienie bazy danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Wprowadź nazwę ustawienia do własnych celów.
1. Wybierz zasób docelowy dla przesyłania strumieniowego danych diagnostycznych: **Archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**.
1. Standardowe, oparte na zdarzeniach środowisko monitorowania wybierz następujące pola wyboru dla bazy danych diagnostycznych dzienników telemetrii: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **błędy** , **DatabaseWaitStatistics**, **przekroczeń limitu czasu**, **bloki**, i **zakleszczenia**.
1. Dla zaawansowanych, jedną minutę na podstawie środowiska monitorowania, zaznacz pole wyboru dla **AllMetrics**.
   ![Skonfigurować diagnostykę dla pojedynczej puli i wystąpienia bazy danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Wybierz pozycję **Zapisz**.
1. Powtórz te czynności dla każdej bazy danych, które chcesz monitorować.

> [!NOTE]
> Dzienniki inspekcji zabezpieczeń i SQLSecurityAuditEvents nie można włączyć przy użyciu ustawień diagnostycznych bazy danych (chociaż wyświetlane na ekranie). Aby włączyć strumieniowe przesyłanie dzienników inspekcji, zobacz [konfigurowania inspekcji dla bazy danych](sql-database-auditing.md#subheading-2), i [inspekcji dzienników w dzienniki usługi Azure Monitor i Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Powtórz te czynności dla każdej usługi Azure SQL Database, którą chcesz monitorować.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla wystąpienia zarządzanego

   ![Ikona wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Można skonfigurować wystąpienia zarządzanego zasobu zebrać następujące dane telemetryczne diagnostyki:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Wystąpienie zarządzane** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) zawiera liczbę rdzeni wirtualnych, średni procent użycia procesora CPU, żądań We/Wy, bajtów odczytanych/zapisanych, zarezerwowane miejsca i użyte miejsce do magazynowania. |

Aby skonfigurować, przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla wystąpienia zarządzanego i wystąpienie bazy danych, musisz oddzielnie skonfigurować **zarówno** z następujących czynności:

- Włączanie przesyłania strumieniowego z dane diagnostyczne i telemetryczne dla wystąpienia zarządzanego **i**
- Włączanie przesyłania strumieniowego z dane diagnostyczne i telemetryczne dla każdego wystąpienia bazy danych

Jest to spowodowane wystąpienia zarządzanego jest kontenerem bazy danych przy użyciu własnej telemetrii, niezależnie od poszczególnych wystąpień bazy danych telemetrycznych.

Aby włączyć przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla wystąpienia zarządzanego zasobu, wykonaj następujące kroki:

1. Przejdź do **wystąpienia zarządzanego** zasobów w witrynie Azure portal.
1. Wybierz **ustawień diagnostycznych**.
1. Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** edytować poprzedniego ustawienia.

   ![Włącz diagnostykę dla wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Wprowadź nazwę ustawienia do własnych celów.
1. Wybierz zasób docelowy dla przesyłania strumieniowego danych diagnostycznych: **Archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**.
1. Dla usługi log analytics wybierz **Konfiguruj** i Utwórz nowy obszar roboczy, wybierając **+ Utwórz nowy obszar roboczy**, lub użyj istniejącego obszaru roboczego.
1. Zaznacz pole wyboru dane diagnostyczne i telemetryczne na przykład: **ResourceUsageStats**.
   ![Skonfigurować diagnostykę dla wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Wybierz pozycję **Zapisz**.
1. Ponadto skonfigurować, przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla każdego wystąpienia bazy danych, w ramach wystąpienia zarządzanego, który chcesz monitorować, wykonując kroki opisane w następnej sekcji.

> [!IMPORTANT]
> Oprócz konfigurowania dane diagnostyczne i telemetryczne dla wystąpienia zarządzanego, również należy skonfigurować dane diagnostyczne i telemetryczne dla każdej bazy danych do wystąpienia, zgodnie z opisem poniżej. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla wystąpienia bazy danych

   ![Baza danych wystąpień ikona wystąpienia zarządzanego](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Aby włączyć przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla wystąpienia bazy danych, wykonaj następujące kroki:

1. Przejdź do **wystąpienia bazy danych** zasobów w ramach wystąpienia zarządzanego.
1. Wybierz **ustawień diagnostycznych**.
1. Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** edytować poprzedniego ustawienia.
   - Możesz utworzyć maksymalnie trzech (3) równoległych połączeń dane diagnostyczne i telemetryczne strumienia.
   - Wybierz **+ Dodaj ustawienie diagnostyczne** skonfigurować transmisję strumieniową równoległego danych diagnostycznych do wielu zasobów.

   ![Włącz diagnostykę dla wystąpienia bazy danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Wprowadź nazwę ustawienia do własnych celów.
1. Wybierz zasób docelowy dla przesyłania strumieniowego danych diagnostycznych: **Archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**.
1. Zaznacz pole wyboru, aby uzyskać dane diagnostyczne i telemetryczne bazy danych: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** i **błędy**.
   ![Skonfigurować diagnostykę dla wystąpienia bazy danych](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Wybierz pozycję **Zapisz**.
1. Powtórz te kroki dla każdego wystąpienia bazy danych, który chcesz monitorować.

> [!TIP]
> Powtórz te kroki dla każdego wystąpienia bazy danych, który chcesz monitorować.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Przy użyciu programu PowerShell, można włączyć rejestrowanie diagnostyczne i metryki.

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu docelowego konta magazynu.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="to-configure-multiple-azure-resources"></a>Aby skonfigurować wielu zasobów platformy Azure

Aby obsługiwać wiele subskrypcji, należy użyć skryptu programu PowerShell z [włączyć usługi Azure rejestrowanie metryki dla zasobów przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Podaj identyfikator zasobu obszaru roboczego \<$WSID\> jako parametr podczas wykonywania skryptu `Enable-AzureRMDiagnostics.ps1` do wysyłania danych diagnostycznych z wielu zasobów, do obszaru roboczego.

- Aby uzyskać identyfikator obszaru roboczego \<$WSID\> miejsca docelowego dla danych diagnostycznych, użyj następującego skryptu:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Zastąp \<subID\> z Identyfikatorem subskrypcji, \<RG_NAME\> nazwą grupy zasobów i \<WS_NAME\> nazwą obszaru roboczego.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Przy użyciu wiersza polecenia platformy Azure można włączyć rejestrowanie diagnostyczne i metryki.

> [!NOTE]
> Włączanie rejestrowania diagnostyki skryptów są obsługiwane dla wiersza polecenia platformy Azure w wersji 1.0. Należy pamiętać, że interfejsu wiersza polecenia w wersji 2.0 jest nieobsługiwany w tej chwili.

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu docelowego konta magazynu.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Identyfikator reguły usługi Service Bus to ciąg w formacie:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Uzyskać informacje dotyczące sposobu [zmiany ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Uzyskać informacje dotyczące sposobu [włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream w usłudze Azure SQL Analytics

Usługa Azure SQL Analytics to rozwiązanie w chmurze, który monitoruje wydajność bazy danych Azure SQL, pul elastycznych i zarządzanych wystąpień na dużą skalę i w ramach wielu subskrypcji. Może ona ułatwić gromadzenie i wizualizowanie metryk wydajności usługi Azure SQL Database i ma wbudowaną inteligencją dla Rozwiązywanie problemów z wydajnością.

![Usługi Azure SQL Analytics — Przegląd](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Dzienniki metryki i Diagnostyka bazy danych SQL może być przesyłany strumieniowo do usługi Azure SQL Analytics przy użyciu wbudowanych **wysyłanie do usługi Log Analytics** opcji na karcie Ustawienia diagnostyki w portalu. Można również włączyć usługi log analytics przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="installation-overview"></a>Omówienie instalacji

Można monitorować floty bazy danych SQL z usługą Azure SQL Analytics. Wykonaj następujące czynności:

1. Tworzenie rozwiązania usługi Azure SQL Analytics w portalu Azure Marketplace.
2. Utwórz obszar roboczy monitorowania w rozwiązaniu.
3. Konfigurowanie bazy danych do usługi stream dane diagnostyczne i telemetryczne do obszaru roboczego.

Jeśli używasz pul elastycznych, lub wystąpienia zarządzane, należy skonfigurować dane diagnostyczne i telemetryczne przesyłanych strumieniowo z tych zasobów.

### <a name="create-azure-sql-analytics-resource"></a>Utwórz zasób usługi Azure SQL Analytics

1. Wyszukiwanie usługi Azure SQL Analytics w witrynie Azure Marketplace i zaznacz go.

   ![Wyszukiwanie usługi Azure SQL Analytics w portalu](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Wybierz **Utwórz** na ekran Przegląd rozwiązania.

3. Wypełnij formularz usługi Azure SQL Analytics, dodatkowe informacje, które jest wymagane: Nazwa obszaru roboczego, subskrypcji, grupy zasobów, lokalizację i warstwę cenową.

   ![Konfigurowanie usługi Azure SQL Analytics w portalu](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Wybierz **OK** upewnij się, a następnie wybierz pozycję **Utwórz**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurowanie bazy danych do rekordów dzienników metryki i Diagnostyka

Najprostszym sposobem skonfigurowania, gdzie baz danych rejestrowania metryk jest przy użyciu witryny Azure portal. Jak opisano wcześniej, przejdź do zasobu usługi SQL Database w witrynie Azure portal i wybierz pozycję **ustawień diagnostycznych**.

Jeśli używasz pul elastycznych, lub wystąpienia zarządzane, należy skonfigurować ustawienia diagnostyki w tych zasobów, aby włączyć telemetrię diagnostyki przesyłać strumieniowo do obszaru roboczego.

### <a name="use-the-sql-analytics-solution"></a>Korzystanie z odpowiedniego rozwiązania SQL Analytics

Usługa SQL Analytics jako hierarchiczna pulpit nawigacyjny służy do wyświetlania zasobów usługi SQL Database. Aby dowiedzieć się, jak używać rozwiązania SQL Analytics, zobacz [monitorowanie bazy danych SQL przy użyciu rozwiązania SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

Można przesyłać strumieniowo dzienniki metryki i Diagnostyka bazy danych SQL do usługi Event Hubs za pomocą wbudowanych **Stream do usługi event hub** opcji w witrynie Azure portal. Możesz również włączyć identyfikator reguły usługi Service Bus przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co należy zrobić metryki i Diagnostyka dzienników w usłudze Event Hubs

Po wybrane dane są przesyłane strumieniowo do usługi Event Hubs, jesteś o krok bliżej włączenie zaawansowanych scenariuszach monitorowania. Usługa Event Hubs działa jako drzwi wejściowe dla potoku zdarzeń. Po zebraniu danych do Centrum zdarzeń, mogą zostać przekształcone i zmagazynowane przy użyciu dostawcy analiz w czasie rzeczywistym lub adapter magazynu. Usługa Event Hubs oddziela Wytwarzanie strumienia zdarzeń od użycia tych zdarzeń. W ten sposób odbiorcy zdarzeń mogą uzyskać dostęp do zdarzeń w swoim własnym harmonogramem. Aby uzyskać więcej informacji na temat usługi Event Hubs zobacz:

- [Co to są usługi Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Strumieniowa metryk można użyć w usłudze Event Hubs do:

- **Aby wyświetlić kondycję usługi, aktywną ścieżkę danych strumieniowych do usługi Power BI**. Za pomocą usługi Event Hubs, Stream Analytics i Power BI, można łatwo przekształcać metryki i Diagnostyka danych do niemal w czasie rzeczywistym szczegółowe informacje na usługi platformy Azure. Aby uzyskać omówienie sposobu konfigurowania Centrum zdarzeń, przetwarzanie danych za pomocą usługi Stream Analytics i używaj usługi Power BI jako dane wyjściowe, zobacz [usługi Stream Analytics i usługą Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream dzienników do innej firmy, rejestrowania i dane telemetryczne strumieni**. Za pomocą usługi Event Hubs, przesyłanie strumieniowe, możesz uzyskać metryki i Diagnostyka dzienników do różnych firm monitorowania oraz dziennik rozwiązań do analizy.

- **Tworzenie niestandardowej telemetrii i rejestrowania platformy**. Czy już masz platformy niestandardowej telemetrii lub rozważa zbudowania jej? Wysoce skalowalne publikowania/subskrybowania rodzaj usługi Event Hubs umożliwia elastyczne pozyskanie dzienniki diagnostyczne. Zobacz [Dan Rosanova Przewodnik po platformie danych telemetrycznych w skali globalnej przy użyciu usługi Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream do usługi Storage

Może przechowywać metryki, bazy danych SQL i dzienniki diagnostyczne w usłudze Azure Storage za pomocą wbudowanych **Zarchiwizuj na koncie magazynu** opcji w witrynie Azure portal. Magazyn można również włączyć przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schemat metryki i Diagnostyka dzienniki na koncie magazynu

Po skonfigurowaniu metryki i Diagnostyka kolekcji dzienników kontenera magazynu jest tworzony w ramach konta magazynu, wybranych podczas pierwszych wierszy danych są dostępne. Struktura obiektów blob jest:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Lub po prostu:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład może być nazwa obiektu blob dla wszystkich metryk:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Nazwa obiektu blob do przechowywania danych z puli elastycznej wygląda następująco:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Pobierz metryki i dzienniki z usługi Storage

Dowiedz się, jak [pobieranie metryki i Diagnostyka dzienników z usługi Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Zasady przechowywania danych i ceny

Wybranie Event Hubs lub konta magazynu, można określić zasady przechowywania. Ta zasada usuwa dane starsze niż w wybranym okresie. Jeśli określisz usługi Log Analytics, zasady przechowywania zależy od wybranej warstwy cenowej. W tym przypadku podana bezpłatny limit jednostek pozyskiwanie danych można włączyć bezpłatne monitorowania kilka baz danych miesięcznie. Wszelkie użycie przekraczające bezpłatny limit jednostek dane diagnostyczne i telemetryczne mogą zostać naliczone koszty. Należy pamiętać, że aktywnych baz danych przy użyciu większych obciążeń pozyskiwać więcej danych niż bezczynna baza danych. Aby uzyskać więcej informacji, zobacz [Log analytics — cennik](https://azure.microsoft.com/pricing/details/monitor/).

Jeśli używasz usługi Azure SQL Analytics, możesz monitorować swoje użycie pozyskiwania danych w rozwiązaniu, wybierając **obszaru roboczego pakietu OMS** w menu nawigacji usługi Azure SQL Analytics, a następnie wybierając **użycia** i **szacowane koszty**.

## <a name="metrics-and-logs-available"></a>Metryki i dostępnych dzienników

Monitorowanie telemetrii dostępne dla usługi Azure SQL Database, pul elastycznych i wystąpienia zarządzanego jest opisane poniżej. Zebrane dane telemetryczne z monitorowania wewnątrz SQL Analytics może służyć do własnych niestandardowych analizy i programowanie aplikacji za pomocą [zapytań dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) języka.

## <a name="all-metrics"></a>Wszystkie metryki

Zapoznaj się z następującymi tabelami, aby uzyskać szczegółowe informacje o wszystkich metryk przez zasób.

### <a name="all-metrics-for-elastic-pools"></a>Wszystkie metryki dla pul elastycznych

|**Zasób**|**Metryki**|
|---|---|
|Pula elastyczna|Procent eDTU używane liczby jednostek eDTU, limitu liczby jednostek eDTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, procent sesji, procent pracowników, Magazyn, procent użycia magazynu, limit przestrzeni dyskowej, procent użycia magazynu XTP |

### <a name="all-metrics-for-azure-sql-databases"></a>Wszystkie metryki dla baz danych SQL Azure

|**Zasób**|**Metryki**|
|---|---|
|Baza danych Azure SQL Database|Procentowa wartość jednostki DTU, używane jednostki DTU, limit jednostek DTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu, XTP procent użycia magazynu, a zakleszczenia |

## <a name="all-logs"></a>Wszystkie dzienniki

W poniższych tabelach opisano szczegóły telemetria dostępna dla wszystkich dzienników. Zobacz [obsługiwane rejestrowanie diagnostyczne](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) do puli, zrozumienie, dzienników, które są obsługiwane w przypadku flavor określonej bazy danych — Azure SQL pojedynczy, lub wystąpienie bazy danych.

### <a name="resource-usage-stats-for-managed-instance"></a>Statystyki użycia zasobów dla wystąpienia zarządzanego

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: ResourceUsageStats |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: MANAGEDINSTANCES |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa wystąpienia zarządzanego |
|ResourceId|Identyfikator URI zasobu |
|SKU_s|Jednostka SKU produktu wystąpienia zarządzanego |
|virtual_core_count_s|Liczba dostępnych rdzeni wirtualnych |
|avg_cpu_percent_s|Średni procent użycia procesora CPU |
|reserved_storage_mb_s|Pojemność magazynu zarezerwowanych na wystąpienia zarządzanego |
|storage_space_used_mb_s|Używany magazyn na wystąpienie zarządzane |
|io_requests_s|Liczba operacji We/Wy |
|io_bytes_read_s|Odczytano bajtów na SEKUNDĘ |
|io_bytes_written_s|Zapisano bajtów na SEKUNDĘ |

### <a name="query-store-runtime-statistics"></a>Statystyki środowiska uruchomieniowego Query Store

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreRuntimeStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreRuntimeStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|query_hash_s|Skrót zapytania |
|query_plan_hash_s|Skrót plan zapytania |
|statement_sql_handle_s|Dojście do instrukcji sql |
|interval_start_time_d|Rozpocznij datetimeoffset interwału w liczbę znaczników, od 1900-1-1 |
|interval_end_time_d|Datetimeoffset zakończenia interwału w liczbę znaczników, od 1900-1-1 |
|logical_io_writes_d|Łączna liczba logicznych Zapisy We/Wy |
|max_logical_io_writes_d|Maksymalna liczba logicznych operacji We/Wy zapisu na wykonanie |
|physical_io_reads_d|Łączna liczba fizyczne Odczyty We/Wy |
|max_physical_io_reads_d|Maksymalna liczba logicznych operacji We/Wy odczyty na wykonanie |
|logical_io_reads_d|Łączna liczba logicznych operacji We/Wy operacji odczytu. |
|max_logical_io_reads_d|Maksymalna liczba logicznych operacji We/Wy odczyty na wykonanie |
|execution_type_d|Typ wykonywania |
|count_executions_d|Liczba wykonań zapytania |
|cpu_time_d|Łączny czas procesora CPU wykorzystany przez zapytanie w mikrosekundach |
|max_cpu_time_d|Maks. czas konsumenta przez pojedyncze wykonanie w mikrosekundach |
|dop_d|Suma stopień równoległości |
|max_dop_d|Maksymalny stopień równoległości, używany do wykonywania pojedynczej |
|rowcount_d|Całkowita liczba zwracanych wierszy |
|max_rowcount_d|Maksymalna liczba wierszy zwracanych w pojedyncze wykonanie |
|query_max_used_memory_d|Całkowita ilość pamięci używana w KB |
|max_query_max_used_memory_d|Maksymalna ilość pamięci używanej przez pojedyncze wykonanie w KB |
|duration_d|Łączny czas wykonywania w mikrosekundach |
|max_duration_d|Maksymalny czas wykonania pojedynczego wykonania |
|num_physical_io_reads_d|Łączna liczba odczytów fizycznych |
|max_num_physical_io_reads_d|Maksymalna liczba odczytów fizycznych na wykonanie |
|log_bytes_used_d|Suma bajtów dziennika używanych |
|max_log_bytes_used_d|Maksymalna ilość bajtów dziennika na wykonanie |
|query_id_d|Identyfikator zapytania w Query Store |
|plan_id_d|Identyfikator planu w Query Store |

Dowiedz się więcej o [danych statystyki czasu wykonywania zapytania Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statystyki oczekiwania Query Store

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: QueryStoreWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_category_s|Kategoria oczekiwania |
|is_parameterizable_s|Zapytanie jest można sparametryzować |
|statement_type_s|Typ instrukcji |
|statement_key_hash_s|Skrót klucza — instrukcja |
|exec_type_d|Typ działania |
|total_query_wait_time_ms_d|Całkowity czas oczekiwania zapytania na kategorii określonych oczekiwania |
|max_query_wait_time_ms_d|Maksymalny czas oczekiwania zapytania podczas wykonywania poszczególnych kategorii określonych oczekiwania |
|query_param_type_d|0 |
|query_hash_s|Skrót zapytania w Query Store |
|query_plan_hash_s|Skrót planu zapytania w Query Store |
|statement_sql_handle_s|Oświadczenie uchwytu Query Store |
|interval_start_time_d|Rozpocznij datetimeoffset interwału w liczbę znaczników, od 1900-1-1 |
|interval_end_time_d|Datetimeoffset zakończenia interwału w liczbę znaczników, od 1900-1-1 |
|count_executions_d|Liczba wykonań zapytania |
|query_id_d|Identyfikator zapytania w Query Store |
|plan_id_d|Identyfikator planu w Query Store |

Dowiedz się więcej o [Query Store oczekiwania dane statystyk](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Błędy zestawu danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQ |
|Kategoria|Nazwa kategorii. Zawsze: Błędy |
|OperationName|Nazwa operacji. Zawsze: ErrorEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|Komunikat|Komunikat o błędzie w postaci zwykłego tekstu |
|user_defined_b|Jest bit zdefiniowane przez użytkownika błędu |
|error_number_d|Kod błędu |
|Ważność|Ważność błędu |
|state_d|Stan błędu |
|query_hash_s|Skrót zapytania zapytania nie powiodło się, jeśli jest dostępny |
|query_plan_hash_s|Skrót planu zapytania, zapytania zakończone niepowodzeniem, jeśli jest dostępny |

Dowiedz się więcej o [komunikaty o błędach programu SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Zestaw danych statystyki oczekiwania bazy danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: DatabaseWaitStatistics |
|OperationName|Nazwa operacji. Zawsze: DatabaseWaitStatisticsEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|wait_type_s|Nazwa typu oczekiwania |
|start_utc_date_t [UTC]|Godzina rozpoczęcia okresu mierzone |
|end_utc_date_t [UTC]|Czas zakończenia okresu mierzone |
|delta_max_wait_time_ms_d|Maksymalny oczekiwany czas wykonywania |
|delta_signal_wait_time_ms_d|Sygnały całkowity czas oczekiwania |
|delta_wait_time_ms_d|Całkowity czas oczekiwania w okresie |
|delta_waiting_tasks_count_d|Liczba oczekujących zadań |

Dowiedz się więcej o [bazy danych statystyki oczekiwania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Zestaw danych limitu czasu

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: Limity czasu |
|OperationName|Nazwa operacji. Zawsze: TimeoutEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|error_state_d|Kod stanu błędu |
|query_hash_s|Zapytanie skrótu, jeśli jest dostępny |
|query_plan_hash_s|Zapytania skrótu planu, jeśli jest dostępny |

### <a name="blockings-dataset"></a>Blokowanie zestawu danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: bloki |
|OperationName|Nazwa operacji. Zawsze: BlockEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|lock_mode_s|Tryb blokady używany przez zapytanie |
|resource_owner_type_s|Właściciel blokady |
|blocked_process_filtered_s|Zablokowane raport z procesu XML |
|duration_d|Czas trwania blokady w mikrosekundach |

### <a name="deadlocks-dataset"></a>Zakleszczenie zestawu danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC] |Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: Zakleszczenia |
|OperationName|Nazwa operacji. Zawsze: DeadlockEvent |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|deadlock_xml_s|Zakleszczenie raportu XML |

### <a name="automatic-tuning-dataset"></a>Automatyczne dostrajanie zestawu danych

|Właściwość|Opis|
|---|---|
|TenantId|Identyfikator dzierżawy |
|SourceSystem|Zawsze: Azure |
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania |
|Typ|Zawsze: AzureDiagnostics |
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT.SQL |
|Kategoria|Nazwa kategorii. Zawsze: AutomaticTuning |
|Zasób|Nazwa zasobu |
|ResourceType|Nazwa typu zasobu. Zawsze: SERWERY/BAZ DANYCH |
|SubscriptionId|Identyfikator GUID subskrypcji dla bazy danych |
|ResourceGroup|Nazwa grupy zasobów dla bazy danych |
|LogicalServerName_s|Nazwa serwera bazy danych |
|LogicalDatabaseName_s|Nazwa bazy danych |
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych, jeśli istnieje |
|DatabaseName_s|Nazwa bazy danych |
|ResourceId|Identyfikator URI zasobu |
|RecommendationHash_s|Unikatowy skrót zalecenia dostrajania automatycznego |
|OptionName_s|Operacja automatycznego dostrajania |
|Schema_s|Schemat bazy danych |
|Table_s|Tabela, w których to dotyczy |
|IndexName_s|Nazwa indeksu |
|IndexColumns_s|Nazwa kolumny |
|IncludedColumns_s|Uwzględnione kolumny |
|EstimatedImpact_s|Szacowany wpływ rekomendacji dostrajania automatycznego JSON |
|Event_s|Typ zdarzenia dostrajania automatycznego |
|Timestamp_t|Znacznik czasu ostatniej aktualizacji |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights zestawu danych.

Dowiedz się więcej o [format dziennika Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumieć, metryki i rejestrowanie kategorie obsługiwane przez różne usługi platformy Azure, zobacz:

- [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Przegląd dzienników diagnostyki platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Aby dowiedzieć się więcej na temat usługi Event Hubs, przeczytaj:

- [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Aby dowiedzieć się więcej o usłudze Azure Storage, zobacz [sposobu pobierania metryki i Diagnostyka dzienników z magazynu](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
