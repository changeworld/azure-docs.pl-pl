---
title: Tworzenie definicji indeksu i pojęcia — usługa Azure Search
description: Wprowadzenie do indeksu terminy i pojęcia, które w usłudze Azure Search, łącznie z części zamiennych i struktura fizyczna.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 462a99ffab8038f34b1ffd038ce5c8e8ec9a8565
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024429"
---
# <a name="create-a-basic-index-in-azure-search"></a>Tworzenie podstawowego indeksu w usłudze Azure Search

W usłudze Azure Search *indeksu* jest trwałym magazynem *dokumentów* i innych konstrukcji używanych do odfiltrowanych i pełnego tekstu wyszukiwania w usłudze Azure Search. Model dokument jest pojedynczą jednostką danych z możliwością wyszukiwania w indeksie. Na przykład sklep internetowy może mieć dokument dla każdego sprzedawanego produktu, a organizacja medialna — dla każdego artykułu itp. W przełożeniu na lepiej znane pojęcia bazodanowe: *indeks* jest podobny do *tabeli*, a *dokumenty* są w przybliżeniu równe *wierszom* w tabeli.

Podczas dodawania lub przekazać indeksu usługi Azure Search tworzy struktury fizyczne, na podstawie schematu, których udzielasz. Na przykład jeśli pola w indeksie jest oznaczony jako wyszukiwanie, odwrócony tworzony jest indeks dla tego pola. Później podczas dodawania lub przekazywać dokumenty lub przesyłania zapytań wyszukiwań do usługi Azure Search są wysyłane żądania do konkretnego indeksu w usłudze wyszukiwania. Ładowanie pola z wartościami dokumentu jest nazywany *indeksowania* i przetwarzanie danych.

