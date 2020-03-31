---
title: Korzystanie z usługi Azure Cognitive Search w sieci .NET
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak używać usługi Azure Cognitive Search w aplikacji .NET przy użyciu języka C# i sdk .NET. Zadania oparte na kodzie obejmują łączenie się z usługą, indeks zawartości i kwerendy indeksu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283071"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Jak korzystać z usługi Azure Cognitive Search z aplikacji .NET

Ten artykuł jest instruktażem, aby rozpocząć działania za pomocą [narzędzia Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Za pomocą narzędzia .NET SDK można zaimplementować bogate środowisko wyszukiwania w aplikacji przy użyciu usługi Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Co jest w sdku usługi Azure Cognitive Search
Zestaw SDK składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksami, źródłami danych, indeksatorami i mapami synonimów, a także przekazywanie i zarządzanie dokumentami oraz wykonywanie zapytań, a wszystko to bez konieczności zajmowania się szczegółami protokołu HTTP i JSON. Wszystkie te biblioteki klienckie są dystrybuowane jako pakiety NuGet.

Główny pakiet NuGet `Microsoft.Azure.Search`jest , który jest meta-pakiet, który zawiera wszystkie inne pakiety jako zależności. Użyj tego pakietu, jeśli dopiero zaczynasz lub jeśli wiesz, że aplikacja będzie potrzebować wszystkich funkcji usługi Azure Cognitive Search.

Inne pakiety NuGet w SDK są:
 
  - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli tworzysz aplikację .NET przy użyciu usługi Azure Cognitive Search i wystarczy tylko kwerendy lub zaktualizować dokumenty w indeksach. Jeśli trzeba również utworzyć lub zaktualizować indeksy, mapy synonimów lub inne `Microsoft.Azure.Search` zasoby na poziomie usługi, użyj pakietu zamiast tego.
  - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli tworzysz automatyzację w .NET do zarządzania indeksami usługi Azure Cognitive Search, mapami synonimów, indeksatorami, źródłami danych lub innymi zasobami na poziomie usługi. Jeśli trzeba tylko kwerendy lub zaktualizować dokumenty w `Microsoft.Azure.Search.Data` indeksach, należy użyć pakietu zamiast tego. Jeśli potrzebujesz wszystkich funkcji usługi Azure Cognitive `Microsoft.Azure.Search` Search, użyj pakietu zamiast tego.
  - `Microsoft.Azure.Search.Common`: Typowe typy wymagane przez biblioteki .NET usługi Azure Cognitive Search. Nie trzeba używać tego pakietu bezpośrednio w aplikacji. Jest przeznaczony tylko do użycia jako zależność.

Różne biblioteki klientów definiują `Index`klasy takie jak , `Field`i `Document`, a także operacje, `Indexes.Create` takie jak `Documents.Search` i na `SearchServiceClient` i `SearchIndexClient` klas. Te klasy są zorganizowane w następujące przestrzenie nazw:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Jeśli chcesz przekazać opinię na temat przyszłej aktualizacji SDK, zobacz naszą [stronę opinii](https://feedback.azure.com/forums/263029-azure-search/) lub utwórz problem w [usłudze GitHub](https://github.com/azure/azure-sdk-for-net/issues) i wspomnij o "usłudze Azure Cognitive Search" w tytule problemu.

Zestaw SDK platformy `2019-05-06` .NET obsługuje wersję [interfejsu API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/). Ta wersja zawiera obsługę [typów złożonych,](search-howto-complex-data-types.md) [wzbogacanie sztucznej](cognitive-search-concept-intro.md) [inteligencji, autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete)i [tryb analizy JsonLines](search-howto-index-json-blobs.md) podczas indeksowania obiektów blob platformy Azure. 

Ten pakiet SDK nie obsługuje [operacji zarządzania,](https://docs.microsoft.com/rest/api/searchmanagement/) takich jak tworzenie i skalowanie usług wyszukiwania oraz zarządzanie kluczami interfejsu API. Jeśli chcesz zarządzać zasobami wyszukiwania za pomocą aplikacji .NET, możesz użyć [sDK zarządzania usługi Azure Cognitive Search .NET](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uaktualnienie do najnowszej wersji sdk
Jeśli używasz już starszej wersji narzędzia Azure Cognitive Search .NET SDK i chcesz uaktualnić do najnowszej ogólnie dostępnej wersji, [w tym artykule](search-dotnet-sdk-migration-version-9.md) wyjaśniono, jak to zrobić.

## <a name="requirements-for-the-sdk"></a>Wymagania dotyczące SDK
1. Visual Studio 2017 lub nowszych.
2. Twoja własna usługa Azure Cognitive Search. Aby korzystać z SDK, trzeba będzie nazwę usługi i jeden lub więcej kluczy interfejsu API. [Tworzenie usługi w portalu](search-create-service-portal.md) pomoże Ci w tych krokach.
3. Pobierz [pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) usługi Azure Cognitive Search .NET przy użyciu "Zarządzaj pakietami NuGet" w programie Visual Studio. Wystarczy wyszukać `Microsoft.Azure.Search` nazwę pakietu na NuGet.org (lub jedną z innych nazw pakietów powyżej, jeśli potrzebujesz tylko podzbioru funkcji).

Zestaw SDK usługi Azure Cognitive Search .NET obsługuje aplikacje przeznaczone dla platformy .NET Framework 4.5.2 i nowszego, a także .NET Core 2.0 lub nowsze.

## <a name="core-scenarios"></a>Podstawowe scenariusze
Istnieje kilka czynności, które należy wykonać w aplikacji wyszukiwania. W tym samouczku omówimy te podstawowe scenariusze:

* Tworzenie indeksu
* Wypełnianie indeksu dokumentami
* Wyszukiwanie dokumentów przy użyciu wyszukiwania pełnotekstowego i filtrów

Poniższy przykładowy kod ilustruje każdy z tych scenariuszy. Możesz korzystać z fragmentów kodu we własnej aplikacji.

### <a name="overview"></a>Omówienie
Przykładowa aplikacja, którą będziemy eksplorować, tworzy nowy indeks o nazwie "hotele", wypełnia go kilkoma dokumentami, a następnie wykonuje niektóre zapytania wyszukiwania. Oto główny program, pokazujący ogólny przepływ:

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

Przejdziemy przez ten krok po kroku. Najpierw musimy stworzyć nowy `SearchServiceClient`. Ten obiekt umożliwia zarządzanie indeksami. Aby utworzyć jeden, należy podać nazwę usługi Azure Cognitive Search, a także klucz interfejsu API administratora. Można wprowadzić te informacje `appsettings.json` w pliku [przykładowej aplikacji](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Jeśli podasz niepoprawny klucz (na przykład klucz zapytania, w `SearchServiceClient` którym wymagany `CloudException` był klucz administratora), zostanie wyświetlony komunikat o błędzie "Zabronione" przy pierwszym wywołaniu metody operacji na nim, takiej jak `Indexes.Create`. Jeśli tak się stanie, sprawdź nasz klucz API.
> 
> 

Kilka następnych wierszy wywołać metody, aby utworzyć indeks o nazwie "hotele", usuwając go najpierw, jeśli już istnieje. Przejdziemy przez te metody nieco później.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Następnie indeks musi być wypełniona. Aby wypełnić indeks, będziemy potrzebować `SearchIndexClient`. Istnieją dwa sposoby, aby uzyskać jeden: poprzez `Indexes.GetClient` jego `SearchServiceClient`konstruowanie, lub wzywając . Używamy tego ostatniego dla wygody.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> W typowej aplikacji wyszukiwania zarządzanie indeksem i zasiewowanie mogą być obsługiwane przez oddzielny składnik od zapytań wyszukiwania. `Indexes.GetClient`jest wygodny do wypełniania indeksu, ponieważ oszczędza ci `SearchCredentials`kłopoty z zapewnieniem dodatkowych . Dzieje się tak dzięki przekazaniu klucza administratora, który został użyty w celu utworzenia klasy `SearchServiceClient` do nowej klasy `SearchIndexClient`. Jednak w części aplikacji, która wykonuje kwerendy, lepiej jest `SearchIndexClient` utworzyć bezpośrednio, dzięki czemu można przekazać w kluczu zapytania, który umożliwia tylko odczytywanie danych, zamiast klucza administratora. Jest to zgodne z zasadą najniższych uprawnień i pomoże zapewnić większe bezpieczeństwo aplikacji. Możesz dowiedzieć się więcej o kluczach administratora i kluczach zapytań [tutaj](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teraz, gdy `SearchIndexClient`mamy , możemy wypełnić indeks. Populacja indeksu odbywa się za pomocą innej metody, którą przejdziemy później.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Na koniec wykonujemy kilka zapytań wyszukiwania i wyświetlamy wyniki. Tym razem używamy `SearchIndexClient`innego:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Przyjrzymy się bliżej `RunQueries` tej metodzie później. Oto kod do tworzenia `SearchIndexClient`nowych:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Tym razem używamy klucza zapytania, ponieważ nie musimy zapisywać dostępu do indeksu. Można wprowadzić te informacje `appsettings.json` w pliku [przykładowej aplikacji](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Jeśli uruchomisz tę aplikację z prawidłową nazwą usługi i kluczami interfejsu API, dane wyjściowe powinny wyglądać następująco: (Niektóre dane wyjściowe konsoli zostały zastąpione "..." w celach ilustracyjnych).)

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

Następnie przyjrzymy się bliżej każdej z `Main`metod wywoływanych przez .

### <a name="creating-an-index"></a>Tworzenie indeksu
Po utworzeniu `SearchServiceClient` `Main` , usuwa indeks "hotele", jeśli już istnieje. To usunięcie odbywa się za pomocą następującej metody:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Ta metoda używa `SearchServiceClient` podane, aby sprawdzić, czy indeks istnieje, a jeśli tak, usuń go.

> [!NOTE]
> Przykładowy kod w tym artykule używa synchronicznych metod azure cognitive search .NET SDK dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. Na przykład w powyższej metodzie `DeleteAsync` można `Exists` użyć `Delete` `ExistsAsync` i zamiast i .
> 
> 

Następnie `Main` tworzy nowy indeks "hoteli", wywołując tę metodę:

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

Ta metoda tworzy `Index` nowy obiekt `Field` z listą obiektów, która definiuje schemat nowego indeksu. Każde pole ma nazwę, typ danych i kilka atrybutów, które definiują jego zachowanie wyszukiwania. Klasa `FieldBuilder` używa odbicia do utworzenia `Field` listy obiektów dla indeksu, badając właściwości publiczne `Hotel` i atrybuty danej klasy modelu. Przyjrzymy się bliżej `Hotel` klasie później.

> [!NOTE]
> Zawsze można utworzyć listę `Field` obiektów bezpośrednio zamiast `FieldBuilder` używać w razie potrzeby. Na przykład może nie chcesz używać klasy modelu lub może być konieczne użycie istniejącej klasy modelu, której nie chcesz modyfikować, dodając atrybuty.
>
> 

Oprócz pól można również dodać profile oceniania, sugest lub opcje CORS do indeksu (te parametry są pomijane w próbce dla zwięzłości). Więcej informacji na temat obiektu Index i jego części składowych można znaleźć w [odwołaniu do zestawu SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)a także w [odwołaniu do interfejsu API rest usługi Azure Cognitive Search.](https://docs.microsoft.com/rest/api/searchservice/)

### <a name="populating-the-index"></a>Wypełnianie indeksu
Następny krok `Main` w wypełnia nowo utworzony indeks. Ta populacja indeksu odbywa się w następującej metodzie: (Niektóre kody zastąpione "..." w celach ilustracyjnych.  Zobacz pełne przykładowe rozwiązanie dla pełnego kodu populacji danych.)

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

Ta metoda ma cztery części. Pierwszy tworzy tablicę 3 `Hotel` obiektów, `Room` każdy z 3 obiektów, które będą służyć jako nasze dane wejściowe do przekazania do indeksu. Te dane są zakodowane na czas dla uproszczenia. W aplikacji własnej dane będą prawdopodobnie pochodzić z zewnętrznego źródła danych, takich jak baza danych SQL.

Druga część tworzy `IndexBatch` zawierające dokumenty. Określ operację, którą chcesz zastosować do partii w momencie jej `IndexBatch.Upload`tworzenia, w tym przypadku przez wywołanie . Partia jest następnie przekazywane do indeksu usługi `Documents.Index` Azure Cognitive Search za pomocą metody.

> [!NOTE]
> W tym przykładzie po prostu przesyłamy dokumenty. Jeśli chcesz scalić zmiany w istniejące dokumenty lub usunąć `IndexBatch.Merge`dokumenty, możesz utworzyć partie, wywołując program , `IndexBatch.MergeOrUpload`lub `IndexBatch.Delete` zamiast tego. Można również mieszać różne operacje w `IndexBatch.New`pojedynczej partii `IndexAction` przez wywołanie , który przyjmuje kolekcję obiektów, z których każdy mówi usługi Azure Cognitive Search do wykonania określonej operacji w dokumencie. Każdy z `IndexAction` nich można utworzyć z własną operacją, wywołując odpowiednią metodę, taką jak `IndexAction.Merge`, `IndexAction.Upload`i tak dalej.
> 
> 

Trzecia część tej metody jest catch bloku, który obsługuje przypadek ważnego błędu do indeksowania. Jeśli usługa Azure Cognitive Search nie może indeksować niektórych dokumentów w partii, `IndexBatchException` jest generowany przez `Documents.Index`. Ten wyjątek może się zdarzyć, jeśli indeksujesz dokumenty, gdy usługa jest pod dużym obciążeniem. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

> [!NOTE]
> Za pomocą [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) tej metody można utworzyć nową partię zawierającą tylko akcje, które nie powiodły się w poprzednim wywołaniu `Index`. Istnieje dyskusja, jak prawidłowo go używać [na StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Na koniec `UploadDocuments` metoda opóźnia się o dwie sekundy. Indeksowanie odbywa się asynchronicznie w usłudze Azure Cognitive Search, więc przykładowa aplikacja musi czekać krótki czas, aby upewnić się, że dokumenty są dostępne do wyszukiwania. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Jak zestaw .NET SDK obsługuje dokumenty
Możesz się zastanawiać, jak azure cognitive search .NET SDK jest w `Hotel` stanie przekazać wystąpienia klasy zdefiniowanej przez użytkownika, takich jak do indeksu. Aby odpowiedzieć na to pytanie, `Hotel` przyjrzyjmy się klasie:

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

Pierwszą rzeczą, aby zauważyć, jest to, `Hotel` że nazwa każdej właściwości publicznej w klasie będzie mapować do pola o tej samej nazwie w definicji indeksu. Jeśli chcesz, aby każde pole zaczęło się od małej litery ("przypadek wielbłąda"), możesz powiedzieć zestawowi `[SerializePropertyNamesAsCamelCase]` SDK, aby automatycznie mapował nazwy właściwości na wielbłąda z atrybutem klasy. Ten scenariusz jest typowy w aplikacjach platformy .NET, które wykonują powiązanie danych, gdzie schemat docelowy znajduje się poza kontrolą dewelopera aplikacji bez konieczności naruszania wytycznych dotyczących nazewnictwa "Przypadek Pascala" w .NET.

> [!NOTE]
> Zestaw SDK usługi Azure Cognitive Search .NET używa biblioteki [JSON.NET NewtonSoft](https://www.newtonsoft.com/json/help/html/Introduction.htm) do serializacji i deserializacji obiektów modelu niestandardowego do i z usługi JSON. W razie potrzeby możesz dostosować serializację. Aby uzyskać więcej informacji, zobacz [Serializacja niestandardowa z JSON.NET](#JsonDotNet).
> 
> 

Drugą rzeczą, którą należy zauważyć, jest `IsFilterable`każda `IsSearchable` `Key`właściwość `Analyzer`jest ozdobiona atrybutami, takimi jak , , i . Te atrybuty są mapowane bezpośrednio do [odpowiednich atrybutów pól w indeksie usługi Azure Cognitive Search](/rest/api/searchservice/create-index). Klasa `FieldBuilder` używa tych właściwości do konstruowania definicji pól dla indeksu.

Trzecią ważną rzeczą `Hotel` w klasie są typy danych właściwości publicznych. Typy .NET tych właściwości są mapowane na odpowiadające im typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `Edm.String`. Istnieją podobne mapowania `bool?`typu `Edm.Boolean` `DateTimeOffset?`między `Edm.DateTimeOffset` , , i tak dalej. Szczegółowe reguły mapowania typów są `Documents.Get` dokumentowane za pomocą metody w [odwołaniu do sdk usługi Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). Klasa `FieldBuilder` zajmuje się tym mapowaniem dla Ciebie, ale nadal może być pomocne do zrozumienia w przypadku, gdy trzeba rozwiązać wszelkie problemy z serializacją.

Czy zdarzyło Ci `SmokingAllowed` się zauważyć właściwość?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Atrybut `JsonIgnore` w tej właściwości `FieldBuilder` mówi, aby nie serializować go do indeksu jako pole.  Jest to świetny sposób na tworzenie właściwości obliczeniowych po stronie klienta, których można używać jako pomocników w aplikacji.  W takim przypadku `SmokingAllowed` obiekt odzwierciedla, `Room` czy `Rooms` którykolwiek z kolekcji zezwala na palenie.  Jeśli wszystkie są fałszywe, oznacza to, że cały hotel nie zezwala na palenie.

Niektóre właściwości, `Address` takie `Rooms` jak i są wystąpieniami klas .NET.  Właściwości te reprezentują bardziej złożone struktury danych i w rezultacie wymagają pól ze [złożonym typem danych](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) w indeksie.

Właściwość `Address` reprezentuje zestaw wielu wartości `Address` w klasie, zdefiniowane poniżej:

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

Ta klasa zawiera standardowe wartości używane do opisywania adresów w Stanach Zjednoczonych lub Kanadzie. Takich typów można używać do grupowanie pól logicznych w indeksie.

Właściwość `Rooms` reprezentuje tablicę `Room` obiektów:

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

Model danych w .NET i odpowiadający mu schemat indeksu powinien być przeznaczony do obsługi środowiska wyszukiwania, które chcesz nadać użytkownikowi końcowemu. Każdy obiekt najwyższego poziomu w .NET, czyli dokument w indeksie, odpowiada wynik wyszukiwania, który można przedstawić w interfejsie użytkownika. Na przykład w aplikacji wyszukiwania hoteli użytkownicy końcowi mogą chcieć wyszukiwać według nazwy hotelu, funkcji hotelu lub charakterystyki danego pokoju. Omówimy kilka przykładów zapytań nieco później.

Ta możliwość korzystania z własnych klas do interakcji z dokumentami w indeksie działa w obu kierunkach; Można również pobrać wyniki wyszukiwania i mieć SDK automatycznie deserialize je do wybranego typu, jak zobaczymy w następnej sekcji.

> [!NOTE]
> Zestaw SDK usługi Azure Cognitive Search .NET obsługuje `Document` również dokumenty wpisywane dynamicznie przy użyciu klasy, która jest mapowaniem klucza/wartości nazw pól do wartości pól. Jest to przydatne, jeśli nie znasz schematu indeksu w czasie projektowania lub jeśli powiązanie z określonymi klasami modelu jest niedogodne. Wszystkie metody w zestawie SDK, które obsługują dokumenty, mają przeciążenia działające z klasą `Document`, a także przeciążenia o silnych typach przyjmujące parametr typu ogólnego. Tylko te ostatnie są używane w przykładowym kodzie w tym samouczku. [ `Document` Klasa](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) dziedziczy `Dictionary<string, object>`po .
> 
>

**Dlaczego należy używać typów danych dopuszczających wartość null**

Podczas projektowania własnych klas modelu do mapowania do indeksu usługi Azure Cognitive `bool` Search `int` zalecamy deklarowanie właściwości `bool?` typów `bool`wartości, takich jak i do anulowania (na przykład zamiast ). W przypadku użycia właściwości niedopuszczającej wartości null musisz **zagwarantować**, że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Ani zestaw SDK, ani usługa Azure Cognitive Search nie pomogą Ci to wymusić.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy są nullable w usłudze Azure Cognitive Search). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu najlepszym i zalecanym rozwiązaniem jest używanie w klasach modeli typów dopuszczających wartość null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Niestandardowa serializacja z JSON.NET
Moduł SDK używa JSON.NET do serializacji i deserializacji dokumentów. Serializację i deserializację można dostosować, definiując `JsonConverter` `IContractResolver`własną lub . Aby uzyskać więcej informacji, zobacz [JSON.NET dokumentacji](https://www.newtonsoft.com/json/help/html/Introduction.htm). Może to być przydatne, gdy chcesz dostosować istniejącą klasę modelu z aplikacji do użytku z usługą Azure Cognitive Search i innymi bardziej zaawansowanymi scenariuszami. Na przykład za pomocą niestandardowej serializacji można:

* Uwzględnij lub wyklucz niektóre właściwości klasy modelu z przechowywania ich jako pól dokumentu.
* Mapuj między nazwami właściwości w kodzie i nazwami pól w indeksie.
* Tworzenie atrybutów niestandardowych, które mogą być używane do mapowania właściwości pól dokumentu.

Przykłady implementowania niestandardowej serializacji można znaleźć w testach jednostkowych dla usługi Azure Cognitive Search .NET SDK w usłudze GitHub. Dobrym punktem wyjścia jest [ten folder](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Zawiera klasy, które są używane przez niestandardowe testy serializacji.

### <a name="searching-for-documents-in-the-index"></a>Wyszukiwanie dokumentów w indeksie
Ostatnim krokiem w przykładowej aplikacji jest wyszukiwanie niektórych dokumentów w indeksie:

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

Za każdym razem, gdy wykonuje kwerendę, `SearchParameters` ta metoda najpierw tworzy nowy obiekt. Ten obiekt jest używany do określenia dodatkowych opcji dla kwerendy, takich jak sortowanie, filtrowanie, stronicowanie i fasetowanie. W tej metodzie ustawiamy `Filter` `Select`, `OrderBy`, `Top` i właściwość dla różnych zapytań. Wszystkie `SearchParameters` właściwości są udokumentowane [tutaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Następnym krokiem jest faktycznie wykonać kwerendę wyszukiwania. Uruchamianie wyszukiwania odbywa `Documents.Search` się przy użyciu metody. Dla każdej kwerendy przekazujemy tekst wyszukiwania do `"*"` użycia jako ciąg (lub jeśli nie ma tekstu wyszukiwania), plus parametry wyszukiwania utworzone wcześniej. Określamy `Hotel` również jako parametr `Documents.Search`typu , który informuje SDK o deserializacji dokumentów `Hotel`w wynikach wyszukiwania na obiekty typu.

> [!NOTE]
> Więcej informacji na temat składni wyrażenia zapytania wyszukiwania można znaleźć [tutaj](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Na koniec po każdej kwerendzie ta metoda iteruje przez wszystkie dopasowania w wynikach wyszukiwania, drukowanie każdego dokumentu do konsoli:

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

Przyjrzyjmy się bliżej każdej z zapytań po kolei. Oto kod do wykonania pierwszej kwerendy:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

W takim przypadku przeszukujemy cały indeks w poszukiwaniu słowa "motel" w dowolnym polu wyszukiwania i chcemy `Select` tylko pobrać nazwy hoteli, określone przez parametr. Oto wyniki:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Następne zapytanie jest nieco bardziej interesujące.  Chcemy znaleźć wszystkie hotele, które mają pokój z nocną stawką mniejszą niż 100 USD i zwróć tylko hotelowy identyfikator i opis:

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

Ta kwerenda używa wyrażenia `$filter` OData, `Rooms/any(r: r/BaseRate lt 100)`do filtrowania dokumentów w indeksie. Używa to [dowolnego operatora](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) do zastosowania "BaseRate lt 100" do każdego elementu w kolekcji Pokoje. Więcej informacji na temat składni OData, którą obsługuje usługa Azure Cognitive Search, można [znaleźć tutaj.](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)

Oto wyniki kwerendy:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Następnie chcemy znaleźć dwa najlepsze hotele, które zostały niedawno odnowione, i pokazać nazwę hotelu i datę ostatniego remontu. Oto kod: 

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

W takim przypadku ponownie używamy składni OData, aby określić `OrderBy` parametr jako `lastRenovationDate desc`. Ustawiliśmy `Top` również 2, aby upewnić się, że otrzymamy tylko dwa najlepsze dokumenty. Tak jak poprzednio, ustawiamy, `Select` aby określić, które pola mają być zwracane.

Oto wyniki:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Na koniec chcemy znaleźć wszystkie nazwy hoteli, które pasują do słowa "hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

A oto wyniki, które obejmują wszystkie pola, `Select` ponieważ nie określiliśmy właściwości:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Ten krok kończy samouczek, ale nie kończy się tutaj. **Następne kroki zapewniają dodatkowe zasoby, aby dowiedzieć się więcej o usłudze Azure Cognitive Search.

## <a name="next-steps"></a>Następne kroki
* Przeglądaj odwołania do [zestawu SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) oraz [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Przejrzyj [konwencje nazewnictwa,](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) aby poznać reguły nazewnictwa różnych obiektów.
* Przejrzyj [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) w usłudze Azure Cognitive Search.
