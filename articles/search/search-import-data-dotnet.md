---
title: Przekazywanie danych (usługa Azure Search na platformie .NET) | Microsoft Docs
description: Dowiedz się, jak przekazywać dane do indeksu w usłudze Azure Search przy użyciu zestawu .NET SDK.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/13/2017
ms.openlocfilehash: fb8fb153c7393af0e084cb96ee2f5e82fba5b220
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798057"
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Przekazywanie danych do usługi Azure Search przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Omówienie](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

W tym artykule opisano, jak używać [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk) w celu importowania danych do indeksu usługi Azure Search.

Przed rozpoczęciem pracy z tym przewodnikiem powinien zostać [utworzony indeks usługi Azure Search](search-what-is-an-index.md). W tym artykule przyjęto również, że już wcześniej utworzono obiekt `SearchServiceClient`, jak pokazano w artykule [Create an Azure Search index using the .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient) (Tworzenie indeksu usługi Azure Search przy użyciu zestawu .NET SDK).

> [!NOTE]
> Cały przykładowy kod przedstawiony w tym artykule został napisany w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](http://aka.ms/search-dotnet-howto). Można również poczytać o [zestawie SDK platformy .NET dla usługi Azure Search](search-howto-dotnet-sdk.md), aby uzyskać bardziej szczegółowe omówienie przykładowego kodu.

Aby wypchnąć dokumenty do indeksu przy użyciu zestawu .NET SDK, konieczne jest wykonanie następujących czynności:

1. Utworzenie obiektu `SearchIndexClient`, aby połączyć się z indeksem wyszukiwania.
2. Utworzenie obiektu `IndexBatch` zawierającego dokumenty przeznaczone do dodania, modyfikacji lub usunięcia.
3. Wywołanie metody `Documents.Index` klasy `SearchIndexClient`, aby wysłać obiekt `IndexBatch` do indeksu wyszukiwania.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Tworzenie wystąpienia klasy SearchIndexClient
Aby zaimportować dane do indeksu, używając zestawu .NET SDK usługi Azure Search, konieczne jest utworzenie wystąpienia klasy `SearchIndexClient`. Takie wystąpienie można utworzyć samodzielnie, ale, jeśli masz już wystąpienie klasy `SearchServiceClient`, łatwiej jest wywołać jej metodę `Indexes.GetClient`. W poniższym przykładzie przedstawiono, jak można uzyskać klasę `SearchIndexClient` dla indeksu o nazwie „hotels” z klasy `SearchServiceClient` o nazwie `serviceClient`:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> W typowej aplikacji wyszukującej wypełnianie indeksu i zarządzanie nim jest obsługiwane przez inny składnik niż zapytania wyszukiwania. `Indexes.GetClient` jest wygodną metodą wypełniania indeksu, ponieważ nie wymaga dostarczenia kolejnej klasy `SearchCredentials`. Dzieje się tak dzięki przekazaniu klucza administratora, który został użyty w celu utworzenia klasy `SearchServiceClient` do nowej klasy `SearchIndexClient`. Jednak w tej części aplikacji, która wykonuje zapytania, zaleca się utworzenie klasy `SearchIndexClient` bezpośrednio, dzięki czemu można przekazać klucz zapytania zamiast klucza administratora. Jest to zgodne z [zasadą najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) i pomoże zapewnić większe bezpieczeństwo aplikacji. Więcej informacji o kluczach administratora i kluczach zapytań można znaleźć w [dokumentacji interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` ma właściwość `Documents`. Ta właściwość zapewnia wszystkie metody, które są potrzebne, aby dodawać, modyfikować i usuwać dokumenty w indeksie oraz wykonywać zapytania o nie.

## <a name="decide-which-indexing-action-to-use"></a>Wybieranie akcji indeksowania do użycia
Aby zaimportować dane przy użyciu zestawu .NET SDK, należy spakować dane do obiektu `IndexBatch`. Obiekt `IndexBatch` hermetyzuje kolekcję obiektów `IndexAction`, z których każdy zawiera dokument i właściwość, które informują usługę Azure Search, jaką akcję wykonać na tym dokumencie (przekazać, scalić, usunąć itp.). W zależności od tego, którą z poniższych akcji wybierzesz, tylko określone pola muszą być uwzględnione w danym dokumencie:

| Akcja | Opis | Wymagane pola dla każdego dokumentu | Uwagi |
| --- | --- | --- | --- |
| `Upload` |Akcja `Upload` jest podobna do akcji „upsert”, co oznacza, że dokument zostanie wstawiony, jeśli jest nowy, albo zaktualizowany/zastąpiony, jeśli już istnieje. |pole klucza oraz inne pola, które chcesz zdefiniować |Podczas aktualizowania/zastępowania istniejącego dokumentu każde pole, które nie jest określone w żądaniu, zostanie ustawione na wartość `null`. Dzieje się tak nawet wtedy, gdy pole było wcześniej ustawione na wartość inną niż null. |
| `Merge` |Aktualizuje istniejący dokument o określone pola. Jeśli dokument nie istnieje w indeksie, scalanie zakończy się niepowodzeniem. |pole klucza oraz inne pola, które chcesz zdefiniować |Wszystkie pola, które określisz w żądaniu scalania, zastąpią istniejące pola w dokumencie. Obejmuje to również pola typu `DataType.Collection(DataType.String)`. Jeśli na przykład dokument zawiera pole `tags` o wartości `["budget"]` i wykonywane jest scalanie z wartością `["economy", "pool"]` dla pola `tags`, końcowa wartość pola `tags` będzie równa `["economy", "pool"]`. Nie będzie to `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Ta akcja działa jak akcja `Merge`, jeśli dokument o danym kluczu już istnieje w indeksie. Jeśli dokument nie istnieje, działa jak akcja `Upload` dla nowego dokumentu. |pole klucza oraz inne pola, które chcesz zdefiniować |- |
| `Delete` |Usuwa określony dokument z indeksu. |tylko pole klucza |Wszystkie pola, które określisz oprócz pola klucza, zostaną zignorowane. Jeśli chcesz usunąć pojedyncze pole z dokumentu, zamiast tej akcji użyj akcji `Merge` i po prostu jawnie ustaw dla pola wartość null. |

Akcję do użycia możesz określić za pomocą różnych metod statycznych klas `IndexBatch` i `IndexAction`. Pokazano to w następnej sekcji.

## <a name="construct-your-indexbatch"></a>Konstruowanie obiektu IndexBatch
Teraz, kiedy już wiesz, które akcje wykonać na dokumentach, możesz przystąpić do konstruowania obiektu `IndexBatch`. Poniższy przykład przedstawia sposób tworzenia partii z kilkoma różnymi akcjami. Zauważ, że przedstawiony przykład używa niestandardowej klasy o nazwie `Hotel`, która jest mapowana na dokument w indeksie „hotels”.

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

## <a name="import-data-to-the-index"></a>Importowanie danych do indeksu
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

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Jak zestaw .NET SDK obsługuje dokumenty
Możesz się zastanawiać, jak zestaw .NET SDK usługi Azure Search jest w stanie przekazać wystąpienia klasy zdefiniowanej przez użytkownika, np. `Hotel`, do indeksu. Aby odpowiedzieć na to pytanie, przyjrzyjmy się klasie `Hotel`, która jest mapowana na schemat indeksu zdefiniowany w artykule [Create an Azure Search index using the .NET SDK](search-create-index-dotnet.md#DefineIndex) (Tworzenie indeksu usługi Azure Search przy użyciu zestawu .NET SDK):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Przede wszystkim należy zauważyć, że każda właściwość publiczna klasy `Hotel` odpowiada polu w definicji indeksu, ale z jedną istotną różnicą: nazwa każdego pola rozpoczyna się małą literą (camelCase), podczas gdy nazwa każdej właściwości publicznej klasy `Hotel` rozpoczyna się wielką literą (PascalCase). Jest to częsty przypadek w aplikacjach platformy .NET, które tworzą powiązania danych, gdzie schemat docelowy jest poza kontrolą dewelopera aplikacji. Zamiast naruszać zasady nazewnictwa platformy .NET przez używanie notacji camelCase dla nazw właściwości, za pomocą atrybutu `[SerializePropertyNamesAsCamelCase]` można określić, że zestaw SDK ma mapować nazwy właściwości na notację camelCase automatycznie.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search korzysta z biblioteki [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) w celu serializacji i deserializacji niestandardowych obiektów modelu do i z formatu JSON. W razie potrzeby możesz dostosować serializację. Więcej szczegółowych informacji znajdziesz w sekcji [Custom Serialization with JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet) (Serializacja niestandardowa przy użyciu programu JSON.NET). Jako przykład może posłużyć użycie atrybutu `[JsonProperty]` dla właściwości `DescriptionFr` w powyższym kodzie przykładowym.
> 
> 

Drugą ważną kwestią dotyczącą klasy `Hotel` są typy danych właściwości publicznych. Typy .NET tych właściwości są mapowane na odpowiadające im typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `DataType.String`. Podobne mapowania typów występują między typami `bool?` i `DataType.Boolean`, `DateTimeOffset?` i `DataType.DateTimeOffset` itd. Dokładne zasady mapowania typów znajdują się w dokumentacji metody `Documents.Get` w [dokumentacji zestawu .NET SDK usługi Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Ta możliwość używania własnych klas jako dokumentów działa w obu kierunkach. Możesz również pobrać wyniki wyszukiwania i za pomocą zestawu SDK dokonać ich automatycznej deserializacji do wybranego przez Ciebie typu, jak pokazano w [kolejnym artykule](search-query-dotnet.md).

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search obsługuje również dynamiczne typowanie dokumentów za pomocą klasy `Document`, która stanowi mapowanie klucz/wartość nazw pól na wartości pól. Jest to przydatne, jeśli nie znasz schematu indeksu w czasie projektowania lub jeśli powiązanie z określonymi klasami modelu jest niedogodne. Wszystkie metody w zestawie SDK, które obsługują dokumenty, mają przeciążenia działające z klasą `Document`, a także przeciążenia o silnych typach przyjmujące parametr typu ogólnego. Tylko te ostatnie są używane w przykładowym kodzie w tym artykule.
> 
> 

**Dlaczego należy używać typów danych dopuszczających wartość null**

Jeśli projektujesz własne klasy modeli na potrzeby mapowania na indeks usługi Azure Search, zalecamy deklarowanie właściwości typów wartości, takich jak `bool` i `int`, aby dopuszczały możliwość użycia wartości null (na przykład `bool?` zamiast `bool`). W przypadku użycia właściwości niedopuszczającej wartości null musisz **zagwarantować**, że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Ani zestaw SDK, ani usługa Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `DataType.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu najlepszym i zalecanym rozwiązaniem jest używanie w klasach modeli typów dopuszczających wartość null.

## <a name="next-steps"></a>Następne kroki
Po wypełnieniu indeksu usługi Azure Search możesz rozpocząć wykonywanie zapytań w celu wyszukania dokumentów. Aby uzyskać szczegóły, zobacz [Query Your Azure Search Index](search-query-overview.md) (Tworzenie zapytań względem indeksu usługi Azure Search).