Można utworzyć indeksu w portalu [interfejsu API REST](search-create-index-rest-api.md), lub [zestawu .NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Zalecanym przepływie pracy

Otrzymywanych projekt prawo indeksu zwykle odbywa się za pośrednictwem wiele iteracji. Przy użyciu kombinacji narzędzia i interfejsy API mogą pomóc szybko Dokończ projektowanie.

1. Określ, czy można użyć [indeksatora](search-indexer-overview.md#supported-data-sources). Jeśli dane zewnętrzne jest jednym z obsługiwanych źródeł danych, możesz to zrobić prototypu i załadować indeks za pomocą [ **importowania danych** ](search-import-data-portal.md) kreatora.

2. Jeśli nie możesz użyć **importowania danych**, nadal możesz [Tworzenie początkowego indeksu w portalu](search-create-index-portal.md), dodając pola i typy danych i przypisanie atrybutów, za pomocą formantów na **Add Index** Strona. W portalu wyświetlany atrybutów, które są dostępne dla różnych typów danych. Jeśli jesteś nowym użytkownikiem projekt indeksu, jest to przydatne.

   ![Dodaj stronę indeksu przedstawiający atrybuty w zależności od typu danych](media/search-create-index-portal/field-attributes.png "strony indeksu Dodaj przedstawiający atrybuty w zależności od typu danych")
  
   Po kliknięciu **Utwórz**, wszystkie struktury fizyczne obsługi indeksu są tworzone w usłudze wyszukiwania.

3. Pobieranie przy użyciu schematu indeksu [uzyskać indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/get-index) i narzędzia, takiego jak testowanie sieci web [Postman](search-fiddler.md). Masz teraz reprezentacja JSON indeks, który został utworzony w portalu. 

   Przejściu na podejście oparte na kodzie w tym momencie. Portal nie jest odpowiednie dla iteracji, ponieważ nie można edytować indeksu, który jest już utworzony. Jednak narzędzie Postman i REST, można użyć dla pozostałych zadań.

4. [Ładowanie indeksu z danymi](search-what-is-data-import.md). Usługa Azure Search akceptuje dokumentów JSON. Aby załadować dane programowo, umożliwia Postman dokumenty JSON w ładunku żądania. Jeśli dane nie jest łatwo wyrażone jako dane JSON, ten krok będzie najczęściej pracy o znacznym wykorzystaniu.

5. Tworzenie zapytań względem indeksu usługi, sprawdź wyniki i dalsze powtarzanie czynności w schemat indeksu, dopóki nie zaczniesz zobaczyć wyniki, których oczekujesz. Możesz użyć [ **Eksploratora wyszukiwania** ](search-explorer.md) lub Postman do zapytania w indeksie.

6. Kontynuuj używanie kodu do wykonywania iteracji projektu.  

Ponieważ struktury fizyczne są tworzone w usłudze [porzucenie i ponowne tworzenie indeksów](search-howto-reindex.md) zachodzi po każdym wprowadzeniu znaczących zmian do istniejącej definicji pola. Oznacza to, że podczas tworzenia aplikacji, należy zaplanować na częste ponowne kompilowanie. Warto rozważyć pracy przy użyciu podzestawu danych, aby wprowadzić odbudowuje go szybciej. 

Kod, a nie podejście portalu, jest zalecane w przypadku iteracyjne projektowania. Jeśli użytkownik korzysta w portalu do definicji indeksu, trzeba będzie Wypełnij definicję indeksu na każdym ponownej kompilacji. Jako alternatywę, narzędzi, takich jak [interfejsu API REST i narzędzia Postman](search-fiddler.md) są przydatne do testowania weryfikacji koncepcji, w przypadku projektów programistycznych w wczesnych faz. Można dokonać zmiany przyrostowe definicji indeksu w treści żądania, a następnie wyślij żądanie do usługi Usługa umożliwiająca ponowne utworzenie indeksu za pomocą zaktualizowanego schematu.

## <a name="components-of-an-index"></a>Składniki indeksu

Schematycznie indeksu usługi Azure Search składa się z następujących elementów. 

[ *Kolekcję pól* ](#fields-collection) jest zazwyczaj największych częścią indeksu, gdzie nosi nazwę każdego pola, wpisane, a z dopuszczalny rozmiar zachowania, które określają, jak są używane. Inne elementy obejmują [sugestory](#suggesters), [profile oceniania](#scoring-profiles), [analizatory](#analyzers) z części składowe, które umożliwiają dostosowanie, [CORS](#cors) i [klucza szyfrowania](#encryption-key) opcje.

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
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Atrybuty pól kolekcji i pola

W trakcie definiowania schematu musisz określić nazwę, typ i atrybuty każdego pola w indeksie. Typ pola klasyfikuje dane, które są w nim przechowywane. Atrybuty są ustawiane dla poszczególnych pól, aby określić sposób użycia pola. W poniższych tabelach zostały wyszczególnione typy i atrybuty, które możesz określić.

### <a name="data-types"></a>Typy danych
| Type | Opis |
| --- | --- |
| *Edm.String* |Tekst, który opcjonalnie można podzielić na tokeny dla wyszukiwania pełnotekstowego (dzielenie wyrazów, analiza słowotwórcza i tak dalej). |
| *Collection(Edm.String)* |Lista ciągów, które opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego. Nie ma teoretycznej górnej granicy liczby elementów w kolekcji, ale rozmiar ładunku w kolekcjach jest ograniczony do 16 MB. |
| *Edm.Boolean* |Zawiera wartości prawda/fałsz. |
| *Edm.Int32* |32-bitowe wartości całkowite. |
| *Edm.Int64* |64-bitowe wartości całkowite. |
| *Edm.Double* |Dane liczbowe o podwójnej precyzji. |
| *Edm.DateTimeOffset* |Wartości daty i godziny reprezentowane w formacie protokołu OData V4 (na przykład `yyyy-MM-ddTHH:mm:ss.fffZ` lub `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Punkt przedstawiający lokalizację geograficzną na świecie. |

Dowiedz się więcej na temat [typów danych obsługiwanych przez usługę Azure Search w tym miejscu](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Atrybuty indeksu
| Atrybut | Opis |
| --- | --- |
| *Klucz* |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, który jest używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| *Pobieranie* |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| *Filtrowanie* |Umożliwia używanie pola w zapytaniach filtrów. |
| *Sortowanie* |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| *Tworzenie aspektów* |Umożliwia używanie pola w strukturze [nawigacji aspektowej](search-faceted-navigation.md) podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| *Wyszukiwanie* |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |

Dowiedz się więcej na temat [atrybutów indeksów usługi Azure Search w tym miejscu](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="storage-implications"></a>Implikacje magazynu

Atrybuty, które możesz wybrać mają wpływ na magazyn. Poniższy zrzut ekranu przedstawia wzorców magazynu indeksu wynikające z różnych kombinacji atrybutów.

Indeks jest oparty na [przykładowe wbudowane nieruchomości](search-get-started-portal.md) źródła danych, co umożliwia indeksowanie i zapytania w portalu. Mimo że schematy indeksu nie są wyświetlane, można wywnioskować atrybutów, w oparciu o nazwę indeksu. Na przykład *realestate wyszukiwanie* indeks ma **wyszukiwanie** atrybut i nic, *pobieranie realestate* indeks ma  **pobieranie** atrybut i nic innego i tak dalej.

![Indeks rozmiaru na podstawie wyboru atrybutu](./media/search-what-is-an-index/realestate-index-size.png "indeksu rozmiaru na podstawie wybranych atrybutów")

Mimo że te wariantów indeksu są sztuczny, firma Microsoft może odwoływać się do nich szerokiego porównania wpływu magazynu atrybutów. Jest ustawienie **pobieranie** zwiększyć rozmiar indeksu? Nie. Dodawanie pól do ma **Sugestora** zwiększyć rozmiar indeksu? Tak.

Proporcjonalnie większe niż indeksy, które obsługują wyszukiwanie pełnotekstowe po prostu są indeksy, które obsługuje filtrowanie i sortowanie. Przyczyną jest to zapytanie filtrowania i sortowania na dokładne dopasowania, dzięki czemu dokumenty zapisywane są opublikowane. Z kolei pola z możliwością wyszukiwania pełnotekstowe i rozmyte pomocniczych Użyj odwrócony indeksy wyszukiwania, które są wypełniane przy użyciu tokenami terminów, które zużywają mniej miejsca niż całego dokumentów.

> [!Note]
> Architektura magazynu jest uznawany za szczegółowo opisuje implementacja usługi Azure Search i może ulec zmianie bez powiadomienia. Nie ma żadnej gwarancji, bieżące zachowanie utrwali w przyszłości.

## <a name="suggesters"></a>Funkcje sugestii
Sugestora to sekcja schemat, który definiuje pola w indeksie, które są używane do obsługi automatycznego uzupełniania wpisywaniu lub Autouzupełnianie zapytań wyszukiwania. Zazwyczaj ciągi częściowe są wysyłane do [sugestie (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions) podczas zapytania wyszukiwania wpisywany przez użytkownika, a interfejs API zwraca zbiór proponowanych fraz. 

Pola dodane do sugestora są używane do tworzenia wpisywania z wyprzedzeniem wyszukiwane terminy. Wszystkie terminy wyszukiwania są tworzone podczas indeksowania i przechowywane w innej lokalizacji. Aby uzyskać więcej informacji na temat tworzenia struktury sugestora zobacz [Dodaj sugestory](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profile oceniania

A [profil oceniania](index-add-scoring-profiles.md) jest części schemat, który definiuje niestandardowe oceniania zachowania umożliwiające wpływają na elementy, które są wyświetlane w wynikach wyszukiwania. Profile oceniania składają się wag pól i funkcji. Z nich korzystać, należy wskazać profil według nazwy w ciągu zapytania.

Domyślny profil oceniania działa w tle, można obliczyć wyniku wyszukiwania dla każdego elementu w zestawie wyników. Możesz użyć wewnętrznego, bez nazwy profilu oceniania. Alternatywnie, ustawić **defaultScoringProfile** do użycia niestandardowego profilu jako domyślnego, wywoływana zawsze wtedy, gdy nie określono profilu niestandardowego w ciągu zapytania.

## <a name="analyzers"></a>Analizatory

Element analizatory ustawia nazwę analizatora języków dla pola. Aby uzyskać więcej informacji na temat zakresu analizatory dostępne zobacz [Dodawanie analizatory do indeksu usługi Azure Search](search-analyzers.md). Analizatory należy używać tylko z pola z możliwością wyszukiwania. Po Analizator jest przypisany do pola, nie można zmienić, chyba że odbudowanie indeksu.

## <a name="cors"></a>CORS

JavaScript po stronie klienta nie można wywołać dowolnych interfejsów API domyślnie, ponieważ przeglądarka uniemożliwi wszystkich żądań cross-origin. Aby zezwolić na współużytkowanie zapytań do indeksu, Włącz mechanizm CORS (Cross-Origin Resource Sharing), ustawiając **corsOptions** atrybutu. Ze względów bezpieczeństwa kwerendy interfejsów API obsługę mechanizmu CORS. 

Do obsługi mechanizmu CORS można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): To jest lista źródeł, które będą mieć dostęp do Twojego indeksu. Oznacza to, że każdy kod JavaScript pochodzący z tych źródeł będzie można wykonywać zapytania w indeksie (przy założeniu, że zawiera poprawny klucz api-key). Każde źródło ma zazwyczaj postać `protocol://<fully-qualified-domain-name>:<port>` chociaż `<port>` jest często pomijane. Zobacz [Cross-origin resource sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) Aby uzyskać więcej informacji.

  Jeśli chcesz zezwolić na dostęp do wszystkich źródeł, Uwzględnij `*` jako pojedynczy element **allowedOrigins** tablicy. *Nie jest to zalecane praktyki dla usługi wyszukiwania w środowisku produkcyjnym* , ale często jest to przydatne w przypadku programowania i debugowania.

+ **Atrybut maxAgeInSeconds** (opcjonalnie): Przeglądarki używają tej wartości można określić czas trwania (w sekundach) do odpowiedzi wstępnego CORS pamięci podręcznej. To musi być nieujemną liczbą całkowitą. Im większa wartość ta jest, Lepsza wydajność, ale tym dłużej będzie trwało zmian zasad CORS zaczęły obowiązywać. Jeśli nie jest ustawiona, będzie używany domyślny czas trwania wynoszącą 5 minut.

## <a name="encryption-key"></a>Klucz szyfrowania

Gdy wszystkie indeksy usługi Azure search są domyślne szyfrowanie przekazywanego materiału przy użyciu kluczy zarządzanych firmy Microsoft, indeksów można skonfigurować, aby być zaszyfrowany przy użyciu **klucze zarządzane przez klienta** w usłudze Key Vault. Aby dowiedzieć się więcej, zobacz [zarządzać kluczami szyfrowania w usłudze Azure Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Kolejne kroki

Po zrozumieniu kompozycja indeksu, można nadal w portalu, aby utworzyć pierwszy indeks.

> [!div class="nextstepaction"]
> [Dodawanie indeksu (portal)](search-create-index-portal.md)
