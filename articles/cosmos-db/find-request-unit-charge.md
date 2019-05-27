---
title: Znajdź (RU) Opłata za jednostki żądań w usłudze Azure Cosmos DB
description: Dowiedz się, jak można znaleźć jednostki żądań (RU) opłaty dla wszelkie operacje wykonane na kontenerze usługi Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 8ccb7ae9869ec01b4f26009535af613ccb1d35d0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241120"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Znajdź opłata za jednostki żądań w usłudze Azure Cosmos DB

W tym artykule przedstawiono różne sposoby, można znaleźć [jednostki żądań](request-units.md) zużycie (RU) dla każdej operacji wykonywane względem kontenera w usłudze Azure Cosmos DB. Obecnie można zmierzyć to użycie tylko przy użyciu witryny Azure portal lub sprawdzając odpowiedzi wysyłane z usługi Azure Cosmos DB za pomocą jednego z zestawów SDK.

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

Jeśli używasz interfejsu API SQL masz wiele opcji do znajdowania zużycia jednostek RU dla operacji na kontenerze usługi Azure Cosmos.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie można znaleźć opłata za żądanie wyrażana w witrynie Azure portal, tylko w przypadku zapytania SQL.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) i przekazywać je z danymi lub wybierz istniejące konto usługi Azure Cosmos, która już zawiera dane.

1. Przejdź do **Eksplorator danych** okienka, a następnie wybierz kontener, aby pracować nad.

1. Wybierz **nowe zapytanie SQL**.

1. Wprowadź prawidłową kwerendę, a następnie wybierz **wykonaj zapytanie**.

1. Wybierz **statystyki zapytań** do wyświetlenia rzeczywiste żądanie opłata żądania, które zostało wykonane.

