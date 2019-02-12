---
title: Tworzenie definicji indeksu i pojęcia — usługa Azure Search
description: Wprowadzenie do indeksu terminy i pojęcia, które w usłudze Azure Search, łącznie z części zamiennych i struktura fizyczna.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000954"
---
# <a name="create-a-basic-index-in-azure-search"></a>Tworzenie podstawowego indeksu w usłudze Azure Search

W usłudze Azure Search *indeksu* jest trwałym magazynem *dokumentów* i innych konstrukcji używanych do odfiltrowanych i pełnego tekstu wyszukiwania w usłudze Azure Search. Model dokument jest pojedynczą jednostką danych z możliwością wyszukiwania w indeksie. Na przykład sklep internetowy może mieć dokument dla każdego sprzedawanego produktu, a organizacja medialna — dla każdego artykułu itp. W przełożeniu na lepiej znane pojęcia bazodanowe: *indeks* jest podobny do *tabeli*, a *dokumenty* są w przybliżeniu równe *wierszom* w tabeli.

Podczas dodawania lub przekazać indeksu usługi Azure Search tworzy struktury fizyczne, na podstawie schematu, których udzielasz. Na przykład jeśli pola w indeksie jest oznaczony jako wyszukiwanie, odwrócony tworzony jest indeks dla tego pola. Później podczas dodawania lub przekazywać dokumenty lub przesyłania zapytań wyszukiwań do usługi Azure Search są wysyłane żądania do konkretnego indeksu w usłudze wyszukiwania. Ładowanie pola z wartościami dokumentu jest nazywany *indeksowania* i przetwarzanie danych.

