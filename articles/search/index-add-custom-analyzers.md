---
title: Dodaj niestandardowe analizatory — usługa Azure Search
description: Zmodyfikuj tokenizatory tekstu i znaku filtry, używane w zapytaniach wyszukiwania pełnotekstowego w usłudze Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9a15078c953c1fab40ad521eff079a623c93b9d9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577547"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Dodaj niestandardowe analizatory do indeksu usługi Azure Search

A *analizatora niestandardowego* jest określonego typu [analizatora tekstu](search-analyzers.md) składający się z kombinacji zdefiniowanych przez użytkownika istniejących tokenizatora i opcjonalne filtry. Łącząc tokenizatory i filtry na nowe sposoby, można dostosować tekst przetwarzania w aparacie wyszukiwania do osiągnięcia określonych wyników. Na przykład można utworzyć analizatora niestandardowego za pomocą *filtr char* przed tekstowe dane wejściowe są stokenizowana, Usuń kod znaczników HTML.

 Można zdefiniować wiele analizatory niestandardowe, będzie się różnić w kombinacji filtrów, ale każde pole do indeksowania, analizy i jeden dla analizy wyszukiwania można używać tylko jednego analizatora. Ilustracja wygląda analizatora klienta znajduje się [przykład analizatora niestandardowego](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Przegląd

 Rola [aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md), mówiąc najprościej, jest do przetwarzania i przechowywania dokumentów w sposób, który umożliwia wydajne wykonywanie zapytań i pobierania. Na wysokim poziomie wszystkie sprowadza się do wyodrębniania słów ważne dokumenty, umieszczenie ich w indeksie i następnie przy użyciu indeksu do wyszukiwania dokumentów, które odpowiadają słów danego zapytania. Proces wyodrębniania wyrazy w dokumentach i zapytaniach wyszukiwania jest nazywany *poddawać analizie leksykalnej*. Składniki, które wykonują poddawać analizie leksykalnej są nazywane *analizatory*.

 W usłudze Azure Search, możesz korzystać z zestawu wstępnie zdefiniowanych analizatory niezależny od języka w [analizatory](#AnalyzerTable) tabeli lub analizatory specyficzny dla języka na liście [analizatory języka &#40;interfejsu API REST usługi Search Azure&#41;](index-add-language-analyzers.md). Istnieje również możliwość definiowania własnych analizatory niestandardowe.  

 Analizator niestandardowy umożliwia przejąć kontrolę nad procesem konwertowania tekstu na tokeny można indeksować i którą można przeszukiwać. To składający się z pojedynczego tokenizatora wstępnie zdefiniowane, co najmniej jeden filtr tokenu i co najmniej jeden filtr char Konfiguracja zdefiniowana przez użytkownika. Tokenizator jest odpowiedzialny za istotne tekstu do tokenów i tokenów filtrów do modyfikowania tokenów wyemitowane przez tokenizatora. CHAR filtry są stosowane do przygotować tekst wejściowy przetworzenia przez tokenizatora. Na przykład filtr char można zastąpić niektórych znaków lub symboli.

 Popularne scenariusze obsługiwane przez analizatory niestandardowe obejmują:  

- Wymowa wyszukiwania. Dodaj fonetycznych filtr, aby umożliwić wyszukiwanie oparte na jak brzmi wyrazu, nie jak został wpisany.  

- Wyłącz poddawać analizie leksykalnej. Użyj analizatora słów kluczowych, aby utworzyć pola z możliwością wyszukiwania, które nie są analizowane.  

- Wyszukaj szybkie prefiksu/sufiksu. Dodaj filtr Edge N-gram tokenu do indeksu prefiksy słów, aby umożliwić dopasowanie prefiksu szybkie. Połącz go z odwrotnej tokenu filtr sufiksu dopasowania.  

- Niestandardowe tokenizacji. Na przykład użyć do Podziel zdania na tokeny przy użyciu spacji, jak ogranicznik tokenizatora białe znaki  

- Łamanie ASCII. Dodawanie standardowych ASCII składanie filtru do normalizacji znaków diakrytycznych, takich jak strumień świetlny lub ê w warunkach wyszukiwania.  

  Ta strona zawiera listę obsługiwanych analizatorów, tokenizatory, filtry tokenu i filtry char. Można również znaleźć opis zmian do definicji indeksu za pomocą przykład użycia. Aby uzyskać więcej ogólnych informacji o podstawową używaną technologią wykorzystywane w implementacji usługi Azure Search, zobacz [podsumowania pakiet analizy (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Przykłady konfiguracji analizatora, zobacz [dodać analizatory w usłudze Azure Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Reguły walidacji  
 Nazwy analizatorów, tokenizatory, filtry tokenu i filtry znak muszą być unikatowe i nie może być taka sama jak analizatory wstępnie zdefiniowanych tokenizatory, filtry tokenu lub char filtrów. Zobacz [odwołania do właściwości](#PropertyReference) nazw już w użyciu.

## <a name="create-custom-analyzers"></a>Utwórz niestandardowe analizatory
 Można zdefiniować niestandardowe analizatory w czasie tworzenia indeksu. W tej sekcji opisano składnia określająca analizatora niestandardowego. Możesz można także zapoznać się ze składnią, przeglądając przykładowe definicje w [dodać analizatory w usłudze Azure Search](search-analyzers.md#examples).  

 Definicja analizatora obejmuje nazwę typu, co najmniej jeden filtr char, maksymalnie jeden tokenizatora i co najmniej jeden filtr tokenu przetwarzanie tokenizacji po. CHAR filtrach są stosowane przed tokenizacji. Filtry tokenu i char filtry są stosowane od lewej do prawej.

 `tokenizer_name` Nazywa się tokenizatora `token_filter_name_1` i `token_filter_name_2` są nazwami tokenu filtry i `char_filter_name_1` i `char_filter_name_2` są nazwami char filtry (zobacz [Tokenizatory](#Tokenizers), [ Token filtry](#TokenFilters) i Char filtry tabel prawidłowych wartości).

Definicja Analizator jest częścią większego indeksu. Zobacz [interfejsu API tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) uzyskać informacji na temat pozostałej części indeksu.

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  Analizatory niestandardowe, które tworzysz, nie są widoczne w witrynie Azure portal. Jest jedynym sposobem dodawania analizatora niestandardowego za pomocą kodu, który sprawia, że wywołania interfejsu API podczas definiowania indeksu.  

 W ramach definicji indeksu można umieścić w tej sekcji dowolnym miejscu w treści żądania tworzenia indeksu, ale zazwyczaj przechodzi na koniec:  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Definicje filtrów char, tokenizatory i filtry tokenu zostaną dodane do indeksu, tylko wtedy, gdy ustawiasz opcje niestandardowe. Aby użyć istniejącego filtru lub tokenizatora jako — jest, podaj je według nazwy w definicji analizatora.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testowanie analizatory niestandardowe

Możesz użyć **operacji analizatora testu** w [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) aby zobaczyć, jak analizator dzieli danego tekstu na tokeny.

**Żądanie**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Odpowiedź**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Niestandardowe analizatory aktualizacji

Po zdefiniowaniu analizator, tokenizator, token filtru lub filtr char, nie można modyfikować. Nowe można dodać do istniejącego indeksu tylko wtedy, gdy `allowIndexDowntime` flaga jest ustawiona na wartość true w żądaniu aktualizacji indeksu:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Ta operacja pobiera indeksu w trybie offline dla co najmniej kilka sekund, co powoduje swoje żądania indeksowania i zapytanie nie powiedzie się. Wydajność i zapisu dostępność indeks może być osłabiona przez kilka minut po indeks jest zaktualizowane lub dłużej dla bardzo dużych indeksów, ale te skutki są tymczasowe i po pewnym czasie rozwiązać samodzielnie.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Odwołania do analizatora

W poniższych tabelach listę właściwości konfiguracji dla analizatorów, tokenizatory, filtry tokenu i char sekcja filtru definicji indeksu. Struktura analizatora, tokenizatora lub filtr w indeksie składa się z tych atrybutów. Wartość przydziału informacji, zobacz [odwołania do właściwości](#PropertyReference).

### <a name="analyzers"></a>Analizatory

Dla analizatorów, atrybuty indeksu różnią się w zależności od tego, czy używasz analizatory wstępnie zdefiniowaną lub niestandardową.

#### <a name="predefined-analyzers"></a>Wstępnie zdefiniowane analizatorów

|||  
|-|-|  
|Name (Nazwa)|Musi zawierać tylko litery, cyfry, spacje, kreski lub znaki podkreślenia, uruchom może i kończyć i jest ograniczona do 128 znaków.|  
|Type|Analizator typ z listy obsługiwanych analizatorów. Zobacz **analyzer_type** kolumny w [analizatory](#AnalyzerTable) w poniższej tabeli.|  
|Opcje|Musi być prawidłowe opcje wstępnie zdefiniowanych analizatora, na liście [analizatory](#AnalyzerTable) w poniższej tabeli.|  

#### <a name="custom-analyzers"></a>Analizatory niestandardowe

|||  
|-|-|  
|Name (Nazwa)|Musi zawierać tylko litery, cyfry, spacje, kreski lub znaki podkreślenia, uruchom może i kończyć i jest ograniczona do 128 znaków.|  
|Type|Musi być "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Ustawiona na jeden z wstępnie zdefiniowanych char filtrów na liście [filtry Char](#CharFilter) tabeli lub filtr niestandardowy char określone w definicji indeksu.|  
|Tokenizer|Wymagany. Ustawiona na jeden z wstępnie zdefiniowanych tokenizatory na liście [Tokenizatory](#Tokenizers) w poniższej tabeli lub tokenizator niestandardowe określone w definicji indeksu.|  
|TokenFilters|Ustawiona na jeden z wstępnie zdefiniowanych tokenów filtrów na liście [Token filtry](#TokenFilters) tabeli lub filtr niestandardowy token określonego w definicji indeksu.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtry char

 Filtr char służy do przygotowania tekst wejściowy przetworzenia przez tokenizatora. Na przykład ich można zastąpić niektórych znaków lub symboli. Może mieć wiele filtrów char w analizatora niestandardowego. CHAR — filtry są uruchamiane w kolejności, w którym są wymienione.  

|||  
|-|-|  
|Name (Nazwa)|Musi zawierać tylko litery, cyfry, spacje, kreski lub znaki podkreślenia, uruchom może i kończyć i jest ograniczona do 128 znaków.|  
|Type|CHAR filtrowania typu z listy obsługiwanych char filtrów. Zobacz **char_filter_type** kolumny w [Char filtry](#CharFilter) w poniższej tabeli.|  
|Opcje|Musi być prawidłowe opcje z danym [Char filtry](#CharFilter) typu.|  

### <a name="tokenizers"></a>Tokenizatory

 Tokenizator dzieli tekst ciągłe na sekwencja tokenów, takie jak przerywanie zdania na słowa.  

 Można określić dokładnie jeden tokenizatora dla analizatora niestandardowego. Jeśli potrzebujesz więcej niż jeden tokenizatora, możesz utworzyć wiele analizatory niestandardowe i przypisać je na podstawie pola przez pole w schemacie indeksu.  
Analizatora niestandardowego za pomocą wstępnie zdefiniowanych tokenizatora domyślnych lub niestandardowych opcji.  

|||  
|-|-|  
|Name (Nazwa)|Musi zawierać tylko litery, cyfry, spacje, kreski lub znaki podkreślenia, uruchom może i kończyć i jest ograniczona do 128 znaków.|  
|Type|Tokenizator nazwę z listy obsługiwanych tokenizatory. Zobacz **tokenizer_type** kolumny w [Tokenizatory](#Tokenizers) w poniższej tabeli.|  
|Opcje|Musi być prawidłowe opcje tokenizatora danego typu na liście [Tokenizatory](#Tokenizers) w poniższej tabeli.|  

### <a name="token-filters"></a>Filtry tokenu

 Filtr token służy do odfiltrowywania, czyli zmodyfikować tokenów wygenerowanych przez tokenizatora. Na przykład można określić małe filtr, który konwertuje wszystkie znaki na małe litery.   
Może mieć wiele filtrów tokenu w analizatora niestandardowego. Token filtry są uruchamiane w kolejności, w którym są wyświetlane.  

|||  
|-|-|  
|Name (Nazwa)|Musi zawierać tylko litery, cyfry, spacje, kreski lub znaki podkreślenia, uruchom może i kończyć i jest ograniczona do 128 znaków.|  
|Type|Nazwa filtru token z listy obsługiwanych filtry tokenu. Zobacz **token_filter_type** kolumny w [Token filtry](#TokenFilters) w poniższej tabeli.|  
|Opcje|Musi być [Token filtry](#TokenFilters) typu podanego filtra tokenu.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Odwołania do właściwości

Ta sekcja zawiera prawidłowe wartości dla atrybutów określonych w definicji analizatora niestandardowego, tokenizatora, char, filtr lub tokenu filtru w indeksie. Analizatory tokenizatory i filtry, które są implementowane przy użyciu Apache Lucene zawierają łącza do dokumentacji interfejsu API Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Odwołanie do wstępnie zdefiniowanych analizatorów

|**analyzer_name**|**analyzer_type**<sup>1  </sup>|**Opis i opcje**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kodów pocztowych, identyfikatory i nazwy niektórych produktów.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Elastyczne oddziela tekstu do warunków przy użyciu wzorca wyrażenia regularnego.<br /><br /> **Opcje**<br /><br /> małe litery (typ: wartość logiczna) — określa, czy warunki są pisany małymi literami. Wartość domyślna to true.<br /><br /> [wzorzec](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: ciąg) — wzorzec wyrażenia regularnego do dopasowania separatory tokenu. Wartość domyślna to \w+.<br /><br /> [flagi](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: ciąg) — flag wyrażenia regularnego. Wartość domyślna to ciąg pusty. Dozwolone wartości: CANON_EQ CASE_INSENSITIVE, KOMENTARZE, DOTALL LITERAŁ, WIELOWIERSZOWE, UNICODE_CASE, UNIX_LINES<br /><br /> Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to pusta lista.|  
|[Proste](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Dzieli tekst bez litery i konwertuje je na małe litery. |  
|[Standardowa](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Nazywane również standard.lucene)|StandardAnalyzer|Standardowy analizator Lucene składa się ze standardowego tokenizatora, małe litery filtru i Zatrzymaj filtru.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Wartość domyślna to 255. Podziel są tokeny większa niż długość maksymalna. Maksymalna długość tokenu, który może służyć to 300 znaków.<br /><br /> Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to pusta lista.|  
|standardasciifolding.lucene|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Standardowy analizator z Ascii składanie filtru. |  
|[Zatrzymaj](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Dzieli tekst bez litery, stosuje małe i odrzucany termin filtr tokenu.<br /><br /> **Opcje**<br /><br /> Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Wartość domyślna to wstępnie zdefiniowanej listy w języku angielskim. |  
|[Białe znaki](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Analizator używającej tokenizatora białe znaki. Podziel się tokenów, które są dłuższe niż 255 znaków.|  

 <sup>1</sup> analizatora typy mają zawsze prefiks w kodzie za pomocą "#Microsoft.Azure.Search" taki sposób, że "PatternAnalyzer" faktycznie przekazywanej jako "#Microsoft.Azure.Search.PatternAnalyzer". Firma Microsoft usunęła prefiks dla zwięzłości, ale prefiks jest wymagany w kodzie. 
 
Analyzer_type jest świadczona wyłącznie dla analizatorów, które można dostosować. Jeśli istnieje żadnych opcji w przypadku analizatora słów kluczowych, nie istnieje żaden typ #Microsoft.Azure.Search skojarzone.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>CHAR — odwołanie do filtrów

W poniższej tabeli filtry znaków, które są implementowane przy użyciu Apache Lucene są połączone z dokumentacji interfejsu API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Tworzy filtr znak, który podejmuje próbę odłączenia HTML.|  
|[mapping](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Filtr znak, który ma zastosowanie mapowania zdefiniowane przy użyciu opcji mapowania. Dopasowanie jest zachłanne (najdłuższego dopasowania do wzorca na danym serwerze wins). Zastąpienie może być pustym ciągiem.<br /><br /> **Opcje**<br /><br /> mapowania (typ: tablica ciągów)-listę mapowań następujący format: "= > b" (wszystkie wystąpienia znaku "" zastępuje się znakiem "b"). Wymagany.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Filtr znak, który zastępuje znaki do ciągu wejściowego. Aby zastąpić używa wyrażenia regularnego, aby zidentyfikować sekwencji znaków do zachowania i wzorzec zamieniania, aby zidentyfikować znaków. Na przykład wprowadzanie tekstu = "aa-bb aa bb", pattern="(aa)\\\s+(bb)" zastąpienie = "$# 1$ 2", wynik = "aa #bb aa #bb".<br /><br /> **Opcje**<br /><br /> wzorzec (typ: ciąg) — wymagane.<br /><br /> zastąpienie (typ: ciąg) — wymagane.|  

 <sup>1</sup> char typów filtrów mają zawsze prefiks w kodzie za pomocą "#Microsoft.Azure.Search" taki sposób, że "MappingCharFilter" faktycznie przekazywanej jako "#Microsoft.Azure.Search.MappingCharFilter. Firma Microsoft usunęła prefiks Zmniejsz szerokość tabeli, ale pamiętaj uwzględnić go w kodzie. Pamiętaj, że char_filter_type jest świadczona wyłącznie dla filtrów, które można dostosować. Jeśli istnieje żadnych opcji w przypadku html_strip, nie istnieje żaden typ #Microsoft.Azure.Search skojarzone.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizatory odwołania

W poniższej tabeli, które są implementowane przy użyciu Apache Lucene tokenizatory są połączone z dokumentacji interfejsu API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[Model Klasyczny](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Gramatyka na podstawie tokenizatora, które jest odpowiednie dla przetwarzania większość dokumentów Europejskiej języka.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Domyślne: 255, maksymalna: 300. Podziel są tokeny większa niż długość maksymalna.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes dane wejściowe z przeglądarki edge do n gramy podane całkowitą pojemność komputerze.<br /><br /> **Opcje**<br /><br /> minGram (typ: int)-domyślne: maksymalnie 1 maja: 300.<br /><br /> maxGram (typ: int)-domyślne: 2, maksymalna: 300. Musi być większa niż minGram.<br /><br /> tokenChars (typ: tablica ciągów)-klasy, aby zachować w tokenach znaku. Dozwolone wartości: <br />"list", "digit", "białe znaki", "wyróżnienie", "symbol". Wartość domyślna to pusta tablica — przechowuje wszystkie znaki. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emituje całego danych wejściowych jako pojedynczy token.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Domyślne: 256 maksymalna: 300. Podziel są tokeny większa niż długość maksymalna.|  
|[List](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Dzieli tekst bez litery. Podziel się tokenów, które są dłuższe niż 255 znaków.|  
|[Małe litery](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Dzieli tekst bez litery i konwertuje je na małe litery. Podziel się tokenów, które są dłuższe niż 255 znaków.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Dzieli tekst przy użyciu reguły specyficzne dla języka.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu, domyślny: 255, maksymalna: 300. Podziel są tokeny większa niż długość maksymalna. Tokeny dłuższy niż 300 znaków najpierw jest podzielony na tokeny o długości 300, a następnie Podziel z każdej z tych tokenów na podstawie maxTokenLength zestawu.<br /><br />isSearchTokenizer (typ: wartość logiczna) — ustaw wartość true, jeśli jest używana jako tokenizatora wyszukiwania, wartość false, jeśli jest używana jako indeksowania tokenizatora. <br /><br /> język (typ: ciąg) — język do użycia, domyślna "angielski". Dozwolone wartości:<br />"Bengalski", "bułgarski", "Kataloński", "chineseSimplified", "chineseTraditional", "Kuna", "czeski", "korona", "dutch", "english", "Francuski", "niemiecki", "grecki", "gudżarati", "hindi", "islandzkim", "indonezyjski", "włoski", "japoński", "kannada" " koreańska","malay","Malajalam","marathi","norwegianBokmaal","Polski","portugalskim","portugueseBrazilian","pendżabski","rumuński","rosyjski","serbianCyrillic","serbianLatin","słoweński","hiszpański","korona","w języku tamilskim","telugu","tajski"" Hrywna","urdu","wietnamski" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Dzieli tekst przy użyciu reguły specyficzne dla języka i zmniejsza słowa do podstawowego formularzy<br /><br /> **Opcje**<br /><br />maxTokenLength (typ: int) — maksymalna długość tokenu, domyślny: 255, maksymalna: 300. Podziel są tokeny większa niż długość maksymalna. Tokeny dłuższy niż 300 znaków najpierw jest podzielony na tokeny o długości 300, a następnie Podziel z każdej z tych tokenów na podstawie maxTokenLength zestawu.<br /><br /> isSearchTokenizer (typ: wartość logiczna) — ustaw wartość true, jeśli jest używana jako tokenizatora wyszukiwania, wartość false, jeśli jest używana jako indeksowania tokenizatora.<br /><br /> język (typ: ciąg) — język do użycia, domyślna "angielski". Dozwolone wartości:<br />"arabski", "Bengalski", "bułgarski", "Kataloński", "Kuna", "czeski", "korona", "dutch", "english", "estoński", "fiński", "Francuski", "niemiecki", "grecki", "gudżarati", "hebrajski", "hindi", "węgierski", "islandzkim", "indonezyjski", "włoski", "kannada" " Łotewski","litewski","malay","Malajalam","marathi","norwegianBokmaal","Polski","portugalskim","portugueseBrazilian","pendżabski","rumuński","rosyjski","serbianCyrillic","serbianLatin","słowacki","słoweński","hiszpański","korona"" języku tamilskim","telugu","lira","Hrywna","urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes dane wejściowe w n gramy całkowitą pojemność danego na komputerze.<br /><br /> **Opcje**<br /><br /> minGram (typ: int)-domyślne: maksymalnie 1 maja: 300.<br /><br /> maxGram (typ: int)-domyślne: 2, maksymalna: 300. Musi być większa niż minGram. <br /><br /> tokenChars (typ: tablica ciągów)-klasy, aby zachować w tokenach znaku. Dozwolone wartości: "list", "digit", "białe znaki", "wyróżnienie", "symbol". Wartość domyślna to pusta tablica — przechowuje wszystkie znaki. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizator dla hierarchii notacji ścieżki.<br /><br /> **Opcje**<br /><br /> Ogranicznik (typ: ciąg) — domyślna: "/.<br /><br /> zastąpienie (typ: ciąg) — Jeśli ustawić, zastępuje znak ogranicznika. Domyślnie takie same jak wartość ogranicznika.<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Domyślne: 300 maksymalna: 300. Ścieżki dłuższe niż maxTokenLength są ignorowane.<br /><br /> zwrotny (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, generuje token w odwrotnej kolejności. Domyślnie: false.<br /><br /> Pomiń (typ: bool)-początkowej tokenów do pominięcia. Wartość domyślna to 0.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Ta tokenizatora używa dopasowywania do wzorca wyrażenia regularnego do konstruowania distinct tokenów.<br /><br /> **Opcje**<br /><br /> wzorzec (typ: ciąg) — wzorzec wyrażenia regularnego. Wartość domyślna to \w+.<br /><br /> [flagi](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: ciąg) — flag wyrażenia regularnego. Wartość domyślna to ciąg pusty. Dozwolone wartości: CANON_EQ CASE_INSENSITIVE, KOMENTARZE, DOTALL LITERAŁ, WIELOWIERSZOWE, UNICODE_CASE, UNIX_LINES<br /><br /> grupy (typ: int)-grupę, do której można wyodrębnić na tokeny. Wartość domyślna to -1 (podziału).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Dzieli tekst następujące [reguły segmentacji tekst Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Domyślne: 255, maksymalna: 300. Podziel są tokeny większa niż długość maksymalna.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes adresów URL i wiadomości e-mail jako jeden token.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) — maksymalna długość tokenu. Domyślne: 255, maksymalna: 300. Podziel są tokeny większa niż długość maksymalna.|  
|[Białe znaki](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Dzieli tekst w miejscach występowania znaków odstępu. Podziel się tokenów, które są dłuższe niż 255 znaków.|  

 <sup>1</sup> typy tokenizatora mają zawsze prefiks w kodzie za pomocą "#Microsoft.Azure.Search" taki sposób, że "ClassicTokenizer" faktycznie przekazywanej jako "#Microsoft.Azure.Search.ClassicTokenizer". Firma Microsoft usunęła prefiks Zmniejsz szerokość tabeli, ale pamiętaj uwzględnić go w kodzie. Pamiętaj, że tokenizer_type jest świadczona wyłącznie dla tokenizatory, które można dostosować. Istnieją żadnych opcji w przypadku tokenizatora litery, czy żaden typ #Microsoft.Azure.Search skojarzone.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Odwołanie tokenu filtry

W poniższej tabeli tokenu filtry, które są implementowane przy użyciu Apache Lucene są połączone z dokumentacji interfejsu API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Filtr tokenu, który ma zastosowanie arabski normalizer do normalizacji orthography.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Usuwa wszystkie znaki po apostrof (w tym apostrofu, sam). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Konwertuje litery, cyfry i symboliczne znaków Unicode, które nie są najpierw 127 znaków ASCII (blok Unicode "Łaciński podstawowy") do ich odpowiedników ASCII, jeśli taka istnieje.<br /><br /> **Opcje**<br /><br /> preserveOriginal (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, pierwotny token jest przechowywana. Wartość domyślna to false.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Formularze bigrams CJK warunki, które są generowane na podstawie StandardTokenizer.<br /><br /> **Opcje**<br /><br /> ignoreScripts (typ: tablica ciągów)-skryptów do ignorowania. Dozwolone wartości to: "Hanowi", "hiragana", "katakana", "hangul". Wartość domyślna to pusta lista.<br /><br /> outputUnigrams (typ: wartość logiczna) — ustaw wartość true, jeśli chcesz zawsze danych wyjściowych unigrams i bigrams. Wartość domyślna to false.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje CJK szerokość różnice. Złożeń pełnej szerokości wariantów ASCII do równoważne łaciński podstawowy i wariantów Katakana połowę szerokości na równoważne znaki kana. |  
|[Model Klasyczny](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Usuwa angielskiej Zaimki dzierżawcze i kropki z akronimów. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Skonstruuj bigrams dla często pojawiają się warunki podczas indeksowania. Pojedyncze terminy nadal są indeksowane, za pomocą bigrams nałożony.<br /><br /> **Opcje**<br /><br /> commonWords (typ: tablica ciągów) — zbiór popularne wyrazy. Wartość domyślna to pusta lista. Wymagany.<br /><br /> ignoreCase (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, dopasowanie jest uwzględniana wielkość liter. Wartość domyślna to false.<br /><br /> Gdy (typ: wartość logiczna) — generuje bigrams, a następnie usuwa popularne wyrazy i pojedyncze terminy następuje wspólnego programu word. Wartość domyślna to false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Rozkłada się znaleźć w wielu językach germański wyrazy złożone.<br /><br /> **Opcje**<br /><br /> Lista wyrazów (typ: tablica ciągów) — lista wyrazów, które ma być przeprowadzane dopasowanie. Wartość domyślna to pusta lista. Wymagany.<br /><br /> minWordSize (typ: int) — tylko słowa dłuższe są przetwarzane. Wartość domyślna to 5.<br /><br /> minSubwordSize (typ: int) — tylko subwords dłuższe są zwrócone. Wartość domyślna to 2.<br /><br /> maxSubwordSize (typ: int) — tylko subwords krótszy niż to jest wyświetlona. Wartość domyślna to 15.<br /><br /> onlyLongestMatch (typ: wartość logiczna) — Dodaj tylko najdłuższy zgodny wyraz podrzędny w danych wyjściowych. Wartość domyślna to false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n gramy całkowitą pojemność danego na komputerze z od przodu lub tyłu tokenu danych wejściowych.<br /><br /> **Opcje**<br /><br /> minGram (typ: int)-domyślne: maksymalnie 1 maja: 300.<br /><br /> maxGram (typ: int)-domyślne: 2, maksymalna 300. Musi być większa niż minGram.<br /><br /> po stronie (typ: ciąg) — określa, która strona danych wejściowych n-gram powinny być generowane z. Dozwolone wartości: "frontonu", "Wróć" |  
|[pominięcia](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Usuwa elisions. Na przykład "l'avion" (płaszczyzna) jest konwertowana "avion" (warstwy).<br /><br /> **Opcje**<br /><br /> artykuły (typ: tablica ciągów) — zbiór artykułów, aby usunąć. Wartość domyślna to pusta lista. Jeśli nie ma listy zestawu artykułów, domyślnie wszystkie artykuły francuska są usuwane.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje niemieckiego znaków zgodnie z algorytmów heurystycznych z [algorytm snowball German2](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje tekstu w Hindi, aby usunąć pewne różnice w odmiany pisowni. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje tekstu w języków indyjskich reprezentację Unicode.
|[Zachowaj](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Filtr tokenu, który przechowuje tylko tokenów z tekstem zawarte w określonej listy słów.<br /><br /> **Opcje**<br /><br /> keepWords (typ: tablica ciągów)-listę słów, aby zachować. Wartość domyślna to pusta lista. Wymagany.<br /><br /> keepWordsCase (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, dolna zamierzone, Zapisz wszystkie wyrazy najpierw. Wartość domyślna to false.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Oznacza warunki jako słowa kluczowe.<br /><br /> **Opcje**<br /><br /> słowa kluczowe (typ: tablica ciągów)-listę słów, aby oznaczyć jako słowa kluczowe. Wartość domyślna to pusta lista. Wymagany.<br /><br /> ignoreCase (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, dolna zamierzone, Zapisz wszystkie wyrazy najpierw. Wartość domyślna to false.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Emituje Każdy przychodzący token dwukrotnie raz jako — słowo kluczowe i jeden raz jako bez słowa kluczowego. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Filtr kstem o wysokiej wydajności dla języka angielskiego. |  
|[Długość](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Usuwa wyrazy, które są zbyt długi lub zbyt krótki.<br /><br /> **Opcje**<br /><br /> min (typ: int) — minimalna liczba. Domyślne: 0, maksymalna: 300.<br /><br /> MAX (typ: int) — maksymalna liczba. Domyślne: 300 maksymalna: 300.|  
|[Limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Ogranicza liczbę tokenów podczas indeksowania.<br /><br /> **Opcje**<br /><br /> maxTokenCount (typ: int) — maksymalna liczba tokenów do utworzenia. Wartość domyślna to 1.<br /><br /> consumeAllTokens (typ: wartość logiczna) — czy nawet wtedy, gdy zostanie osiągnięty maxTokenCount należy wykorzystać wszystkie tokeny z danych wejściowych. Wartość domyślna to false.|  
|[Małe litery](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje tokenu tekst na małe litery. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n gramy całkowitą pojemność danego na komputerze.<br /><br /> **Opcje**<br /><br /> minGram (typ: int)-domyślne: maksymalnie 1 maja: 300.<br /><br /> maxGram (typ: int)-domyślne: 2, maksymalna 300. Musi być większa niż minGram.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Używa wyrażenia regularne języka Java do emitowania wielu tokenów, po jednym dla każdej grupy przechwytywania we wzorcach jeden lub więcej.<br /><br /> **Opcje**<br /><br /> wzorce (typ: tablica ciągów) — lista wzorców do dopasowywania każdy token. Wymagany.<br /><br /> preserveOriginal (typ: wartość logiczna) — ustaw wartość true, aby przywrócić pierwotny token nawet wtedy, gdy z jednym z wzorców jest zgodny, domyślna: true |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Filtr tokenu, który dotyczy wzorca każdy token w strumieniu, zastępując dopasowanie wystąpienia określony ciąg zastępczy.<br /><br /> **Opcje**<br /><br /> wzorzec (typ: ciąg) — wymagane.<br /><br /> zastąpienie (typ: ciąg) — wymagane.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje) |Stosuje normalizacji dla perski. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Utwórz tokeny dopasowania fonetycznego.<br /><br /> **Opcje**<br /><br /> Koder (typ: ciąg) — koder fonetycznych do użycia. Dozwolone wartości to: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "Kolonia", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Wartość domyślna: "metaphone". Wartość domyślna to metaphone.<br /><br /> Zobacz [kodera](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) Aby uzyskać więcej informacji.<br /><br /> Zastąp (typ: wartość logiczna) — jest to wartość True, jeśli zakodowany tokeny należy zastąpić oryginalny tokenów, false, jeśli należy dodać ich jako synonimów. Wartość domyślna to true.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Przekształca strumień tokenu zgodnie [algorytm korzystanie z rdzeni Porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverse](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Odwraca ciąg tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje wymienne skandynawskich znaków. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Folds Scandinavian characters åÅäæÄÆ->a and öÖøØ->o. On również eliminuje przed użyciem podwójnego samogłosek aa, ae, ao, oe i wprowadzaniem, pozostawiając tylko pierwszy z nich. |  
|[Małe konfetti](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Tworzy kombinacji tokenów jako pojedynczy token.<br /><br /> **Opcje**<br /><br /> maxShingleSize (typ: int) — wartość domyślna to 2.<br /><br /> minShingleSize (typ: int) — wartość domyślna to 2.<br /><br /> outputUnigrams (typ: wartość logiczna) — Jeśli ma wartość true, dane ze strumienia wyjściowego oraz płytki kamienne zawiera tokeny danych wejściowych (unigrams). Wartość domyślna to true.<br /><br /> outputUnigramsIfNoShingles (typ: wartość logiczna) — w przypadku opcji true zastąpić zachowanie outputUnigrams == false przydatny w sytuacjach, kiedy są dostępne nie płytki kamienne. Wartość domyślna to false.<br /><br /> tokenSeparator (typ: ciąg) — ciąg do użycia podczas przyłączania sąsiadujących tokenów w celu utworzenia Małe konfetti. Wartość domyślna to "".<br /><br /> filterToken (typ: ciąg) — ciąg, aby wstawić dla każdej pozycji, na którym jest brak tokenu. Wartość domyślna to "_".|  
|[Snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtr tokenu Snowball.<br /><br /> **Opcje**<br /><br /> język (typ: ciąg) — dozwolone wartości to: "armeński", "Baskijski", "Kataloński", "korona", "dutch", "english", "fiński", "Francuski", "niemiecki", "german2", "węgierski", "włoski", "kp", "lovins", "norweski", "porter", "portugalski", "rumuński", " rosyjski","hiszpański","korona","turecki"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentację Unicode Sorani tekstu.<br /><br /> **Opcje**<br /><br /> Brak.|  
|Program szukający rdzeni|StemmerTokenFilter|Korzystanie z rdzeni filtr specyficzny dla języka.<br /><br /> **Opcje**<br /><br /> język (typ: ciąg) — dozwolone wartości to: <br /> -   ["arabski"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armeński"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazylijski"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"bułgarski"<br />-   ["Kataloński"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czeski"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["korona"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["fiński"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-"lightFinnish"<br />-   ["Francuska"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galicyjski"<br />-"minimalGalician"<br />-   ["niemiecki"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-"minimalGerman"<br />-   ["greckim"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"hindi"<br />-   ["węgierski"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonezyjski"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irlandzki"](https://snowballstem.org/otherapps/oregan/)<br />-   ["włoski"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["łotewski"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norweski"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portugalskim"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["rumuński"](https://snowballstem.org/otherapps/romanian/)<br />-   ["rosyjski"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["hiszpański"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["korona"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-"lightSwedish"<br />-   ["lira"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Postanowieniami pęd słownika oznaczonych jako słów kluczowych, co uniemożliwia, analiza słowotwórcza w łańcuchu. Muszą znajdować się przed wszystkie filtry, korzystanie z rdzeni.<br /><br /> **Opcje**<br /><br /> reguły (typ: tablicy ciągów)-wynikające z zasad w następującym formacie "word = > stem" na przykład "uruchomiono = > Uruchom". Wartość domyślna to pusta lista.  Wymagany.|  
|[stopwords](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Usuwa Zatrzymaj wyrazów strumień tokenu. Domyślnie filtr korzysta listę wyrazów stop wstępnie zdefiniowanych w języku angielskim.<br /><br /> **Opcje**<br /><br /> Stop-słowa (typ: tablica ciągów) — lista Stop-słowa. Nie można określić, jeśli określono stopwordsList.<br /><br /> stopwordsList (typ: ciąg) — wstępnie zdefiniowanej listy Stop-słowa. Nie można określić, jeśli określono Stop-słowa. Dozwolone wartości to: "arabski", "armeński", "Baskijski", "brazylijski", "bułgarski", "Kataloński", "czeski", "korona", "dutch", "english", "fiński", "Francuski", "Galicyjski", "niemiecki", "greckim", "hindi", "węgierski", "indonezyjski", "irlandzki", "włoski", "łotewski ","norweski","perski","portugalskim","rumuński","rosyjski","sorani","hiszpański","korona","tajski","lira"domyślna:"angielski". Nie można określić, jeśli określono Stop-słowa. <br /><br /> ignoreCase (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, wszystkie wyrazy są niższe pierwszy z uwzględnieniem wielkości liter. Wartość domyślna to false.<br /><br /> removeTrailing (typ: wartość logiczna) — w przypadku opcji true Ignoruj ostatni termin wyszukiwania, jeśli jest słowem stop. Wartość domyślna to true.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Dopasowania jednego lub wielu word synonimów w strumieniu tokenu.<br /><br /> **Opcje**<br /><br /> synonimy (typ: tablica ciągów) — wymagane. Lista synonimów w jednym z dwóch następujących formatów:<br /><br /> -przełomowe, dźwięku, wspaniałe = > wspaniałe — wszystkie terminy po lewej stronie = > symboli są zastępowane wszystkie warunki jego po prawej stronie.<br /><br /> — to przełomowe, dźwięku, wspaniałe, niesamowite - rozdzielaną przecinkami listę słów równoważne. Ustaw opcję Rozwiń, aby zmienić, jak ta lista jest interpretowany.<br /><br /> ignoreCase (typ: wartość logiczna) — przypadek złożeń dane wejściowe na potrzeby dopasowywania. Wartość domyślna to false.<br /><br /> Rozwiń (typ: bool) — Jeśli to ustawienie ma wartość true, wszystkie wyrazy w listę synonimów (jeśli = > nie jest używana notacja) mapowania na siebie nawzajem. <br />Poniższa lista: wspaniałe przełomowe, dźwięku, wspaniałe, jest odpowiednikiem: przełomowe, dźwięku, wspaniałe, wspaniałe = > przełomowe, dźwięku, wspaniałe, wspaniałe<br /><br />-Jeśli false, Poniższa lista: przełomowe, dźwięku, wspaniałe, wspaniałe są równoważne: przełomowe, dźwięku, wspaniałe, wspaniałe = > radykalnie.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Przycina początkowe i końcowe białe znaki z tokenów. |  
|[Obetnij](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Obcina warunki do określonej długości.<br /><br /> **Opcje**<br /><br /> długość (typ: int)-domyślne: 300 maksymalna: 300. Wymagany.|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Odfiltrowuje tokenów z informacjami o ten sam tekst co poprzedni token.<br /><br /> **Opcje**<br /><br /> onlyOnSamePosition (typ: wartość logiczna) — Jeśli ustawić, Usuń duplikaty tylko na tym samym położeniu. Wartość domyślna to true.|  
|[wielkie litery](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy są dostępne opcje)  |Normalizuje tokenu text na wielkie litery. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Dzieli słów na subwords i wykonuje przekształcenia opcjonalne w grupach wyrazu podrzędnego.<br /><br /> **Opcje**<br /><br /> generateWordParts (typ: wartość logiczna) — powoduje, że części słowa do wygenerowania, na przykład "AzureSearch" staje się "Azure" "Search". Wartość domyślna to true.<br /><br /> generateNumberParts (typ: wartość logiczna) — powoduje, że liczba subwords do wygenerowania. Wartość domyślna to true.<br /><br /> catenateWords (typ: wartość logiczna) — powoduje, że maksymalna uruchomienia programu word składa się z części catenated, na przykład "Usługi Azure Search" staje się "AzureSearch". Wartość domyślna to false.<br /><br /> catenateNumbers (typ: wartość logiczna) — maksymalna przyczyn uruchomienia numeru części można catenated, na przykład "1-2" staje się "np. 12". Wartość domyślna to false.<br /><br /> catenateAll (typ: wartość logiczna) — powoduje, że wyraz podrzędny wszystkich części do być catenated, np. "Azure-Search-1" staje się "AzureSearch1". Wartość domyślna to false.<br /><br /> splitOnCaseChange (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, podziały wyrazów na caseChange, na przykład "AzureSearch" staje się "Azure" "Search". Wartość domyślna to true.<br /><br /> preserveOriginal - powoduje, że oryginalna wyrazy, aby zostać zachowany i dodany do listy wyrazu podrzędnego. Wartość domyślna to false.<br /><br /> splitOnNumerics (typ: wartość logiczna) — Jeśli to ustawienie ma wartość true, dzieli dane według wartości liczbowych, na przykład "Azure1Search" staje się "Azure" "1" "Search". Wartość domyślna to true.<br /><br /> stemEnglishPossessive (typ: wartość logiczna) — powoduje, że końcowe "użytkownika" do usunięcia dla każdego wyrazu podrzędnego. Wartość domyślna to true.<br /><br /> protectedWords (typ: tablica ciągów)-tokenów, aby chronić przed są rozdzielane. Wartość domyślna to pusta lista.|  

 <sup>1</sup> tokenu typów filtrów mają zawsze prefiks w kodzie za pomocą "#Microsoft.Azure.Search" taki sposób, że "ArabicNormalizationTokenFilter" faktycznie przekazywanej jako "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Firma Microsoft usunęła prefiks Zmniejsz szerokość tabeli, ale pamiętaj uwzględnić go w kodzie.  

> [!NOTE]
> Jest to wymagane, należy skonfigurować z analizatorów niestandardowych do generuje tokeny więcej niż 300 znaków. Indeksowanie kończy się niepowodzeniem dla dokumentów za pomocą tych tokenów. Aby przyciąć je lub je ignorować, użyj **TruncateTokenFilter** i **LengthTokenFilter** odpowiednio.


## <a name="see-also"></a>Zobacz także  
 [REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analizatory w usłudze Azure Search > przykłady](search-analyzers.md#examples)    
 [Tworzenie indeksu &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
