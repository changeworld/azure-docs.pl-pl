---
title: Odkrywaj interfejsy API REST w narzędziu Postman lub Fiddler — usługa Azure Search
description: Jak za pomocą narzędzia Postman lub Fiddler wystawiania żądań HTTP i interfejsu API REST wywołań do usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c99380faee8fd1bc42922f7f0e367edde1154a9b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368905"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman-or-fiddler"></a>Szybki start: Odkrywaj interfejsy API REST usługi Azure Search przy użyciu narzędzi Postman lub Fiddler

Jednym z najprostszych sposobów eksploracji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) jest wykorzystanie narzędzia Postman lub Fiddler do sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

> [!div class="checklist"]
> * Pobieranie narzędzia do testowania internetowego interfejsu api
> * Pobieranie klucza api-key i punktu końcowego dla Twojej usługi wyszukiwania
> * Konfigurowanie nagłówków żądania
> * Tworzenie indeksu
> * Ładowanie indeksu
> * Przeszukiwanie indeksu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), a następnie [zarejestruj się w usłudze Azure Search](search-create-service-portal.md).

## <a name="download-tools"></a>Pobieranie narzędzi

Następujące narzędzia są powszechnie używane przy projektowaniu aplikacji internetowych, ale jeśli znasz inne narzędzie, instrukcje zawarte w tym artykule nadal będą miały zastosowanie.

