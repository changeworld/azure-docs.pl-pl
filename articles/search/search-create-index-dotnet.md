---
title: Tworzenie indeksu (interfejs API .NET — usługa Azure Search) | Microsoft Docs
description: Tworzenie indeksu za pomocą kodu przy użyciu zestawu .NET SDK usługi Azure Search.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7e7d1f8110d8470fe7596633563529f397c5551e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794756"
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Tworzenie indeksu usługi Azure Search przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Omówienie](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Ten artykuł przeprowadzi Cię przez proces tworzenia [indeksu](https://docs.microsoft.com/rest/api/searchservice/Create-Index) usługi Azure Search przy użyciu [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk).

Przed rozpoczęciem pracy z przewodnikiem oraz przed utworzeniem indeksu powinna zostać [utworzona usługa Azure Search](search-create-service-portal.md).

> [!NOTE]
> Cały przykładowy kod przedstawiony w tym artykule został napisany w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](http://aka.ms/search-dotnet-howto). Można również poczytać o [zestawie SDK platformy .NET dla usługi Azure Search](search-howto-dotnet-sdk.md), aby uzyskać bardziej szczegółowe omówienie przykładowego kodu.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identyfikowanie klucza api-key administratora usługi Azure Search
Po aprowizowaniu usługi Azure Search wszystko jest już prawie gotowe, aby móc wysyłać żądania do punktu końcowego usługi za pomocą zestawu .NET SDK. Najpierw należy uzyskać klucz api-key administratora, który został wygenerowany dla aprowizowanej usługi wyszukiwania. Zestaw .NET SDK przesyła ten klucz przy każdorazowym wysłaniu żądania do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

1. Aby znaleźć klucze api-key dla usługi, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/)
2. Przejdź do bloku usługi Azure Search
3. Kliknij ikonę „Klucze”

Usługa będzie dysponować *kluczami administratora* i *kluczami zapytań*.

* Za pomocą podstawowego i pomocniczego *klucza administratora* przyznawane są pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych. Dostępne są dwa klucze, dzięki czemu możesz nadal używać klucza pomocniczego, jeśli zdecydujesz się na ponowne wygenerowanie klucza podstawowego, i na odwrót.
* *Klucze zapytań* umożliwiają dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.

Podczas tworzenia indeksu można użyć zarówno podstawowego, jak i pomocniczego klucza administratora.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Tworzenie wystąpienia klasy SearchServiceClient
Aby rozpocząć korzystanie z zestawu .NET SDK usługi Azure Search, konieczne jest utworzenie wystąpienia klasy `SearchServiceClient`. Ta klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

Poniższy kod tworzy nową klasę `SearchServiceClient` przy użyciu wartości dla nazwy usługi wyszukiwania i klucza api-key, które są przechowywane w pliku konfiguracji aplikacji (`appsettings.json` w przypadku [przykładowej aplikacji](http://aka.ms/search-dotnet-howto)):

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

## <a name="define-your-azure-search-index"></a>Definiowanie indeksu usługi Azure Search
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

## <a name="create-the-index"></a>Tworzenie indeksu
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

## <a name="next-steps"></a>Następne kroki
Po utworzeniu indeksu usługi Azure Search można [przekazać zawartość do indeksu](search-what-is-data-import.md), aby rozpocząć wyszukiwanie danych.

