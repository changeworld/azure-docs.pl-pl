---
title: Jak używać usługi Azure Search z aplikacji .NET | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure Search z aplikacji .NET
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: e8a492a0786281bdc1d7c2123a7188c32a124e13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Jak używać usługi Azure Search z aplikacji .NET
Ten artykuł zawiera wskazówki ułatwiające rozpoczęcie działa z [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk). Zestaw .NET SDK umożliwia wdrożenie zaawansowane wyszukiwanie w aplikacji przy użyciu usługi Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Co to jest Azure wyszukiwania zestawu SDK
Zestaw SDK składa się z kilku bibliotek klienta, które umożliwiają zarządzanie z indeksów, źródła danych, indeksatorów i synonim mapy, a także przekazywania i zarządzania dokumentami i wykonywać zapytania, bez konieczności postępowania w przypadku szczegóły HTTP i JSON. Te biblioteki klienta są dystrybuowane jako pakietów NuGet.

Główny pakiet NuGet jest `Microsoft.Azure.Search`, który jest meta pakiet, który zawiera wszystkie pakiety jako zależności. Użyj tego pakietu, jeśli użytkownik dopiero rozpoczynasz pracę lub jeśli wiadomo, że aplikacja musi mieć wszystkie funkcje usługi Azure Search.

Inne pakiety NuGet w zestawie SDK to:
 
  - `Microsoft.Azure.Search.Data`: Użyj tego pakietu, jeśli projektujesz aplikację .NET przy użyciu usługi Azure Search i należy do zapytań, lub zaktualizować dokumentów w Twojej indeksów. Jeśli należy utworzyć lub zaktualizować indeksów, użyj mapy synonimu lub innych zasobów poziomu usług, `Microsoft.Azure.Search` zamiast tego pakietu.
  - `Microsoft.Azure.Search.Service`: Użyj tego pakietu, jeśli projektujesz automatyzacji w .NET do zarządzania indeksów usługi Azure Search, mapy synonim, indeksatorów, źródła danych lub innych zasobów na poziomie usługi. Jeśli wymagane jest tylko do dokumentów zapytania lub aktualizacji w Twojej indeksów, użyj `Microsoft.Azure.Search.Data` zamiast tego pakietu. Wszystkie funkcje usługi Azure Search, należy użyć `Microsoft.Azure.Search` zamiast tego pakietu.
  - `Microsoft.Azure.Search.Common`: Wymagane przez bibliotek usługi Azure Search .NET popularnych typów. Nie należy do tego pakietu można użyć bezpośrednio w aplikacji; Jest przeznaczone tylko do użycia jako zależność.

