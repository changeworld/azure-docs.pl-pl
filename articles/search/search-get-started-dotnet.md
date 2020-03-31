---
title: 'Szybki start: tworzenie indeksu wyszukiwania w języku C# przy użyciu platformy .NET'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start języka C# dowiedz się, jak utworzyć indeks, załadować dane i uruchomić kwerendy przy użyciu narzędzia Azure Cognitive Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589219"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w języku C# przy użyciu sdk .NET
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Utwórz aplikację konsoli .NET Core C#, która tworzy, ładuje i wysyła kwerendy do indeksu usługi Azure Cognitive Search przy użyciu programu Visual Studio i [narzędzia Azure Cognitive Search .NET SDK.](https://aka.ms/search-sdk) W tym artykule wyjaśniono, jak tworzyć aplikację krok po kroku. Alternatywnie, można [pobrać i uruchomić pełną aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!NOTE]
> Kod demonstracyjny w tym artykule używa synchronicznych metod azure cognitive search .NET SDK dla uproszczenia. Jednak w scenariuszach produkcyjnych zaleca się przy użyciu metod asynchronicznych we własnych aplikacjach, aby zachować ich skalowalne i elastyczne. Na przykład można `CreateAsync` użyć `DeleteAsync` i `Create` zamiast `Delete`i .

## <a name="prerequisites"></a>Wymagania wstępne

Do tego przewodnika Szybki start wymagane są następujące usługi i narzędzia.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), dowolna wersja. Przykładowy kod i instrukcje zostały przetestowane w bezpłatnym wydaniu wspólnotowym.

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu przy każdym żądaniu. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

   Pobierz klucz zapytania, jak również. Jest najlepszym rozwiązaniem do wystawiania żądań zapytań z dostępem tylko do odczytu.

