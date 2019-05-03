---
title: 'Szybki start: Tworzenie, obciążenia i tworzenie zapytań względem indeksu przy użyciu programu PowerShell i interfejsu API REST — usługa Azure Search'
description: Tworzenie, obciążenia i tworzenie zapytań względem indeksu przy użyciu programu PowerShell Invoke RestMethod i interfejsu API REST usługi Azure Search.
ms.date: 05/02/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9459ab44f366c87660297a8564534156a56777bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024139"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Szybki start: Tworzenie indeksu usługi Azure Search przy użyciu programu PowerShell i interfejsu API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (interfejs API REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Ten artykuł przeprowadzi Cię przez proces tworzenia, ładowanie i wykonywanie zapytań usługi Azure Search [indeksu](search-what-is-an-index.md) przy użyciu programu PowerShell i [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). Definicja indeksu i którą można przeszukiwać zawartość jest dostępna w treści żądania jako poprawnie sformułowana zawartość JSON.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi i narzędzia są używane w tym przewodniku Szybki Start. 

[Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa dla tego przewodnika Szybki Start. 

[Program PowerShell 5.1 lub nowszej](https://github.com/PowerShell/PowerShell)przy użyciu [Invoke RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) czynności sekwencyjne i interaktywne.

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania interfejsu REST wymagają adresu URL usługi i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona przy użyciu obu, więc jeśli usługa Azure Search została dodana do Twojej subskrypcji, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com/)i w usłudze wyszukiwania **Przegląd** strony, Pobierz adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W **ustawienia** > **klucze**, Pobierz klucz administratora dla pełnych praw w usłudze. Istnieją dwa klucze administratora wymienne, podany w celu zachowania ciągłości w razie potrzeby do jednego przerzucania. Dodawanie, modyfikowanie i usuwanie obiektów, można użyć zarówno klucz podstawowy lub pomocniczy w odpowiedzi na żądania.

![Pobierz HTTP punktu końcowego i klucza dostępu](media/search-fiddler/get-url-key.png "uzyskać HTTP punktu końcowego i klucza dostępu")

Wszystkie żądania wymagają klucza interfejsu api na każde żądanie wysłane do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="connect-to-azure-search"></a>Łączenie z usługą Azure Search

W programie PowerShell utworzyć **$headers** obiekt ma być przechowywany, typu zawartości i klucz interfejsu API. Musisz ustawić tego pliku nagłówkowego raz na czas trwania sesji, ale doda go do każdego żądania. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Tworzenie **$url** obiekt, który określa usługę indeksuje kolekcji. `mydemo` Nazwa usługi jest przeznaczony jako symbol zastępczy. Zastąp go za pomocą usługi prawidłowe wyszukiwania w bieżącej subskrypcji, w tym przykładzie.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2019-05-06"
```

Uruchom **Invoke RestMethod** Wyślij żądanie Pobierz do usługi i sprawdzić połączenie. Dodaj **ConvertTo Json** tak, aby wyświetlić odpowiedzi wysłana odpowiedź od usługi.

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

Jeśli używasz portalu indeksu musi istnieć w usłudze przed załadowaniem danych. Ten krok definiuje indeks i wypycha go do usługi. [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) jest używany w tym kroku.

Wymagane elementy indeksu obejmują nazwy i kolekcji pól. Kolekcja pól definiuje strukturę *dokumentu*. Każde pole ma nazwę, typ i atrybuty, które określają, jak jest używana (na przykład, czy jest pełnotekstowe wyszukiwanie, filtrowanie lub pobieranie w wynikach wyszukiwania). W ramach indeksu, jednego pola typu `Edm.String` musi zostać wyznaczona jako *klucz* dokumentu tożsamości.

Ten indeks o nazwie "hotels" i ma definicje pól, pokazane poniżej. Określa definicję indeksu [analizatora języków](index-add-language-analyzers.md) dla `description_fr` pola, ponieważ jest ono przeznaczone do przechowywania tekstu w języku francuskim, które dodamy w przykładzie nowsze.

Wklej w tym przykładzie w programie PowerShell, aby utworzyć **$body** obiektu zawierającego schemat indeksu.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Ustaw identyfikator URI kolekcji indeksów na usługę i *hotele* indeksu.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2019-05-06"
```

Uruchom polecenie z **$url**, **$headers**, i **$body** do utworzenia indeksu w usłudze. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Wyniki powinny wyglądać podobnie do poniższego (obcięty do pierwsze dwa pola dla zwięzłości):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> W celu weryfikacji, można również sprawdzać listy indeksów w portalu, lub uruchom ponownie polecenie używany do sprawdzenia połączenia z usługą, aby wyświetlić *hotele* indeksu na liście kolekcji indeksów.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ładowanie dokumentów

Aby wypchnąć dokumenty, należy użyć żądania HTTP POST do punktu końcowego adresu URL Twojego indeksu. Interfejs API REST dla tego zadania jest [Add, Update lub usuwanie dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Wklej w tym przykładzie w programie PowerShell, aby utworzyć **$body** obiektu zawierającego dokumenty przeznaczone do przekazania. 

To żądanie zawiera dwa pełne i jeden rekord częściowe. Częściowe rekordu pokazuje przekazać niekompletne dokumentów. `@search.action` Parametr określa, jak indeksowanie jest wykonywane. Prawidłowe wartości to przekazywanie, scalanie, mergeOrUpload i delete. Zachowanie mergeOrUpload albo tworzy nowy dokument dla hotelId = 3, lub aktualizuje zawartość, jeśli już istnieje.

```powershell
$body = @"
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Ustaw punkt końcowy *hotele* kolekcji docs i obejmują operacja indeksu (indeksy/hotels/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06"
```

Uruchom polecenie z **$url**, **$headers**, i **$body** ładowanie dokumentów do indeksu hotels.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Wyniki powinny wyglądać podobnie do poniższego przykładu. Wyświetlony kod stanu 201. Aby uzyskać opis wszystkich kodów stanu, zobacz [kodów stanu HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
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
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

W tym kroku przedstawiono sposób tworzenia zapytań względem indeksu przy użyciu [interfejsu API wyszukiwania w dokumentach](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Ustaw punkt końcowy *hotele* kolekcji docs i Dodaj **wyszukiwania** parametru, aby dołączyć ciągi zapytań. Ten ciąg jest puste wyszukiwanie i zwraca unranked wykaz wszystkich dokumentów.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*'
```

Uruchom polecenie, aby wysłać **$url** do usługi.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Wypróbuj kilka innych przykładów zapytanie, aby uzyskać pewne pojęcie składni. Można zrobić w przypadku wyszukiwania na ciąg zapytania verbatim $filter, ograniczyć zestaw wyników, zakres wyszukiwania do określonych pól i nie tylko.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Czyszczenie 

Jeśli nie są już potrzebne, należy usunąć indeks. Usługi w warstwie bezpłatna jest ograniczona do trzech indeksów. Możesz chcieć usunąć wszystkie indeksy nie aktywnie używasz, aby można było przechodzić przez innych samouczków.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Kolejne kroki

Spróbuj dodać francuska opisy do indeksu. Poniższy przykład zawiera francuskie ciągi i pokazuje wyszukiwania dodatkowe akcje. Użyj mergeOrUpload, aby utworzyć lub dodać do istniejących pól. Następujące ciągi muszą być zakodowane w formacie UTF-8.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
