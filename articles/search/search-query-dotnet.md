---
title: Wysyłanie zapytań względem indeksu za pomocą kodu przy użyciu zestawu SDK platformy .NET — Azure Search
description: Przykładowy kod C# do tworzenia zapytań wyszukiwania w usłudze Azure Search. Dodawanie parametrów wyszukiwania w celu filtrowania i sortowania wyników wyszukiwania.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/19/2017
ms.custom: seodec2018
ms.openlocfilehash: 5c89902da5e773c60c8e2694159ddeed874ecab2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317002"
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Tworzenie zapytań względem indeksu usługi Azure Search przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Omówienie](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

W tym artykule opisano tworzenie zapytań względem indeksu przy użyciu [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk).

Przed rozpoczęciem pracy z tym przewodnikiem należy [utworzyć indeks usługi Azure Search](search-what-is-an-index.md) i [wypełnić go danymi](search-what-is-data-import.md).

> [!NOTE]
> Cały przykładowy kod przedstawiony w tym artykule został napisany w języku C#. Pełny kod źródłowy można znaleźć [w usłudze GitHub](https://aka.ms/search-dotnet-howto). Można również poczytać o [zestawie SDK platformy .NET dla usługi Azure Search](search-howto-dotnet-sdk.md), aby uzyskać bardziej szczegółowe omówienie przykładowego kodu.

## <a name="identify-your-azure-search-services-query-api-key"></a>Identyfikowanie klucza api-key zapytania usługi Azure Search
Po utworzeniu indeksu usługi Azure Search wszystko jest już prawie gotowe do wysyłania zapytań przy użyciu zestawu .NET SDK. Najpierw musisz uzyskać jeden z kluczy api-key zapytania, który został wygenerowany dla aprowizowanej usługi wyszukiwania. Zestaw .NET SDK przesyła ten klucz przy każdorazowym wysłaniu żądania do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

1. Aby znaleźć klucze api-key dla usługi, możesz zalogować się w witrynie [Azure Portal](https://portal.azure.com/)
2. Przejdź do bloku usługi Azure Search
3. Kliknij ikonę „Klucze”

Usługa będzie dysponować *kluczami administratora* i *kluczami zapytań*.

* Za pomocą podstawowego i pomocniczego *klucza administratora* przyznawane są pełne prawa do wszystkich operacji, łącznie z możliwością zarządzania usługą oraz tworzenia i usuwania indeksów, indeksatorów i źródeł danych. Dostępne są dwa klucze, dzięki czemu możesz nadal używać klucza pomocniczego, jeśli zdecydujesz się na ponowne wygenerowanie klucza podstawowego, i na odwrót.
* *Klucze zapytań* umożliwiają dostęp tylko do odczytu do indeksów oraz dokumentów i są zazwyczaj dystrybuowane do aplikacji klienckich, które wysyłają żądania wyszukiwania.

W celu tworzenia zapytań względem indeksu można użyć dowolnego klucza zapytania. Do tworzenia zapytań można również używać kluczy administratora, ale w kodzie aplikacji należy używać klucza zapytania, ponieważ takie podejście jest bardziej zgodne z [zasadą najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Tworzenie wystąpienia klasy SearchIndexClient
Aby wysyłać zapytania przy użyciu zestawu .NET SDK usługi Azure Search, konieczne jest utworzenie wystąpienia klasy `SearchIndexClient`. Ta klasa ma kilka konstruktorów. Ten, który nas interesuje, przyjmuje jako parametry nazwę usługi wyszukiwania, nazwę indeksu i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

Poniższy kod tworzy nową klasę `SearchIndexClient` dla indeksu „hotels” (utworzonego w ramach instrukcji zawartych w temacie [Create an Azure Search index using the .NET SDK](search-create-index-dotnet.md) [Tworzenie indeksu usługi Azure Search przy użyciu zestawu .NET SDK]) za pomocą wartości nazwy usługi wyszukiwania i klucza api-key, które są przechowywane w pliku konfiguracji aplikacji (`appsettings.json` w przypadku [przykładowej aplikacji](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` ma właściwość `Documents`. Ta właściwość zawiera wszystkie metody, które są potrzebne do tworzenie zapytań względem indeksów usługi Azure Search.

## <a name="query-your-index"></a>Tworzenie zapytań względem indeksu
Wyszukiwanie za pomocą zestawu .NET SDK jest równie proste co wywołanie metody `Documents.Search` klasy `SearchIndexClient`. Ta metoda przyjmuje kilka parametrów, łącznie z tekstem wyszukiwania oraz obiektem `SearchParameters`, który może służyć do uściślenia zapytania.

#### <a name="types-of-queries"></a>Typy zapytań
Dwa najczęściej używane [typy zapytań](search-query-overview.md#types-of-queries) to `search` i `filter`. Zapytanie `search` umożliwia wyszukanie jednego lub większej liczby terminów we wszystkich polach *z możliwością wyszukiwania* w indeksie. Zapytanie `filter` ocenia wyrażenie logiczne w odniesieniu do wszystkich pól *z możliwością filtrowania* w indeksie. Wyszukiwań i filtrów można używać razem lub oddzielnie.

Zarówno operacja wyszukiwania, jak i filtrowania są wykonywane przy użyciu metody `Documents.Search`. Zapytanie wyszukiwania może zostać przekazane za pośrednictwem parametru `searchText`, natomiast wyrażenie filtrowania może zostać przekazane za pośrednictwem właściwości `Filter` klasy `SearchParameters`. Aby filtrować bez wyszukiwania, po prostu przekaż wartość `"*"` jako parametr `searchText`. Aby wyszukiwać bez filtrowania, pozostaw nieustawioną właściwość `Filter` lub nie przekazuj jej w wystąpieniu obiektu `SearchParameters`.

#### <a name="example-queries"></a>Przykładowe zapytania
W poniższym przykładowym kodzie przedstawiono inne sposoby tworzenia zapytań względem indeksu „hotels” zdefiniowanego w ramach instrukcji zawartych w artykule [Create an Azure Search index using the .NET SDK](search-create-index-dotnet.md#DefineIndex) (Tworzenie indeksu usługi Azure Search przy użyciu zestawu .NET SDK). Zwróć uwagę na to, że dokumenty zwrócone w wynikach wyszukiwania są wystąpieniami klasy `Hotel`, która została zdefiniowana w ramach instrukcji zawartych w artykule [Data Import in Azure Search using the .NET SDK](search-import-data-dotnet.md#HotelClass) (Importowanie danych do usługi Azure Search przy użyciu zestawu .NET SDK). W przykładowym kodzie użyto metody `WriteDocuments`, aby przekazać wyniki wyszukiwania do konsoli. Ta metoda została opisana w następnej sekcji.

```csharp
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
```

## <a name="handle-search-results"></a>Obsługa wyników wyszukiwania
Metoda `Documents.Search` zwraca obiekt `DocumentSearchResult` zawierający wyniki zapytania. W poprzedniej sekcji użyto metody o nazwie `WriteDocuments` w celu przekazania wyników wyszukiwania do konsoli:

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

Poniżej przedstawiono wyniki zapytań z poprzedniej sekcji przy założeniu, że indeks „hotels” został wypełniony przykładowymi danymi w ramach instrukcji zawartych w temacie [Data Import in Azure Search using .NET SDK](search-import-data-dotnet.md) (Importowanie danych w usłudze Azure Search przy użyciu zestawu .NET SDK):

```
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
```

W powyższym przykładowym kodzie użyto konsoli do wyświetlenia wyników wyszukiwania. W podobny sposób musisz wyświetlić wyniki wyszukiwania w swojej aplikacji. Zapoznaj się z [tym przykładem w witrynie GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample), aby zobaczyć, jak wyświetlić wyniki wyszukiwania w aplikacji internetowej opartej na platformie ASP.NET MVC.

