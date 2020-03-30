---
title: Znajdź opłatę za jednostkę żądania (RU) w usłudze Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) za dowolną operację wykonaną z kontenera usługi Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585904"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Znajdowanie opłaty za jednostkę żądania w usłudze Azure Cosmos DB

W tym artykule przedstawiono różne sposoby znajdowania użycia [jednostki żądania](request-units.md) (RU) dla dowolnej operacji wykonywanej względem kontenera w usłudze Azure Cosmos DB. Obecnie można zmierzyć to zużycie tylko przy użyciu witryny Azure portal lub sprawdzając odpowiedź odesłana z usługi Azure Cosmos DB za pośrednictwem jednego z zestawów SDK.

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

Jeśli używasz interfejsu API SQL, masz wiele opcji znajdowania użycia ru dla operacji względem kontenera usługi Azure Cosmos.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie można znaleźć opłaty za żądanie w witrynie Azure portal tylko dla zapytania SQL.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) i podaj je z danymi lub wybierz istniejące konto usługi Azure Cosmos, które już zawiera dane.

1. Przejdź do okienka **Eksploratora danych,** a następnie wybierz kontener, nad który chcesz pracować.

1. Wybierz pozycję **Nowe zapytanie SQL**.

1. Wprowadź prawidłową kwerendę, a następnie wybierz pozycję **Wykonaj kwerendę**.

1. Wybierz **statystyki kwerend,** aby wyświetlić rzeczywistą opłatę za żądanie dla wykonanego żądania.

