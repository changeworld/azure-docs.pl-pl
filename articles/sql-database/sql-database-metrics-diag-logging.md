---
title: Rejestrowanie diagnostyczne i metryki bazy danych SQL platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie konfigurowania usługi Azure SQL Database do przechowywania użycia zasobów, łącznością i statystyk wykonywania zapytań.
services: sql-database
documentationcenter: ''
author: Danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: c7a5031fab10f44809f9533e43c3596d46dc77e3
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346029"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metryki usługi Azure SQL Database i rejestrowania diagnostycznego 
Usługa Azure SQL Database można tworzyć metryki i Diagnostyka dzienników, które ułatwiają monitorowanie. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

* **Usługa Azure Storage**: używane w celu archiwizowania ogromnych ilości danych telemetrycznych za niewielką cenę.
* **Usługa Azure Event Hubs**: używane do integracji danych telemetrycznych usługi SQL Database z niestandardowym rozwiązaniem monitorowania lub potokami.
* **Usługa Azure Log Analytics**: używane do rozwiązania do monitorowania poza pole z raportowania, zgłaszania alertów i łagodzenia możliwości. Jest to funkcja pakietu [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Włącz rejestrowanie

Metryki i Diagnostyka rejestrowania nie jest włączona domyślnie. Można włączyć i zarządzać metryki i Diagnostyka rejestrowania przy użyciu jednej z następujących metod:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST usługi Azure Monitor 
- Szablon usługi Azure Resource Manager

Jeśli włączysz rejestrowanie diagnostyczne i metryki, należy określić zasobów platformy Azure, w którym wybrane dane są zbierane. Dostępne opcje obejmują:

- Log Analytics
- Event Hubs
- Magazyn 

Można uaktywniać nowego zasobu platformy Azure, lub wybierz istniejący zasób. Po wybraniu zasobu magazynu, należy określić dane, które mają być zbierane. Dostępne opcje obejmują:

- [Wszystkie metryki](sql-database-metrics-diag-logging.md#all-metrics): procent zawiera jednostki DTU, limit jednostek DTU, procent użycia procesora CPU, danych fizycznych odczytać wartości procentowej, dziennik zapisu procent, Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu i procent użycia magazynu XTP.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): zawiera informacje dotyczące środowiska uruchomieniowego statystyki zapytań, np. czas trwania użycia i zapytanie procesora CPU.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): zawiera informacje o statystyki oczekiwania zapytań, które informujący o tym, co zapytań oczekiwany, takie jak procesor CPU, DZIENNIKÓW i blokowanie.
- [Błędy](sql-database-metrics-diag-logging.md#errors-dataset): zawiera informacje na temat błędów programu SQL, które wystąpiły dla tej bazy danych.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): zawiera informacje, o ile czasu bazy danych poświęcony oczekiwanie na oczekiwania różnych typów.
- [Limity czasu](sql-database-metrics-diag-logging.md#time-outs-dataset): zawiera informacje dotyczące limitów czasu, który wystąpił w bazie danych.
- [Bloki](sql-database-metrics-diag-logging.md#blockings-dataset): zawiera informacje o blokowaniu zdarzenia, które wystąpiły w bazie danych.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): zawiera Intelligent Insights. [Dowiedz się więcej o Intelligent Insights](sql-database-intelligent-insights.md).
- **Inspekcja** / **SQLSecurityAuditEvents**: obecnie niedostępne.

Wybranie Event Hubs lub konta magazynu, można określić zasady przechowywania. Ta zasada usuwa dane starsze niż w wybranym okresie. Jeśli określisz usługi Log Analytics, zasady przechowywania zależy od wybranej warstwy cenowej. Aby uzyskać więcej informacji, zobacz [cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Dowiedz się, jak włączyć rejestrowanie i zrozumienie kategorii metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, zalecamy przeczytanie: 

* [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Przegląd dzienników diagnostyki platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

1. Aby włączyć metryki i Diagnostyka zbieranie dzienników w portalu, przejdź do usługi SQL Database lub strona elastycznej puli, a następnie wybierz **ustawień diagnostycznych**.

   ![Włącz w witrynie Azure portal](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Utwórz nowe lub Edytuj istniejące ustawienia diagnostyki, wybierając element docelowy i dane telemetryczne.

   ![Ustawienia diagnostyki](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Aby włączyć metryki i Diagnostyka rejestrowania przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, w której chcesz wysłać dzienniki.

- Aby włączyć strumieniowe przesyłanie dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć te parametry, aby włączyć wiele opcji danych wyjściowych.

### <a name="to-configure-multiple-azure-resources"></a>Aby skonfigurować wielu zasobów platformy Azure

Aby obsługiwać wiele subskrypcji, należy użyć skryptu programu PowerShell z [włączyć usługi Azure rejestrowanie metryki dla zasobów przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Podaj identyfikator zasobu obszaru roboczego &lt;$WSID&gt; jako parametr podczas wykonywania skryptu (Enable-AzureRMDiagnostics.ps1) do wysyłania danych diagnostycznych z wielu zasobów, do obszaru roboczego. Aby uzyskać identyfikator obszaru roboczego &lt;$WSID&gt; do której chcesz wysyłać dane diagnostyczne, Zastąp &lt;subID&gt; z Identyfikatorem subskrypcji, Zastąp &lt;RG_NAME&gt; nazwą grupy zasobów i Zastąp &lt;WS_NAME&gt; nazwą obszaru roboczego w poniższym skrypcie.

- Aby skonfigurować wielu zasobów platformy Azure, użyj następujących poleceń:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć metryki i Diagnostyka rejestrowania przy użyciu wiersza polecenia platformy Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienniki diagnostyczne na koncie magazynu, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, w której chcesz wysłać dzienniki.

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

Uzyskać informacje dotyczące sposobu [zmiany ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Uzyskać informacje dotyczące sposobu [włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Stream w usłudze Log Analytics 
Dzienniki metryki i Diagnostyka bazy danych SQL może być przesyłany strumieniowo do usługi Log Analytics przy użyciu wbudowanych **wysyłanie do usługi Log Analytics** opcji w portalu. Możesz również włączyć usługi Log Analytics przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="installation-overview"></a>Omówienie instalacji

Monitorowanie floty bazy danych SQL Database jest proste z usługą Log Analytics. Wymagane są trzy kroki:

1. Tworzenie zasobu usługi Log Analytics.

2. Konfigurowanie bazy danych do rekordów dzienników metryki i Diagnostyka do zasobu usługi Log Analytics, który został utworzony.

3. Zainstaluj **usługi Azure SQL Analytics** rozwiązania z galerii w usłudze Log Analytics.

### <a name="create-a-log-analytics-resource"></a>Tworzenie zasobu usługi Log Analytics

1. Wybierz **Utwórz zasób** w menu po lewej stronie.

2. Wybierz **monitorowanie + zarządzanie**.

3. Wybierz pozycję **Log Analytics**.

4. Wypełnij formularz usługi Log Analytics z dodatkowymi informacjami, które jest wymagane: Nazwa obszaru roboczego, subskrypcji, grupy zasobów, lokalizację i warstwę cenową.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurowanie bazy danych do rekordów dzienników metryki i Diagnostyka

Jest najprostszym sposobem skonfigurowania, gdzie baz danych rejestrowania ich metryki w witrynie Azure portal. W portalu przejdź do zasobu usługi SQL Database, a następnie wybierz pozycję **ustawień diagnostycznych**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Instalowanie rozwiązania SQL Analytics z galerii

1. Po utworzeniu zasobu usługi Log Analytics, a Twoje dane będą przepływać do niego, należy zainstalować rozwiązanie SQL Analytics. Na stronie głównej w menu po stronie wybierz **galerii rozwiązań**. W galerii, wybierz **usługi Azure SQL Analytics** rozwiązania, a następnie wybierz **Dodaj**.

   ![Rozwiązanie do monitorowania](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na stronie głównej **usługi Azure SQL Analytics** pojawi się Kafelek. Wybierz ten Kafelek, aby otworzyć pulpit nawigacyjny analizy SQL.

### <a name="use-the-sql-analytics-solution"></a>Korzystanie z odpowiedniego rozwiązania SQL Analytics

Usługa SQL Analytics jest hierarchiczna pulpit nawigacyjny, który pozwala na przenoszenie przez hierarchię zasobów usługi SQL Database. Aby dowiedzieć się, jak używać rozwiązania SQL Analytics, zobacz [monitorowanie bazy danych SQL przy użyciu rozwiązania SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Stream do usługi Event Hubs

Dzienniki metryki i Diagnostyka bazy danych SQL może być przesyłany strumieniowo do usługi Event Hubs za pomocą wbudowanych **Stream do usługi event hub** opcji w portalu. Możesz również włączyć identyfikator reguły usługi Service Bus przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co należy zrobić metryki i Diagnostyka dzienników w usłudze Event Hubs
Po wybrane dane są przesyłane strumieniowo do usługi Event Hubs, jesteś o krok bliżej włączenie zaawansowanych scenariuszach monitorowania. Usługa Event Hubs działa jako drzwi wejściowe dla potoku zdarzeń. Po zebraniu danych do Centrum zdarzeń, mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy Analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Usługa Event Hubs oddziela Wytwarzanie strumienia zdarzeń od użycia tych zdarzeń. W ten sposób odbiorcy zdarzeń mogą uzyskać dostęp do zdarzeń w swoim własnym harmonogramem. Aby uzyskać więcej informacji na temat usługi Event Hubs zobacz:

- [Co to są usługi Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Oto kilka sposobów, można użyć możliwości przesyłania strumieniowego:

* **Aby wyświetlić kondycję usługi, aktywną ścieżkę danych strumieniowych do usługi Power BI**. Za pomocą usługi Event Hubs, Stream Analytics i Power BI, można łatwo przekształcać metryki i Diagnostyka danych do niemal w czasie rzeczywistym szczegółowe informacje na usługi platformy Azure. Aby uzyskać omówienie sposobu konfigurowania Centrum zdarzeń, przetwarzanie danych za pomocą usługi Stream Analytics i używaj usługi Power BI jako dane wyjściowe, zobacz [usługi Stream Analytics i usługą Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream dzienników do innej firmy, rejestrowania i dane telemetryczne strumieni**. Za pomocą usługi Event Hubs, przesyłanie strumieniowe, możesz uzyskać metryki i Diagnostyka dzienników do różnych firm monitorowania oraz dziennik rozwiązań do analizy. 

* **Tworzenie niestandardowej telemetrii i rejestrowania platformy**. Jeśli już masz platformy niestandardowej telemetrii lub rozważa zbudowania jej, wysoce skalowalna publikowania/subskrybowania rodzaj usługi Event Hubs umożliwia elastyczne pozyskanie dzienniki diagnostyczne. Zobacz [Dan Rosanova Przewodnik po platformie danych telemetrycznych w skali globalnej przy użyciu usługi Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream do usługi Storage

Bazy danych SQL, metryki i Diagnostyka Dzienniki mogą być przechowywane w magazynie przy użyciu wbudowanych **Zarchiwizuj na koncie magazynu** opcji w portalu. Możesz również włączyć magazynu przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schemat metryki i Diagnostyka dzienniki na koncie magazynu

Po skonfigurowaniu metryki i Diagnostyka kolekcji dzienników kontenera magazynu jest tworzony w ramach konta magazynu, wybranych podczas pierwszych wierszy danych są dostępne. Struktura tych obiektów blob jest:

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

Jeśli chcesz zarejestrować dane z puli elastycznej, nazwa obiektu blob jest nieco inna:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Pobierz metryki i dzienniki z usługi Storage

Dowiedz się, jak [pobieranie metryki i Diagnostyka dzienników z usługi Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="metrics-and-logs-available"></a>Metryki i dostępnych dzienników

### <a name="all-metrics"></a>Wszystkie metryki

|**Zasób**|**Metryki**|
|---|---|
|Database (Baza danych)|Procentowa wartość jednostki DTU, używane jednostki DTU, limit jednostek DTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu, XTP procent użycia magazynu, a zakleszczenia |
|Pula elastyczna|Procent eDTU używane liczby jednostek eDTU, limitu liczby jednostek eDTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, procent sesji, procent pracowników, Magazyn, procent użycia magazynu, limit przestrzeni dyskowej, procent użycia magazynu XTP |
|||

### <a name="query-store-runtime-statistics"></a>Statystyki środowiska uruchomieniowego Query Store

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreRuntimeStatistics|
|OperationName|Nazwa operacji. Zawsze: QueryStoreRuntimeStatisticsEvent|
|Zasób|Nazwa zasobu.|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|query_hash_s|Wyślij zapytanie do wyznaczania wartości skrótu.|
|query_plan_hash_s|Skrót planu zapytania.|
|statement_sql_handle_s|Dojście do instrukcji sql.|
|interval_start_time_d|Uruchom datetimeoffset interwału w liczbę znaczników, od 1900-1-1.|
|interval_end_time_d|Datetimeoffset zakończenia interwału w liczbę znaczników, od 1900-1-1.|
|logical_io_writes_d|Łączna liczba logicznych operacji We/Wy zapisu.|
|max_logical_io_writes_d|Maksymalna liczba logicznych operacji We/Wy zapisu na wykonanie.|
|physical_io_reads_d|Całkowita liczba fizyczne Odczyty We/Wy.|
|max_physical_io_reads_d|Maksymalna liczba logicznych operacji We/Wy odczyty na wykonanie.|
|logical_io_reads_d|Całkowita liczba logiczne Odczyty We/Wy.|
|max_logical_io_reads_d|Maksymalna liczba logicznych operacji We/Wy odczyty na wykonanie.|
|execution_type_d|Typ wykonywania.|
|count_executions_d|Liczba wykonań zapytania.|
|cpu_time_d|Łączny czas procesora CPU wykorzystany przez zapytanie w mikrosekundach.|
|max_cpu_time_d|Maks. czas konsumenta przez pojedyncze wykonanie w mikrosekundach.|
|dop_d|Suma stopień równoległości.|
|max_dop_d|Maksymalny stopień równoległości, używany do wykonywania pojedynczej.|
|rowcount_d|Całkowita liczba zwracanych wierszy.|
|max_rowcount_d|Maksymalna liczba wierszy zwracanych w pojedynczej operacji.|
|query_max_used_memory_d|Całkowita ilość pamięci używana w KB.|
|max_query_max_used_memory_d|Maksymalna ilość pamięci używanej przez pojedyncze wykonanie w KB.|
|duration_d|Łączny czas wykonywania w mikrosekundach.|
|max_duration_d|Maksymalny czas wykonania pojedynczego wykonania.|
|num_physical_io_reads_d|Łączna liczba odczytów fizycznych.|
|max_num_physical_io_reads_d|Maksymalna liczba odczytów fizycznych na wykonanie.|
|log_bytes_used_d|Suma bajtów dziennika.|
|max_log_bytes_used_d|Maksymalna ilość bajtów dziennika na wykonanie.|
|query_id_d|Identyfikator zapytania w Query Store.|
|plan_id_d|Identyfikator planu w Query Store.|

Dowiedz się więcej o [danych statystyki czasu wykonywania zapytania Store](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Statystyki oczekiwania Query Store

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreWaitStatistics|
|OperationName|Nazwa operacji. Zawsze: QueryStoreWaitStatisticsEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|wait_category_s|Kategoria oczekiwania.|
|is_parameterizable_s|To zapytanie można sparametryzować.|
|statement_type_s|Typ instrukcji.|
|statement_key_hash_s|Skrót klucza instrukcji.|
|exec_type_d|Typ działania.|
|total_query_wait_time_ms_d|Całkowity czas oczekiwania zapytania na kategorii określonych oczekiwania.|
|max_query_wait_time_ms_d|Maksymalny czas oczekiwania zapytania podczas wykonywania poszczególnych kategorii określonych oczekiwania.|
|query_param_type_d|0|
|query_hash_s|Zapytania skrótu w Query Store.|
|query_plan_hash_s|Skrót planu zapytania w Query Store.|
|statement_sql_handle_s|Dojście instrukcji w Query Store.|
|interval_start_time_d|Uruchom datetimeoffset interwału w liczbę znaczników, od 1900-1-1.|
|interval_end_time_d|Datetimeoffset zakończenia interwału w liczbę znaczników, od 1900-1-1.|
|count_executions_d|Liczba wykonań zapytania.|
|query_id_d|Identyfikator zapytania w Query Store.|
|plan_id_d|Identyfikator planu w Query Store.|

Dowiedz się więcej o [Query Store oczekiwania dane statystyk](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Błędy zestawu danych

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: błędy|
|OperationName|Nazwa operacji. Zawsze: ErrorEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|Komunikat|Komunikat o błędzie w postaci zwykłego tekstu.|
|user_defined_b|Jest błąd zdefiniowane przez użytkownika.|
|error_number_d|Kod błędu.|
|Ważność|Ważność błędu.|
|state_d|Stan błędu.|
|query_hash_s|Skrót zapytania nie powiodło się zapytanie, jeśli jest dostępny.|
|query_plan_hash_s|Skrót planu zapytania, zapytania zakończone niepowodzeniem, jeśli jest dostępny.|

Dowiedz się więcej o [komunikaty o błędach programu SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Zestaw danych statystyki oczekiwania bazy danych

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: DatabaseWaitStatistics|
|OperationName|Nazwa operacji. Zawsze: DatabaseWaitStatisticsEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|wait_type_s|Nazwa typu oczekiwania.|
|start_utc_date_t [UTC]|Mierzy czas rozpoczęcia okresu.|
|end_utc_date_t [UTC]|Mierzy czas zakończenia okresu.|
|delta_max_wait_time_ms_d|Maksymalny oczekiwany czas wykonywania|
|delta_signal_wait_time_ms_d|Sygnał całkowity czas oczekiwania.|
|delta_wait_time_ms_d|Całkowity czas oczekiwania w okresie.|
|delta_waiting_tasks_count_d|Liczba oczekujących zadań.|

Dowiedz się więcej o [bazy danych statystyki oczekiwania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Zestaw danych limitu czasu

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: limity czasu|
|OperationName|Nazwa operacji. Zawsze: TimeoutEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|error_state_d|Kod stanu błędu.|
|query_hash_s|Zapytania skrótu, jeśli jest dostępny.|
|query_plan_hash_s|Zapytania skrótu planu, jeśli jest dostępny.|

### <a name="blockings-dataset"></a>Blokowanie zestawu danych

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: bloków|
|OperationName|Nazwa operacji. Zawsze: BlockEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|lock_mode_s|Tryb blokady używany przez zapytanie.|
|resource_owner_type_s|Właściciel blokady.|
|blocked_process_filtered_s|Zablokowane raport z procesu XML.|
|duration_d|Czas trwania blokady w mikrosekundach.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights zestawu danych.
Dowiedz się więcej o [format dziennika Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumieć kategorie metryk i dzienników, obsługiwane przez różne usługi platformy Azure, przeczytaj:

 * [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Przegląd dzienników diagnostyki platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Aby dowiedzieć się więcej na temat usługi Event Hubs, przeczytaj:

* [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Aby dowiedzieć się więcej na temat magazynu, zobacz temat jak [pobieranie metryki i Diagnostyka dzienników z usługi Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