![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza api przy każdym żądaniu wysłanym do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Rozpocznij od otwarcia programu Visual Studio i utworzenia nowego projektu aplikacji konsoli, który można uruchomić w programie .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

Zestaw [SDK usługi Azure Cognitive Search .NET](https://aka.ms/search-sdk) składa się z kilku bibliotek klienckich, które są dystrybuowane jako pakiety NuGet.

W tym projekcie należy użyć `Microsoft.Azure.Search` wersji 9 pakietu `Microsoft.Extensions.Configuration.Json` NuGet i najnowszego pakietu NuGet.

1. W **menedżerze pakietów Narzędzia** > **NuGet**wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania...**. 

1. Kliknij pozycję **Browse (Przeglądaj)**.

1. Wyszukaj `Microsoft.Azure.Search` i wybierz wersję 9.0.1 lub nowszą.

1. Kliknij **przycisk Zainstaluj** po prawej stronie, aby dodać zestaw do projektu i rozwiązania.

1. Powtórz `Microsoft.Extensions.Configuration.Json`tę czynność , wybierając wersję 2.2.0 lub nowszą.


### <a name="add-azure-cognitive-search-service-information"></a>Dodawanie informacji o usłudze Azure Cognitive Search

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj** > **nowy element...** . 

1. W polu Dodaj nowy element wyszukaj hasło "JSON", aby zwrócić listę typów elementów związanych z JSON.

1. Wybierz **polecenie JSON File**, nazwij plik "appsettings.json" i kliknij przycisk **Dodaj**. 

1. Dodaj plik do katalogu wyjściowego. Kliknij prawym przyciskiem myszy plik appsettings.json i wybierz polecenie **Właściwości**. W **obszarze Kopiuj do katalogu wyjściowego**wybierz pozycję **Kopiuj, jeśli nowsza**.

1. Skopiuj następujący plik JSON do nowego pliku JSON. Zastąp nazwę usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i klucz interfejsu API administratora (YOUR-ADMIN-API-KEY) prawidłowymi wartościami. Jeśli punktem końcowym `https://mydemo.search.windows.net`usługi jest , nazwa usługi będzie "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Dodaj klasę ". Metoda" plików do projektu

Podczas drukowania wyników w oknie konsoli poszczególne pola z obiektu Hotel muszą być zwracane jako ciągi. Aby wykonać to zadanie, można zaimplementować [ToString(),](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) kopiując niezbędny kod do dwóch nowych plików.

1. Dodaj do projektu dwie puste definicje klas: Address.Methods.cs Hotel.Methods.cs

1. W Address.Methods.cs zastąp domyślną zawartość następującym kodem, [wiersze 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. W Hotel.Methods.cs skopiuj [wiersze 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - Tworzenie indeksu

Indeks hoteli składa się z prostych i złożonych pól, gdzie prostym polem jest "HotelName" lub "Opis", a pola złożone są adresem z podmaliami lub kolekcją pokoi. Gdy indeks zawiera typy złożone, izoluj definicje złożonych pól w oddzielnych klasach.

1. Dodaj do projektu dwie puste definicje klas: Address.cs Hotel.cs

1. W Address.cs zastąp domyślną zawartość następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. W Hotel.cs klasa definiuje ogólną strukturę indeksu, w tym odwołania do klasy adresu.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Atrybuty w polu określają sposób jego użycia w aplikacji. Na przykład `IsSearchable` atrybut musi być przypisany do każdego pola, które powinno zostać uwzględnione w wyszukiwaniu pełnotekstowym. 
    
    > [!NOTE]
    > W sdk .NET pola muszą być [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)jawnie przypisywane jako , [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet), i [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). To zachowanie jest w przeciwieństwie do interfejsu API REST, który niejawnie umożliwia atrybucję na podstawie typu danych (na przykład proste pola ciągów są automatycznie przeszukiwalne).

    Dokładnie jedno pole w indeksie typu `string` musi być *polem klucza,* jednoznacznie identyfikującym każdy dokument. W tym schemacie `HotelId`kluczem jest .

    W tym indeksie pola opisu używają właściwości opcjonalnej, [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) określonej, gdy chcesz zastąpić domyślny standardowy analizator Lucene. Pole `description_fr` jest za pomocą francuskiego analizatora Lucene ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)), ponieważ przechowuje tekst francuski. Używa `description` opcjonalnego analizatora języka firmy Microsoft ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. W Program.cs utwórz wystąpienie [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) klasy, aby połączyć się z usługą, przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json). 

   `SearchServiceClient`ma [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) właściwość, podając wszystkie metody potrzebne do tworzenia, listy, aktualizacji lub usuwania indeksów usługi Azure Cognitive Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Jeśli to możliwe, udostępnij jedno wystąpienie `SearchServiceClient` w aplikacji, aby uniknąć otwierania zbyt wielu połączeń. Metody klasy są bezpieczne dla wątków, aby włączyć takie udostępnianie.

   Klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

    W definicji indeksu najprostszym sposobem `Field` utworzenia obiektów `FieldBuilder.BuildForType` jest wywołanie metody, przekazywanie klasy modelu dla parametru typu. Właściwości klasy modelu są mapowane na pola indeksu. To mapowanie umożliwia powiązanie dokumentów z indeksu wyszukiwania do wystąpień klasy modelu.

    > [!NOTE]
    > Jeśli nie zamierzasz używać klasy modelu, nadal możesz zdefiniować indeks poprzez bezpośrednie utworzenie obiektów `Field`. Możesz podać nazwę pola konstruktorowi wraz z typem danych (lub analizatorem w przypadku pól ciągów). Można również ustawić inne `IsSearchable`właściwości, takie jak , `IsFilterable`aby wymienić tylko kilka.
    >

1. Naciśnij klawisz F5, aby utworzyć aplikację i utworzyć indeks. 

    Jeśli projekt tworzy pomyślnie, zostanie otwarte okno konsoli, zapisując komunikaty o stanie na ekranie w celu usunięcia i utworzenia indeksu. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Ładowanie dokumentów

W usłudze Azure Cognitive Search dokumenty są strukturami danych, które są zarówno dane wejściowe do indeksowania i dane wyjściowe z zapytań. Uzyskane z zewnętrznego źródła danych dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami blob w magazynie obiektów Blob lub dokumentami JSON na dysku. W tym przykładzie bierzemy skrót i osadzamy dokumenty JSON dla czterech hoteli w samym kodzie. 

Podczas przesyłania dokumentów należy użyć [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) obiektu. Zawiera `IndexBatch` kolekcję [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) obiektów, z których każdy zawiera dokument i właściwość informującą usługę Azure Cognitive Search, jaką akcję wykonać[(przekazywanie, scalanie, usuwanie i scalanieOrUpload).](search-what-is-data-import.md#indexing-actions)

1. W Program.cs utwórz tablicę dokumentów i akcji indeksu, a `IndexBatch`następnie przekaż tablicę do programu . Poniższe dokumenty są zgodne z indeksem szybkiej oferty szybkiej startu, zdefiniowanym przez hotel i zajęcia adresowe.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Po zainicjowaniu`IndexBatch` obiektu można wysłać go do [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) indeksu, wywołując obiekt. [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) `Documents`jest `SearchIndexClient` właściwością, która zawiera metody dodawania, modyfikowania, usuwania lub wykonywania zapytań o dokumenty w indeksie.

    `try` / `catch` Otaczające wywołanie `Index` metody połowy błędów indeksowania, co może się zdarzyć, jeśli usługa jest pod dużym obciążeniem. W kodzie produkcyjnym można opóźnić, a następnie ponowić próbę indeksowania dokumentów, które nie powiodły się, lub zalogować się i kontynuować tak jak w przykładzie lub obsłużyć je w inny sposób, który spełnia wymagania dotyczące spójności danych aplikacji.

    Opóźnienie 2-sekundowe kompensuje indeksowanie, które jest asynchroniczne, dzięki czemu wszystkie dokumenty mogą być indeksowane przed wykonaniem kwerend. Kodowanie z opóźnieniem jest zazwyczaj konieczne tylko w pokazach, testach i przykładowych aplikacjach.

1. W Program.cs, w głównym, odkomentuj wiersze dla "2 - Załaduj dokumenty". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Naciśnij klawisz F5, aby odbudować aplikację. 

    Jeśli projekt tworzy pomyślnie, zostanie otwarte okno konsoli, pisanie komunikatów o stanie, tym razem z komunikatem o przekazywaniu dokumentów. W witrynie Azure portal na stronie **Przegląd** usługi wyszukiwania indeks szybki start hoteli powinien mieć teraz 4 dokumenty.

Aby uzyskać więcej informacji na temat przetwarzania dokumentów, zobacz ["Jak sdk .NET obsługuje dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Można uzyskać wyniki kwerendy, jak tylko pierwszy dokument jest indeksowany, ale rzeczywiste testowanie indeksu należy poczekać, aż wszystkie dokumenty są indeksowane. 

W tej sekcji dodano dwa elementy funkcjonalności: logikę kwerendy i wyniki. W przypadku kwerend [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) należy użyć metody. Ta metoda przyjmuje tekst wyszukiwania, a także inne [parametry](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

Klasa [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) reprezentuje wyniki.


1. W Program.cs utwórz metodę WriteDocuments, która drukuje wyniki wyszukiwania na konsoli.

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

1. Utwórz RunQueries metody do wykonywania zapytań i zwraca wyniki. Wyniki są obiekty hotelowe. Można użyć parametru select, aby powierzchnię poszczególnych pól. Jeśli pole nie jest uwzględnione w parametrze select, jego odpowiedni obiekt hotelowy będzie miał wartość null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Istnieją dwa [sposoby dopasowywania terminów w zapytaniu:](search-query-overview.md#types-of-queries)wyszukiwanie pełnotekstowe i filtry. Pełnotekstowe zapytanie wyszukiwania wyszukuje jeden lub więcej terminów w `IsSearchable` polach w indeksie. Filtr jest wyrażeniem logicznym, `IsFilterable` które jest oceniane przez pola w indeksie. Wyszukiwanie pełnotekstowe i filtry można używać razem lub osobno.

    Zarówno operacja wyszukiwania, jak i filtrowania są wykonywane przy użyciu metody `Documents.Search`. Zapytanie wyszukiwania może zostać przekazane za pośrednictwem parametru `searchText`, natomiast wyrażenie filtrowania może zostać przekazane za pośrednictwem właściwości `Filter` klasy `SearchParameters`. Aby filtrować bez wyszukiwania, po prostu przekaż wartość `"*"` jako parametr `searchText`. Aby wyszukiwać bez filtrowania, pozostaw nieustawioną właściwość `Filter` lub nie przekazuj jej w wystąpieniu obiektu `SearchParameters`.

1. W Program.cs, w głównej, odkomentuj wiersze "3 - Szukaj". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Rozwiązanie zostało zakończone. Naciśnij klawisz F5, aby odbudować aplikację i uruchomić program w całości. 

    Dane wyjściowe zawiera te same komunikaty jak poprzednio, z dodatkiem informacji o kwerendzie i wyników.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start języka C# przepracowano za pomocą serii zadań, aby utworzyć indeks, załadować go z dokumentami i uruchomić kwerendy. Na różnych etapach, zrobiliśmy skróty, aby uprościć kod dla czytelności i zrozumienia. Jeśli masz komfort z podstawowych pojęć, zalecamy następny artykuł do zbadania alternatywnych podejść i koncepcji, które pogłębią swoją wiedzę. 

Przykładowy kod i indeks są rozszerzone wersje tego. Następny przykład dodaje Rooms kolekcji, używa różnych klas i akcji i ma bliżej przyjrzeć się, jak działa przetwarzania.

> [!div class="nextstepaction"]
> [Jak rozwijać się w .NET](search-howto-dotnet-sdk.md)