![Zrzut ekranu przedstawiający opłaty żądania zapytania SQL w witrynie Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Użyj zestawu SDK platformy .NET w wersji 2

Obiekty, które są zwracane z [zestawu SDK platformy .NET w wersji 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) ujawnić `RequestCharge` właściwości:

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

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji internetowej platformy .NET przy użyciu konta interfejsu API SQL w usłudze Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Obiekty, które są zwracane z [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) ujawnić `getRequestCharge()` metody:

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

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji Java przy użyciu konta z interfejsem API SQL usługi Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK środowiska Node.js

Obiekty, które są zwracane z [zestawu SDK środowiska Node.js](https://www.npmjs.com/package/@azure/cosmos) ujawnić `headers` podobiektów, która mapuje wszystkie nagłówki, które są zwracane przez podstawowego interfejsu API protokołu HTTP. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza:

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

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji Node.js przy użyciu konta z interfejsem API SQL usługi Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Korzystanie z języka Python SDK

`CosmosClient` Obiektu z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) udostępnia `last_response_headers` słownik, który mapuje wszystkie nagłówki, które są zwracane przez podstawowego interfejsu API protokołu HTTP dla ostatniej operacji, które są wykonywane. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji w języku Python przy użyciu konta z interfejsem API SQL usługi Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Opłata za RU jest uwidaczniany przez niestandardowy [bazy danych polecenie](https://docs.mongodb.com/manual/reference/command/) o nazwie `getLastRequestStatistics`. Polecenie zwraca dokument, który zawiera nazwę Ostatnia operacja wykonana, jego opłata za żądanie wyrażana i jego czas trwania. Jeśli używasz interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, masz wiele opcji pobieranie opłat jednostek RU.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie można znaleźć opłata za żądanie wyrażana w witrynie Azure portal, tylko dla zapytania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) i przekazywać je z danymi lub wybierz istniejące konto, które zawiera już dane.

1. Przejdź do **Eksplorator danych** okienka, a następnie wybierz kolekcję, aby pracować nad.

1. Wybierz pozycję **Nowe zapytanie**.

1. Wprowadź prawidłową kwerendę, a następnie wybierz **wykonaj zapytanie**.

1. Wybierz **statystyki zapytań** do wyświetlenia rzeczywiste żądanie opłata żądania, które zostało wykonane.

![Zrzut ekranu przedstawiający bazy danych MongoDB zapytania opłata za żądanie wyrażana w witrynie Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Użyj sterownik MongoDB .NET

Kiedy używać [oficjalne sterownik MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/), mogą wykonywać polecenia przez wywołanie metody `RunCommand` metody w `IMongoDatabase` obiektu. Ta metoda wymaga implementacji `Command<>` abstrakcyjnej klasy:

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

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji internetowej platformy .NET przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Użyj sterownik MongoDB Java


Kiedy używać [oficjalne sterownik MongoDB Java](https://mongodb.github.io/mongo-java-driver/), mogą wykonywać polecenia przez wywołanie metody `runCommand` metody w `MongoDatabase` obiektu:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji internetowej przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB i zestawu SDK Java](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Użyj sterownika bazy danych MongoDB Node.js

Kiedy używać [oficjalne sterownik bazy danych MongoDB Node.js](https://mongodb.github.io/node-mongodb-native/), mogą wykonywać polecenia przez wywołanie metody `command` metody w `db` obiektu:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Migrowanie istniejącej bazy danych MongoDB Node.js aplikacji sieci web do usługi Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Podczas wykonywania operacji dotyczących interfejsu API rozwiązania Cassandra usługi Azure Cosmos DB opłaty RU jest zwracany w ładunku przychodzących jako pole o nazwie `RequestCharge`. Istnieje wiele opcji pobierania opłaty jednostek RU.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Zastosowania [zestawu SDK platformy .NET](https://www.nuget.org/packages/CassandraCSharpDriver/), możesz pobrać przychodzące obciążenie w ramach `Info` właściwość `RowSet` obiektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji bazy danych Cassandra przy użyciu zestawu SDK platformy .NET i usługi Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Podczas korzystania [zestawu Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), możesz pobrać ładunku przychodzących przez wywołanie metody `getExecutionInfo()` metody `ResultSet` obiektu:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji bazy danych Cassandra przy użyciu zestawu SDK języka Java i usługi Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

Korzystając z interfejsu API języka Gremlin, masz wiele opcji do znajdowania zużycia jednostek RU dla operacji na kontenerze usługi Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Użyj sterowników i zestawu SDK

Zwrócone przez interfejs API języka Gremlin nagłówki są mapowane na stan niestandardowe atrybuty, które obecnie są udostępniane przez Gremlin .NET i zestawu SDK języka Java. Opłata za żądanie wyrażana jest dostępna w ramach `x-ms-request-charge` klucza.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Kiedy używać [Gremlin.NET zestawu SDK](https://www.nuget.org/packages/Gremlin.Net/), stan atrybuty są dostępne w obszarze `StatusAttributes` właściwość `ResultSet<>` obiektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji .NET Framework lub Core przy użyciu konta usługi Azure Cosmos DB — interfejs API Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z języka Java SDK

Kiedy używać [zestawu SDK Java Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), można pobrać atrybutów stanu przez wywołanie metody `statusAttributes()` metody `ResultSet` obiektu:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie bazy danych programu graph w usłudze Azure Cosmos DB przy użyciu zestawu SDK Java](create-graph-java.md).

## <a name="table-api"></a>Interfejs API tabel

Obecnie tylko zestaw SDK, który zwraca RU opłata za operacje tabeli jest [zestaw .NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). `TableResult` Obiektu ujawnia `RequestCharge` właściwość, która jest wypełniana przez zestaw SDK, korzystając z interfejsu API tabeli usługi Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Aby uzyskać więcej informacji, zobacz temat [Szybki start: Tworzenie aplikacji interfejsu API tabel przy użyciu zestawu SDK platformy .NET i usługi Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o optymalizacji sieci zużycia jednostek RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizuj koszt aprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizuj koszt zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Skaluj globalnie aprowizowana przepływność](scaling-throughput.md)
* [Aprowizowanie przepływności na kontenerach i baz danych](set-throughput.md)
* [Aprowizowanie przepływności dla kontenera](how-to-provision-container-throughput.md)
