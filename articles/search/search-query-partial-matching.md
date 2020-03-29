---
title: Dopasowywki i znaki specjalne
titleSuffix: Azure Cognitive Search
description: Użyj wieloznacznych i zapytań prefiksów, aby dopasować na całe lub częściowe terminy w żądaniu zapytania usługi Azure Cognitive Search. Trudne do dopasowania wzorce, które zawierają znaki specjalne można rozpoznać za pomocą pełnej składni kwerendy i analizatorów niestandardowych.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289315"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Dopasowywuj się do wzorów i znaków specjalnych (kresek)

W przypadku kwerend zawierających`-, *, (, ), /, \, =`znaki specjalne ( ) lub wzorców zapytań opartych na terminach częściowych w większym terminie, zazwyczaj potrzebne są dodatkowe kroki konfiguracji, aby upewnić się, że indeks zawiera oczekiwaną zawartość w odpowiednim formacie. 

Domyślnie numer telefonu `+1 (425) 703-6214` jest tokenizowany `"425"` `"703"`jako `"6214"` `"1"`, , . Jak można sobie wyobrazić, wyszukiwanie na `"3-62"`, częściowe terminy, które zawierają myślnik, zakończy się niepowodzeniem, ponieważ ta zawartość w rzeczywistości nie istnieje w indeksie. 

Jeśli trzeba wyszukiwać na ciągach częściowych lub znakach specjalnych, można zastąpić domyślny analizator za pomocą niestandardowego analizatora, który działa w ramach prostszych reguł tokenizacji, zachowując całe terminy, niezbędne, gdy ciągi zapytania zawierają części terminu lub specjalne Znaków. Robiąc krok wstecz, podejście wygląda następująco:

+ Wybierz wstępnie zdefiniowany analizator lub zdefiniuj analizator niestandardowy, który wytwarza żądane dane wyjściowe
+ Przypisywanie analizatora do pola
+ Tworzenie indeksu i testowanie

W tym artykule otrzymasz od tych zadań. Podejście opisane w tym miejscu jest przydatne w innych scenariuszach: wieloznaczne i zapytania wyrażenia regularnego również potrzebują całych terminów jako podstawy do dopasowywania wzorców. 

