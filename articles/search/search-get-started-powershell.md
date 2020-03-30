---
title: 'Szybki start: tworzenie indeksu wyszukiwania w programie PowerShell przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym interfejsie Szybki start interfejsu API REST dowiedz się, jak utworzyć indeks, załadować dane i uruchomić kwerendy przy użyciu programu PowerShell Invoke-RestMethod i interfejsu API REST usługi Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 612751c2405cd55ad0b3760aa8e093e434a22f57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121606"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w programie PowerShell przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Program PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Listonosz (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

W tym artykule obieg procesu tworzenia, ładowania i wykonywania zapytań o indeks usługi Azure Cognitive Search przy użyciu programów PowerShell i [interfejsów API REST usługi Azure Cognitive Search.](https://docs.microsoft.com/rest/api/searchservice/) W tym artykule wyjaśniono, jak interaktywnie uruchamiać polecenia programu PowerShell. Alternatywnie można [pobrać i uruchomić skrypt programu Powershell,](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) który wykonuje te same operacje.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do tego przewodnika Szybki start wymagane są następujące usługi i narzędzia. 

+ [Program PowerShell 5.1 lub nowszy](https://github.com/PowerShell/PowerShell), używając [invoke-restMetod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) dla kroków sekwencyjnych i interaktywnych.

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu. 

## <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza api przy każdym żądaniu wysłanym do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Łączenie się z usługą Azure Cognitive Search

1. W programie PowerShell utwórz **obiekt $headers** do przechowywania typu zawartości i klucza interfejsu API. Zastąp klucz interfejsu API administratora (YOUR-ADMIN-API-KEY) kluczem, który jest prawidłowy dla usługi wyszukiwania. Musisz tylko ustawić ten nagłówek raz na czas trwania sesji, ale dodasz go do każdego żądania. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Utwórz **obiekt $url,** który określa kolekcję indeksów usługi. Zastąp nazwę usługi (YOUR-SEARCH-SERVICE-NAME) prawidłową usługą wyszukiwania.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Uruchom **Invoke-RestMethod,** aby wysłać żądanie GET do usługi i zweryfikować połączenie. Dodaj **ConvertTo-Json,** dzięki czemu można wyświetlić odpowiedzi wysyłane z powrotem z usługi.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Jeśli usługa jest pusta i nie ma indeksów, wyniki są podobne do poniższego przykładu. W przeciwnym razie zobaczysz reprezentację JSON definicji indeksu.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli nie używasz portalu, indeks musi istnieć w usłudze, zanim będzie można załadować dane. Ten krok definiuje indeks i wypycha go do usługi. W tym kroku jest używany [interfejs API create index REST.](https://docs.microsoft.com/rest/api/searchservice/create-index)

Wymagane elementy indeksu obejmują nazwę i kolekcję pól. Kolekcja pól definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają sposób jego użycia (na przykład, czy można go przeszukiwać pełnotekstowo, filtrować lub pobierać w wynikach wyszukiwania). W ramach indeksu jedno z `Edm.String` pól typu musi być wyznaczone jako *klucz* tożsamości dokumentu.

Ten indeks nosi nazwę "hotels-quickstart" i zawiera definicje pól, które widzisz poniżej. Jest to podzbiór większego [indeksu hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używanego w innych instruktażyach. Przycięliśmy go w tym przewodniku szybki start dla zwięzłości.

1. Wklej ten przykład do programu PowerShell, aby utworzyć **obiekt $body** zawierający schemat indeksu.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Ustaw identyfikator URI do kolekcji indeksów w usłudze i *indeksie szybki start hoteli.*

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Uruchom polecenie z **$url** **, $headers**i **$body,** aby utworzyć indeks w usłudze. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Wyniki powinny wyglądać podobnie do tego (obcięty do dwóch pierwszych pól dla zwięzłości):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> W celu weryfikacji można również sprawdzić listę Indeksy w portalu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Ładowanie dokumentów

Aby wypchnąć dokumenty, użyj żądania HTTP POST do punktu końcowego adresu URL indeksu. Interfejs API REST dla tego zadania to [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Wklej ten przykład do programu PowerShell, aby utworzyć **obiekt $body** zawierający dokumenty, które chcesz przekazać. 

    To żądanie zawiera dwa pełne i jeden częściowy rekord. Rekord częściowy pokazuje, że można przekazać niekompletne dokumenty. Parametr `@search.action` określa sposób indeksowania. Prawidłowe wartości obejmują przekazywanie, scalanie, scalanieOrUpload i usuwanie. Zachowanie mergeOrUpload tworzy nowy dokument dla hotelId = 3 lub aktualizuje zawartość, jeśli już istnieje.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Ustaw punkt końcowy na kolekcję dokumentów *szybki start hoteli* i uwzględnij operację indeksu (indeksy/hotele-szybki start/dokumenty/indeks).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Uruchom polecenie za pomocą **$url**, **$headers**i **$body,** aby załadować dokumenty do indeksu szybki start hoteli.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Wyniki powinny wyglądać podobnie do poniższego przykładu. Powinien zostać wyświetlony [kod stanu 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku pokazano, jak zbadać indeks za pomocą [interfejsu API wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Pamiętaj, aby używać pojedynczych cudzysłowów w $urls wyszukiwania. Ciągi zapytań zawierają **$** znaki i można pominąć konieczności ich ucieczki, jeśli cały ciąg jest ujęty w pojedyncze cudzysłowy..

1. Ustaw punkt końcowy na kolekcję dokumentów *szybki start hoteli* i dodaj parametr **wyszukiwania** do przekazania w ciągu zapytania. 
  
   Ten ciąg wykonuje puste wyszukiwanie (search=*), zwracając listę nierankingową (wynik wyszukiwania = 1.0) dowolnych dokumentów. Domyślnie usługa Azure Cognitive Search zwraca 50 dopasowań naraz. Zgodnie z ustrukturyzowania, ta kwerenda zwraca całą strukturę dokumentu i wartości. Dodaj **$count=true,** aby uzyskać liczbę wszystkich dokumentów w wynikach.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Uruchom polecenie, aby wysłać **$url** do usługi.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Wypróbuj kilka innych przykładów zapytań, aby uzyskać odczucie składni. Można wykonać wyszukiwanie ciągów, dosłownie $filter zapytania, ograniczyć zestaw wyników, zakres wyszukiwania do określonych pól i więcej.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto programu PowerShell do przechodzenia przez podstawowy przepływ pracy służący do tworzenia i uzyskiwania dostępu do zawartości w usłudze Azure Cognitive Search. Mając na uwadze pojęcia, zaleca się przejście do bardziej zaawansowanych scenariuszy, takich jak indeksowanie ze źródeł danych platformy Azure;

> [!div class="nextstepaction"]
> [Samouczek REST: Indeksy i wyszukiwanie danych o częściowo ustrukturyzowanych (JSON blob) w usłudze Azure Cognitive Search](search-semi-structured-data.md)