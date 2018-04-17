---
title: Praca z daty w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu pracy z daty w usłudze Azure DB rozwiązania Cosmos.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: sngun
ms.openlocfilehash: a1f8b38725eb188b609fbdc8efa09ed5aaaf6ae5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Praca z daty w Azure rozwiązania Cosmos bazy danych
Azure DB rozwiązania Cosmos zapewnia elastyczność schematu i rozbudowane indeksowanie za pomocą natywny [JSON](http://www.json.org) modelu danych. Wszystkie zasoby bazy danych rozwiązania Cosmos platformy Azure, w tym baz danych, kolekcji, dokumentów i procedury składowane są modelowane i przechowywane jako dokumenty JSON. Jako wymaganiem jest portable JSON (i bazy danych Azure rozwiązania Cosmos) obsługuje tylko niewielki zestaw typów podstawowych: ciąg, Number, Boolean, tablicy, obiektu i wartości Null. Jednak JSON jest elastyczny i umożliwia deweloperom i platformy, do reprezentowania bardziej złożonych typów przy użyciu tych elementów podstawowych i tworzenia ich jako obiekty i tablice. 

Oprócz podstawowych typów wiele aplikacji muszą [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) typu do reprezentowania daty i czasu. W tym artykule opisano sposób deweloperzy można przechowywać, pobieranie i zapytania dat w usłudze Azure DB rozwiązania Cosmos przy użyciu zestawu .NET SDK.

## <a name="storing-datetimes"></a>Przechowywanie dat i godzin
Domyślnie [zestawu SDK usługi Azure rozwiązania Cosmos DB](sql-api-sdk-dotnet.md) serializuje wartości daty/godziny jako [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) ciągów. Większość aplikacji można użyć domyślnej reprezentacji ciągu dla typu DateTime z następujących powodów:

* Można porównać ciągów i względne uporządkowanie wartości daty/godziny jest zachowywana, gdy są one przekształcone na ciągi. 
* Takie podejście nie wymaga żadnych niestandardowy kod lub atrybuty konwersji do formatu JSON.
* Dane przechowywane w formacie JSON są człowieka do odczytu.
* Tej metody można korzystać z Azure rozwiązania Cosmos DB indeks wydajność szybkiego zapytań.

Na przykład poniższy fragment przechowuje `Order` obiektu zawierającego dwa właściwości daty i godziny — `ShipDate` i `OrderDate` jako dokument przy użyciu zestawu .NET SDK:

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

Ten dokument jest przechowywany w usłudze Azure DB rozwiązania Cosmos w następujący sposób:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternatywnie można przechowywać dat i godzin jako systemu Unix sygnatury czasowe, czyli jako liczbę reprezentującą liczbę sekund czas od 1 stycznia 1970. Sygnatura czasowa wewnętrzny Azure DB rozwiązania Cosmos (`_ts`) właściwość następuje tej metody. Można użyć [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) klasy do serializacji dat i godzin jako liczby. 

## <a name="indexing-datetimes-for-range-queries"></a>Indeksowanie zapytań zakres dat i godzin
Zakres zapytania są często używane z wartości daty/godziny. Na przykład jeśli należy znaleźć zamówienia utworzone od wczoraj lub znaleźć zamówienia wydane w ciągu ostatnich pięciu minut, należy wykonać kwerendy zakresu. Do wykonania tych zapytań wydajnie, należy skonfigurować kolekcję dla zakresu indeksowania na ciągach.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Dowiedz się więcej na temat sposobu konfigurowania zasad indeksowania w [zasady indeksowania bazy danych Azure rozwiązania Cosmos](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Wykonywanie zapytania dat i godzin w składniku LINQ
Zestaw SDK .NET SQL obsługuje automatycznie zapytywanie o dane przechowywane w bazie danych rozwiązania Cosmos Azure za pomocą LINQ. Na przykład poniższy fragment kodu przedstawia zapytania LINQ tego zamówienia filtry, które zostały wysłane w ciągu ostatnich trzech dni.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Dowiedz się więcej o język zapytań SQL Azure rozwiązania Cosmos DB i dostawcy LINQ w [zapytań DB rozwiązania Cosmos](sql-api-sql-query.md).

W tym artykule analizujemy przechowywania, indeksu i zapytania dat i godzin w usłudze Azure DB rozwiązania Cosmos.

## <a name="next-steps"></a>Następne kroki
* Pobierz i uruchom [przykłady w serwisie GitHub kodu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Dowiedz się więcej o [zapytania SQL](sql-api-sql-query.md)
* Dowiedz się więcej o [zasady indeksowania bazy danych Azure rozwiązania Cosmos](indexing-policies.md)
