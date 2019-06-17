---
title: Pobierz metryki wydajności i wykonywania zapytań SQL
description: Dowiedz się, jak pobrać profil wydajności zapytań SQL usługi Azure Cosmos DB żądań i metryk wykonywania zapytania SQL.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481567"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Pobierz metryki wykonywania zapytań SQL i analizować wydajność zapytań przy użyciu zestawu .NET SDK

W tym artykule przedstawiono sposób profilu wydajność zapytań SQL w usłudze Azure Cosmos DB. Ta profilowania może odbywać się przy użyciu `QueryMetrics` pobierane z zestawu SDK platformy .NET i została szczegółowo opisana w tym miejscu. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) to silnie typizowany obiekt informacje na temat wykonywania zapytań w wewnętrznej bazie danych. Te metryki są opisane bardziej szczegółowo w [Dostosowywanie wydajności zapytań](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artykułu.

## <a name="set-the-feedoptions-parameter"></a>Ustaw parametr FeedOptions

Wszystkie przeciążenia dla [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) zająć opcjonalny [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) parametru. Ta opcja jest o tym, co umożliwia wykonanie zapytania dopasowane i sparametryzowanych. 

Aby zbierać metryki wykonywania zapytania Sql, należy ustawić parametr [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) w [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) do `true`. Ustawienie `PopulateQueryMetrics` do wartości true spowoduje, że tak, aby `FeedResponse` będzie zawierać odpowiedni `QueryMetrics`. 

## <a name="get-query-metrics-with-asdocumentquery"></a>Pobierz metryki zapytania przy użyciu AsDocumentQuery()
Poniższy przykład kodu pokazuje, jak pobrać metryk, korzystając z [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) metody:

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
## <a name="aggregating-querymetrics"></a>Agregowanie QueryMetrics

W poprzedniej sekcji, zwróć uwagę, że były wielu wywołań do [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) metody. Każdy Wywołanie zwróciło `FeedResponse` obiekt, który ma słownik `QueryMetrics`; jeden dla każdego kontynuacji zapytania. Poniższy przykład pokazuje, jak je agregować `QueryMetrics` za pomocą LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>Metryki zapytania grupowanie według Identyfikatora partycji

Możesz grupować `QueryMetrics` według identyfikatora partycji. Grupowanie według Identyfikatora partycji umożliwia zobaczenie, czy określonej partycji jest przyczyną problemów z wydajnością w porównaniu do innych osób. Poniższy przykład pokazuje, jak należy zgrupować `QueryMetrics` za pomocą LINQ:

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

## <a name="linq-on-documentquery"></a>LINQ w DocumentQuery

Możesz też pobrać `FeedResponse` zapytanie LINQ, w którym używana jest `AsDocumentQuery()` metody:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Kosztownych zapytań

Można przechwycić jednostek żądania używane przez każde zapytanie do badania kosztownych zapytań lub zapytań, korzystających z dużej przepływności. Opłata za żądanie wyrażana można uzyskać za pomocą [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) właściwość `FeedResponse`. Aby dowiedzieć się więcej na temat sposobu uzyskania opłata za żądanie wyrażana przy użyciu witryny Azure portal i różne zestawy SDK, zobacz [znaleźć opłat za jednostkę żądania](find-request-unit-charge.md) artykułu.

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

## <a name="get-the-query-execution-time"></a>Uzyskaj czas wykonywania zapytania

Podczas obliczania czas wymagany do wykonania zapytania po stronie klienta, upewnij się, zawierać wyłącznie czasu wywołania `ExecuteNextAsync` metody, a nie inne części bazy kodu. Tylko te wywołania pomocy podczas obliczania, czas wykonywania zapytania trwania jak pokazano w poniższym przykładzie:

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>Skanowanie zapytań (często powolne i drogie)

Zapytanie skanowania odwołuje się do zapytania, który nie był obsługiwany przez indeks, z powodu, wiele dokumenty są załadowane przed zwróceniem zestawu wyników.

Poniżej przedstawiono przykład zapytania skanowania:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

To zapytanie filtru funkcja system GÓRNEGO, który nie jest obsługiwany z indeksu. Wykonującego to zapytanie względem dużych kolekcji produkowany następujące metryki zapytania dla pierwszej kontynuacji:

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

Zwróć uwagę na następujące wartości z danych wyjściowych metryki zapytania:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

To zapytanie załadować 60,951 dokumentów, które sumowane 399,998,938 bajtów. Ładowanie następująca liczba bajtów powoduje wysokiej opłaty jednostki kosztów ani żądania. Również zajmuje dużo czasu wykonywania zapytania, która wynika z właściwością całkowity czas spędzany:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Co oznacza, która zapytanie podjęło 4.5 sekundy, aby wykonać (jest to tylko jednej kontynuacji).

W celu zoptymalizowania to przykładowe zapytanie, należy unikać użycia GÓRNY w filtrze. Zamiast tego, kiedy dokumenty są tworzone lub aktualizowane, `c.description` wartości musi znajdować się w wszystkie znaki na wielkie litery. Zapytanie staje się: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

To zapytanie może teraz służyć z indeksu.

Aby dowiedzieć się więcej na temat dostrajania wydajności zapytań, zobacz [Dostosowywanie wydajności zapytań](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics) artykułu.

## <a id="References"></a>Dokumentacja

- [Azure Cosmos DB SQL specification (Specyfikacja języka SQL w usłudze Azure Cosmos DB)](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Kolejne kroki

- [Dostosowywanie wydajności zapytań](sql-api-query-metrics.md)
- [Omówienie indeksowania](index-overview.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