> [!TIP]
> Ocena analyers jest iteracyjnych proces, który wymaga częste przebudowy indeksu. Ten krok można ułatwić, używając funkcji Postman, interfejsów API REST dla [tworzenia indeksu,](https://docs.microsoft.com/rest/api/searchservice/create-index) [indeksu](https://docs.microsoft.com/rest/api/searchservice/delete-index)[usuwania, ładowania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)i [wyszukiwania dokumentów.](https://docs.microsoft.com/rest/api/searchservice/search-documents) W przypadku ładowania dokumentów treść żądania powinna zawierać mały reprezentatywny zestaw danych, który chcesz przetestować (na przykład pole z numerami telefonów lub kodami produktów). Za pomocą tych interfejsów API w tej samej kolekcji postman, można szybko przełączać się między tymi krokami.

## <a name="choosing-an-analyzer"></a>Wybieranie analizatora

Wybierając analizator, który produkuje tokeny całego okresu, następujące analizatory są typowe wybory:

| Analizator | Zachowania |
|----------|-----------|
| [Słowa kluczowego](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Zawartość całego pola jest tokenizowana jako pojedynczy termin. |
| [Odstępu](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Oddziela tylko na białych przestrzeniach. Terminy, które zawierają kreski lub inne znaki są traktowane jako pojedynczy token. |
| [analizator niestandardowy](index-add-custom-analyzers.md) | (zalecane) Tworzenie analizatora niestandardowego umożliwia określenie zarówno tokenizatora i filtru tokenów. Poprzednie analizatory muszą być używane w stanie— jest. Analizator niestandardowy umożliwia wybranie tokenizatorów i filtrów tokenów do użycia. <br><br>Zalecaną kombinacją jest [tokenizator słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) z [filtrem tokenów o mniejszej litery.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html) Sam [analizator wstępnie zdefiniowanych słów kluczowych](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) nie wykonuje wielkich liter bez żadnych wielkich liter, co może spowodować niepowodzenie kwerend. Analizator niestandardowy zapewnia mechanizm dodawania filtru tokenu dolnoślądkowych. |

Jeśli używasz narzędzia do testowania interfejsu API sieci web, takiego jak Postman, możesz dodać [wywołanie REST analizatora testów,](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) aby sprawdzić tokenizowane dane wyjściowe. Biorąc pod uwagę istniejący indeks i pole zawierające myślniki lub terminy częściowe, można wypróbować różne analizatory w określonych terminach, aby zobaczyć, jakie tokeny są emitowane.  

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
> Należy pamiętać, że analizatory zapytań często małe litery w wyrażeniu wyszukiwania podczas tworzenia drzewa kwerendy. Jeśli używasz analizatora, który nie wprowadza małe litery tekstu i nie otrzymujesz oczekiwanych wyników, może to być przyczyną. Rozwiązaniem jest dodanie filtru tokenu lwower-case.

## <a name="analyzer-definitions"></a>Definicje analizatora
 
Niezależnie od tego, czy oceniasz analizatory, czy przechodzisz do przodu z określoną konfiguracją, musisz określić analizator w definicji pola i ewentualnie skonfigurować sam analizator, jeśli nie używasz wbudowanego analizatora. Podczas wymiany analizatorów, zazwyczaj należy odbudować indeks (upuść, ponownie utworzyć i przeładować). 

### <a name="use-built-in-analyzers"></a>Używanie wbudowanych analizatorów

Wbudowane lub wstępnie zdefiniowane analizatory mogą być `analyzer` określone przez nazwę na właściwości definicji pola, bez dodatkowej konfiguracji wymagane w indeksie. W poniższym przykładzie pokazano, `whitespace` jak można ustawić analizatora w polu.

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
Aby uzyskać więcej informacji na temat wszystkich dostępnych wbudowanych analizatorów, zobacz [wstępnie zdefiniowane analizatory listy](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Korzystanie z analizatorów niestandardowych

Jeśli używasz [analizatora niestandardowego,](index-add-custom-analyzers.md)zdefiniuj go w indeksie za pomocą zdefiniowanej przez użytkownika kombinacji tokenizatora, tokenfiltera z możliwymi ustawieniami konfiguracji. Następnie odwołaj się do niego w definicji pola, tak jak wbudowany analizator.

Gdy celem jest tokenizacji całego okresu, analizator niestandardowy, który składa się z **tokenizatora słów kluczowych** i **filtr tokenu małe litery** jest zalecane.

+ Tokenizator słów kluczowych tworzy pojedynczy token dla całej zawartości pola.
+ Filtr tokenu małych liter przekształca wielkie litery w tekst małych liter. Analizatory zapytań zazwyczaj małe litery wszelkie wielkie dane wejściowe tekstu. Małe przysłów homogenizuje dane wejściowe z tokenizowanych terminów.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Duplikuj pola dla różnych scenariuszy

Inna opcja wykorzystuje przypisanie analizatora na pole do optymalizacji pod kątem różnych scenariuszy. W szczególności można zdefiniować "featureCode" i "featureCodeRegex", aby obsługiwać regularne wyszukiwanie pełnotekstowego w pierwszym i zaawansowane dopasowanie wzorca w drugim.

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

W tym artykule wyjaśniono, jak analizatory przyczyniają się do problemów z zapytaniami i rozwiązywania problemów z zapytaniami. W następnym kroku przyjrzyj się bliżej wpływowi analizatora na indeksowanie i przetwarzanie zapytań. W szczególności należy rozważyć użycie interfejsu API analizy tekstu do zwrócenia tokenizowanego danych wyjściowych, dzięki czemu można zobaczyć dokładnie, co analizator tworzy dla indeksu.

+ [Analizatory języków](search-language-support.md)
+ [Analizatory przetwarzania tekstu w usłudze Azure Cognitive Search](search-analyzers.md)
+ [Analizowanie interfejsu API tekstu (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Jak działa wyszukiwanie pełnotekstowe (architektura zapytań)](search-lucene-query-architecture.md)