Można utworzyć indeksu w portalu [interfejsu API REST](search-create-index-rest-api.md), lub [zestawu .NET SDK](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Składniki indeksu

Schematycznie indeksu usługi Azure Search składa się z następujących elementów. 

[ *Kolekcję pól* ](#fields-collection) jest zazwyczaj największych częścią indeksu, gdzie nosi nazwę każdego pola, wpisane, a z dopuszczalny rozmiar zachowania, które określają, jak są używane. Inne elementy obejmują [sugestory](#suggesters), [profile oceniania](#scoring-profiles), [analizatory](#analyzers) z części składowe, które umożliwiają dostosowanie, a [CORS](#cors) Opcje.

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Kolekcja pól i uznanie autorstwa
W trakcie definiowania schematu musisz określić nazwę, typ i atrybuty każdego pola w indeksie. Typ pola klasyfikuje dane, które są w nim przechowywane. Atrybuty są ustawiane dla poszczególnych pól, aby określić sposób użycia pola. W poniższych tabelach zostały wyszczególnione typy i atrybuty, które możesz określić.

### <a name="data-types"></a>Typy danych
| Type | Opis |
| --- | --- |
| *Edm.String* |Tekst, który opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego (dzielenie wyrazów, analiza słowotwórcza itp.). |
| *Collection(Edm.String)* |Lista ciągów, które opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego. Nie ma teoretycznej górnej granicy liczby elementów w kolekcji, ale rozmiar ładunku w kolekcjach jest ograniczony do 16 MB. |
| *Edm.Boolean* |Zawiera wartości prawda/fałsz. |
| *Edm.Int32* |32-bitowe wartości całkowite. |
| *Edm.Int64* |64-bitowe wartości całkowite. |
| *Edm.Double* |Dane liczbowe o podwójnej precyzji. |
| *Edm.DateTimeOffset* |Wartości daty i godziny przedstawione w formacie OData 4 (np. w formacie `yyyy-MM-ddTHH:mm:ss.fffZ` lub `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Punkt przedstawiający lokalizację geograficzną na świecie. |

Dowiedz się więcej na temat [typów danych obsługiwanych przez usługę Azure Search w tym miejscu](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Atrybuty indeksu
| Atrybut | Opis |
| --- | --- |
| *Klucz* |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| *Pobieranie* |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| *Filtrowanie* |Umożliwia używanie pola w zapytaniach filtrów. |
| *Sortowanie* |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| *Tworzenie aspektów* |Umożliwia używanie pola w strukturze [nawigacji aspektowej](search-faceted-navigation.md) podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| *Wyszukiwanie* |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |

Dowiedz się więcej na temat [atrybutów indeksów usługi Azure Search w tym miejscu](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="suggesters"></a>Funkcje sugestii
Sugestora to sekcja schemat, który definiuje pola w indeksie, które są używane do obsługi automatycznego uzupełniania wpisywaniu lub Autouzupełnianie zapytań wyszukiwania. Zazwyczaj ciągi częściowe są wysyłane do sugestie (Azure Search interfejs API REST usługi), podczas zapytania wyszukiwania wpisywany przez użytkownika, a interfejs API zwraca zbiór proponowanych fraz. Sugestora, który definiuje w indeksie określa pola, które są używane do tworzenia wpisywania z wyprzedzeniem wyszukiwane terminy. Aby uzyskać więcej informacji, zobacz [Dodaj sugestory](index-add-suggesters.md) szczegółowe informacje dotyczące konfiguracji.

## <a name="scoring-profiles"></a>Profile oceniania

Profil oceniania to sekcja schemat, który definiuje oceniania zachowań niestandardowych, które umożliwiają mają wpływ na elementy, które są wyświetlane w wynikach wyszukiwania. Profile oceniania składają się wag pól i funkcji. Z nich korzystać, należy wskazać profil według nazwy w ciągu zapytania.

Domyślny profil oceniania działa w tle, można obliczyć wyniku wyszukiwania dla każdego elementu w zestawie wyników. Możesz użyć wewnętrznego, bez nazwy profilu oceniania. Alternatywnie można ustawić defaultScoringProfile do użycia niestandardowego profilu jako domyślnego, wywoływana zawsze wtedy, gdy nie określono profilu niestandardowego w ciągu zapytania.

Aby uzyskać więcej informacji, zobacz [dodać profile oceniania](index-add-scoring-profiles.md).

## <a name="analyzers"></a>Analizatory

Element analizatory ustawia nazwę analizatora języków dla pola. Aby uzyskać zestaw dozwolonych wartości, zobacz [analizatory języka w usłudze Azure Search](index-add-language-analyzers.md). Tej opcji można używać tylko w przypadku pola z możliwością wyszukiwania i nie można ustawić razem z wartościami **searchAnalyzer** lub **indexAnalyzer**. Po wybraniu Analizator nie można zmienić dla pola.

## <a name="cors"></a>CORS

JavaScript po stronie klienta nie można wywołać dowolnych interfejsów API domyślnie, ponieważ przeglądarka uniemożliwi wszystkich żądań cross-origin. Aby zezwolić na współużytkowanie zapytań do indeksu, Włącz mechanizm CORS (Cross-Origin Resource Sharing), ustawiając **corsOptions** atrybutu. Ze względów bezpieczeństwa kwerendy interfejsów API obsługę mechanizmu CORS. 

Do obsługi mechanizmu CORS można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): To jest lista źródeł, które będą mieć dostęp do Twojego indeksu. Oznacza to, że każdy kod JavaScript pochodzący z tych źródeł będzie można wykonywać zapytania w indeksie (przy założeniu, że zawiera poprawny klucz api-key). Każde źródło ma zazwyczaj postać `protocol://<fully-qualified-domain-name>:<port>` chociaż `<port>` jest często pomijane. Zobacz [Cross-origin resource sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) Aby uzyskać więcej informacji.

  Jeśli chcesz zezwolić na dostęp do wszystkich źródeł, Uwzględnij `*` jako pojedynczy element **allowedOrigins** tablicy. *Nie jest to zalecane praktyki dla usługi wyszukiwania w środowisku produkcyjnym* , ale często jest to przydatne w przypadku programowania i debugowania.

+ **Atrybut maxAgeInSeconds** (opcjonalnie): Przeglądarki używają tej wartości można określić czas trwania (w sekundach) do odpowiedzi wstępnego CORS pamięci podręcznej. To musi być nieujemną liczbą całkowitą. Im większa wartość ta jest, Lepsza wydajność, ale tym dłużej będzie trwało zmian zasad CORS zaczęły obowiązywać. Jeśli nie jest ustawiona, będzie używany domyślny czas trwania wynoszącą 5 minut.

## <a name="next-steps"></a>Kolejne kroki

Po zrozumieniu kompozycja indeksu, można nadal w portalu, aby utworzyć pierwszy indeks.

> [!div class="nextstepaction"]
> [Dodawanie indeksu (portal)](search-create-index-portal.md)