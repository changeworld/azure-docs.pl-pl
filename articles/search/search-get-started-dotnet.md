---
title: 'C#Szybki Start: Tworzenie, obciążenia i zapytań indeksy przy użyciu zestawu .NET SDK — usługa Azure Search'
description: Wyjaśnia, jak utworzyć indeks, Załaduj dane i uruchamianie zapytań przy użyciu C# i zestawu .NET SDK usługi Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2019
ms.openlocfilehash: a5cbd2036f92c27709d92d0cf415cc9837645fb8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485602"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Szybki start: Tworzenie indeksu usługi Azure Search w C# przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Tworzenie platformy .NET Core C# konsoli aplikacji, który tworzy, ładuje i zapytania indeksu usługi Azure Search przy użyciu programu Visual Studio i [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk). W tym artykule opisano sposób tworzenia aplikacji krok po kroku. Możesz też [pobieranie i uruchamianie kompletna aplikacja](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Kod wersji demonstracyjnej, w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Na potrzeby scenariuszy produkcyjnych zalecamy używanie metod asynchronicznych we własnych aplikacjach, aby pozostały skalowalne i elastyczny. Na przykład można użyć `CreateAsync` i `DeleteAsync` zamiast `Create` i `Delete`.

## <a name="prerequisites"></a>Wymagania wstępne

Następujących usług, narzędzi i danych są używane w tym przewodniku Szybki Start. 

