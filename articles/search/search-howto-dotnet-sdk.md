---
title: Jak używać Azure Search z aplikacji .NET Azure Search
description: Dowiedz się, jak używać Azure Search w aplikacji .NET C# przy użyciu zestawu SDK platformy .NET. Zadania oparte na kodzie obejmują łączenie się z usługą, indeksowanie zawartości i wykonywanie zapytań względem indeksu.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 42d3a4a0840e7241666f66a09e7e6b11342cbfbc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182272"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Jak używać Azure Search z aplikacji .NET

Ten artykuł zawiera wskazówki ułatwiające rozpoczęcie pracy z [zestawem SDK Azure Search platformy .NET](https://aka.ms/search-sdk). Korzystając z zestawu SDK platformy .NET, można zaimplementować bogate środowisko wyszukiwania w aplikacji przy użyciu Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Co znajduje się w zestawie Azure Search SDK
Zestaw SDK składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksami, źródłami danych, indeksatorami i mapami synonimów, a także przekazywaniem i zarządzaniem dokumentami oraz wykonywanie zapytań, bez konieczności zajmowania się szczegółowymi informacjami dotyczącymi protokołu HTTP i JSON. Te biblioteki klienckie są dystrybuowane jako pakiety NuGet.

Głównym pakietem NuGet jest `Microsoft.Azure.Search`, czyli Meta-pakiet, który zawiera wszystkie pozostałe pakiety jako zależności. Użyj tego pakietu, jeśli dopiero zaczynasz pracę lub wiesz, że aplikacja będzie potrzebować wszystkich funkcji Azure Search.

Inne pakiety NuGet w zestawie SDK są następujące:
 
  - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET przy użyciu Azure Search i chcesz tylko wysyłać zapytania lub aktualizować dokumenty w swoich indeksach. Jeśli konieczne jest również utworzenie lub zaktualizowanie indeksów, map synonimów lub innych zasobów na poziomie usług, zamiast tego `Microsoft.Azure.Search` należy użyć pakietu.
  - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzację w programie .NET do zarządzania indeksami Azure Search, mapami synonimów, indeksatorami, źródłami danych lub innymi zasobami na poziomie usług. Jeśli musisz tylko zbadać lub zaktualizować dokumenty w indeksach, użyj `Microsoft.Azure.Search.Data` zamiast niego pakietu. Jeśli potrzebujesz wszystkich funkcji Azure Search, zamiast tego użyj `Microsoft.Azure.Search` pakietu.
  - `Microsoft.Azure.Search.Common`: Typy wspólne wymagane przez Azure Search biblioteki platformy .NET. Nie musisz używać tego pakietu bezpośrednio w aplikacji. Jest ona przeznaczona tylko do użycia jako zależność.

Różne biblioteki klienta definiują takie klasy `Index`jak `Field`,, `Document`i, `SearchServiceClient` jak również operacje takie `Indexes.Create` jak `Documents.Search` i w klasach i `SearchIndexClient` . Te klasy są zorganizowane w następujące przestrzenie nazw:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Jeśli chcesz przekazać opinię na temat przyszłej aktualizacji zestawu SDK, zobacz [stronę opinii](https://feedback.azure.com/forums/263029-azure-search/) lub Utwórz problem w witrynie [GitHub](https://github.com/azure/azure-sdk-for-net/issues) i wskaż "Azure Search" w tytule problemu.

Zestaw SDK platformy .NET obsługuje `2019-05-06` wersję [interfejsu API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Ta wersja obejmuje obsługę [typów złożonych](search-howto-complex-data-types.md), [wyszukiwania poznawczego](cognitive-search-concept-intro.md), [](https://docs.microsoft.com/rest/api/searchservice/autocomplete)autouzupełniania i [trybu analizowania JsonLines](search-howto-index-json-blobs.md) podczas indeksowania obiektów blob platformy Azure. 

Ten zestaw SDK nie obsługuje [operacji zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) , takich jak tworzenie i skalowanie usług wyszukiwania oraz zarządzanie kluczami interfejsu API. Jeśli potrzebujesz zarządzać zasobami wyszukiwania z poziomu aplikacji .NET, możesz użyć [zestawu SDK zarządzania Azure Search .NET](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uaktualnianie do najnowszej wersji zestawu SDK
Jeśli używasz już starszej wersji zestawu SDK programu Azure Search .NET i chcesz przeprowadzić uaktualnienie do najnowszej, ogólnie dostępnej wersji, w [tym artykule](search-dotnet-sdk-migration-version-9.md) wyjaśniono, jak.

## <a name="requirements-for-the-sdk"></a>Wymagania dotyczące zestawu SDK
1. Program Visual Studio 2017 lub nowszy.
2. Twoja usługa Azure Search. Aby można było korzystać z zestawu SDK, potrzebna jest nazwa usługi i jeden lub więcej kluczy interfejsu API. [Tworzenie usługi w portalu](search-create-service-portal.md) pomoże Ci wykonać te kroki.
3. Pobierz [pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) zestawu SDK platformy .NET Azure Search przy użyciu opcji "Zarządzaj pakietami NuGet" w programie Visual Studio. Po prostu wyszukaj nazwę `Microsoft.Azure.Search` pakietu w NuGet.org (lub jedną z powyższych nazw pakietów, jeśli potrzebujesz tylko podzestawu funkcjonalności).

Zestaw SDK platformy .NET Azure Search obsługuje aplikacje ukierunkowane na .NET Framework 4.5.2 i nowsze, a także platformę .NET Core 2,0 lub nowszą.

## <a name="core-scenarios"></a>Scenariusze podstawowe
Istnieje kilka rzeczy, które należy wykonać w aplikacji wyszukiwania. W tym samouczku omówiono następujące podstawowe scenariusze:

* Tworzenie indeksu
* Zapełnianie indeksu dokumentami
* Wyszukiwanie dokumentów przy użyciu wyszukiwania pełnotekstowego i filtrów

Poniższy przykładowy kod ilustruje każdy z tych scenariuszy. Możesz korzystać z fragmentów kodu we własnej aplikacji.

### <a name="overview"></a>Omówienie
Przykładowa aplikacja, którą będziemy eksplorować, utworzy nowy indeks o nazwie "Hotele", wypełni go za pomocą kilku dokumentów, a następnie wykonuje kilka zapytań wyszukiwania. Oto główny program przedstawiający ogólny przepływ:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Możesz znaleźć pełny kod źródłowy przykładowej aplikacji używanej w tym poradniku w portalu [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Przeprowadzimy Cię przez ten krok po kroku. Najpierw musimy utworzyć nowy `SearchServiceClient`. Ten obiekt umożliwia zarządzanie indeksami. W celu skonstruowania jednego należy podać nazwę usługi Azure Search, a także klucz interfejsu API administratora. Możesz wprowadzić te informacje w `appsettings.json` pliku przykładowej [aplikacji](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> W przypadku podania niepoprawnego klucza (na przykład klucza zapytania, w którym jest wymagany klucz administratora), `SearchServiceClient` program zgłosi `CloudException` komunikat o błędzie "zabronione" przy pierwszym wywołaniu metody operacji na niej, takiej jak `Indexes.Create`. W takim przypadku należy dokładnie sprawdzić nasz klucz interfejsu API.
> 
> 

Kolejne kilka wierszy wywołuje metody w celu utworzenia indeksu o nazwie "Hotele", usuwając go najpierw, jeśli już istnieje. Przeprowadzimy przez te metody nieco później.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Następnie należy wypełnić indeks. Aby wypełnić indeks, potrzebny `SearchIndexClient`będzie. Istnieją dwa sposoby uzyskania jednego: poprzez jego konstruowanie lub przez wywołanie `Indexes.GetClient`. `SearchServiceClient` Używamy tej ostatniej dla wygody.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> W typowej aplikacji wyszukiwania zarządzanie indeksami i populacją mogą być obsługiwane przez oddzielny składnik od zapytań wyszukiwania. `Indexes.GetClient`jest wygodną metodą wypełniania indeksu, ponieważ powoduje to zaoszczędzenie `SearchCredentials`problemu związanego z udostępnianiem dodatkowych. Dzieje się tak dzięki przekazaniu klucza administratora, który został użyty w celu utworzenia klasy `SearchServiceClient` do nowej klasy `SearchIndexClient`. Jednak w ramach aplikacji, która wykonuje zapytania, lepiej jest utworzyć `SearchIndexClient` bezpośrednio, aby można było przekazać klucz zapytania, który umożliwia tylko odczytywanie danych, a nie klucza administratora. Jest to zgodne z zasadą najniższych uprawnień i pomaga zwiększyć bezpieczeństwo aplikacji. Więcej informacji o kluczach administratora i kluczach zapytań można znaleźć [tutaj](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teraz, gdy `SearchIndexClient`mamy, możemy wypełnić indeks. Wypełnianie indeksu odbywa się za pomocą innej metody, która zostanie przeprowadzona w późniejszym czasie.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Na koniec wykonujemy kilka zapytań wyszukiwania i wyświetlają wyniki. Tym razem korzystamy z innego `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Przejdziemy bliżej `RunQueries` metody później. Oto kod, aby utworzyć nowy `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tym razem korzystamy z klucza zapytania, ponieważ nie potrzebujemy dostępu do zapisu w indeksie. Możesz wprowadzić te informacje w `appsettings.json` pliku przykładowej [aplikacji](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Jeśli uruchomisz tę aplikację z prawidłową nazwą usługi i kluczami interfejsu API, dane wyjściowe powinny wyglądać podobnie do tego przykładu: (Niektóre dane wyjściowe konsoli zostały zastąpione "..." na potrzeby ilustracji.

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Pełny kod źródłowy aplikacji znajduje się na końcu tego artykułu.

Następnie zajmiemy się bliżej każdej metody wywoływanej przez `Main`.

### <a name="creating-an-index"></a>Tworzenie indeksu
Po utworzeniu programu `SearchServiceClient`program `Main` usunie indeks "Hotele", jeśli już istnieje. To usuwanie jest wykonywane przez następującą metodę:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ta metoda używa danego `SearchServiceClient` do sprawdzenia, czy indeks istnieje, a jeśli tak, należy go usunąć.

> [!NOTE]
> Przykład kodu przedstawiony w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. Na przykład w powyższej metodzie można użyć `ExistsAsync` elementów `DeleteAsync` `Exists` i zamiast i `Delete`.
> 
> 

`Main` Następnie tworzy nowy indeks "Hotele" przez wywołanie tej metody:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Ta metoda tworzy nowy `Index` obiekt z `Field` listą obiektów, które definiują schemat nowego indeksu. Każde pole ma nazwę, typ danych i kilka atrybutów definiujących zachowanie wyszukiwania. Klasa używa odbicia, aby utworzyć `Field` listę obiektów dla indeksu, sprawdzając właściwości publiczne i atrybuty danej `Hotel` klasy modelu. `FieldBuilder` Przejdziemy bliżej `Hotel` klasy później.

> [!NOTE]
> Możesz zawsze utworzyć listę `Field` obiektów bezpośrednio zamiast używać `FieldBuilder` w razie konieczności. Na przykład nie trzeba używać klasy modelu lub może być konieczne użycie istniejącej klasy modelu, która nie ma być modyfikowana przez dodanie atrybutów.
>
> 

Oprócz pól można także dodać profile oceniania, sugerują lub opcje CORS do indeksu (te parametry są pomijane na przykład dla zwięzłości). Więcej informacji na temat obiektu index i jego części składowych można znaleźć w [dokumentacji zestawu SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), a także w [dokumentacji interfejsu API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Zapełnianie indeksu
Następny krok w `Main` wypełnia nowo utworzony indeks. Ta populacja indeksu jest wykonywana w następującej metodzie: (Kod zamieniono na "..." na potrzeby ilustracji.  Zobacz pełne przykładowe rozwiązanie dla kodu populacji pełnej danych.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

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
}
```

Ta metoda ma cztery części. Pierwszy tworzy tablicę trzech `Hotel` obiektów z 3 `Room` obiektami, które będą traktować jako dane wejściowe do przekazania do indeksu. Te dane są trwale kodowane dla uproszczenia. W Twojej aplikacji Twoje dane prawdopodobnie będą pochodzić z zewnętrznego źródła danych, takiego jak baza danych SQL.

Druga część tworzy `IndexBatch` dokument zawierający dokumenty. Należy określić operację, która ma zostać zastosowana do partii w momencie utworzenia, w tym przypadku przez wywołanie `IndexBatch.Upload`. Partia jest następnie przekazywana do indeksu Azure Search przez `Documents.Index` metodę.

> [!NOTE]
> W tym przykładzie właśnie przekazująmy dokumenty. Jeśli chcesz scalić zmiany w istniejących dokumentach lub usunąć dokumenty, możesz utworzyć partie, wywołując `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`lub `IndexBatch.Delete` zamiast. Możesz również mieszać różne operacje w pojedynczej partii, wywołując `IndexBatch.New`, który pobiera `IndexAction` kolekcję obiektów, z których każdy informuje Azure Search, aby wykonać określoną operację na dokumencie. Każdą z nich `IndexAction` można utworzyć przy użyciu własnej operacji poprzez wywołanie odpowiedniej metody, takiej `IndexAction.Merge`jak `IndexAction.Upload`,, i tak dalej.
> 
> 

Trzecią częścią tej metody jest blok catch, który obsługuje ważny przypadek błędu indeksowania. Jeśli usługa Azure Search nie może zindeksować niektórych dokumentów w partii, metoda `Documents.Index` zgłasza wyjątek `IndexBatchException`. Ten wyjątek może wystąpić, jeśli indeksowane są dokumenty, gdy usługa jest mocno obciążona. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

> [!NOTE]
> Można użyć [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metody do konstruowania nowej partii zawierającej tylko akcje, które zakończyły się niepowodzeniem w `Index`poprzednim wywołaniu. Zawarto informacje na temat prawidłowego używania go w programie [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Na koniec Metoda `UploadDocuments` opóźnia się przez dwie sekundy. Indeksowanie w usłudze Azure Search jest asynchroniczne, więc przykładowa aplikacja musi czekać przez krótki czas, aby upewnić się, że dokumenty można wyszukiwać. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Jak zestaw .NET SDK obsługuje dokumenty
Możesz się zastanawiać, jak zestaw .NET SDK usługi Azure Search jest w stanie przekazać wystąpienia klasy zdefiniowanej przez użytkownika, np. `Hotel`, do indeksu. Aby pomóc odpowiedzieć na to pytanie, przyjrzyjmy się `Hotel` klasie:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Najpierw należy zauważyć, że nazwa każdej właściwości publicznej w `Hotel` klasie zostanie zamapowana na pole o tej samej nazwie w definicji indeksu. Jeśli chcesz, aby każde pole było uruchamiane z małą literą ("notacji CamelCase Case"), możesz poinstruować zestaw SDK, aby mapuje nazwy właściwości do notacji CamelCase-Case automatycznie z `[SerializePropertyNamesAsCamelCase]` atrybutem klasy. Ten scenariusz jest typowy w aplikacjach .NET, które wykonują powiązania danych, w których schemat docelowy jest poza kontrolą dewelopera aplikacji bez konieczności naruszania wytycznych dotyczących nazewnictwa "przypadek w języku Pascal" w programie .NET.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search korzysta z biblioteki [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) w celu serializacji i deserializacji niestandardowych obiektów modelu do i z formatu JSON. W razie potrzeby możesz dostosować serializację. Aby uzyskać więcej informacji, zobacz [niestandardowe serializacji z JSON.NET](#JsonDotNet).
> 
> 

Druga `IsFilterable`należy zauważyć `IsSearchable` ,żekażdaWłaściwość`Analyzer`zawiera atrybuty, takie jak,, ,i.`Key` Te atrybuty są mapowane bezpośrednio do [odpowiednich atrybutów pola w indeksie Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Klasa używa tych właściwości do konstruowania definicji pól dla indeksu.

Trzecią istotną kwestią `Hotel` dotyczącą klasy są typy danych właściwości publicznych. Typy .NET tych właściwości są mapowane na odpowiadające im typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `Edm.String`. Istnieją podobne mapowania typu `bool?`między, `Edm.Boolean`, `DateTimeOffset?`, i `Edm.DateTimeOffset` tak dalej. Dokładne zasady mapowania typów znajdują się w dokumentacji metody `Documents.Get` w [dokumentacji zestawu .NET SDK usługi Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). `FieldBuilder` Klasa obsługuje to mapowanie, ale nadal może być zrozumiałe w przypadku konieczności rozwiązywania problemów z serializacji.

Czy wiesz, że właściwość została `SmokingAllowed` zadana?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Atrybut dla tej właściwości informuje, `FieldBuilder` aby nie serializować go do indeksu jako pole. `JsonIgnore`  Jest to świetny sposób tworzenia właściwości obliczeniowych po stronie klienta, których można użyć jako pomocników w aplikacji.  W tym przypadku `SmokingAllowed` właściwość odzwierciedla, czy którykolwiek `Room` z `Rooms` kolekcji pozwala na palenie.  Jeśli wszystkie mają wartość false, oznacza to, że cały hotel nie zezwala na palenie.

Niektóre właściwości, takie `Address` jak `Rooms` i są wystąpieniami klas .NET.  Te właściwości reprezentują bardziej złożone struktury danych, a w rezultacie wymagają pól ze [złożonym typem danych](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) w indeksie.

Właściwość reprezentuje zestaw wielu wartości `Address` w klasie zdefiniowanej poniżej: `Address`

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Ta klasa zawiera standardowe wartości używane do opisywania adresów w Stany Zjednoczone lub Kanadzie. Możesz użyć typów takich jak ten do grupowania pól logicznych w indeksie.

Właściwość reprezentuje tablicę `Room`obiektów: `Rooms`

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Model danych w programie .NET i odpowiadający mu schemat indeksu powinien zostać zaprojektowany do obsługi wyszukiwania, które chcesz przekazać użytkownikowi końcowemu. Każdy obiekt najwyższego poziomu w programie .NET, dokument programu IE w indeksie, odpowiada wynikowi wyszukiwania, który jest obecny w interfejsie użytkownika. Na przykład w aplikacji wyszukiwania w hotelu użytkownicy końcowi mogą chcieć przeszukiwać według nazwy hotelu, funkcji hotelu lub cech charakterystycznych w konkretnym pokoju. Przykłady zapytań będą omawiane nieco później.

Możliwość korzystania z własnych klas w celu współdziałania z dokumentami w indeksie działa w obu kierunkach. Możesz również pobrać wyniki wyszukiwania i zestaw SDK automatycznie deserializacji do wybranego typu, ponieważ zobaczymy w następnej sekcji.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search obsługuje również dynamiczne typowanie dokumentów za pomocą klasy `Document`, która stanowi mapowanie klucz/wartość nazw pól na wartości pól. Jest to przydatne, jeśli nie znasz schematu indeksu w czasie projektowania lub jeśli powiązanie z określonymi klasami modelu jest niedogodne. Wszystkie metody w zestawie SDK, które obsługują dokumenty, mają przeciążenia działające z klasą `Document`, a także przeciążenia o silnych typach przyjmujące parametr typu ogólnego. Tylko te ostatnie są używane w przykładowym kodzie w tym samouczku. Klasa dziedziczy z. [ `Document` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) `Dictionary<string, object>`
> 
>

**Dlaczego należy używać typów danych dopuszczających wartość null**

Jeśli projektujesz własne klasy modeli na potrzeby mapowania na indeks usługi Azure Search, zalecamy deklarowanie właściwości typów wartości, takich jak `bool` i `int`, aby dopuszczały możliwość użycia wartości null (na przykład `bool?` zamiast `bool`). W przypadku użycia właściwości niedopuszczającej wartości null musisz **zagwarantować**, że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Ani zestaw SDK, ani usługa Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem. Wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu najlepszym i zalecanym rozwiązaniem jest używanie w klasach modeli typów dopuszczających wartość null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serializacja niestandardowa z JSON.NET
Zestaw SDK używa JSON.NET do serializacji i deserializacji dokumentów. W razie konieczności można dostosować serializację i deserializacja, definiując własne `JsonConverter` lub. `IContractResolver` Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Może to być przydatne, jeśli chcesz dostosować istniejącą klasę modelu z aplikacji do użycia z Azure Search i innych zaawansowanych scenariuszy. Na przykład przy użyciu serializacji niestandardowej można:

* Dołącz lub Wyklucz pewne właściwości klasy modelu z przechowywania jako pola dokumentu.
* Mapuj między nazwami właściwości w kodzie i nazwach pól w indeksie.
* Tworzenie atrybutów niestandardowych, których można użyć do mapowania właściwości do pól dokumentu.

Przykłady implementacji serializacji niestandardowej można znaleźć w testach jednostkowych dla Azure Search .NET SDK w witrynie GitHub. Dobrym punktem początkowym jest [ten folder](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Zawiera klasy, które są używane przez niestandardowe testy serializacji.

### <a name="searching-for-documents-in-the-index"></a>Wyszukiwanie dokumentów w indeksie
Ostatnim krokiem w przykładowej aplikacji jest wyszukanie niektórych dokumentów w indeksie:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Za każdym razem, gdy wykonuje zapytanie, ta metoda najpierw tworzy nowy `SearchParameters` obiekt. Ten obiekt służy do określania dodatkowych opcji zapytania, takich jak sortowanie, filtrowanie, stronicowanie i aspektowanie. `Filter`W tej metodzie ustawiamy właściwość, `Select`, `OrderBy`, i `Top` dla różnych zapytań. Wszystkie właściwości są udokumentowane [tutaj.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) `SearchParameters`

Następnym krokiem jest faktyczne wykonanie zapytania wyszukiwania. Uruchamianie wyszukiwania odbywa się przy użyciu `Documents.Search` metody. Dla każdego zapytania przekazujemy tekst wyszukiwania do użycia jako ciąg (lub `"*"` w przypadku braku tekstu wyszukiwania) oraz utworzonych wcześniej parametrów wyszukiwania. Określamy `Hotel` również jako parametr typu dla `Documents.Search`, który informuje zestaw SDK, aby deserializować dokumenty w wynikach wyszukiwania do obiektów typu `Hotel`.

> [!NOTE]
> Więcej informacji na temat składni wyrażenia zapytania wyszukiwania można znaleźć [tutaj](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Na koniec po każdym zapytaniu ta metoda iteruje wszystkie dopasowania w wynikach wyszukiwania, drukując każdy dokument do konsoli:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Przyjrzyjmy się bliżej każdej z tych zapytań. Oto kod do wykonania pierwszego zapytania:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

W tym przypadku przeszukujemy cały indeks słowa "Motel" w dowolnym polu z możliwością wyszukiwania i chcemy tylko pobrać nazwy hotelu, zgodnie z `Select` parametrem. Oto wyniki:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Następne zapytanie jest nieco bardziej interesujące.  Chcemy znaleźć wszelkie Hotele z sali o nocnej stawce mniejszej niż $100 i zwrócić tylko identyfikator hotelu i opis:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

To zapytanie używa wyrażenia OData `$filter` , `Rooms/any(r: r/BaseRate lt 100)`aby odfiltrować dokumenty w indeksie. Używa [dowolnego operatora](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) , aby zastosować "BaseRate lt 100" do każdego elementu w kolekcji pokojów. Więcej informacji na temat składni OData, która Azure Search obsługiwana w [tym miejscu](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Oto wyniki zapytania:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Następnie chcemy znaleźć dwie pierwsze Hotele, które zostały ostatnio renovatedne, a następnie pokazać nazwę hotelu i datę ostatniej renowacji. Oto kod: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

W takim przypadku będziemy ponownie używać składni OData do określenia `OrderBy` parametru jako. `lastRenovationDate desc` Ustawimy `Top` również 2, aby upewnić się, że pobieramy tylko pierwsze dwa dokumenty. Jak wcześniej ustawimy `Select` , aby określić, które pola powinny być zwracane.

Oto wyniki:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Na koniec chcemy znaleźć wszystkie nazwy hoteli, które pasują do wyrazu "Hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

A oto wyniki, które obejmują wszystkie pola, ponieważ nie określono `Select` właściwości:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ten krok kończy pracę z samouczkiem, ale nie został zatrzymany w tym miejscu. \* * Następne kroki zapewniają dodatkowe zasoby, aby dowiedzieć się więcej na temat Azure Search.

## <a name="next-steps"></a>Następne kroki
* Przeglądaj odwołania do [zestawu SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) oraz [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Zapoznaj się z [konwencjami nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) , aby poznać reguły nazewnictwa różnych obiektów.
* Przejrzyj [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) w Azure Search.
