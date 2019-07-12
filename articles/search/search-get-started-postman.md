---
title: 'Szybki start: Tworzenie, obciążenia i zapytań indeksy przy użyciu interfejsów API REST — usługa Azure Search i narzędzia Postman'
description: Dowiedz się, jak wywołać z usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman i przykładowe dane i definicje.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 015dd3631322978d6416041a3eea8390a72b0c17
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840260"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Szybki start: Tworzenie indeksu usługi Azure Search w narzędziu Postman przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-howto-dotnet-sdk.md)
>*

Jedną z najprostszych sposobów eksploracji [interfejsów API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) jest przy użyciu narzędzia Postman lub innego narzędzia do testowania w sieci web do sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

W tym artykule wyjaśniono, jak interaktywnie sformułowania żądań. Możesz też [pobieranie i importowanie kolekcji Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) można użyć wstępnie zdefiniowanych żądań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

+ [Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) służy do wysyłania żądań do usługi Azure Search.

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

W tej sekcji narzędzie usługi sieci web wybranego do konfigurowania połączeń do usługi Azure Search. Każde narzędzie utrzymuje informacje o nagłówku żądania dla tej sesji, co oznacza, że należy wprowadzić klucz interfejsu api i Content-Type jeden raz.

Dla dowolnego z tych narzędzi muszą wybrać polecenie (GET, POST, PUT i tak dalej), podaj adres URL punktu końcowego, a w przypadku niektórych zadań, należy zapewnić JSON w treści żądania. Zamień nazwy usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) Nieprawidłowa wartość. Dodaj `$select=name` zwracać tylko nazwę każdego indeksu. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Zwróć uwagę, prefiks protokołu HTTPS, nazwę usługi, nazwa obiektu (w tym przypadku kolekcja indeksy), a [parametru api-version](search-api-versions.md). Api-version jest wymagana, małe litery ciągu, określony jako `?api-version=2019-05-06` dla bieżącej wersji. Wersje interfejsu API są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typu zawartości, a także klucz interfejsu api używany do uwierzytelniania usługi Azure Search. Zastąp klucz administratora interfejsu API (YOUR-AZURE-SEARCH-ADMIN-API-KEY) prawidłową wartość. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

W narzędziu Postman Sformułuj żądanie wygląda jak poniższy zrzut ekranu. Wybierz **UZYSKAĆ** jako zlecenie, podaj adres URL, a następnie kliknij przycisk **wysyłania**. To polecenie łączy do usługi Azure Search odczytuje kolekcja indeksy i zwraca kod stanu HTTP 200 po pomyślnym nawiązaniu połączenia. Jeśli usługa ma indeksy już, odpowiedź będzie również udostępniać definicje indeksów.

![Adres URL żądania narzędzia postman i nagłówek](media/search-get-started-postman/postman-url.png "adresu URL żądania narzędzia Postman i nagłówek")

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

