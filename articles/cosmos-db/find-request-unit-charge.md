---
title: Znajdź (RU) Opłata za jednostki żądań w usłudze Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłat za jednostkę żądania dla każdej operacji na kontenerze usługi Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 833f815f0c84584f084e4d4637c0318f7c2daec0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888785"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Znajdź (RU) Opłata za jednostki żądań w usłudze Azure Cosmos DB

W tym artykule przedstawiono różne sposoby, aby znaleźć [jednostki żądań](request-units.md) zużycie wszelkie operacje wykonane na kontenerze usługi Azure Cosmos. Jest to obecnie możliwe do mierzenia użycia tego za pomocą witryny Azure portal lub sprawdzając odpowiedzi wysyłane z usługi Azure Cosmos DB za pomocą jednego z zestawów SDK.

## <a name="sql-core-api"></a>(Rdzenie) interfejsu API SQL

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Witryna Azure portal umożliwia obecnie znaleźć opłata za żądanie wyrażana dla zapytania SQL.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) i przekazywać je z danymi lub wybierz istniejące konto usługi Azure Cosmos, która już zawiera dane.

1. Otwórz **Eksplorator danych** okienka i wybierz kontener, w którym chcesz pracować.

1. Kliknij pozycję **nowe zapytanie SQL**.

1. Wprowadź prawidłową kwerendę, a następnie kliknij pozycję **wykonaj zapytanie**.

1. Kliknij pozycję **statystyki zapytań** do wyświetlenia opłaty rzeczywistego żądania dla żądania, po prostu został wykonany.

![Zrzut ekranu SQL zapytania żądania opłaty w witrynie Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Użyj zestawu SDK platformy .NET w wersji 2

Obiekty zwrócone przez [zestawu SDK platformy .NET w wersji 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (zobacz [ten przewodnik Szybki Start](create-sql-api-dotnet.md) dotyczące jego użycia) udostępnianie `RequestCharge` właściwości.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Obiekty zwrócone przez [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (zobacz [ten przewodnik Szybki Start](create-sql-api-java.md) dotyczące jego użycia) udostępnianie `getRequestCharge()` metody.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node.js

Obiekty zwrócone przez [zestawu SDK środowiska Node.js](https://www.npmjs.com/package/@azure/cosmos) (zobacz [ten przewodnik Szybki Start](create-sql-api-nodejs.md) dotyczące jego użycia) udostępnianie `headers` obiektu podrzędnego, który mapuje wszystkie nagłówki, które są zwracane przez podstawowego interfejsu API protokołu HTTP. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Korzystanie z języka Python SDK

`CosmosClient` Obiektu z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) (zobacz [ten przewodnik Szybki Start](create-sql-api-python.md) dotyczące jego użycia) udostępnia `last_response_headers` słownik, który mapuje wszystkie nagłówki, które są zwracane przez podstawowego interfejsu API protokołu HTTP dla Ostatnia operacja wykonana. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Opłata za jednostkę żądania jest uwidaczniany przez niestandardowy [bazy danych polecenie](https://docs.mongodb.com/manual/reference/command/) o nazwie `getLastRequestStatistics.` to polecenie zwraca dokument zawierający nazwę Ostatnia operacja wykonana, jego opłata za żądanie wyrażana i jego czas trwania.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Witryna Azure portal umożliwia obecnie znaleźć opłata za żądanie wyrażana tylko zapytania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) i przekazywać je z danymi lub wybierz istniejące konto, które zawiera już dane.

1. Otwórz **Eksplorator danych** okienka, a następnie wybierz kolekcję, którą chcesz pracować.

1. Kliknij pozycję **nowe zapytanie**.

1. Wprowadź prawidłową kwerendę, a następnie kliknij pozycję **wykonaj zapytanie**.

1. Kliknij pozycję **statystyki zapytań** do wyświetlenia opłaty rzeczywistego żądania dla żądania, po prostu został wykonany.

![Zrzut ekranu z bazy danych MongoDB zapytania żądania opłaty w witrynie Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Użyj sterownik MongoDB .NET

Korzystając z [oficjalne sterownik MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/) (zobacz [ten przewodnik Szybki Start](create-mongodb-dotnet.md) dotyczące jego użycia), polecenia mogą być wykonywane przez wywołanie metody `RunCommand` metody `IMongoDatabase` obiektu. Ta metoda wymaga implementacji `Command<>` klasy abstrakcyjnej.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Użyj sterownik MongoDB Java

Korzystając z [oficjalne sterownik MongoDB Java](http://mongodb.github.io/mongo-java-driver/) (zobacz [ten przewodnik Szybki Start](create-mongodb-java.md) dotyczące jego użycia), polecenia mogą być wykonywane przez wywołanie metody `runCommand` metody `MongoDatabase` obiektu.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Użyj sterownika bazy danych MongoDB Node.js

Korzystając z [oficjalne sterownik bazy danych MongoDB Node.js](https://mongodb.github.io/node-mongodb-native/) (zobacz [ten przewodnik Szybki Start](create-mongodb-nodejs.md) dotyczące jego użycia), polecenia mogą być wykonywane przez wywołanie metody `command` metody `db` obiektu.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Podczas wykonywania operacji dotyczących interfejsu API rozwiązania Cassandra usługi Azure Cosmos DB, opłat za jednostkę żądania jest zwracany w ładunku przychodzących jako pole o nazwie `RequestCharge`.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Podczas korzystania [zestawu .NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (zobacz [ten przewodnik Szybki Start](create-cassandra-dotnet.md) dotyczące jego użycia), ładunek przychodzące mogą być pobierane w ramach `Info` właściwość `RowSet` obiektu.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Korzystając z [zestawu Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (zobacz [ten przewodnik Szybki Start](create-cassandra-java.md) dotyczące jego użycia), przychodzące ładunku może być pobierany przez wywołanie `getExecutionInfo()` metody w `ResultSet` obiektu.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

### <a name="use-drivers-and-sdk"></a>Użyj sterowników i zestawu SDK

Zwrócone przez interfejs API języka Gremlin nagłówki są mapowane na stan niestandardowe atrybuty, które obecnie są udostępniane przez Gremlin .NET i zestawu SDK języka Java. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Korzystając z [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (zobacz [ten przewodnik Szybki Start](create-graph-dotnet.md) dotyczące jego użycia), stan atrybuty są dostępne w obszarze `StatusAttributes` właściwość `ResultSet<>` obiektu.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Korzystając z [zestawu SDK Java Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (zobacz [ten przewodnik Szybki Start](create-graph-java.md) dotyczące jego użycia), atrybuty stanu może być pobierany przez wywołanie `statusAttributes()` metody `ResultSet` obiektu.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>Interfejs API tabel

Tylko zestaw SDK obecnie zwracanie opłat za jednostkę żądania dla operacji tabeli [zestaw .NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (zobacz [ten przewodnik Szybki Start](create-table-dotnet.md) dotyczące jego użycia). `TableResult` Obiektu ujawnia `RequestCharge` właściwość, która jest wypełniana przez zestaw SDK, gdy jest używana dla interfejsu API tabel usługi Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat optymalizowania zużycia Twojego żądania:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizuj koszt aprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizuj koszt zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Skaluj globalnie aprowizowana przepływność](scaling-throughput.md)
* [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
* [How to provision throughput for a container (Jak aprowizować przepływność dla kontenera)](how-to-provision-container-throughput.md)