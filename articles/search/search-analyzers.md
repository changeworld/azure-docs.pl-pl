---
title: Analizatory do przetwarzania języka i tekstu
titleSuffix: Azure Cognitive Search
description: Przypisz analizatory do przeszukiwalnych pól tekstowych w indeksie, aby zastąpić domyślny standardowy Lucene niestandardowymi, wstępnie zdefiniowanymi lub specyficznymi dla języka alternatywami.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460718"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analizatory przetwarzania tekstu w usłudze Azure Cognitive Search

*Analizator* jest składnikiem [wyszukiwarki pełnotekstowej](search-lucene-query-architecture.md) odpowiedzialnej za przetwarzanie tekstu w ciągach zapytań i indeksowanych dokumentach. Różne analizatory manipulować tekstem na różne sposoby w zależności od scenariusza. Analizatory języka przetwarzają tekst przy użyciu reguł językowych w celu poprawy jakości wyszukiwania, podczas gdy inne analizatory wykonują bardziej podstawowe zadania, takie jak konwertowanie znaków na małe litery, na przykład. 

Analizatory języka są najczęściej używane i istnieje domyślny analizator języka przypisany do każdego pola z możliwością wyszukiwania w indeksie usługi Azure Cognitive Search. Następujące przekształcenia języka są typowe podczas analizy tekstu:

+ Inne niż istotne słowa (stopwords) i znaki interpunkcyjne są usuwane.
+ Frazy i wyrazy dzielone są podzielone na części składowe.
+ Wielkie litery są małe litery.
+ Słowa są zredukowane do form korzeniowych, dzięki czemu można znaleźć dopasowanie niezależnie od czasu.

Analizatory języka konwertują wprowadzanie tekstu na pierwotne lub główne formularze, które są wydajne do przechowywania i pobierania informacji. Konwersja występuje podczas indeksowania, gdy indeks jest zbudowany, a następnie ponownie podczas wyszukiwania, gdy indeks jest odczytywany. Jest bardziej prawdopodobne, aby uzyskać wyniki wyszukiwania można oczekiwać, jeśli używasz tego samego analizatora dla obu operacji.

## <a name="default-analyzer"></a>Analizator domyślny  

