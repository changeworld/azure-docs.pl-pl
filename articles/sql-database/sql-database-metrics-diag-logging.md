---
title: Rejestrowanie diagnostyczne i metryki bazy danych SQL platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie konfigurowania usługi Azure SQL Database do przechowywania użycia zasobów, łącznością i statystyk wykonywania zapytań.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 775883d575a87758f563bd8dae8e5a726cd8ed36
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49959081"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Metryki usługi Azure SQL Database i rejestrowania diagnostycznego 

Usługa Azure SQL Database, pul elastycznych, wystąpienia zarządzanego i baz danych w wystąpieniu zarządzanym można emitowania dzienników metryki i Diagnostyka ułatwiają monitorowanie wydajności. Można skonfigurować bazę danych do użycia zasobów usługi stream, pracowników i sesji oraz połączeń z jednym z następujących zasobów platformy Azure:

* **Usługa Azure SQL Analytics**: używana jako zintegrowane usługi Azure database wydajności inteligentne rozwiązania z raportowania, zgłaszania alertów i łagodzenia możliwości monitorowania.
* **Usługa Azure Event Hubs**: używane do integracji danych telemetrycznych usługi SQL Database z niestandardowym rozwiązaniem monitorowania lub potokami.
* **Usługa Azure Storage**: używane w celu archiwizowania ogromnych ilości danych telemetrycznych za ułamek ceny.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

Aby zrozumieć metryki i zaloguj się kategorie, które są obsługiwane przez różne usługi platformy Azure, zastanów się nad odczytu:

