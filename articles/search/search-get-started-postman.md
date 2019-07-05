---
title: 'Szybki start: Tworzenie, obciążenia i zapytań indeksy przy użyciu interfejsów API REST — usługa Azure Search i narzędzia Postman'
description: Dowiedz się, jak wywołać z usługi Azure Search interfejsów API REST przy użyciu narzędzia Postman i przykładowe dane i definicje.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b3c5c08e9862d59481573b501045466d6d691a77
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485584"
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

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), a następnie [zarejestruj się w usłudze Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

+ [Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) lub [narzędzia Telerik Fiddler](https://www.telerik.com/fiddler) służy do wysyłania żądań do usługi Azure Search.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-get-started-postman/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

W tej sekcji narzędzie usługi sieci web wybranego do konfigurowania połączeń do usługi Azure Search. Każde narzędzie utrzymuje informacje o nagłówku żądania dla tej sesji, co oznacza, że należy wprowadzić klucz interfejsu api i Content-Type jeden raz.

Dla dowolnego z tych narzędzi muszą wybrać polecenie (GET, POST, PUT i tak dalej), podaj adres URL punktu końcowego, a w przypadku niektórych zadań, należy zapewnić JSON w treści żądania. Zamień nazwy usługi wyszukiwania (YOUR-SEARCH-SERVICE-NAME) Nieprawidłowa wartość. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

Zwróć uwagę, prefiks protokołu HTTPS, nazwę usługi, nazwa obiektu (w tym przypadku kolekcja indeksy), a [parametru api-version](search-api-versions.md). Api-version jest wymagana, małe litery ciągu, określony jako `?api-version=2019-05-06` dla bieżącej wersji. Wersje interfejsu API są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typu zawartości, a także klucz interfejsu api używany do uwierzytelniania usługi Azure Search. Zastąp klucz administratora interfejsu API (YOUR-ADMIN-API-KEY) prawidłową wartość. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

W narzędziu Postman Sformułuj żądanie wygląda jak poniższy zrzut ekranu. Wybierz **UZYSKAĆ** jako zlecenie, podaj adres URL, a następnie kliknij przycisk **wysyłania**. To polecenie łączy do usługi Azure Search odczytuje kolekcja indeksy i zwraca kod stanu HTTP 200 po pomyślnym nawiązaniu połączenia. Jeśli usługa ma indeksy już, odpowiedź będzie również udostępniać definicje indeksów.

![Nagłówek żądania narzędzia Postman][6]

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

W usłudze Azure Search zazwyczaj Utwórz indeks przed załadowaniem danych. [Utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index) jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `hotels` nazwę indeksu.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **umieścić**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Należy podać definicję indeksu (pokazana poniżej) w treści żądania.

4. Kliknij przycisk **wysyłania**.

![Treść żądania narzędzia Postman][8]

### <a name="index-definition"></a>Definicja indeksu

Kolekcja pól definiuje strukturę dokumentu. Każdy dokument musi zawierać następujące pola, a każde pole musi mieć typ danych. Pola ciągów są używane podczas wyszukiwania pełnotekstowego, możesz więc rzutować dane numeryczne jako ciągi, jeśli potrzebujesz, aby ta zawartość dawała możliwość wyszukiwania.

Atrybuty w polu określają dozwoloną akcję. Interfejsy API REST domyślnie umożliwiają wiele akcji. Na przykład wszystkie ciągi domyślnie umożliwiają wyszukiwanie, pobieranie, filtrowanie i tworzenie aspektów. Często wystarczy ustawić atrybuty, gdy trzeba wyłączyć to zachowanie.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

Po przesłaniu tego żądania powinna pojawić się odpowiedź 201 protokołu HTTP wskazująca, że indeks został pomyślnie utworzony. Możesz sprawdzić tę akcję w portalu, ale pamiętaj, że strona portalu ma odstępy czasowe odświeżania, więc wyrównanie może zająć minutę lub dwie.

> [!TIP]
> Jeśli otrzymasz odpowiedź 504 protokołu HTTP, sprawdź, czy adres URL określa protokół HTTPS. Jeśli zobaczysz odpowiedź 400 lub 404 protokołu HTTP, sprawdź treść żądania, aby zweryfikować, czy nie było żadnych błędów podczas kopiowania i wklejania. Odpowiedź 403 protokołu HTTP zazwyczaj wskazuje na problem z kluczem api-key (nieprawidłowy klucz lub problem ze składnią klucza api-key).

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. W usłudze Azure Search indeks zawiera wszystkich dane umożliwiające wyszukiwanie, które zostały udostępnione jako dokumenty JSON. [Add, Update lub usuwanie dokumentów interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `docs` kolekcji i `index` operacji.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **POST**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`.

3. Podaj dokumentów JSON (pokazana poniżej) w treści żądania.

4. Kliknij przycisk **wysyłania**.

![Ładunek żądania narzędzia Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Dokumenty JSON do ładowania do indeksu

Obszar Request Body (Treść żądania) zawiera cztery dokumenty, które mają zostać dodane do indeksu hotels.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

W ciągu kilku sekund na liście sesji powinna zostać wyświetlona odpowiedź 200 protokołu HTTP. Oznacza to, że dokumenty zostały pomyślnie utworzone. 

Jeśli otrzymasz odpowiedź 207, przekazanie co najmniej jednego dokumentu nie powiodło się. Jeśli otrzymasz odpowiedź 404, wystąpił błąd składniowy w nagłówku lub w treści żądania: zweryfikuj, czy zmiana punktu końcowego uwzględniła `/docs/index`.

> [!Tip]
> Dla wybranych źródeł danych możesz wybrać alternatywne podejście *indeksatora*, które upraszcza i zmniejsza ilość kodu wymaganą do indeksowania. Aby uzyskać więcej informacji, zobacz [Operacje indeksatora](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy indeks i dokumenty są załadowane, można wykonywać zapytania względem nich za pomocą [interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Adres URL jest rozszerzony do Dołącz ciąg zapytania określony przy użyciu operatora wyszukiwania.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **UZYSKAĆ**.

2. Skopiuj ten adres URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`.

3. Kliknij przycisk **wysyłania**.

To zapytanie wyszukuje termin „motel” i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![Odpowiedź na zapytanie narzędzia Postman][11]

## <a name="get-index-properties"></a>Pobierz właściwości indeksu
Zapytania możesz także wykonywać względem informacji o systemie, aby uzyskać informacje o liczbie dokumentów i użyciu przestrzeni dyskowej: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Zapytanie do systemu narzędzia Postman][12]

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. `?` Oddziela ścieżkę adresu URL od ciągu zapytania, zaś & oddziela każdą "nazwa = wartość" pary ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

Aby uzyskać więcej informacji na temat tego interfejsu API, zobacz [uzyskać indeks Statystyka interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).

## <a name="clean-up"></a>Czyszczenie

Jeśli nie potrzebujesz już usługę wyszukiwania, najszybszym sposobem wersji usług jest, usuwając grupę zasobów zawierającą usługę Azure Search. Trwałe usuwanie grupy zasobów powoduje usunięcie całej zawartości, w tym usług i wszelkich przechowywana zawartość. W portalu nazwa grupy zasobów znajduje się na stronie Przegląd każdej usługi.

## <a name="next-steps"></a>Kolejne kroki

Klienci interfejsu REST są nieocenieni dla spontanicznej eksploracji, ale teraz, gdy wiesz, jak działają interfejsy API REST, możesz pójść dalej, używając kodu. Aby poznać dalsze czynności, zobacz następujące linki:

+ [Szybki start: Tworzenie indeksu przy użyciu zestawu .NET SDK](search-create-index-dotnet.md)
+ [Szybki start: Tworzenie indeksu (REST) przy użyciu programu PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
