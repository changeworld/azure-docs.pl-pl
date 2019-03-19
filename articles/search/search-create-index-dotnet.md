---
title: Tworzenie indeksu za pomocą kodu przy użyciu interfejsu API .NET — Azure Search
description: Dowiedz się, jak utworzyć indeks wyszukiwania pełnotekstowego przy użyciu zestawu SDK .NET usługi Azure Search i przykładowego kodu w języku C#.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f0ac5ee77bf4a479e48bbe048d2a558d78b18fe3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136414"
---
# <a name="quickstart-create-load-and-query-an-azure-search-index-using-the-net-sdk"></a>Szybki start: Tworzenie, obciążenia i tworzenie zapytań względem indeksu usługi Azure Search przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [Postman (interfejs API REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

W tym artykule opisano proces tworzenia, ładowanie i wykonywanie zapytań usługi Azure Search [indeksu](search-what-is-an-index.md) przy użyciu [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk).

> [!NOTE]
> Cały przykładowy kod przedstawiony w tym artykule został napisany w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://aka.ms/search-dotnet-howto). Można również poczytać o [zestawie SDK platformy .NET dla usługi Azure Search](search-howto-dotnet-sdk.md), aby uzyskać bardziej szczegółowe omówienie przykładowego kodu.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Tworzenie usługi Azure Search](search-create-service-portal.md). Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start.

+ [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), w każdej wersji. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

+ Adres URL punktu końcowego i administratora klucz interfejsu api usługi Search. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

    1. W witrynie Azure portal [Znajdź swoją usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

    2. W **Przegląd**, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://my-service-name.search.windows.net`.

    3. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Na żądanie, można użyć zarówno klucz podstawowy lub pomocniczy.

    ![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

    Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="1---create-a-new-project"></a>1 — Tworzenie nowego projektu

W programie Visual Studio, tworzenie nowej wizualizacji C# projektu. Dobre szablonu dla tego przewodnika Szybki Start jest Visual C# > wprowadzenie > Aplikacja sieci Web. Ten szablon zawiera plik appsettings.json.  

W pliku appsettings.json, zastąp domyślną zawartość w poniższym przykładzie, a następnie podaj nazwę usługi i administratora klucz api-key dla Twojej usługi. Dla nazwy usługi wystarczy sama nazwa. Na przykład, jeśli adres URL jest https://mydemo.search.windows.net, Dodaj `mydemo` do pliku JSON.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-an-instance-of-the-searchserviceclient-class"></a>2 — Tworzenie wystąpienia klasy SearchServiceClient
Aby rozpocząć korzystanie z zestawu .NET SDK usługi Azure Search, konieczne jest utworzenie wystąpienia klasy `SearchServiceClient`. Ta klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

Skopiuj poniższy kod do pliku Program.cs. Poniższy kod tworzy nową `SearchServiceClient` za pomocą wartości nazwy usługi wyszukiwania i klucza interfejsu api, które są przechowywane w pliku konfiguracji aplikacji (pliku appsettings.json).

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

## <a name="3---define-an-index-schema"></a>3 - definiowania schematu indeksu
Pojedyncze wywołanie metody `Indexes.Create` spowoduje utworzenie indeksu. Ta metoda przyjmuje jako parametr obiekt `Index`, który definiuje indeks usługi Azure Search. Należy utworzyć obiekt `Index` i zainicjować go w następujący sposób:

1. Ustaw właściwość `Name` obiektu `Index` na nazwę Twojego indeksu.

2. Ustaw właściwość `Fields` obiektu `Index` na tablicę obiektów `Field`. Najłatwiejszym sposobem tworzenia obiektów `Field` jest wywołanie metody `FieldBuilder.BuildForType` z przekazaniem klasy modelu jako parametru typu. Właściwości klasy modelu są mapowane na pola indeksu. Umożliwia to powiązanie dokumentów z indeksu wyszukiwania z wystąpieniami klasy modelu.

> [!NOTE]
> Jeśli nie zamierzasz używać klasy modelu, nadal możesz zdefiniować indeks poprzez bezpośrednie utworzenie obiektów `Field`. Możesz podać nazwę pola konstruktorowi wraz z typem danych (lub analizatorem w przypadku pól ciągów). Możesz również ustawić inne właściwości, takie jak `IsSearchable`, `IsFilterable` itp.
>
>

Ważne, aby podczas projektowania indeksu pamiętać o środowisku wyszukiwania oraz o potrzebach biznesowych użytkownika, ponieważ każdemu polu należy przypisać [odpowiednie właściwości](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Te właściwości kontrolują, które funkcje wyszukiwania (filtrowanie, tworzenie aspektów, sortowanie, wyszukiwanie pełnotekstowe itp.) dotyczą określonych pól. Dla każdej właściwości, która nie zostanie jawnie ustawiona, klasa `Field` domyślnie wyłączy odpowiednią funkcję wyszukiwania.

W naszym przykładzie indeksowi nadaliśmy nazwę „hotels”, a pola zdefiniowaliśmy przy użyciu klasy modelu. Każda właściwość klasy modelu ma atrybuty, które określają związane z wyszukiwaniem zachowania odpowiedniego pola indeksu. Klasa modelu jest zdefiniowana następująco:

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

W powyższej definicji indeksu użyto analizatora języków dla pola `description_fr`, ponieważ jest ono przeznaczone do przechowywania tekstu w języku francuskim. Aby uzyskać więcej informacji o analizatorach języków, zobacz [temat Language support (Obsługa języków)](https://docs.microsoft.com/rest/api/searchservice/Language-support) oraz odpowiadający mu [wpis na blogu](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [!NOTE]
> Domyślnie nazwa każdej właściwości w klasie modelu jest używana jako nazwa odpowiedniego pola w indeksie. Aby zamapować wszystkie nazwy właściwości na nazwy pól w notacji camelCase, oznacz klasę za pomocą atrybutu `SerializePropertyNamesAsCamelCase`. Jeśli chcesz zamapować właściwość na inną nazwę, możesz użyć atrybutu `JsonProperty`, takiego jak powyższa właściwość `DescriptionFr`. Atrybut `JsonProperty` ma pierwszeństwo przed atrybutem `SerializePropertyNamesAsCamelCase`.
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

## <a name="4---create-the-index-on-the-service"></a>4. Tworzenie indeksu w usłudze
Po zainicjowaniu obiektu `Index` możesz utworzyć indeks przez wywołanie metody `Indexes.Create` względem obiektu `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

W przypadku żądania zakończonego powodzeniem metoda zwróci dane normalnie. W przypadku problemu z żądaniem, takiego jak nieprawidłowy parametr, metoda zgłosi wyjątek `CloudException`.

Gdy ukończysz pracę z indeksem i zechcesz go usunąć, po prostu wywołaj metodę `Indexes.Delete` względem obiektu `SearchServiceClient`. Na przykład indeks „hotele” możesz usunąć w następujący sposób:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Przykład kodu przedstawiony w tym artykule używa metod synchronicznych zestawu .NET SDK usługi Azure Search dla uproszczenia. Zalecamy użycie metod asynchronicznych w aplikacjach, aby pozostały skalowalne i szybko reagowały. W powyższych przykładach można było użyć metod `CreateAsync` i `DeleteAsync` zamiast `Create` i `Delete`.
> 
> 


## <a name="next-steps"></a>Kolejne kroki
Po utworzeniu indeksu usługi Azure Search można [przekazać zawartość do indeksu](search-what-is-data-import.md), aby rozpocząć wyszukiwanie danych.

