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
ms.openlocfilehash: 9a36b46d11657ef52051f8bf8df1e4944051da23
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454262"
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

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitorowanie danych zebranych z Azure Cosmos DB
Azure Cosmos DB gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez Azure Cosmos DB, zobacz [Informacje o danych monitorowania Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Strona **Przegląd** w Azure Portal dla każdej bazy danych usługi Azure Cosmos zawiera krótki widok użycia bazy danych, w tym jego żądanie i co godzinę użycie rozliczeń. Jest to przydatne informacje, ale tylko niewielka ilość dostępnych danych monitorowania. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy zaraz po utworzeniu bazy danych i włączeniu dodatkowej kolekcji danych z konfiguracją.

![Strona przeglądu](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Metryki platformy i dziennik aktywności są zbierane automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby zbierać dzienniki zasobów lub przesyłać je dalej poza Azure Monitor. Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](../azure-monitor/platform/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.

Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane. Kategorie Azure Cosmos DB są wymienione poniżej wraz z przykładowymi danymi.

 * **DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania wewnętrznej bazy danych do wszystkich interfejsów API, które zawierają konta SQL, Graph, MongoDB, Cassandra i interfejs API tabel w Azure Cosmos DB. Właściwości klucza do uwagi: Requestcharge, statusCode, clientIPaddress i partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontonu, aby obsłużyć żądania do interfejsu API Azure Cosmos DB dla MongoDB. Żądania MongoDB będą wyświetlane w MongoRequests i DataPlaneRequests. Właściwości klucza do uwagi to: Requestcharge, opCode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Wybierz tę opcję, aby zarejestrować tekst zapytania, który został wykonany. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Wybierz tę opcję, aby rejestrować statystyki kluczy partycji. Jest to obecnie reprezentowane z rozmiarem magazynu (KB) kluczy partycji. Dziennik jest emitowany w oparciu o trzy pierwsze klucze partycji, które zajmują większość magazynu danych.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Żądania metryk**: Wybierz tę opcję, aby zbierać dane metryk z Azure Cosmos DB do miejsc docelowych w ustawieniu diagnostyki. Są to te same dane, które są zbierane automatycznie w ramach metryk platformy Azure. Zbieraj dane metryk z dziennikami zasobów, aby analizować jednocześnie oba rodzaje danych i wysyłać dane metryk poza Azure Monitor.

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
