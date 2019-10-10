---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w programie Poster przy użyciu interfejsów API REST — Azure Search'
description: Dowiedz się, jak wywoływać interfejsy API REST Azure Search przy użyciu programu Poster oraz dane przykładowe i definicje.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.openlocfilehash: ffa20599ae57908f9b0ea848ab68f41a3d0e2a14
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176035"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Szybki Start: Tworzenie indeksu Azure Search w programie Poster przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [Narzędzia](search-howto-dotnet-sdk.md)
>*

Jednym z najprostszych sposobów eksplorowania [Azure Search interfejsów API REST](https://docs.microsoft.com/rest/api/searchservice) jest użycie programu Poster lub innego narzędzia do testowania sieci Web w celu sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i tych instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed zapisaniem dowolnego kodu.

W tym artykule wyjaśniono, jak w sposób interaktywny sformułować żądania. Alternatywnie można [pobrać i zaimportować kolekcję programu Poster](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) , aby użyć wstępnie zdefiniowanych żądań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start wymaga następujących usług i narzędzi. 

+ [Aplikacja klasyczna programu post](https://www.getpostman.com/) jest używana do wysyłania żądań do Azure Search.

+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano Azure Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać jak `https://mydemo.search.windows.net`.

1. W obszarze **ustawienia** > **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

![Pobieranie punktu końcowego http i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego http i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia zaufanie dla poszczególnych żądań, między aplikacją wysyłającą żądanie a usługą, która go obsługuje.

## <a name="connect-to-azure-search"></a>Połącz z Azure Search

W tej sekcji Użyj wybranego narzędzia sieci Web, aby skonfigurować połączenia do Azure Search. Każde narzędzie utrzymuje informacje nagłówka żądania dla sesji, co oznacza, że wystarczy tylko raz wprowadzić klucz API-Key i Content-Type.

Dla każdego narzędzia należy wybrać polecenie (GET, POST, PUT itd.), podać punkt końcowy adresu URL, a w przypadku niektórych zadań podać kod JSON w treści żądania. Zastąp nazwę usługi wyszukiwania (nazwa użytkownika-SEARCH-SERVICE-NAME) prawidłową wartością. Dodaj `$select=name`, aby zwrócić tylko nazwę każdego indeksu. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Zwróć uwagę na prefiks protokołu HTTPS, nazwę usługi, nazwę obiektu (w tym przypadku kolekcja indeksów) i [wersję interfejsu API-Version](search-api-versions.md). Wersja API-Version jest wymagana, ponieważ w bieżącej wersji określono `?api-version=2019-05-06`. Wersje interfejsu API są regularnie aktualizowane. Dołączenie interfejsu API-Version dla każdego żądania daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typ zawartości oraz klucz API-Key służący do uwierzytelniania w Azure Search. Zastąp klucz interfejsu API administratora (parametr-AZURE-SEARCH-ADMIN-API-KEY) prawidłową wartością. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

W programie Poster należy sformułować żądanie, które wygląda podobnie do poniższego zrzutu ekranu. Wybierz pozycję **Get** jako zlecenie, podaj adres URL, a następnie kliknij pozycję **Wyślij**. To polecenie nawiązuje połączenie z Azure Search, odczytuje kolekcje indeksów i zwraca kod stanu HTTP 200 dla pomyślnego połączenia. Jeśli usługa ma już indeksy, odpowiedź będzie zawierać również definicje indeksów.

![Adres URL żądania post i]nagłówek(media/search-get-started-postman/postman-url.png "i nagłówek żądania postname")

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

W Azure Search, zazwyczaj tworzysz indeks przed załadowaniem go z danymi. Dla tego zadania jest używany [interfejs API Rest tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) . 

Adres URL jest rozszerzany w celu uwzględnienia nazwy indeksu `hotels`.

Aby to zrobić w programie Poster:

1. Zmień czasownik na wartość **Put**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Podaj definicję indeksu (kod przygotowany do kopiowania jest podany poniżej) w treści żądania.

4. Kliknij przycisk **Wyślij**.

![Indeksowanie dokumentu JSON w dokumencie żądania indeksu treści](media/search-get-started-postman/postman-request.png "JSON w treści żądania")

### <a name="index-definition"></a>Definicja indeksu

Kolekcja Fields definiuje strukturę dokumentu. Każdy dokument musi mieć te pola, a każde pole musi mieć typ danych. Pola ciągów są używane w wyszukiwaniu pełnotekstowym, więc możesz chcieć rzutować dane liczbowe jako ciągi, jeśli chcesz, aby zawartość była przeszukiwana.

Atrybuty w polu określają dozwolone akcje. Interfejsy API REST domyślnie zezwalają na wiele akcji. Na przykład wszystkie ciągi są domyślnie wyszukiwania, pobierania, filtrowania i tworzenia kroju. Często należy ustawić atrybuty tylko wtedy, gdy trzeba wyłączyć zachowanie.

```json
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
```

Po przesłaniu tego żądania należy uzyskać odpowiedź HTTP 201, wskazując, że indeks został pomyślnie utworzony. Tę akcję można sprawdzić w portalu, ale należy pamiętać, że strona portalu ma interwały odświeżania, co może potrwać minutę lub dwie.

> [!TIP]
> Jeśli zostanie wyświetlony protokół HTTP 504, sprawdź, czy adres URL określa protokół HTTPS. Jeśli widzisz HTTP 400 lub 404, sprawdź treść żądania, aby sprawdzić, czy nie wystąpiły błędy kopiowania i wklejania. Protokół HTTP 403 zwykle wskazuje problem z kluczem API-Key (nieprawidłowy klucz lub błąd składniowy z określonym parametrem API-Key).

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu są osobnymi krokami. W Azure Search indeks zawiera wszystkie dane, które można przeszukiwać jako dokumenty JSON. Dla tego zadania jest używany [interfejs API REST Dodawanie, aktualizowanie lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) . 

Adres URL jest rozszerzony, aby uwzględnić kolekcje `docs` i operację `index`.

Aby to zrobić w programie Poster:

1. Zmień zlecenie na **post**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Podaj dokumenty JSON (kod przygotowany do kopiowania poniżej) w treści żądania.

4. Kliknij przycisk **Wyślij**.

![Dokumenty JSON w](media/search-get-started-postman/postman-docs.png "dokumentach JSON żądania w treści żądania")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do załadowania do indeksu

Treść żądania zawiera cztery dokumenty, które mają zostać dodane do indeksu hoteli.

```json
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
```

W ciągu kilku sekund na liście sesji powinna zostać wyświetlona odpowiedź HTTP 201. Oznacza to, że dokumenty zostały utworzone pomyślnie. 

Jeśli otrzymasz 207, nie można przekazać co najmniej jednego dokumentu. Jeśli otrzymujesz 404, wystąpi błąd składniowy w nagłówku lub treści żądania: Sprawdź, czy punkt końcowy został zmieniony tak, aby obejmował `/docs/index`.

> [!Tip]
> Dla wybranych źródeł danych można wybrać alternatywne podejście *indeksatora* , które upraszcza i zmniejsza ilość kodu wymaganego do indeksowania. Aby uzyskać więcej informacji, zobacz [indeksator operacji](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 — wyszukiwanie indeksu

Teraz, gdy indeks i dokumenty są ładowane, można wysyłać zapytania do nich za pomocą [interfejsu API REST dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adres URL jest rozszerzony, aby uwzględnić wyrażenie zapytania, określone za pomocą operatora wyszukiwania.

Aby to zrobić w programie Poster:

1. Zmień zlecenie, aby **uzyskać**.

2. Kopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kliknij przycisk **Wyślij**.

To zapytanie jest puste i zwraca liczbę dokumentów w wynikach wyszukiwania. Żądanie i odpowiedź powinny wyglądać podobnie do poniższego zrzutu ekranu dla programu Poster po kliknięciu przycisku **Wyślij**. Kod stanu powinien mieć wartość 200.

 ![Pobierz ciąg wyszukiwania w adresie URL](media/search-get-started-postman/postman-query.png "pobieranie z ciągu wyszukiwania w adresie URL")

Wypróbuj kilka innych przykładów zapytania, aby uzyskać działanie dla składni. Możesz wykonać wyszukiwanie ciągów, Verbatim $filter zapytań, ograniczyć zestaw wyników, określić zakres wyszukiwania do określonych pól itd.

Zamień bieżący adres URL na poniższe, a następnie kliknij pozycję **Wyślij** za każdym razem, aby wyświetlić wyniki.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Pobierz właściwości indeksu
Można również użyć [Get Statistics](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) do wykonywania zapytań dotyczących liczby dokumentów i rozmiaru indeksu: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Dodanie `/stats` do adresu URL zwraca informacje o indeksie. W programie Poster żądanie powinno wyglądać podobnie do poniższego, a odpowiedź obejmuje liczbę dokumentów i miejsce używane w bajtach.

 ![Uzyskaj informacje o indeksie](media/search-get-started-postman/postman-system-query.png "Pobierz informacje o indeksie")

Zauważ, że składnia interfejsu API-Version jest inna. W przypadku tego żądania Użyj wartości `?` w celu dołączenia interfejsu API-Version. @No__t-0 oddziela ścieżkę URL od ciągu zapytania, podczas & oddziela każdą parę "Name = wartość" w ciągu zapytania. W przypadku tego zapytania interfejs API-Version jest pierwszym i jedynym elementem w ciągu zapytania.

## <a name="clean-up"></a>Oczyść

Gdy pracujesz nad własną subskrypcją, dobrym pomysłem jest zakończenie projektu w celu ustalenia, czy nadal potrzebujesz utworzonych zasobów. Zasoby po lewej stronie mogą być kosztowne. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wykonywać podstawowe zadania, możesz przejść do przodu z dodatkowymi wywołaniami interfejsu API REST, aby uzyskać bardziej zaawansowane funkcje, takie jak indeksatory, lub [skonfigurować potok wyszukiwania poznawczego](cognitive-search-tutorial-blob.md). W następnym kroku zalecamy następujące łącze:

> [!div class="nextstepaction"]
> [Samouczek REST: indeksowanie i przeszukiwanie danych z częściową strukturą (obiekty blob JSON) w Azure Search](search-semi-structured-data.md)
