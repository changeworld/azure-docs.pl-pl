---
title: Pełny tekst Architektura aparatu (Lucene) wyszukiwania w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Wyjaśnienie Lucene zapytań i przetwarzania dokumentów pobierania pojęcia dotyczące wyszukiwania pełnotekstowego w odniesieniu do usługi Azure Search.
services: search
manager: jhubbard
author: yahnoosh
documentationcenter: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/06/2017
ms.author: jlembicz
ms.openlocfilehash: 0b2e66cd40c1b49832b865e5bf59edcf78996eb8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="how-full-text-search-works-in-azure-search"></a>Ile wyszukiwanie pełnotekstowe działa w usłudze Azure Search

Ten artykuł jest dla deweloperów, którzy potrzebują lepiej zrozumieć, jak działa wyszukiwanie pełnotekstowe Lucene w usłudze Azure Search. Tekst zapytania usługi Azure Search w większości przypadków będzie dostarczać bezproblemowo oczekiwanych rezultatów, ale niekiedy może otrzymać wynik, który wydaje się jakiś sposób "off". W takich przypadkach mających tła w czterech etapów wykonywania zapytań Lucene (zapytania analizy, leksykalne analizy, dokumentów dopasowania, oceniania) mogą ułatwić identyfikację określonej zmiany parametrów zapytania ani konfigurację indeksu, który będzie dostarczać żądanego wyniku. 

> [!Note] 
> Wyszukiwanie Azure używana Lucene wyszukiwania pełnotekstowego, ale nie jest wyczerpująca Lucene integracji. Selektywnie możemy ujawniać i rozszerzyć funkcjonalność Lucene na potrzeby scenariuszy ważne do usługi Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Przegląd architektury i diagramu

Przetwarzania zapytania wyszukiwania pełnotekstowego rozpoczyna się od analizowanie tekstu zapytania, aby wyodrębnić terminy wyszukiwania. Aparat wyszukiwania używa indeksu można pobrać dokumentów za pomocą pasujących terminów. Warunki określone zapytanie są czasami podziale i odtworzona w nowych formularzy można rzutować szerszych net za pośrednictwem co może być uznana za potencjalne dopasowania. Zestaw wyników jest posortowane według wynik istotność przypisany do oddzielnych dokumentów dopasowania. Te w górnej części listy uporządkowanej według rangi są zwracane do aplikacji wywołującej.

Przekształcone, wykonywania zapytania składa się z czterech etapów: 

1. Podczas analizowania zapytania 
2. Analiza leksykalne 
3. Pobieranie dokumentu 
4. Oceniania 

Na poniższym diagramie przedstawiono składniki używane do przetwarzania żądania wyszukiwania. 

 ![Diagram architektury zapytań Lucene w usłudze Azure Search][1]


| Główne składniki | Opis funkcjonalności | 
|----------------|------------------------|
|**Analizatory składni zapytania** | Oddzielne terminów zapytania z operatorów zapytań, a następnie utwórz strukturę zapytania (drzewo zapytania) do wysłania do aparatu wyszukiwania. |
|**Analizatory** | Analiza leksykalne na warunki zapytania. Ten proces może obejmować Przekształcanie, usunięcie lub rozszerzania terminów zapytania. |
|**Indeks** | Struktura danych wydajne używany do przechowywania i organizowania warunki wyszukiwania wyodrębnione ze indeksowanych dokumentów. |
|**Aparat wyszukiwania** | Pobiera i wyników pasujących dokumentów na podstawie zawartości odwrócony indeksu. |

## <a name="anatomy-of-a-search-request"></a>Anatomia żądania wyszukiwania

Żądania wyszukiwania jest pełna Specyfikacja powinny być zwracane w zestawie wyników. W najprostszej postaci jest pusty zapytania z kryteriów dowolnego rodzaju. Przykład bardziej realistyczne zawiera parametry, kilka terminów zapytania, prawdopodobnie są ograniczone do niektórych pól, prawdopodobnie wyrażenie filtru i uporządkowanie reguł.  

Poniższy przykład pochodzi żądanie wyszukiwania może wysłać do usługi Azure Search przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2016-09-01 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

