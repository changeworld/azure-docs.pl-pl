---
title: Analizatory w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Przypisz analizatorów do pól tekstowych można wyszukiwać w indeksie, aby zastąpić domyślne standardowe Lucene z alternatyw niestandardowych, wstępnie zdefiniowanych lub specyficzny dla języka.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.openlocfilehash: e858966fb5a15b84af1952399a5eff3ca50d0d59
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="analyzers-in-azure-search"></a>Analizatory w usłudze Azure Search

*Analizator* wchodzi w skład [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) odpowiedzialna za przetwarzanie tekstu w ciągów zapytania i indeksowanych dokumentów. Następujące przekształcenia są typowe podczas analizy:

+ Wyrazy niemające istotnego (odrzucanych) i znaki interpunkcyjne zostały usunięte.
+ Wyrażenia i wyrazów dzielą się na części.
+ Wielkie litery słowa są małe — z uwzględnieniem wielkości liter.
+ Słowa zostały zredukowane do głównego formularzy, dzięki czemu można znaleźć dopasowania niezależnie od tego, czas.

Konwertuj językowe analizatorów tekst wejściowy do pierwotnego lub głównego formularze, które są wydajne przechowywanie informacji i pobierania. Konwersja występuje podczas indeksowania po utworzeniu indeksu, a następnie ponownie podczas wyszukiwania została przeczytana indeksu. Jest bardziej prawdopodobne uzyskać wyniki wyszukiwania spodziewasz się, jeśli używasz tego samego analizatora tekstu dla obu operacji.

