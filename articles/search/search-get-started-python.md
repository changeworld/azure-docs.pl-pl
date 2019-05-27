---
title: 'Szybki start: Python i interfejsów API REST — usługa Azure Search'
description: Tworzenie, obciążenia i tworzenie zapytań względem indeksu przy użyciu języka Python, notesy Jupyter i interfejsu API REST usługi Azure Search.
ms.date: 05/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: a79a5fe1632eeabee670274ebbb19c4c34bd84d2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117338"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Szybki start: Tworzenie indeksu usługi Azure Search przy użyciu notesów programu Jupyter w języku Python
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (interfejs API REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Tworzenie notesu programu Jupyter, który tworzy, ładuje i zapytania indeksu usługi Azure Search przy użyciu języka Python i [interfejsów API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). W tym artykule opisano sposób tworzenia notesu krok po kroku, zaczynając od podstaw. Można również uruchomić Zakończono notesu. Aby pobrać kopię, przejdź do [repozytorium Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), zapewniając Python 3.x i notesy Jupyter.

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

Otwieranie notesu Jupyter i sprawdź połączenie z lokalnej stacji roboczej, żądając listy indeksów w usłudze. Na Windows za pomocą anaconda3, wersja Nawigator Anaconda służy do uruchamiania notesu.

1. Tworzenie nowego notesu środowiska python3 jako.

1. W pierwszej komórki załadować bibliotek używanych do pracy z formatu JSON i sformułowania żądań HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. W drugiej komórce wpisać elementy żądania, które będą stałe na każde żądanie. Zamień nazwy usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) i klucz administratora interfejsu API (YOUR-ADMIN-API-KEY) prawidłowych wartości. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. W trzeciej komórce sformułować żądania. To żądanie GET jest przeznaczony dla kolekcja indeksy usługi wyszukiwania i wybiera właściwość name.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Uruchom każdego kroku. Jeśli istnieje indeksów, odpowiedź zawiera listy indeksów. Na poniższym zrzucie ekranu usługa obejmuje indeks obiektu blob platformy Azure i indeks realestate-us-sample.

   ![Skrypt w języku Python w notesie Jupyter za pośrednictwem protokołu HTTP żądania do usługi Azure Search](media/search-get-started-python/connect-azure-search.png "skrypt w języku Python w notesie Jupyter za pośrednictwem protokołu HTTP żądania do usługi Azure Search")

   Kolekcja pusty indeks zwraca tej odpowiedzi: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

> [!Tip]
> Bezpłatne usługi są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Ten przewodnik Szybki Start tworzy jeden. Upewnij się, że dysponujesz miejscem do tworzenia nowych obiektów, zanim przejdziesz dalej.

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Jeśli używasz portalu indeksu musi istnieć w usłudze przed załadowaniem danych. Ten krok używa [utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) wypychania schematu indeksu w usłudze

Kolekcja pól definiuje strukturę *dokumentu*. Wymagane elementy indeksu obejmują nazwy i kolekcji pól. Każde pole ma nazwę, typ i atrybuty, które określają, jak jest używana (na przykład, czy jest pełnotekstowe wyszukiwanie, filtrowanie lub pobieranie w wynikach wyszukiwania). W ramach indeksu, jednego pola typu `Edm.String` musi zostać wyznaczona jako *klucz* dokumentu tożsamości.

Ten indeks o nazwie "hotels-py" i ma definicje pól, pokazane poniżej. Jest podzbiorem większego [indeksu Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) używane w innych instruktaży. Firma Microsoft spacje w tym przewodniku Szybki Start w celu skrócenia programu.

