---
title: Terminy częściowe, wzorce i znaki specjalne
titleSuffix: Azure Cognitive Search
description: Użyj wieloznacznych, wyrażenia regularnego i zapytań prefiksów, aby dopasować je w całości lub częściowych terminach w żądaniu zapytania usługi Azure Cognitive Search. Trudne do dopasowania wzorce, które zawierają znaki specjalne można rozpoznać za pomocą pełnej składni kwerendy i analizatorów niestandardowych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 3e0e0291ff855b4502224466e17696a4fe668c2a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656002"
---
# <a name="partial-term-search-in-azure-cognitive-search-queries-wildcard-regex-fuzzy-search-patterns"></a>Częściowe wyszukiwanie terminów w zapytaniach usługi Azure Cognitive Search (symbol wieloznaczny, wyrażenie regularne, wyszukiwanie rozmyte, wzorce)

*Częściowe wyszukiwanie terminów* odnosi się do zapytań składających się z fragmentów terminów, takich jak pierwsza, ostatnia lub wewnętrzna część ciągu lub wzorzec składający się z kombinacji fragmentów, często oddzielonych znakami specjalnymi, takimi jak kreski lub ukośniki. Typowe przypadki użycia obejmują wykonywanie zapytań dotyczących fragmentów numeru telefonu, adresu URL, osób lub kodów produktów lub słów złożonych.

Wyszukiwanie częściowe może być problematyczne, ponieważ sam indeks zazwyczaj nie przechowuje terminów w sposób, który sprzyja częściowym dopasowywaniu ciągów i wzorców. Podczas fazy analizy tekstu indeksowania znaki specjalne są odrzucane, ciągi złożone i złożone są dzielone, co powoduje niepowodzenie kwerend wzorca, gdy nie zostanie znalezione dopasowanie. Na `+1 (425) 703-6214`przykład numer telefonu, taki `"1"`jak `"425"` `"703"`(tokenized as , , , `"6214"`) nie będzie się wyświetlał w `"3-62"` kwerendzie, ponieważ ta zawartość w rzeczywistości nie istnieje w indeksie. 

Rozwiązaniem jest przechowywanie wersji tych ciągów w indeksie, dzięki czemu można obsługiwać scenariusze wyszukiwania częściowego. Tworzenie dodatkowego pola dla nienaruszonego ciągu, a także przy użyciu analizatora zachowania zawartości, jest podstawą rozwiązania.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Co to jest wyszukiwanie częściowe w usłudze Azure Cognitive Search

W usłudze Azure Cognitive Search wyszukiwanie częściowe jest dostępne w następujących formach:

