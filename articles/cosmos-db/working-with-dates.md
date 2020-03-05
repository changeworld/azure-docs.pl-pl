---
title: Praca z datami w usłudze Azure Cosmos DB
description: Dowiedz się, jak przechowywać, indeksować i wykonywać zapytania dotyczące obiektów czasu danych w Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273185"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Praca z datami w usłudze Azure Cosmos DB

Azure Cosmos DB zapewnia elastyczność schematu i rozbudowane indeksowanie za pośrednictwem natywnego modelu danych [JSON](https://www.json.org) . Wszystkie zasoby usługi Azure Cosmos DB, w tym baz danych, kontenerów, dokumenty i procedury składowane są modelowane i przechowywane jako dokumenty JSON. Jako wymaganie jest przenośny, JSON (i usługi Azure Cosmos DB) obsługuje tylko niewielki zestaw typów podstawowych: ciąg, liczba, atrybut typu wartość logiczna, Array, obiekt i o wartości Null. Jednak JSON jest elastyczny i umożliwia deweloperom i struktur, do reprezentowania bardziej złożonych typów, przy użyciu tych wartości pierwotnych i tworzenie ich jako obiekty i tablice.

Oprócz typów podstawowych wiele aplikacji wymaga typu DateTime do reprezentowania dat i sygnatur czasowych. W tym artykule opisano, jak deweloperzy mogą przechowywanie, pobieranie i zapytania dat w usłudze Azure Cosmos DB przy użyciu zestawu .NET SDK.

## <a name="storing-datetimes"></a>Przechowywanie Data/Godzina

Azure Cosmos DB obsługuje typy JSON, takie jak-String, Number, Boolean, null, Array i Object. Nie obsługuje bezpośrednio typu DateTime. Obecnie Azure Cosmos DB nie obsługuje lokalizacji dat. Dlatego należy przechowywać wartości DateTimes jako ciągi. Zalecany format ciągów DateTime w Azure Cosmos DB jest `YYYY-MM-DDThh:mm:ss.sssZ`, który jest zgodny ze standardem ISO 8601 UTC. Zaleca się przechowywanie wszystkich dat w Azure Cosmos DB jako czas UTC. Konwersja ciągów daty na ten format umożliwi lexicographically dat sortowania. Jeśli są przechowywane daty inne niż UTC, logika musi być obsługiwana po stronie klienta. Aby skonwertować lokalną datę i godzinę do UTC, przesunięcie musi być znane/przechowywane jako właściwość w formacie JSON, a klient może użyć przesunięcia do obliczania wartości DateTime czasu UTC.

Większość aplikacji można użyć domyślnego ciągu reprezentującego daty/godziny z następujących powodów:

* Można porównać ciągi i względne uporządkowanie wartości daty/godziny jest zachowywana, gdy są one przekształcane na ciągi.
* To podejście nie wymaga niestandardowego kodu ani atrybutów do konwersji do formatu JSON.
* Daty zapisane w formacie JSON są ludzi do odczytu.
* To podejście korzystać z zalet indeksu usługi Azure Cosmos DB za wydajność szybkiego przetwarzania zapytań.

Na przykład poniższy fragment kodu przechowuje `Order` obiektu zawierającego dwie właściwości DateTime-`ShipDate` i `OrderDate` jako dokument przy użyciu zestawu .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

W tym dokumencie są przechowywane w usłudze Azure Cosmos DB:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Alternatywnie można przechowywać Data/Godzina, jak sygnatury czasowe systemu Unix, oznacza to, jako liczbę reprezentującą liczbę sekund upłynęło od 1 stycznia 1970 roku. Azure Cosmos DB wewnętrzna właściwość sygnatury czasowej (`_ts`) jest zgodna z tym podejściem. Klasy [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) można użyć do serializacji datetimes jako liczby.

## <a name="querying-datetimes-in-linq"></a>Data/Godzina w składniku LINQ do zapytań

Zestaw SDK platformy .NET SQL automatycznie obsługuje wykonywanie zapytań o dane przechowywane w usłudze Azure Cosmos DB za pomocą LINQ. Na przykład poniższy fragment kodu przedstawia zapytanie LINQ, które filtruje zamówienia, które zostały wysłane w ciągu ostatnich trzech dni:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Przetłumaczone na następującą instrukcję SQL i wykonywane na Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Możesz dowiedzieć się więcej na temat języka zapytań SQL Azure Cosmos DB i dostawcy LINQ podczas [wykonywania zapytań Cosmos DB w LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indeksowanie zapytań o zakres dat i godzin

Zapytania są typowe dla wartości typu DateTime. Aby efektywnie wykonywać te zapytania, musisz mieć zdefiniowany indeks we wszystkich właściwościach filtru zapytania.

Więcej informacji na temat konfigurowania zasad indeksowania można znaleźć w [Azure Cosmos DB zasad indeksowania](index-policy.md). 

## <a name="next-steps"></a>Następne kroki

* Pobierz i uruchom [przykłady kodu w witrynie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Dowiedz się więcej o [zapytaniach SQL](sql-query-getting-started.md)
* Dowiedz się więcej na temat [Azure Cosmos DB zasad indeksowania](index-policy.md)
