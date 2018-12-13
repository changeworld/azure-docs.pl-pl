---
title: Analizatory do równoległego językowej i przetwarzanie tekstu — usługi Azure Search
description: Analizatory przypisywanie do pól tekstowych można wyszukiwać w indeksie, aby zastąpić domyślne standardowa Lucene z alternatywami niestandardowych, wstępnie zdefiniowanych lub specyficzne dla języka.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 1de3743d6ec37d263e16b168d32d2b56b0a28295
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310542"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analizatory do przetwarzania w usłudze Azure Search tekstu

*Analizatora* jest składnikiem [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) odpowiedzialna za przetwarzanie tekstu w ciągów zapytania i indeksowane dokumenty. Następujące przekształcenia są typowe podczas analizy:

+ Wyrazy inne niż niezbędne (STOP-słowa) i znaki interpunkcyjne są usuwane.
+ Wyrażenia i wyrazów są podzielone na części zamienne.
+ Wielkie litery słowa są małe — z uwzględnieniem wielkości liter.
+ Słowa zostały zredukowane do głównego formularzy, dzięki czemu można znaleźć dopasowania niezależnie od tego czasu teraźniejszego.

Convert językowej analizatory wprowadzanie tekstu do podstawowego lub główny formularzy, które są wydajne informacji przechowywania i pobierania. Konwersja występuje podczas indeksowania, gdy indeks został utworzony, a następnie ponownie podczas wyszukiwania, gdy indeks jest do odczytu. Jesteś bardziej prawdopodobne uzyskać wyniki wyszukiwania, oczekujesz, że jeśli używasz tego samego analizatora tekstu dla obu operacji.

