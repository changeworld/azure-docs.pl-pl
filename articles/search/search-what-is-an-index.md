---
title: Tworzenie definicji indeksu i pojęć
titleSuffix: Azure Cognitive Search
description: Wprowadzenie do terminów i pojęć indeksu w usłudze Azure Cognitive Search, w tym części składowych i struktury fizycznej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282785"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Tworzenie podstawowego indeksu w usłudze Azure Cognitive Search

W usłudze Azure Cognitive Search *indeks* jest trwałym magazynem *dokumentów* i innych konstrukcji używanych do wyszukiwania filtrowanego i pełnotekstowego w usłudze Azure Cognitive Search. Koncepcyjnie dokument jest pojedynczą jednostką danych zdatnych do wyszukiwania w indeksie. Na przykład sklep internetowy może mieć dokument dla każdego sprzedawanego produktu, a organizacja medialna — dla każdego artykułu itp. W przełożeniu na lepiej znane pojęcia bazodanowe: *indeks* jest podobny do *tabeli*, a *dokumenty* są w przybliżeniu równe *wierszom* w tabeli.

Po dodaniu lub przekazaniu indeksu usługa Azure Cognitive Search tworzy struktury fizyczne na podstawie schematu, który podasz. Jeśli na przykład pole w indeksie jest oznaczone jako możliwe do wyszukania, dla tego pola tworzony jest odwrócony indeks. Później po dodaniu lub przekazaniu dokumentów lub przesłaniu zapytań wyszukiwania do usługi Azure Cognitive Search wysyłasz żądania do określonego indeksu w usłudze wyszukiwania. Ładowanie pól z wartościami dokumentu jest nazywane *indeksowanie* lub pozyskiwania danych.

Indeks można utworzyć w portalu, [interfejsie API REST](search-create-index-rest-api.md)lub [sdku .NET.](search-create-index-dotnet.md)

## <a name="recommended-workflow"></a>Zalecany przepływ pracy

Osiągnięcie odpowiedniego projektu indeksu jest zazwyczaj osiągane za pomocą wielu iteracji. Korzystanie z kombinacji narzędzi i interfejsów API może pomóc szybko sfinalizować projekt.

