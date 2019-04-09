---
title: Wykonywanie zapytań dotyczących danych do indeksu usługi Azure Search w C# (.NET SDK) — usługa Azure Search
description: Przykładowy kod C# do tworzenia zapytań wyszukiwania w usłudze Azure Search. Dodawanie parametrów wyszukiwania w celu filtrowania i sortowania wyników wyszukiwania.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264951"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Szybki start: 3 — tworzenie zapytań względem indeksu usługi Azure Search wC#

W tym artykule dowiesz się, jak wykonywać zapytania [indeksu usługi Azure Search](search-what-is-an-index.md) przy użyciu C# i [zestawu .NET SDK](https://aka.ms/search-sdk). Wyszukiwanie dokumentów w indeksie odbywa się przez wykonanie następujących zadań:

> [!div class="checklist"]
> * Tworzenie [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) obiekt, aby nawiązać połączenie z indeksu wyszukiwania z uprawnieniami tylko do odczytu.
> * Tworzenie [ `SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) obiekt zawierający definicję wyszukiwania lub filtru.
> * Wywołaj `Documents.Search` metody `SearchIndexClient` do wysyłania zapytań do indeksu.

## <a name="prerequisites"></a>Wymagania wstępne

[Ładowanie indeksu usługi Azure Search](search-import-data-dotnet.md) z przykładowymi danymi hotels.

Uzyskaj klucz zapytania używane dla dostępu tylko do odczytu do dokumentów. Do tej pory użyto klucza administratora interfejsu API, aby mogli tworzyć obiektów i zawartości, które znajdują się w usłudze wyszukiwania. Jednak do obsługi zapytań w aplikacjach, zdecydowanie zaleca się używanie klucza zapytania. Aby uzyskać instrukcje, zobacz [Utwórz klucz zapytania](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Tworzenie klienta
Utwórz wystąpienie obiektu `SearchIndexClient` klasy tak, aby można mu klucza zapytania, aby uzyskać dostęp tylko do odczytu (w przeciwieństwie do praw dostępu do zapisu powierzonych `SearchServiceClient` używane w poprzedniej lekcji).

Ta klasa ma kilka konstruktorów. Odpowiedni przyjmuje Twojej nazwy usługi wyszukiwania, nazwę indeksu i [ `SearchCredentials` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) obiektu jako parametry. `SearchCredentials` opakowuje klucz api-key.

Poniższy kod tworzy nową `SearchIndexClient` dla indeksu "hotels" za pomocą wartości nazwy usługi wyszukiwania i klucza interfejsu api, które są przechowywane w pliku konfiguracji aplikacji (`appsettings.json` w przypadku właściwości [Przykładowa aplikacja](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` ma [ `Documents` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) właściwości. Ta właściwość zawiera wszystkie metody, które są potrzebne do tworzenie zapytań względem indeksów usługi Azure Search.

## <a name="construct-searchparameters"></a>Konstrukcja obiektu SearchParameters
Wyszukiwanie za pomocą zestawu .NET SDK jest równie proste co wywołanie metody `Documents.Search` klasy `SearchIndexClient`. Ta metoda przyjmuje kilka parametrów, łącznie z tekstem wyszukiwania oraz obiektem `SearchParameters`, który może służyć do uściślenia zapytania.

### <a name="types-of-queries"></a>Typy zapytań
Dwa najczęściej używane [typy zapytań](search-query-overview.md#types-of-queries) to `search` i `filter`. Zapytanie `search` umożliwia wyszukanie jednego lub większej liczby terminów we wszystkich polach *z możliwością wyszukiwania* w indeksie. Zapytanie `filter` ocenia wyrażenie logiczne w odniesieniu do wszystkich pól *z możliwością filtrowania* w indeksie. Wyszukiwań i filtrów można używać razem lub oddzielnie.

Zarówno operacja wyszukiwania, jak i filtrowania są wykonywane przy użyciu metody `Documents.Search`. Zapytanie wyszukiwania może zostać przekazane za pośrednictwem parametru `searchText`, natomiast wyrażenie filtrowania może zostać przekazane za pośrednictwem właściwości `Filter` klasy `SearchParameters`. Aby filtrować bez wyszukiwania, po prostu przekaż wartość `"*"` jako parametr `searchText`. Aby wyszukiwać bez filtrowania, pozostaw nieustawioną właściwość `Filter` lub nie przekazuj jej w wystąpieniu obiektu `SearchParameters`.

### <a name="example-queries"></a>Przykładowe zapytania
Następujący przykładowy kod zawiera kilka różnych sposobów tworzenie zapytań względem indeksu "hotels" zdefiniowany w [Tworzenie indeksu usługi Azure Search w C# ](search-create-index-dotnet.md#DefineIndex). Należy pamiętać, że dokumenty zwrócone w wynikach wyszukiwania są wystąpieniami `Hotel` klasy, która została zdefiniowana w [importowanie danych do indeksu usługi Azure Search w C# ](search-import-data-dotnet.md#construct-indexbatch). W przykładowym kodzie użyto metody `WriteDocuments`, aby przekazać wyniki wyszukiwania do konsoli. Ta metoda została opisana w następnej sekcji.

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

Poniżej przedstawiono wygląd wyniki zapytań z poprzedniej sekcji przy założeniu, że indeks "hotels" jest wypełniana z przykładowymi danymi:

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

W powyższym przykładowym kodzie użyto konsoli do wyświetlenia wyników wyszukiwania. W podobny sposób musisz wyświetlić wyniki wyszukiwania w swojej aplikacji. Na przykład jak wyświetlić wyniki wyszukiwania w aplikacji sieci web opartych na programie ASP.NET MVC, zobacz [projektu DotNetSample](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) w witrynie GitHub.

## <a name="next-steps"></a>Kolejne kroki

Jeśli użytkownik jeszcze tego nie zrobiono, zapoznaj się z przykładowego kodu w [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) w serwisie GitHub, wraz z [jak używać usługi Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md) szczegółowe opisy przykładowy kod. 