+ [Program Visual Studio](https://visualstudio.microsoft.com/downloads/), w każdej wersji. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

+ Indeks przykładów i dokumenty są zawarte w tym artykule, również w [rozwiązania Visual Studio](https://github.com/Azure-Samples/azure-search-dotnet-samples/quickstart) dla tego przewodnika Szybki Start.

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołań do usługi wymagają adresu URL punktu końcowego i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

   Uzyskaj także klucz zapytania. Jest najlepszym rozwiązaniem do wysyłania żądań zapytań z dostępem tylko do odczytu.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Rozpocznij, otwierając program Visual Studio i tworzenia nowego projektu aplikacji konsoli, która może działać na platformie .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk) składa się z kilku bibliotek klienckich, które są dystrybuowane jako pakiety NuGet.

Dla tego projektu, należy użyć wersji 9 `Microsoft.Azure.Search` NuGet pakietu i najnowsze `Microsoft.Extensions.Configuration.Json` pakietu NuGet.

1. W **narzędzia** > **Menedżera pakietów NuGet**, wybierz opcję **Zarządzaj pakietami NuGet dla rozwiązania...** . 

1. Kliknij pozycję **Browse (Przeglądaj)** .

1. Wyszukaj `Microsoft.Azure.Search` i wybierz wersję 9.0.1 lub nowszej.

1. Kliknij przycisk **zainstalować** po prawej stronie, aby dodać zestaw do projektu i rozwiązania.

1. Powtórz tę procedurę dla `Microsoft.Extensions.Configuration.Json`, wybierz wersję 2.2.0 lub nowszej.


### <a name="add-azure-search-service-information"></a>Dodaj informacje o usłudze Azure Search

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Dodaj** > **nowy element...**  . 

1. Dodaj nowy element wyszukiwanie "JSON" powrócić do listy związane z formatu JSON, typów elementów.

1. Wybierz **plik JSON**plikowi nazwę "appsettings.json" i kliknij przycisk **Dodaj**. 

1. Dodaj plik do katalogu wyjściowego. Kliknij prawym przyciskiem myszy pliku appsettings.json i wybierz **właściwości**. W **Kopiuj do katalogu wyjściowego**, wybierz opcję **Kopiuj Jeśli nowszy**.

1. Skopiuj następujące dane JSON do nowego pliku JSON. Zamień nazwy usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i klucz administratora interfejsu API (YOUR-ADMIN-API-KEY) prawidłowych wartości. Jeśli do punktu końcowego usługi jest `https://mydemo.search.windows.net`, nazwa usługi będzie "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Dodaj klasę ". Metoda"pliki do projektu

Podczas drukowania wyniki w oknie konsoli, poszczególne pola z obiektu hotelu musi zostać zwrócone jako ciągi. Możesz zaimplementować [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) do wykonania tego zadania kopiowania niezbędny kod do dwa nowe pliki.

1. Dodaj dwie definicje pustą klasę do projektu: Address.Methods.cs, Hotel.Methods.cs

1. W Address.Methods.cs, należy zastąpić domyślną zawartość następującym kodem [linii 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. W Hotel.Methods.cs, skopiuj [linii 1 66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1\. Tworzenie indeksu

Indeksu hotels składa się z pól proste i złożone, gdzie jest prostym polem, "HotelName" lub "Description", i złożone pola adres z pól podrzędnych lub zbiór pokoje. Jeśli indeks zawiera typy złożone, odizoluj definicje pól złożone w osobnych klas.

1. Dodaj dwie definicje pustą klasę do projektu: Address.CS, Hotel.cs

1. W Address.cs należy zastąpić domyślną zawartość następującym kodem:

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

1. W pliku Hotel.cs klasa określa ogólną strukturę wskaźnika, w tym odwołania do klasy adresów.

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

    Atrybuty pola określają, jak jest używana w aplikacji. Na przykład `IsSearchable` atrybut jest przypisany do każdego pola, które powinny zostać uwzględnione w wyszukiwanie pełnotekstowe. W zestawie SDK platformy .NET wartość domyślna to wyłączyć zachowania pola, które nie są jawnie włączone.

    Dokładnie jedno pole w indeksie typu `string` musi być *klucz* pola, który unikatowo identyfikuje każdy dokument. W tym schemacie klucz jest `HotelId`.

    W ten indeks pola Opis, użyj właściwości opcjonalne analizatora, określić, gdy chcesz zastąpić domyślny standardowy analizator Lucene. `description_fr` Pole używa analizator Lucene francuski ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)), ponieważ przechowuje tekstu w języku francuskim. `description` Używa opcjonalne analizatora języka firmy Microsoft ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. W pliku Program.cs, Utwórz wystąpienie obiektu [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) klasy, aby nawiązać połączenie z usługą przy użyciu wartości, które są przechowywane w pliku konfiguracji aplikacji (pliku appsettings.json). 

   `SearchServiceClient` ma [ `Indexes` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) właściwość, zapewniając wszystkie metody, należy utworzyć, listy, aktualizowania lub usuwania indeksów usługi Azure Search. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
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

    Jeśli to możliwe, udostępnić pojedyncze wystąpienie `SearchServiceClient` w aplikacji w celu uniknięcia otwarcia zbyt wiele połączeń. Metody klasy są odporne na wątki umożliwienie tego rodzaju udostępnianie.

   Klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

    W definicji indeksu, najprostszym sposobem utworzenia `Field` obiektów jest wywołanie `FieldBuilder.BuildForType` metody przekazaniem klasy modelu jako parametru typu. Właściwości klasy modelu są mapowane na pola indeksu. To mapowanie umożliwia powiązanie dokumentów z indeksu wyszukiwania z wystąpieniami klasy modelu.

    > [!NOTE]
    > Jeśli nie zamierzasz używać klasy modelu, nadal możesz zdefiniować indeks poprzez bezpośrednie utworzenie obiektów `Field`. Możesz podać nazwę pola konstruktorowi wraz z typem danych (lub analizatorem w przypadku pól ciągów). Można również ustawić inne właściwości, takie jak `IsSearchable`, `IsFilterable`, kilka.
    >

1. Naciśnij klawisz F5, aby skompilować aplikację i utworzyć indeks. 

    Jeśli projekt zostanie skompilowany poprawnie, zostanie otwarte okno konsoli zapisywania komunikatów o stanie do ekranu w celu usunięcia i utworzenia indeksu. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

W usłudze Azure Search dokumenty są struktur danych, które są zarówno dane wejściowe do indeksowania i dane wyjściowe z zapytań. Uzyskany z zewnętrznego źródła danych, dane wejściowe dokument może być wierszy w bazie danych obiektów blob w magazynie obiektów Blob lub dokumenty JSON na dysku. W tym przykładzie firma Microsoft biorąc skrótu i osadzanie dokumentów JSON dla 4 hoteli w sam kod. 

Podczas przekazywania dokumentów, musisz użyć [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) obiektu. `IndexBatch` Zawiera zbiór [ `IndexAction` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) obiektów, z których każdy zawiera dokument i właściwość z informacją o usługi Azure Search, jaką akcję wykonać ([przekazywania, scalanie, usuwania i mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. W pliku Program.cs, utworzyć tablicę, dokumentów i akcji indeksu, a następnie przekazać tablicę na `IndexBatch`. Poniższe dokumenty zgodne z indeksem hotelu — Szybki Start, zgodnie z definicją klasy hotelu i adres.

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

    Po inicjowania`IndexBatch` obiektu i wysłać do indeksu przez wywołanie metody [ `Documents.Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) na Twoje [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) obiektu. `Documents` jest właściwością `SearchIndexClient` , zawiera metody służące do dodawania, modyfikowania lub wykonywanie zapytań względem dokumentów w indeksie.

    `try` / `catch` Otaczający wywołanie `Index` połowy metody indeksowania błędów, które może się zdarzyć, jeśli usługa jest mocno obciążony. W kodzie produkcyjnym można opóźnić, a następnie ponowić indeksowanie dokumentów, dla których nie powiodła się, zaloguj się i kontynuować, takie jak przykładowa lub go obsłużyć w inny sposób, który spełnia wymagania dotyczące spójności danych aplikacji.

    2-sekundowe opóźnienie kompensuje indeksowania, który jest asynchroniczna, tak aby wszystkie dokumenty mogą być indeksowane, przed wykonaniem zapytania. Generowanie kodu z opóźnieniem jest zazwyczaj konieczne tylko wtedy w pokazów, testów i przykładowych aplikacji.

1. W pliku Program.cs w głównym oknie Usuń komentarz wiersze "2 - ładowanie dokumentów". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Naciśnij klawisz F5, aby ponownie kompilować aplikacji. 

    Jeśli projekt zostanie skompilowany poprawnie, zostanie otwarte okno konsoli zapisywania komunikatów o stanie, tym razem z komunikat dotyczący przekazywania dokumentów. W witrynie Azure portal w usłudze wyszukiwania **Przegląd** strony indeksu hotels — Szybki Start teraz dostępne są 4 dokumenty.

Aby uzyskać więcej informacji na temat przetwarzania dokumentu, zobacz ["Jak zestaw .NET SDK obsługuje dokumenty"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wyniki zapytania można uzyskać, jak tylko pierwszy dokument jest indeksowana, ale rzeczywistych testów indeksu powinna poczekaj, aż wszystkie dokumenty są indeksowane. 

W tej sekcji dodaje dwa elementy funkcjonalności: logiki zapytania i wyniki. Dla zapytań, należy użyć [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metody. Ta metoda przyjmuje tekst wyszukiwania, a także innych [parametry](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

[ `DocumentsSearchResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) Klasa reprezentuje wyniki.


1. W pliku Program.cs należy utworzyć metodę WriteDocuments czy drukuje wyniki wyszukiwania w celu konsoli.

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

1. Utwórz metodę RunQueries do wykonywania zapytań i zwracają wartości. Wyniki są hotelu obiektów. Zaznacz parametr służy do powierzchni poszczególnych pól. Jeśli pole nie jest uwzględniony w parametrze select, jego odpowiadającą właściwość hotelu będzie mieć wartości null.

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

    Istnieją dwa [sposobów pasujące terminy w zapytaniu](search-query-overview.md#types-of-queries): wyszukiwanie pełnotekstowe i filtry. Zapytanie wyszukiwania pełnotekstowego wyszukuje jeden lub więcej warunków w `IsSearchable` pól w indeksie. Filtr jest wyrażenie logiczne, które jest obliczana z uwzględnieniem `IsFilterable` pól w indeksie. Można użyć wyszukiwania pełnotekstowego i filtry, razem lub oddzielnie.

    Zarówno operacja wyszukiwania, jak i filtrowania są wykonywane przy użyciu metody `Documents.Search`. Zapytanie wyszukiwania może zostać przekazane za pośrednictwem parametru `searchText`, natomiast wyrażenie filtrowania może zostać przekazane za pośrednictwem właściwości `Filter` klasy `SearchParameters`. Aby filtrować bez wyszukiwania, po prostu przekaż wartość `"*"` jako parametr `searchText`. Aby wyszukiwać bez filtrowania, pozostaw nieustawioną właściwość `Filter` lub nie przekazuj jej w wystąpieniu obiektu `SearchParameters`.

1. W pliku Program.cs w głównym oknie Usuń komentarz wiersze dla "3 - Search". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. To rozwiązanie jest już ukończona. Naciśnij klawisz F5, aby ponownie skompilować aplikację i uruchomić program w całości. 

    Dane wyjściowe obejmują tymi samymi komunikatami jako przed, dodając informacje o kwerendzie i wyników.

## <a name="clean-up"></a>Czyszczenie

Gdy ukończysz pracę z indeksem i zechcesz go usunąć, należy wywołać `Indexes.Delete` metody w swojej `SearchServiceClient`.

```csharp
serviceClient.Indexes.Delete("hotels");
```

Jeśli jesteś także zakończyło się z usługą wyszukiwania, możesz usunąć zasoby z witryny Azure portal.

## <a name="next-steps"></a>Kolejne kroki

W tym C# Szybki Start, nad którą były prowadzone przez szereg zadań tworzenia indeksu, Załaduj do niej dokumenty i uruchamiać zapytania. Na różnych etapach, Skorzystaliśmy skróty w celu uproszczenia kodu dla czytelności i zrozumienie. Jeśli masz doświadczenia z podstawowych pojęć, firma Microsoft zaleca następnego artykułu na potrzeby eksploracji alternatywnych metod i pojęcia, które będą pogłębić swoją wiedzę. 

Przykładowy kod i indeksu są rozwinięte wersje tego. Następny przykład dodaje kolekcji pokoje, korzysta z różnych klas i działań i Trwa dokładniej poznać sposób działania przetwarzania.

> [!div class="nextstepaction"]
> [Jak tworzyć aplikacje na platformie .NET](search-howto-dotnet-sdk.md)