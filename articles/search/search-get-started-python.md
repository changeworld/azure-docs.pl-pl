---
title: 'Szybki start: tworzenie indeksu wyszukiwania w języku Python przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym artykule wyjaśniono, jak utworzyć indeks, załadować dane i uruchomić kwerendy przy użyciu języka Python, notesów jupyter i interfejsu API REST usługi Azure Cognitive Search.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 93fb9ec735de1abf89eb217d0f4096fcfc0afe94
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78227103"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w języku Python przy użyciu notesów Jupyter

> [!div class="op_single_selector"]
> * [Python (RESZTA)](search-get-started-python.md)
> * [Program PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Listonosz (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Tworzenie notesu Jupyter, który tworzy, ładuje i wysyła kwerendy do indeksu usługi Azure Cognitive Search przy użyciu języka Python i [interfejsów API REST usługi Azure Cognitive Search.](https://docs.microsoft.com/rest/api/searchservice/) W tym artykule wyjaśniono, jak tworzyć notes krok po kroku. Alternatywnie, można [pobrać i uruchomić gotowy notebook Jupyter Python](https://github.com/Azure-Samples/azure-search-python-samples).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do tego przewodnika Szybki start wymagane są następujące usługi i narzędzia. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), zapewniając Python 3.x i Jupyter Notebooki.

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym przewodniku Szybki start można użyć warstwy Bezpłatna. 

## <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza api przy każdym żądaniu wysłanym do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Łączenie się z usługą Azure Cognitive Search

W tym zadaniu uruchom notes Jupyter i sprawdź, czy możesz połączyć się z usługą Azure Cognitive Search. Zrobisz to, żądając listy indeksów z usługi. W systemie Windows z anaconda3 można użyć Anaconda Navigator do uruchomienia notesu.

1. Utwórz nowy notes Python3.

1. W pierwszej komórce załaduj biblioteki używane do pracy z JSON i formułowania żądań HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. W drugiej komórce wprowadź elementy żądania, które będą stałymi przy każdym żądaniu. Zastąp nazwę usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i klucz interfejsu API administratora (YOUR-ADMIN-API-KEY) prawidłowymi wartościami. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Jeśli zostanie otrzymasz `"Failed to establish a new connection"`ConnectionError , sprawdź, czy klucz interfejsu API jest podstawowym lub`?` pomocniczym kluczem administratora i czy wszystkie znaki wiodące i końcowe ( i `/`) są na miejscu.

1. W trzeciej komórce sformułowaj żądanie. To żądanie GET jest przeznaczone dla kolekcji indeksów usługi wyszukiwania i wybiera właściwość nazwy istniejących indeksów.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Uruchom każdy krok. Jeśli istnieją indeksy, odpowiedź zawiera listę nazw indeksów. Na poniższym zrzucie ekranu usługa ma już indeks azureblob i indeks przykładowy realestate-us.

   ![Skrypt Języka Python w notesie Jupyter z żądaniami HTTP do usługi Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Skrypt Języka Python w notesie Jupyter z żądaniami HTTP do usługi Azure Cognitive Search")

   Natomiast pusta kolekcja indeksu zwraca tę odpowiedź:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli nie używasz portalu, indeks musi istnieć w usłudze, zanim będzie można załadować dane. Ten krok używa [interfejsu API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index) do wypychania schematu indeksu do usługi.

Wymagane elementy indeksu obejmują nazwę, kolekcję pól i klucz. Kolekcja pól definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają sposób jego użycia (na przykład, czy można je przeszukiwać pełnotekstowo, filtrować czy pobierać w wynikach wyszukiwania). W ramach indeksu jedno z `Edm.String` pól typu musi być wyznaczone jako *klucz* tożsamości dokumentu.

Ten indeks nosi nazwę "hotels-quickstart" i zawiera definicje pól, które widzisz poniżej. Jest to podzbiór większego [indeksu hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używanego w innych instruktażyach. Przycięliśmy go w tym przewodniku szybki start dla zwięzłości.

1. W następnej komórce wklej poniższy przykład do komórki, aby zapewnić schemat. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. W innej komórce sformułowaj żądanie. To żądanie POST jest przeznaczone dla kolekcji indeksów usługi wyszukiwania i tworzy indeks na podstawie schematu indeksu podanego w poprzedniej komórce.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Uruchom każdy krok.

   Odpowiedź zawiera reprezentację JSON schematu. Poniższy zrzut ekranu pokazuje tylko część odpowiedzi.

    ![Żądanie utworzenia indeksu](media/search-get-started-python/create-index.png "Żądanie utworzenia indeksu")

> [!Tip]
> Innym sposobem weryfikacji tworzenia indeksu jest sprawdzenie listy Indeksy w portalu.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Ładowanie dokumentów

Aby wypchnąć dokumenty, użyj żądania HTTP POST do punktu końcowego adresu URL indeksu. Interfejs API REST to [Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty pochodzą z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) na GitHub.

1. W nowej komórce podaj cztery dokumenty, które są zgodne ze schematem indeksu. Określ akcję przekazywania dla każdego dokumentu.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. W innej komórce sformułowaj żądanie. To żądanie POST jest przeznaczone dla kolekcji dokumentów indeksu szybki start w hotelach i wypycha dokumenty dostarczone w poprzednim kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Uruchom każdy krok, aby wypchnąć dokumenty do indeksu w usłudze wyszukiwania. Wyniki powinny wyglądać podobnie do poniższego przykładu. 

    ![Wysyłanie dokumentów do indeksu](media/search-get-started-python/load-index.png "Wysyłanie dokumentów do indeksu")

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku pokazano, jak zbadać indeks za pomocą [interfejsu API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. W komórce podaj wyrażenie zapytania, które wykonuje puste wyszukiwanie (search=*), zwracając listę nierankingową (wynik wyszukiwania = 1,0) dowolnych dokumentów. Domyślnie usługa Azure Cognitive Search zwraca 50 dopasowań naraz. Zgodnie z ustrukturyzowania, ta kwerenda zwraca całą strukturę dokumentu i wartości. Dodaj $count=true, aby uzyskać liczbę wszystkich dokumentów w wynikach.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. W nowej komórce podaj poniższy przykład wyszukiwania na terminach "hotele" i "wifi". Dodaj $select, aby określić, które pola mają być uwzględniane w wynikach wyszukiwania.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. W innej komórce sformułowaj żądanie. To żądanie GET jest przeznaczone dla kolekcji dokumentów indeksu szybki start hoteli i dołącza kwerendę określoną w poprzednim kroku.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Uruchom każdy krok. Wyniki powinny wyglądać podobnie do następujących danych wyjściowych. 

    ![Przeszukiwanie indeksu](media/search-get-started-python/search-index.png "Przeszukiwanie indeksu")

1. Wypróbuj kilka innych przykładów zapytań, aby uzyskać odczucie składni. Można zastąpić `searchstring` poniższymi przykładami, a następnie ponownie uruchomić żądanie wyszukiwania. 

   Zastosuj filtr: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Weź dwa najlepsze wyniki:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Kolejność według określonego pola:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Czyszczenie

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

W ramach uproszczenia ten szybki start wykorzystuje skróconą wersję indeksu Hotele. Możesz utworzyć pełną wersję, aby wypróbować bardziej interesujące zapytania. Aby uzyskać pełną wersję i wszystkie 50 dokumentów, uruchom **Kreatora importu danych,** wybierając *przykładowe hotele* z wbudowanych przykładowych źródeł danych.

> [!div class="nextstepaction"]
> [Szybki start: tworzenie indeksu w witrynie Azure portal](search-get-started-portal.md)
