---
title: Dodawanie analizatorów niestandardowych do pól ciągów
titleSuffix: Azure Cognitive Search
description: Konfigurowanie tokenizatorów tekstu i filtrów znaków używanych w kwerendach wyszukiwania pełnotekstowego usługi Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113633"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie analizatorów niestandardowych do pól ciągów w indeksie usługi Azure Cognitive Search

*Analizator niestandardowy* jest określony typ [analizatora tekstu,](search-analyzers.md) który składa się z kombinacji zdefiniowanej przez użytkownika istniejącego tokenizatora i opcjonalnych filtrów. Łącząc tokenizatory i filtry na nowe sposoby, można dostosować przetwarzanie tekstu w wyszukiwarce, aby osiągnąć określone wyniki. Na przykład można utworzyć analizator niestandardowy z *filtrem char,* aby usunąć znaczniki HTML przed tokenizem danych wejściowych tekstu.

 Można zdefiniować wiele analizatorów niestandardowych, aby zmieniać kombinację filtrów, ale każde pole może używać tylko jednego analizatora do indeksowania analizy i jednego do analizy wyszukiwania. Aby uzyskać ilustrację tego, jak wygląda analizator klientów, zobacz [przykład analizatora niestandardowego](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Omówienie

 Rolą [wyszukiwarki pełnotekstowej](search-lucene-query-architecture.md), w prostych słowach, jest przetwarzanie i przechowywanie dokumentów w sposób umożliwiający efektywne wyszukiwanie i wyszukiwanie. Na wysokim poziomie wszystko sprowadza się do wyodrębniania ważnych słów z dokumentów, umieszczania ich w indeksie, a następnie używania indeksu do znajdowania dokumentów, które pasują do słów danej kwerendy. Proces wyodrębniania słów z dokumentów i zapytań wyszukiwania nazywa się *analizą leksyksyczną*. Komponenty, które wykonują analizę leksykalne, są nazywane *analizatorami*.

 W usłudze Azure Cognitive Search można wybierać spośród zestawu wstępnie zdefiniowanych analizatorów niezależnych od języka w tabeli [Analizatory](#AnalyzerTable) lub analizatorów specyficznych dla języka wymienionych w [analizatorach języka &#40;interfejs&#41;ie API REST usługi Azure Cognitive Search ](index-add-language-analyzers.md). Istnieje również możliwość zdefiniowania własnych analizatorów niestandardowych.  

 Analizator niestandardowy pozwala przejąć kontrolę nad procesem konwersji tekstu na tokeny z wymienne i przeszukiwalne. Jest to konfiguracja zdefiniowana przez użytkownika składająca się z jednego wstępnie zdefiniowanego tokenizatora, jednego lub więcej filtrów tokenu i co najmniej jednego filtru char. Tokenizator jest odpowiedzialny za podział tekstu na tokeny i filtry tokenów do modyfikowania tokenów emitowanych przez tokenizatora. Char filtry są stosowane do przygotowania tekstu wejściowego, zanim zostanie przetworzony przez tokenizatora. Na przykład filtr char może zastąpić niektóre znaki lub symbole.

 Popularne scenariusze włączone przez analizatory niestandardowe obejmują:  

- Wyszukiwanie fonetyczne. Dodaj filtr fonetyczny, aby umożliwić wyszukiwanie na podstawie tego, jak brzmi słowo, a nie jak jest napisane.  

- Wyłącz analizę leksyksyczną. Analizator słów kluczowych służy do tworzenia pól z wyszukuj, które nie są analizowane.  

- Szybkie wyszukiwanie prefiksów/sufiksów. Dodaj filtr tokenu Edge N-gram do indeksowania prefiksów wyrazów, aby umożliwić szybkie dopasowywanie prefiksów. Połącz go z reverse token filtru do dopasowywania sufiksów.  

- Tokenizacja niestandardowa. Na przykład użyj tokenizatora odstępów, aby podzielić zdania na tokeny przy użyciu odstępu jako ogranicznika  

- Składanie ASCII. Dodaj standardowy filtr składania ASCII, aby znormalizować znaki diakrytyczne, takie jak ö lub ê w wyszukiwanych hasłach.  

  Ta strona zawiera listę obsługiwanych analizatorów, tokenizatorów, filtrów tokenów i filtrów char. Można również znaleźć opis zmian w definicji indeksu w przykładzie użycia. Aby uzyskać więcej informacji na temat podstawowej technologii wykorzystywanej w implementacji usługi Azure Cognitive Search, zobacz [Podsumowanie pakietu analizy (Lucene).](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html) Aby zapoznać się z przykładami konfiguracji analizatora, zobacz [Dodawanie analizatorów w usłudze Azure Cognitive Search.](search-analyzers.md#examples)

## <a name="validation-rules"></a>Reguły sprawdzania poprawności  
 Nazwy analizatorów, tokenizatorów, filtrów tokenów i filtrów char muszą być unikatowe i nie mogą być takie same jak żadne wstępnie zdefiniowane analizatory, tokenizatory, filtry tokenów lub filtry char. Zobacz [odwołanie do właściwości](#PropertyReference) dla nazw już w użyciu.

## <a name="create-custom-analyzers"></a>Tworzenie analizatorów niestandardowych
 Analizatorów niestandardowych można zdefiniować w czasie tworzenia indeksu. Składnia określania analizatora niestandardowego jest opisana w tej sekcji. Można również zapoznać się ze składnią, przeglądając przykładowe definicje w [analizatorach dodaj w usłudze Azure Cognitive Search.](search-analyzers.md#examples)  

 Definicja analizatora zawiera nazwę, typ, jeden lub więcej filtrów char, maksymalnie jeden tokenizator i jeden lub więcej filtrów tokenu do przetwarzania post-tokenizacji. Filery char są stosowane przed tokenizacji. Filtry tokenów i filtry char są stosowane od lewej do prawej.

 Jest `tokenizer_name` to nazwa tokenizatora `token_filter_name_1` `token_filter_name_2` i są nazwy filtrów `char_filter_name_1` tokenów i `char_filter_name_2` są nazwy filtrów char (zobacz [Tokenizers](#Tokenizers), [Filtry tokenów](#TokenFilters) i Char filtry tabel prawidłowe wartości).

Definicja analizatora jest częścią większego indeksu. Zobacz [Tworzenie interfejsu API indeksu,](https://docs.microsoft.com/rest/api/searchservice/create-index) aby uzyskać informacje na temat pozostałej części indeksu.

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
>  Analizatory niestandardowe, które tworzysz nie są udostępniane w witrynie Azure portal. Jedynym sposobem, aby dodać analizatora niestandardowego jest za pomocą kodu, który sprawia, że wywołania interfejsu API podczas definiowania indeksu.  

 W definicji indeksu można umieścić tę sekcję w dowolnym miejscu w treści żądania indeksu tworzenia, ale zwykle idzie na końcu:  

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

Definicje filtrów char, tokenizatorów i filtrów tokenów są dodawane do indeksu tylko wtedy, gdy ustawiasz opcje niestandardowe. Aby użyć istniejącego filtru lub tokenizatora w stanie as-is, należy określić go według nazwy w definicji analizatora.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testowanie analizatorów niestandardowych

Można użyć **operacji analizatora testów** w [interfejsie API REST,](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) aby zobaczyć, jak analizator dzieli dany tekst na tokeny.

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
**Odpowiedzi**
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

## <a name="update-custom-analyzers"></a>Aktualizowanie analizatorów niestandardowych

Po zdefiniowaniu analizatora, tokenizatora, filtru tokenu lub filtru char nie można go zmodyfikować. Nowe można dodać do istniejącego indeksu tylko wtedy, gdy `allowIndexDowntime` flaga jest ustawiona na true w żądaniu aktualizacji indeksu:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Ta operacja powoduje, że indeks jest w trybie offline przez co najmniej kilka sekund, co powoduje niepowodzenie żądań indeksowania i zapytań. Wydajność i zapis dostępność indeksu może być ograniczona przez kilka minut po zaktualizowaniu indeksu lub dłużej dla bardzo dużych indeksów, ale te efekty są tymczasowe i ostatecznie rozwiązać na własną rękę.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Odwołanie do analizatora

Poniższe tabele zawiera listę właściwości konfiguracji dla analizatorów, tokenizatorów, filtrów tokenów i sekcji filtru char definicji indeksu. Struktura analizatora, tokenizatora lub filtru w indeksie składa się z tych atrybutów. Aby uzyskać informacje o przypisaniu wartości, zobacz [odwołanie do właściwości](#PropertyReference).

### <a name="analyzers"></a>Analizatory

W przypadku analizatorów atrybuty indeksu różnią się w zależności od tego, czy używasz wstępnie zdefiniowanych, czy niestandardowych analizatorów.

#### <a name="predefined-analyzers"></a>Wstępnie zdefiniowane analizatory

|||  
|-|-|  
|Nazwa|Musi zawierać tylko litery, cyfry, spacje, kreski lub podkreślenia, może rozpoczynać się i kończyć tylko znakami alfanumerycznymi i jest ograniczona do 128 znaków.|  
|Typ|Typ analizatora z listy obsługiwanych analizatorów. Zobacz **kolumnę analyzer_type** w tabeli [Analizatory](#AnalyzerTable) poniżej.|  
|Opcje|Muszą być prawidłowe opcje wstępnie zdefiniowanego analizatora wymienione w [tabeli Analizatory](#AnalyzerTable) poniżej.|  

#### <a name="custom-analyzers"></a>Analizatory niestandardowe

|||  
|-|-|  
|Nazwa|Musi zawierać tylko litery, cyfry, spacje, kreski lub podkreślenia, może rozpoczynać się i kończyć tylko znakami alfanumerycznymi i jest ograniczona do 128 znaków.|  
|Typ|Musi być "#Microsoft.Azure.Search.CustomAnalyzer".|  
|Filtry charfilters|Ustaw jeden ze wstępnie zdefiniowanych filtrów char wymienionych w tabeli [Filtry char](#char-filters-reference) lub niestandardowy filtr char określony w definicji indeksu.|  
|Tokenizator|Wymagany. Ustaw jeden z wstępnie zdefiniowanych tokenizatorów wymienionych w tabeli [Tokenizers](#Tokenizers) poniżej lub niestandardowy tokenizator określony w definicji indeksu.|  
|Filtry tokenów|Ustaw jeden z wstępnie zdefiniowanych filtrów tokenu wymienionych w tabeli [Filtry tokenu](#TokenFilters) lub niestandardowy filtr tokenu określony w definicji indeksu.|  

> [!NOTE]
> Wymagane jest skonfigurowanie analizatora niestandardowego, aby nie tworzyć tokenów dłuższych niż 300 znaków. Indeksowanie kończy się niepowodzeniem dla dokumentów z takimi tokenami. Aby je przyciąć lub zignorować, należy użyć **trudateTokenFilter** i **LengthTokenFilter** odpowiednio.  Sprawdź [**filtry tokenu w celach informacyjnych.**](#TokenFilters)

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtry char

 Filtr char jest używany do przygotowania tekstu wejściowego, zanim zostanie przetworzony przez tokenizatora. Na przykład mogą zastąpić niektóre znaki lub symbole. W analizatorze niestandardowym może być dostępnych wiele filtrów znaków. Filtry char są uruchamiane w kolejności, w jakiej są wyświetlane.  

|||  
|-|-|  
|Nazwa|Musi zawierać tylko litery, cyfry, spacje, kreski lub podkreślenia, może rozpoczynać się i kończyć tylko znakami alfanumerycznymi i jest ograniczona do 128 znaków.|  
|Typ|Typ filtru Char z listy obsługiwanych filtrów char. Zobacz **kolumnę char_filter_type** w tabeli [Filtry char](#char-filters-reference) poniżej.|  
|Opcje|Muszą być prawidłowe opcje danego typu [Filtry char.](#char-filters-reference)|  

### <a name="tokenizers"></a>Tokenizatory

 Tokenizator dzieli tekst ciągły na sekwencję tokenów, takich jak dzielenie zdania na słowa.  

 Można określić dokładnie jeden tokenizator na analizatora niestandardowego. Jeśli potrzebujesz więcej niż jednego tokenizatora, można utworzyć wiele analizatorów niestandardowych i przypisać je w oparciu o pole po polu w schemacie indeksu.  
Analizator niestandardowy można użyć wstępnie zdefiniowanego tokenizatora z domyślnymi lub dostosowanymi opcjami.  

|||  
|-|-|  
|Nazwa|Musi zawierać tylko litery, cyfry, spacje, kreski lub podkreślenia, może rozpoczynać się i kończyć tylko znakami alfanumerycznymi i jest ograniczona do 128 znaków.|  
|Typ|Nazwa tokenizatora z listy obsługiwanych tokenizatorów. Zobacz **tokenizer_type** kolumnę w tabeli [Tokenizers](#Tokenizers) poniżej.|  
|Opcje|Muszą być prawidłowe opcje danego typu tokenizatora wymienione w tabeli [Tokenizers](#Tokenizers) poniżej.|  

### <a name="token-filters"></a>Filtry tokenów

 Filtr tokenu służy do filtrowania lub modyfikowania tokenów generowanych przez tokenizatora. Można na przykład określić mały filtr, który konwertuje wszystkie znaki na małe litery.   
W analizatorze niestandardowym może być dostępnych wiele filtrów tokenów. Filtry tokenu są uruchamiane w kolejności, w jakiej są wyświetlane.  

|||  
|-|-|  
|Nazwa|Musi zawierać tylko litery, cyfry, spacje, kreski lub podkreślenia, może rozpoczynać się i kończyć tylko znakami alfanumerycznymi i jest ograniczona do 128 znaków.|  
|Typ|Nazwa filtru tokenu z listy obsługiwanych filtrów tokenu. Zobacz **token_filter_type** kolumnę w tabeli [Filtry tokenów](#TokenFilters) poniżej.|  
|Opcje|Muszą być [filtry tokenu](#TokenFilters) danego typu filtru tokenu.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Odwołanie do właściwości

Ta sekcja zawiera prawidłowe wartości dla atrybutów określonych w definicji analizatora niestandardowego, tokenizatora, filtru char lub filtru tokenu w indeksie. Analizatory, tokenizatory i filtry, które są implementowane przy użyciu Apache Lucene mają łącza do dokumentacji interfejsu API Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Predefiniowane odwołanie do analizatorów

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[Słowa kluczowego](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody pocztowe, identyfikatory i niektóre nazwy produktów.|  
|[Wzór](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|WzórAnalyzer|Elastycznie dzieli tekst na terminy za pomocą wzorca wyrażenia regularnego.<br /><br /> **Opcje**<br /><br /> małe litery (typ: bool) - Określa, czy terminy są małe. Wartość domyślna jest true.<br /><br /> [wzorzec](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (typ: ciąg) - wzorzec wyrażenia regularnego, aby dopasować separatory tokenu. Wartość domyślna to \w+.<br /><br /> [flagi](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: ciąg) - Flagi wyrażeń regularnych. Wartość domyślna to pusty ciąg. Dozwolone wartości: CANON_EQ, CASE_INSENSITIVE, KOMENTARZE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (typ: tablica ciągów) - Lista stopwords. Wartością domyślną jest pusta lista.|  
|[Prosty](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Dzieli tekst na litery inne i konwertuje go na małe litery. |  
|[Standardowych](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Również określane jako standard.lucene)|Standardowyanalizer|Standardowy analizator Lucene, składający się ze standardowego tokenizatora, filtra małych liter i filtru stop.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Wartość domyślna to 255. Tokeny dłuższe niż maksymalna długość są dzielone. Maksymalna długość tokenu, która może być używana, to 300 znaków.<br /><br /> stopwords (typ: tablica ciągów) - Lista stopwords. Wartością domyślną jest pusta lista.|  
|standardasciifolding.lucene|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Standardowy analizator z filtrem składanym Ascii. |  
|[Zatrzymać](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer (StopAnalyzer)|Dzieli tekst na inne litery, stosuje małe i stopword filtry tokenów.<br /><br /> **Opcje**<br /><br /> stopwords (typ: tablica ciągów) - Lista stopwords. Wartość domyślna to wstępnie zdefiniowana lista dla języka angielskiego. |  
|[Odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Analizator, który używa tokenizatora odstępów. Tokeny, które są dłuższe niż 255 znaków są podzielone.|  

 <sup>1</sup> Typy analizatora są zawsze poprzedzone w kodzie z "#Microsoft.Azure.Search", takie, że "PatternAnalyzer" faktycznie zostanie określony jako "#Microsoft.Azure.Search.PatternAnalyzer". Usunęliśmy prefiks dla zwięzłości, ale prefiks jest wymagany w kodzie. 
 
Analyzer_type jest dostępna tylko dla analizatorów, które można dostosować. Jeśli nie ma żadnych opcji, jak w przypadku analizatora słów kluczowych, nie ma skojarzonego typu #Microsoft.Azure.Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Odwołanie do filtrów char

W poniższej tabeli filtry znaków, które są implementowane przy użyciu Apache Lucene są połączone z dokumentacją interfejsu API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Filtr char, który próbuje usunąć konstrukcje HTML.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MapOwanieFilter|Filtr char, który stosuje mapowania zdefiniowane za pomocą opcji mapowania. Dopasowanie jest chciwi (najdłuższy wzór dopasowywania w danym punkcie wygrywa). Wymiana może być pustym ciągiem.<br /><br /> **Opcje**<br /><br /> mapowania (typ: tablica ciągów) - Lista mapowań w następującym formacie: "a=>b" (wszystkie wystąpienia znaku "a" są zastępowane znakiem "b"). Wymagany.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceFilter nadrzędny|Filtr char, który zastępuje znaki w ciągu wejściowym. Używa wyrażenia regularnego do identyfikowania sekwencji znaków w celu zachowania i wzorca zastępczego do identyfikowania znaków do zastąpienia. Na przykład tekst wejściowy = "aa bb aa bb", pattern="(aa)\\\s+(bb)" replacement="$1#$2", result = "aa#bb aa#bb".<br /><br /> **Opcje**<br /><br /> wzór (typ: ciąg) - Wymagane.<br /><br /> wymiana (typ: ciąg) - Wymagane.|  

 <sup>1</sup> Typy filtrów znaków są zawsze poprzedzone w kodzie z "#Microsoft.Azure.Search" takie, że "MappingCharFilter" faktycznie zostanie określony jako "#Microsoft.Azure.Search.MappingCharFilter. Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby dołączyć go do kodu. Należy zauważyć, że char_filter_type jest dostępna tylko dla filtrów, które można dostosować. Jeśli nie ma żadnych opcji, jak to ma miejsce w przypadku html_strip, nie ma skojarzonego typu #Microsoft.Azure.Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Odwołanie do tokenizatorów

W poniższej tabeli tokenizatorów, które są implementowane przy użyciu Apache Lucene są połączone z dokumentacji interfejsu API Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[Klasyczny](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|KlasycznyTokenizer|Tokenizator oparty na gramatyki, który nadaje się do przetwarzania większości dokumentów w języku europejskim.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Domyślnie: 255, maksymalnie: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizuje dane wejściowe z krawędzi do n-gramów danego rozmiaru(-ów).<br /><br /> **Opcje**<br /><br /> minGram (typ: int) - Domyślnie: 1, maksymalnie: 300.<br /><br /> maxGram (typ: int) - Domyślnie: 2, maksymalnie: 300. Musi być większa niż minGram.<br /><br /> tokenChars (typ: tablica ciąg) - Klasy znaków, aby zachować w tokenach. Dozwolone wartości: <br />"letter", "digit", "whitespace", "interpunkcja", "symbol". Domyślnie pusta tablica — zachowuje wszystkie znaki. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emituje całe dane wejściowe jako pojedynczy token.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Domyślnie: 256, maksymalnie: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[Literę](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Dzieli tekst na inne niż litery. Tokeny, które są dłuższe niż 255 znaków są podzielone.|  
|[Małe litery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Dzieli tekst na litery inne i konwertuje go na małe litery. Tokeny, które są dłuższe niż 255 znaków są podzielone.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Dzieli tekst przy użyciu reguł specyficznych dla języka.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - Maksymalna długość tokenu, domyślnie: 255, maksymalna: 300. Tokeny dłuższe niż maksymalna długość są dzielone. Tokeny dłuższe niż 300 znaków są najpierw podzielone na tokeny o długości 300, a następnie każdy z tych tokenów jest dzielony na podstawie zestawu maxTokenLength.<br /><br />isSearchTokenizer (typ: bool) - Ustaw true, jeśli jest używany jako tokenizator wyszukiwania, zestaw false, jeśli jest używany jako tokenizator indeksowania. <br /><br /> język (typ: ciąg) - Język do użycia, domyślnie "angielski". Dozwolone wartości obejmują:<br />"bangla", "bułgarski", "kataloński", "chiński Uproszczony", "chińskiTradicyjny", "chorwacki", "czeski", "duński", "holenderski", "angielski", "francuski", "niemiecki", "grecki", "gudżarati", "hindi", "islandzki", "indonezyjski", "włoski", "japoński", "kannada", " koreański", "malajski", "malayalam", "marathi", "norwegianBokmaal", "polski", "portugalski", "portugalskiBrazilian", "pendżabski", "rumuński", "rosyjski", "serbskiCyrillic", "serbskiLatin", "słoweński", "hiszpański", "szwedzki", "tamil", "telugu", "tajski", "tajski", " ukraiński", "urdu", "wietnamski" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Dzieli tekst za pomocą reguł specyficznych dla języka i zmniejsza liczbę wyrazów do ich formularzy podstawowych<br /><br /> **Opcje**<br /><br />maxTokenLength (typ: int) - Maksymalna długość tokenu, domyślnie: 255, maksymalna: 300. Tokeny dłuższe niż maksymalna długość są dzielone. Tokeny dłuższe niż 300 znaków są najpierw podzielone na tokeny o długości 300, a następnie każdy z tych tokenów jest dzielony na podstawie zestawu maxTokenLength.<br /><br /> isSearchTokenizer (typ: bool) - Ustaw true, jeśli jest używany jako tokenizator wyszukiwania, zestaw false, jeśli jest używany jako tokenizator indeksowania.<br /><br /> język (typ: ciąg) - Język do użycia, domyślnie "angielski". Dozwolone wartości obejmują:<br />"arabic", "bangla", "bulgarian", "catalan", "croatian", "czech", "danish", "dutch", "english", "estonian", "finnish", "french", "german", "greek", "gujarati", "hebrew", "hindi", "hungarian", "icelandic", "indonesian", "italian", "kannada", " łotewski", "litewski", "malajski", "malayalam", "marathi", "norwegianBokmaal", "polski", "portugalski", "portugueseBrazylian", "pendżabski", "rumuński", "rosyjski", "serbskiCyrillic", "serbianLatin", "słowacki", "słoweński", "hiszpański", "szwedzki", " tamil", "telugu", "turecki", "ukraiński", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizuje dane wejściowe do n-gramów danego rozmiaru(-ów).<br /><br /> **Opcje**<br /><br /> minGram (typ: int) - Domyślnie: 1, maksymalnie: 300.<br /><br /> maxGram (typ: int) - Domyślnie: 2, maksymalnie: 300. Musi być większa niż minGram. <br /><br /> tokenChars (typ: tablica ciąg) - Klasy znaków, aby zachować w tokenach. Dozwolone wartości: "letter", "digit", "whitespace", "interpunkcja", "symbol". Domyślnie pusta tablica — zachowuje wszystkie znaki. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|ŚcieżkaHierarchyTokenizerV2|Tokenizator dla hierarchii podobnych do ścieżki.<br /><br /> **Opcje**<br /><br /> ogranicznik (typ: ciąg) - Domyślnie: '/.<br /><br /> wymiana (typ: ciąg) - Jeśli jest ustawiona, zastępuje znak ogranicznika. Domyślnie taka sama jak wartość ogranicznika.<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Domyślnie: 300, maksymalnie: 300. Ścieżki dłuższe niż maxTokenLength są ignorowane.<br /><br /> reverse (typ: bool) - Jeśli true, generuje token w odwrotnej kolejności. Wartość domyślna: false.<br /><br /> skip (typ: bool) - Początkowe tokeny do pominięcia. Wartość domyślna to 0.|  
|[Wzór](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer (WzornikTokenizer)|Ten tokenizator używa dopasowania wzorca wyrażenia regularnego do konstruowania różnych tokenów.<br /><br /> **Opcje**<br /><br /> [wzorzec](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (typ: ciąg) - Wzorzec wyrażenia regularnego. Wartość domyślna to \W+. <br /><br /> [flagi](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (typ: ciąg) - Flagi wyrażeń regularnych. Wartość domyślna to pusty ciąg. Dozwolone wartości: CANON_EQ, CASE_INSENSITIVE, KOMENTARZE, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> group (type: int) - Która grupa ma być wyodrębnina w tokeny. Wartość domyślna to -1 (podział).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Przerywa tekst zgodnie z [regułami segmentacji tekstu Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Domyślnie: 255, maksymalnie: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxurlEmailTokenizer|Tokenizes adresy URL i e-maile jako jeden token.<br /><br /> **Opcje**<br /><br /> maxTokenLength (typ: int) - maksymalna długość tokenu. Domyślnie: 255, maksymalnie: 300. Tokeny dłuższe niż maksymalna długość są dzielone.|  
|[Odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Dzieli tekst w odstępach. Tokeny, które są dłuższe niż 255 znaków są podzielone.|  

 <sup>1</sup> Typy tokenizatora są zawsze poprzedzone w kodzie z "#Microsoft.Azure.Search", takie jak "ClassicTokenizer" faktycznie zostanie określony jako "#Microsoft.Azure.Search.ClassicTokenizer". Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby dołączyć go do kodu. Należy zauważyć, że tokenizer_type jest dostępna tylko dla tokenizatorów, które można dostosować. Jeśli nie ma żadnych opcji, jak w przypadku tokenizatora liter, nie ma skojarzonego typu #Microsoft.Azure.Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Odwołanie do filtrów tokenów

W poniższej tabeli filtry tokenu, które są implementowane przy użyciu Apache Lucene są połączone z dokumentacją interfejsu API Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Opis i opcje**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Filtr tokenu, który stosuje normalizację języka arabskiego do normalizacji ortografii.|  
|[Apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Paski wszystkie znaki po apostrofu (w tym apostrof się). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|Filtr AsciiFoldingToken|Konwertuje alfabetyczne, numeryczne i symboliczne znaki Unicode, które nie znajdują się w pierwszych 127 znakach ASCII ("Basic Latin" Unicode block) na ich odpowiedniki ASCII, jeśli taki istnieje.<br /><br /> **Opcje**<br /><br /> preserveOriginal (typ: bool) - Jeśli true, oryginalny token jest zachowywany. Wartością domyślną jest false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|Filtr CjkBigramToken|Tworzy bigramy terminów CJK, które są generowane z StandardTokenizer.<br /><br /> **Opcje**<br /><br /> ignoreScripts (typ: tablica ciągów) - Skrypty do zignorowania. Dozwolone wartości to: "han", "hiragana", "katakana", "hangul". Wartością domyślną jest pusta lista.<br /><br /> outputUnigrams (typ: bool) - Ustaw wartość true, jeśli zawsze chcesz wyprowadzić zarówno unigramy, jak i bigramy. Wartością domyślną jest false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje różnice szerokości CJK. Składa warianty ASCII o pełnej szerokości w równoważne podstawowe warianty katakana o łacinie i połowie szerokości w odpowiednik kana. |  
|[Klasyczny](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Usuwa angielskie zaborcze i kropki z akronimów. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|Filtr CommonGramToken|Konstruuj bigramy dla często występujących terminów podczas indeksowania. Pojedyncze terminy są nadal indeksowane zbyt, z bigrams nałożone.<br /><br /> **Opcje**<br /><br /> commonWords (typ: tablica ciągów) - Zestaw wspólnych słów. Wartością domyślną jest pusta lista. Wymagany.<br /><br /> ignoreCase (typ: bool) - Jeśli true, dopasowanie jest bez uwzględniania wielkości liter. Wartością domyślną jest false.<br /><br /> queryMode (typ: bool) - Generuje bigrams następnie usuwa wspólne słowa i pojedyncze terminy następuje wspólne słowo. Wartością domyślną jest false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|SłownikDekompozytTokenFilter|Rozkłada złożone słowa występujące w wielu językach germańskich.<br /><br /> **Opcje**<br /><br /> wordList (typ: tablica ciągów) - Lista słów do dopasowania. Wartością domyślną jest pusta lista. Wymagany.<br /><br /> minWordSize (typ: int) — przetwarzane są tylko słowa dłuższe niż te. Wartość domyślna to 5.<br /><br /> minSubwordSize (typ: int) — wyprowadzane są tylko podsłowy dłuższe niż te. Wartość domyślna to 2.<br /><br /> maxSubwordSize (typ: int) — wyprowadzane są tylko podsłowy krótsze niż te. Wartość domyślna to 15.<br /><br /> onlyLongestMatch (typ: bool) - Dodaj tylko najdłuższe pasujące podsłodo do danych wyjściowych. Wartością domyślną jest false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Generuje n-gramów danego rozmiaru(-ów) od początku od przodu lub z tyłu tokenu wejściowego.<br /><br /> **Opcje**<br /><br /> minGram (typ: int) - Domyślnie: 1, maksymalnie: 300.<br /><br /> maxGram (typ: int) - Domyślnie: 2, maksymalnie 300. Musi być większa niż minGram.<br /><br /> strona (typ: ciąg) - Określa, z której strony wejścia powinien zostać wygenerowany n-gram. Dozwolone wartości: "przód", "tył" |  
|[elision (elision)](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|Filtr ElisionToken|Usuwa elisions. Na przykład "l'avion" (płaszczyzna) jest konwertowany na "avion" (płaszczyzna).<br /><br /> **Opcje**<br /><br /> artykuły (typ: tablica ciągów) — zestaw artykułów do usunięcia. Wartością domyślną jest pusta lista. Jeśli nie ma ustawionej listy artykułów, domyślnie wszystkie artykuły francuskie są usuwane.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje niemieckie znaki zgodnie z heurystyką [niemieckiego algorytmu kuli śnieżnej2](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje tekst w języku hindi, aby usunąć pewne różnice w odmianach pisowni. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normalizuje reprezentację tekstu w języku indyjskim w języku indyjskim.
|[Zachować](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|Filtr KeepToken|Filtr tokenu, który przechowuje tylko tokeny z tekstem zawartym w określonej liście słów.<br /><br /> **Opcje**<br /><br /> keepWords (typ: tablica ciągów) — lista słów do zachowania. Wartością domyślną jest pusta lista. Wymagany.<br /><br /> keepWordsCase (typ: bool) - Jeśli true, małe litery najpierw wszystkie słowa. Wartością domyślną jest false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Oznacza terminy jako słowa kluczowe.<br /><br /> **Opcje**<br /><br /> słowa kluczowe (typ: tablica ciągów) - lista słów do oznaczenia jako słowa kluczowe. Wartością domyślną jest pusta lista. Wymagany.<br /><br /> ignoreCase (typ: bool) - Jeśli true, małe litery najpierw wszystkie słowa. Wartością domyślną jest false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Emituje każdy przychodzący token dwa razy jako słowo kluczowe i raz jako słowo kluczowe. |  
|[kstem ( kstem )](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Wysokowydajny filtr kstem dla języka angielskiego. |  
|[Długość](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|Filtr DługościToken|Usuwa słowa, które są zbyt długie lub zbyt krótkie.<br /><br /> **Opcje**<br /><br /> min (typ: int) - Minimalna liczba. Domyślnie: 0, maksymalnie: 300.<br /><br /> max (typ: int) - Maksymalna liczba. Domyślnie: 300, maksymalnie: 300.|  
|[Limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Ogranicza liczbę tokenów podczas indeksowania.<br /><br /> **Opcje**<br /><br /> maxTokenCount (typ: int) — maksymalna liczba tokenów do wyprodukowania. Domyślnym ustawieniem jest 1.<br /><br /> consumeAllTokens (typ: bool) - Czy wszystkie tokeny z danych wejściowych muszą być używane, nawet jeśli maxTokenCount zostanie osiągnięty. Wartością domyślną jest false.|  
|[Małe litery](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje tekst tokenu na małe litery. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Generuje n-gramy danego rozmiaru(-ów).<br /><br /> **Opcje**<br /><br /> minGram (typ: int) - Domyślnie: 1, maksymalnie: 300.<br /><br /> maxGram (typ: int) - Domyślnie: 2, maksymalnie 300. Musi być większa niż minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Używa regexes Java emituje wiele tokenów, po jednym dla każdej grupy przechwytywania w jeden lub więcej wzorców.<br /><br /> **Opcje**<br /><br /> wzorce (typ: tablica ciągów) — lista wzorców, które mają być zgodne z każdym tokenem. Wymagany.<br /><br /> preserveOriginal (typ: bool) - Ustaw wartość true, aby zwrócić oryginalny token, nawet jeśli jeden z wzorców pasuje, domyślnie: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|Filtr wzoruReplaceToken|Filtr tokenu, który stosuje wzorzec do każdego tokenu w strumieniu, zastępując wystąpienia dopasowania określonym ciągiem zastępczym.<br /><br /> **Opcje**<br /><br /> wzór (typ: ciąg) - Wymagane.<br /><br /> wymiana (typ: ciąg) - Wymagane.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje) |Stosuje normalizację dla perskiego. |  
|[Fonetyczny](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|Filtr Fonetyczny Doken|Tworzenie tokenów dla meczów fonetycznych.<br /><br /> **Opcje**<br /><br /> koder (typ: ciąg) - Koder fonetyczny do użycia. Dozwolone wartości to: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Domyślnie: "metaphone". Domyślnie jest to metafon.<br /><br /> Zobacz [koder, aby](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) uzyskać więcej informacji.<br /><br /> replace (type: bool) - Prawda, jeśli zakodowane tokeny powinny zastąpić oryginalne tokeny, false, jeśli powinny być dodawane jako synonimy. Wartość domyślna jest true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Przekształca strumień tokenu zgodnie z [algorytmem wynikające porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[Odwrócić](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Odwraca ciąg tokenu. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje użycie wymiennych znaków skandynawskich. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Składa skandynawskie postacie åÅäæÄÆ->i ööøø->o. Dyskryminuje również stosowanie podwójnych samogłosek aa, ae, ao, oe i oo, pozostawiając tylko pierwszą. |  
|[Gont](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|Filtr gontuToken|Tworzy kombinacje tokenów jako pojedynczy token.<br /><br /> **Opcje**<br /><br /> maxShingleSize (typ: int) - Domyślnie 2.<br /><br /> minShingleSize (typ: int) - Domyślnie 2.<br /><br /> outputUnigrams (typ: bool) - jeśli true, strumień wyjściowy zawiera tokeny wejściowe (unigramy) oraz półpasiec. Wartość domyślna jest true.<br /><br /> outputUnigramsIfNoShingles (typ: bool) - Jeśli true, zastąpić zachowanie outputUnigrams==false dla tych czasów, gdy nie półpasiec są dostępne. Wartością domyślną jest false.<br /><br /> tokenSeparator (typ: ciąg) — ciąg używany podczas łączenia sąsiednich tokenów w celu utworzenia gontu. Wartość domyślna to " ".<br /><br /> filterToken (typ: ciąg) - Ciąg do wstawienia dla każdej pozycji, w której nie ma tokenu. Wartość domyślna to "_".|  
|[Śnieżki](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtr tokenu kuli śnieżnej.<br /><br /> **Opcje**<br /><br /> język (typ: string) - Dozwolone wartości to: "ormiański", "baskijski", "kataloński", "duński", "holenderski", "angielski", "fiński", "francuski", "niemiecki", "german2", "węgierski", "włoski", "kp", "lovins", "norweski", "porter", "portugalski", "rumuński", "rumuński", " rosyjski", "hiszpański", "szwedzki", "turecki"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normalizuje reprezentację Unicode tekstu Sorani.<br /><br /> **Opcje**<br /><br /> Brak.|  
|stemmer (właz|Filtr StemmerToken|Filtr wynikowy specyficzny dla języka.<br /><br /> **Opcje**<br /><br /> język (typ: ciąg) - Dozwolone wartości obejmują: <br /> -   ["arabski"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["ormiański"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["baskijski"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["Bardzo"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "bułgarski"<br />-   ["kataloński"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["Czeski"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["duński"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["holenderski"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["Bardzo wyd."](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lekkiangielski"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalangielski"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["zaborczyanglski"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["Lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["fiński"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   ["francuski"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />- "galicyjski"<br />- "minimalGalician"<br />-   ["niemiecki"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["światłoNiemiec"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalnyGerman"<br />-   ["Grecki"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["węgierski"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["światłoHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonezyjski"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irlandzki"](https://snowballstem.org/otherapps/oregan/)<br />-   ["bardzo"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["światłoWłoski"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["łotewski"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norweski"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["światłoNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalnorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portugalski"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["Rumuński"](https://snowballstem.org/otherapps/romanian/)<br />-   ["rosyjski"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["światłoRosyjskie"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["hiszpański"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["światłoSpany"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["szwedzki"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "światłoWedysz"<br />-   ["turecki"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|Filtr StemmerOverrideToken|Każdy słownik- Stemmed terminy są oznaczone jako słowa kluczowe, co zapobiega wynikające w dół łańcucha. Należy umieścić przed jakimikolwiek filtrami odcinającymi.<br /><br /> **Opcje**<br /><br /> reguły (typ: tablica ciągów) - Reguły wynikające w następującym formacie "word => stem" na przykład "ran => run". Wartością domyślną jest pusta lista.  Wymagany.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Usuwa słowa zatrzymania ze strumienia tokenu. Domyślnie filtr używa wstępnie zdefiniowanej listy słów stop dla języka angielskiego.<br /><br /> **Opcje**<br /><br /> stopwords (typ: tablica ciągów) - Lista stopwords. Nie można określić, jeśli określono stopwordsList.<br /><br /> stopwordsList (typ: ciąg) - Wstępnie zdefiniowana lista stopwords. Nie można określić, jeśli określono stopwords. Dozwolone wartości to:"arabski", "ormiański", "baskijski", "brazylijski", "bułgarski", "kataloński", "czeski", "duński", "holenderski", "angielski", "fiński", "francuski", "galicyjski", "niemiecki", "grecki", "hindi", "węgierski", "indonezyjski", "irlandzki", "włoski", "łotewski ", "norweski", "perski", "portugalski", "rumuński", "rosyjski", "sorani", "hiszpański", "szwedzki", "tajski", "turecki", domyślnie: "angielski". Nie można określić, jeśli określono stopwords. <br /><br /> ignoreCase (typ: bool) - Jeśli true, wszystkie słowa są małe litery pierwszy. Wartością domyślną jest false.<br /><br /> removeTrailing (typ: bool) - Jeśli true, zignoruj ostatni wyszukiwany termin, jeśli jest to słowo stop. Wartość domyślna jest true.
|[Synonim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|Filtr SynonimToken|Dopasowuje synonimy pojedyncze lub wielowyrazowe w strumieniu tokenu.<br /><br /> **Opcje**<br /><br /> synonimy (typ: tablica ciągów) - Wymagane. Lista synonimów w jednym z następujących dwóch formatów:<br /><br /> -incredible, unbelievable, fabulous => amazing - wszystkie terminy po lewej stronie => symbol są zastępowane wszystkimi terminami po prawej stronie.<br /><br /> -niesamowite, niewiarygodne, wspaniałe, niesamowite - rozdzielona przecinkami lista równoważnych słów. Ustaw opcję rozwijania, aby zmienić sposób interpretacji tej listy.<br /><br /> ignoreCase (typ: bool) - Dane wejściowe case-fold do dopasowania. Wartością domyślną jest false.<br /><br /> rozwiń (typ: bool) - Jeśli true, wszystkie wyrazy na liście synonimów (jeśli => notacji nie jest używany) mapują się do siebie. <br />Następująca lista: niesamowite, niewiarygodne, wspaniałe, niesamowite jest odpowiednikiem: niesamowite, niewiarygodne, wspaniałe, niesamowite => niesamowite, niewiarygodne, wspaniałe, niesamowite<br /><br />- Jeśli fałszywe, następująca lista: niesamowite, niewiarygodne, wspaniałe, niesamowite są równoważne: niesamowite, niewiarygodne, wspaniałe, niesamowite => niesamowite.|  
|[Przycinanie](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Przycina odstępy początkowe i końcowe z tokenów. |  
|[Obciąć](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|ObciąłFiltr Doken|Obcina terminy na określoną długość.<br /><br /> **Opcje**<br /><br /> długość (typ: int) - Domyślnie: 300, maksymalnie: 300. Wymagany.|  
|[Unikatowy](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|Filtr UniqueToken|Odfiltrowywają tokeny z tym samym tekstem co poprzedni token.<br /><br /> **Opcje**<br /><br /> onlyOnSamePosition (typ: bool) - Jeśli jest ustawiona, usuń duplikaty tylko w tej samej pozycji. Wartość domyślna jest true.|  
|[Wielkie](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(typ ma zastosowanie tylko wtedy, gdy dostępne są opcje)  |Normalizuje tekst tokenu na wielkie litery. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|Filtr WordDelimiterTokenFilter|Dzieli wyrazy na podwory i wykonuje opcjonalne przekształcenia w grupach podsłogów.<br /><br /> **Opcje**<br /><br /> generateWordParts (typ: bool) — powoduje, że części słów mają być generowane, na przykład "AzureSearch" staje się "Azure" "Search". Wartość domyślna jest true.<br /><br /> generateNumberParts (typ: bool) - Powoduje wygenerowanie podworów liczbowych. Wartość domyślna jest true.<br /><br /> catenateWords (typ: bool) — powoduje, że maksymalna liczba przebiegów części wyrazu podlega katecheczowi, na przykład "Azure-Search" staje się "AzureSearch". Wartością domyślną jest false.<br /><br /> catenateNumbers (typ: bool) - Powoduje, że maksymalne przebiegi części liczbowych mają być kategoryczne, na przykład "1-2" staje się "12". Wartością domyślną jest false.<br /><br /> catenateAll (typ: bool) — powoduje, że wszystkie części podsłożu mają być kategoryzowane, na przykład "Azure-Search-1" staje się "AzureSearch1". Wartością domyślną jest false.<br /><br /> splitOnCaseChange (typ: bool) — jeśli true, dzieli wyrazy na caseChange, na przykład "AzureSearch" staje się "Azure" "Search". Wartość domyślna jest true.<br /><br /> preserveOriginal — powoduje zachowanie oryginalnych wyrazów i dodawania ich do listy podsłonów. Wartością domyślną jest false.<br /><br /> splitOnNumerics (typ: bool) — jeśli true, dzieli się na liczby, na przykład "Azure1Search" staje się "Azure" "1" "Search". Wartość domyślna jest true.<br /><br /> stemEnglishPossessive (typ: bool) - Powoduje, że końcowe "s" do usunięcia dla każdego podsłowa. Wartość domyślna jest true.<br /><br /> protectedWords (typ: tablica ciągów) — tokeny chroniące przed rozdzielaniem. Wartością domyślną jest pusta lista.|  

 <sup>1</sup> Typy filtrów tokenów są zawsze poprzedzone w kodzie z "#Microsoft.Azure.Search" w taki sposób, że "ArabicNormalizationTokenFilter" faktycznie zostanie określony jako "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Usunęliśmy prefiks, aby zmniejszyć szerokość tabeli, ale pamiętaj, aby dołączyć go do kodu.  


## <a name="see-also"></a>Zobacz też  
 [Interfejsy API rest wyszukiwania funkcji Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analizatory w usłudze Azure Cognitive Search > przykłady](search-analyzers.md#examples)    
 [Tworzenie &#40;interfejsu API usługi Azure Cognitive Search REST&#41;&#40;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
