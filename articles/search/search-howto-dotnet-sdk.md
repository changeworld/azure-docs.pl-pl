---
title: Jak używać usługi Azure Search z poziomu aplikacji .NET — usługa Azure Search
description: Dowiedz się, jak używać usługi Azure Search w aplikacji .NET za pomocą C# i zestawu SDK platformy .NET. Oparte na kodzie zadania to m.in. połączenia z usługą indeksowanie zawartości i wykonywania zapytań względem indeksu.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450010"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Jak używać usługi Azure Search z poziomu aplikacji .NET

Ten artykuł stanowi wskazówki pomagające w pracy z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk). Zestaw SDK platformy .NET umożliwia Implementowanie zaawansowanych funkcji wyszukiwania w aplikacji za pomocą usługi Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Co to jest na platformie Azure wyszukiwania zestawu SDK
Zestaw SDK składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksów, źródeł danych, indeksatorów i synonim mapuje, a także przekazywania i zarządzania dokumentami i wykonywania zapytań, bez konieczności ze szczegółami protokołów HTTP i JSON. Tych bibliotek klienckich są dystrybuowane jako pakiety NuGet.

Główny pakiet NuGet jest `Microsoft.Azure.Search`, czyli meta pakiet, który zawiera wszystkie pakiety jako zależności. Użyj tego pakietu, jeśli po prostu rozpoczynasz pracę lub jeśli wiesz, że Twoja aplikacja musi mieć wszystkie funkcje usługi Azure Search.

Inne pakiety NuGet zestawu SDK są:
 
  - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET za pomocą usługi Azure Search i wystarczy tylko zapytania lub aktualizowanie dokumentów w indeksów. Jeśli trzeba będzie również tworzenie lub aktualizowanie indeksów, map synonimów lub innych zasobów dotyczących poziomu usług, należy użyć `Microsoft.Azure.Search` zamiast tego pakietu.
  - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzacji na platformie .NET do zarządzania indeksów usługi Azure Search, map synonimów, indeksatory, źródła danych lub innych zasobów na poziomie usługi. Jeśli wymagane jest tylko do dokumentów zapytania lub aktualizacji w indeksów, użyj `Microsoft.Azure.Search.Data` zamiast tego pakietu. Jeśli potrzebujesz wszystkich funkcji usługi Azure Search, użyj `Microsoft.Azure.Search` zamiast tego pakietu.
  - `Microsoft.Azure.Search.Common`: Popularne typy wymagane przez bibliotekami .NET usługi Azure Search. Nie trzeba używać tego pakietu bezpośrednio w aplikacji. Jest przeznaczone tylko do użycia jako zależność.