Dla tego żądania aparatu wyszukiwania wykonuje następujące czynności:

1. Odfiltrowuje dokumentów, których cena jest co najmniej 60 $ i mniejsza niż 300.
2. Wykonuje zapytanie. W tym przykładzie zapytanie wyszukiwania składa się z wyrażenia oraz postanowienia: `"Spacious, air-condition* +\"Ocean view\""` (użytkownicy zwykle nie wprowadzaj znaki interpunkcyjne, ale tym go w przykładzie pozwala wyjaśniają, jak analizatorów jego obsługa). Dla tego zapytania, aparat wyszukiwania skanuje opis i tytuł pola określone w `searchFields` dokumentów zawierających "Oceanu widok" i również termin "obszerne" lub warunków, które rozpoczyna się od prefiksu "air-condition". `searchMode` Parametr jest używany do dopasowywania na wszystkie wyrażenia (ustawienie domyślne) lub wszystkie z nich w przypadkach, gdy warunek nie jest jawnie wymagane (`+`).
3. Ustala kolejność powstałe w ten sposób zestawu hoteli zbliżeniowe w danej lokalizacji geograficznej lokalizacji, a następnie zwracany do aplikacji wywołującej. 

Większość ten artykuł dotyczy przetwarzania *zapytania wyszukiwania*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrowanie i kolejność są poza zakresem. Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Etap 1: Podczas analizowania zapytania 

Jak wspomniano, ciąg zapytania jest pierwszy wiersz żądania: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analizator składni zapytań oddziela operatory (takie jak `*` i `+` w przykładzie) z wyszukiwania definicje terminów i deconstructs zapytania wyszukiwania w *podzapytania* obsługiwanego typu: 

+ *Termin kwerendy* terminów autonomiczne (takich jak obszerne)
+ *Wyrażenie kwerendy* terminów ujętego w cudzysłów (na przykład Oceanu widoku)
+ *Prefiks zapytania* warunków, a następnie operatora prefiksu `*` (np. air-condition)

