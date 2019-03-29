---
title: Analizatory do równoległego językowej i przetwarzanie tekstu — usługi Azure Search
description: Analizatory przypisywanie do pól tekstowych można wyszukiwać w indeksie, aby zastąpić domyślne standardowa Lucene z alternatywami niestandardowych, wstępnie zdefiniowanych lub specyficzne dla języka.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 3e6f0a2b9b935df9b12cf9146ebf05f1b1c84855
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578768"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analizatory do przetwarzania w usłudze Azure Search tekstu

*Analizatora* jest składnikiem [aparat wyszukiwania pełnotekstowego](search-lucene-query-architecture.md) odpowiedzialna za przetwarzanie tekstu w ciągów zapytania i indeksowane dokumenty. Analizatory różnych manipulować tekstem w różny sposób w zależności od scenariusza. Analizatory języków przetwarzanie tekstu przy użyciu językowej reguły w celu poprawy jakości wyszukiwania, podczas gdy inne analizatory wykonywania bardziej podstawowe zadania, takie jak na przykład konwertowania znaków na małe litery. 

Analizatory języków są najczęściej używane i Brak domyślnego analizatora języków przypisany do każdego pola można wyszukiwać w indeksie usługi Azure Search. Następujące przekształcenia języka są typowe podczas analizy tekstu:

+ Wyrazy inne niż niezbędne (STOP-słowa) i znaki interpunkcyjne są usuwane.
+ Wyrażenia i wyrazów są podzielone na części zamienne.
+ Wielkie litery słowa są małe — z uwzględnieniem wielkości liter.
+ Słowa zostały zredukowane do głównego formularzy, dzięki czemu można znaleźć dopasowania niezależnie od tego czasu teraźniejszego.

Konwertuj analizatory języka wprowadzanie tekstu do podstawowego lub główny formularzy, które są wydajne informacji przechowywania i pobierania. Konwersja występuje podczas indeksowania, gdy indeks został utworzony, a następnie ponownie podczas wyszukiwania, gdy indeks jest do odczytu. Jesteś bardziej prawdopodobne uzyskać wyniki wyszukiwania, oczekujesz, że jeśli używasz tego samego analizatora dla obu operacji.

## <a name="default-analyzer"></a>Analizator domyślne  

