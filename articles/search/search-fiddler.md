---
title: Odkrywaj interfejsy API REST w narzędziu Fiddler lub Postman (interfejs REST usługi Azure Search) | Microsoft Docs
description: Jak używać narzędzia Fiddler lub Postman do wystawiania żądań HTTP i wywołań interfejsu API REST do usługi Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/20/2018
ms.author: heidist
ms.openlocfilehash: eba41086da645c2ff5cee65f9395267227cb1c11
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Odkrywaj interfejsy API REST usługi Azure Search przy użyciu narzędzia Fiddler lub Postman

Jednym z najprostszych sposobów eksploracji [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) jest wykorzystanie narzędzia Fiddler lub Postman do sformułowania żądań HTTP i sprawdzenia odpowiedzi. Za pomocą odpowiednich narzędzi i niniejszej instrukcji możesz wysyłać żądania i wyświetlać odpowiedzi przed napisaniem jakiegokolwiek kodu.

> [!div class="checklist"]
> * Pobieranie narzędzia do testowania internetowego interfejsu api
> * Pobieranie klucza api-key i punktu końcowego dla Twojej usługi wyszukiwania
> * Konfigurowanie nagłówków żądania
> * Tworzenie indeksu
> * Ładowanie indeksu
> * Przeszukiwanie indeksu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), a następnie [zarejestruj się w usłudze Azure Search](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Pobieranie i instalowanie narzędzi

Następujące narzędzia są powszechnie używane przy projektowaniu aplikacji internetowych, ale jeśli znasz inne narzędzie, instrukcje zawarte w tym artykule nadal będą miały zastosowanie.

+ [Aplikacja klasyczna narzędzia Postman](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Pobieranie klucza api-key i punktu końcowego

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. W witrynie Azure Portal otwórz stronę usługi wyszukiwania z pulpitu nawigacyjnego lub [znajdź swoją usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.
2. Pobierz punkt końcowy, wybierając kolejno pozycje **Omówienie** > **Podstawy** > **Adres URL**. Przykładowy punkt końcowy może wyglądać podobnie jak `https://my-service-name.search.windows.net`.
3. Pobierz klucz api-key w pozycji **Ustawienia** > **Klucze**. W przypadku gdy chcesz przerzucić klucze, istnieją dwa klucze administratora w celu zapewnienia nadmiarowości. Klucze administratora przyznają w usłudze uprawnienia do zapisu niezbędne do tworzenia i ładowania indeksów. Dla operacji zapisu możesz użyć zarówno klucza podstawowego, jak i pomocniczego.

## <a name="configure-request-headers"></a>Konfigurowanie nagłówków żądania

Każde narzędzie utrzymuje informacje o nagłówku żądania dla tej sesji, co oznacza, że musisz tylko raz wprowadzić adres URL punktu końcowego, wersję interfejsu api, klucz api-key i typ zawartości.

Pełny adres URL powinien wyglądać podobnie do poniższego przykładu, ale musisz zapewnić prawidłowy zamiennik dla **`my-app`** nazwy symbolu zastępczego: `https://my-app.search.windows.net/indexes/hotels?api-version=2017-11-11`

Kompozycja adresu URL usługi obejmuje następujące elementy:

+ Prefiks protokołu HTTPS.
+ Adres URL usługi uzyskany z portalu.
+ Zasób, operację tworzącą obiekt w Twojej usłudze. W tym kroku jest to indeks o nazwie hotels.
+ Parametr api-version, czyli wymagany ciąg małych liter określony jako „?api-version=2017-11-11” dla bieżącej wersji. [Wersje interfejsu API](search-api-versions.md) są regularnie aktualizowane. Uwzględnienie parametru api-version w każdym żądaniu daje pełną kontrolę nad tym, która z nich jest używana.  

Kompozycja nagłówka żądania zawiera dwa elementy, typ zawartości i klucz api-key, opisane w poprzedniej sekcji:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Sformułuj żądanie takie, jak to przedstawiono na poniższym zrzucie ekranu. Wybierz **PUT** jako zlecenie. Narzędzie Fiddler dodaje `User-Agent=Fiddler`. Możesz wkleić dwa dodatkowe nagłówki żądania w nowych wierszach poniżej. Uwzględnij typ zawartości i klucz api-key dla swojej usługi, używając klucza dostępu administratora dla usługi.

![Nagłówek żądania narzędzia Fiddler][1]

> [!Tip]
> Możesz wyłączyć ruch internetowy w celu ukrycia dodatkowej aktywności protokołu HTTP niezwiązanej z wykonywanymi zadaniami. W narzędziu Fiddler przejdź do menu **Plik** i wyłącz opcję **Przechwytywanie ruchu**. 

### <a name="postman"></a>Postman

Sformułuj żądanie takie, jak to przedstawiono na poniższym zrzucie ekranu. Wybierz **PUT** jako zlecenie. 

![Nagłówek żądania narzędzia Postman][6]

## <a name="create-the-index"></a>Tworzenie indeksu

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

### <a name="fiddler"></a>Fiddler

Skopiuj definicję indeksu do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie kliknij przycisk **Wykonaj** u góry z prawej strony, aby wysłać zakończone żądanie.

![Treść żądania narzędzia Fiddler][7]

### <a name="postman"></a>Postman

Skopiuj definicję indeksu do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie kliknij przycisk **Wyślij** u góry z prawej strony, aby wysłać zakończone żądanie.

![Treść żądania narzędzia Postman][8]

## <a name="load-documents"></a>Ładowanie dokumentów

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

### <a name="fiddler"></a>Fiddler

Zmień zlecenie na **POST**. Zmień adres URL, aby uwzględnić `/docs/index`. Skopiuj dokumenty do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie wykonaj żądanie.

![Ładunek żądania narzędzia Fiddler][9]

### <a name="postman"></a>Postman

Zmień zlecenie na **POST**. Zmień adres URL, aby uwzględnić `/docs/index`. Skopiuj dokumenty do treści żądania, podobnie jak na poniższym zrzucie ekranu, a następnie wykonaj żądanie.

![Ładunek żądania narzędzia Postman][10]

## <a name="query-the-index"></a>Wykonywanie zapytań względem indeksu
Teraz, gdy indeks i dokumenty są załadowane, możesz wykonywać zapytania względem nich. Aby uzyskać więcej informacji dotyczących tego interfejsu API, zobacz [Search Documents (REST) (Wyszukiwanie dokumentów (REST))](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

+ Dla tego kroku zmień zlecenie na **GET**.
+ Zmień punkt końcowy, aby uwzględnić parametry zapytania wraz z ciągami wyszukiwania. Adres URL zapytania może wyglądać jak `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ Zachowywanie nagłówków żądania w takim stanie, w jakim są

To zapytanie wyszukuje termin „motel” i zwraca liczbę dokumentów w wynikach wyszukiwania. Żądanie i odpowiedź, po kliknięciu przycisku **Wyślij**, powinny wyglądać podobnie jak na poniższym zrzucie ekranu dla narzędzia Postman. Kod stanu powinien być równy 200.

 ![Odpowiedź na zapytanie narzędzia Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Porady dotyczące uruchamiania naszych przykładowych zapytań w narzędziu Fiddler

Poniższe przykładowe zapytanie można znaleźć w artykule [Search Index operation (Azure Search API) (Operacje wyszukiwania indeksu (interfejs API usługi Azure Search))](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Wiele przykładowych zapytań w tym artykule zawiera spacje, które nie są dozwolone w narzędziu Fiddler. Przed wklejeniem ciągu zapytania oraz podjęciem próby jego wykonania w narzędziu Fiddler zastąp każdą spację znakiem +.

**Przed zastąpieniem spacji (w zapytaniu lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**Po zastąpieniu spacji znakiem + (w zapytaniu lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

## <a name="query-index-properties"></a>Właściwości indeksu zapytania
Zapytania możesz także wykonywać względem informacji o systemie, aby uzyskać informacje o liczbie dokumentów i użyciu przestrzeni dyskowej: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

W narzędziu Postman Twoje żądanie powinno wyglądać podobnie do poniższego, zaś odpowiedź zawiera liczbę dokumentów i użyte miejsce w bajtach.

 ![Zapytanie do systemu narzędzia Postman][12]

Zwróć uwagę, że składnia parametru api-version różni się. Dla tego żądania użyj `?`, aby dołączyć parametr api-version. Znak ? oddziela ścieżkę adresu URL od ciągu zapytania, zaś & oddziela każdą parę „nazwa=wartość” w ciągu zapytania. Dla tego zapytania parametr api-version jest pierwszym i jedynym elementem ciągu zapytania.

Aby uzyskać więcej informacji dotyczących tego interfejsu API, zobacz [Get Index Statistics (REST) (Pobieranie statystyki indeksu (REST))](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Porady dotyczące wyświetlania statystyki indeksu w narzędziu Fiddler

W narzędziu Fiddler kliknij kartę **Inspectors (Inspektorzy)**, potem kliknij kartę **Headers (Nagłówki)**, a następnie wybierz format JSON. Powinny zostać wyświetlone informacje o liczbie dokumentów i rozmiarze magazynu (w KB).

## <a name="next-steps"></a>Następne kroki

Klienci interfejsu REST są nieocenieni dla spontanicznej eksploracji, ale teraz, gdy wiesz, jak działają interfejsy API REST, możesz pójść dalej, używając kodu. Aby poznać dalsze czynności, zobacz następujące linki:

+ [Tworzenie indeksu (REST)](search-create-index-rest-api.md)
+ [Importowanie danych (REST)](search-import-data-rest-api.md)
+ [Przeszukiwanie indeksu (REST)](search-query-rest-api.md)

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