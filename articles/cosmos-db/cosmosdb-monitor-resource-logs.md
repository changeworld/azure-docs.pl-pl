---
title: Monitorowanie danych usługi Azure Cosmos DB przy użyciu ustawień diagnostyki platformy Azure
description: Dowiedz się, jak używać ustawień diagnostyki platformy Azure do monitorowania wydajności i dostępności danych przechowywanych w usłudze Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521048"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorowanie danych usługi Azure Cosmos DB przy użyciu ustawień diagnostycznych na platformie Azure

Ustawienia diagnostyczne na platformie Azure są używane do zbierania dzienników zasobów. Dzienniki zasobów platformy Azure są emitowane przez zasób i dostarczają bogatych, częstych danych dotyczących działania tego zasobu. Te dzienniki są przechwytywane na żądanie i są również określane jako "dzienniki płaszczyzny danych". Niektóre przykłady operacji na płaszczyźnie danych obejmują usuwanie, wstawianie i odczyt. Zawartość tych dzienników różni się w zależności od typu zasobu.

Metryki platformy i dzienniki aktywności są zbierane automatycznie, podczas gdy należy utworzyć ustawienie diagnostyczne do zbierania dzienników zasobów lub przesyłania dalej ich poza usługą Azure Monitor. Ustawienie diagnostyczne dla kont usługi Azure Cosmos można włączyć, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

1. Przejdź do konta usługi Azure Cosmos. Otwórz **okienko Ustawienia diagnostyczne,** a następnie wybierz pozycję Dodaj ustawienie **diagnostyczne.**

1. W okienku **Ustawienia diagnostyczne** wypełnij formularz następującymi szczegółami: 

    * **Nazwa**: Wprowadź nazwę dzienników do utworzenia.

    * Dzienniki do archiwum można **przechowywać na koncie magazynu**, **Przesyłaj strumieniowo do centrum zdarzeń** lub Wyślij do usługi Log **Analytics**

1. Podczas tworzenia ustawienia diagnostycznego, należy określić, które kategorie dzienników do zbierania. Kategorie dzienników obsługiwanych przez usługę Azure Cosmos DB są wymienione poniżej wraz z dziennikiem przykładowym zebranym przez nich:

 * **DataPlaneRequests:** Wybierz tę opcję, aby rejestrować żądania zaplecza do wszystkich interfejsów API, które obejmują konta SQL, Graph, MongoDB, Cassandra i Table API w usłudze Azure Cosmos DB. Kluczowe właściwości, które `Requestcharge`należy `statusCode` `clientIPaddress`zwrócić `partitionID`na uwagę, to: , , i .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontu, aby obsługiwać żądania do interfejsu API usługi Azure Cosmos DB dla mongodb. Ten typ dziennika nie jest dostępny dla innych kont interfejsu API. Kluczowe właściwości, na `Requestcharge`które `opCode`należy zwrócić uwagę to: , . Po włączeniu MongoRequests w dziennikach diagnostycznych, upewnij się, aby wyłączyć DataPlaneRequests. Zostanie wyświetlony jeden dziennik dla każdego żądania w interfejsie API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests:** Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontu, aby obsługiwać żądania do interfejsu API usługi Azure Cosmos DB dla Kasandry. Ten typ dziennika nie jest dostępny dla innych kont interfejsu API. Kluczowe właściwości, które `operationName`należy `requestCharge` `piiCommandText`zwrócić na uwagę, to , . Po włączeniu CassandraRequests w dziennikach diagnostycznych, upewnij się, aby wyłączyć DataPlaneRequests. Zostanie wyświetlony jeden dziennik dla każdego żądania w interfejsie API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Wybierz tę opcję, aby rejestrować tekst kwerendy, który został wykonany. Ten typ dziennika jest dostępny tylko dla kont interfejsu API SQL.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Wybierz tę opcję, aby rejestrować statystyki kluczy partycji. Jest to obecnie reprezentowane z rozmiarem magazynu (KB) kluczy partycji. Zobacz [rozwiązywanie problemów przy użyciu zapytań usługi Azure Diagnostic](#diagnostic-queries) sekcji tego artykułu. Na przykład kwerendy, które używają "PartitionKeyStatistics". Dziennik jest emitowany względem pierwszych trzech kluczy partycji, które zajmują większość magazynu danych. Ten dziennik zawiera dane, takie jak identyfikator subskrypcji, nazwa regionu, nazwa bazy danych, nazwa kolekcji, klucz partycji i rozmiar magazynu w KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Ten dziennik raportuje zagregowane zużycie kluczy partycji przez sekundę RU/s. Obecnie usługa Azure Cosmos DB raportuje klucze partycji tylko dla kont interfejsu API SQL oraz operacji odczytu/zapisu punktowego i procedur składowanych. inne interfejsy API i typy operacji nie są obsługiwane. W przypadku innych interfejsów API kolumna klucza partycji w tabeli dziennika diagnostycznego będzie pusta. Ten dziennik zawiera dane, takie jak identyfikator subskrypcji, nazwa regionu, nazwa bazy danych, nazwa kolekcji, klucz partycji, typ operacji i opłata za żądanie. Zobacz [rozwiązywanie problemów przy użyciu zapytań usługi Azure Diagnostic](#diagnostic-queries) sekcji tego artykułu. Na przykład kwerendy, które używają "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Ten dziennik zawiera szczegóły dotyczące operacji na płaszczyźnie sterowania, takich jak tworzenie konta, dodawanie lub usuwanie regionu, aktualizowanie ustawień replikacji konta itp. Ten typ dziennika jest dostępny dla wszystkich typów interfejsu API, które obejmują SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Żądania:** Wybierz tę opcję, aby zbierać dane metryki z usługi Azure Cosmos DB do miejsc docelowych w ustawieniu diagnostycznym. Są to te same dane zbierane automatycznie w metryki platformy Azure. Zbieranie danych metryk za pomocą dzienników zasobów do analizowania obu rodzajów danych razem i do wysyłania danych metryk poza usługą Azure Monitor.

Aby uzyskać szczegółowe informacje dotyczące tworzenia ustawień diagnostycznych przy użyciu witryny Azure portal, interfejsu wiersza polecenia lub programu PowerShell, zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników platformy i metryk w artykule platformy Azure.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Rozwiązywanie problemów z kwerendami diagnostycznymi

1. Jak uzyskać opłaty za żądania za drogie zapytania?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Jak znaleźć, które operacje są biorąc większość RU / s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Jak uzyskać dystrybucję dla różnych operacji?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Jaka jest maksymalna przepływność, która została wykorzystana przez partycję?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Jak uzyskać informacje o zużyciu kluczy partycji RU/s na sekundę?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Jak uzyskać opłatę za żądanie dla określonego klucza partycji

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Jak uzyskać najlepsze klucze partycji z większością RU / s zużyte w określonym okresie? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Jak uzyskać dzienniki dla kluczy partycji, których rozmiar magazynu jest większy niż 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Jak uzyskać partycji Kluczowe statystyki do oceny pochylenie w górę trzy partycje dla konta bazy danych?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure Monitor dla usługi Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorowanie i debugowanie za pomocą metryk w usłudze Azure Cosmos DB](use-metrics.md)
