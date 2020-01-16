---
title: Dopasowanie wzorców i znaków specjalnych
titleSuffix: Azure Cognitive Search
description: Wykonaj zapytania z symbolami wieloznacznymi i prefiksami, aby dopasować całe lub częściowe warunki w żądaniu zapytania Wyszukiwanie poznawcze platformy Azure. Wzorce twarde do dopasowania, które zawierają znaki specjalne, można rozpoznać przy użyciu pełnej składni zapytań i analizatorów niestandardowych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989620"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Dopasowanie do wzorców i znaków specjalnych (łączników)

W przypadku zapytań zawierających znaki specjalne (`-, *, (, ), /, \, =`) lub dla wzorców kwerendy opartych na częściowych terminach w większym okresie, dodatkowe czynności konfiguracyjne są zwykle wymagane do zapewnienia, że indeks zawiera oczekiwaną zawartość, w odpowiednim formacie. 

Domyślnie numer telefonu, taki jak `+1 (425) 703-6214`, jest uznawany za token `"1"`, `"425"`, `"703"`, `"6214"`. Jak można wyobrazić, wyszukiwanie `"3-62"`, częściowe warunki, które zawierają łącznik, zakończy się niepowodzeniem, ponieważ zawartość nie istnieje w indeksie. 

Gdy musisz przeszukać ciągi częściowe lub znaki specjalne, możesz zastąpić analizatora niestandardowego analizatorem niestandardowym, który działa w prostszych regułach tokenizacji, zachowując całe warunki, które są niezbędne, gdy ciągi zapytania zawierają części terminu lub specjalne znaków. Po przełączeniu do tyłu podejście wygląda następująco:

+ Wybierz wstępnie zdefiniowany Analizator lub Zdefiniuj niestandardowy Analizator, który generuje żądane dane wyjściowe
+ Przypisz Analizator do pola
+ Kompilowanie indeksu i testu

Ten artykuł przeprowadzi Cię przez te zadania. Opisane w tym miejscu podejście jest przydatne w innych scenariuszach: zapytania z symbolami wieloznacznymi i wyrażeniami regularnymi potrzebują również całych terminów jako podstawy do dopasowania wzorców. 

