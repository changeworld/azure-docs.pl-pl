---
title: Monitorowanie usługi Azure Cosmos DB | Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować wydajność i dostępność usługi Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250388"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitorowanie usługi Azure Cosmos DB
Jeśli masz krytyczne aplikacje i procesy biznesowe korzystające z zasobów platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i działania. W tym artykule opisano dane monitorowania generowane przez bazy danych usługi Azure Cosmos i jak można używać funkcji usługi Azure Monitor do analizowania i ostrzegania o tych danych.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Usługa Azure Cosmos DB tworzy dane monitorowania przy użyciu [usługi Azure Monitor,](../azure-monitor/overview.md) która jest usługą monitorowania pełnego stosu na platformie Azure, która zapewnia pełny zestaw funkcji do monitorowania zasobów platformy Azure oprócz zasobów w innych chmurach i lokalnie. 

Jeśli nie jesteś jeszcze zaznajomiony z monitorowaniem usług platformy Azure, zacznij od artykułu [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor,](../azure-monitor/insights/monitor-azure-resource.md) w którym opisano następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure do analizowania i ostrzegania o danych monitorowania

Poniższe sekcje opierają się na tym artykule, opisując określone dane zebrane z usługi Azure Cosmos DB i podając przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Usługa Azure Monitor for Cosmos DB (wersja zapoznawcza)
[Usługa Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) jest oparta na [funkcji skoroszytów usługi Azure Monitor](../azure-monitor/app/usage-workbooks.md) i używa tych samych danych monitorowania zebranych dla usługi Cosmos DB opisanych w poniższych sekcjach. Użyj tego narzędzia, aby wyświetlić ogólną wydajność, awarie, pojemność i kondycję operacyjną wszystkich zasobów usługi Azure Cosmos DB w ujednoliconym interaktywnym środowiskach i wykorzystać inne funkcje usługi Azure Monitor do szczegółowej analizy i alertów. 