Pełna lista zapytań obsługiwanych typów [składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatory skojarzone z podzapytania sprawdzenia, czy zapytanie "musi być" "powinien być" spełnione, aby dokument być uznawane za zgodne. Na przykład `+"Ocean view"` jest "musi" z powodu `+` operatora. 

Analizator składni zapytań restrukturyzuje podzapytań w *zapytania drzewa* (wewnętrznej struktury reprezentujący zapytania) on przekazuje do aparatu wyszukiwania. Podczas pierwszego etapu zapytania analizowania drzewa zapytanie wygląda następująco.  

 ![Wartość logiczna zapytania searchmode any][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Obsługiwane analizatory składni: proste i Pełna Lucene 

 Usługa Azure Search udostępnia dwa języki inne zapytanie, `simple` (ustawienie domyślne) i `full`. Przez ustawienie `queryType` parametru do żądania wyszukiwania, można sprawdzić analizator składni zapytań język zapytań, które można wybrać co wiadomo interpretowanie operatorów i składni. [Prostego zapytania języka](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) jest intuicyjne i niezawodne, często odpowiednie interpretowanie danych wprowadzonych przez użytkownika jako — bez przetwarzania po stronie klienta. Obsługuje ona operatorów zapytań zapoznać się z wyszukiwarek sieci web. [Język zapytań Lucene pełne](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), który można uzyskać za pomocą ustawienia `queryType=full`, rozszerza domyślny język prostego zapytania, dodając obsługę więcej operatorów i typów zapytania jak symbolu wieloznacznego rozmytego, wyrażenia regularnego i zapytania należące do zakresu pola. Na przykład wyrażenia regularnego wysyłany w prosta składnia zapytań zostanie zinterpretowany jako ciąg zapytania i nie wyrażenie. Przykład żądania, w tym artykule używa języka zapytań Lucene pełna.

### <a name="impact-of-searchmode-on-the-parser"></a>Wpływ searchMode parsera 

Inny parametr żądania wyszukiwania wpływającym na analizowanie jest `searchMode` parametru. Kontroluje domyślnego operatora logiczna zapytań: wszelkie (ustawienie domyślne) lub wszystkie.  

Gdy `searchMode=any`, co jest ustawieniem domyślnym ogranicznikiem miejsca między obszerne i air-condition jest lub (`||`), co odpowiada przykładowy tekst zapytania: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Operatory jawnej, takich jak `+` w `+"Ocean view"`, są jednoznaczne konstrukcji Zapytanie logiczne (termin *musi* zgodne). Jest krótszy oczywisty sposób interpretowania pozostałe postanowienia: obszerne i air-condition. Należy aparatu wyszukiwania Znajdź dopasowania w widoku Oceanu *i* obszerne *i* air-condition? Lub należy znaleźć widoku Oceanu plus *jedną* pozostałe postanowienia? 

Domyślnie (`searchMode=any`), aparat wyszukiwania zakłada szerszych interpretacji. Pole albo *powinien* można dopasować, odzwierciedlający semantyki "lub". Drzewo początkowego zapytania przedstawiono wcześniej, z dwóch "powinien" operacje, określa wartość domyślną.  

Załóżmy, że mamy teraz ustawić `searchMode=all`. W takim przypadku miejsca jest interpretowany jako operacji "i". Każdy z pozostałych warunków zarówno należy dokumentów na kwalifikować się jako dopasowanie. Wynikowa przykładowe zapytanie będzie interpretować w następujący sposób: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Drzewo zmodyfikowane zapytanie dla tego zapytania zostaną wyświetlone następujące, gdzie pasującego dokumentu jest przecięcie wszystkie trzy podzapytania: 

 ![Wszystkie searchmode Zapytanie logiczne][3]

> [!Note] 
> Wybieranie `searchMode=any` za pośrednictwem `searchMode=all` jest najlepszych decyzji dotarły uruchamiając reprezentatywny zapytania. Użytkownicy, którzy mogą zawierać operatorów (wspólna podczas wyszukiwania dokumentu przechowuje) stało się wyniki bardziej intuicyjne `searchMode=all` informuje konstrukcje Zapytanie logiczne. Aby uzyskać więcej informacji na temat wzajemnego między `searchMode` i operatorów, zobacz [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Etapu 2: Analiza leksykalne 

Proces leksykalne analizatorów *termin zapytania* i *frazę zapytania* po drzewie zapytania mają strukturę. Analizator akceptuje dane wejściowe tekst nadawane przez analizator, przetwarza tekstu, a następnie wysyła ponownie podzielić na tokeny warunki można włączyć do drzewa zapytania. 

Jest najczęściej używana forma leksykalne analizy *językową analiza* który transformacji zapytań warunków na podstawie reguł specyficzne dla danego języka: 

* Zmniejszenie wyszukiwanego terminu do formularza głównego słowa 
* Usuwanie nieistotne wyrazów (odrzucanych, takie jak "" lub "i" w języku angielskim) 
* Fundamentalne wyraz złożony na części składnika 
* Małą wielkość liter word wielkimi literami 

Wszystkie te operacje zazwyczaj wymazać różnice między wprowadzanie tekstu przez użytkownika i warunki przechowywane w indeksie. Takie operacje wykracza poza przetwarzania tekstu i wymaga dogłębnej wiedzy sam język. Aby dodać tej warstwy w przypadku świadomości, usługi Azure Search obsługuje długą listę [analizatorów języka](https://docs.microsoft.com/rest/api/searchservice/language-support) z Lucene i firmy Microsoft.

> [!Note]
> Wymagania dotyczące analizy może należeć do zakresu od minimalnej do rozbudowane w zależności od danego scenariusza. Złożoność leksykalne analizy można kontrolować, wybierając jedną z analizatorów wstępnie zdefiniowanych lub tworząc własne [analizatora niestandardowego](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analizatory ograniczone do pól z możliwością wyszukiwania i są określone jako część definicji pola. Dzięki temu można różnią się leksykalne analizy na podstawie na pola. Nieokreślona, *standardowe* Lucene Analizator jest używany.

W naszym przykładzie przed analizy drzewa początkowego zapytania ma termin "Spacious" z wielkimi literami "S" i przecinkami, które analizator składni zapytań interpretuje jako część wyszukiwanego terminu (przecinek nie jest uznawany za operatora język kwerendy).  

Kiedy analizatora domyślne przetwarza terminu, zostanie małe litery "Oceanu widok" i "obszerne" i Usuń znak przecinkami. W drzewie zmodyfikowane zapytanie będzie wyglądać w następujący sposób: 

 ![Wartość logiczna zapytania z warunkami przeanalizowane][4]

### <a name="testing-analyzer-behaviors"></a>Testowanie zachowania analizatora 

Działanie analizatora można przetestować przy użyciu [analizowanie interfejsu API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Podaj tekst, który można analizować, aby zobaczyć, co spowoduje wygenerowanie pojęcia analizatora. Na przykład aby zobaczyć, jak standardowe analizator może przetwarzać tekst "air-condition", możesz wykonywać następujące żądania:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardowa analizatora dzieli wejściowego tekstu na następujące dwa tokeny, dodawanie adnotacji do nich z atrybutów, takich jak rozpoczęcia i zakończenia przesunięcia (używanych do wyróżnianie trafień), a także ich pozycji (używane na potrzeby uzgadniania frazy):

~~~~
{  
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Wyjątki analizy leksykalne 

Leksykalne analizy ma zastosowanie tylko do typów zapytania, które wymagają pełną warunki — zapytania termin lub frazę zapytania. Nie ma zastosowania do typów zapytań z warunkami niekompletne — prefiks zapytania, wieloznaczne zapytania regex — lub rozmytego zapytania. Te typy, tym query prefiks terminem zapytań *air-condition\**  w naszym przykładzie zostaną dodane bezpośrednio do drzewa zapytania, pomijanie etap analizy. Lowercasing jest transformacja tylko wykonywane na terminów zapytania tych typów.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Etapu 3: Pobieranie dokumentu 

Pobieranie dokumentu odnosi się do znajdowania dokumentów spełniające warunki w indeksie. Ten etap jest rozpoznawany najlepiej za pośrednictwem przykładem. Zacznijmy od indeksu hotels o następujących prostych schematu: 

~~~~
{   
    "name": "hotels",     
    "fields": [     
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },     
        { "name": "title", "type": "Edm.String", "searchable": true },     
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Dodatkowo założono, że ten indeks zawiera następujące cztery dokumenty: 

~~~~
{ 
    "value": [
        {         
            "id": "1",         
            "title": "Hotel Atman",         
            "description": "Spacious rooms, ocean view, walking distance to the beach."   
        },       
        {         
            "id": "2",         
            "title": "Beach Resort",        
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."     
        },       
        {         
            "id": "3",         
            "title": "Playa Hotel",         
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },       
        {         
            "id": "4",         
            "title": "Ocean Retreat",         
            "description": "Quiet and secluded"
        }    
    ]
}
~~~~

**Jak są indeksowane warunków**

Aby zrozumieć, pobieranie, pomaga znać podstaw indeksowania. Jednostki magazynu jest odwrócony indeks, po jednej dla każdego pola wyszukiwania. W ramach odwrócony indeksu jest posortowana lista wszystkie warunki z wszystkie dokumenty. Mapuje każdego terminu do listy dokumentów, w których występuje, takich jak w poniższym przykładzie.

Do tworzenia warunków w indeksie odwrócony, aparat wyszukiwania przeprowadza analizę leksykalne za pośrednictwem zawartość dokumentów, podobnie jak co się dzieje podczas przetwarzania zapytania:

1. *Dane wejściowe tekst* są przekazywane do analizatora, małych liter, pozbawione znaki interpunkcyjne i tak dalej, w zależności od konfiguracji analizatora. 
2. *Tokeny* są dane wyjściowe z analizy tekstu.
3. *Warunki* zostaną dodane do indeksu.

To typowe, ale nie jest to wymagane, na potrzeby wyszukiwanie i indeksowanie operacje, aby terminów zapytania wyglądu warunków wewnątrz indeks tej samej analizatorów.

> [!Note]
> Wyszukiwanie Azure pozwala określić różne analizatorów do indeksowania i wyszukiwania za pomocą dodatkowych `indexAnalyzer` i `searchAnalyzer` pól parametrów. Jeśli zostanie określona, analizatora ustawiany `analyzer` właściwość jest używana zarówno indeksowania i wyszukiwania.  

**Odwrócony indeksu dla dokumentów przykład**

Powrót do naszym przykładzie dla **tytuł** pola odwrócony indeksu wygląda jak to:

| Termin | Listy dokumentów |
|------|---------------|
| atman | 1 |
| Nieznane | 2 |
| hotel | 1, 3 |
| Oceanu | 4  |
| playa | 3 |
| możliwości | 3 |
| Retreat | 4 |

W polu Tytuł tylko *hoteli* zostaną wyświetlone w dwa dokumenty: 1, 3.

Aby uzyskać **opis** pola, indeks jest następujący:

| Termin | Listy dokumentów |
|------|---------------|
| lotniczego | 3
| i | 4
| Nieznane | 1
| przygotować | 3
| comfortable | 3
| odległość | 1
| Wyspy | 2
| kauaʻi | 2
| znajduje się | 2
| Północna | 2
| Oceanu | 1, 2, 3
| z | 2
| włączone |2
| quiet | 4
| pokoje  | 1, 3
| secluded | 4
| brzegu | 2
| obszerne | 1
| programu | 1, 2
| na | 1
| wyświetl | 1, 2, 3
| Przejście | 1
| z  | 3


**Dopasowywanie terminów zapytania względem indeksowanego warunków**

Załóżmy podane powyżej indeksy odwrócony, wróć do przykładowe zapytanie i zobacz jak pasujących dokumentów zostały znalezione dla nasze przykładowe zapytanie. Odwołaj się, że drzewa końcowego zapytanie wygląda następująco: 

 ![Wartość logiczna zapytania z warunkami przeanalizowane][4]

Podczas wykonywania zapytania poszczególnych kwerend są wykonywane względem pola z możliwością przeszukiwania niezależnie. 

+ Dopasowania "obszerne" TermQuery, dokumentu 1 (hoteli Atman). 

+ PrefixQuery "air-condition *", nie pasuje do żadnych dokumentów. 

  Jest to zachowanie, która czasami confuses deweloperów. Mimo że termin air-conditioned istnieje w dokumencie, są one podzielone na dwa wyrazy przez analizatora domyślne. Odwołaj czy kwerendy prefiksu, zawierających częściowe warunki, nie są analizowane. W związku z tym warunki z prefiksem "air-condition" są wyszukiwane w indeksie odwrócony i nie można odnaleźć.

+ PhraseQuery, "Oceanu widok", wyszukuje Oceanu"warunki" i "Wyświetl" i sprawdza zbliżeniowe terminów w oryginalnym dokumencie. Dokumenty, 1, 2 i 3 odpowiada to zapytanie w polu Opis. Zwróć uwagę, dokument 4 ma Oceanu termin w tytule, ale nie jest uznawane za zgodne, ponieważ szukamy frazy "Oceanu widok", a nie poszczególnych wyrazów. 

> [!Note]
> Wyszukaj zapytanie jest wykonywane niezależnie względem wszystkich pól z możliwością wyszukiwania w indeksie usługi Azure Search, chyba że limit ustawiony za pomocą pola `searchFields` parametru, jak pokazano w przykładzie żądania wyszukiwania. Dokumenty, które odpowiadają w żadnym z wybranych pól są zwracane. 

Na całej dla danego zapytania, dokumenty, które odpowiadają są 1, 2, 3. 

## <a name="stage-4-scoring"></a>Etap 4: oceniania  

Każdy dokument w zestawie wyników wyszukiwania jest przypisany wynik istotności. Funkcja oceny przydatności jest wyższy stopień tych dokumentów, które najlepiej odpowiadają pytanie użytkownika wyrażonej przez zapytanie wyszukiwania. Wynik jest obliczana na podstawie właściwości statystyczne warunków, które pasują do. Stanowiącej podstawę oceniania formuła jest [(częstotliwość dokumentu odwrotność częstotliwość termin) TF/IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). W zapytaniach zawierający rzadkie i wspólne warunki TF/IDF zamienia wyniki zawierające rzadkich terminu. Na przykład w indeks hipotetyczny z wszystkie artykuły Wikipedia z dokumentów wynik kwerendy *Prezes*, dokumentów na *prezesa* są uznawane za istotne więcej niż dokumenty *na*.


### <a name="scoring-example"></a>Przykład oceniania

Odwołaj trzy dokumentów, których dopasowano nasze przykładowe zapytanie:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{  
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Dokument 1 dopasować najlepiej zapytania, ponieważ zarówno termin *obszerne* i wymagane wyrażenie *widoku Oceanu* występują w polu Opis. Następne dwa dokumenty zgodny tylko frazy *widoku Oceanu*. Może być zaskakująco oceny przydatności dla dokumentu, 2 i 3 jest inny, nawet jeśli ich wynik kwerendy w taki sam sposób. Jest to spowodowane oceniania formuła zawiera więcej elementów niż tylko TF/IDF. W takim przypadku dokumentu 3 zostało przydzielone nieco więcej punktów, ponieważ jego opis jest krótszy. Dowiedz się więcej o [Lucene jego praktycznych oceniania formuła](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) zrozumieć, jak długość pola i inne czynniki mogą mieć wpływ wynik istotności.

Niektóre typy (symbol wieloznaczny, prefiksu, wyrażenie regularne) zapytań zawsze współtworzenia stałej wynik do ogólny wynik dokumentu. Dzięki temu dopasowań za pośrednictwem rozszerzenia zapytania mają zostać uwzględnione w wynikach, ale bez wpływu na kolejność. 

Dlaczego to ma znaczenie pokazano w przykładzie. Wyszukiwanie symboli wieloznacznych, tym wyszukiwania prefiksów są niejednoznaczne zgodnie z definicją, ponieważ dane wejściowe są częściowe ciągiem o potencjalnych dopasowań na bardzo dużą liczbę różnych warunków (rozważ danych wejściowych "samouczek *" dopasowań "przewodniki", "tourettes" i "tourmaline"). Biorąc pod uwagę rodzaj tych wyników, nie istnieje sposób rozsądnych rozpoznać terminów są większej wartości niż inne. Z tego powodu możemy Ignoruj określenie częstotliwości podczas oceniania wyników w kwerendach typów symboli wieloznacznych, prefiksu i wyrażenia regularnego. W żądaniu wieloczęściowych wyszukiwania zawierającego częściowymi lub całkowitymi warunki wyniki z częściowa dane wejściowe są włączone z wynikiem stałej, aby uniknąć odchylenia kierunku potencjalnie nieoczekiwany dopasowań.

### <a name="score-tuning"></a>Wynik dostrajania

Istnieją dwa sposoby dostroić oceny przydatności w usłudze Azure Search:

1. **Ocenianie profile** promowania dokumentów na liście uporządkowanej według rangi wyników na podstawie zestawu reguł. W tym przykładzie firma Microsoft może wziąć pod uwagę dokumentów, których dopasowano w polu Tytuł większe niż dokumenty zgodnych w polu Opis. Ponadto indeksu, gdyby pola ceny dla każdej hoteli, możemy wspierania dokumentów za pomocą dolnej ceny. Dowiedz się, jak więcej [Dodaj oceniania profile do indeksu wyszukiwania.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Promowanie** (dostępne tylko w składnia zapytań Lucene pełne) zapewnia operatora zwiększania wyniku `^` , który można zastosować do dowolnej części drzewa zapytania. W naszym przykładzie zamiast wyszukiwanie na podstawie prefiksów *air-condition*\*, co może wyszukać albo dokładną termin *air-condition* lub prefiks, ale dokumenty, które odpowiadają na określenie dokładnej wyżej stosując zwiększanie wyniku zapytania termin: * warunku lotniczego ^ 2 || AIR-Condition **. Dowiedz się więcej o [promowanie](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Ocenianie rozproszonej indeksu

Wszystkie indeksy w usłudze Azure Search automatycznie są podzielone na kilka fragmentów, co pozwala nam szybkie dystrybuowanie indeksu wśród wielu węzłów podczas skalowania usługi górę lub w dół. Podczas generowania żądania wyszukiwania wystawienia go przed każdym niezależnych niezależnie. Wyniki z każdym niezależnego fragmentu są następnie scalić i uporządkowanych według wyników (jeśli inne kolejność nie jest określona). Ważne jest, aby dowiedzieć się, że oceniania wag funkcja wysyłanie zapytań określenie częstotliwości przed jego częstotliwość odwrotny dokumentu w wszystkich dokumentów w niezależnych, nie na wszystkich odłamków!

Oznacza to, że oceny przydatności *można* mogą różnić się w dokumentach identyczne znajdują się na różnych fragmentów. Na szczęście różnice te zwykle znikają w miarę zwiększania się liczby dokumentów w indeksie z powodu więcej nawet termin dystrybucji. Nie jest możliwe do wniosku, na które niezależnych zostaną umieszczone wszystkie danego dokumentu. Jednak przy założeniu, że klucz dokumentu nie powoduje zmiany, zostanie zawsze do niej przypisany do sam identyfikator niezależnego fragmentu.

Ogólnie rzecz biorąc wynik dokumentu nie jest atrybutem najlepsze porządkowania dokumentów, jeśli stabilność kolejność jest ważna. Na przykład mając dwa dokumenty z identycznymi wynik, brak żadnej gwarancji, które z nich znajduje się w kolejnych uruchomieniach tego samego zapytania. Wynik dokumentu tylko powinien zapewnić zobaczyć znaczenie dokumentu względem innych dokumentów w zestawie wyników.

## <a name="conclusion"></a>Podsumowanie

Powodzenie aparaty wyszukiwania w Internecie spowodował oczekiwania dotyczące wyszukiwania pełnotekstowego w dane prywatne. Dla niemal każdego rodzaju środowiska wyszukiwania oczekuje się teraz aparat zrozumienie naszym celem nawet wtedy, gdy warunki są błędne lub niekompletne. Oczekujemy może nawet dopasowań w oparciu równoważnego lub synonimy, które nigdy określone w pobliżu.

Z technicznego punktu widzenia wyszukiwanie pełnotekstowe jest wysoce złożone, zaawansowane analizy językowe i systematyczne podejście do przetwarzania w sposób przetwarzania, rozwiń i przekształcanie terminów zapytania w celu dostarczania odpowiednich wyników. Podana związanego z używaniem skomplikowane, istnieje wiele czynników, które mogą wpłynąć na wyniki zapytania. Z tego powodu poświęcić czas zrozumienie sposobu wyszukiwania pełnotekstowego oferuje wymierne korzyści podczas próby pracy za pośrednictwem nieoczekiwane wyniki.  

W tym artykule przedstawione wyszukiwania pełnotekstowego w kontekście usługi Azure Search. Mamy nadzieję, że umożliwia tła wystarczające do rozpoznawania możliwe przyczyny i rozwiązania dotyczące typowych problemów z zapytania. 

## <a name="next-steps"></a>Kolejne kroki

+ Tworzenie indeksu próbki, wypróbować różne zapytania i przegląd wyników. Aby uzyskać instrukcje, zobacz [kompilacji i tworzenie zapytań względem indeksu w portalu](search-get-started-portal.md#query-index).

+ Spróbuj składni zapytania dodatkowe [dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) sekcji przykład lub [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Przegląd [oceniania profile](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Jeśli chcesz dostroić rankingu w aplikacji wyszukiwania.

+ Dowiedz się, jak zastosować [analizatorów leksykalne specyficzny dla języka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurowanie niestandardowych analizatorów](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimalnego przetwarzania lub specjalne przetwarzania określonych pól.

+ [Porównanie analizatorów standard i angielskiej wersji językowej](http://alice.unearth.ai/)) obok siebie w tej witrynie sieci web demonstracyjnej. 

## <a name="see-also"></a>Zobacz także

[Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Obsługa wyników wyszukiwania](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
