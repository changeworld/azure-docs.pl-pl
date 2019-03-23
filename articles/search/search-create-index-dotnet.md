---
title: Tworzenie indeksu w C# — usługa Azure Search
description: Dowiedz się, jak utworzyć indeks wyszukiwanie pełnotekstowe w C# przy użyciu zestawu .NET SDK usługi Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/22/2019
ms.openlocfilehash: a5861faaf26962d34d1c356e29dce1be40f8716b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370588"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Szybki start: 1 — Tworzenie indeksu usługi Azure Search wC#

Ten artykuł przeprowadzi Cię przez proces tworzenia [indeksu usługi Azure Search](search-what-is-an-index.md) przy użyciu C# i [zestawu .NET SDK](https://aka.ms/search-sdk). To jest pierwszą lekcją ćwiczenie 3 części do tworzenia, ładowania i zapytania indeksu. Tworzenie indeksu odbywa się przez wykonanie następujących zadań:

> [!div class="checklist"]
> * Tworzenie [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) obiekt, aby nawiązać połączenie z usługą wyszukiwania.
> * Tworzenie [ `Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) obiekt można przekazać jako parametru, aby `Indexes.Create`.
> * Wywołaj `Indexes.Create` metody `SearchServiceClient` wysyłać `Index` do usługi.

## <a name="prerequisites"></a>Wymagania wstępne

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start.

[Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), w każdej wersji. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

Pobierz adres URL punktu końcowego i administratora klucz interfejsu api usługi search. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

  1. W witrynie Azure portal w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

  2. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

  ![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="1---open-the-project"></a>1 — Otwórz projekt

Pobierz przykładowy kod [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) z usługi GitHub. 

W pliku appsettings.json, zastąp domyślną zawartość w poniższym przykładzie, a następnie podaj nazwę usługi i administratora klucz api-key dla Twojej usługi. Dla nazwy usługi wystarczy sama nazwa. Na przykład, jeśli adres URL jest https://mydemo.search.windows.net, Dodaj `mydemo` do pliku JSON.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

Po ustawieniu tych wartości możesz F5 kompilacji rozwiązania, aby uruchomić aplikację konsoli. Pozostałe kroki w tym ćwiczeniu oraz te, które należy wykonać to omówienie sposobu działania tego kodu. 

Alternatywnie, możesz zapoznać się z [jak używać usługi Azure Search z poziomu aplikacji .NET ](search-howto-dotnet-sdk.md) bardziej szczegółowe pokrycia zachowania zestawu SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 — Tworzenie klienta

Aby rozpocząć korzystanie z zestawu .NET SDK usługi Azure Search, Utwórz wystąpienie obiektu `SearchServiceClient` klasy. Ta klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

Poniższy kod, można znaleźć w pliku Program.cs. Tworzy nową `SearchServiceClient` za pomocą wartości nazwy usługi wyszukiwania i klucza interfejsu api, które są przechowywane w pliku konfiguracji aplikacji (pliku appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` ma właściwość `Indexes`. Ta właściwość udostępnia wszystkie metody, które są potrzebne do tworzenia, wyświetlania list, aktualizowania lub usuwania indeksów usługi Azure Search.

> [!NOTE]
> Klasa `SearchServiceClient` zarządza połączeniami z usługą wyszukiwania. W celu uniknięcia otwarcia zbyt wielu połączeń, należy, w miarę możliwości, udostępnić pojedyncze wystąpienie klasy `SearchServiceClient` w aplikacji. Metody tej klasy są bezpieczne wątkowo, co pozwala na tego rodzaju udostępnianie.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 — konstrukcja indeksu
Pojedyncze wywołanie `Indexes.Create` metoda tworzy indeks. Ta metoda przyjmuje jako parametr `Index` obiekt, który definiuje indeks usługi Azure Search. Utwórz `Index` obiektu i zainicjować go w następujący sposób:

1. Ustaw właściwość `Name` obiektu `Index` na nazwę Twojego indeksu.

2. Ustaw właściwość `Fields` obiektu `Index` na tablicę obiektów `Field`. Najłatwiejszym sposobem tworzenia obiektów `Field` jest wywołanie metody `FieldBuilder.BuildForType` z przekazaniem klasy modelu jako parametru typu. Właściwości klasy modelu są mapowane na pola indeksu. Umożliwia to powiązanie dokumentów z indeksu wyszukiwania z wystąpieniami klasy modelu.

> [!NOTE]
> Jeśli nie zamierzasz używać klasy modelu, nadal możesz zdefiniować indeks poprzez bezpośrednie utworzenie obiektów `Field`. Możesz podać nazwę pola konstruktorowi wraz z typem danych (lub analizatorem w przypadku pól ciągów). Można również ustawić inne właściwości, takie jak `IsSearchable`, `IsFilterable`, kilka.
>
>

Należy zachować swoje wyszukiwania użytkownika oraz o potrzebach biznesowych na uwadze podczas projektowania indeksu. Każde pole należy przypisać [atrybuty](https://docs.microsoft.com/rest/api/searchservice/Create-Index) , kontrolują, które wyszukiwania funkcji (filtrowanie, tworzenie aspektów, sortowanie i tak dalej) dotyczą określonych pól. Dla każdej właściwości, która nie zostanie jawnie ustawiona, klasa `Field` domyślnie wyłączy odpowiednią funkcję wyszukiwania.

W tym przykładzie nazwa indeksu jest "hotels" i pola są definiowane przy użyciu klasy modelu. Każda właściwość klasy modelu ma atrybuty, które określają związane z wyszukiwaniem zachowania odpowiedniego pola indeksu. Klasa modelu jest zdefiniowana następująco:

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

Dla każdej właściwości starannie wybraliśmy atrybuty, kierując się tym, jak naszym zdaniem będą używane w aplikacji. Na przykład istnieje duże prawdopodobieństwo, że osoby szukające hoteli będzie interesować dopasowanie słów kluczowych w polu `description`, dlatego włączyliśmy wyszukiwanie pełnotekstowe dla tego pola przez dodanie atrybutu `IsSearchable` do właściwości `Description`.

Zwróć uwagę, że dokładnie jedno pole typu `string` w Twoim indeksie musi być wyznaczone jako pole *klucza* przez dodanie atrybutu `Key` (zobacz właściwość `HotelId` w powyższym przykładzie).

W powyższej definicji indeksu użyto analizatora języków dla pola `description_fr`, ponieważ jest ono przeznaczone do przechowywania tekstu w języku francuskim. Aby uzyskać więcej informacji, zobacz [dodać analizatory języka do indeksu usługi Azure Search](index-add-language-analyzers.md).

> [!NOTE]
> Domyślnie nazwa każdej właściwości w klasie modelu odnosi się do nazwy pola w indeksie. Aby zamapować wszystkie nazwy właściwości na nazwy pól w notacji camelCase, oznacz klasę za pomocą atrybutu `SerializePropertyNamesAsCamelCase`. Jeśli chcesz zamapować właściwość na inną nazwę, możesz użyć atrybutu `JsonProperty`, takiego jak powyższa właściwość `DescriptionFr`. Atrybut `JsonProperty` ma pierwszeństwo przed atrybutem `SerializePropertyNamesAsCamelCase`.
> 
> 

Zdefiniowaliśmy już klasę modelu, dlatego z łatwością możemy utworzyć definicję indeksu:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 — Indexes.Create wywołanie
Teraz, gdy masz zainicjowane `Index` obiektów, utworzyć indeks przez wywołanie metody `Indexes.Create` na Twoje `SearchServiceClient` obiektu:

```csharp
serviceClient.Indexes.Create(definition);
```

W przypadku żądania zakończonego powodzeniem metoda zwróci dane normalnie. W przypadku problemu z żądaniem, takiego jak nieprawidłowy parametr, metoda zgłosi wyjątek `CloudException`.

Gdy ukończysz pracę z indeksem i zechcesz go usunąć, należy wywołać `Indexes.Delete` metody w swojej `SearchServiceClient`. Na przykład:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Przykład kodu przedstawiony w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. W powyższych przykładach można było użyć metod `CreateAsync` i `DeleteAsync` zamiast `Create` i `Delete`.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki Start utworzono pusty indeksu usługi Azure Search na podstawie schematu, który definiuje typy danych pola i zachowania. Indeks jest indeksem "bare kości" składający się z nazwy i kolekcji pól opartego na atrybutach. Bardziej realistycznego indeksu będzie obejmować inne elementy, takie jak [profile oceniania](index-add-scoring-profiles.md), [sugestory](index-add-suggesters.md) do obsługi typeahead [synonimy](search-synonyms.md)i ewentualnie [ niestandardowe analizatory](index-add-custom-analyzers.md). Firma Microsoft zaleca ponowne przeanalizowanie tych funkcji po zidentyfikowaniu podstawowy przepływ pracy.

Kolejnym przewodniku Szybki Start w tej serii opisano, jak załadować indeks z możliwością wyszukiwania zawartości.

> [!div class="nextstepaction"]
> [Ładowanie danych do indeksu usługi Azure Search przy użyciuC#](search-import-data-dotnet.md)