---
title: Praca z datami w usłudze Azure Cosmos DB
description: Dowiedz się, jak przechowywać, indeksować i wykonywać zapytania dotyczące obiektów czasu danych w Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 162b1a4ad089e75f4ad953a339b9b4c15e245a70
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869685"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Praca z datami w usłudze Azure Cosmos DB
Usługa Azure Cosmos DB zapewnia elastyczność schematu i rozbudowane indeksowanie za pomocą macierzystej [JSON](https://www.json.org) modelu danych. Wszystkie zasoby usługi Azure Cosmos DB, w tym baz danych, kontenerów, dokumenty i procedury składowane są modelowane i przechowywane jako dokumenty JSON. Jako wymaganie jest przenośny, JSON (i usługi Azure Cosmos DB) obsługuje tylko niewielki zestaw typów podstawowych: ciąg, liczba, atrybut typu wartość logiczna, Array, obiekt i o wartości Null. Jednak JSON jest elastyczny i umożliwia deweloperom i struktur, do reprezentowania bardziej złożonych typów, przy użyciu tych wartości pierwotnych i tworzenie ich jako obiekty i tablice. 

Oprócz typów podstawowych wiele aplikacji wymaga typu DateTime do reprezentowania dat i sygnatur czasowych. W tym artykule opisano, jak deweloperzy mogą przechowywanie, pobieranie i zapytania dat w usłudze Azure Cosmos DB przy użyciu zestawu .NET SDK.

## <a name="storing-datetimes"></a>Przechowywanie Data/Godzina

Azure Cosmos DB obsługuje typy JSON, takie jak-String, Number, Boolean, null, Array i Object. Nie obsługuje bezpośrednio typu DateTime. Obecnie Azure Cosmos DB nie obsługuje lokalizacji dat. Dlatego należy przechowywać wartości DateTimes jako ciągi. Zalecany format ciągów DateTime w Azure Cosmos DB jest `YYYY-MM-DDThh:mm:ss.sssZ`, który jest zgodny ze standardem ISO 8601 UTC. Zaleca się przechowywanie wszystkich dat w Azure Cosmos DB jako czas UTC. Konwersja ciągów daty na ten format umożliwi lexicographically dat sortowania. Jeśli są przechowywane daty inne niż UTC, logika musi być obsługiwana po stronie klienta. Aby skonwertować lokalną datę i godzinę do UTC, przesunięcie musi być znane/przechowywane jako właściwość w formacie JSON, a klient może użyć przesunięcia do obliczania wartości DateTime czasu UTC.

Większość aplikacji można użyć domyślnego ciągu reprezentującego daty/godziny z następujących powodów:

* Można porównać ciągi i względne uporządkowanie wartości daty/godziny jest zachowywana, gdy są one przekształcane na ciągi. 
* To podejście nie wymaga niestandardowego kodu ani atrybutów do konwersji do formatu JSON.
* Daty zapisane w formacie JSON są ludzi do odczytu.
* To podejście korzystać z zalet indeksu usługi Azure Cosmos DB za wydajność szybkiego przetwarzania zapytań.

Na przykład poniższy fragment kodu przechowuje `Order` obiekt zawierający dwie właściwości daty/godziny — `ShipDate` i `OrderDate` jako dokument przy użyciu zestawu .NET SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

W tym dokumencie są przechowywane w usłudze Azure Cosmos DB:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternatywnie można przechowywać Data/Godzina, jak sygnatury czasowe systemu Unix, oznacza to, jako liczbę reprezentującą liczbę sekund upłynęło od 1 stycznia 1970 roku. Sygnatura czasowa wewnętrznych danych usługi Azure Cosmos DB (`_ts`) to podejście jest zgodna właściwości. Możesz użyć [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) klasy do serializacji dat i godzin jako liczby. 

## <a name="indexing-datetimes-for-range-queries"></a>Indeksowanie zapytań o zakres dat i godzin
Zapytania zakresowe są powszechne z wartości daty/godziny. Na przykład jeśli potrzebujesz znaleźć wszystkie zamówienia utworzone od wczoraj, lub znaleźć wszystkich zamówień wysłanych w ciągu ostatnich pięciu minut, należy wykonać zapytania. Wydajne wykonywanie tych zapytań, należy skonfigurować kolekcję w celu zakresu indeksowania dla ciągów.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Dowiedz się więcej na temat sposobu konfigurowania zasad indeksowania w [zasad indeksowania usługi Azure Cosmos DB](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Data/Godzina w składniku LINQ do zapytań
Zestaw SDK platformy .NET SQL automatycznie obsługuje wykonywanie zapytań o dane przechowywane w usłudze Azure Cosmos DB za pomocą LINQ. Na przykład poniższy fragment kodu pokazuje zapytanie LINQ tego zamówienia filtry, które zostały wysłane w ciągu ostatnich trzech dni.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Dowiedz się więcej na temat języka zapytania SQL usługi Azure Cosmos DB i do dostawcy LINQ [podczas badania usługi Cosmos DB](how-to-sql-query.md).

W tym artykule zobaczyliśmy, jak przechowywać, indeksu i wykonywać zapytania Data/Godzina w usłudze Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki
* Pobierz i uruchom [przykłady kodu w serwisie GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Dowiedz się więcej o [zapytania SQL](how-to-sql-query.md)
* Dowiedz się więcej o [zasad indeksowania usługi Azure Cosmos DB](index-policy.md)