Usługa Azure Cognitive Search używa [apache Lucene Standardowy analizator (standardowy lucene) jako domyślny,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) który dzieli tekst na elementy zgodnie z [regułami "Segmentacja tekstu Unicode".](https://unicode.org/reports/tr29/) Ponadto analizator standardowy konwertuje wszystkie znaki do ich postaci dolnej litery. Zarówno indeksowane dokumenty, jak i wyszukiwane terminy przechodzą przez analizę podczas indeksowania i przetwarzania zapytań.  

Jest on używany automatycznie w każdym polu z możliwością wyszukiwania. Wartość domyślną można zastąpić w zależności od pola. Alternatywne analizatory mogą być [analizatorem języka,](index-add-language-analyzers.md) [analizatorem niestandardowym](index-add-custom-analyzers.md)lub wstępnie zdefiniowanym analizatorem z [listy dostępnych analizatorów.](index-add-custom-analyzers.md#AnalyzerTable)


## <a name="types-of-analyzers"></a>Rodzaje analizatorów

Na poniższej liście opisano, które analizatory są dostępne w usłudze Azure Cognitive Search.

| Kategoria | Opis |
|----------|-------------|
| [Standardowy analizator luceny](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Specyfikacja ani konfiguracja nie jest wymagana. Ten analizator ogólnego przeznaczenia działa dobrze dla większości języków i scenariuszy.|
| Wstępnie zdefiniowane analizatory | Oferowany jako produkt gotowy przeznaczony do stosowania w stanie gotowym. <br/>Istnieją dwa typy: specjalistyczne i językowe. Co sprawia, że są "wstępnie zdefiniowane" jest to, że odwołujesz się do nich według nazwy, bez konfiguracji lub dostosowywania. <br/><br/>[Wyspecjalizowane (niezależny od języka) analizatory](index-add-custom-analyzers.md#AnalyzerTable) są używane, gdy wprowadzanie tekstu wymaga specjalistycznego przetwarzania lub minimalnego przetwarzania. Nieprojowe wstępnie zdefiniowane analizatory obejmują **asciifolding**, **słowo kluczowe**, **wzór**, **prosty**, **stop**, **odstępy**.<br/><br/>[Analizatory języka](index-add-language-analyzers.md) są używane, gdy potrzebujesz bogatej obsługi językowej dla poszczególnych języków. Usługa Azure Cognitive Search obsługuje 35 analizatorów języka Lucene i 50 analizatorów przetwarzania języka naturalnego firmy Microsoft. |
|[Analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odnosi się do zdefiniowanej przez użytkownika konfiguracji kombinacji istniejących elementów, składającej się z jednego tokenizatora (wymagane) i opcjonalnych filtrów (char lub token).|

Kilka wstępnie zdefiniowanych analizatorów, takich jak **Pattern** lub **Stop,** obsługuje ograniczony zestaw opcji konfiguracji. Aby ustawić te opcje, można skutecznie utworzyć analizator niestandardowy, składający się ze wstępnie zdefiniowanego analizatora i jednej z alternatywnych opcji udokumentowanych w [predefiniowanym odwołaniu do analizatora](index-add-custom-analyzers.md#AnalyzerTable). Podobnie jak w przypadku każdej konfiguracji niestandardowej, podaj nową konfigurację z nazwą, taką jak *myPatternAnalyzer,* aby odróżnić ją od analizatora wzorca lucene.

## <a name="how-to-specify-analyzers"></a>Jak określić analizatory

1. (tylko dla analizatorów niestandardowych) Utwórz sekcję **analizatora** o nazwie w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Tworzenie indeksu,](https://docs.microsoft.com/rest/api/searchservice/create-index) a także [Dodawanie analizatorów niestandardowych](index-add-custom-analyzers.md).

2. W [definicji pola](https://docs.microsoft.com/rest/api/searchservice/create-index) w indeksie ustaw właściwość **analizatora** pola na nazwę analizatora docelowego (na przykład `"analyzer" = "keyword"`. Prawidłowe wartości obejmują nazwę wstępnie zdefiniowanego analizatora, analizatora języka lub analizatora niestandardowego zdefiniowanego również w schemacie indeksu. Planowanie przypisywania analizatora w fazie definicji indeksu przed utworzeniem indeksu w usłudze.

3. Opcjonalnie zamiast jednej właściwości **analizatora** można ustawić różne analizatory do indeksowania i wykonywania zapytań przy użyciu **indexAnalyzer** i **searchAnalyzer** parametrów pola. Należy użyć różnych analizatorów do przygotowywania i pobierania danych, jeśli jedno z tych działań wymaga określonej transformacji nie jest potrzebne przez inne.

> [!NOTE]
> Nie jest możliwe użycie [innego analizatora języka](index-add-language-analyzers.md) w czasie indeksowania niż w czasie kwerendy dla pola. Ta możliwość jest zarezerwowana dla [analizatorów niestandardowych](index-add-custom-analyzers.md). Z tego powodu, jeśli spróbujesz ustawić **searchAnalyzer** lub **indexAnalyzer** właściwości do nazwy analizatora języka, interfejs API REST zwróci odpowiedź błędu. Zamiast tego należy użyć właściwości **analizatora.**

Przypisywanie **analizatora** lub **indexAnalyzer** do pola, które zostało już fizycznie utworzone, jest niedozwolone. Jeśli którykolwiek z tych jest niejasne, przejrzyj poniższą tabelę, który podział, które akcje wymagają odbudować i dlaczego.
 
 | Scenariusz | Wpływ | Kroki |
 |----------|--------|-------|
 | Dodawanie nowego pola | Minimalne | Jeśli pole jeszcze nie istnieje w schemacie, nie ma żadnej rewizji pola, ponieważ pole nie ma jeszcze fizycznej obecności w indeksie. Można użyć [Update Index,](https://docs.microsoft.com/rest/api/searchservice/update-index) aby dodać nowe pole do istniejącego indeksu i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) go wypełnić.|
 | Dodaj **analizatora** lub **indexAnalyzer** do istniejącego pola indeksowanego. | [Odbudować](search-howto-reindex.md) | Odwrócony indeks dla tego pola musi zostać odtworzony od podstaw, a zawartość tych pól musi zostać ponownie zindeksowana. <br/> <br/>W przypadku indeksów w obszarze aktywnego rozwoju [usuń](https://docs.microsoft.com/rest/api/searchservice/delete-index) i [utwórz](https://docs.microsoft.com/rest/api/searchservice/create-index) indeks, aby podnieść nową definicję pola. <br/> <br/>W przypadku indeksów w produkcji można odroczyć przebudowę, tworząc nowe pole, aby zapewnić poprawioną definicję i rozpocząć używanie jej zamiast starej. Użyj [update index,](https://docs.microsoft.com/rest/api/searchservice/update-index) aby włączyć nowe pole i [mergeOrUpload,](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) aby go wypełnić. Później, w ramach planowanej obsługi indeksu, można wyczyścić indeks, aby usunąć przestarzałe pola. |

## <a name="when-to-add-analyzers"></a>Kiedy dodać analizatory

Najlepszy czas, aby dodać i przypisać analizatory jest podczas aktywnego rozwoju, gdy upuszczanie i odtworzenie indeksów jest rutynowe.

Jako definicja indeksu ugruntowuje, można dołączyć nowe konstrukcje analizy do indeksu, ale trzeba będzie przekazać **allowIndexDowntime** flagi [aktualizacji indeksu,](https://docs.microsoft.com/rest/api/searchservice/update-index) jeśli chcesz uniknąć tego błędu:

*"Aktualizacja indeksu nie jest dozwolona, ponieważ spowodowałaby przestoje. Aby dodać nowe analizatory, tokenizatory, filtry tokenów lub filtry znaków do istniejącego indeksu, ustaw parametr zapytania "allowIndexDowntime" na "true" w żądaniu aktualizacji indeksu. Należy zauważyć, że ta operacja spowoduje, że indeks zostanie przełączyć indeks w tryb offline na co najmniej kilka sekund, powodując indeksowanie i żądania kwerendy zakończyć się niepowodzeniem. Wydajność i dostępność zapisu indeksu może być ograniczona przez kilka minut po zaktualizowaniu indeksu lub dłużej dla bardzo dużych indeksów."*

To samo dotyczy podczas przypisywania analizatora do pola. Analizator jest integralną częścią definicji pola, dzięki czemu można go dodać tylko wtedy, gdy pole jest tworzone. Jeśli chcesz dodać analizatory do istniejących pól, musisz [upuścić i odbudować](search-howto-reindex.md) indeks lub dodać nowe pole z analizatorem, który chcesz.

Jak wspomniano, wyjątkiem jest **searchAnalyzer** wariant. Z trzech sposobów określania analizatorów (**analizator**, **indexAnalyzer**, **searchAnalyzer**), tylko **atrybut searchAnalyzer** można zmienić w istniejącym polu.

## <a name="recommendations-for-working-with-analyzers"></a>Zalecenia dotyczące pracy z analizatorami

W tej sekcji znajduje się porady dotyczące pracy z analizatorami.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analizator odczytu i zapisu, chyba że masz określone wymagania

Usługa Azure Cognitive Search umożliwia określenie różnych analizatorów do indeksowania i wyszukiwania za pomocą dodatkowych parametrów pola **indexAnalyzer** i **searchAnalyzer.** Jeśli nie określono, zestaw analizatora z **właściwością analizatora** jest używany zarówno do indeksowania i wyszukiwania. Jeśli `analyzer` jest nieokreślony, używany jest domyślny analizator standardowej luceny.

Ogólną regułą jest użycie tego samego analizatora do indeksowania i wykonywania zapytań, chyba że określone wymagania postanowią inaczej. Pamiętaj, aby dokładnie przetestować. Gdy przetwarzanie tekstu różni się w czasie wyszukiwania i indeksowania, istnieje ryzyko niezgodności między terminami kwerendy i terminami indeksowanymi, gdy konfiguracje analizatora wyszukiwania i indeksowania nie są wyrównane.

### <a name="test-during-active-development"></a>Test podczas aktywnego rozwoju

Zastępowanie analizatora standardowego wymaga przebudowy indeksu. Jeśli to możliwe, zdecydować, które analizatory do użycia podczas aktywnego rozwoju, przed stopniem wysuwu indeksu do produkcji.

### <a name="inspect-tokenized-terms"></a>Sprawdzanie terminów tokenizowanych

Jeśli wyszukiwanie nie zwróci oczekiwanych wyników, najbardziej prawdopodobnym scenariuszem są rozbieżności tokenów między wejściami terminowymi w kwerendzie i tokenizowane terminy w indeksie. Jeśli tokeny nie są takie same, dopasowania nie zmaterializują się. Aby sprawdzić dane wyjściowe tokenizatora, zaleca się przy użyciu [analizy interfejsu API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako narzędzie badania. Odpowiedź składa się z tokenów, generowanych przez określony analizator.

<a name="examples"></a>

## <a name="rest-examples"></a>PRZYKŁADY REST

Poniższe przykłady pokazują definicje analizatora dla kilku kluczowych scenariuszy.

+ [Przykład analizatora niestandardowego](#Custom-analyzer-example)
+ [Przypisywanie analizatorów do przykładu pola](#Per-field-analyzer-assignment-example)
+ [Analizatory mieszania do indeksowania i wyszukiwania](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Przykład analizatora języka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Przykład analizatora niestandardowego

W tym przykładzie przedstawiono definicję analizatora z opcjami niestandardowymi. Niestandardowe opcje dla filtrów char, tokenizatorów i filtrów tokenów są określone oddzielnie jako nazwane konstrukcje, a następnie odwołuje się do definicji analizatora. Wstępnie zdefiniowane elementy są używane jako— jest i po prostu odwołuje się przez nazwę.

Przechodząc przez ten przykład:

* Analizatory są właściwością klasy pola dla pola z wyszukujalnym.
* Analizator niestandardowy jest częścią definicji indeksu. Może być lekko dostosowany (na przykład dostosowanie jednej opcji w jednym filtrze) lub dostosowany w wielu miejscach.
* W takim przypadku analizator niestandardowy jest "my_analyzer", który z kolei używa niestandardowego standardowego tokenizatora "my_standard_tokenizer" i dwóch filtrów tokenów: małych liter i dostosowanego filtru asciifolding "my_asciifolding".
* Definiuje również 2 niestandardowe filtry char "map_dash" i "remove_whitespace". Pierwsza z nich zastępuje wszystkie kreski podkreśleniami, a druga usuwa wszystkie spacje. Spacje muszą być UTF-8 zakodowane w regułach mapowania. Filtry char są stosowane przed tokenizacji i wpłynie na tokeny wynikowe (standardowy tokenizator przerwy na myślnik i spacji, ale nie na podkreślenia).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Przykład przydziału analizatora pól

Standardowy analizator jest wartością domyślną. Załóżmy, że chcesz zastąpić wartość domyślną innym wstępnie zdefiniowanym analizatorem, takim jak analizator wzorców. Jeśli nie ustawiasz opcji niestandardowych, wystarczy określić je według nazwy w definicji pola.

Element "analizator" zastępuje analizator standardowy w zależności od pola. Nie ma globalnego zastąpienia. W tym `text1` przykładzie używa analizatora wzorców i `text2`, który nie określa analizatora, używa wartości domyślnej.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mieszanie analizatorów do indeksowania i wyszukiwania operacji

Interfejsy API zawierają dodatkowe atrybuty indeksu do określania różnych analizatorów do indeksowania i wyszukiwania. **SearchAnalyzer** i **indexAnalyzer** atrybuty muszą być określone jako para, zastępując atrybut **analizatora** pojedynczego.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Przykład analizatora języka

Pola zawierające ciągi w różnych językach można użyć analizatora języka, podczas gdy inne pola zachowują wartość domyślną (lub użyć innego wstępnie zdefiniowanego lub niestandardowego analizatora). Jeśli używasz analizatora języka, musi być używany zarówno do indeksowania i wyszukiwania operacji. Pola, które używają analizatora języka nie może mieć różne analizatory do indeksowania i wyszukiwania.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>Przykłady języka C#

Jeśli używasz przykładów kodu .NET SDK, można dołączyć te przykłady do użycia lub skonfigurować analizatory.

+ [Przypisywanie wbudowanego analizatora](#Assign-a-language-analyzer)
+ [Konfigurowanie analizatora](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Przypisywanie analizatora języka

Każdy analizator, który jest używany jako — jest, bez konfiguracji, jest określony w definicji pola. Nie ma wymogu tworzenia konstrukcji analizatora. 

W tym przykładzie przypisuje microsoft angielski i francuski analizatory do pól opisu. Jest to fragment pobrany z większej definicji indeksu hoteli, tworząc przy użyciu klasy Hotel w pliku hotels.cs próbki [DotNetHowTo.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

[Analizator połączeń](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), określając typ [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zapewniając analizator tekstu obsługiwany w usłudze Azure Cognitive Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definiowanie analizatora niestandardowego

Gdy wymagane jest dostosowanie lub konfiguracja, należy dodać konstruktora do indeksu. Po zdefiniowaniu można dodać definicję pola, jak pokazano w poprzednim przykładzie.

Utwórz obiekt [CustomAnalyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) Aby uzyskać więcej przykładów, zobacz [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Następne kroki

+ Zapoznaj się z naszym kompleksowym [wyjaśnieniem, jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search.](search-lucene-query-architecture.md) W tym artykule użyto przykładów do wyjaśnienia zachowań, które mogą wydawać się sprzeczne z intuicją na powierzchni.

+ Wypróbuj dodatkową składnię kwerendy w sekcji [Przykładowe dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) lub w [sekcji Proste zapytanie](query-simple-syntax.md) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak stosować [analizatory leksykalne specyficzne dla języka](index-add-language-analyzers.md).

+ [Skonfiguruj analizatory niestandardowe](index-add-custom-analyzers.md) do minimalnego przetwarzania lub specjalistycznego przetwarzania w poszczególnych polach.

## <a name="see-also"></a>Zobacz też

 [Interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](query-simple-syntax.md) 

 [Pełna składnia zapytań Lucene](query-lucene-syntax.md) 
 
 [Obsługa wyników wyszukiwania](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