1. Określ, czy można użyć [indeksatora](search-indexer-overview.md#supported-data-sources). Jeśli dane zewnętrzne są jednym z obsługiwanych źródeł danych, można prototypować i ładować indeks za pomocą [**Kreatora importu danych.**](search-import-data-portal.md)

2. Jeśli nie można użyć **importu danych,** nadal można [utworzyć początkowy indeks w portalu,](search-create-index-portal.md)dodając pola, typy danych i przypisując atrybuty za pomocą kontrolek na stronie Dodaj **indeks.** Portal pokazuje, które atrybuty są dostępne dla różnych typów danych. Jeśli jesteś nowy w projekcie indeksu, jest to pomocne.

   ![Dodawanie strony indeksu z atrybutami według typu danych](media/search-create-index-portal/field-attributes.png "Dodawanie strony indeksu z atrybutami według typu danych")
  
   Po kliknięciu **przycisku Utwórz**wszystkie struktury fizyczne wspierające indeks są tworzone w usłudze wyszukiwania.

3. Pobierz schemat indeksu przy użyciu [interfejsu API Get Index REST](https://docs.microsoft.com/rest/api/searchservice/get-index) i narzędzia do testowania sieci Web, takiego jak [Postman](search-get-started-postman.md). Masz teraz reprezentację JSON indeksu utworzonego w portalu. 

   Przełączasz się na podejście oparte na kodzie w tym momencie. Portal nie nadaje się do iteracji, ponieważ nie można edytować indeksu, który jest już utworzony. Ale można użyć Postman i REST dla pozostałych zadań.

4. [Załaduj indeks z danymi](search-what-is-data-import.md). Usługa Azure Cognitive Search akceptuje dokumenty JSON. Aby załadować dane programowo, można użyć listonosza z dokumentami JSON w ładunku żądania. Jeśli dane nie są łatwo wyrażone jako JSON, ten krok będzie najbardziej pracochłonne.

5. Kwerenda indeksu, zbadać wyniki i dalsze iteracji w schemacie indeksu, aż zaczniesz widzieć oczekiwane wyniki. Za pomocą [**Eksploratora wyszukiwania**](search-explorer.md) lub listonosza można zbadać indeks.

6. Kontynuuj używanie kodu do iteracji nad projektem.  

Ponieważ struktury fizyczne są tworzone w usłudze, [upuszczanie i odtworzenie indeksów](search-howto-reindex.md) jest konieczne za każdym razem, gdy wprowadzasz istotne zmiany w istniejącej definicji pola. Oznacza to, że podczas rozwoju należy planować częste przebudowy. Można rozważyć pracę z podzbiorem danych, aby przebudowa przejść szybciej. 

Kod, a nie podejście portalu, jest zalecane dla projektowania iteracyjnego. Jeśli polegasz na portalu dla definicji indeksu, trzeba będzie wypełnić definicję indeksu na każdej przebudowy. Alternatywnie narzędzia, takie jak [Postman i INTERFEJS API REST,](search-get-started-postman.md) są pomocne w testowaniu weryfikacji koncepcji, gdy projekty programistyczne są nadal we wczesnych fazach. Można wprowadzić przyrostowe zmiany w definicji indeksu w treści żądania, a następnie wysłać żądanie do usługi, aby odtworzyć indeks przy użyciu zaktualizowanego schematu.

## <a name="components-of-an-index"></a>Składniki indeksu

Schematycznie indeks usługi Azure Cognitive Search składa się z następujących elementów. 

[*Kolekcja pól*](#fields-collection) jest zazwyczaj największą częścią indeksu, gdzie każde pole jest nazwane, wpisane i przypisane z dopuszczalnymi zachowaniami, które określają sposób jego użycia. Inne elementy obejmują [sugestów](#suggesters), [profile oceniania,](#scoring-profiles) [analizatory](#analyzers) z częściami składowymi do obsługi dostosowywania, [CORS](#cors) i opcje [klucza szyfrowania.](#encryption-key)

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

## <a name="fields-collection-and-field-attributes"></a>Atrybuty kolekcji i pól

W trakcie definiowania schematu musisz określić nazwę, typ i atrybuty każdego pola w indeksie. Typ pola klasyfikuje dane, które są w nim przechowywane. Atrybuty są ustawiane dla poszczególnych pól, aby określić sposób użycia pola. W poniższych tabelach zostały wyszczególnione typy i atrybuty, które możesz określić.

### <a name="data-types"></a>Typy danych
| Typ | Opis |
| --- | --- |
| *Edm.String* |Tekst, który opcjonalnie może być tokenizowany do wyszukiwania pełnotekstowego (łamanie wyrazów, wynikające i tak dalej). |
| *Collection(Edm.String)* |Lista ciągów, które opcjonalnie można podzielić na tokeny na potrzeby wyszukiwania pełnotekstowego. Nie ma teoretycznej górnej granicy liczby elementów w kolekcji, ale rozmiar ładunku w kolekcjach jest ograniczony do 16 MB. |
| *Edm.Boolean* |Zawiera wartości prawda/fałsz. |
| *Edm.Int32* |32-bitowe wartości całkowite. |
| *Edm.Int64* |64-bitowe wartości całkowite. |
| *Edm.Double* |Dane liczbowe o podwójnej precyzji. |
| *Edm.DateTimeOffset* |Wartości daty i godziny reprezentowane w formacie OData V4 (na przykład `yyyy-MM-ddTHH:mm:ss.fffZ` lub `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Punkt przedstawiający lokalizację geograficzną na świecie. |

Bardziej szczegółowe informacje na temat [obsługiwanych typów danych usługi](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)Azure Cognitive Search można znaleźć tutaj.

### <a name="index-attributes"></a>Atrybuty indeksu

Dokładnie jedno pole w indeksie musi być wyznaczone jako pole **klucza,** które jednoznacznie identyfikuje każdy dokument.

Inne atrybuty określają sposób, w jaki pole jest używane w aplikacji. Na przykład atrybut **z wyszukuj** jest przypisywany do każdego pola, które powinno zostać uwzględnione w wyszukiwaniu pełnotekstowym. 

Interfejsy API używane do tworzenia indeksu mają różne zachowania domyślne. W przypadku [interfejsów API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index)większość atrybutów jest domyślnie włączona (na przykład **przeszukiwalne** i **możliwe do pobrania** dotyczy pól ciągów) i często trzeba je ustawić tylko wtedy, gdy chcesz je wyłączyć. W przypadku sdk .NET jest odwrotnie. W każdej właściwości, której nie ustawiono jawnie, domyślnie jest wyłączenie odpowiedniego zachowania wyszukiwania, chyba że zostanie ono specjalnie włączone.

| Atrybut | Opis |
| --- | --- |
| `key` |Ciąg udostępniający unikatowy identyfikator każdego dokumentu, który jest używany do wyszukiwania dokumentu. Każdy indeks musi mieć jeden klucz. Tylko jedno pole może być kluczem i musi ono mieć typ Edm.String. |
| `retrievable` |Określa, czy pole może być zwracane w wynikach wyszukiwania. |
| `filterable` |Umożliwia używanie pola w zapytaniach filtrów. |
| `Sortable` |Umożliwia zapytaniom sortowanie wyników wyszukiwania za pomocą tego pola. |
| `facetable` |Umożliwia używanie pola w strukturze [nawigacji aspektowej](search-faceted-navigation.md) podczas samodzielnego filtrowania przez użytkownika. Zwykle jako aspekty najlepiej sprawdzają się pola zawierające powtarzające się wartości, które umożliwiają grupowanie wielu dokumentów (na przykład wiele dokumentów podlegających pod jedną markę lub kategorię usługi). |
| `searchable` |Oznacza pole jako podlegające wyszukiwaniu pełnotekstowemu. |

## <a name="index-size"></a>Rozmiar indeksu

Rozmiar indeksu zależy od rozmiaru przekazytowane dokumenty oraz konfiguracji indeksu, takich jak to, czy są dołączane sugestatory i jak ustawić atrybuty w poszczególnych polach. Poniższy zrzut ekranu ilustruje wzorce magazynu indeksu wynikające z różnych kombinacji atrybutów.

Indeks jest oparty na wbudowanym źródle [danych przykładowych nieruchomości,](search-get-started-portal.md) które można indeksować i wyszukiwać w portalu. Chociaż schematy indeksu nie są wyświetlane, można wywnioskować atrybuty na podstawie nazwy indeksu. Na przykład indeks *przeszukiwalny przez realestate* ma wybrany atrybut **z wyszukuj i** nic więcej, indeks *realestate-retrievable* ma wybrany atrybut **do pobrania** i nic więcej, i tak dalej.

![Rozmiar indeksu na podstawie wyboru atrybutu](./media/search-what-is-an-index/realestate-index-size.png "Rozmiar indeksu na podstawie wyboru atrybutu")

Mimo że te warianty indeksu są sztuczne, możemy odwoływać się do nich do szerokiego porównania, jak atrybuty wpływają na magazyn. Czy ustawienie **można odzyskać** zwiększyć rozmiar indeksu? Nie. Czy dodawanie pól do **sugestywny** zwiększa rozmiar indeksu? Tak.

Indeksy obsługujące filtrowanie i sortowanie są proporcjonalnie większe niż indeksy obsługujące tylko wyszukiwanie pełnotekstowe. Operacje filtrowania i sortowania skanują w poszukiwaniu dokładnych dopasowań, wymagając obecności nienaruszonych dokumentów. Z kolei pola z możliwością wyszukiwania obsługujące wyszukiwanie pełnotekstowe i rozmyte używają odwróconych indeksów, które są wypełniane terminami tokenizowanymi, które zajmują mniej miejsca niż całe dokumenty. 

> [!Note]
> Architektura magazynu jest uważany za szczegóły implementacji usługi Azure Cognitive Search i może ulec zmianie bez powiadomienia. Nie ma żadnej gwarancji, że bieżące zachowanie będzie się utrzymywać w przyszłości.

## <a name="suggesters"></a>Funkcje sugestii
Sugestywny jest sekcja schematu, który definiuje, które pola w indeksie są używane do obsługi auto-complete lub typu z wyprzedzeniem kwerend w wyszukiwaniach. Zazwyczaj częściowe ciągi wyszukiwania są wysyłane do [sugestii (INTERFEJS API REST),](https://docs.microsoft.com/rest/api/searchservice/suggestions) gdy użytkownik wpisuje kwerendę wyszukiwania, a interfejs API zwraca zestaw sugerowanych dokumentów lub fraz. 

Pola dodane do sugestyw są używane do tworzenia wyszukiwanych haseł typu z wyprzedzeniem. Wszystkie wyszukiwane terminy są tworzone podczas indeksowania i przechowywane oddzielnie. Aby uzyskać więcej informacji na temat tworzenia struktury sugestywny, zobacz [Dodawanie sugestów](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profile oceniania

[Profil oceniania](index-add-scoring-profiles.md) to sekcja schematu, która definiuje niestandardowe zachowania oceniania, które umożliwiają wpływ na elementy, które są wyświetlane wyżej w wynikach wyszukiwania. Profile punktacji są wykonane z mas terenowych i funkcji. Aby ich użyć, należy określić profil według nazwy w ciągu zapytania.

Domyślny profil oceniania działa w tle, aby obliczyć wynik wyszukiwania dla każdego elementu w zestawie wyników. Można użyć wewnętrznego, nienazwanego profilu oceniania. Alternatywnie ustaw **defaultScoringProfile** używać profilu niestandardowego jako domyślnego, wywoływane, gdy profil niestandardowy nie jest określony w ciągu zapytania.

## <a name="analyzers"></a>Analizatory

Analizatorów element ustawia nazwę analizatora języka do użycia w polu. Aby uzyskać więcej informacji na temat zakresu analizatorów dostępnych dla Ciebie, zobacz [Dodawanie analizatorów do indeksu usługi Azure Cognitive Search.](search-analyzers.md) Analizatory mogą być używane tylko z polami z wyszukujnymi. Po przypisaniu analizatora do pola nie można go zmienić, chyba że zostanie odbudowany indeks.

## <a name="cors"></a>CORS

JavaScript po stronie klienta nie może domyślnie wywoływać żadnych interfejsów API, ponieważ przeglądarka uniemożliwi wszystkie żądania cross-origin. Aby zezwolić na zapytania między źródłami do indeksu, włącz CORS (Cross-Origin Resource Sharing), ustawiając atrybut **corsOptions.** Ze względów bezpieczeństwa tylko kwerendy interfejsów API obsługuje cors. 

Dla cors można ustawić następujące opcje:

+ **allowedOrigins** (wymagane): Jest to lista źródeł, które będą przyznawane dostęp do indeksu. Oznacza to, że każdy kod JavaScript obsługiwany z tych źródeł będzie mógł wysyłać zapytania do indeksu (przy założeniu, że zawiera poprawny klucz interfejsu api). Każde źródło jest zazwyczaj `protocol://<fully-qualified-domain-name>:<port>` formularza, chociaż `<port>` często jest pomijany. Więcej informacji można znaleźć w [artykule Udostępnianie zasobów między źródłami (Wikipedia).](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

  Jeśli chcesz zezwolić na dostęp do `*` wszystkich źródeł, należy uwzględnić jako pojedynczy element w **tablicy allowedOrigins.** *Nie jest to zalecana praktyka dla usług wyszukiwania produkcyjnego,* ale często jest przydatna do tworzenia i debugowania.

+ **maxAgeInSeconds** (opcjonalnie): Przeglądarki używają tej wartości do określenia czasu trwania (w sekundach) do buforowania odpowiedzi inspekcji wstępnej CORS. Musi to być nieujemna liczba całkowita. Im większa jest ta wartość, tym lepsza będzie wydajność, ale im dłużej będzie to trwać, aby zmiany zasad CORS weszły w życie. Jeśli nie jest ustawiona, zostanie użyty domyślny czas trwania 5 minut.

## <a name="encryption-key"></a>Klucz szyfrowania

Podczas gdy wszystkie indeksy usługi Azure Cognitive Search są domyślnie szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft, indeksy można skonfigurować tak, aby były szyfrowane za pomocą **kluczy zarządzanych przez klienta** w usłudze Key Vault. Aby dowiedzieć się więcej, zobacz [Zarządzanie kluczami szyfrowania w usłudze Azure Cognitive Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Następne kroki

Ze zrozumieniem składu indeksu można kontynuować w portalu, aby utworzyć pierwszy indeks.

> [!div class="nextstepaction"]
> [Dodawanie indeksu (portalu)](search-create-index-portal.md)
