---
title: 'Szybki start: Odkrywaj interfejsy API REST w narzędziu Postman — usługa Azure Search'
description: Jak używać narzędzia Postman do wystawiania żądań HTTP i interfejsu API REST wywołań do usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7db3292bc5f377d9728e42994dd3a437cb59958e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024805"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Szybki start: Zapoznaj się z wyszukiwania interfejsów API REST Azure przy użyciu narzędzia Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](search-howto-dotnet-sdk.md)
>*

Jedną z najprostszych sposobów eksploracji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) jest przy użyciu narzędzia Postman lub innego narzędzia do testowania w sieci web do sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

> [!div class="checklist"]
> * Pobieranie narzędzia do testowania internetowego interfejsu api
> * Pobierz klucz i adres URL dla usługi wyszukiwania
> * Łączenie z usługą Azure Search
> * Tworzenie indeksu
> * Ładowanie indeksu
> * Przeszukiwanie indeksu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), a następnie [zarejestruj się w usłudze Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

[Aplikacja klasyczna narzędzia postman](https://www.getpostman.com/) lub [narzędzia Telerik Fiddler](https://www.telerik.com/fiddler) służy do wysyłania żądań do usługi Azure Search.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

W tej sekcji narzędzie usługi sieci web wybranego do konfigurowania połączeń do usługi Azure Search. Każde narzędzie utrzymuje informacje o nagłówku żądania dla tej sesji, co oznacza, że należy wprowadzić klucz interfejsu api i Content-Type jeden raz.

Dla dowolnego z tych narzędzi muszą wybrać polecenie (GET, POST, PUT i tak dalej), podaj adres URL punktu końcowego, a w przypadku niektórych zadań, należy zapewnić JSON w treści żądania. Pełny adres URL wygląda podobnie do następującego:

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2019-05-06

Zwróć uwagę, prefiks protokołu HTTPS, nazwę usługi, nazwa obiektu (w tym przypadku kolekcja indeksy), a [parametru api-version](search-api-versions.md). Api-version jest wymagana, małe litery ciągu, określony jako `?api-version=2019-05-06` dla bieżącej wersji. Wersje interfejsu API są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typu zawartości, a także klucz interfejsu api używany do uwierzytelniania usługi Azure Search:

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

W narzędziu Postman Sformułuj żądanie wygląda jak poniższy zrzut ekranu. Wybierz **UZYSKAĆ** jako zlecenie, podaj adres URL, a następnie kliknij przycisk **wysyłania**. To polecenie łączy do usługi Azure Search odczytuje kolekcja indeksy i zwraca kod stanu HTTP 200 po pomyślnym nawiązaniu połączenia. Jeśli usługa ma indeksy już, odpowiedź będzie również udostępniać definicje indeksów.

![Nagłówek żądania narzędzia Postman][6]

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

W usłudze Azure Search zazwyczaj Utwórz indeks przed załadowaniem danych. [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) interfejsu API REST jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `hotel` nazwę indeksu.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **PUT**
2. Skopiuj ten adres URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2019-05-06`
3. Podać definicję indeksu (pokazana poniżej) w treści żądania
4. Kliknij przycisk **wysyłania**

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

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. W usłudze Azure Search indeks zawiera wszystkich dane umożliwiające wyszukiwanie, które zostały udostępnione jako dokumenty JSON. [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) interfejsu API REST jest używany dla tego zadania. 

Adres URL jest rozszerzona, aby uwzględnić `docs` kolekcji i `index` operacji.

Aby to zrobić w narzędziu Postman:

1. Zmień zlecenie na **POST**
2. Skopiuj ten adres URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`
3. Podaj dokumentów JSON (pokazana poniżej) w treści żądania
4. Kliknij przycisk **wysyłania**

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

Teraz, gdy indeks i dokumenty są załadowane, można wykonywać zapytania względem nich za pomocą [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents) interfejsu API REST.

Adres URL jest rozszerzony do Dołącz ciąg zapytania określony przy użyciu operatora wyszukiwania.

Aby to zrobić w narzędziu Postman:

+ Zmień zlecenie na **Pobierz**
+ Skopiuj ten adres URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`
+ Kliknij przycisk **wysyłania**

To zapytanie wyszukuje termin „motel” i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![Odpowiedź na zapytanie narzędzia Postman][11]


## <a name="get-index-properties"></a>Pobierz właściwości indeksu
Zapytania możesz także wykonywać względem informacji o systemie, aby uzyskać informacje o liczbie dokumentów i użyciu przestrzeni dyskowej: `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Zapytanie do systemu narzędzia Postman][12]

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. `?` Oddziela ścieżkę adresu URL od ciągu zapytania, zaś & oddziela każdą "nazwa = wartość" pary ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

Aby uzyskać więcej informacji dotyczących tego interfejsu API, zobacz [Get Index Statistics (REST) (Pobieranie statystyki indeksu (REST))](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


## <a name="use-fiddler"></a>Używanie narzędzia Fiddler

Ta sekcja jest odpowiednikiem w poprzednich sekcjach, tylko przy użyciu programu Fiddler zrzuty ekranu i instrukcje

### <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

Sformułuj takie żądanie, jak przedstawiono na poniższym zrzucie ekranu. Wybierz **UZYSKAĆ** jako zlecenie. Narzędzie Fiddler dodaje `User-Agent=Fiddler`. Możesz wkleić dwa dodatkowe nagłówki żądania w nowych wierszach poniżej. Uwzględnij typ zawartości i klucz api-key dla swojej usługi, używając klucza dostępu administratora dla usługi.

Dla elementu docelowego skopiuj w zmodyfikowanej wersji tego adresu URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2019-05-06`

![Nagłówek żądania narzędzia Fiddler][1]

> [!Tip]
> Wyłącz ruch internetowy do ukrycia nadmiarowe, niezależne aktywności protokołu HTTP. W narzędziu Fiddler firmy **pliku** menu, wyłącz **Przechwytywanie ruchu**. 

### <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Zmień zlecenie na **umieścić**. Skopiuj zmodyfikowanej wersji tego adresu URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2019-05-06`. Skopiuj definicję indeksu podanych powyżej do treści żądania. Strona powinien wyglądać podobnie do poniższej zrzut ekranu. Kliknij przycisk **Execute** u góry po prawej stronie, aby wysłać zakończone żądanie.

![Treść żądania narzędzia Fiddler][7]

### <a name="2---load-documents"></a>2 - ładowanie dokumentów

Zmień zlecenie na **POST**. Zmień adres URL, aby uwzględnić `/docs/index`. Skopiuj dokumenty do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie wykonaj żądanie.

![Ładunek żądania narzędzia Fiddler][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Porady dotyczące uruchamiania naszych przykładowych zapytań w narzędziu Fiddler

Poniższe przykładowe zapytanie można znaleźć w artykule [Search Index operation (Azure Search API) (Operacje wyszukiwania indeksu (interfejs API usługi Azure Search))](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Wiele przykładowych zapytań w tym artykule zawiera spacje, które nie są dozwolone w narzędziu Fiddler. Przed wklejeniem ciągu zapytania oraz podjęciem próby jego wykonania w narzędziu Fiddler zastąp każdą spację znakiem +.

**Przed zastąpieniem spacji (w zapytaniu lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06

**Po zastąpieniu spacji znakiem + (w zapytaniu lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2019-05-06

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Porady dotyczące wyświetlania statystyki indeksu w narzędziu Fiddler

W narzędziu Fiddler kliknij kartę **Inspectors (Inspektorzy)**, potem kliknij kartę **Headers (Nagłówki)**, a następnie wybierz format JSON. Powinny zostać wyświetlone informacje o liczbie dokumentów i rozmiarze magazynu (w KB).

## <a name="next-steps"></a>Kolejne kroki

Klienci interfejsu REST są nieocenieni dla spontanicznej eksploracji, ale teraz, gdy wiesz, jak działają interfejsy API REST, możesz pójść dalej, używając kodu. Aby poznać dalsze czynności, zobacz następujące linki:

+ [Szybki start: Tworzenie indeksu przy użyciu zestawu .NET SDK](search-create-index-dotnet.md)
+ [Szybki start: Tworzenie indeksu (REST) przy użyciu programu PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png