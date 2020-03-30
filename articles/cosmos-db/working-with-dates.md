---
title: Praca z datami w usłudze Azure Cosmos DB
description: Dowiedz się, jak przechowywać, indeksować i wysyłać zapytania do obiektów DataTime w usłudze Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273185"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Praca z datami w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia elastyczność schematu i zaawansowane indeksowanie za pośrednictwem natywnego modelu danych [JSON.](https://www.json.org) Wszystkie zasoby usługi Azure Cosmos DB, w tym bazy danych, kontenery, dokumenty i procedury przechowywane są modelowane i przechowywane jako dokumenty JSON. Jako wymóg przenośny, JSON (i Azure Cosmos DB) obsługuje tylko niewielki zestaw typów podstawowych: Ciąg, Liczba, Boolean, Tablica, Obiekt i Null. Jednak JSON jest elastyczny i umożliwiają deweloperom i frameworkom reprezentowanie bardziej złożonych typów przy użyciu tych elementów pierwotnych i komponowanie ich jako obiektów lub tablic.

Oprócz podstawowych typów wiele aplikacji potrzebuje typu DateTime do reprezentowania dat i sygnatur czasowych. W tym artykule opisano, jak deweloperzy mogą przechowywać, pobierać i wysyłać zapytania daty w usłudze Azure Cosmos DB przy użyciu pliku .NET SDK.

## <a name="storing-datetimes"></a>Przechowywanie dat

Usługa Azure Cosmos DB obsługuje typy JSON, takie jak - ciąg, liczba, wartość logiczna, null, tablica, obiekt. Nie obsługuje bezpośrednio typu DateTime. Obecnie usługa Azure Cosmos DB nie obsługuje lokalizacji dat. Tak, trzeba przechowywać DateTimes jako ciągi. Zalecany format ciągów DateTime w usłudze `YYYY-MM-DDThh:mm:ss.sssZ` Azure Cosmos DB jest zgodny ze standardem ISO 8601 UTC. Zaleca się przechowywanie wszystkich dat w usłudze Azure Cosmos DB jako UTC. Konwersja ciągów daty do tego formatu pozwoli na sortowanie dat leksykograficznie. Jeśli są przechowywane daty inne niż UTC, logika musi być obsługiwana po stronie klienta. Aby przekonwertować lokalny DateTime do czasu UTC, przesunięcie musi być znane/przechowywane jako właściwość w JSON i klient może użyć przesunięcia do obliczenia wartości DATATime UTC.

Większość aplikacji może używać domyślnej reprezentacji ciągu datetime z następujących powodów:

* Ciągi mogą być porównywane, a względna kolejność wartości DateTime jest zachowywana, gdy są one przekształcane w ciągi.
* Takie podejście nie wymaga żadnego kodu niestandardowego lub atrybutów dla konwersji JSON.
* Daty przechowywane w JSON są czytelne dla człowieka.
* Takie podejście może korzystać z indeksu usługi Azure Cosmos DB dla szybkiej wydajności zapytań.

Na przykład następujący fragment kodu przechowuje obiekt zawierający `Order` dwie `ShipDate` właściwości `OrderDate` DateTime — i jako dokument przy użyciu pliku .NET SDK:

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

Ten dokument jest przechowywany w usłudze Azure Cosmos DB w następujący sposób:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Alternatywnie można przechowywać DateTimes jako sygnatury czasowe Unix, czyli jako liczbę reprezentującą liczbę sekund, które upłynęło od 1 stycznia 1970 roku. Właściwość wewnętrznej sygnatury czasowej usługi Azure Cosmos DB (`_ts`) jest zgodna z tym podejściem. Klasy [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) można użyć do serializacji Datów jako liczb.

## <a name="querying-datetimes-in-linq"></a>Wykonywanie zapytań o datowanie w linq

Zestaw SDK sql.net automatycznie obsługuje wykonywanie zapytań o dane przechowywane w usłudze Azure Cosmos DB za pośrednictwem linq. Na przykład poniższy fragment kodu pokazuje kwerendę LINQ, która filtruje zamówienia, które zostały wysłane w ciągu ostatnich trzech dni:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Przetłumaczone na następującą instrukcję SQL i wykonane w usłudze Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Więcej informacji na temat języka zapytań SQL usługi Azure Cosmos DB i dostawcy LINQ można dowiedzieć się więcej o języku aplikacji Azure [Cosmos DB w witrynie LINQ.](sql-query-linq-to-sql.md)

## <a name="indexing-datetimes-for-range-queries"></a>Indeksowanie Datduszu dla zapytań zakresowych

Zapytania są wspólne z wartościami DateTime. Aby skutecznie wykonywać te kwerendy, musisz mieć indeks zdefiniowany dla wszystkich właściwości w filtrze kwerendy.

Więcej informacji na temat konfigurowania zasad indeksowania można uzyskać w witrynie [Azure Cosmos DB Indexing Policies](index-policy.md). 

## <a name="next-steps"></a>Następne kroki

* Pobieranie i [uruchamianie przykładów kodu w usłudze GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Dowiedz się więcej o [kwerendach SQL](sql-query-getting-started.md)
* Dowiedz się więcej o [zasadach indeksowania usługi Azure Cosmos DB](index-policy.md)