Usługa Azure Search używa [analizator Lucene standardowa](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako domyślny. Można zastąpić domyślne na podstawie pól pola. W tym artykule opisano zakresu opcji oraz oferuje najlepsze rozwiązania w zakresie analizy niestandardowych. Umożliwia także przykładowe konfiguracje dla kluczowych scenariuszy.

## <a name="supported-analyzers"></a>Obsługiwane analizatorów

Na poniższej liście opisano, w których analizatory są obsługiwane w usłudze Azure Search.

| Kategoria | Opis |
|----------|-------------|
| [Standardowy analizator Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Domyślne. Nie specyfikacji lub konfiguracja jest wymagana. Ta ogólnego przeznaczenia analizatora wykonuje również większość języków i scenariuszy.|
| Wstępnie zdefiniowane analizatorów | Oferowana jako gotowego produktu jest przeznaczony do użycia jako — z ograniczoną dostosowywania. <br/>Istnieją dwa typy: wyspecjalizowanych i język. Co sprawia, że ich "wstępnie zdefiniowane" jest odwoływać je przy użyciu nazwy, za pomocą Brak dostosowanych wartości. <br/><br/>[Wyspecjalizowane analizatory (niezależny od języka)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) są używane, gdy tekst w danych wejściowych wymaga wyspecjalizowane przetwarzania lub przetwarzanie minimalny. Wstępnie zdefiniowane analizatory języka nie obejmują **Asciifolding**, **— słowo kluczowe**, **wzorzec**, **proste**, **zatrzymać**, **Odstępu**.<br/><br/>[Analizatory języków](https://docs.microsoft.com/rest/api/searchservice/language-support) są używane, gdy potrzebujesz zaawansowanych językowej pomocy technicznej dla poszczególnych języków. Usługa Azure Search obsługuje 35 analizatory języka Lucene i 50 analizatory przetwarzania języka naturalnego firmy Microsoft. |
|[Analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Konfiguracja użytkownika kombinacja elementów istniejącego, składające się z jednego tokenizatora (wymagane) i opcjonalne filtry (char lub tokenu).|

Można dostosować wstępnie zdefiniowanych analizatora, takich jak **wzorzec** lub **zatrzymać**, aby użyć alternatywnych opcji udokumentowane w artykule [wstępnie zdefiniowanych odwołania do analizatora](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Tylko kilka wstępnie zdefiniowanych analizatory mają opcje, które można ustawić. Zgodnie z jakiegokolwiek dostosowywania, podaj nowej konfiguracji z nazwą, taką jak *myPatternAnalyzer* odróżniający go od analizator Lucene wzorca.

## <a name="how-to-specify-analyzers"></a>Jak określić analizatorów

1. (dla tylko analizatory niestandardowe) Tworzenie **analizatora** sekcji w definicji indeksu. Aby uzyskać więcej informacji, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) , a także [analizatory niestandardowe > Utwórz](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Na [definicji pola](https://docs.microsoft.com/rest/api/searchservice/create-index) w indeksie, ustaw **analizatora** właściwość na nazwę analizatora docelowej (na przykład `"analyzer" = "keyword"`. Prawidłowe wartości to nazwa wstępnie zdefiniowanych analizatora, analizatora języków lub analizatora niestandardowego, również zdefiniowanej w schemacie indeksu.

3. Opcjonalnie, zamiast jednego **analizatora** właściwości można ustawić różne analizatory do indeksowania i zapytań za pomocą **indexAnalyzer** i **searchAnalyzer "** pola Parametry. 

3. Dodanie analizatora do definicji pola jest naliczana opłata operacji zapisu w indeksie. Jeśli dodasz **analizator** do istniejącego indeksu, należy pamiętać, następujące czynności:
 
 | Scenariusz | Wpływ | Kroki |
 |----------|--------|-------|
 | Dodawanie nowego pola | Minimalny | Jeśli pole nie istnieje jeszcze w schemacie, nie istnieje żadna zmiana pola, aby upewnić się, ponieważ pole nie ma jeszcze fizycznej obecności w indeksie. Użyj [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) dla tego zadania.|
 | Dodaj Analizator do istniejącego pola indeksowane. | Ponowna kompilacja | Odwrócony indeksu dla tego pola muszą zostać ponownie utworzone od podstaw w górę i zawartość dla tych pól muszą być ponownie indeksowane. <br/> <br/>Dla indeksów w opracowaniu active [Usuń](https://docs.microsoft.com/rest/api/searchservice/delete-index) i [tworzenie](https://docs.microsoft.com/rest/api/searchservice/create-index) indeksu do pobrania nowej definicji pola. <br/> <br/>W przypadku indeksów w środowisku produkcyjnym należy utworzyć nowe pole, aby podać definicję poprawione i rozpocząć korzystanie z niej. Użyj [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index) i [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) włączenie nowego pola. Później, w ramach indeksu planowana obsługi można wyczyścić indeksów, aby usunąć przestarzałe pola. |

## <a name="tips-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

Ta sekcja zawiera wskazówki dotyczące sposobu pracy z analizatorów.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analizator dla odczytu / zapisu, jeśli nie masz określonych wymagań

Usługa Azure Search pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą dodatkowych `indexAnalyzer` i `searchAnalyzer` pola parametrów. Jeśli nie zostanie podany, analizator ustawiany `analyzer` właściwość jest używana do indeksowania i wyszukiwania. Jeśli `analyzer` jest nieokreślony, używany analizator Lucene Standardowy domyślny.

Ogólną zasadą jest do użytku z tego samego analizatora indeksowania i wykonywania zapytań, chyba że określone uwarunkowania. Należy dokładnie przetestować. Podczas przetwarzania tekstu różni się w momencie indeksowania i wyszukiwania, możesz uruchomić ryzyko niezgodność między indeksowanych terminy, gdy wyszukiwanie i indeksowanie analizatora konfiguracje nie są wyrównane i zapytania.

### <a name="test-during-active-development"></a>Testowania podczas programowania active

Zastępowanie standardowych analyzer wymaga odbudowywanie indeksu. Jeśli to możliwe decyzję w sprawie analizatorów, który ma być używany podczas aktywnego rozwoju, przed stopniowe indeksu w środowisku produkcyjnym.

### <a name="inspect-tokenized-terms"></a>Sprawdzanie warunków tokenami

Jeśli wyszukiwanie nie zwraca oczekiwanych rezultatów, najbardziej prawdopodobnym scenariuszem jest token rozbieżności między termin wejść na tym zapytaniu i tokenami terminy w indeksie. Jeśli tokeny nie są takie same, do zmaterializowania się nie powieść dopasowań. Aby sprawdzić dane wyjściowe tokenizatora, zaleca się używanie [analizowanie API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako narzędzie do analizy. Odpowiedź składa się tokenów, tak jak w przypadku określonego analizatora.

### <a name="compare-english-analyzers"></a>Porównaj analizatory w języku angielskim

[Wersja demonstracyjna analizatora wyszukiwania](http://alice.unearth.ai/) jest aplikacją firm pokaz przedstawiający porównania side-by-side standardowy analizator Lucene, Lucene w języku angielskim analizator i procesora angielskiej języka naturalnego firmy Microsoft. Indeks został rozwiązany; zawiera ona tekstu z popularnych wątku. Dla każdego dane wejściowe zostaną podane, wyniki z każdego analizatora są wyświetlane w sąsiadujących okienka, co daje poczucie przetwarzaniu tego samego ciągu w każdej analizatora. 

## <a name="examples"></a>Przykłady

Poniższe przykłady pokazują analizatora definicje kilku kluczowych scenariuszy.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Przykład 1: Niestandardowe opcje

W tym przykładzie przedstawiono definicję analyzer przy użyciu opcji niestandardowych. Niestandardowe opcje filtrów char, tokenizatory i tokenu filtry są określane oddzielnie jako nazwany konstrukcje, a następnie wspomniane w definicji analizatora. Wstępnie zdefiniowane elementy są używane jako — jest i po prostu odwołuje się nazwa.

Instruktaż następująco:

* Analizatory to właściwość klasy polu możliwym do przeszukania.
* Analizatora niestandardowego jest częścią definicji indeksu. Może on lekko dostosować (na przykład dostosowywania pojedynczego opcję w jeden filtr) lub dostosowanych w kilku miejscach.
* W tym przypadku analizatora niestandardowego to "my_analyzer", który z kolei używa dostosowane tokenizatora standardowy "my_standard_tokenizer" i dwa filtry tokenu: małe litery i dostosowane asciifolding filtru "my_asciifolding".
* Definiuje filtr char niestandardowe "map_dash", aby zastąpić wszystkie łączniki podkreślenia przed tokenizacji (standardowa tokenizatora podziały dash, ale nie na podkreślenie).

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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Przykład 2: Zastąp analizatora domyślne

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Przykład 3: Różne analizatory dla operacji indeksowania i wyszukiwania

Interfejsy API zawiera atrybuty indeksu do określania różnych analizatory do indeksowania i wyszukiwania. `searchAnalyzer` i `indexAnalyzer` atrybuty muszą być określone jako pary, zastępując pojedynczy `analyzer` atrybutu.


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
### <a name="example-4-language-analyzer"></a>Przykład 4: Analizatora języków

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

## <a name="next-steps"></a>Kolejne kroki

+ Przejrzyj nasze wyczerpujący opis [jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md). W tym artykule używa przykładów, aby wyjaśnić, zachowania, które mogą wydawać się counter-intuitive na powierzchni.

+ Spróbuj składnię zapytań dodatkowe [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) sekcji z przykładowym lub [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Dowiedz się, jak zastosować [analizatory leksykalne specyficzny dla języka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Skonfiguruj niestandardowe analizatory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimalnego przetwarzania lub wyspecjalizowanych przetwarzania dla poszczególnych pól.

+ [Porównaj analizatory standardowych i angielskim](http://alice.unearth.ai/) w okienkach sąsiadująco w tej wersji demonstracyjnej witryny sieci web. 

## <a name="see-also"></a>Zobacz także

 [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Obsługa wyników wyszukiwania](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