+ [Wyszukiwanie prefiksów](query-simple-syntax.md#prefix-search), takie jak `search=cap*`, pasujące do "Cap'n Jack's Waterfront Inn" lub "Gacc Capital". Można użyć po prostu składni kwerendy do wyszukiwania prefiksów.
+ [Wyszukiwanie symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard) lub wyrażenia regularne, które [wyszukują](query-lucene-syntax.md#bkmk_regex) wzorzec lub części osadzonego ciągu, w tym sufiks. Na przykład, biorąc pod uwagę termin "alfanumeryczny",`search=/.*numeric.*/`należy użyć wyszukiwania symboli wieloznacznych ( ) dla dopasowania zapytania sufiksu w tym okresie. Symbol wieloznaczny i wyrażenia regularne wymagają pełnej składni lucene.

Gdy którykolwiek z powyższych typów zapytań są potrzebne w aplikacji klienckiej, wykonaj kroki opisane w tym artykule, aby upewnić się, że istnieje niezbędna zawartość w indeksie.

## <a name="solving-partial-search-problems"></a>Rozwiązywanie problemów z wyszukiwaniem częściowym

Gdy trzeba wyszukiwać wzorce lub znaki specjalne, można zastąpić analizatora domyślnego z analizatora niestandardowego, który działa w ramach prostszych reguł tokenizacji, zachowując cały ciąg. Robiąc krok wstecz, podejście wygląda następująco:

+ Zdefiniuj pole do przechowywania nienaruszonej wersji ciągu (przy założeniu, że chcesz przeanalizować i nie analizować tekstu)
+ Wybierz wstępnie zdefiniowany analizator lub zdefiniuj analizator niestandardowy, aby wyprokować nienaruszony ciąg
+ Przypisywanie analizatora do pola
+ Tworzenie i testowanie indeksu

> [!TIP]
> Analizowanie analizatorów jest procesem iteracyjnym, który wymaga częstego przebudowy indeksu. Ten krok można ułatwić, używając funkcji Postman, interfejsów API REST dla [tworzenia indeksu,](https://docs.microsoft.com/rest/api/searchservice/create-index) [indeksu](https://docs.microsoft.com/rest/api/searchservice/delete-index)[usuwania, ładowania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)i [wyszukiwania dokumentów.](https://docs.microsoft.com/rest/api/searchservice/search-documents) W przypadku ładowania dokumentów treść żądania powinna zawierać mały reprezentatywny zestaw danych, który chcesz przetestować (na przykład pole z numerami telefonów lub kodami produktów). Za pomocą tych interfejsów API w tej samej kolekcji postman, można szybko przełączać się między tymi krokami.

## <a name="duplicate-fields-for-different-scenarios"></a>Duplikuj pola dla różnych scenariuszy

Analizatory są przypisywane na podstawie pola, co oznacza, że można tworzyć pola w indeksie w celu optymalizacji dla różnych scenariuszy. W szczególności można zdefiniować "featureCode" i "featureCodeRegex", aby obsługiwać regularne wyszukiwanie pełnotekstowego w pierwszym i zaawansowane dopasowanie wzorca w drugim.

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

## <a name="choose-an-analyzer"></a>Wybieranie analizatora

Wybierając analizator, który produkuje tokeny całego okresu, następujące analizatory są typowe wybory:

| Analizator | Zachowania |
|----------|-----------|
| [Słowa kluczowego](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Zawartość całego pola jest tokenizowana jako pojedynczy termin. |
| [Odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Oddziela tylko na białych przestrzeniach. Terminy, które zawierają kreski lub inne znaki są traktowane jako pojedynczy token. |
| [analizator niestandardowy](index-add-custom-analyzers.md) | (zalecane) Tworzenie analizatora niestandardowego umożliwia określenie zarówno tokenizatora i filtru tokenów. Poprzednie analizatory muszą być używane w stanie— jest. Analizator niestandardowy umożliwia wybranie tokenizatorów i filtrów tokenów do użycia. <br><br>Zalecaną kombinacją jest [tokenizator słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) z [filtrem tokenów o mniejszej litery.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html) Sam [analizator wstępnie zdefiniowanych słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nie wykonuje wielkich liter bez żadnych wielkich liter, co może spowodować niepowodzenie kwerend. Analizator niestandardowy zapewnia mechanizm dodawania filtru tokenu dolnoślądkowych. |

Jeśli używasz narzędzia do testowania interfejsu API sieci web, takiego jak Postman, możesz dodać [wywołanie REST analizatora testów,](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) aby sprawdzić tokenizowane dane wyjściowe.

Musisz mieć istniejący indeks do pracy z. Biorąc pod uwagę istniejący indeks i pole zawierające myślniki lub terminy częściowe, można wypróbować różne analizatory w określonych terminach, aby zobaczyć, jakie tokeny są emitowane.  

1. Sprawdź standardowy analizator, aby zobaczyć, jak terminy są tokenizowane domyślnie.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Oceń odpowiedź, aby zobaczyć, jak tekst jest tokenizowana w indeksie. Zwróć uwagę, jak każdy termin jest małe litery i podzielone.

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
1. Zmodyfikuj `whitespace` `keyword` żądanie, aby użyć lub analizatora:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Teraz odpowiedź składa się z pojedynczego tokenu, z wielką literą, z myślnikami zachowanymi jako część ciągu. Jeśli chcesz wyszukać wzorzec lub termin częściowy, aparat zapytań ma teraz podstawę do znalezienia dopasowania.


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
> Należy pamiętać, że analizatory zapytań często małe litery w wyrażeniu wyszukiwania podczas tworzenia drzewa kwerendy. Jeśli używasz analizatora, który nie wprowadza małe litery tekstu i nie otrzymujesz oczekiwanych wyników, może to być przyczyną. Rozwiązaniem jest dodanie filtru tokenu małe litery, zgodnie z opisem w sekcji "Użyj analizatorów niestandardowych" poniżej.

## <a name="configure-an-analyzer"></a>Konfigurowanie analizatora
 
Niezależnie od tego, czy oceniasz analizatory, czy przechodzisz do przodu z określoną konfiguracją, musisz określić analizator w definicji pola i ewentualnie skonfigurować sam analizator, jeśli nie używasz wbudowanego analizatora. Podczas wymiany analizatorów, zazwyczaj należy odbudować indeks (upuść, ponownie utworzyć i przeładować). 

### <a name="use-built-in-analyzers"></a>Używanie wbudowanych analizatorów

Wbudowane lub wstępnie zdefiniowane analizatory mogą być `analyzer` określone przez nazwę na właściwości definicji pola, bez dodatkowej konfiguracji wymagane w indeksie. W poniższym przykładzie pokazano, `whitespace` jak można ustawić analizatora w polu. Aby uzyskać więcej informacji na temat dostępnych wbudowanych analizatorów, zobacz [wstępnie zdefiniowane analizatory listy](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

### <a name="use-custom-analyzers"></a>Korzystanie z analizatorów niestandardowych

Jeśli używasz [analizatora niestandardowego,](index-add-custom-analyzers.md)zdefiniuj go w indeksie za pomocą zdefiniowanej przez użytkownika kombinacji tokenizatora, filtru tokenów z możliwymi ustawieniami konfiguracji. Następnie odwołaj się do niego w definicji pola, tak jak wbudowany analizator.

Gdy celem jest tokenizacji całego okresu, analizator niestandardowy, który składa się z **tokenizatora słów kluczowych** i **filtr tokenu małe litery** jest zalecane.

+ Tokenizator słów kluczowych tworzy pojedynczy token dla całej zawartości pola.
+ Filtr tokenu małych liter przekształca wielkie litery w tekst małych liter. Analizatory zapytań zazwyczaj małe litery wszelkie wielkie dane wejściowe tekstu. Dolna obudowa homogenizuje dane wejściowe za pomocą terminów tokenizowanych.

Poniższy przykład ilustruje analizatora niestandardowego, który udostępnia tokenizator słów kluczowych i filtr tokenu małych liter.

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
],

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
> Tokenizator `keyword_v2` i `lowercase` filtr tokenów są znane systemowi i przy użyciu ich domyślnych konfiguracji, dlatego można odwoływać się do nich według nazwy bez konieczności definiowania ich najpierw.

## <a name="build-and-test"></a>Skompiluj i testuj

Po zdefiniowaniu indeksu z analizatorów i definicje pól, które obsługują scenariusz, załadować dokumenty, które mają reprezentatywne ciągi, dzięki czemu można przetestować kwerendy ciągów częściowych. 

W poprzednich sekcjach wyjaśniono logikę. W tej sekcji kroki za pośrednictwem każdego interfejsu API, który należy wywołać podczas testowania rozwiązania. Jak wcześniej wspomniano, jeśli używasz interaktywnego narzędzia do testowania sieci Web, takiego jak Postman, możesz szybko przejść przez te zadania.

+ [Usuń indeks](https://docs.microsoft.com/rest/api/searchservice/delete-index) usuwa istniejący indeks o tej samej nazwie, dzięki czemu można go odtworzyć.

+ [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) tworzy strukturę indeksu w usłudze wyszukiwania, w tym definicje analizatora i pola ze specyfikacją analizatora.

+ [Załaduj dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importuje dokumenty o takiej samej strukturze jak indeks, a także zawartość z wyszukuj. Po tym kroku indeks jest gotowy do kwerendy lub testowania.

+ [Analizator testów](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) został wprowadzony w [Wybierz analizator](#choose-an-analyzer). Przetestuj niektóre ciągi w indeksie przy użyciu różnych analizatorów, aby zrozumieć, jak terminy są tokenizowane.

+ [W dokumentach wyszukiwania wyjaśniono,](https://docs.microsoft.com/rest/api/searchservice/search-documents) jak skonstruować żądanie kwerendy przy użyciu [prostej składni](query-simple-syntax.md) lub [pełnej składni lucene](query-lucene-syntax.md) dla symboli wieloznacznych i wyrażeń regularnych.

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="tune-query-performance"></a>Dostosowywanie wydajności zapytań

Jeśli zaimplementujesz zalecaną konfigurację, która obejmuje tokenizator keyword_v2 i filtr tokenów małe litery, można zauważyć spadek wydajności kwerendy ze względu na dodatkowe przetwarzanie filtru tokenów za pomocą istniejących tokenów w indeksie. 

W poniższym przykładzie dodaje [EdgeNGramTokenFilter,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) aby prefiks pasuje szybciej. Dodatkowe tokeny są generowane w kombinacjach znaków 2-25, które zawierają znaki: (nie tylko MS, MSF, MSFT, MSFT/, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Jak można sobie wyobrazić, dodatkowe tokenizacji wyniki w większym indeksie.

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
],

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

Analizatory są wywoływane podczas indeksowania i podczas wykonywania kwerendy. Często używa się tego samego analizatora dla obu, ale można skonfigurować analizatory niestandardowe dla każdego obciążenia. Zastąpienia analizatora są określone w [definicji indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index) `analyzers` w sekcji, a następnie odwołuje się do określonych pól. 

Gdy analiza niestandardowa jest wymagana tylko podczas indeksowania, można zastosować analizatora niestandardowego tylko indeksowania i nadal używać standardowego analizatora Lucene (lub innego analizatora) dla zapytań.

Aby określić analizę specyficzną dla roli, można ustawić `indexAnalyzer` `searchAnalyzer` właściwości w polu `analyzer` dla każdego z nich, ustawienie i zamiast właściwości domyślnej.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak analizatory przyczyniają się do problemów z zapytaniami i rozwiązywania problemów z zapytaniami. W następnym kroku przyjrzyj się bliżej wpływowi analizatora na indeksowanie i przetwarzanie zapytań. W szczególności należy rozważyć użycie interfejsu API analizy tekstu do zwrócenia tokenizowanego danych wyjściowych, dzięki czemu można zobaczyć dokładnie, co analizator tworzy dla indeksu.

+ [Analizatory języków](search-language-support.md)
+ [Analizatory przetwarzania tekstu w usłudze Azure Cognitive Search](search-analyzers.md)
+ [Analizowanie interfejsu API tekstu (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak działa wyszukiwanie pełnotekstowe (architektura zapytań)](search-lucene-query-architecture.md)