---
title: Ładowanie danych do indeksu usługi Azure Search w C# (.NET SDK) — usługa Azure Search
description: Dowiedz się, jak przekazywać dane do indeksu umożliwiającego wyszukiwanie pełnotekstowe w usłudze Azure Search przy użyciu przykładowego kodu w języku C# i zestawu .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871050"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Szybki start: 2 - ładowanie danych do indeksu usługi Azure Search przy użyciuC#

W tym artykule pokazano, jak importować dane [indeksu usługi Azure Search](search-what-is-an-index.md) przy użyciu C# i [zestawu .NET SDK](https://aka.ms/search-sdk). Wypychanie dokumentów do indeksu odbywa się przez wykonanie następujących zadań:

> [!div class="checklist"]
> * Tworzenie [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) obiekt, aby nawiązać połączenie z indeksu wyszukiwania.
> * Tworzenie [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) obiektu zawierającego dokumenty, które mają zostać dodane, zmodyfikowane lub usunięte.
> * Wywołaj `Documents.Index` metody `SearchIndexClient` na przekazywanie dokumentów do indeksu.

## <a name="prerequisites"></a>Wymagania wstępne

[Tworzenie indeksu usługi Azure Search](search-create-index-dotnet.md) i `SearchServiceClient` obiektu, jak pokazano na ["Tworzenie klienta"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Tworzenie klienta
Aby zaimportować dane, potrzebujesz wystąpienie `SearchIndexClient` klasy. Istnieją różne podejścia do tworzenia tej klasy, w tym o korzystaniu z `SearchServiceClient` wystąpienie, które jest już utworzony. 

Tak jak pokazano w poniższym przykładzie, można użyć `SearchServiceClient` wystąpienie i wywołania jego `Indexes.GetClient` metody. Ten fragment kodu uzyskuje `SearchIndexClient` dla indeksu o nazwie "hotels" z `SearchServiceClient` o nazwie `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` ma właściwość `Documents`. Ta właściwość zapewnia wszystkie metody, które są potrzebne, aby dodawać, modyfikować i usuwać dokumenty w indeksie oraz wykonywać zapytania o nie.

> [!NOTE]
> W typowej aplikacji wyszukującej zapytań i indeksowania są obsługiwane osobno. Gdy `Indexes.GetClient` jest wygodne, ponieważ można ponownie użyć obiektów, takich jak `SearchCredentials`, bardziej niezawodne podejście związane z utworzeniem `SearchIndexClient` bezpośrednio, aby przekazać klucz zapytania zamiast klucza administratora. Jest to zgodne z [zasadę najmniejszych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) i pomaga zapewnić większe bezpieczeństwo aplikacji. Będzie konstruowania `SearchIndexClient` w następnym ćwiczeniu. Aby uzyskać więcej informacji na temat kluczy, zobacz [tworzenie i zarządzanie nimi klucze api KEY dla usługi Azure Search](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Construct IndexBatch

Aby zaimportować dane przy użyciu zestawu .NET SDK, spakować dane do `IndexBatch` obiektu. `IndexBatch` Hermetyzuje kolekcję `IndexAction` obiektów, z których każdy zawiera dokument i właściwości, które informują usługę Azure Search, jaką akcję wykonać na tym dokumencie (przekazywania, scalanie, usuwania i mergeOrUpload). Aby uzyskać więcej informacji na temat akcji indeksowania zobacz [indeksowania akcji: przekazanie, scalenie, mergeOrUpload, Usuń](search-what-is-data-import.md#indexing-actions).

Zakładając, że wiesz, które akcje wykonać na dokumentach, możesz przystąpić do konstruowania `IndexBatch`. Poniższy przykład przedstawia sposób tworzenia partii z kilkoma różnymi akcjami. W przykładzie użyto klasy niestandardowej o nazwie `Hotel` która jest mapowana na dokument w indeksie "hotels".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

W tym przypadku użyto akcji wyszukiwania `Upload`, `MergeOrUpload` i `Delete` określonych przez metody wywołane względem klasy `IndexAction`.

Załóżmy, że przedstawiony w przykładzie indeks „hotels” jest już wypełniony różnymi dokumentami. Zwróć uwagę na to, że w przypadku akcji `MergeOrUpload` nie było konieczne określenie wszystkich możliwych pól dokumentu. Klucz dokumentu (`HotelId`) został określony tylko w przypadku akcji `Delete`.

Zauważ również, że pojedyncze żądanie indeksowania może zawierać maksymalnie 1000 dokumentów.

> [!NOTE]
> W tym przykładzie stosujemy różne akcje do różnych dokumentów. Jeśli chcesz wykonać te same akcje dla wszystkich dokumentów w partii, zamiast wywoływać metodę `IndexBatch.New`, możesz użyć innej metody statycznej klasy `IndexBatch`. Na przykład możesz utworzyć partie przez wywołanie metody `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` lub `IndexBatch.Delete`. Te metody przyjmują kolekcję dokumentów (w tym przykładzie obiekty typu `Hotel`) zamiast obiektów `IndexAction`.
> 
> 

## <a name="call-documentsindex"></a>Wywołanie Documents.Index
Po zainicjowaniu obiektu `IndexBatch` możesz go wysłać do indeksu przez wywołanie metody `Documents.Index` względem obiektu `SearchIndexClient`. Poniższy przykład pokazuje, jak wywołać metodę `Index` oraz inne dodatkowe kroki, które należy wykonać:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Zwróć uwagę na blok `try`/`catch` otaczający wywołanie metody `Index`. Blok catch obsługuje ważny przypadek błędu indeksowania. Jeśli usługa Azure Search nie może zindeksować niektórych dokumentów w partii, metoda `Documents.Index` zgłasza wyjątek `IndexBatchException`. Może to nastąpić, jeśli indeksujesz dokumenty w czasie, gdy usługa jest mocno obciążona. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

Na końcu powyższego przykładu umieszczono dwusekundowe opóźnienie. Indeksowanie w usłudze Azure Search jest asynchroniczne, więc przykładowa aplikacja musi czekać przez krótki czas, aby upewnić się, że dokumenty można wyszukiwać. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

Aby uzyskać więcej informacji na temat przetwarzania dokumentu, zobacz ["Jak zestaw .NET SDK obsługuje dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Kolejne kroki
Po wypełnieniu indeksu usługi Azure Search, następnym krokiem jest wykonywanie zapytań w celu wyszukania dokumentów. 

> [!div class="nextstepaction"]
> [Tworzenie zapytań względem indeksu usługi Azure Search wC#](search-query-dotnet.md)
