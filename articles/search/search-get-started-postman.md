---
title: 'Szybki start: tworzenie indeksu wyszukiwania w postman przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start interfejsu API REST dowiedz się, jak wywołać interfejsy API REST usługi Azure Cognitive Search przy użyciu usług Postman i przykładowych danych i definicji.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: c502886aac9d13f7a470a9b83f1fc12334913beb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121638"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Szybki start: tworzenie indeksu usługi Azure Cognitive Search w usłudze Postman przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C #](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-howto-dotnet-sdk.md)
>*

Jednym z najprostszych sposobów eksplorowania [interfejsów API rest usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice) jest użycie usługi Postman lub innego narzędzia do testowania sieci Web do formułowania żądań HTTP i sprawdzania odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

W tym artykule wyjaśniono, jak formułować żądania interaktywnie. Alternatywnie można [pobrać i zaimportować kolekcję Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) do korzystania ze wstępnie zdefiniowanych żądań.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Do tego przewodnika Szybki start wymagane są następujące usługi i narzędzia. 

+ [Aplikacja klasyczna Postman](https://www.getpostman.com/) służy do wysyłania żądań do usługi Azure Cognitive Search.

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu. 

## <a name="get-a-key-and-url"></a>Uzyskaj klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona z obu, więc jeśli dodano usługę Azure Cognitive Search do subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/), a na stronie **przegląd** usługi wyszukiwania pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawieniach** > **klawiszy**pobierz klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administracyjne, przewidziane dla ciągłości biznesowej w przypadku, gdy trzeba przewrócić jeden. Klucz podstawowy lub pomocniczy można używać w żądaniach dodawania, modyfikowania i usuwania obiektów.

![Uzyskiwanie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Uzyskiwanie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza api przy każdym żądaniu wysłanym do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-cognitive-search"></a>Łączenie się z usługą Azure Cognitive Search

W tej sekcji użyj wybranego narzędzia sieci web, aby skonfigurować połączenia z usługą Azure Cognitive Search. Każde narzędzie powtarza informacje nagłówka żądania dla sesji, co oznacza, że wystarczy wprowadzić api-key i Content-Type tylko raz.

Dla obu narzędzi, należy wybrać polecenie (GET, POST, PUT, itd.), podaj punkt końcowy adresu URL, a dla niektórych zadań, podaj JSON w treści żądania. Zastąp nazwę usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) prawidłową wartością. Dodaj, `$select=name` aby zwrócić tylko nazwę każdego indeksu. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Zwróć uwagę na prefiks HTTPS, nazwę usługi, nazwę obiektu (w tym przypadku kolekcję indeksów) i [wersję interfejsu api](search-api-versions.md). Wersja interfejsu api jest wymaganym, małe litery ciąg określony jak `?api-version=2019-05-06` dla bieżącej wersji. Wersje interfejsu API są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typ zawartości oraz klucz interfejsu API używany do uwierzytelniania w usłudze Azure Cognitive Search. Zastąp klucz interfejsu API administratora (YOUR-AZURE-SEARCH-ADMIN-API-KEY) prawidłową wartością. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

W polu Postman sformułowanie żądania, które wygląda jak poniższy zrzut ekranu. Wybierz **GET** jako czasownik, podaj adres URL i kliknij przycisk **Wyślij**. To polecenie łączy się z usługą Azure Cognitive Search, odczytuje kolekcję indeksów i zwraca kod stanu HTTP 200 w przypadku pomyślnego połączenia. Jeśli usługa ma już indeksy, odpowiedź będzie również zawierać definicje indeksu.

![Adres URL i nagłówek żądania listonosza](media/search-get-started-postman/postman-url.png "Adres URL i nagłówek żądania listonosza")

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

W usłudze Azure Cognitive Search zwykle tworzysz indeks przed załadowaniem go z danymi. Dla tego zadania jest używany [interfejs API Create Index REST.](https://docs.microsoft.com/rest/api/searchservice/create-index) 

Adres URL zostanie rozszerzony `hotels` o nazwę indeksu.

Aby to zrobić w Postman:

1. Zmień czasownik na **PUT**.

2. Skopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Podaj definicję indeksu (kod gotowy do kopiowania znajduje się poniżej) w treści żądania.

4. Kliknij pozycję **Wyślij**.

![Indeks dokumentu JSON w treści żądania](media/search-get-started-postman/postman-request.png "Indeks dokumentu JSON w treści żądania")

### <a name="index-definition"></a>Definicja indeksu

Kolekcja pól definiuje strukturę dokumentu. Każdy dokument musi mieć te pola, a każde pole musi mieć typ danych. Pola ciągów są używane podczas wyszukiwania pełnotekstowego, możesz więc rzutować dane numeryczne jako ciągi, jeśli potrzebujesz, aby ta zawartość dawała możliwość wyszukiwania.

Atrybuty w polu określają dozwoloną akcję. Interfejsy API REST domyślnie umożliwiają wiele akcji. Na przykład wszystkie ciągi domyślnie umożliwiają wyszukiwanie, pobieranie, filtrowanie i tworzenie aspektów. Często trzeba ustawić atrybuty tylko wtedy, gdy trzeba wyłączyć zachowanie.

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

Po przesłaniu tego żądania powinna pojawić się odpowiedź 201 protokołu HTTP wskazująca, że indeks został pomyślnie utworzony. Możesz sprawdzić tę akcję w portalu, ale pamiętaj, że strona portalu ma odstępy czasowe odświeżania, więc wyrównanie może zająć minutę lub dwie.

> [!TIP]
> Jeśli otrzymasz odpowiedź 504 protokołu HTTP, sprawdź, czy adres URL określa protokół HTTPS. Jeśli zobaczysz odpowiedź 400 lub 404 protokołu HTTP, sprawdź treść żądania, aby zweryfikować, czy nie było żadnych błędów podczas kopiowania i wklejania. Odpowiedź 403 protokołu HTTP zazwyczaj wskazuje na problem z kluczem api-key (nieprawidłowy klucz lub problem ze składnią klucza api-key).

## <a name="2---load-documents"></a>2 - Ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. W usłudze Azure Cognitive Search indeks zawiera wszystkie dane z wyszukujne, które można podać jako dokumenty JSON. Dla tego zadania jest używany [interfejs API Dodawanie, aktualizowanie lub usuwanie dokumentów REST.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 

Adres URL zostanie rozszerzony `docs` o `index` kolekcje i działanie.

Aby to zrobić w Postman:

1. Zmień zlecenie na **POST**.

2. Skopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Podaj dokumenty JSON (kod gotowy do kopiowania znajduje się poniżej) w treści żądania.

4. Kliknij pozycję **Wyślij**.

![Dokumenty JSON w treści żądania](media/search-get-started-postman/postman-docs.png "Dokumenty JSON w treści żądania")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do załadowania do indeksu

Obszar Request Body (Treść żądania) zawiera cztery dokumenty, które mają zostać dodane do indeksu hotels.

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

W ciągu kilku sekund powinna zostać wyświetlona odpowiedź HTTP 201 na liście sesji. Oznacza to, że dokumenty zostały pomyślnie utworzone. 

Jeśli otrzymasz odpowiedź 207, przekazanie co najmniej jednego dokumentu nie powiodło się. Jeśli otrzymasz odpowiedź 404, wystąpił błąd składniowy w nagłówku lub w treści żądania: zweryfikuj, czy zmiana punktu końcowego uwzględniła `/docs/index`.

> [!Tip]
> Dla wybranych źródeł danych możesz wybrać alternatywne podejście *indeksatora*, które upraszcza i zmniejsza ilość kodu wymaganą do indeksowania. Aby uzyskać więcej informacji, zobacz [Operacje indeksatora](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy indeks i dokumenty są ładowane, można wystawiać zapytania przeciwko nim za pomocą [interfejsu API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adres URL jest rozszerzany o wyrażenie zapytania, określone za pomocą operatora wyszukiwania.

Aby to zrobić w Postman:

1. Zmień czasownik na **GET**.

2. Skopiuj w tym adresie URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kliknij pozycję **Wyślij**.

Ta kwerenda jest pusta i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![GET z ciągiem wyszukiwania na adresie URL](media/search-get-started-postman/postman-query.png "GET z ciągiem wyszukiwania na adresie URL")

Wypróbuj kilka innych przykładów zapytań, aby uzyskać odczucie składni. Można wykonać wyszukiwanie ciągów, dosłownie $filter zapytania, ograniczyć zestaw wyników, zakres wyszukiwania do określonych pól i więcej.

Zamień bieżący adres URL na poniższy adres URL, klikając za każdym razem **pozycję Wyślij,** aby wyświetlić wyniki.

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
Można również użyć [Pobierz statystyki](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) do wykonywania zapytań o liczbę dokumentów i rozmiar indeksu: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Dodanie `/stats` do adresu URL zwraca informacje o indeksie. W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Uzyskaj informacje o indeksie](media/search-get-started-postman/postman-system-query.png "Uzyskaj informacje o indeksie")

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. Oddziela `?` ścieżkę adresu URL od ciągu zapytania, podczas gdy & oddziela każdą parę "name=value" w ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz, jak wykonywać podstawowe zadania, możesz przejść do przodu dzięki dodatkowym wywołam interfejsu API REST dla bardziej zaawansowanych funkcji, takich jak indeksatory lub [konfigurowanie potoku wyszukiwania kognitywnego.](cognitive-search-tutorial-blob.md) W następnym kroku zalecamy następujące łącze:

> [!div class="nextstepaction"]
> [Samouczek REST: Indeksy i wyszukiwanie danych o częściowo ustrukturyzowanych (JSON blob) w usłudze Azure Cognitive Search](search-semi-structured-data.md)
