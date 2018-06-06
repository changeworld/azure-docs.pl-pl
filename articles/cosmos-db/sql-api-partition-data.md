---
title: Partycjonowanie i skalowania w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat działa jak partycjonowania bazy danych rozwiązania Cosmos Azure, jak skonfigurować partycje i kluczy partycji i Wybieranie klucza prawego partycji aplikacji.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7e3c15ce201cee88d400b9d5fb9272b584293a2f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797436"
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Podział na partycje w usłudze Azure DB rozwiązania Cosmos przy użyciu interfejsu API SQL

[Bazy danych programu Microsoft Azure rozwiązania Cosmos](../cosmos-db/introduction.md) jest usługą bazy danych globalnych, rozproszone i wiele modeli pomaga osiągnąć szybkie, przewidywalną wydajność i skalę bezproblemowo wraz z aplikacji jako ich przyrostu. 

Ten artykuł zawiera omówienie sposobu pracy z partycjonowania DB rozwiązania Cosmos kontenerów przy użyciu interfejsu API SQL. Zobacz [partycjonowania i skalowanie w poziomie](../cosmos-db/partition-data.md) omówienie pojęć i najlepsze rozwiązania dotyczące partycjonowania z jakiegokolwiek interfejsu API Azure rozwiązania Cosmos bazy danych. 