Usługa Azure Search korzysta [analizator standardowe Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako domyślny. Można zastąpić domyślną na podstawie przez pole. W tym artykule opisano zakresu opcji oraz oferuje najlepsze rozwiązania dotyczące analizy niestandardowych. Umożliwia także przykładowe konfiguracje dla różnych kluczowych scenariuszy.

## <a name="supported-analyzers"></a>Obsługiwane analizatorów

Poniższa lista zawiera analizatorów, które są obsługiwane w usłudze Azure Search.

| Kategoria | Opis |
|----------|-------------|
| [Standardowa analizator Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Nie specyfikacji lub konfiguracja jest wymagana. Ta ogólnego przeznaczenia analizatora wykonuje również dla większości środowisk i języków.|
| Wstępnie zdefiniowane analizatorów | Oferowana jako produkt gotowy przeznaczony do użycia jako-przy ograniczonej dostosowania. <br/>Istnieją dwa typy: specjalistyczne i języka. Dzięki czemu można je "wstępnie zdefiniowane" jest odwoływać je według nazwy z bez dostosowania. <br/><br/>[Specjalizowany analizatorów (niezależny od języka)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) są używane, gdy dane wejściowe tekst wymagają specjalnych przetwarzania lub minimalnego przetwarzania. Wstępnie zdefiniowane analizatorów języka nie obejmują **Asciifolding**, **— słowo kluczowe**, **wzorzec**, **proste**, **zatrzymać**, **Odstępem**.<br/><br/>[Analizatorów języka](https://docs.microsoft.com/rest/api/searchservice/language-support) są używane, jeśli potrzebujesz zaawansowanych językowej pomocy technicznej dla poszczególnych języków. Usługa Azure Search obsługuje 35 analizatorów języka Lucene i 50 analizatorów języka naturalnego przetwarzania firmy Microsoft. |
|[Analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Konfiguracja użytkownika kombinację istniejące elementy składające się z jednego tokenizatora (wymagane) i opcjonalnie filtrów (char lub tokena).|

Można dostosować wstępnie zdefiniowanych analizatora, takich jak **wzorzec** lub **zatrzymać**, aby użyć alternatywnych opcji udokumentowane w [wstępnie zdefiniowanych odwołanie analizatora](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Tylko kilka wstępnie zdefiniowanych analizatorów ma opcje, które można ustawić. Jak z dowolnej dostosowania zapewniają nowej konfiguracji o nazwie, takich jak *myPatternAnalyzer* odróżniający go od analizator Lucene wzorca.

## <a name="how-to-specify-analyzers"></a>Jak określać analizatorów

1. (w przypadku niestandardowych analizatorów tylko) Utwórz **analizator** sekcji w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) , a także [analizatorów niestandardowe > Utwórz](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Na [definicji pola](https://docs.microsoft.com/rest/api/searchservice/create-index) w indeksie, ustaw **analizator** właściwości analizatora docelowego na nazwę (na przykład `"analyzer" = "keyword"`. Prawidłowe wartości to nazwa wstępnie zdefiniowanych analizatora, analizatora języka lub analizatora niestandardowego także zdefiniowana w schemacie indeksu.

3. Opcjonalnie, zamiast jedną **analizator** właściwości, można ustawić różne analizatorów indeksowanie i zapytań za pomocą **indexAnalyzer** i **searchAnalyzer "** pola Parametry. 

3. Dodawanie analizatora do definicji pola wiąże się z operacji zapisu w indeksie. Jeśli dodasz **analizator** do istniejącego indeksu, zanotuj następujące kroki:
 
 | Scenariusz | Wpływ | Kroki |
 |----------|--------|-------|
 | Dodaj nowe pole | Minimalny | Jeśli pole nie istnieje w schemacie, nie ma żadna zmiana pola, aby upewnić się, ponieważ pole nie ma jeszcze fizycznej obecności w indeksie. Użyj [Aktualizuj indeks](https://docs.microsoft.com/rest/api/searchservice/update-index) i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) dla tego zadania.|
 | Dodaj analizatora do istniejącego pola indeksowanego. | Skompiluj ponownie | Odwrócony indeksu dla tego pola muszą zostać ponownie utworzone od podstaw w górę i muszą być ponownie indeksowane zawartość tych pól. <br/> <br/>Dla indeksów w opracowaniu active [usunąć](https://docs.microsoft.com/rest/api/searchservice/delete-index) i [utworzyć](https://docs.microsoft.com/rest/api/searchservice/create-index) indeks, aby pobrać nową definicję pola. <br/> <br/>Indeksy w środowisku produkcyjnym należy utworzyć nowe pole do poprawione zdefiniowanie i rozpocząć korzystanie z niej. Użyj [Aktualizuj indeks](https://docs.microsoft.com/rest/api/searchservice/update-index) i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) uwzględnienie nowego pola. Później, w ramach obsługi planowanych indeksu możesz wyczyścić indeksu, aby usunąć przestarzałe pola. |

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

Ta sekcja zawiera wskazówki dotyczące sposobu pracy z analizatorów.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analizator do odczytu i zapisu w przypadku braku określonych wymagań

Wyszukiwanie Azure pozwala określić różne analizatorów do indeksowania i wyszukiwania za pomocą dodatkowych `indexAnalyzer` i `searchAnalyzer` pól parametrów. Jeśli zostanie określona, analizatora ustawiany `analyzer` właściwość jest używana zarówno indeksowania i wyszukiwania. Jeśli `analyzer` jest określony, analizator Lucene Standardowy domyślny jest używany.

Ogólną zasadą jest do korzystania z tego samego analizatora indeksowanie i badania, chyba że określone uwarunkowania inaczej. Należy dokładnie przetestować. Podczas przetwarzania tekstu różni się wyszukiwania i indeksowania godziny, możesz uruchomić ryzyko niezgodność między indeksowanego terminy podczas wyszukiwania i indeksowania analizatora konfiguracje nie są wyrównane i zapytania.

### <a name="test-during-active-development"></a>Testowanie podczas tworzenia active

Zastępowanie standardowych analyzer wymaga odbudowywanie indeksu. Zdecyduj, jeśli to możliwe, na które analizatorów do użycia podczas tworzenia active, przed udostępnieniem indeksu w środowisku produkcyjnym.

### <a name="inspect-tokenized-terms"></a>Przejrzyj postanowienia tokenami

Jeśli wyszukiwanie nie zwraca oczekiwanych rezultatów, najbardziej prawdopodobnym scenariuszem jest token rozbieżności między termin dane wejściowe zapytania i tokenami terminy w indeksie. Jeśli tokenów nie są takie same, dopasowań nie zmaterializowania. Aby sprawdzić tokenizatora danych wyjściowych, zaleca się używanie [analizowanie interfejsu API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako narzędzie do badania. Odpowiedź składa się z tokenów, tak jak w przypadku określonego analizatora.

### <a name="compare-english-analyzers"></a>Porównanie angielskiej wersji językowej analizatorów

[Pokaz analizatora wyszukiwania](http://alice.unearth.ai/) jest to aplikacja innej firmy pokaz przedstawiający porównanie side-by-side standardowe analizator Lucene, Lucene w języku angielskim analizator i firmy Microsoft w angielskiej wersji językowej języka naturalnego procesora. Indeks jest stała; zawiera on tekst z popularnych wątku. Dla każdego wyszukiwania danych wejściowych musisz podać, wyniki z każdym analizatora są wyświetlane w okienka sąsiadujących ze sobą, co pozwala zorientować się, jak każdy analizator przetwarza te same parametry. 

## <a name="examples"></a>Przykłady

Poniższe przykłady Pokaż definicje analizatora kilka kluczowych scenariuszy.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Przykład 1: Opcje niestandardowe

W tym przykładzie przedstawiono definicję analyzer przy użyciu opcji niestandardowych. Niestandardowe opcje filtry char, tokenizatory i filtry tokenu są określane oddzielnie jako nazwane konstrukcje, a następnie odwołania do w definicji analizatora. Wstępnie zdefiniowane elementy są używane jako — jest i po prostu wskazywana przez nazwę.

Instruktaż w tym przykładzie:

* Analizatory są właściwościami klasy pola dla pola wyszukiwania.
* Niestandardowy analizator jest częścią definicji indeksu. Może on lekkim dostosowane (na przykład dostosowywania jednej opcji w jednym filtrze) lub w kilku miejscach.
* W takim przypadku analizatora niestandardowego jest "my_analyzer", który z kolei używa dostosowane tokenizatora standardowe "my_standard_tokenizer" i dwa filtry tokenu: małe litery i dostosowane asciifolding filtru "my_asciifolding".
* Definiuje filtr char niestandardowe "map_dash" należy zastąpić wszystkie łączniki podkreślenia przed tokenizacji (standardowe tokenizatora podziały na łączniki, ale nie na podkreślenie).

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Przykład 2: Zastąpienie domyślnego analizatora

Standardowa Analizator jest ustawieniem domyślnym. Załóżmy, że chcesz zastąpić domyślne inny analizator wstępnie zdefiniowane, takich jak wzorzec analizatora. Jeśli nie ma ustawienia niestandardowe opcje, wystarczy określić jego nazwę w definicji pola.

Element "analizator" zastępuje standardowe analizatora na podstawie przez pole. Nie istnieje żadne przesłonięcie globalnego. W tym przykładzie `text1` używa analizatora wzorca i `text2`, która nie określa analizatora używa domyślnej.

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Przykład 3: Analizatorów różne operacje indeksowania i wyszukiwania

Interfejsy API zawiera atrybuty indeksu określenie różnych analizatorów do indeksowania i wyszukiwania. `searchAnalyzer` i `indexAnalyzer` atrybuty muszą być określone jako pary, zastępując pojedynczą `analyzer` atrybutu.


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

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Przykład 4: Analizatora języka

Pola zawierające ciągi w różnych językach można użyć analizatora języka, podczas gdy inne pola Zachowaj ustawienie domyślne (lub użyć innych wstępnie zdefiniowany lub niestandardowy analizator). Jeśli używasz analizatora języka, należy użyć dla operacji indeksowania i wyszukiwania. Pola, które użyć analizatora języka nie może mieć różne analizatorów do indeksowania i wyszukiwania.

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

## <a name="next-steps"></a>Kolejne kroki

+ Przejrzyj naszych wyczerpujący opis [jak pełne wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md). W tym artykule wykorzystano przykładowe wyjaśnić zachowania, które mogą wydawać się counter-intuitive na powierzchni.

+ Spróbuj składni zapytania dodatkowe [dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) sekcji przykład lub [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak zastosować [analizatorów leksykalne specyficzny dla języka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurowanie niestandardowych analizatorów](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimalnego przetwarzania lub specjalne przetwarzania dla poszczególnych pól.

+ [Porównanie analizatorów standard i angielskiej wersji językowej](http://alice.unearth.ai/) w okienkach sąsiadujących ze sobą w tej witrynie sieci web demonstracyjnej. 

## <a name="see-also"></a>Zobacz także

 [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Obsługa wyników wyszukiwania](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