W usłudze Azure Search zazwyczaj Utwórz indeks przed załadowaniem danych. [Utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `hotels` nazwę indeksu.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **umieścić**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. W treści żądania, należy podać definicję indeksu (gotowych do użycia kopii kodu znajduje się poniżej).

4. Kliknij przycisk **wysyłania**.

![Dokument JSON indeksu w treści żądania](media/search-get-started-postman/postman-request.png "dokumentów JSON do indeksu w treści żądania")

### <a name="index-definition"></a>Definicja indeksu

Kolekcja pól definiuje strukturę dokumentu. Każdy dokument musi zawierać następujące pola, a każde pole musi mieć typ danych. Pola ciągów są używane podczas wyszukiwania pełnotekstowego, możesz więc rzutować dane numeryczne jako ciągi, jeśli potrzebujesz, aby ta zawartość dawała możliwość wyszukiwania.

Atrybuty w polu określają dozwoloną akcję. Interfejsy API REST domyślnie umożliwiają wiele akcji. Na przykład wszystkie ciągi domyślnie umożliwiają wyszukiwanie, pobieranie, filtrowanie i tworzenie aspektów. Często wystarczy ustawić atrybuty, gdy trzeba wyłączyć to zachowanie.

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

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. W usłudze Azure Search indeks zawiera wszystkich dane umożliwiające wyszukiwanie, które zostały udostępnione jako dokumenty JSON. [Add, Update lub usuwanie dokumentów interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `docs` kolekcji i `index` operacji.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **POST**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Podaj dokumentów JSON (gotowych do użycia kopii kodu zostało przedstawione poniżej) w treści żądania.

4. Kliknij przycisk **wysyłania**.

![Dokumenty JSON w treści żądania](media/search-get-started-postman/postman-docs.png "dokumenty JSON w treści żądania")

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do ładowania do indeksu

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

W ciągu kilku sekund powinien pojawić się odpowiedź 201 protokołu HTTP, na liście sesji. Oznacza to, że dokumenty zostały pomyślnie utworzone. 

Jeśli otrzymasz odpowiedź 207, przekazanie co najmniej jednego dokumentu nie powiodło się. Jeśli otrzymasz odpowiedź 404, wystąpił błąd składniowy w nagłówku lub w treści żądania: zweryfikuj, czy zmiana punktu końcowego uwzględniła `/docs/index`.

> [!Tip]
> Dla wybranych źródeł danych możesz wybrać alternatywne podejście *indeksatora*, które upraszcza i zmniejsza ilość kodu wymaganą do indeksowania. Aby uzyskać więcej informacji, zobacz [Operacje indeksatora](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy indeks i dokumenty są załadowane, można wykonywać zapytania względem nich za pomocą [interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adres URL jest rozszerzona, aby uwzględnić wyrażenie zapytania, określony przy użyciu operatora wyszukiwania.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **UZYSKAĆ**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Kliknij przycisk **wysyłania**.

To zapytanie jest pusta i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![SKORZYSTAJ z wyszukiwany ciąg w polu adres URL](media/search-get-started-postman/postman-query.png "SKORZYSTAJ z wyszukiwany ciąg w polu adres URL")

Wypróbuj kilka innych przykładów zapytanie, aby uzyskać pewne pojęcie składni. Można zrobić w przypadku wyszukiwania na ciąg zapytania verbatim $filter, ograniczyć zestaw wyników, zakres wyszukiwania do określonych pól i nie tylko.

Wymiany się bieżący adres URL z nich, które poniżej, klikając **wysyłania** każdym razem, aby wyświetlić wyniki.

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
Można również użyć [uzyskać statystyki](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) wyszukiwać liczby dokumentów do indeksu rozmiar: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06`
```

Dodawanie `/stats` do adresu URL zwraca informacje o indeksie. W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Uzyskaj informacje o indeksie](media/search-get-started-postman/postman-system-query.png "uzyskać informacje o indeksie")

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. `?` Oddziela ścieżkę adresu URL od ciągu zapytania, zaś & oddziela każdą "nazwa = wartość" pary ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

## <a name="clean-up"></a>Czyszczenie

Podczas pracy w ramach własnej subskrypcji jest dobrym pomysłem na końcu projektu ustalić, czy nadal potrzebujesz zasoby utworzone. Po lewej stronie umożliwia uruchamianie zasobów kosztów pieniądze. Możesz usunąć zasoby pojedynczo lub usunąć grupę zasobów, aby usunąć cały zestaw zasobów.

Możesz znaleźć i zarządzanie zasobami w portalu przy użyciu **wszystkie zasoby** lub **grup zasobów** łącze w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu w celu pozostania w ramach limitu. 

## <a name="next-steps"></a>Następne kroki

Klienci interfejsu REST są nieocenieni dla spontanicznej eksploracji, ale teraz, gdy wiesz, jak działają interfejsy API REST, możesz pójść dalej, używając kodu. Zalecanym następnym krokiem zobacz następujące łącze:

+ [Szybki start: Tworzenie indeksu przy użyciu zestawu .NET SDK](search-get-started-dotnet.md)