![Zrzut ekranu przedstawiający opłatę za żądanie kwerendy SQL w witrynie Azure portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET
### <a name="net-v2-sdk"></a>Plik SDK .Net V2

Obiekty, które są zwracane z [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) uwidaczniają `RequestCharge` właściwość:

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

Obiekty, które są zwracane z [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) uwidaczniają `RequestCharge` właściwość:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji sieci web platformy .NET przy użyciu konta interfejsu API SQL w usłudze Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z sdk Java

Obiekty, które są zwracane z `getRequestCharge()` [java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) uwidaczniają metodę:

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

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji Java przy użyciu konta interfejsu API SQL usługi Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Korzystanie z sdk node.js

Obiekty zwracane z [SDK node.js](https://www.npmjs.com/package/@azure/cosmos) udostępniają `headers` podobiekt, który mapuje wszystkie nagłówki zwracane przez podstawowy interfejs API HTTP. Opłata za zamówienie jest `x-ms-request-charge` dostępna pod kluczem:

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

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji Node.js przy użyciu konta interfejsu API usługi Azure Cosmos DB SQL](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Korzystanie z modułu SDK języka Python

Obiekt `CosmosClient` z [modułu SDK](https://pypi.org/project/azure-cosmos/) `last_response_headers` języka Python udostępnia słownik, który mapuje wszystkie nagłówki zwrócone przez podstawowy interfejs API HTTP dla ostatniej wykonanej operacji. Opłata za zamówienie jest `x-ms-request-charge` dostępna pod kluczem:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji języka Python przy użyciu konta interfejsu API sql usługi Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

Opłata RU jest narażona `getLastRequestStatistics`przez niestandardowe polecenie bazy [danych](https://docs.mongodb.com/manual/reference/command/) o nazwie . Polecenie zwraca dokument zawierający nazwę ostatniej wykonanej operacji, opłatę za żądanie i czas jej trwania. Jeśli używasz interfejsu API bazy danych usługi Azure Cosmos dla mongodb, masz wiele opcji pobierania opłaty RU.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Obecnie można znaleźć opłaty za żądanie w witrynie Azure portal tylko dla kwerendy.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) i podaj je z danymi lub wybierz istniejące konto, które już zawiera dane.

1. Przejdź do okienka **Eksploratora danych,** a następnie wybierz kontener, nad który chcesz pracować.

1. Wybierz pozycję **Nowe zapytanie**.

1. Wprowadź prawidłową kwerendę, a następnie wybierz pozycję **Wykonaj kwerendę**.

1. Wybierz **statystyki kwerend,** aby wyświetlić rzeczywistą opłatę za żądanie dla wykonanego żądania.

![Zrzut ekranu przedstawiający opłatę za żądanie kwerendy mongodb w witrynie Azure portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Używanie sterownika MongoDB .NET

Korzystając z [oficjalnego sterownika MongoDB .NET,](https://docs.mongodb.com/ecosystem/drivers/csharp/)można wykonywać `RunCommand` polecenia, `IMongoDatabase` wywołując metodę na obiekcie. Ta metoda wymaga implementacji klasy abstrakcyjnej: `Command<>`

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

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji sieci web .NET przy użyciu interfejsu API bazy danych Usługi Azure Cosmos dla usługi MongoDB.](create-mongodb-dotnet.md)

### <a name="use-the-mongodb-java-driver"></a>Używanie sterownika MongoDB Java


Korzystając z [oficjalnego sterownika Java MongoDB,](https://mongodb.github.io/mongo-java-driver/)można wykonywać `runCommand` polecenia, `MongoDatabase` wywołując metodę na obiekcie:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji sieci web przy użyciu interfejsu API usługi Azure Cosmos DB dla mongodb i java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Używanie sterownika MongoDB Node.js

Korzystając z [oficjalnego sterownika MongoDB Node.js,](https://mongodb.github.io/node-mongodb-native/)można wykonywać `command` polecenia, `db` wywołując metodę na obiekcie:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Migrowanie istniejącej aplikacji sieci Web MongoDB Node.js do usługi Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

Podczas wykonywania operacji względem interfejsu API Cassandra usługi Azure Cosmos DB, opłata RU `RequestCharge`jest zwracana w przychodzącym ładunku jako pole o nazwie . Istnieje wiele opcji pobierania opłaty za jednostkę żądania.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Korzystając z [sdk .NET,](https://www.nuget.org/packages/CassandraCSharpDriver/)można pobrać przychodzące ładunku `Info` pod `RowSet` właściwością obiektu:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji Cassandra przy użyciu pliku .NET SDK i usługi Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z sdk Java

Korzystając z [sdk Java,](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)można pobrać ładunek przychodzący, wywołując `getExecutionInfo()` metodę na `ResultSet` obiekcie:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji Cassandra przy użyciu zestawów Java SDK i usługi Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

Korzystając z interfejsu API Gremlin, masz wiele opcji znajdowania użycia ru dla operacji względem kontenera usługi Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Używanie sterowników i SDK

Nagłówki zwracane przez interfejs API Gremlin są mapowane do atrybutów stanu niestandardowego, które są obecnie na powierzchni przez Gremlin .NET i Java SDK. Opłata za zamówienie jest `x-ms-request-charge` dostępna pod kluczem.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

Podczas korzystania z [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/)atrybuty stanu `StatusAttributes` są dostępne `ResultSet<>` pod właściwością obiektu:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji .NET Framework lub Core przy użyciu konta interfejsu API usługi Azure Cosmos DB Gremlin](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Korzystanie z sdk Java

Korzystając z [gremlin java sdk](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), można pobrać atrybuty stanu, wywołując `statusAttributes()` metodę na `ResultSet` obiekcie:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie bazy danych wykresów w usłudze Azure Cosmos DB przy użyciu zestawu Java SDK](create-graph-java.md).

## <a name="table-api"></a>Interfejs API tabel

Obecnie jedynym pakietem SDK, który zwraca opłatę RU dla operacji tabeli, jest [standardowy sdk .NET.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) Obiekt `TableResult` udostępnia `RequestCharge` właściwość, która jest wypełniona przez zestaw SDK, gdy używasz go względem interfejsu API tabeli usługi Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie aplikacji interfejsu API tabel przy użyciu narzędzia .NET SDK i usługi Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o optymalizacji zużycia ru, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Przepływność obsługi kontenera](how-to-provision-container-throughput.md)
* [Monitorowanie i debugowanie za pomocą metryk w usłudze Azure Cosmos DB](use-metrics.md)
