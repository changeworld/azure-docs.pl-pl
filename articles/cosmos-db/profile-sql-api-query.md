---
title: Pobierz metryki wydajności & wykonywania zapytań SQL
description: Dowiedz się, jak pobrać metryki wykonywania zapytań SQL i profil wydajności zapytań SQL żądań usługi Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998365"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Pobierz metryki wykonywania zapytań SQL i analizuj wydajność kwerend przy użyciu pliku .NET SDK

W tym artykule przedstawiono sposób profilowania wydajności zapytań SQL w usłudze Azure Cosmos DB. To profilowanie można `QueryMetrics` wykonać za pomocą pobranego z .NET SDK i jest szczegółowo tutaj. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) jest silnie wpisany obiekt z informacjami o wykonywaniu kwerendy wewnętrznej bazy danych. Te metryki są bardziej szczegółowo opisane w artykule [Tune Query Performance.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>Ustawianie parametru FeedOptions

Wszystkie przeciążenia [documentclient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) przyjmują opcjonalny parametr [FeedOptions.](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) Ta opcja umożliwia dostrojenie i sparametryzację wykonywania kwerend. 

Aby zebrać metryki wykonywania zapytań SQL, należy ustawić parametr [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) w [plikach FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) na `true`. Ustawienie `PopulateQueryMetrics` na true sprawi, `FeedResponse` że będzie `QueryMetrics`tak, że będzie zawierać odpowiednie . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Pobierz metryki zapytań z AsDocumentQuery()
Poniższy przykładowy kod pokazuje, jak pobrać metryki podczas korzystania z [asDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metoda:

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregowanie metryk zapytań

W poprzedniej sekcji należy zauważyć, że było wiele wywołań [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metody. Każde wywołanie `FeedResponse` zwróciło obiekt, `QueryMetrics`który ma słownik ; jeden dla każdej kontynuacji kwerendy. W poniższym przykładzie `QueryMetrics` pokazano, jak zagregować te przy użyciu LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Grupowanie metryk kwerendy według identyfikatora partycji

Identyfikator partycji `QueryMetrics` można pogrupować według identyfikatora partycji. Grupowanie według identyfikatora partycji pozwala sprawdzić, czy określona partycja powoduje problemy z wydajnością w porównaniu do innych. W poniższym przykładzie `QueryMetrics` pokazano, jak grupować z LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ na DocumentQuery

Można również uzyskać `FeedResponse` z LINQ Kwerendy przy użyciu `AsDocumentQuery()` metody:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Drogie zapytania

Można przechwycić jednostki żądania używane przez każdą kwerendę do zbadania kosztownych zapytań lub kwerend, które zużywają wysoką przepływność. Opłatę za żądanie można uzyskać za pomocą `FeedResponse`właściwości [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) w pliku . Aby dowiedzieć się więcej o tym, jak uzyskać opłatę za żądanie za pomocą witryny Azure portal i różnych zestawów SDK, zobacz [artykuł o oliczce jednostki żądania.](find-request-unit-charge.md)

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Pobierz czas wykonywania kwerendy

Podczas obliczania czasu wymaganego do wykonania kwerendy po stronie klienta, upewnij `ExecuteNextAsync` się, że zawiera tylko czas, aby wywołać metodę, a nie inne części bazy kodu. Tylko te wywołania pomagają w obliczaniu, jak długo trwa wykonywanie kwerendy, jak pokazano w poniższym przykładzie:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Skanowanie zapytań (często powolnych i kosztownych)

Kwerenda skanowania odwołuje się do kwerendy, która nie była obsługiwana przez indeks, z powodu którego wiele dokumentów są ładowane przed zwróceniem zestawu wyników.

Poniżej znajduje się przykład kwerendy skanowania:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtr tej kwerendy używa funkcji systemowej UPPER, która nie jest obsługiwana z indeksu. Wykonywanie tej kwerendy względem dużej kolekcji spowodowało powstanie następujących metryk kwerendy dla pierwszej kontynuacji:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Zwróć uwagę na następujące wartości z danych wyjściowych metryk kwerendy:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

W tej kwerendzie załadowano 60 951 dokumentów, których liczba wyniosła 399 998 938 bajtów. Ładowanie tej liczby bajtów powoduje wysokie koszty lub obciążenie jednostkowe żądania. Wykonanie kwerendy zajmuje również dużo czasu, co jest jasne z właściwością całkowitego czasu spędzonego:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Co oznacza, że wykonanie kwerendy trwało 4,5 sekundy (i była to tylko jedna kontynuacja).

Aby zoptymalizować tę przykładną kwerendę, należy unikać użycia UPPER w filtrze. Zamiast tego, gdy dokumenty są `c.description` tworzone lub aktualizowane, wartości muszą być wstawiane we wszystkich wielkich literach. Następnie kwerenda staje się: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Ta kwerenda może być teraz obsługiwana z indeksu.

Aby dowiedzieć się więcej o wydajności kwerendy dostrajania, zobacz artykuł [Dostrajanie wydajności kwerendy.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Dokumentacja

- [Azure Cosmos DB SQL specification (Specyfikacja języka SQL w usłudze Azure Cosmos DB)](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie wydajności zapytań](sql-api-query-metrics.md)
- [Omówienie indeksowania](index-overview.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
