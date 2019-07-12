---
title: 'Szybki Start programu PowerShell: Tworzenie, obciążenia i zapytań indeksy przy użyciu usługi Azure Search REST API — usługi Azure Search'
description: Wyjaśnia, jak utworzyć indeks, Załaduj dane i uruchamianie zapytań przy użyciu programu PowerShell Invoke RestMethod i interfejsu API REST usługi Azure Search.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c8a49fe5d334b5752b9272e480fb2502a980b0a4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840169"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Szybki start: Tworzenie indeksu usługi Azure Search w programie PowerShell, za pomocą interfejsów API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (interfejs API REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

Ten artykuł przeprowadzi Cię przez proces tworzenia, ładowanie i wykonywanie zapytania dotyczącego indeksu usługi Azure Search przy użyciu programu PowerShell i [interfejsów API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). W tym artykule wyjaśniono, jak uruchomić interaktywnie poleceń programu PowerShell. Możesz też [Pobierz i uruchom skrypt programu Powershell](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) który wykonuje te same operacje.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

+ [Program PowerShell 5.1 lub nowszej](https://github.com/PowerShell/PowerShell)przy użyciu [Invoke RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) czynności sekwencyjne i interaktywne.

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

1. W programie PowerShell utworzyć **$headers** obiekt ma być przechowywany, typu zawartości i klucz interfejsu API. Zastąp klucz administratora interfejsu API (YOUR-ADMIN-API-KEY) z kluczem, który jest prawidłowy dla Twojej usługi wyszukiwania. Musisz ustawić tego pliku nagłówkowego raz na czas trwania sesji, ale doda go do każdego żądania. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Tworzenie **$url** obiekt, który określa usługę indeksuje kolekcji. Zastąp nazwę usługi (YOUR-SEARCH-SERVICE-NAME) z usługą wyszukiwania prawidłowe.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Uruchom **Invoke RestMethod** Wyślij żądanie Pobierz do usługi i sprawdzić połączenie. Dodaj **ConvertTo Json** tak, aby wyświetlić odpowiedzi wysłana odpowiedź od usługi.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Jeśli usługa jest pusta i nie zawiera indeksów, wyniki są podobne do poniższego przykładu. W przeciwnym razie zostanie wyświetlona reprezentacja JSON w definicji indeksu.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli używasz portalu indeksu musi istnieć w usłudze przed załadowaniem danych. Ten krok definiuje indeks i wypycha go do usługi. [Utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) jest używany w tym kroku.

Wymagane elementy indeksu obejmują nazwy i kolekcji pól. Kolekcja pól definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają, jak jest używana (na przykład, czy jest pełnotekstowe wyszukiwanie, filtrowanie lub pobieranie w wynikach wyszukiwania). W ramach indeksu, jednego pola typu `Edm.String` musi zostać wyznaczona jako *klucz* dokumentu tożsamości.

Ten indeks o nazwie "hotels — Szybki Start" i ma definicje pól, pokazane poniżej. Jest podzbiorem większego [indeksu Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używane w innych instruktaży. Firma Microsoft spacje w tym przewodniku Szybki Start w celu skrócenia programu.

1. Wklej w tym przykładzie w programie PowerShell, aby utworzyć **$body** obiektu zawierającego schemat indeksu.

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

2. Ustaw identyfikator URI kolekcji indeksów na usługę i *hotele — Szybki Start* indeksu.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Uruchom polecenie z **$url**, **$headers**, i **$body** do utworzenia indeksu w usłudze. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Wyniki powinny wyglądać podobnie do poniższego (obcięty do pierwsze dwa pola dla zwięzłości):

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
> W celu weryfikacji można również sprawdzić listy indeksów w portalu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Aby wypchnąć dokumenty, należy użyć żądania HTTP POST do punktu końcowego adresu URL Twojego indeksu. Interfejs API REST dla tego zadania jest [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Wklej w tym przykładzie w programie PowerShell, aby utworzyć **$body** obiektu zawierającego dokumenty przeznaczone do przekazania. 

    To żądanie zawiera dwa pełne i jeden rekord częściowe. Częściowe rekordu pokazuje przekazać niekompletne dokumentów. `@search.action` Parametr określa, jak indeksowanie jest wykonywane. Prawidłowe wartości to przekazywanie, scalanie, mergeOrUpload i delete. Zachowanie mergeOrUpload albo tworzy nowy dokument dla hotelId = 3, lub aktualizuje zawartość, jeśli już istnieje.

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

1. Ustaw punkt końcowy *hotele — Szybki Start* kolekcji docs i obejmują operacja indeksu (indeksy/hotels — Szybki Start/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Uruchom polecenie z **$url**, **$headers**, i **$body** ładowanie dokumentów do indeksu hotels — Szybki Start.

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

W tym kroku przedstawiono sposób tworzenia zapytań względem indeksu przy użyciu [interfejsu API wyszukiwania w dokumentach](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Pamiętaj użyć apostrofów $urls wyszukiwania. Ciągi zapytania obejmują **$** znaków i można pominąć konieczności ucieczki je, jeśli cały ciąg jest ujęta w apostrofy...

1. Ustaw punkt końcowy *hotele — Szybki Start* kolekcji docs i Dodaj **wyszukiwania** parametr do przekazania w ciągu zapytania. 
  
   Ten ciąg wykonuje to puste wyszukiwanie (wyszukiwania = *), zwracając unranked listy (Wyszukaj wynik = 1,0) dowolnych dokumentów. Domyślnie usługa Azure Search zwraca 50 dopasowaniami w danym momencie. Jako ze strukturą ta kwerenda zwraca strukturę całego dokumentu i wartości. Dodaj **$count = true** można pobrać liczby wszystkich dokumentów w wynikach.

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

Wypróbuj kilka innych przykładów zapytanie, aby uzyskać pewne pojęcie składni. Można zrobić w przypadku wyszukiwania na ciąg zapytania verbatim $filter, ograniczyć zestaw wyników, zakres wyszukiwania do określonych pól i nie tylko.

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
## <a name="clean-up"></a>Czyszczenie 

Podczas pracy w ramach własnej subskrypcji jest dobrym pomysłem na końcu projektu ustalić, czy nadal potrzebujesz zasoby utworzone. Po lewej stronie umożliwia uruchamianie zasobów kosztów pieniądze. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Możesz znaleźć i zarządzanie zasobami w portalu przy użyciu **wszystkie zasoby** lub **grup zasobów** łącze w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu w celu pozostania w ramach limitu. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki Start programu PowerShell jest używany do wykonania kroków podstawowy przepływ pracy tworzenia i uzyskiwania dostępu do zawartości w usłudze Azure Search. Pojęcia, pamiętając zaleca się przejście do bardziej zaawansowanych scenariuszy, takie jak indeksowanie ze źródłami danych platformy Azure;

> [!div class="nextstepaction"]
> [Samouczek dotyczący architektury REST: Indeksowanie i wyszukiwanie częściowo ustrukturyzowanych danych (obiektów blob JSON) w usłudze Azure Search](search-semi-structured-data.md)