+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
+ [Telerik Fiddler](https://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Pobieranie klucza api-key i punktu końcowego

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. W witrynie Azure portal w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://my-service-name.search.windows.net`.

2. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.


## <a name="configure-headers"></a>Konfigurowanie nagłówków

Każde narzędzie utrzymuje informacje o nagłówku żądania dla tej sesji, co oznacza, że musisz tylko raz wprowadzić adres URL punktu końcowego, wersję interfejsu api, klucz api-key i typ zawartości.

Pełny adres URL powinien wyglądać podobnie do poniższego przykładu, ale musisz zapewnić prawidłowy zamiennik dla **`my-app`** nazwy symbolu zastępczego: `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

Kompozycja adresu URL usługi obejmuje następujące elementy:

+ Prefiks protokołu HTTPS.
+ Adres URL usługi uzyskany z portalu.
+ Zasób, operację tworzącą obiekt w Twojej usłudze. W tym kroku jest to indeks o nazwie *hotele*.
+ Parametr api-version, czyli wymagany ciąg małych liter określony jako „?api-version=2017-11-11” dla bieżącej wersji. [Wersje interfejsu API](search-api-versions.md) są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typ zawartości i klucz api-key, opisane w poprzedniej sekcji:

    api-key: <placeholder>
    Content-Type: application/json


### <a name="postman"></a>Postman

Sformułuj takie żądanie, jak przedstawiono na poniższym zrzucie ekranu. Wybierz **PUT** jako zlecenie. 

![Nagłówek żądania narzędzia Postman][6]

### <a name="fiddler"></a>Fiddler

Sformułuj takie żądanie, jak przedstawiono na poniższym zrzucie ekranu. Wybierz **PUT** jako zlecenie. Narzędzie Fiddler dodaje `User-Agent=Fiddler`. Możesz wkleić dwa dodatkowe nagłówki żądania w nowych wierszach poniżej. Uwzględnij typ zawartości i klucz api-key dla swojej usługi, używając klucza dostępu administratora dla usługi.

![Nagłówek żądania narzędzia Fiddler][1]

> [!Tip]
> Wyłącz ruch internetowy do ukrycia nadmiarowe, niezależne aktywności protokołu HTTP. W narzędziu Fiddler firmy **pliku** menu, wyłącz **Przechwytywanie ruchu**. 

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Treść żądania zawiera definicję indeksu. Dodanie treści żądania kończy żądanie, które spowoduje utworzenie Twojego indeksu.

Oprócz nazwy indeksu najważniejszym składnikiem żądania jest kolekcja pól. Kolekcja pól definiuje schemat indeksu. W każdym polu określ jego typ. Pola ciągów są używane podczas wyszukiwania pełnotekstowego, możesz więc rzutować dane numeryczne jako ciągi, jeśli potrzebujesz, aby ta zawartość dawała możliwość wyszukiwania.

Atrybuty w polu określają dozwoloną akcję. Interfejsy API REST domyślnie umożliwiają wiele akcji. Na przykład wszystkie ciągi domyślnie umożliwiają wyszukiwanie, pobieranie, filtrowanie i tworzenie aspektów. Często wystarczy ustawić atrybuty, gdy należy wyłączyć zachowanie. Aby uzyskać więcej informacji na temat atrybutów, zobacz [Create Index (REST) (Tworzenie indeksu (REST))](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

Jeśli otrzymasz odpowiedź 504 protokołu HTTP, sprawdź, czy adres URL określa protokół HTTPS. Jeśli zobaczysz odpowiedź 400 lub 404 protokołu HTTP, sprawdź treść żądania, aby zweryfikować, czy nie było żadnych błędów podczas kopiowania i wklejania. Odpowiedź 403 protokołu HTTP zazwyczaj wskazuje na problem z kluczem api-key (nieprawidłowy klucz lub problem ze składnią klucza api-key).


### <a name="postman"></a>Postman

Skopiuj definicję indeksu do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie kliknij przycisk **Wyślij** u góry z prawej strony, aby wysłać ukończone żądanie.

![Treść żądania narzędzia Postman][8]

### <a name="fiddler"></a>Fiddler

Skopiuj definicję indeksu do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie kliknij przycisk **Wykonaj** u góry z prawej strony, aby wysłać ukończone żądanie.

![Treść żądania narzędzia Fiddler][7]

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Tworzenie indeksu i wypełnianie indeksu to oddzielne kroki. W usłudze Azure Search indeks zawiera wszystkich dane umożliwiające wyszukiwanie, które zostały udostępnione jako dokumenty JSON. Aby zapoznać się z interfejsem API dla tej operacji, zobacz [Add, update, or delete documents (REST) (Dodawanie, aktualizowanie lub usuwanie dokumentów (REST))](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Dla tego kroku zmień zlecenie na **POST**.
+ Zmień punkt końcowy, aby uwzględnić `/docs/index`. Pełny adres URL powinien wyglądać jak `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
+ Zachowaj nagłówki żądania w takim stanie, w jakim są. 

Obszar Request Body (Treść żądania) zawiera cztery dokumenty, które mają zostać dodane do indeksu hotels.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville"
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


### <a name="postman"></a>Postman

Zmień zlecenie na **POST**. Zmień adres URL, aby uwzględnić `/docs/index`. Skopiuj dokumenty do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie wykonaj żądanie.

![Ładunek żądania narzędzia Postman][10]

### <a name="fiddler"></a>Fiddler

Zmień zlecenie na **POST**. Zmień adres URL, aby uwzględnić `/docs/index`. Skopiuj dokumenty do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie wykonaj żądanie.

![Ładunek żądania narzędzia Fiddler][9]

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu
Teraz, gdy indeks i dokumenty są załadowane, można wykonywać zapytania względem nich za pomocą [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents) interfejsu API REST.

+ Dla tego kroku zmień zlecenie na **GET**.
+ Zmień punkt końcowy, aby uwzględnić parametry zapytania wraz z ciągami wyszukiwania. Adres URL zapytania może wyglądać jak `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ Zachowywanie nagłówków żądania w takim stanie, w jakim są

To zapytanie wyszukuje termin „motel” i zwraca liczbę dokumentów w wynikach wyszukiwania. Po kliknięciu przycisku **Wyślij** żądanie i odpowiedź powinny wyglądać podobnie jak na poniższym zrzucie ekranu z narzędzia Postman. Kod stanu powinien być równy 200.

 ![Odpowiedź na zapytanie narzędzia Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Porady dotyczące uruchamiania naszych przykładowych zapytań w narzędziu Fiddler

Poniższe przykładowe zapytanie można znaleźć w artykule [Search Index operation (Azure Search API) (Operacje wyszukiwania indeksu (interfejs API usługi Azure Search))](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Wiele przykładowych zapytań w tym artykule zawiera spacje, które nie są dozwolone w narzędziu Fiddler. Przed wklejeniem ciągu zapytania oraz podjęciem próby jego wykonania w narzędziu Fiddler zastąp każdą spację znakiem +.

**Przed zastąpieniem spacji (w zapytaniu lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**Po zastąpieniu spacji znakiem + (w zapytaniu lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="get-index-properties"></a>Pobierz właściwości indeksu
Zapytania możesz także wykonywać względem informacji o systemie, aby uzyskać informacje o liczbie dokumentów i użyciu przestrzeni dyskowej: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Zapytanie do systemu narzędzia Postman][12]

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. Znak ? oddziela ścieżkę adresu URL od ciągu zapytania, zaś & oddziela każdą parę „nazwa=wartość” w ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

Aby uzyskać więcej informacji dotyczących tego interfejsu API, zobacz [Get Index Statistics (REST) (Pobieranie statystyki indeksu (REST))](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


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