Używa usługi Azure Search [analizatora Apache Lucene Standard (standardowe lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako wartość domyślna, która dzieli tekst na następujące elementy ["Segmentacji tekst Unicode"](https://unicode.org/reports/tr29/) reguły. Ponadto standardowy analizator konwertuje wszystkie znaki do postaci małymi literami. Indeksowane dokumenty i terminy wyszukiwania przechodzą przez analizę, podczas indeksowania i przetwarzanie zapytań.  

Jest automatycznie używany w każdym polu możliwym do przeszukania. Można zastąpić domyślne na podstawie pól pola. Alternatywne analizatory mogą być [analizatora języków](index-add-language-analyzers.md), [analizatora niestandardowego](index-add-custom-analyzers.md), lub wstępnie zdefiniowanych analyzer z [listę dostępnych analizatory](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typy analizatorów

Na poniższej liście opisano, w których analizatory są dostępne w usłudze Azure Search.

| Kategoria | Opis |
|----------|-------------|
| [Standardowy analizator Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Nie specyfikacji lub konfiguracja jest wymagana. Ta ogólnego przeznaczenia analizatora wykonuje również większość języków i scenariuszy.|
| Wstępnie zdefiniowane analizatorów | Oferowana jako gotowego produktu jest przeznaczony do użycia jako-to. <br/>Istnieją dwa typy: wyspecjalizowanych i język. Co sprawia, że ich "wstępnie zdefiniowane" to odwołać je według nazwy bez konfiguracji i dostosowania. <br/><br/>[Wyspecjalizowane analizatory (niezależny od języka)](index-add-custom-analyzers.md#AnalyzerTable) są używane, gdy tekst w danych wejściowych wymaga wyspecjalizowane przetwarzania lub przetwarzanie minimalny. Wstępnie zdefiniowane analizatory języka nie obejmują **Asciifolding**, **— słowo kluczowe**, **wzorzec**, **proste**, **zatrzymać**, **Odstępu**.<br/><br/>[Analizatory języków](index-add-language-analyzers.md) są używane, gdy potrzebujesz zaawansowanych językowej pomocy technicznej dla poszczególnych języków. Usługa Azure Search obsługuje 35 analizatory języka Lucene i 50 analizatory przetwarzania języka naturalnego firmy Microsoft. |
|[Analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odnosi się do zdefiniowanych przez użytkownika konfiguracji kombinacja elementów istniejącego, składające się z jednego tokenizatora (wymagane) i opcjonalne filtry (char lub tokenu).|

Kilka predefiniowanymi analizatorów, takimi jak **wzorzec** lub **zatrzymać**, obsługuje ograniczony zestaw opcji konfiguracji. Aby ustawić te opcje, efektywnie tworzysz analizatora niestandardowego, składający się z wstępnie zdefiniowanych analizator i jedną z alternatywnych opcji udokumentowane w artykule [wstępnie zdefiniowanych odwołania do analizatora](index-add-custom-analyzers.md#AnalyzerTable). Za pomocą dowolnej konfiguracji niestandardowej dostarczać nowej konfiguracji o nazwie, takich jak *myPatternAnalyzer* odróżniający go od analizator Lucene wzorca.

## <a name="how-to-specify-analyzers"></a>Jak określić analizatorów

1. (dla tylko analizatory niestandardowe) Utwórz nazwane **analizatora** sekcji w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) , a także [dodać analizatory niestandardowe](index-add-custom-analyzers.md).

2. Na [definicji pola](https://docs.microsoft.com/rest/api/searchservice/create-index) w indeksie, ustaw w polu **analizatora** właściwość na nazwę analizatora docelowej (na przykład `"analyzer" = "keyword"`. Prawidłowe wartości to nazwa wstępnie zdefiniowanych analizatora, analizatora języków lub analizatora niestandardowego, również zdefiniowanej w schemacie indeksu. Planowanie dotyczące przypisywania analizator w fazie definicji indeksu, przed utworzeniem indeksu w usłudze.

3. Opcjonalnie, zamiast jednego **analizatora** właściwości można ustawić różne analizatory do indeksowania i zapytań za pomocą **indexAnalyzer** i **searchAnalyzer** pola Parametry. Należy użyć różnych analizatory do przygotowania danych i pobierania w razie potrzeby jedną z tych działań odpowiednie przekształcenie nie jest wymagany przez drugi.

Przypisywanie **analizatora** lub **indexAnalyzer** do pola, które fizycznie utworzono już nie jest dozwolone. Jeśli któryś z tym jest jasne, przejrzyj poniższą tabelę, do podziału, w których akcje wymagają odbudowania indeksu i dlaczego.
 
 | Scenariusz | Wpływ | Kroki |
 |----------|--------|-------|
 | Dodawanie nowego pola | Minimalny | Jeśli pole nie istnieje jeszcze w schemacie, nie istnieje żadna zmiana pola, aby upewnić się, ponieważ pole nie ma jeszcze fizycznej obecności w indeksie. Możesz użyć [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) można dodać nowe pole do istniejącego indeksu, a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) wypełnić.|
 | Dodaj **analizatora** lub **indexAnalyzer** do istniejącego pola indeksowane. | [rebuild](search-howto-reindex.md) | Odwrócony indeksu dla tego pola muszą zostać ponownie utworzone od podstaw, a zawartość tych pól muszą być ponownie indeksowane. <br/> <br/>Dla indeksów w opracowaniu active [Usuń](https://docs.microsoft.com/rest/api/searchservice/delete-index) i [tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) indeksu do pobrania nowej definicji pola. <br/> <br/>W przypadku indeksów w środowisku produkcyjnym może odroczyć odbudowania indeksu, tworząc nowe pole, aby podać definicję poprawione i rozpocząć korzystanie z niej zamiast starego. Użyj [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) włączenie nowego pola i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) wypełnić. Później, w ramach indeksu planowana obsługi można wyczyścić indeksów, aby usunąć przestarzałe pola. |

## <a name="when-to-add-analyzers"></a>Kiedy należy dodać analizatory

Najlepszy moment na dodawanie i przypisywanie analizatory jest podczas tworzenia aktywne po upuszczeniu i ponowne tworzenie indeksów jest procedury.

Jak ekstrakt definicji indeksu, można dołączyć nowego konstrukcje analizy do indeksu, ale musisz przekazać **allowIndexDowntime** flaga [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) Jeśli chcesz uniknąć tego błędu:

*"Index aktualizacji niedozwolone, ponieważ mogłoby to spowodować Przestój. Aby dodać nowe analizatory, tokenizatory, filtry tokenu lub znak filtry do istniejącego indeksu, należy ustawić parametr zapytania "allowIndexDowntime" na "true" w żądaniu aktualizacji indeksu. Należy pamiętać, że ta operacja umieści indeksu w trybie offline dla co najmniej kilka sekund, co powoduje usługi indeksowania i żądań zapytań nie powiedzie się. Wydajność i zapisu dostępność indeks może być osłabiona przez kilka minut, po zaktualizowaniu indeksu lub dłużej dla bardzo dużych indeksów."*

To samo dotyczy podczas przypisywania Analizator do pola. Analizator jest integralną częścią definicji pola, dzięki czemu możesz tylko dodawać go po utworzeniu pola. Jeśli chcesz dodać analizatory do istniejących pól, musisz [Porzuć i ponownie skompiluj](search-howto-reindex.md) indeksu, lub Dodaj nowe pole za pomocą analizatora ma.

Jak wspomniano, wyjątek jest **searchAnalyzer** wariant. Trzy sposoby, aby określić analizatory (**analizatora**, **indexAnalyzer**, **searchAnalyzer**), tylko **searchAnalyzer** atrybutu można zmienić na istniejącego pola.

## <a name="recommendations-for-working-with-analyzers"></a>Zalecenia dotyczące pracy z analizatorów

Ta sekcja zawiera wskazówki dotyczące sposobu pracy z analizatorów.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analizator dla odczytu / zapisu, jeśli nie masz określonych wymagań

Usługa Azure Search pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą dodatkowych **indexAnalyzer** i **searchAnalyzer** pola parametrów. Jeśli nie zostanie podany, analizator ustawiany **analizatora** właściwość jest używana do indeksowania i wyszukiwania. Jeśli `analyzer` jest nieokreślony, używany analizator Lucene Standardowy domyślny.

Ogólną zasadą jest do użytku z tego samego analizatora indeksowania i wykonywania zapytań, chyba że określone uwarunkowania. Należy dokładnie przetestować. Podczas przetwarzania tekstu różni się w momencie indeksowania i wyszukiwania, możesz uruchomić ryzyko niezgodność między indeksowanych terminy, gdy wyszukiwanie i indeksowanie analizatora konfiguracje nie są wyrównane i zapytania.

### <a name="test-during-active-development"></a>Testowania podczas programowania active

Zastępowanie standardowych analyzer wymaga odbudowywanie indeksu. Jeśli to możliwe decyzję w sprawie analizatorów, który ma być używany podczas aktywnego rozwoju, przed stopniowe indeksu w środowisku produkcyjnym.

### <a name="inspect-tokenized-terms"></a>Sprawdzanie warunków tokenami

Jeśli wyszukiwanie nie zwraca oczekiwanych rezultatów, najbardziej prawdopodobnym scenariuszem jest token rozbieżności między termin wejść na tym zapytaniu i tokenami terminy w indeksie. Jeśli tokeny nie są takie same, do zmaterializowania się nie powieść dopasowań. Aby sprawdzić dane wyjściowe tokenizatora, zaleca się używanie [analizowanie API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako narzędzie do analizy. Odpowiedź składa się tokenów, tak jak w przypadku określonego analizatora.

### <a name="compare-english-analyzers"></a>Porównaj analizatory w języku angielskim

[Wersja demonstracyjna analizatora wyszukiwania](https://alice.unearth.ai/) jest aplikacją firm pokaz przedstawiający porównania side-by-side standardowy analizator Lucene, Lucene w języku angielskim analizator i procesora angielskiej języka naturalnego firmy Microsoft. Indeks został rozwiązany; zawiera ona tekstu z popularnych wątku. Dla każdego dane wejściowe zostaną podane, wyniki z każdego analizatora są wyświetlane w sąsiadujących okienka, co daje poczucie przetwarzaniu tego samego ciągu w każdej analizatora. 

<a name="examples"></a>

## <a name="rest-examples"></a>Przykłady REST

Poniższe przykłady pokazują analizatora definicje kilku kluczowych scenariuszy.

+ [Przykład analizatora niestandardowego](#Custom-analyzer-example)
+ [Przypisz analizatory do przykładu pola](#Per-field-analyzer-assignment-example)
+ [Mieszanie analizatory na indeksowanie i wyszukiwanie](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Przykład analizatora języka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Przykład analizatora niestandardowego

W tym przykładzie przedstawiono definicję analyzer przy użyciu opcji niestandardowych. Niestandardowe opcje filtrów char, tokenizatory i tokenu filtry są określane oddzielnie jako nazwany konstrukcje, a następnie wspomniane w definicji analizatora. Wstępnie zdefiniowane elementy są używane jako — jest i po prostu odwołuje się nazwa.

Instruktaż następująco:

* Analizatory to właściwość klasy polu możliwym do przeszukania.
* Analizatora niestandardowego jest częścią definicji indeksu. Może on lekko dostosować (na przykład dostosowywania pojedynczego opcję w jeden filtr) lub dostosowanych w kilku miejscach.
* W tym przypadku analizatora niestandardowego to "my_analyzer", który z kolei używa dostosowane tokenizatora standardowy "my_standard_tokenizer" i dwa filtry tokenu: małe litery i dostosowane asciifolding filtru "my_asciifolding".
* Definiuje również 2 filtry niestandardowe char "map_dash" i "remove_whitespace". Pierwsza z nich zamienia wszystkie łączniki znaki podkreślenia, podczas gdy drugi usuwa wszystkie spacje. Miejsca do magazynowania muszą być zakodowane w reguły mapowania UTF-8. Filtry char są stosowane przed tokenizacji i będzie mieć wpływ na wynikowy tokenów (standardowa tokenizatora podziały dash i miejsca do magazynowania, ale nie na podkreślenia).

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

### <a name="per-field-analyzer-assignment-example"></a>Przykład przypisania analizatora pola

Standardowy Analizator jest ustawieniem domyślnym. Załóżmy, że chcesz zastąpić domyślny za pomocą różnych wstępnie zdefiniowanych analizatora, takich jak analizator wzorca. Jeśli nie jesteś ustawienie opcji niestandardowych, wystarczy określić je według nazwy w definicji pola.

Element "analizatora" zastępuje standardowy analizator na podstawie pól pola. Nie istnieje żadne przesłonięcie nie globalny. W tym przykładzie `text1` używa analizatora wzorca i `text2`, który nie określa analizatora używa domyślnego.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mieszanie analizatory dla operacji indeksowania i wyszukiwania

Interfejsy API zawiera atrybuty indeksu do określania różnych analizatory do indeksowania i wyszukiwania. **SearchAnalyzer** i **indexAnalyzer** atrybuty muszą być określone jako pary, zastępując pojedynczy **analizatora** atrybutu.


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

Pola zawierające ciągi w różnych językach, można użyć analizatora języków, podczas gdy inne pola Zachowaj ustawienie domyślne (lub użyć innych wstępnie zdefiniowaną lub niestandardową analizatora). Jeśli używasz analizatora języków, wymagane podczas operacji indeksowania i wyszukiwania. Pola, które użyć analizatora języka nie może mieć różne analizatory do indeksowania i wyszukiwania.

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

## <a name="c-examples"></a>C#Przykłady

Korzystając z przykładów kodu zestawu .NET SDK, można dołączyć tych przykładów użycia i konfigurowania analizatorów.

+ [Przypisz wbudowanych analizatora](#Assign-a-language-analyzer)
+ [Konfigurowanie analizatora](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Przypisz analizatora języków

Dowolnego analizatora, który jest używany jako — jest bez konfiguracji, jest określone w definicji pola. Nie jest wymagane do tworzenia konstrukcji analizatora. 

W tym przykładzie przypisuje analizatory English firmy Microsoft i francuskim, do pola Opis. To fragment kodu, pobranego z większych definicję indeksu hotels, tworzenie, używanie klasy hotelu w pliku hotels.cs [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) próbki.

Wywołaj [analizatora](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), określanie [klasy AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zawierający wszystkie analizatory tekstu, obsługiwane w usłudze Azure Search.

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

### <a name="define-a-custom-analyzer"></a>Definiowanie niestandardowego analizatora

Podczas dostosowywania lub konfiguracja jest wymagana, należy dodać konstrukcja analizatora do indeksu. Po zdefiniowaniu, można dodać jej definicja pola jak pokazano w poprzednim przykładzie.

Użyj [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) do utworzenia obiektu. Aby uzyskać więcej przykładów, zobacz [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

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

## <a name="next-steps"></a>Kolejne kroki

+ Przejrzyj nasze wyczerpujący opis [jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md). W tym artykule używa przykładów, aby wyjaśnić, zachowania, które mogą wydawać się counter-intuitive na powierzchni.

+ Spróbuj składnię zapytań dodatkowe [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) sekcji z przykładowym lub [prosta składnia zapytań](query-simple-syntax.md) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak zastosować [analizatory leksykalne specyficzny dla języka](index-add-language-analyzers.md).

+ [Skonfiguruj niestandardowe analizatory](index-add-custom-analyzers.md) minimalnego przetwarzania lub wyspecjalizowanych przetwarzania dla poszczególnych pól.

+ [Porównaj analizatory standardowych i angielskim](https://alice.unearth.ai/) w okienkach sąsiadująco w tej wersji demonstracyjnej witryny sieci web. 

## <a name="see-also"></a>Zobacz także

 [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](query-simple-syntax.md) 

 [Pełna składnia zapytań Lucene](query-lucene-syntax.md) 
 
 [Obsługa wyników wyszukiwania](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