* [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Przegląd dzienników diagnostyki platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

 Można włączyć i zarządzać metryki i Diagnostyka danych telemetrycznych rejestrowania w bazie danych przy użyciu jednej z następujących metod:

- Azure Portal
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Interfejs API REST usługi Azure Monitor 
- Szablon usługi Azure Resource Manager

Po włączeniu metryk i rejestrowania diagnostycznego, należy określić docelowy zasobów platformy Azure, gdzie będą zbierane wybranych danych. Dostępne opcje obejmują:

- Usługa SQL Analytics
- Event Hubs
- Magazyn 

Można uaktywniać nowego zasobu platformy Azure, lub wybierz istniejący zasób. Po wybraniu zasobu, za pomocą opcji ustawień diagnostycznych, należy określić dane, które mają być zbierane. 

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Włączanie rejestrowania dla pul elastycznych lub wystąpienia zarządzanego

Pule elastyczne i wystąpieniach zarządzanych jako kontenery bazy danych ma swoje własne dane telemetryczne Diagnostyka nie jest domyślnie włączona. Należy pamiętać, że tych danych telemetrycznych jest oddzielony od bazy danych dane diagnostyczne i telemetryczne. Dlatego przesyłanie strumieniowe dane diagnostyczne i telemetryczne dla pul elastycznych i wystąpienia zarządzanego musi mieć skonfigurowaną Ponadto Konfigurowanie bazy danych diagnostyki telemetrii, jako wyjaśnione dalej poniżej. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla pul elastycznych

Następujące dane diagnostyczne i telemetryczne jest dostępna dla kolekcji dla elastycznych pul zasobów:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Pula elastyczna** | [Wszystkie metryki](sql-database-metrics-diag-logging.md#all-metrics) zawiera procent eDTU/użycia procesora CPU, limit jednostek eDTU/procesora CPU, fizycznych procent odczytanych danych, dzienników zapisu procent, procent sesji, procent pracowników, magazynu, procent użycia magazynu, limit przestrzeni dyskowej i procent użycia magazynu XTP. |

Do przesyłania strumieniowego dane diagnostyczne i telemetryczne dla **zasobów puli elastycznej**, wykonaj następujące kroki:

- Przejdź do zasobu elastycznej puli w witrynie Azure portal
- Wybierz **ustawień diagnostycznych**
- Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** Aby edytować poprzednie ustawienia
- Wpisz nazwę dla ustawienia — do własnych potrzeb
- Wybierz zasoby, które mają strumienia danych diagnostycznych z elastycznej puli: **archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**
- W przypadku, gdy usługi Log Analytics jest zaznaczone, zaznacz **Konfiguruj** i Utwórz nowy obszar roboczy, wybierając **+ Utwórz nowy obszar roboczy**, lub wybierz istniejący obszar roboczy
- Zaznacz pole wyboru dla puli elastycznej dane diagnostyczne i telemetryczne **AllMetrics**
- Kliknij pozycję **Zapisz**

Powtórz powyższe kroki dla każdej puli elastycznej, którą chcesz monitorować.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Konfigurowanie, przesyłanie strumieniowe dane diagnostyczne i telemetryczne do wystąpienia zarządzanego

Następujące dane diagnostyczne i telemetryczne są dostępne do kolekcji dla wystąpienia zarządzanego zasobu:

| Zasób | Monitorowanie danych telemetrycznych |
| :------------------- | ------------------- |
| **Wystąpienie zarządzane** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) zawiera liczbę rdzeni wirtualnych, średni procent użycia procesora CPU, we/wy żądań, bajtów odczytanych/zapisanych, zarezerwowane miejsce do magazynowania, użyte miejsce do magazynowania. |

Do przesyłania strumieniowego dane diagnostyczne i telemetryczne dla **wystąpienia zarządzanego zasobu**, wykonaj następujące kroki:

- Przejdź do wystąpienia zarządzanego zasobu w witrynie Azure portal
- Wybierz **ustawień diagnostycznych**
- Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** Aby edytować poprzednie ustawienia
- Wpisz nazwę dla ustawienia — do własnych potrzeb
- Wybierz zasoby, które mają strumienia danych diagnostycznych z elastycznej puli: **archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**
- W przypadku, gdy wybrano usługi Log Analytics, utworzyć lub użyć istniejącego obszaru roboczego
- Zaznacz pole wyboru dane diagnostyczne i telemetryczne przykładowo **ResourceUsageStats**
- Kliknij pozycję **Zapisz**

Powtórz powyższe kroki dla każdego wystąpienia zarządzanego, chcesz monitorować.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Włącz rejestrowanie dla usługi Azure SQL Database lub baz danych w wystąpieniu zarządzanym

Metryki i Diagnostyka logowanie do bazy danych SQL i baz danych w wystąpieniu zarządzanym usługi nie jest włączona domyślnie.

Następujące dane diagnostyczne i telemetryczne są dostępne do kolekcji dla baz danych SQL Azure i bazy danych w wystąpieniu zarządzanym usługi:

| Monitorowanie telemetrii dla baz danych | Obsługa usługi Azure SQL Database | Bazy danych w wystąpieniu zarządzanym usługi pomocy technicznej |
| :------------------- | ------------------- | ------------------- |
| [Wszystkie metryki](sql-database-metrics-diag-logging.md#all-metrics): ograniczenie liczby jednostek DTU/procesora CPU procentowe zawiera jednostki DTU, użycie procesora CPU, procent, procent zapisu dziennika, ilość odczytanych danych fizycznych Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu, a Procent użycia magazynu XTP. | Yes | Nie |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): zawiera informacje o statystyki czasu wykonywania zapytania, takie są użycie procesora CPU i Statystyki czasu trwania zapytania. | Yes | Yes |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): zawiera informacje o statystyki oczekiwania zapytań, które informujący o tym, co zapytań oczekiwany, takie jak procesor CPU, DZIENNIKÓW i blokowanie. | Yes | Yes |
| [Błędy](sql-database-metrics-diag-logging.md#errors-dataset): zawiera informacje na temat błędów programu SQL, które wystąpiły dla tej bazy danych. | Yes | Nie |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): zawiera informacje, o ile czasu bazy danych poświęcony oczekiwanie na oczekiwania różnych typów. | Yes | Nie |
| [Limity czasu](sql-database-metrics-diag-logging.md#time-outs-dataset): zawiera informacje dotyczące limitów czasu, który wystąpił w bazie danych. | Yes | Nie |
| [Bloki](sql-database-metrics-diag-logging.md#blockings-dataset): zawiera informacje o blokowaniu zdarzenia, które wystąpiły w bazie danych. | Yes | Nie |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): zawiera inteligentne szczegółowych informacji o wydajności. [Dowiedz się więcej o Intelligent Insights](sql-database-intelligent-insights.md). | Yes | Yes |

### <a name="azure-portal"></a>Azure Portal

Przesyłanie strumieniowe dane diagnostyczne i telemetryczne do usługi Azure SQL Database i baz danych w wystąpieniu zarządzanym do miejsc docelowych z usługi Azure storage, usługi event hubs lub usługi Log Analytics jest skonfigurowana za pośrednictwem menu ustawienia diagnostyki dla wszystkich wymienionych baz danych w witrynie Azure portal.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Konfigurowanie, przesyłanie strumieniowe dane diagnostyczne i telemetryczne do usługi Azure SQL Database

Do przesyłania strumieniowego dane diagnostyczne i telemetryczne dla **usługi Azure SQL Database**, wykonaj następujące kroki:

- Przejdź do zasobu usługi Azure SQL Database
- Wybierz **ustawień diagnostycznych**
- Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** Aby edytować poprzednie ustawienia
- Można utworzyć maksymalnie trzech (3) równoległych połączeń dane diagnostyczne i telemetryczne strumienia. Aby skonfigurować wiele równoległych strumieniowe przesyłanie danych diagnostycznych do wielu zasobów, wybierz **+ Dodaj ustawienie diagnostyczne** utworzyć dodatkowe ustawienie.

   ![Włącz w witrynie Azure portal](./media/sql-database-metrics-diag-logging/enable-portal.png)

- Wpisz nazwę dla ustawienia — do własnych potrzeb
- Wybierz zasoby, które mają strumienia danych diagnostycznych z bazy danych: **archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**
- Standardowe środowisko monitorowania, zaznacz pola wyboru dla bazy danych diagnostycznych dzienników telemetrii: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics** , **QueryStoreWaitStatistics**, **błędy**, **DatabaseWaitStatistics**, **przekroczeń limitu czasu**, **bloków** , **Zakleszczenia**. Tych danych telemetrycznych jest zdarzenie na podstawie i udostępnia standardowy środowiska do monitorowania.
- Zaawansowane środowisko monitorowania, zaznacz pole wyboru obok pozycji **AllMetrics**. Jest to 1 minutę oparte na danych telemetrycznych dla telemetrii diagnostyki bazy danych, zgodnie z powyższym opisem. 

   ![Ustawienia diagnostyczne](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

Powtórz powyższe kroki dla każdej bazy danych SQL Azure, chcesz monitorować.

> [!NOTE]
> Nie można włączyć dziennika inspekcji z ustawień diagnostycznych bazy danych, nawet, jeśli opcja jest wyświetlana. Aby włączyć strumieniowe przesyłanie dzienników inspekcji, zobacz [konfigurowania inspekcji dla bazy danych](sql-database-auditing.md#subheading-2)
>

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Skonfigurować transmisję strumieniową z dane diagnostyczne i telemetryczne dla baz danych w wystąpieniu zarządzanym

Do przesyłania strumieniowego dane diagnostyczne i telemetryczne dla **baz danych w wystąpieniu zarządzanym**, wykonaj następujące kroki:

- Przejdź do bazy danych w wystąpieniu zarządzanym
- Wybierz **ustawień diagnostycznych**
- Wybierz **Włącz diagnostykę** Jeśli nie poprzednie ustawienia istnieje, lub wybierz **Edytuj ustawienie** Aby edytować poprzednie ustawienia
- Można utworzyć maksymalnie trzech (3) równoległych połączeń dane diagnostyczne i telemetryczne strumienia. Aby skonfigurować wiele równoległych strumieniowe przesyłanie danych diagnostycznych do wielu zasobów, wybierz **+ Dodaj ustawienie diagnostyczne** utworzyć dodatkowe ustawienie.
- Wpisz nazwę dla ustawienia — do własnych potrzeb
- Wybierz zasoby, które mają strumienia danych diagnostycznych z bazy danych: **archiwum do konta magazynu**, **Stream do usługi event hub**, lub **wysyłanie do usługi Log Analytics**
- Zaznacz pola wyboru dla bazy danych dane diagnostyczne i telemetryczne: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** i **błędy**

   ![Ustawienia diagnostyczne](./media/sql-database-metrics-diag-logging/diagnostics-portal-mi.png)

Powtórz powyższe kroki dla każdej bazy danych w wystąpieniu zarządzanym, chcesz monitorować.

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

### <a name="to-configure-multiple-azure-subscriptions"></a>Aby skonfigurować wiele subskrypcji platformy Azure

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

Uzyskać informacje dotyczące sposobu [zmiany ustawień diagnostycznych przy użyciu interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Uzyskać informacje dotyczące sposobu [włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu usługi Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Stream w usłudze Log Analytics 

Dzienniki metryki i Diagnostyka bazy danych SQL może być przesyłany strumieniowo do usługi Log Analytics przy użyciu wbudowanych **wysyłanie do usługi Log Analytics** opcji w portalu. Możesz również włączyć usługi Log Analytics przy użyciu ustawienia diagnostyki za pomocą poleceń cmdlet programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST usługi Azure Monitor.

### <a name="installation-overview"></a>Omówienie instalacji

Monitorowanie floty bazy danych SQL Database jest proste z usługą Log Analytics. Wymagane są trzy kroki:

1. Tworzenie zasobu usługi Log Analytics.

2. Konfigurowanie bazy danych do rekordów dzienników metryki i Diagnostyka do zasobu usługi Log Analytics, który został utworzony.

3. Zainstaluj **usługi Azure SQL Analytics** rozwiązania w portalu Azure Marketplace.

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

## <a name="stream-into-event-hubs"></a>Przesyłanie strumieniowe do usługi Event Hubs

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

## <a name="data-retention-policy-and-pricing"></a>Zasady przechowywania danych i ceny

Wybranie Event Hubs lub konta magazynu, można określić zasady przechowywania. Ta zasada usuwa dane starsze niż w wybranym okresie. Jeśli określisz usługi Log Analytics, zasady przechowywania zależy od wybranej warstwy cenowej. Ma zastosowanie użycie dane diagnostyczne i telemetryczne powyżej bezpłatne jednostki pozyskiwanie danych przydzielone każdego miesiąca. Bezpłatne jednostki pozyskiwanie danych, pod warunkiem włączyć, bezpłatne monitorowanie kilka baz danych każdego miesiąca. Należy pamiętać, że aktywnych baz danych o większych obciążeń będzie pozyskiwania większej ilości danych i baz danych w stanie bezczynności. Aby uzyskać więcej informacji, zobacz [cen usługi Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Jeśli używasz usługi Azure SQL Analytics, możesz łatwo monitorować swoje użycie pozyskiwania danych w rozwiązaniu, wybierając obszar roboczy pakietu OMS w menu nawigacji usługi Azure SQL Analytics, a następnie wybierając użycie i szacowane koszty.

## <a name="metrics-and-logs-available"></a>Metryki i dostępnych dzienników

Można znaleźć szczegółowe monitorowania zawartości telemetrii metryk i dzienników dostępnych dla usługi Azure SQL Database, pul elastycznych, wystąpienia zarządzanego i baz danych w wystąpieniu zarządzanym usługi dla swojej **niestandardowe analizy** i **aplikacji Programowanie** przy użyciu [języka SQL Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="all-metrics"></a>Wszystkie metryki

### <a name="all-metrics-for-elastic-pools"></a>Wszystkie metryki dla pul elastycznych

|**Zasób**|**Metryki**|
|---|---|
|Pula elastyczna|Procent eDTU używane liczby jednostek eDTU, limitu liczby jednostek eDTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, procent sesji, procent pracowników, Magazyn, procent użycia magazynu, limit przestrzeni dyskowej, procent użycia magazynu XTP |

### <a name="all-metrics-for-azure-sql-database"></a>Wszystkie metryki usługi Azure SQL Database

|**Zasób**|**Metryki**|
|---|---|
|Azure SQL Database|Procentowa wartość jednostki DTU, używane jednostki DTU, limit jednostek DTU, procent użycia procesora CPU i procent odczytu danych fizycznych, dziennik zapisu procent, Powodzenie/niepowodzenie/blokada połączeń zapory, procent sesji, procent pracowników, magazynu, procent użycia magazynu, XTP procent użycia magazynu, a zakleszczenia |

## <a name="logs"></a>Dzienniki

### <a name="logs-for-managed-instance"></a>Dzienniki dla wystąpienia zarządzanego

### <a name="resource-usage-stats"></a>Statystyki użycia zasobów

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: ResourceUsageStats|
|Zasób|Nazwa zasobu.|
|ResourceType|Nazwa typu zasobu. Zawsze: MANAGEDINSTANCES|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa wystąpienia zarządzanego.|
|ResourceId|Identyfikator URI zasobu.|
|SKU_s|Zarządzane wystąpienia produktu jednostki SKU|
|virtual_core_count_s|Numver dostępne rdzenie wirtualne|
|avg_cpu_percent_s|Średni procent użycia procesora CPU|
|reserved_storage_mb_s|Pojemność magazynu zarezerwowane na wystąpieniu zarządzanym|
|storage_space_used_mb_s|Magazynu używanych na wystąpieniu zarządzanym|
|io_requests_s|Liczba operacji We/Wy|
|io_bytes_read_s|Odczytano bajtów na SEKUNDĘ|
|io_bytes_written_s|Zapisano bajtów na SEKUNDĘ|

### <a name="logs-for-azure-sql-database-and-managed-instance-database"></a>Dzienniki dla bazy danych Azure SQL Database i wystąpienia zarządzanego

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

### <a name="deadlocks-dataset"></a>Zakleszczenie zestawu danych

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC] |Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: zakleszczenie|
|OperationName|Nazwa operacji. Zawsze: DeadlockEvent|
|Zasób|Nazwa zasobu.|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych. |
|ResourceId|Identyfikator URI zasobu.|
|deadlock_xml_s|Zakleszczenie raportu XML.|

### <a name="automatic-tuning-dataset"></a>Automatyczne dostrajanie zestawu danych

|Właściwość|Opis|
|---|---|
|Identyfikator dzierżawy|Twoim identyfikatorem dzierżawy.|
|SourceSystem|Zawsze: Azure|
|TimeGenerated [UTC]|Sygnatura czasowa podczas rejestrowania.|
|Typ|Zawsze: AzureDiagnostics|
|ResourceProvider|Nazwa dostawcy zasobów. Zawsze: MICROSOFT. SQL|
|Kategoria|Nazwa kategorii. Zawsze: AutomaticTuning|
|Zasób|Nazwa zasobu.|
|ResourceType|Nazwa typu zasobu. Zawsze: Serwery/baz danych|
|SubscriptionId|Identyfikator GUID, który bazy danych należy do subskrypcji.|
|ResourceGroup|Nazwa grupy zasobów, do której należy bazy danych.|
|LogicalServerName_s|Nazwa serwera, na którym należy baza danych.|
|LogicalDatabaseName_s|Nazwa bazy danych.|
|ElasticPoolName_s|Nazwa puli elastycznej bazy danych należy, jeśli istnieje.|
|DatabaseName_s|Nazwa bazy danych.|
|ResourceId|Identyfikator URI zasobu.|
|RecommendationHash_s|Unikatowy skrót zalecenia dostrajania automatycznego.|
|OptionName_s|Operacja automatycznego dostrajania.|
|Schema_s|Schemat bazy danych.|
|Table_s|Tabela, w których to dotyczy.|
|IndexName_s|Nazwa indeksu.|
|IndexColumns_s|Nazwa kolumny.|
|IncludedColumns_s|Uwzględnione kolumny.|
|EstimatedImpact_s|Szacowany wpływ rekomendacji dostrajania automatycznego JSON.|
|Event_s|Typ zdarzenia dostrajania automatycznego.|
|Timestamp_t|Ostatnia aktualizacja: sygnatura czasowa.|

### <a name="intelligent-insights-dataset"></a>Intelligent Insights zestawu danych.
Dowiedz się więcej o [format dziennika Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak włączyć rejestrowanie i zrozumieć kategorie metryk i dzienników, obsługiwane przez różne usługi platformy Azure, przeczytaj:

 * [Przegląd metryk w systemie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Przegląd dzienników diagnostyki platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Aby dowiedzieć się więcej na temat usługi Event Hubs, przeczytaj:

* [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Aby dowiedzieć się więcej na temat magazynu, zobacz temat jak [pobieranie metryki i Diagnostyka dzienników z usługi Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
