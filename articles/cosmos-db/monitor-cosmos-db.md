---
title: Azure Cosmos DB monitorowania | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250388"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB monitorowania
Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez bazy danych usługi Azure Cosmos oraz sposób używania funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Cosmos DB tworzy dane monitorowania przy użyciu [Azure monitor](../azure-monitor/overview.md) , który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych. 

Jeśli nie znasz jeszcze monitorowania usług platformy Azure, Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) , w którym opisano następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując określone dane zebrane z Azure Cosmos DB i dostarczając przykłady dotyczące konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor dla Cosmos DB (wersja zapoznawcza)
[Azure monitor dla Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) jest oparta na [funkcjach skoroszytów Azure monitor](../azure-monitor/app/usage-workbooks.md) i korzysta z tych samych danych monitorowania zebranych dla Cosmos DB opisanych w poniższych sekcjach. Za pomocą tego narzędzia można zapoznać się z ogólną wydajnością, niepowodzeniami, pojemnością i kondycją operacyjną wszystkich zasobów Azure Cosmos DB w ujednoliconym środowisku interaktywnym oraz korzystać z innych funkcji Azure Monitor na potrzeby szczegółowej analizy i generowania alertów. 

![Azure Monitor Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Wyświetl metryki poziomu operacji dla Azure Cosmos DB

1. Zaloguj się do [Azure portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   ![Okienko metryki w Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. W okienku **metryki** > **Wybierz zasób** > Wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   ![Wybierz konto Cosmos DB, aby wyświetlić metryki](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **jednostki żądania** i **średnika** jako wartość agregacji.

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć średnią liczbę jednostek żądań zużytych na minutę.  

   ![Wybierz metrykę z Azure Portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Dodawanie filtrów do metryk

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **OperationType**, **region**i **StatusCode**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **OperationType** , i wybierz wartość, taką jak **zapytanie**. Następnie Wykres wyświetla jednostki żądania wykorzystane dla operacji zapytania w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

![Dodaj filtr, aby wybrać stopień szczegółowości metryki](./media/monitor-cosmos-db/add-metrics-filter.png)

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** . Na przykład można grupować jednostki żądań na typ operacji i wyświetlać wykres dla wszystkich operacji na raz, jak pokazano na poniższej ilustracji:

![Dodaj filtr podziału zastosowania](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Oto inny przykład wyświetlania metryk opóźnienia po stronie serwera dla określonej bazy danych, kontenera lub operacji:

![Metryki opóźnienia po stronie serwera](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitorowanie danych zebranych z Azure Cosmos DB

Azure Cosmos DB gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez Azure Cosmos DB, zobacz [Informacje o danych monitorowania Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Strona **Przegląd** w Azure Portal dla każdej bazy danych usługi Azure Cosmos zawiera krótki widok użycia bazy danych, w tym jego żądanie i co godzinę użycie rozliczeń. Jest to przydatne informacje, ale tylko niewielka ilość dostępnych danych monitorowania. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy zaraz po utworzeniu bazy danych i włączeniu dodatkowej kolekcji danych z konfiguracją.

![Strona przeglądu](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analizowanie danych metryki

Azure Cosmos DB udostępnia niestandardowe środowisko pracy z metrykami. Aby uzyskać szczegółowe informacje na temat korzystania z tego środowiska Azure Cosmos DB, zobacz temat [monitorowanie i debugowanie Azure Cosmos DB metryki z Azure monitor](cosmos-db-azure-monitor-metrics.md) .

Metryki dla Azure Cosmos DB z metrykami z innych usług platformy Azure za pomocą Eksploratora metryk można analizować, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md) . Wszystkie metryki dla Azure Cosmos DB znajdują się w przestrzeni nazw **Cosmos DB metrykach standardowych**. Podczas dodawania filtru do wykresu można użyć następujących wymiarów z tymi metrykami:

- collectionName
- DatabaseName
- OperationType
- Region
- StatusCode


## <a name="analyzing-log-data"></a>Analizowanie danych dziennika
Dane w dziennikach Azure Monitor są przechowywane w tabelach, dla których każda tabela ma swój własny zestaw unikatowych właściwości. Azure Cosmos DB przechowuje dane w poniższych tabelach.

| Tabela | Opis |
|:---|:---|
| AzureDiagnostics | Wspólna tabela używana przez wiele usług do przechowywania dzienników zasobów. Dzienniki zasobów z Azure Cosmos DB mogą być zidentyfikowane przy użyciu `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Wspólna tabela przechowująca wszystkie rekordy z dziennika aktywności. 


> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Azure Cosmos DB, log Analytics zostanie otwarty z zakresem zapytania ustawionym na bieżącą bazę danych usługi Azure Cosmos. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics zapytań w Azure Monitor

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku wyszukiwania **przeszukiwania dzienników** , aby ułatwić monitorowanie kontenerów usługi Azure Cosmos. Te zapytania działają w [nowym języku](../log-analytics/log-analytics-log-search-upgrade.md).

Poniżej przedstawiono zapytania, których można użyć do monitorowania baz danych usługi Azure Cosmos.

* Aby wykonać zapytanie dotyczące wszystkich dzienników diagnostycznych z usługi Azure Cosmos DB w określonym przedziale czasu:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Aby wysłać zapytanie 10 najbardziej niedawno zarejestrowane zdarzenia:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Aby wykonać zapytanie dla wszystkich operacji, pogrupowane według typu operacji:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Aby wykonać zapytanie o wszystkie operacje, pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Aby wykonać zapytanie dla wszystkich działań użytkowników, pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Aby uzyskać wszystkie zapytania większe niż 100 jednostek ru dołączone do danych z **DataPlaneRequests** i **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Aby wykonać zapytanie, dla którego trwać dłużej niż 3 milisekundy operacje:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Aby wykonać zapytanie, które agent jest uruchomiony operacje:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Aby wyszukać podczas długotrwałych operacji zostały wykonane:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Aby uzyskać statystykę klucza partycji umożliwiającą ocenę pochylenia w pierwszych 3 partycjach dla konta bazy danych:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programowe monitorowanie usługi Azure Cosmos DB
Metryki poziomu konta dostępne w portalu, takich jak żądania użycia i łączna liczba kont magazynu, nie są dostępne za pośrednictwem interfejsów API SQL. Jednakże możesz pobrać dane użycia na poziomie kolekcji przy użyciu interfejsów API SQL. Aby pobrać danych na poziomie kolekcji, wykonaj następujące czynności:

* Aby użyć interfejsu API REST, [Wykonaj OPERACJĘ Get na kolekcji](https://msdn.microsoft.com/library/mt489073.aspx). Przydział i użycie informacji dla kolekcji jest zwracany w nagłówków x-ms-resource przydziału i x-ms-resource użycia w odpowiedzi.
* Aby użyć zestawu .NET SDK, użyj metody [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , która zwraca [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , która zawiera wiele właściwości użycia, takich jak **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**i inne.

Aby uzyskać dostęp do dodatkowych metryk, użyj [zestawu SDK Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostępne definicje metryk mogą być pobierane przez wywołanie metody:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Zapytania, aby pobrać poszczególne metryki, użyj następującego formatu:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o dziennikach i metrykach utworzonych przez Azure Cosmos DB, zobacz temat [Informacje o danych monitorowania Azure Cosmos DB](monitor-cosmos-db-reference.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