Różnych bibliotek klienta definiuje klasy, takie jak `Index`, `Field`, i `Document`, jak również operacje, takie jak `Indexes.Create` i `Documents.Search` na `SearchServiceClient` i `SearchIndexClient` klasy. Te klasy są podzielone na następujące przestrzenie nazw:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Jeśli chcesz przekazać opinię dla przyszłej aktualizacji zestawu SDK, zobacz nasze [strona opinii o](https://feedback.azure.com/forums/263029-azure-search/) lub Utwórz problem w [GitHub](https://github.com/azure/azure-sdk-for-net/issues) i wspomina o identyfikatorach "Usługi Azure Search" w tytule problem.

Zestaw .NET SDK obsługuje wersję `2019-05-06` z [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Ta wersja obejmuje obsługę [typy złożone](search-howto-complex-data-types.md), [wyszukiwania kognitywnego](cognitive-search-concept-intro.md), [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete), i [JsonLines tryb analizy](search-howto-index-json-blobs.md) po Indeksowanie obiektów blob platformy Azure. 

Ten zestaw SDK nie obsługuje [operacji zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) takich jak tworzenie i skalowanie usługi wyszukiwania i zarządzanie kluczami interfejsu API. Jeśli musisz zarządzać zasobami wyszukiwania z poziomu aplikacji .NET, możesz użyć [zestawu SDK usługi Azure Search .NET zarządzania](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uaktualnianie do najnowszej wersji zestawu SDK
Jeśli już używasz starszej wersji zestawu .NET SDK usługi Azure Search i chcesz uaktualnić do najnowszej wersji ogólnie dostępnej wersji [w tym artykule](search-dotnet-sdk-migration-version-9.md) wyjaśnia sposób.

## <a name="requirements-for-the-sdk"></a>Wymagania dotyczące zestawu SDK
1. Program Visual Studio 2017 r. lub nowszej.
2. Własnej usługi Azure Search. Aby można było używać zestawu SDK, należy nazwę usługi i co najmniej jeden klucz interfejsu API. [Tworzenie usługi w portalu](search-create-service-portal.md) pomoże Ci przez następujące kroki.
3. Pobieranie zestawu .NET SDK usługi Azure Search [pakietu NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) za pomocą "Zarządzaj pakietami NuGet" w programie Visual Studio. Po prostu wyszukać nazwę pakietu `Microsoft.Azure.Search` w witrynie NuGet.org (lub jednego z innych pakietów nazwy powyżej, jeśli potrzebujesz tylko podzbiór funkcji).

Zestaw .NET SDK usługi Azure Search obsługuje aplikacji przeznaczonych dla platformy .NET Framework 4.5.2 i nowsze, jak również jako zestaw .NET Core 2.0 lub nowszym.

## <a name="core-scenarios"></a>Podstawowe scenariusze
Istnieje kilka rzeczy, które należy wykonać w aplikacji wyszukiwania. W tym samouczku omówiono te podstawowe scenariusze:

* Tworzenie indeksu
* Podczas wypełniania indeksu z dokumentami
* Wyszukiwanie dokumentów przy użyciu wyszukiwania pełnotekstowego i filtrów

Poniższy przykład kodu ilustruje każdą z tych scenariuszy. Możesz użyć fragmentów kodu w swojej aplikacji.

### <a name="overview"></a>Przegląd
Firma Microsoft będzie mieć Eksplorowanie przykładowej aplikacji, tworzy nowy indeks o nazwie "hotels", wypełnia ją za pomocą kilku dokumentów, a następnie wykonuje kilka zapytań wyszukiwania. Oto głównego programu, przedstawiający ogólny przepływ:

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

Omówimy to krok po kroku. Najpierw musimy utworzyć nową `SearchServiceClient`. Ten obiekt umożliwia zarządzanie indeksami. Aby można było utworzyć jeden, musisz podać nazwę usługi Azure Search, a także klucz administratora interfejsu API. Możesz wprowadzić te informacje w `appsettings.json` pliku [Przykładowa aplikacja](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Jeśli podano nieprawidłowy klucz (na przykład klucz zapytania gdzie był wymagany klucz administratora), `SearchServiceClient` zgłosi `CloudException` z powodu błędu komunikatu "Dostęp zabroniony" wywołujesz metodę operacji, takich jak po raz pierwszy `Indexes.Create`. W takim przypadku użytkownikowi dokładnie Sprawdź nasze klucz interfejsu API.
> 
> 

Następnych kilka wierszy wywoływać metody, by utworzyć indeks o nazwie "hotels", usuwając go najpierw, jeśli już istnieje. Firma Microsoft przeprowadzi tych metod nieco później.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Następnie indeksu musi być wypełnione. Aby wypełnić indeks, musimy `SearchIndexClient`. Istnieją dwa sposoby na uzyskanie takiego: tworząc je lub przez wywołanie `Indexes.GetClient` na `SearchServiceClient`. Możemy użyć drugiej opcji dla wygody.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> W typowej aplikacji wyszukującej wypełnianie indeksu i zarządzanie mogą być obsługiwane przez inny składnik niż zapytania wyszukiwania. `Indexes.GetClient` jest wygodną podczas wypełniania indeksu, ponieważ zapisuje problemów związanych z udostępnianiem dodatkowe `SearchCredentials`. Dzieje się tak dzięki przekazaniu klucza administratora, który został użyty w celu utworzenia klasy `SearchServiceClient` do nowej klasy `SearchIndexClient`. Jednak w części aplikacji, która wykonuje zapytania, lepiej jest utworzyć `SearchIndexClient` bezpośrednio, aby przekazać klucz zapytania, która służy tylko do odczytu danych, zamiast klucza administratora. Jest zgodny z zasadą najniższych uprawnień i pomoże zapewnić większe bezpieczeństwo aplikacji. Możesz dowiedzieć się więcej o kluczach administratora i kluczach zapytań [tutaj](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Skoro mamy już `SearchIndexClient`, firma Microsoft można wypełnić indeksu. Wypełniania indeksami pełnotekstowymi odbywa się przy użyciu innej metody, które firma Microsoft przeprowadzi później.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Na koniec możemy wykonać kilka zapytań wyszukiwania i wyświetlić wyniki. Teraz możemy użyć innego `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Firma Microsoft będzie Przyjrzyj się bliżej `RunQueries` metoda później. Poniżej przedstawiono kod, aby utworzyć nowy `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Teraz możemy używanie klucza zapytania, ponieważ nie potrzebujemy do zapisu w indeksie. Możesz wprowadzić te informacje w `appsettings.json` pliku [Przykładowa aplikacja](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Po uruchomieniu tej aplikacji z prawidłową nazwę usługi i klucze interfejsu API, dane wyjściowe powinny wyglądać następująco: (Niektóre dane wyjściowe konsoli zostało zastąpione przez "..." w celach ilustracyjnych.)

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

Następnie firma Microsoft będzie się im bliżej przyjrzeć w każdej metody wywoływane przez `Main`.

### <a name="creating-an-index"></a>Tworzenie indeksu
Po utworzeniu `SearchServiceClient`, `Main` usuwa indeksu "hotels", jeśli już istnieje. Usunięcie odbywa się według następującej metody:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ta metoda używa danej `SearchServiceClient` do sprawdzenia istnienia indeksu, a jeśli tak, usuń go.

> [!NOTE]
> Przykład kodu przedstawiony w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. Na przykład w powyższej metody należy użyć `ExistsAsync` i `DeleteAsync` zamiast `Exists` i `Delete`.
> 
> 

Następnie `Main` powoduje utworzenie nowego indeksu "hotels" przez wywołanie tej metody:

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

Ta metoda tworzy nowy `Index` obiekt z listy `Field` obiektów, które definiuje schemat nowego indeksu. Każde pole ma nazwę, typ danych i kilka atrybutów, które określają zachowanie wyszukiwania. `FieldBuilder` Klasy używa odbicia w celu utworzenia listy `Field` obiektów dla indeksu, sprawdzając właściwości publiczne i atrybuty danego `Hotel` klasa modelu. Firma Microsoft będzie Przyjrzyj się bliżej `Hotel` klasy później.

> [!NOTE]
> Zawsze można utworzyć listy `Field` obiektów bezpośrednio zamiast przy użyciu `FieldBuilder` w razie potrzeby. Na przykład może nie chcesz używać klasy modelu, lub może być konieczne użycie istniejącej klasy modelu, których nie chcesz, aby zmodyfikować przez dodanie atrybutów.
>
> 

Oprócz pól można również dodać profile oceniania, sugestory lub opcje CORS do indeksu (parametry te pominięto z przykładu w celu skrócenia programu). Można znaleźć więcej informacji o obiekcie indeksu i jego elementów składowych w [odwołanie do zestawu SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), oraz w [odwołanie do interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Wypełnianie indeksu
Następnym krokiem podczas `Main` wypełnia nowo utworzony indeks. Ta wypełniania indeksami pełnotekstowymi odbywa się w następującej metody: (Niektóre kody zastępowany "..." dla celów ilustracyjnych.  Zobacz pełny przykład rozwiązania Code populacji pełnych danych).

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

Ta metoda ma cztery części. Pierwszy tworzy tablicę o 3 `Hotel` obiektów, każdy z 3 `Room` obiektów, które będzie służyć jako nasze dane wejściowe do przekazania do indeksu. Te dane są zakodowane dla uproszczenia. W swojej aplikacji dane będą prawdopodobnie pochodzić z zewnętrznego źródła danych takich jak bazy danych SQL.

Druga część tworzy `IndexBatch` zawierającego dokumenty przeznaczone. Określ operacji, który chcesz zastosować do danego wsadu w czasie jego tworzenia, w tym przypadku przez wywołanie metody `IndexBatch.Upload`. Partii jest następnie przekazywany do indeksu usługi Azure Search przy `Documents.Index` metody.

> [!NOTE]
> W tym przykładzie mamy po prostu przekazywania dokumentów. Jeśli chcesz scalić zmiany istniejących dokumentów lub usuwanie dokumentów, możesz utworzyć partie przez wywołanie metody `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, lub `IndexBatch.Delete` zamiast tego. Można także łączyć różne operacje w jednej partii, wywołując `IndexBatch.New`, który przyjmuje zbiór `IndexAction` obiektów, które informują usługę Azure Search można wykonać określonej operacji na dokumencie. Można utworzyć każdego `IndexAction` z działaniem przez wywołanie odpowiedniej metody, takie jak `IndexAction.Merge`, `IndexAction.Upload`i tak dalej.
> 
> 

Trzecia część tej metody jest blok catch obsługuje ważny przypadek błędu indeksowania. Jeśli usługa Azure Search nie może zindeksować niektórych dokumentów w partii, metoda `Documents.Index` zgłasza wyjątek `IndexBatchException`. Ten wyjątek może się zdarzyć, jeśli indeksujesz dokumenty, gdy usługa jest mocno obciążony. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

> [!NOTE]
> Możesz użyć [ `FindFailedActionsToRetry` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metodę, aby utworzyć nową partię zawierający tylko akcje, które nie udało się poprzednie wywołanie `Index`. Brak dyskusji jak prawidłowo używać go [na stronie StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Na koniec `UploadDocuments` opóźnienia metody dla dwóch sekund. Indeksowanie w usłudze Azure Search jest asynchroniczne, więc przykładowa aplikacja musi czekać przez krótki czas, aby upewnić się, że dokumenty można wyszukiwać. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Jak zestaw .NET SDK obsługuje dokumenty
Możesz się zastanawiać, jak zestaw .NET SDK usługi Azure Search jest w stanie przekazać wystąpienia klasy zdefiniowanej przez użytkownika, np. `Hotel`, do indeksu. Aby pomóc odpowiedzieć na to pytanie, Przyjrzyjmy się `Hotel` klasy:

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

Pierwszą rzeczą, którą należy zwrócić uwagę jest nazwa każdej właściwości publicznej w `Hotel` klasy będzie zmapowana do pola o tej samej nazwie w definicji indeksu. Jeśli chcesz każdego pola, aby rozpoczynać się małą literą ("format camel case") można stwierdzić, zestaw SDK ma mapować nazwy właściwości na notację camelcase automatycznie za pomocą `[SerializePropertyNamesAsCamelCase]` atrybut w klasie. Ten scenariusz jest typowy w aplikacjach platformy .NET, które tworzą powiązania danych, gdzie schemat docelowy jest poza kontrolą dewelopera aplikacji bez konieczności naruszają "pascalcase" nazewnictwa na platformie .NET.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search korzysta z biblioteki [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) w celu serializacji i deserializacji niestandardowych obiektów modelu do i z formatu JSON. W razie potrzeby możesz dostosować serializację. Aby uzyskać więcej informacji, zobacz [serializacja niestandardowa przy użyciu struktury JSON.NET](#JsonDotNet).
> 
> 

Drugi wszystkim należy zauważyć jest każda właściwość zostanie nadany za pomocą atrybutów, takich jak `IsFilterable`, `IsSearchable`, `Key`, i `Analyzer`. Te atrybuty mapowania bezpośrednio do [atrybuty odpowiedniego pola w indeksie usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Klasa używa tych właściwości do konstruowania definicje pól dla indeksu.

Trzeci ważną kwestią dotyczącą `Hotel` klasa jest typy danych właściwości publicznych. Typy .NET tych właściwości są mapowane na odpowiadające im typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `Edm.String`. Podobne mapowania typów występują między `bool?`, `Edm.Boolean`, `DateTimeOffset?`, i `Edm.DateTimeOffset` i tak dalej. Dokładne zasady mapowania typów znajdują się w dokumentacji metody `Documents.Get` w [dokumentacji zestawu .NET SDK usługi Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). `FieldBuilder` Klasy zajmie się to mapowanie dla Ciebie, ale nadal można zrozumieć, w przypadku, gdy trzeba rozwiązać wszelkie problemy z serializacją.

Czy stanie się zauważyć `SmokingAllowed` właściwości?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore` Informuje atrybutu dla tej właściwości `FieldBuilder` nie serializować do indeksu jako pole.  Jest to doskonały sposób na tworzenie właściwości obliczeniowe po stronie klienta służących jako obiekty pomocnicze w aplikacji.  W tym przypadku `SmokingAllowed` właściwość odzwierciedla czy `Room` w `Rooms` kolekcja umożliwia palenia.  Wszystkie mają wartość false, wskazuje, cały hotelu nie zezwala na palenia.

Niektóre właściwości, takie jak `Address` i `Rooms` wystąpień klas platformy .NET.  Te właściwości bardziej złożonych danych reprezentują struktury i dlatego wymagają pola z [typ złożony danych](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) w indeksie.

`Address` Właściwość reprezentuje zbiór wielu wartości w `Address` klasa zdefiniowana poniżej:

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

Ta klasa zawiera standardowe wartości używane do opisywania adresów w Stanach Zjednoczonych lub Kanadzie. Można użyć typów, takich jak to, aby pogrupować pola logicznych w indeksie.

`Rooms` Właściwość reprezentuje tablicę `Room` obiektów:

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

Modelu danych w programie .NET i jego odpowiedni schemat indeksu, powinny zostać tak zaprojektowane, aby obsługiwać środowisko wyszukiwania, które chcesz nadać użytkownikowi końcowemu. Każdy obiekt najwyższego poziomu, na platformie .NET, ie dokument w indeksie, odnosi się do wyniku wyszukiwania, które będą obecne w interfejsie użytkownika. Na przykład aplikację wyszukiwania w hotelu użytkownicy końcowi mogą prowadzone wyszukiwanie przez Nazwa hotelu funkcji hotelu lub cechy danego miejsca. Omówimy kilka przykładów zapytania nieco później.

Ta możliwość używania własnych klas do interakcji z dokumentów w indeksie działa w obu kierunkach; Można również pobrać wyniki wyszukiwania i mieć zestawu SDK dokonać ich do typu, co pozwala automatycznej deserializacji, jak firma Microsoft zostanie wyświetlony w następnej sekcji.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search obsługuje również dynamiczne typowanie dokumentów za pomocą klasy `Document`, która stanowi mapowanie klucz/wartość nazw pól na wartości pól. Jest to przydatne, jeśli nie znasz schematu indeksu w czasie projektowania lub jeśli powiązanie z określonymi klasami modelu jest niedogodne. Wszystkie metody w zestawie SDK, które obsługują dokumenty, mają przeciążenia działające z klasą `Document`, a także przeciążenia o silnych typach przyjmujące parametr typu ogólnego. Tylko te ostatnie są używane w przykładowym kodzie w ramach tego samouczka. [ `Document` Klasy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) dziedziczy `Dictionary<string, object>`.
> 
>

**Dlaczego należy używać typów danych dopuszczających wartość null**

Jeśli projektujesz własne klasy modeli na potrzeby mapowania na indeks usługi Azure Search, zalecamy deklarowanie właściwości typów wartości, takich jak `bool` i `int`, aby dopuszczały możliwość użycia wartości null (na przykład `bool?` zamiast `bool`). W przypadku użycia właściwości niedopuszczającej wartości null musisz **zagwarantować**, że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Ani zestaw SDK, ani usługa Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem. Wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu najlepszym i zalecanym rozwiązaniem jest używanie w klasach modeli typów dopuszczających wartość null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Niestandardowej serializacji przy użyciu struktury JSON.NET
Zestaw SDK używa struktury JSON.NET do serializacji i deserializacji dokumentów. Można dostosować serializacji i deserializacji, jeśli to konieczne, definiując własne `JsonConverter` lub `IContractResolver`. Aby uzyskać więcej informacji, zobacz [dokumentacji na składnik JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Może to być przydatne, jeśli chcesz dostosować istniejącą klasę modelu z aplikacji do użycia z usługą Azure Search i innych bardziej zaawansowanych scenariuszy. Na przykład za pomocą niestandardowej serializacji możesz wykonywać następujące czynności:

* Dołącz lub Wyklucz określone właściwości klasy modelu z są przechowywane jako dokument pola.
* Mapowania nazw właściwości w kodzie i nazwy pól w indeksie.
* Tworzenie atrybutów niestandardowych, które mogą służyć do mapowania właściwości pól dokumentu.

Można znaleźć przykłady stosowania niestandardowej serializacji w testach jednostkowych for Azure Search .NET SDK w witrynie GitHub. To dobry punkt wyjścia [ten folder](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Zawiera klasy, które są używane przez testy niestandardowej serializacji.

### <a name="searching-for-documents-in-the-index"></a>Wyszukiwanie dokumentów w indeksie
Ostatnim krokiem w przykładowej aplikacji jest do wyszukiwania niektórych dokumentów w indeksie:

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

Każdorazowo wykonuje kwerendę, Metoda ta najpierw tworzy nową `SearchParameters` obiektu. Ten obiekt jest używany, aby określić dodatkowe opcje dla zapytania, takich jak sortowanie, filtrowanie, stronicowanie i tworzenia kategorii wyszukanych informacji. W przypadku tej metody, firma Microsoft jest ustawienie `Filter`, `Select`, `OrderBy`, i `Top` właściwość dla różnych zapytań. Wszystkie `SearchParameters` właściwości są udokumentowane [tutaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Następnym krokiem jest faktycznie wykonać zapytania wyszukiwania. Uruchamianie wyszukiwanie jest wykonywane przy użyciu `Documents.Search` metody. Dla każdego zapytania przekazujemy wyszukiwany tekst do użycia jako ciąg (lub `"*"` Jeśli nie ma żadnego tekstu wyszukiwania), oraz parametry wyszukiwania utworzone wcześniej. Możemy również określić `Hotel` jako parametr typu dla `Documents.Search`, informuje, zestaw SDK do deserializacji dokumentów w wynikach wyszukiwania w obiektach typu `Hotel`.

> [!NOTE]
> Można znaleźć więcej informacji na temat składni wyrażeń zapytania wyszukiwania [tutaj](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Na koniec po każdej kwerendy, ta metoda wykonuje iterację przez wszystkie dopasowania w wynikach wyszukiwania, drukowania każdego dokumentu do konsoli:

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

Przyjrzyjmy się bliżej w każdej kwerendy z kolei. Poniżej przedstawiono kod, aby wykonać pierwsze zapytanie:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

W takim przypadku w dowolnym polu możliwym do przeszukania Trwa przeszukiwanie całego indeksu dla słowa "motel" i ma być uruchamiany tylko można pobrać nazw hotelu, określony przez `Select` parametru. Poniżej przedstawiono wyniki:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Następne zapytanie jest nieco bardziej interesująca.  Chcemy znaleźć hotele, dowolnego miejsca nocne szybkość wynosi mniej niż 100 USD, które zwracają tylko identyfikator hotelu i opis:

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

To zapytanie używa OData `$filter` wyrażenie `Rooms/any(r: r/BaseRate lt 100)`, aby filtrowanie dokumentów w indeksie. Ta metoda korzysta z [każdy podmiot](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) do zastosowania "lt BaseRate 100' do każdego elementu w kolekcji pomieszczeniach. Możesz dowiedzieć się więcej na temat służy składnia OData, który obsługuje usługi Azure Search [tutaj](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Poniżej przedstawiono wyniki zapytania:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Następnie chcemy znaleźć najważniejsze dwóch hotele, które zostały ostatnio renovated i Pokaż Nazwa hotelu i Data ostatniego odnawianie. Oto kod: 

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

W takim przypadku ponownie używamy składnia OData do określenia `OrderBy` jako parametr `lastRenovationDate desc`. Dodatkowo ustawimy `Top` 2, aby upewnić się, uzyskujemy tylko dokumenty dwóch pierwszych. Ponieważ wcześniej ustawiliśmy `Select` do określenia, które pola powinny być zwracane.

Poniżej przedstawiono wyniki:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Na koniec chcemy znaleźć wszystkie nazwy hotele, które odpowiadają słowo "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

A Oto wyniki, które obejmują wszystkie pola, ponieważ firma Microsoft nie określa `Select` właściwości:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ten krok jest wykonywany samouczka, ale nie zatrzymać w tym miejscu. ** Następnych kroków zapewniają dodatkowe zasoby, aby uzyskać więcej informacji na temat usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki
* Przeglądaj odwołania do [zestawu SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) oraz [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Przegląd [konwencje nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) się zasady nazewnictwa różnych obiektów.
* Przegląd [obsługiwanych typów danych](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) w usłudze Azure Search.