Różne bibliotek klienckich Definiowanie klas takich jak `Index`, `Field`, i `Document`, jak również operacji, takich jak `Indexes.Create` i `Documents.Search` na `SearchServiceClient` i `SearchIndexClient` klasy. Te klasy są podzielone na następujące obszary nazw:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Bieżąca wersja zestawu .NET SDK usługi Azure Search teraz jest ogólnie dostępna. Jeśli chcesz przekazać opinię firmie Microsoft w celu uwzględnienia w następnej wersji, skontaktuj się z odwiedziny naszych [strony](https://feedback.azure.com/forums/263029-azure-search/).

Zestaw .NET SDK obsługuje wersję `2017-11-11` z [interfejsu API REST wyszukiwania Azure](https://docs.microsoft.com/rest/api/searchservice/). Ta wersja zawiera teraz obsługę synonimy, a także ulepszeń przyrostowych, indeksatorów. Funkcji, które są w wersji zapoznawczej *nie* są częścią tej wersji, takie jak Obsługa indeksowania plików CSV i tablic JSON w [Podgląd](search-api-2016-09-01-preview.md) i dostępne za pośrednictwem [zestawu.NETSDKwwersji4.0-preview](https://aka.ms/search-sdk-preview).

Zestaw SDK nie obsługuje [operacji zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) takich jak tworzenie i skalowanie usługi wyszukiwania i zarządzanie nimi klucze interfejsu API. Jeśli trzeba zarządzać zasobami wyszukiwania z aplikacji .NET, możesz użyć [zestawu SDK usługi Azure Search .NET zarządzania](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uaktualnianie do najnowszej wersji zestawu SDK
Jeśli już używasz starszej wersji zestawu .NET SDK usługi Azure Search i chcesz uaktualnić do nowej wersji ogólnie dostępna, [w tym artykule](search-dotnet-sdk-migration-version-5.md) wyjaśniono sposób.

## <a name="requirements-for-the-sdk"></a>Wymagania dotyczące zestawu SDK
1. Program Visual Studio 2017.
2. Własne usługi Azure Search. Aby korzystać z zestawu SDK, należy uzyskać nazwę usługi i co najmniej jeden klucz interfejsu API. [Tworzenie usługi w portalu](search-create-service-portal.md) pomoże Ci tych kroków.
3. Pobierz zestaw .NET SDK usługi Azure Search [pakietu NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) za pomocą "Manage NuGet Packages" programu Visual Studio. Po prostu wyszukać nazwę pakietu `Microsoft.Azure.Search` NuGet.org (lub jednego z innych pakietu nazwy powyżej, jeśli wymagane jest tylko podzbiór funkcji).

Zestaw .NET SDK usługi Azure Search obsługuje aplikacji przeznaczonych dla platformy .NET Framework 4.5.2 lub nowszej, a także .NET Core.

## <a name="core-scenarios"></a>Podstawowe scenariusze
Istnieje kilka kwestii, które należy wykonać w aplikacji wyszukiwania. W tym samouczku omówione zostaną następujące czynności te podstawowe scenariusze:

* Tworzenie indeksu
* Podczas wypełniania indeksu z dokumentami
* Wyszukiwanie dokumentów za pomocą wyszukiwania pełnotekstowego oraz filtry

Przykładowy kod, który jest zgodny ilustruje każdą z nich. Możesz także użyć wstawki kodu w aplikacji.

### <a name="overview"></a>Przegląd
Tworzy nową aplikację przykładową, firma Microsoft będzie można badać indeksu o nazwie "hotels" wypełnia kilku dokumentów, a następnie wykonuje kilka zapytań wyszukiwania. Oto główne programu, przedstawiający ogólny przepływ:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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

Zostanie omówiony ten krok po kroku. Najpierw należy utworzyć nowy `SearchServiceClient`. Ten obiekt umożliwia zarządzanie indeksów. Aby można było utworzyć jedną, musisz podać nazwę usługi Azure Search, a także klucz interfejsu API administratora. Możesz wprowadzić te informacje w `appsettings.json` pliku [Przykładowa aplikacja](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Jeśli podasz nieprawidłowy klucz (na przykład klucz zapytania gdy był wymagany klucz administratora), `SearchServiceClient` zgłosi `CloudException` z powodu błędu komunikatu "Dostęp zabroniony" po raz pierwszy metoda jest wywoływana operacji, takich jak `Indexes.Create`. W takim przypadku do Ciebie należy dokładnie sprawdzić naszych klucz interfejsu API.
> 
> 

Następnych kilku wierszy wywołania metody, aby utworzyć indeks o nazwie "hotels", usuwając go najpierw, jeśli już istnieje. Firma Microsoft przeprowadzi tych metod nieco później.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Następnie indeks musi być wypełnione. Aby to zrobić, firma Microsoft będzie `SearchIndexClient`. Istnieją dwa sposoby uzyskania: tworząc go lub poprzez wywołanie `Indexes.GetClient` na `SearchServiceClient`. Używamy jego dla wygody.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> W typowej aplikacji wyszukującej wypełnianie indeksu i zarządzanie nim jest obsługiwane przez inny składnik niż zapytania wyszukiwania. `Indexes.GetClient` jest wygodną metodą wypełniania indeksu, ponieważ nie wymaga dostarczenia kolejnej klasy `SearchCredentials`. Dzieje się tak dzięki przekazaniu klucza administratora, który został użyty w celu utworzenia klasy `SearchServiceClient` do nowej klasy `SearchIndexClient`. Jednak w tej części aplikacji, która wykonuje zapytania, zaleca się utworzenie klasy `SearchIndexClient` bezpośrednio, dzięki czemu można przekazać klucz zapytania zamiast klucza administratora. To jest zgodna z zasadą najniższych uprawnień i pomoże zapewnić bardziej bezpieczne aplikacji. Można dowiedzieć się więcej o kluczach administratora i kluczach zapytań [tutaj](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Teraz, gdy mamy `SearchIndexClient`, firma Microsoft może wypełnić indeksu. Jest to realizowane przy użyciu innej metody, które firma Microsoft przeprowadzi później.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Ponadto możemy wykonać kilka zapytań wyszukiwania i wyświetlić wyniki. Teraz możemy użyć innego `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Firma Microsoft podejmie bliższe spojrzenie na `RunQueries` metody później. Oto kod, aby utworzyć nowy `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Teraz możemy użyć klucza zapytania, ponieważ firma Microsoft nie musi uzyskać dostęp do indeksu. Możesz wprowadzić te informacje w `appsettings.json` pliku [Przykładowa aplikacja](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Po uruchomieniu tej aplikacji z prawidłową nazwę usługi i klucze interfejsu API, dane wyjściowe powinny wyglądać następująco:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
    Search the entire index for the term 'budget' and return only the hotelName field:
    
    Name: Roach Motel
    
    Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:
    
    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river
    
    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:
    
    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
    
    Search the entire index for the term 'motel':
    
    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
    
    Complete.  Press any key to end application...

Kodu źródłowego aplikacji znajduje się na końcu tego artykułu.

Następnie będzie trwać bliższe spojrzenie na każdej z metod wywoływane przez `Main`.

### <a name="creating-an-index"></a>Tworzenie indeksu
Po utworzeniu `SearchServiceClient`, co dalej `Main` jest jest usuwania indeksu "hotels", jeśli już istnieje. Który odbywa się następującymi metodami:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Ta metoda używa danej `SearchServiceClient` do sprawdzenia, jeśli istnieje indeks, a jeśli tak, usuń go.

> [!NOTE]
> Przykład kodu przedstawiony w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. Na przykład w powyższej metodzie możesz użyć `ExistsAsync` i `DeleteAsync` zamiast `Exists` i `Delete`.
> 
> 

Następnie `Main` powoduje utworzenie nowego indeksu "hotels" przez wywołanie tej metody:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Ta metoda tworzy nowy `Index` obiektu z listy `Field` obiektów, które definiuje schemat nowego indeksu. Każde pole ma nazwę, typ danych i kilka atrybutów, które określają zachowanie wyszukiwania. `FieldBuilder` Klasy używa odbicia w celu utworzenia listy `Field` obiektów dla indeksu przez sprawdzenie właściwości publiczne i atrybuty danego `Hotel` klasa modelu. Przeniesiemy bliższe spojrzenie na `Hotel` klasy później.

> [!NOTE]
> Zawsze można utworzyć listę `Field` obiektów bezpośrednio zamiast `FieldBuilder` w razie potrzeby. Na przykład nie można używać klasy modelu lub może być konieczne użycie istniejącej klasy modelu, który chcesz zmodyfikować przez dodanie atrybutów.
>
> 

Oprócz pól można również dodać do indeksu (te zostaną pominięte próbkę do skrócenia) oceniania profile, sugestorów lub opcji CORS. Można znaleźć więcej informacji o obiekcie indeksu i jego elementów składowych w [odwołania do zestawu SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), oraz w [dokumentacji interfejsu API REST wyszukiwania Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Podczas wypełniania indeksu
Następny krok `Main` jest do wypełniania indeksu nowo utworzona. Można to zrobić w następującą metodę:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
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
        },
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
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

Ta metoda ma cztery części. Pierwszy tworzy tablicę `Hotel` obiektów, które będzie służyć jako danych wejściowych do przekazania do indeksu. Te dane są stałe dla uproszczenia. W aplikacji danych prawdopodobnie będzie pochodził z zewnętrznego źródła danych takie jak bazy danych SQL.

Druga część tworzy `IndexBatch` zawierającego dokumenty. Określ działanie, które chcesz zastosować do wykonywania zadania wsadowego w czasie tworzenia, w tym przypadku przez wywołanie metody `IndexBatch.Upload`. Partii są następnie przekazywane do indeksu usługi Azure Search przez `Documents.Index` metody.

> [!NOTE]
> W tym przykładzie mamy tylko przekazywania dokumentów. Jeśli chcesz scalić zmian w istniejących dokumentów lub usuwanie dokumentów, możesz utworzyć partie przez wywołanie metody `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, lub `IndexBatch.Delete` zamiast tego. Możesz łączyć różnych operacji w pojedynczej partii przez wywołanie metody `IndexBatch.New`, który przyjmuje Kolekcja `IndexAction` obiektów, które informują usługę Azure Search do wykonywania określonej operacji w dokumencie. Można utworzyć każdego `IndexAction` z działaniem przez wywołanie metody odpowiedniej metody, takie jak `IndexAction.Merge`, `IndexAction.Upload`i tak dalej.
> 
> 

Trzeci część tej metody jest blok catch obsługuje ważny przypadek błędu indeksowania. Jeśli usługa Azure Search nie może zindeksować niektórych dokumentów w partii, metoda `Documents.Index` zgłasza wyjątek `IndexBatchException`. Może to nastąpić, jeśli indeksujesz dokumenty w czasie, gdy usługa jest mocno obciążona. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

> [!NOTE]
> Można użyć `FindFailedActionsToRetry` metodę, aby utworzyć nową instancję zawierający tylko akcje, których nie powiodła się podczas poprzedniego wywołania `Index`. Metoda jest udokumentowany [tutaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) i znajduje się omówienie sposobu jego używania prawidłowo [w witrynie StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Na koniec `UploadDocuments` umieszczono dwusekundowe opóźnienie metody. Indeksowanie w usłudze Azure Search jest asynchroniczne, więc przykładowa aplikacja musi czekać przez krótki czas, aby upewnić się, że dokumenty można wyszukiwać. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

#### <a name="how-the-net-sdk-handles-documents"></a>Jak zestaw .NET SDK obsługuje dokumenty
Możesz się zastanawiać, jak zestaw .NET SDK usługi Azure Search jest w stanie przekazać wystąpienia klasy zdefiniowanej przez użytkownika, np. `Hotel`, do indeksu. Aby odpowiedzieć na to pytanie, Przyjrzyjmy się `Hotel` klasy:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
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
}
```

Przede wszystkim należy zauważyć, że każda właściwość publiczna klasy `Hotel` odpowiada polu w definicji indeksu, ale z jedną istotną różnicą: nazwa każdego pola rozpoczyna się małą literą (camelCase), podczas gdy nazwa każdej właściwości publicznej klasy `Hotel` rozpoczyna się wielką literą (PascalCase). Jest to częsty przypadek w aplikacjach platformy .NET, które tworzą powiązania danych, gdzie schemat docelowy jest poza kontrolą dewelopera aplikacji. Zamiast naruszać zasady nazewnictwa platformy .NET przez używanie notacji camelCase dla nazw właściwości, za pomocą atrybutu `[SerializePropertyNamesAsCamelCase]` można określić, że zestaw SDK ma mapować nazwy właściwości na notację camelCase automatycznie.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search korzysta z biblioteki [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) w celu serializacji i deserializacji niestandardowych obiektów modelu do i z formatu JSON. W razie potrzeby możesz dostosować serializację. Aby uzyskać więcej informacji, zobacz [niestandardowe serializacji z struktury JSON.NET](#JsonDotNet).
> 
> 

Drugi wszystkim należy zauważyć są atrybuty, takie jak `IsFilterable`, `IsSearchable`, `Key`, i `Analyzer` który dekoracji każda właściwość publiczna. Te atrybuty bezpośrednio do mapy [odpowiednie atrybuty indeksu usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/create-index#request). `FieldBuilder` Klasy wykorzystuje te do skonstruowania definicje pól dla indeksu.

Trzeci ważną kwestią dotyczącą `Hotel` klasy są typy danych właściwości publicznych. Typy .NET tych właściwości są mapowane na odpowiadające im typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `Edm.String`. Podobne mapowania typów występują między typami `bool?` i `Edm.Boolean`, `DateTimeOffset?` i `Edm.DateTimeOffset` itp. Dokładne zasady mapowania typów znajdują się w dokumentacji metody `Documents.Get` w [dokumentacji zestawu .NET SDK usługi Azure Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). `FieldBuilder` Klasy zajmuje się to mapowanie dla Ciebie, ale nadal można zrozumieć, w razie potrzeby można rozwiązać wszelkie napotkane problemy serializacji.

Ta możliwość używania własnych klas jako dokumentów działa w obu kierunkach; Można również pobrać wyniki wyszukiwania i mieć automatycznie je na typ wybranym deserializacji zestawu SDK, które zostanie wyświetlone w następnej sekcji.

> [!NOTE]
> Zestaw .NET SDK usługi Azure Search obsługuje również dynamiczne typowanie dokumentów za pomocą klasy `Document`, która stanowi mapowanie klucz/wartość nazw pól na wartości pól. Jest to przydatne, jeśli nie znasz schematu indeksu w czasie projektowania lub jeśli powiązanie z określonymi klasami modelu jest niedogodne. Wszystkie metody w zestawie SDK, które obsługują dokumenty, mają przeciążenia działające z klasą `Document`, a także przeciążenia o silnych typach przyjmujące parametr typu ogólnego. Tylko te ostatnie są używane w przykładowym kodzie w tym samouczku. `Document` Klasa dziedziczy `Dictionary<string, object>`. Inne szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Dlaczego należy używać typów danych dopuszczających wartość null**

Jeśli projektujesz własne klasy modeli na potrzeby mapowania na indeks usługi Azure Search, zalecamy deklarowanie właściwości typów wartości, takich jak `bool` i `int`, aby dopuszczały możliwość użycia wartości null (na przykład `bool?` zamiast `bool`). W przypadku użycia właściwości niedopuszczającej wartości null musisz **zagwarantować**, że żaden dokument w indeksie nie zawiera wartości null w odpowiednim polu. Ani zestaw SDK, ani usługa Azure Search nie pomoże Ci tego wymusić.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Z tego powodu najlepszym i zalecanym rozwiązaniem jest używanie w klasach modeli typów dopuszczających wartość null.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Niestandardowej serializacji z struktury JSON.NET
Zestaw SDK używa struktury JSON.NET do serializacji i deserializacji dokumentów. Można dostosować serializacji i deserializacji w razie potrzeby, definiując własne `JsonConverter` lub `IContractResolver` (zobacz [dokumentacji struktury JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) więcej szczegółów). Może to być przydatne, jeśli chcesz dostosować istniejącej klasy modelu z aplikacji do użycia z usługi Azure Search i innych bardziej zaawansowanych scenariuszy. Na przykład za pomocą niestandardowej serializacji można:

* Uwzględnić lub wykluczyć pewne właściwości klasy modelu są przechowywane jako pól dokumentu.
* Mapowania nazw właściwości w kodzie i nazw pól w indeksie.
* Tworzenie atrybutów niestandardowych, które mogą być używane do mapowania właściwości pól dokumentu.

Można znaleźć przykłady stosowania niestandardowej serializacji w testy jednostek dla zestawu SDK .NET wyszukiwanie Azure w serwisie GitHub. Dobry punkt wyjścia jest [ten folder](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Zawiera klasy, które są używane przez testy niestandardowej serializacji.

### <a name="searching-for-documents-in-the-index"></a>Wyszukiwanie dokumentów w indeksie
Ostatnim krokiem w przykładowej aplikacji jest do wyszukiwania niektórych dokumentów w indeksie. Następująca metoda robi to:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "hotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("budget", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "lastRenovationDate desc" },
            Select = new[] { "hotelName", "lastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'motel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);
}
```

Każdy czasu wykonywania zapytania, najpierw ta metoda tworzy nowy `SearchParameters` obiektu. Umożliwia określenie dodatkowych opcji zapytania, takie jak sortowanie, filtrowanie, stronicowania i tworzenie aspektów. W przypadku tej metody jest firma Microsoft ustawienie `Filter`, `Select`, `OrderBy`, i `Top` właściwości dla różnych zapytań. Wszystkie `SearchParameters` właściwości są udokumentowane [tutaj](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Następnym krokiem jest rzeczywiście wykonać kwerendy wyszukiwania. Jest to realizowane przy użyciu `Documents.Search` metody. Dla każdego zapytania jest przekazywana szukany tekst do użycia jako ciąg (lub `"*"` , jeśli nie ma żadnego tekstu wyszukiwania), oraz parametry wyszukiwania utworzony wcześniej. Również określić `Hotel` jako parametr typu dla `Documents.Search`, który określa, że zestaw SDK, aby deserializować dokumentów w wynikach wyszukiwania w obiektach typu `Hotel`.

> [!NOTE]
> Można znaleźć więcej informacji na temat składni wyrażeń zapytania wyszukiwania [tutaj](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Na koniec po każdym zapytaniem tej metody iteruje wszystkie dopasowania w wynikach wyszukiwania, drukowania każdego dokumentu do konsoli:

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

Z kolei wytłumaczone bliższe spojrzenie na każdym z zapytania. Oto kod do wykonania pierwszej kwerendy:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

W takim przypadku możemy Cię szukające hoteli, zgodne słowo "budget" i chcemy, aby odzyskać tylko nazwy hoteli, określony przez `Select` parametru. Poniżej przedstawiono wyniki:

    Name: Roach Motel

Następnie chcemy znaleźć hotele co noc szybkość wynosi mniej niż 150 USD i zwróć tylko identyfikator hoteli i opis:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

To zapytanie używa OData `$filter` wyrażenie `baseRate lt 150`, aby filtrować dokumenty w indeksie. Można dowiedzieć się więcej o składni OData, który obsługuje usługę Azure Search [tutaj](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Poniżej przedstawiono wyniki zapytania:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Następnie chcemy się znaleźć górny dwóch hotele, które zostały ostatnio renovated i zawierają nazwę hoteli i datę ostatniej odnawianie. Oto kod: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

W takim przypadku ponownie używamy składni OData do określenia `OrderBy` parametr jako `lastRenovationDate desc`. Możemy również ustawić `Top` 2, aby upewnić się, uzyskujemy tylko dokumenty dwóch pierwszych. Jak wcześniej, możemy ustawić `Select` do określenia pola, które ma zostać zwrócony.

Poniżej przedstawiono wyniki:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Wreszcie chcemy mieć Znajdź wszystkie hotele, zgodne słowo "motel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

A Oto wyniki, które obejmują wszystkie pola, ponieważ firma Microsoft nie określono `Select` właściwości:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Ten krok jest wykonywany samouczek, ale nie zatrzymuje tutaj. **Następne kroki** przedstawiono dodatkowe zasoby, aby uzyskać więcej informacji na temat usługi Azure Search.

## <a name="next-steps"></a>Kolejne kroki
* Przeglądaj odwołania do [zestawu SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) oraz [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Pogłębić swoją wiedzę za pośrednictwem [wideo i inne przykłady i samouczki](search-video-demo-tutorial-list.md).
* Przegląd [konwencje nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) Aby dowiedzieć się reguły nazewnictwa różnych obiektów.
* Przegląd [obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) w usłudze Azure Search.