1. W następnej komórki Wklej poniższy w komórce w celu zapewnienia schematu. 

    ```python
    index_schema = {
       "name": "hotels-py",  
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

2. W innej komórce sformułować żądania. Umieść to żądanie jest przeznaczony dla kolekcja indeksy usługi wyszukiwania i tworzy indeks na podstawie schematu indeksu podany w poprzednim kroku.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Uruchom każdego kroku.

   Odpowiedź zawiera reprezentacji JSON schematu. Poniższy zrzut ekranu przycina części schemat indeksu, aby zobaczyć więcej odpowiedzi.

    ![Żądanie utworzenia indeksu](media/search-get-started-python/create-index.png "żądanie utworzenia indeksu")

> [!Tip]
> W celu weryfikacji, można również zapoznaj się z listą indeksów w portalu, lub ponownie uruchomić żądanie połączenia usługi, aby zobaczyć *py hotele* indeksu na liście kolekcji indeksów.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Aby wypchnąć dokumenty, należy użyć żądania HTTP POST do punktu końcowego adresu URL Twojego indeksu. Interfejs API REST jest [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokumenty te pochodzą z [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) w witrynie GitHub.

1. Nową komórkę zawiera cztery dokumenty, które są zgodne ze schematem indeksu. Określ akcję przekazywania dla każdego dokumentu.

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

2. W innej komórce sformułować żądania. To żądanie POST jest przeznaczony dla kolekcji docs indeksu hotels py i wypycha dokumenty określone w poprzednim kroku.

   ```python
   url = endpoint + "indexes/hotels-py/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Uruchom każdego kroku, aby wypchnąć dokumenty do indeksu w usłudze wyszukiwania. Wyniki powinny wyglądać podobnie do poniższego przykładu. 

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
    'value': [{'errorMessage': None,
            'key': '1',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '2',
            'status': True,
            'statusCode': 201},
           {'errorMessage': None,
            'key': '3',
            'status': True,
            'statusCode': 201}]},
           {'errorMessage': None,
            'key': '4',
            'status': True,
            'statusCode': 201}]}
     ```


## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku przedstawiono sposób tworzenia zapytań względem indeksu przy użyciu [interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).


1. W nowej komórki należy podać wyrażenie zapytania. Poniższy przykład wyszukuje warunki "hotels" i "sieć Wi-Fi". Również zwraca *liczba* dokumentów, które są zgodne, i *wybiera* pola do uwzględnienia w wynikach wyszukiwania.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

2. Sformułuj żądanie. To żądanie GET jest przeznaczony dla kolekcji docs indeksu hotels py i dołącza zapytania, które określiłeś w poprzednim kroku.

   ```python
   url = endpoint + "indexes/hotels-py/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

   Wyniki powinny wyglądać podobnie do następujących danych wyjściowych. Wyniki są unranked (search.score = 1,0), ponieważ firma Microsoft nie zapewnia żadnych kryteriów dopasowania.

   ```
   {'@odata.context': "https://mydemo.search.windows.net/indexes('hotels-py')/$metadata#docs(*)",
    '@odata.count': 3,
    'value': [{'@search.score': 1.0,
               'HotelId': '1',
               'HotelName': 'Secret Point Motel'},
              {'@search.score': 1.0,
               'HotelId': '2',
               'HotelName': 'Twin Dome Motel'},
              {'@search.score': 1.0,
               'HotelId': '3',
               'HotelName': 'Triple Landscape Hotel'},
              {'@search.score': 1.0,
               'HotelId': '4',
               'HotelName': 'Sublime Cliff Hotel'}]}
   ```

3. Wypróbuj kilka innych przykładów zapytanie, aby uzyskać pewne pojęcie składni. Możesz zastosować filtr, wykonać dwóch pierwszych wyników lub kolejność według określonego pola.

   + `searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'`

   + `searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'`

## <a name="clean-up"></a>Czyszczenie 

Jeśli nie są już potrzebne, należy usunąć indeks. Usługi w warstwie bezpłatna jest ograniczona do trzech indeksów. Możesz chcieć usunąć wszystkie indeksy, których nie używasz aktywnie aby zwolnić miejsce dla innych samouczków.

   ```python
  url = endpoint + "indexes/hotels-py" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Możesz sprawdzić usunięcie indeksu, zwracając listę istniejące indeksy. Jeśli py hotele został usunięty, wiesz żądanie zakończyło się pomyślnie.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Kolejne kroki

Jako uproszczenia w tym przewodniku Szybki Start skróconą wersję indeksu Hotels. Można utworzyć pełnej wersji, aby wypróbować bardziej interesujące zapytania. Aby uzyskać pełną wersję i wszystkie dokumenty 50, uruchom **importowania danych** kreatora, wybierając *przykład hotele* ze źródeł danych wbudowany przykładowy.

> [!div class="nextstepaction"]
> [Szybki start: Tworzenie indeksu w witrynie Azure portal](search-get-started-portal.md)