> [!TIP]
> Ocenianie analyers jest procesem iteracyjnym wymagającym częstych rekompilacji indeksów. Ten krok można ułatwić przy użyciu programu Poster, interfejsów API REST do [tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index), [usuwania indeksu](https://docs.microsoft.com/rest/api/searchservice/delete-index),[ładowania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)i [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents). W przypadku dokumentów załadunkowych treść żądania powinna zawierać mały reprezentatywny zestaw danych, który ma zostać przetestowany (na przykład pole z numerami telefonów lub kodami produktów). Za pomocą tych interfejsów API w tej samej kolekcji ogłoszeń można szybko wykonać te kroki.

## <a name="choosing-an-analyzer"></a>Wybieranie analizatora

W przypadku wybrania analizatora, który tworzy tokeny całodzienne, typowe są następujące analizatory:

| Analizator | Zachowania |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Zawartość całego pola jest tokenem pojedynczym terminem. |
| [odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Oddziela tylko odstępy. Warunki, które zawierają łączniki lub inne znaki, są traktowane jako pojedynczy token. |
| [Analizator niestandardowy](index-add-custom-analyzers.md) | Rekomendowane Utworzenie analizatora niestandardowego pozwala określić zarówno tokenizatora, jak i filtr tokenu. Poprzednie analizatory muszą być używane jako-is. Analizator niestandardowy umożliwia wybranie filtrów tokenizatory i tokenów, które mają być używane. <br><br>Zalecaną kombinacją jest [słowo kluczowe tokenizatora](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) z [filtrem tokenu o niższym przypadku](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). We wstępnie zdefiniowanym [analizatorze słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nie jest rozróżniana wielkość liter, co może spowodować niepowodzenie zapytań. Analizator niestandardowy zapewnia mechanizm dodawania do filtru tokenów małych liter. |

Jeśli używasz narzędzia testowego interfejsu API sieci Web, takiego jak Poster, możesz dodać [wywołanie REST analizatora testu](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) , aby przeprowadzić inspekcję danych wyjściowych z tokenami. Uwzględniając istniejący indeks i pole zawierające łączniki lub częściowe warunki, możesz wypróbować różne analizatory, aby zobaczyć, jakie tokeny są emitowane.  

1. Sprawdź Analizator standardowej, aby zobaczyć, jak są domyślnie opisane tokeny.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Oceń odpowiedź, aby zobaczyć, w jaki sposób tekst jest tokenem w indeksie. Zwróć uwagę na to, jak każdy termin jest niższy i podzielony na siebie.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Zmodyfikuj żądanie, aby użyć analizatora `whitespace` lub `keyword`:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Teraz odpowiedź składa się z pojedynczego tokenu, wielkie litery, za pomocą łączników zachowywanych jako część ciągu. Jeśli chcesz wyszukać wzorzec lub częściowy termin, aparat zapytań ma teraz podstawę do znalezienia dopasowania.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Podczas kompilowania drzewa zapytań należy pamiętać, że analizatory zapytań często mają małe litery w wyrażeniu wyszukiwania. Jeśli używasz analizatora, który nie uwzględnia małych liter tekstowych, i nie otrzymujesz oczekiwanych wyników, może to być przyczyną. Rozwiązaniem jest dodanie filtru tokenów lwower.

## <a name="analyzer-definitions"></a>Definicje analizatora
 
Bez względu na to, czy oceniasz analizatory, czy przenosisz się do przodu przy użyciu określonej konfiguracji, musisz określić analizatorze w definicji pola i ewentualnie skonfigurować Analizator, jeśli nie używasz wbudowanej analizatora. Podczas wymiany analizatorów należy zwykle ponownie skompilować indeks (Porzuć, Odtwórz ponownie i Załaduj ponownie). 

### <a name="use-built-in-analyzers"></a>Używanie wbudowanych analizatorów

Wbudowane lub wstępnie zdefiniowane analizatory mogą być określone przez nazwę we właściwości `analyzer` definicji pola, bez konieczności dodatkowej konfiguracji w indeksie. W poniższym przykładzie pokazano, jak ustawić analizator `whitespace` w polu.

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
Aby uzyskać więcej informacji na temat wszystkich dostępnych wbudowanych analizatorów, zobacz [Lista wstępnie zdefiniowanych analizatorów](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Korzystanie z analizatorów niestandardowych

Jeśli używasz [analizatora niestandardowego](index-add-custom-analyzers.md), zdefiniuj go w indeksie przy użyciu zdefiniowanej przez użytkownika kombinacji tokenizatora, tokenfilter, z możliwymi ustawieniami konfiguracji. Następnie odwołują się do niego w definicji pola tak samo jak w przypadku wbudowanej analizatora.

Gdy celem jest tokenizacji w całości, zaleca się użycie analizatora niestandardowego, który składa się z **słowa kluczowego tokenizatora** i **niskiego poziomu** .

+ Słowo kluczowe tokenizatora tworzy pojedynczy token dla całej zawartości pola.
+ Filtr tokenów małymi literami przekształca wielkie litery w tekst małymi literami. Analizatory zapytań zwykle małymi literami danych tekstowych. Lowercasing homogenizować dane wejściowe za pomocą tokenów postanowień.

Poniższy przykład ilustruje niestandardowy Analizator, który udostępnia tokenizatora słów kluczowych i filtr tokenu z małymi literami.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Filtr tokenów `keyword_v2` tokenizatora i `lowercase` jest znany systemowi i korzysta z ich domyślnych konfiguracji, dlatego można odwoływać się do nich według nazwy bez konieczności definiowania ich jako pierwszej.

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="tune-query-performance"></a>Dostosowywanie wydajności zapytań

W przypadku zaimplementowania zalecanej konfiguracji obejmującej filtr keyword_v2 tokenizatora i niższych wielkości liter można zauważyć spadek wydajności zapytania ze względu na dodatkowe przetwarzanie filtru tokenów dla istniejących tokenów w indeksie. 

Poniższy przykład dodaje [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) , aby przyspieszyć dopasowanie prefiksu. Dodatkowe tokeny są generowane dla kombinacji znaków 2-25, które zawierają znaki: (nie tylko MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/kW, MSFT/SQL). Jak można wyobrazić, dodatkowe tokenizacji wyniki w większym indeksie.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Używanie różnych analizatorów do indeksowania i przetwarzania zapytań

Analizatory są wywoływane podczas indeksowania i podczas wykonywania zapytania. Jest to typowy sposób użycia tego samego analizatora dla obu, ale dla każdego obciążenia można skonfigurować niestandardowe analizy. Zastąpienia analizatora są określone w [definicji indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) w sekcji `analyzers`, a następnie przywoływane dla określonych pól. 

Gdy analiza niestandardowa jest wymagana tylko podczas indeksowania, można zastosować Analizator niestandardowy do samego indeksowania i nadal używać standardowego analizatora Lucene (lub innego analizatora) dla zapytań.

Aby określić analizę specyficzną dla roli, można ustawić właściwości dla każdego z nich, ustawiając `indexAnalyzer` i `searchAnalyzer` zamiast domyślnej właściwości `analyzer`.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Duplikowanie pól dla różnych scenariuszy

Inna opcja wykorzystuje przypisanie analizatora poszczególnych pól w celu optymalizacji pod kątem różnych scenariuszy. W zależności od tego można zdefiniować "featureCode" i "featureCodeRegex" do obsługi regularnego wyszukiwania pełnotekstowego w pierwszej i zaawansowane dopasowywanie do wzorca w drugim.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak analizatory przyczyniają się do problemów z kwerendą i rozwiązują problemy z kwerendami. Następnym krokiem jest bliższe przybliżenie wpływu analizatora na indeksowanie i przetwarzanie zapytań. W szczególności należy rozważyć użycie interfejsu API analizy tekstu do zwracania tokenów wyjściowych, aby zobaczyć dokładnie, co Analizator tworzy dla indeksu.

+ [Analizatory języków](search-language-support.md)
+ [Analizatory do przetwarzania tekstu na platformie Azure Wyszukiwanie poznawcze](search-analyzers.md)
+ [Analizowanie interfejsu API tekstu (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak działa wyszukiwanie pełnotekstowe (architektura zapytania)](search-lucene-query-architecture.md)