![Azure Monitor for Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Wyświetlanie metryk poziomu operacji dla usługi Azure Cosmos DB

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Wybierz **pozycję Monitor** z lewego paska nawigacyjnego i wybierz pozycję **Metryki**.

   ![Okienko Metryki w usłudze Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. W okienku **Metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W przypadku **typu zasobu**wybierz pozycję **Konta usługi Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   ![Wybieranie konta usługi Cosmos DB w celu wyświetlenia danych](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Następnie możesz wybrać metrykę z listy dostępnych danych. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnienia, dostępności, Cassandra i innych. Aby dowiedzieć się szczegółowo o wszystkich dostępnych danych na tej liście, zobacz [Metryki według](monitor-cosmos-db-reference.md) kategorii artykułu. W tym przykładzie wybierzmy **jednostki żądania** i **Średnia** jako wartość agregacji.

   Oprócz tych szczegółów można również wybrać **zakres czasu** i **szczegółowość czasu** metryk. Maksymalnie możesz wyświetlać dane z ostatnich 30 dni.  Po zastosowaniu filtru wykres jest wyświetlany na podstawie filtru. Można zobaczyć średnią liczbę jednostek żądań zużytych na minutę dla wybranego okresu.  

   ![Wybieranie metryki z witryny Azure portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Dodawanie filtrów do danych

Można również filtrować metryki i wykres wyświetlany przez określony **Nazwa Kolekcji**, **Nazwa bazy danych**, **OperationType**, **Region**i **StatusCode**. Aby filtrować dane, wybierz **dodaj filtr** i wybierz wymaganą właściwość, taką jak **OperationType** i wybierz wartość, taką jak **Kwerenda**. Następnie na wykresie wyświetlane są jednostki żądań zużyte dla operacji kwerendy dla wybranego okresu. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach OperationType metryki.

![Dodawanie filtru w celu wybrania szczegółowości metryki](./media/monitor-cosmos-db/add-metrics-filter.png)

Metryki można grupować za pomocą opcji **Zastosuj podział.** Na przykład można pogrupować jednostki żądań według typu operacji i wyświetlić wykres dla wszystkich operacji jednocześnie, jak pokazano na poniższej ilustracji:

![Dodaj zastosuj filtr podziału](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Oto kolejny przykład, aby wyświetlić metryki opóźnienia po stronie serwera dla określonej bazy danych, kontenera lub operacji:

![Metryki opóźnienia po stronie serwera](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitorowanie danych zebranych z usługi Azure Cosmos DB

Usługa Azure Cosmos DB zbiera te same rodzaje danych monitorowania, co inne zasoby platformy Azure, które są opisane w [obszarze Monitorowanie danych z zasobów platformy Azure.](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) Zobacz [odwołanie do danych monitorowania usługi Azure Cosmos DB, aby](monitor-cosmos-db-reference.md) uzyskać szczegółowe informacje na temat dzienników i metryk utworzonych przez usługę Azure Cosmos DB.

Strona **Omówienie** w witrynie Azure portal dla każdej bazy danych usługi Azure Cosmos zawiera krótki widok użycia bazy danych, w tym jego żądania i co godzinę rozliczeń użycia. Jest to przydatne informacje, ale tylko niewielka ilość dostępnych danych monitorowania. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy, gdy tylko utworzysz bazę danych, podczas gdy można włączyć zbieranie dodatkowych danych w ramach niektórych konfiguracji.

![Strona przeglądowa](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analizowanie danych metrycznych

Usługa Azure Cosmos DB zapewnia niestandardowe środowisko pracy z metrykami. Zobacz [Monitorowanie i debugowanie metryk usługi Azure Cosmos DB z usługi Azure Monitor, aby](cosmos-db-azure-monitor-metrics.md) uzyskać szczegółowe informacje na temat korzystania z tego środowiska i analizowania różnych scenariuszy usługi Azure Cosmos DB.

Możesz analizować metryki usługi Azure Cosmos DB z metrykami z innych usług platformy Azure przy użyciu Eksploratora metryk, otwierając **metryki** z menu **Usługi Azure Monitor.** Zobacz [Wprowadzenie do Eksploratora metryk platformy Azure, aby](../azure-monitor/platform/metrics-getting-started.md) uzyskać szczegółowe informacje na temat korzystania z tego narzędzia. Wszystkie metryki usługi Azure Cosmos DB znajdują się w **standardowych metrykach usługi Cosmos DB**obszaru nazw. Podczas dodawania filtru do wykresu można użyć następujących wymiarów z tymi metrykami:

- CollectionName
- DatabaseName
- Operationtype
- Region
- Statuscode


## <a name="analyzing-log-data"></a>Analizowanie danych dziennika
Dane w dziennikach usługi Azure Monitor są przechowywane w tabelach, w których każda tabela ma swój własny zestaw unikatowych właściwości. Usługa Azure Cosmos DB przechowuje dane w poniższych tabelach.

| Tabela | Opis |
|:---|:---|
| AzureDiagnostics | Wspólna tabela używana przez wiele usług do przechowywania dzienników zasobów. Dzienniki zasobów z usługi Azure Cosmos `MICROSOFT.DOCUMENTDB`DB można zidentyfikować za pomocą pliku .   |
| AzureActivity    | Wspólna tabela, która przechowuje wszystkie rekordy z dziennika aktywności. 


> [!IMPORTANT]
> Po wybraniu **dzienników** z menu usługi Azure Cosmos DB usługa Log Analytics jest otwierana z ustawionym zakresem zapytania na bieżącą bazę danych usługi Azure Cosmos. Oznacza to, że kwerendy dziennika będzie zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić kwerendę, która zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz **dzienniki** z menu **Usługi Azure Monitor.** Zobacz [zakres zapytań dziennika i zakres czasu w usłudze Azure Monitor Log Analytics, aby](../azure-monitor/log-query/scope.md) uzyskać szczegółowe informacje.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Zapytania usługi Azure Cosmos DB Log Analytics w usłudze Azure Monitor

Oto kilka zapytań, które można wprowadzić w **pasku wyszukiwania dziennika,** aby ułatwić monitorowanie kontenerów usługi Azure Cosmos. Te zapytania działają z [nowym językiem](../log-analytics/log-analytics-log-search-upgrade.md).

Poniżej przedstawiono kwerendy, których można użyć, aby ułatwić monitorowanie baz danych usługi Azure Cosmos.

* Aby zbadać wszystkie dzienniki diagnostyczne z usługi Azure Cosmos DB przez określony czas:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Aby zbadać 10 ostatnio zarejestrowanych zdarzeń:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Aby wykonać kwerendę dla wszystkich operacji, pogrupowane według typu operacji:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Aby zbadać wszystkie operacje pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Aby zbadać wszystkie działania użytkownika, pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Aby uzyskać wszystkie zapytania większe niż 100 RU połączone z danymi z **DataPlaneRequests** i **QueryRunTimeStatistics**.

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

* Aby zbadać, dla których operacje trwać dłużej niż 3 milisekundy:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Aby zbadać, dla którego agenta jest uruchomiony operacji:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Aby zbadać, kiedy zostały wykonane długotrwałe operacje:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Aby uzyskać statystyki klucza partycji, aby ocenić pochylenie w górę 3 partycje dla konta bazy danych:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programowo monitoruj usługę Azure Cosmos DB
Metryki poziomu konta dostępne w portalu, takie jak użycie magazynu kont i całkowita liczba żądań, nie są dostępne za pośrednictwem interfejsów API SQL. Można jednak pobrać dane użycia na poziomie kolekcji przy użyciu interfejsów API SQL. Aby pobrać dane na poziomie kolekcji, wykonaj następujące czynności:

* Aby użyć interfejsu API REST, [wykonaj GET na kolekcji](https://msdn.microsoft.com/library/mt489073.aspx). Informacje o przydziałze i użyciu dla kolekcji są zwracane w nagłówkach x-ms-resource-quota i x-ms-resource-usage w odpowiedzi.
* Aby użyć zestawu .NET SDK, należy użyć metody [DocumentClient.ReadDocumentCollectionAsync,](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) która zwraca [funkcję ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) zawierającą wiele właściwości użycia, takich jak **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**i inne.

Aby uzyskać dostęp do dodatkowych metryk, użyj [sdk usługi Azure Monitor.](https://www.nuget.org/packages/Microsoft.Azure.Insights) Dostępne definicje metryki można pobrać, wywołując:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Kwerendy do pobierania poszczególnych metryk używają następującego formatu:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Następne kroki

- Zobacz [odwołanie do danych monitorowania usługi Azure Cosmos DB,](monitor-cosmos-db-reference.md) aby uzyskać odwołanie do dzienników i metryk utworzonych przez usługę Azure Cosmos DB.
- Zobacz [monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor, aby](../azure-monitor/insights/monitor-azure-resource.md) uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure.