Aby rozpocząć pracę z kodem, Pobierz projektu z [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Po przeczytaniu tego artykułu, będzie mógł odpowiedzieć na następujące pytania:   

* Jak działa partycjonowania w usłudze Azure DB rozwiązania Cosmos?
* Jak skonfigurować partycje w usłudze Azure DB rozwiązania Cosmos
* Co to są klucze partycji, i jak pobranie klucza partycji prawo do mojej aplikacji?

Aby rozpocząć pracę z kodem, Pobierz projektu z [Azure rozwiązania Cosmos DB wydajności testowania sterownik Sample](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Klucze partycji

W interfejsu API SQL należy określić definicję klucza partycji w formie ścieżce JSON. W poniższej tabeli przedstawiono przykłady definicji klucza partycji oraz odpowiadający każdej wartości. Klucz partycji jest określony jako ścieżkę, na przykład `/department` reprezentuje dział właściwości. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Klucz partycji</strong></p></td>
            <td valign="top"><p><strong>Opis</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Odpowiada wartości doc.department dokumentu w przypadku elementu.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ właściwości/Nazwa</p></td>
            <td valign="top"><p>Odpowiada wartości doc.properties.name dokumentu w przypadku elementu (właściwość zagnieżdżonych).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ Identyfikator</p></td>
            <td valign="top"><p>Odpowiada wartości doc.id (tę samą właściwość są identyfikator i partycji klucza).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ "dział name"</p></td>
            <td valign="top"><p>Odpowiada wartości doc ["Nazwa działu"], gdzie dokumentu jest element.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> Składnia dla klucza partycji jest podobna do specyfikacji ścieżki do indeksowania ścieżki zasad z klucza różnicą, że ścieżka odnosi się do właściwości zamiast wartości, tzn. Brak nie symbol wieloznaczny na końcu. Na przykład należy określić/dział /? do indeksowania wartości w polach działu, ale /department jako określić definicję klucza partycji. Klucz partycji jest niejawnie indeksowane i nie można wykluczyć z indeksowania, używając zastąpień zasad indeksowania.
> 
> 

Oto jak wybór klucza partycji ma wpływ na wydajność aplikacji.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Praca z zestawów SDK usługi Azure rozwiązania Cosmos bazy danych
Azure DB rozwiązania Cosmos dodano obsługę automatycznego partycjonowania z [interfejsu API REST wersji 2015-12-16](/rest/api/cosmos-db/). Aby można było utworzyć kontenery podzielonym na partycje, należy pobrać zestaw SDK w wersji 1.6.0 lub nowszej w jednym z obsługiwanych Platform SDK (.NET, Node.js, Java, Python, bazy danych MongoDB). 

### <a name="creating-containers"></a>Tworzenie kontenerów
Poniższy przykład przedstawia fragment .NET, aby utworzyć kontener do przechowywania danych telemetrycznych urządzenie 20 000 jednostek żądań na sekundę, przepływności. Zestaw SDK ustawia wartość OfferThroughput (który z kolei ustawia `x-ms-offer-throughput` nagłówek żądania w interfejsie API REST). W tym miejscu możesz zdefiniować `/deviceId` jako klucza partycji. Wybór klucza partycji są zapisywane wraz z resztą metadanych kontenera, takie jak nazwa i zasady indeksowania.

Dla tego przykładu pobrane `deviceId` ponieważ wiadomo, że () nie ma dużą liczbę urządzeń, zapisuje może być równomiernie rozłożone na partycje, dzięki czemu można skalować bazy danych w celu pozyskiwania bardzo dużych woluminów danych i (b) wiele żądań, takich jak pobieranie najnowsze odczytu dla urządzenia ograniczone do jednego deviceId i mogą być pobierane z jednej partycji.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Ta metoda powoduje interfejsu API REST wywołania w celu rozwiązania Cosmos bazy danych i usługi Obsługa będzie inicjowana liczba partycji na podstawie przepływności żądanej. W razie rozwoju potrzeb wydajność można zmienić przepływności kontenera lub grupy kontenerów. 

### <a name="reading-and-writing-items"></a>Odczytywanie i zapisywanie elementów
Teraz Dodajmy danych w bazie danych rozwiązania Cosmos. Oto przykładowe klasa zawierająca urządzenia odczytywania i wywołanie CreateDocumentAsync Wstaw nowe urządzenie odczytu do kontenera. Poniżej przedstawiono przykład blok kodu, który korzysta z interfejsu API SQL:

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Umożliwia odczytać elementu przez identyfikator i klucz partycji, zaktualizować go, a następnie jako ostatni krok, usuń go przez klucz partycji i identyfikator. Odczyty obejmują wartość PartitionKey (odpowiadający `x-ms-documentdb-partitionkey` nagłówek żądania w interfejsie API REST).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Wykonywanie zapytania kontenery podzielonym na partycje
Kwerendy danych w kontenerach podzielonym na partycje, DB rozwiązania Cosmos automatycznie rozsyła kwerendy do partycji odpowiadający wartości klucza partycji podana w filtrze (jeśli istnieją). Na przykład to zapytanie jest kierowane tylko do partycji zawierającej klucz partycji „XMS 0001”.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Poniższe zapytanie nie ma filtra klucza partycji (DeviceId) i jest rozsyłane do wszystkich partycji, w których jest wykonywane względem indeksu partycji. Należy określić EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` w interfejsie API REST) ma zestaw SDK, aby wykonać zapytania na partycje.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Obsługuje rozwiązania cosmos DB [funkcje agregujące](sql-api-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`,, i `AVG` over podzielona na partycje przy użyciu programu SQL z zestawów SDK 1.12.0 i powyżej uruchomienia kontenerów. Zapytania musi zawierać pojedynczy operatora agregacji i musi zawierać jedną wartość w projekcji.

### <a name="parallel-query-execution"></a>Równoległe wykonywanie zapytań
Zestawy SDK DB rozwiązania Cosmos 1.9.0 i powyżej opcje wykonywania zapytania równoległe pomocy technicznej, które umożliwiają wykonywanie zapytań o małych opóźnieniach dotyczących kolekcji partycjonowanych nawet wtedy, gdy konieczne jest touch dużą liczbę partycji. Na przykład poniższe zapytanie zostało skonfigurowane do uruchomienia równoległego w ramach partycji.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

* Przez ustawienie `MaxDegreeOfParallelism`, możliwość sterowania stopniem równoległości czyli maksymalną liczbę równoczesnych połączeń sieciowych do kontenera partycji. Jeśli ta właściwość jest ustawiona na wartość -1, stopień równoległości jest zarządzany przez zestaw SDK. Jeśli `MaxDegreeOfParallelism` nie jest określony lub ustawiona na 0, co jest to wartość domyślna, będzie jednego połączenia sieciowego z partycjami kontenera.
* Ustawiając element `MaxBufferedItemCount`, można osiągnąć kompromis między opóźnieniem zapytania i wykorzystaniem pamięci po stronie klienta. Jeśli ten parametr lub ustaw tą właściwość na wartość -1, liczba elementów buforowane podczas równoległego wykonywania zapytań jest zarządzana przez zestaw SDK.

Jeśli kolekcja będzie mieć taki sam stan, zapytanie równoległe zwróci wyniki w tej samej kolejności jak wykonanie szeregowe. Podczas wykonywania kwerendy między partycji zawierającej, sortowanie (ORDER BY i/lub góry), zestaw SDK usługi Azure rozwiązania Cosmos DB wystawia zapytania równolegle na partycje i scala częściowo sortowane wyniki w po stronie klienta, aby wygenerować globalnie uporządkowanych wyników.

### <a name="executing-stored-procedures"></a>Wykonywanie procedury składowane
Można również wykonywać transakcje atomic względem dokumentów mających taki sam identyfikator urządzenia, na przykład, jeśli jest utrzymanie agreguje lub najnowszy stan urządzeń w jeden element. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
W następnej sekcji przyjrzymy się jak można przenieść do kontenerów podzielonym na partycje z jedną partycją kontenerów.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule podać omówienie sposobu pracy z partycji bazy danych Azure rozwiązania Cosmos kontenerów przy użyciu interfejsu API SQL. Zobacz też [partycjonowania i skalowanie w poziomie](../cosmos-db/partition-data.md) omówienie pojęć i najlepsze rozwiązania dotyczące partycjonowania z jakiegokolwiek interfejsu API Azure rozwiązania Cosmos bazy danych. 

* Należy przeprowadzić testowanie z bazy danych Azure rozwiązania Cosmos wydajności i skalowania. Zobacz [wydajności i skalowania testowania z bazy danych Azure rozwiązania Cosmos](performance-testing.md) przykładowe.
* Rozpoczynanie pracy kodowanie dzięki funkcjom [zestawów SDK](sql-api-sdk-dotnet.md) lub [interfejsu API REST](/rest/api/cosmos-db/)
* Dowiedz się więcej o [udostępnionej przepływności w usłudze Azure DB rozwiązania Cosmos](request-units.md)

