---
title: Metryki bazy danych SQL Azure i rejestrowanie danych diagnostycznych | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu konfigurowania bazy danych SQL Azure do przechowywania użycia zasobów, łączności i statystyki wykonania zapytania.
services: sql-database
documentationcenter: ''
author: veljko-msft
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 03/16/2018
ms.author: vvasic
ms.openlocfilehash: c63429e80b2e38e9e5c08c6b589afa0086e7171d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metryki bazy danych SQL Azure i rejestrowanie danych diagnostycznych 
Baza danych SQL Azure może emitować metryki i informacji diagnostycznych dzienników łatwiejsze monitorowania. Usługę SQL Database można skonfigurować do przechowywania danych dotyczących użycia zasobów, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

* **Usługa Azure Storage**: dla czasach ogromne ilości danych telemetrycznych dla małych ceny.
* **Usługa Azure Event Hubs**: używany do integrowania telemetrii bazy danych SQL z niestandardowe rozwiązanie monitorowania lub gorących potoków.
* **Analiza dzienników Azure**: używany dla poza pole rozwiązanie monitorowania, raportowania, alerty i zmniejszenia możliwości. Jest to funkcja pakietu [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Włącz rejestrowanie

Metryki i rejestrowania diagnostyki nie jest włączona domyślnie. Można włączyć i zarządzać metryki i diagnostyki rejestrowanie przy użyciu jednej z następujących metod:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST Azure monitora 
- Szablon usługi Azure Resource Manager

Po włączeniu metryki i rejestrowanie danych diagnostycznych, należy określić zasobów platformy Azure, w którym są zbierane wybranych danych. Dostępne opcje to:

- Log Analytics
- Event Hubs
- Magazyn 

Można udostępnić nowych zasobów platformy Azure lub wybierz istniejący zasób. Po wybraniu zasobów magazynu, należy określić dane, które mają być zbierane. Dostępne opcje to:

- [Wszystkie metryki](sql-database-metrics-diag-logging.md#all-metrics): procent zawiera jednostek dtu w warstwie, limit jednostek dtu w warstwie, procent użycia procesora CPU danych fizycznych odczytu procent, dziennika zapisu procent, Powodzenie/nie powiodło się/zablokowane przez połączeń zapory, wartość procentowa sesji, procent pracowników, magazynu, procent użycia magazynu i procent użycia magazynu XTP.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): zawiera informacje o statystyki czasu wykonywania zapytania, takie jak czas trwania użycia i zapytanie Procesora.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): zawiera informacje dotyczące statystyk oczekiwania zapytania informuje zapytań oczekiwanie na, takie jak procesor CPU, LOG oraz blokowanie.
- [Błędy](sql-database-metrics-diag-logging.md#errors-dataset): zawiera informacje o błędach SQL, które wystąpiły w tej bazie danych.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): zawiera informacje o ile czasu bazy danych podczas oczekiwania na oczekiwania różnych typów.
- [Limity czasu](sql-database-metrics-diag-logging.md#time-outs-dataset): zawiera informacje dotyczące limitów czasu, który wystąpił w bazie danych.
- [Blokowanie](sql-database-metrics-diag-logging.md#blockings-dataset): zawiera informacje o blokowaniu zdarzenia, które wystąpiły w bazie danych.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): zawiera inteligentnego szczegółowych informacji. [Dowiedz się więcej o inteligentnego Insights](sql-database-intelligent-insights.md).
- **Inspekcji** / **SQLSecurityAuditEvents**: obecnie niedostępne.

W przypadku wybrania usługi Event Hubs lub konto magazynu, można określić zasady przechowywania. Ta zasada usuwa dane starsze niż w wybranym okresie. Jeśli określisz analizy dzienników, zasad przechowywania zależy od wybranej warstwy cenowej. Aby uzyskać więcej informacji, zobacz [cennik analizy dzienników](https://azure.microsoft.com/pricing/details/log-analytics/). 

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumienie kategorii metryki i dziennika, które są obsługiwane przez różne usługi platformy Azure, zaleca się przeczytanie: 

* [Omówienie metryk w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Przegląd dzienników diagnostycznych platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Azure Portal

1. Aby włączyć metryki i informacji diagnostycznych zbieranie dzienników w portalu, przejdź do bazy danych SQL lub puli elastycznej strony, a następnie wybierz **ustawień diagnostycznych**.

   ![Włącz w portalu Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Utwórz nowe lub edytować istniejące ustawienia diagnostyki, wybierając obiekt docelowy i dane telemetryczne.

   ![Ustawienia diagnostyki](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Aby włączyć metryki i diagnostyki rejestrowanie przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na konto magazynu, należy użyć tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, której chcesz wysłać dzienniki.

- Aby włączyć przesyłania strumieniowego dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły Azure Service Bus to ciąg w formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników, użyj tego polecenia:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego analizy dzienników można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć tych parametrów, aby włączyć wiele opcji danych wyjściowych.

### <a name="to-configure-multiple-azure-resources"></a>Aby skonfigurować wielu zasobów platformy Azure

Aby obsługiwać wiele subskrypcji, należy użyć skryptu programu PowerShell z [rejestrowania metryki zasobów Azure włączyć przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Podaj identyfikator zasobu obszaru roboczego &lt;$WSID&gt; jako parametr podczas wykonywania skryptu (Enable-AzureRMDiagnostics.ps1) do wysyłania danych diagnostycznych z wielu zasobów do obszaru roboczego. Aby uzyskać identyfikator obszaru roboczego &lt;$WSID&gt; , do którego chcesz wysyłać dane diagnostyczne, Zastąp &lt;subID&gt; z Identyfikatorem subskrypcji, należy zastąpić &lt;RG_NAME&gt; z nazwy grupy zasobów i Zastąp &lt;WS_NAME&gt; z nazwą obszaru roboczego w poniższym skrypcie.

- Aby skonfigurować wielu zasobów platformy Azure, użyj następujących poleceń:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć metryki i diagnostyki rejestrowanie przy użyciu interfejsu wiersza polecenia Azure, użyj następujących poleceń:

- Aby włączyć magazyn dzienników diagnostycznych na konto magazynu, należy użyć tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   Identyfikator konta magazynu jest identyfikator zasobu dla konta magazynu, której chcesz wysłać dzienniki.

- Aby włączyć przesyłania strumieniowego dzienników diagnostycznych do Centrum zdarzeń, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Identyfikator reguły usługi Service Bus to ciąg w formacie:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Aby włączyć wysyłanie dzienników diagnostycznych do obszaru roboczego analizy dzienników, użyj tego polecenia:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Można połączyć tych parametrów, aby włączyć wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Przeczytaj informacje o sposobie [zmiany ustawień diagnostyki za pomocą interfejsu API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Przeczytaj informacje o sposobie [. Włącz ustawienia diagnostyki na tworzenie zasobów przy użyciu szablonu usługi Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Strumień do analizy dzienników 
Baza danych SQL metryki i informacji diagnostycznych dzienników mogła być przesłana strumieniowo do analizy dzienników przy użyciu wbudowanych **wysyłać do analizy dzienników** opcji w portalu. Można również włączyć analizy dzienników przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST Azure monitora.

### <a name="installation-overview"></a>Omówienie instalacji

Monitorowanie floty bazy danych SQL jest proste — analizy dzienników. Wymagane są trzy kroki:

1. Utwórz zasób analizy dzienników.

2. Konfigurowanie bazy danych do rekordów metryki i informacji diagnostycznych dzienników do utworzonego zasobu analizy dzienników.

3. Zainstaluj **analiza SQL Azure** rozwiązania z galerii w analizy dzienników.

### <a name="create-a-log-analytics-resource"></a>Utwórz zasób analizy dzienników

1. Wybierz **Utwórz zasób** w menu po lewej stronie.

2. Wybierz **monitorowania i zarządzania**.

3. Wybierz pozycję **Log Analytics**.

4. Wypełnij formularz analizy dzienników z dodatkowymi informacjami, która jest wymagana: Nazwa obszaru roboczego, subskrypcji, grupy zasobów, lokalizacji i warstwę cenową.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Konfigurowanie bazy danych do rekordów metryki i informacji diagnostycznych dzienników

Najprostszym sposobem konfigurowania, gdzie baz danych rejestrowania ich metryki jest za pośrednictwem portalu Azure. W portalu przejdź do zasobu bazy danych SQL i wybierz **ustawień diagnostycznych**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Instalowanie rozwiązania analizy SQL z galerii

1. Po utworzeniu zasobu analizy dzienników i danych jest przepływać do niego, należy zainstalować rozwiązania analizy SQL. Na stronie głównej w menu po stronie wybierz **galerii rozwiązań**. W galerii, wybierz **analiza SQL Azure** rozwiązania, a następnie wybierz **Dodaj**.

   ![rozwiązanie monitorowania](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na stronie głównej **analiza SQL Azure** pojawi się Kafelek. Wybierz ten Kafelek, aby otworzyć pulpitu nawigacyjnego SQL Analytics.

### <a name="use-the-sql-analytics-solution"></a>Użyj rozwiązania analizy SQL

Analiza SQL jest hierarchiczne pulpit nawigacyjny, który umożliwia przeniesienie w hierarchii zasobów bazy danych SQL. Aby dowiedzieć się, jak używać rozwiązania analizy SQL, zobacz [monitora bazy danych SQL za pomocą rozwiązania analizy SQL](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Strumień do usługi Event Hubs

Baza danych SQL metryki i informacji diagnostycznych dzienników mogła być przesłana strumieniowo do usługi Event Hubs przy użyciu wbudowanych **strumienia do Centrum zdarzeń** opcji w portalu. Można również włączyć identyfikator reguły usługi Service Bus przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST Azure monitora. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Co należy zrobić metryki i informacji diagnostycznych loguje centra zdarzeń
Po wybrane dane są przesyłane strumieniowo do usługi Event Hubs, możesz bliższe włączenie zaawansowanych scenariuszach monitorowania. Usługa Event Hubs działa jako drzwi dla potoku zdarzeń. Po zebraniu danych do Centrum zdarzeń, można przekształcać i przechowywane za pomocą dowolnego dostawcy analiz w czasie rzeczywistym lub kart przetwarzania wsadowego i magazynowania. Usługa Event Hubs oddziela Wytwarzanie strumienia zdarzeń od użycia tych zdarzeń. W ten sposób odbiorcy zdarzeń mogą uzyskiwać dostęp do zdarzeń zgodnie z ich harmonogramem. Aby uzyskać więcej informacji dotyczących usługi Event Hubs zobacz:

- [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Poniżej przedstawiono kilka sposobów, że można użyć funkcji przesyłania strumieniowego:

* **Wyświetlić kondycję usługi przesyłania strumieniowego hot ścieżki danych do usługi Power BI**. Za pomocą usługi Event Hubs, Stream Analytics i usługi Power BI, mogą łatwo przekształcić metryki i diagnostyki danych w pobliżu wgląd w czasie rzeczywistym na usługami Azure. Omówienie sposobu konfigurowania Centrum zdarzeń, przetwarzania danych z analizą strumieni i użycia usługi Power BI jako dane wyjściowe, zobacz [Stream Analytics i usługi Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Strumienia dzienników do innej firmy, rejestrowania i dane telemetryczne strumieni**. Za pomocą usługi Event Hubs przesyłania strumieniowego, metryki i informacji diagnostycznych dzienników można uzyskać w różnych rozwiązaniach innych firm monitorowania i dziennika analizy. 

* **Tworzenie niestandardowych telemetrii i rejestrowanie platformy**. Już platformy telemetrii niestandardowej lub rozważa tworzenia jedną, wysoce skalowalna publikacji / subskrypcji rodzaj usługi Event Hubs umożliwia elastyczne pozyskiwania dzienników diagnostycznych. Zobacz [przewodnik Dan Rosanova przy użyciu usługi Event Hubs na platformie telemetrii Światowej skali](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Strumień do magazynu

Dzienniki metryki i informacji diagnostycznych bazy danych SQL mogą być przechowywane w magazynie przy użyciu wbudowanych **archiwum na konto magazynu** opcji w portalu. Można również włączyć magazynu przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST Azure monitora.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Schemat metryki i informacji diagnostycznych logowania na koncie magazynu

Po skonfigurowaniu metryki i informacji diagnostycznych zbierania dzienników kontenera magazynu jest tworzony na koncie magazynu, wybranych podczas pierwszego wiersze danych są dostępne. Struktura tych obiektów blob jest:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Lub po prostu:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład może być nazwa obiektu blob, dla wszystkich metryki:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Jeśli chcesz rejestrować dane z puli elastycznej, nazwa obiektu blob różni się nieco:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Pobierz dzienniki i metryki z magazynu

Dowiedz się, jak [pobrać dzienniki metryki i informacji diagnostycznych z magazynu](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="metrics-and-logs-available"></a>Metryki i dostępnych dzienników

### <a name="all-metrics"></a>Wszystkie metryki

|**Zasób**|**Metryki**|
|---|---|
|Database (Baza danych)|Procent użycia jednostek DTU, używane jednostek dtu w warstwie, limit jednostek dtu w warstwie, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennika zapisu procent, Powodzenie/nie powiodło się/zablokowane przez połączeń zapory, wartość procentowa sesji, procent pracowników, magazynu, procent użycia magazynu, XTP procent użycia magazynu, i Zakleszczenie |
|Pula elastyczna|procent liczby jednostek eDTU używany eDTU, limit liczby jednostek eDTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennika zapisu procent, procent sesji, procent pracowników, magazynu, procent użycia magazynu, limit magazynu, XTP procent użycia magazynu |
|||

### <a name="query-store-runtime-statistics"></a>Magazyn zapytań statystyk czasu wykonywania

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreRuntimeStatistics|
|OperationName|Nazwa operacji. Zawsze: QueryStoreRuntimeStatisticsEvent|
|Zasób|Nazwa zasobu.|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|query_hash_s|Wyślij zapytanie do wyznaczania wartości skrótu.|
|query_plan_hash_s|Skrót planu zapytania.|
|statement_sql_handle_s|Dojście do instrukcji sql.|
|interval_start_time_d|Uruchom datetimeoffset interwału w liczbę taktów z 1900-1-1.|
|interval_end_time_d|Datetimeoffset końcowy zakresu, w liczbę taktów z 1900-1-1.|
|logical_io_writes_d|Łączna liczba logicznych Zapisy We/Wy.|
|max_logical_io_writes_d|Maksymalna liczba logicznych We/Wy zapisu na wykonanie.|
|physical_io_reads_d|Całkowita liczba fizyczne Odczyty We/Wy.|
|max_physical_io_reads_d|Odczyty maks. Liczba logicznych we/wy na wykonanie.|
|logical_io_reads_d|Całkowita liczba logiczne Odczyty We/Wy.|
|max_logical_io_reads_d|Odczyty maks. Liczba logicznych we/wy na wykonanie.|
|execution_type_d|Typ wykonywania.|
|count_executions_d|Liczba wykonań zapytania.|
|cpu_time_d|Łączny czas procesora CPU używane przez zapytanie w mikrosekundach.|
|max_cpu_time_d|Maks. czas odbiorców w pojedynczej operacji w mikrosekundach.|
|dop_d|Suma stopień równoległości.|
|max_dop_d|Maksymalny stopień równoległości, używany do pojedynczego uruchomienia.|
|rowcount_d|Całkowita liczba wierszy zwracanych.|
|max_rowcount_d|Maksymalna liczba wierszy zwracanych w pojedynczej operacji.|
|query_max_used_memory_d|Całkowita ilość pamięci użytej w artykule bazy wiedzy.|
|max_query_max_used_memory_d|Maksymalna ilość pamięci użytej przez pojedynczego uruchomienia w artykule bazy wiedzy.|
|duration_d|Łączny czas wykonywania w mikrosekundach.|
|max_duration_d|Maksymalny czas wykonywania pojedynczego wykonania.|
|num_physical_io_reads_d|Łączna liczba odczytów fizycznych.|
|max_num_physical_io_reads_d|Maksymalna liczba odczytów fizycznych na wykonanie.|
|log_bytes_used_d|Suma bajtów dziennika używanych.|
|max_log_bytes_used_d|Maksymalna liczba bajtów dziennika używanych na wykonanie.|
|query_id_d|Identyfikator zapytania w magazynie zapytań.|
|plan_id_d|Identyfikator planu w magazynie zapytań.|

Dowiedz się więcej o [dane statystyk czasu wykonywania magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Magazyn zapytań oczekiwania statystyki

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: QueryStoreWaitStatistics|
|OperationName|Nazwa operacji. Zawsze: QueryStoreWaitStatisticsEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|wait_category_s|Kategoria czas oczekiwania.|
|is_parameterizable_s|To zapytanie można sparametryzować.|
|statement_type_s|Typ instrukcji.|
|statement_key_hash_s|Wartość skrótu klucza instrukcji.|
|exec_type_d|Typ wykonywania.|
|total_query_wait_time_ms_d|Czas oczekiwania całkowita zapytania na oczekiwania określonej kategorii.|
|max_query_wait_time_ms_d|Maksymalny czas oczekiwania podczas wykonywania poszczególnych kategorii oczekiwania określonego zapytania.|
|query_param_type_d|0|
|query_hash_s|Zapytanie skrótu w magazynie zapytań.|
|query_plan_hash_s|Skrót plan zapytania w magazynie zapytań.|
|statement_sql_handle_s|Dojście do instrukcji w magazynie zapytań.|
|interval_start_time_d|Uruchom datetimeoffset interwału w liczbę taktów z 1900-1-1.|
|interval_end_time_d|Datetimeoffset końcowy zakresu, w liczbę taktów z 1900-1-1.|
|count_executions_d|Liczba wykonania zapytania.|
|query_id_d|Identyfikator zapytania w magazynie zapytań.|
|plan_id_d|Identyfikator planu w magazynie zapytań.|

Dowiedz się więcej o [magazyn zapytań oczekiwania statystyki danych](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Błędy zestawu danych

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: błędy|
|OperationName|Nazwa operacji. Zawsze: ErrorEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|Komunikat|Komunikat o błędzie w postaci zwykłego tekstu.|
|user_defined_b|Jest błąd zdefiniowanych przez użytkownika.|
|error_number_d|Kod błędu.|
|Ważność|Ważność błędu.|
|state_d|Stan błędu.|
|query_hash_s|Zapytanie skrótu kwerendy nie powiodło się, jeśli jest dostępna.|
|query_plan_hash_s|Skrót plan zapytania nie powiodło się zapytanie, jeśli jest dostępna.|

Dowiedz się więcej o [komunikaty o błędach programu SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Zestaw danych statystyki oczekiwania bazy danych

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: DatabaseWaitStatistics|
|OperationName|Nazwa operacji. Zawsze: DatabaseWaitStatisticsEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|wait_type_s|Nazwa typu oczekiwania.|
|start_utc_date_t [UTC]|Mierzy czas rozpoczęcia okresu.|
|end_utc_date_t [UTC]|Mierzy czas zakończenia okresu.|
|delta_max_wait_time_ms_d|Maksymalny czas potrzebny czas na wykonanie|
|delta_signal_wait_time_ms_d|Sygnał łączny czas oczekiwania.|
|delta_wait_time_ms_d|Czas oczekiwania całkowita w okresie.|
|delta_waiting_tasks_count_d|Liczba oczekujących zadań.|

Dowiedz się więcej o [bazy danych statystyki oczekiwania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Limity czasu zestawu danych

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: limitów czasu|
|OperationName|Nazwa operacji. Zawsze: TimeoutEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|error_state_d|Kod stanu błędu.|
|query_hash_s|Zapytanie skrótu, jeśli jest dostępna.|
|query_plan_hash_s|Zapytanie skrótu planu, jeśli jest dostępna.|

### <a name="blockings-dataset"></a>Blokowanie zestawu danych

|Właściwość|Opis|
|---|---|
|Dla identyfikatora dzierżawcy|Do identyfikatora dzierżawcy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: bloków|
|OperationName|Nazwa operacji. Zawsze: BlockEvent|
|Zasób|Nazwa zasobu|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery lub bazy danych|
|SubscriptionId|Identyfikator GUID bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, należącego do bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|lock_mode_s|Tryb blokowania użyta przez zapytanie.|
|resource_owner_type_s|Właściciel blokady.|
|blocked_process_filtered_s|Zablokowane procesu raportu XML.|
|duration_d|Czas trwania blokady w mikrosekundach.|

### <a name="intelligent-insights-dataset"></a>Inteligentnego Insights zestawu danych.
Dowiedz się więcej o [format dziennika inteligentnego Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumienie kategorii metryki i dziennika obsługiwanych przez różne usługi platformy Azure, przeczytaj:

 * [Omówienie metryk w Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Przegląd dzienników diagnostycznych platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Aby dowiedzieć się więcej o usłudze Event Hubs, należy przeczytać:

* [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Aby dowiedzieć się więcej na temat magazynu, zobacz temat jak [pobrać dzienniki metryki i informacji diagnostycznych z magazynu](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
