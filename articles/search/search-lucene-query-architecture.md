---
title: Pełnotekstowa architektura aparatu zapytań i indeksowania (Lucene)
titleSuffix: Azure Cognitive Search
description: Sprawdza przetwarzania zapytań Lucene i procesy pobierania dokumentów dla wyszukiwania pełnotekstowego, związane z usługi Azure Cognitive Search.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282941"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search

Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują głębszego zrozumienia, jak działa wyszukiwanie pełnotekstowe lucene w usłudze Azure Cognitive Search. W przypadku zapytań tekstowych usługa Azure Cognitive Search zwraca oczekiwane wyniki w większości scenariuszy, okazjonalnie jednak możesz otrzymywać wyniki, które wydają się w jakiś sposób „nieprawidłowe”. W takiej sytuacji wiedza na temat czterech etapów wykonywania zapytań Lucene (analizowanie zapytania, analiza leksykalna, dopasowywanie dokumentów i ocenianie) może pomóc w określeniu, jakie zmiany parametrów zapytania lub konfiguracji indeksu umożliwią uzyskanie oczekiwanych wyników. 

> [!Note] 
> Usługa Azure Cognitive Search używa Lucene do wyszukiwania pełnotekstowego, ale integracja lucene nie jest wyczerpująca. Możemy selektywnie uwidaczniać i rozszerzać funkcje Lucene, aby włączyć scenariusze ważne dla usługi Azure Cognitive Search. 

## <a name="architecture-overview-and-diagram"></a>Omówienie architektury i diagram

Przetwarzanie kwerendy wyszukiwania pełnotekstowego rozpoczyna się od analizowania tekstu zapytania w celu wyodrębnienia wyszukiwanych haseł. Wyszukiwarka używa indeksu do pobierania dokumentów z pasującymi terminami. Poszczególne terminy zapytania są czasami podzielone i odtworzone na nowe formy, aby rzucić szerszą sieć na to, co można uznać za potencjalne dopasowanie. Zestaw wyników jest następnie sortowany według wyniku trafności przypisanego do każdego dokumentu dopasowania. Osoby z górnej części listy rankingowej są zwracane do aplikacji wywołującej.

Przekształcone, wykonywanie kwerend ma cztery etapy: 

1. Analizowanie kwerend 
2. Analiza leksykalne 
3. Pobieranie dokumentów 
4. Scoring (Ocenianie) 

Poniższy diagram ilustruje składniki używane do przetwarzania żądania wyszukiwania. 

 ![Diagram architektury zapytań lucene w usłudze Azure Cognitive Search][1]


| Główne składniki | Opis funkcjonalny | 
|----------------|------------------------|
|**Analizatory zapytań** | Odłącz terminy kwerend od operatorów zapytań i utwórz strukturę zapytań (drzewo zapytań), które mają być wysyłane do wyszukiwarki. |
|**Analizatory** | Przeprowadzaj analizę leksykalne na warunkach zapytania. Ten proces może obejmować przekształcanie, usuwanie lub rozszerzanie terminów kwerendy. |
|**Index** | Wydajna struktura danych używana do przechowywania i organizowania terminów z wyszukiwanych wyodrębnionych z indeksowanych dokumentów. |
|**Wyszukiwarki** | Pobiera i ocenia pasujące dokumenty na podstawie zawartości odwróconego indeksu. |

## <a name="anatomy-of-a-search-request"></a>Anatomia żądania wyszukiwania

Żądanie wyszukiwania jest pełną specyfikacją tego, co powinno zostać zwrócone w zestawie wyników. W najprostszej formie jest to puste zapytanie bez żadnych kryteriów. Bardziej realistyczny przykład zawiera parametry, kilka terminów kwerendy, być może ograniczone do niektórych pól, z ewentualnie wyrażenie filtru i reguły zamawiania.  

Poniższy przykład jest żądanie wyszukiwania, które można wysłać do usługi Azure Cognitive Search przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

W przypadku tego żądania wyszukiwarka wykonuje następujące czynności:

1. Filtruje dokumenty, w których cena wynosi co najmniej $60 i mniej niż $300.
2. Wykonuje kwerendę. W tym przykładzie kwerenda wyszukiwania składa `"Spacious, air-condition* +\"Ocean view\""` się z fraz i terminów: (użytkownicy zazwyczaj nie wprowadź interpunkcji, ale włączenie go w przykładzie pozwala nam wyjaśnić, jak analizatory obsługiwać go). W przypadku tej kwerendy wyszukiwarka skanuje `searchFields` pola opisu i tytułu określone w przypadku dokumentów zawierających "Widok oceanu", a dodatkowo na określenie "przestronne" lub na warunkach rozpoczynających się od prefiksu "klimatyzacja". Parametr `searchMode` jest używany do dopasowania w dowolnym terminie (domyślnie) lub wszystkich z nich,`+`w przypadkach, gdy termin nie jest wyraźnie wymagane ( ).
3. Zamawia wynikowy zestaw hoteli według bliskości danej lokalizacji geograficznej, a następnie zwraca do aplikacji wywołującej. 

Większość tego artykułu dotyczy przetwarzania *zapytania wyszukiwania:* `"Spacious, air-condition* +\"Ocean view\""`. Filtrowanie i zamawianie są poza zakresem. Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Etap 1: Analizowanie zapytań 

Jak wspomniano, ciąg zapytania jest pierwszym wierszem żądania: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analizator zapytań oddziela operatory `*` (takie jak i `+` w przykładzie) od wyszukiwanych haseł i dekonstruuje kwerendę wyszukiwania na *podkwery* obsługiwanego typu: 

+ *zapytanie terminowe* dla terminów autonomicznych (takich jak przestronne)
+ *zapytanie fraz o* terminy (np. widok oceanu)
+ *kwerenda prefiksu* dla terminów, po których następuje operator prefiksu `*` (np. stan klimatyzacji)

Aby uzyskać pełną listę obsługiwanych typów kwerend, zobacz [Składnia kwerendy lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatory skojarzone z podkwerendą określają, czy kwerenda "musi być" lub "powinna być" spełniona, aby dokument został uznany za zgodny. Na przykład `+"Ocean view"` jest "musi" `+` ze względu na operatora. 

Analizator kwerend restrukturyzuje podkwery w *drzewie zapytań* (struktura wewnętrzna reprezentująca kwerendę), który przekazuje do wyszukiwarki. W pierwszym etapie analizowania kwerendy drzewo kwerend wygląda następująco.  

 ![Tryb wyszukiwania zapytania logicznego dowolny][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Obsługiwane analizatory: Prosty i pełny lucene 

 Usługa Azure Cognitive Search udostępnia dwa `simple` różne języki `full`zapytań (domyślnie) i . Ustawiając `queryType` parametr z żądaniem wyszukiwania, należy poinformować analizatora zapytań, który język kwerendy zostanie wybrany, aby wiedział, jak interpretować operatory i składnię. [Prosty język zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) jest intuicyjny i niezawodny, często nadaje się do interpretacji danych wejściowych użytkownika, jak jest bez przetwarzania po stronie klienta. Obsługuje operatorów zapytań znanych z wyszukiwarek internetowych. [Pełny język zapytania Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), który `queryType=full`można uzyskać przez ustawienie, rozszerza domyślny prosty język kwerendy, dodając obsługę większej liczby operatorów i typów zapytań, takich jak wieloznaczne, rozmyte, wyrażenia regularne i zapytania o zakresie pola. Na przykład wyrażenie regularne wysyłane w składni kwerendy prostej będzie interpretowane jako ciąg zapytania, a nie wyrażenie. Przykładowe żądanie w tym artykule używa pełnego języka kwerendy Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Wpływ searchMode na analizatora 

Innym parametrem żądania wyszukiwania, który `searchMode` wpływa na analizowanie jest parametr. Steruje domyślnym operatorem dla zapytań logicznych: dowolny (domyślny) lub wszystkie.  

Gdy `searchMode=any`, co jest ustawieniem domyślnym, ogranicznik przestrzeni`||`między przestronnym i klimatyzowanym jest OR ( ), dzięki czemu przykładowy tekst zapytania odpowiada: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Jawne operatory, takie jak `+` w `+"Ocean view"`, są jednoznaczne w konstrukcji zapytania logicznego (termin musi *być* zgodny). Mniej oczywiste jest, jak interpretować pozostałe terminy: przestronne i klimatyzowane. Czy wyszukiwarka powinna znaleźć dopasowania na widok na ocean *i* przestronne *i* klimatyzowane? Czy powinien znaleźć widok na ocean plus *jeden z* pozostałych terminów? 

Domyślnie`searchMode=any`( ), wyszukiwarka zakłada szerszą interpretację. Dopasowywanie obu *pól,* odzwierciedlając semantykę "lub" ( "lub" ("lub" semantyka. Początkowe drzewo kwerendy zilustrowane wcześniej, z dwoma operacjami "powinien", pokazuje wartość domyślną.  

Załóżmy, `searchMode=all`że teraz ustawiliśmy . W takim przypadku spacja jest interpretowana jako operacja "i". Każdy z pozostałych warunków musi być obecny w dokumencie, aby zakwalifikować się jako mecz. Wynikowe przykładowe zapytanie będzie interpretowane w następujący sposób: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Zmodyfikowane drzewo kwerend dla tej kwerendy będzie w następujący sposób, gdzie pasujący dokument jest przecięciem wszystkich trzech podkemieci: 

 ![Tryb wyszukiwania zapytań logicznych wszystkie][3]

> [!Note] 
> Wybór `searchMode=any` `searchMode=all` nad to decyzja najlepiej podjęta poprzez uruchamianie zapytań reprezentacyjnych. Użytkownicy, którzy mogą zawierać operatorów (typowe podczas wyszukiwania `searchMode=all` magazynów dokumentów) może znaleźć wyniki bardziej intuicyjne, jeśli informuje konstrukcje zapytań logicznych. Aby uzyskać więcej informacji `searchMode` na temat wzajemnego oddziaływania między operatorami i operatorami, zobacz [Prosta składnia kwerendy](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Etap 2: Analiza leksykalna 

Analizatory *leksykalne przetwarzają kwerendy terminowe* i zapytania fraz po ustrukturyzowanie drzewa zapytań. *phrase queries* Analizator akceptuje dane wejściowe tekstu podane do niego przez analizatora, przetwarza tekst, a następnie wysyła z powrotem tokenizowane terminy, które mają zostać włączone do drzewa zapytań. 

Najczęstszą formą analizy leksykalne jest *analiza językowa,* która przekształca terminy zapytania na podstawie reguł specyficznych dla danego języka: 

* Zmniejszanie terminu kwerendy do głównej formy wyrazu 
* Usuwanie innych niż istotne słowa (słowa stopwords, takie jak "" lub "i" w języku angielskim) 
* Podział wyrazu złożonego na części składowe 
* Dolna obudowa wielkie litery wyraz 

Wszystkie te operacje mają tendencję do usuwania różnic między wprowadzaniem tekstu dostarczonym przez użytkownika a terminami przechowywanymi w indeksie. Takie operacje wykraczają poza przetwarzanie tekstu i wymagają dogłębnej znajomości samego języka. Aby dodać tę warstwę świadomości językowej, usługa Azure Cognitive Search obsługuje długą listę [analizatorów języka](https://docs.microsoft.com/rest/api/searchservice/language-support) zarówno z Lucene, jak i microsoftu.

> [!Note]
> Wymagania dotyczące analizy mogą wahać się od minimalnego do opracowania w zależności od scenariusza. Złożoność analizy leksykalne można kontrolować, wybierając jeden ze wstępnie zdefiniowanych analizatorów lub tworząc własny [analizator niestandardowy.](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) Analizatory są ograniczone do pól z możliwością wyszukiwania i są określone jako część definicji pola. Pozwala to na zmianę analizy leksykalne w zależności od pola. Nieokreślony, *standardowy* analizator Lucene jest używany.

W naszym przykładzie przed analizą początkowe drzewo zapytań ma termin "Przestronne", z wielkimi literami "S" i przecinek, który analizator kwerendy interpretuje jako część terminu kwerendy (przecinek nie jest uważany za operator języka kwerendy).  

Gdy domyślny analizator przetwarza termin, będzie on opuszczał litery "widok oceanu" i "przestronny" i usuwał znak przecinka. Zmodyfikowane drzewo zapytań będzie wyglądać w następujący sposób: 

 ![Kwerenda logiczna z analizowanymi terminami][4]

### <a name="testing-analyzer-behaviors"></a>Testowanie zachowań analizatora 

Zachowanie analizatora można przetestować przy użyciu [interfejsu API analizy](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Podaj tekst, który chcesz przeanalizować, aby zobaczyć, jakie terminy będą generować analizator. Na przykład, aby zobaczyć, jak analizator standardowy będzie przetwarzać tekst "klimatyzacja", można wydać następujące żądanie:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardowy analizator dzieli tekst wejściowy na następujące dwa tokeny, donośnąc je atrybutami, takimi jak odsunięcia początkowe i końcowe (używane do wyróżniania trafień), a także ich położenie (używane do dopasowywania fraz):

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

### <a name="exceptions-to-lexical-analysis"></a>Wyjątki od analizy leksykalne 

Analiza leksykalne ma zastosowanie tylko do typów zapytań, które wymagają pełnych terminów — kwerendy terminowej lub kwerendy frazowej. Nie ma zastosowania do typów kwerend z niekompletnymi terminami — kwerendą prefiksu, kwerendą wieloznaczną, kwerendą wycięciową — ani do kwerendy rozmytej. Te typy zapytań, w `air-condition*` tym kwerendy prefiks z terminem w naszym przykładzie, są dodawane bezpośrednio do drzewa kwerend, z pominięciem etapu analizy. Jedyną transformacją wykonano na warunkach kwerendy tych typów jest małe skreślenie.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Etap 3: Pobieranie dokumentów 

Pobieranie dokumentu odnosi się do znajdowania dokumentów z pasującymi terminami w indeksie. Ten etap jest najlepiej rozumiany przez przykład. Zacznijmy od indeksu hoteli o następującym prostym schemacie: 

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

Ponadto załóżmy, że indeks ten zawiera następujące cztery dokumenty: 

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

**Jak są indeksowane terminy**

Aby zrozumieć pobieranie, pomaga poznać kilka podstaw dotyczących indeksowania. Jednostka magazynu jest odwróconym indeksem, po jednym dla każdego pola z wyszukujalnym. W indeksie odwróconym znajduje się posortowana lista wszystkich terminów ze wszystkich dokumentów. Każdy termin jest mapowana na listę dokumentów, w których występuje, co widać w poniższym przykładzie.

Aby utworzyć terminy w odwróconym indeksie, wyszukiwarka przeprowadza leksykalne analizy zawartości dokumentów, podobne do tego, co dzieje się podczas przetwarzania zapytań:

1. *Dane wejściowe tekstu* są przekazywane do analizatora, małe litery, pozbawione znaków interpunkcyjnych i tak dalej, w zależności od konfiguracji analizatora. 
2. *Tokeny* są dane wyjściowe analizy tekstu.
3. *Warunki* są dodawane do indeksu.

Jest to typowe, ale nie jest wymagane, aby użyć tych samych analizatorów do wyszukiwania i indeksowania operacji, tak aby terminy kwerendy wyglądają bardziej jak terminy wewnątrz indeksu.

> [!Note]
> Usługa Azure Cognitive Search umożliwia określenie różnych analizatorów do indeksowania i wyszukiwania za pomocą parametrów dodatkowych `indexAnalyzer` i `searchAnalyzer` pól. Jeśli nie określono, zestaw analizatora z właściwością `analyzer` jest używany do indeksowania i wyszukiwania.  

**Odwrócony indeks dla przykładów dokumentów**

Wracając do naszego przykładu, dla pola **tytułu** odwrócony indeks wygląda następująco:

| Termin | Lista dokumentów |
|------|---------------|
| Atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| Ocean | 4  |
| Playa | 3 |
| Resort | 3 |
| Retreat | 4 |

W polu tytułowym tylko *hotel* pojawia się w dwóch dokumentach: 1, 3.

W polu **opisu** indeks jest następujący:

| Termin | Lista dokumentów |
|------|---------------|
| Powietrza | 3
| i | 4
| Beach | 1
| Uwarunkowane | 3
| Komfortowe | 3
| odległość | 1
| Wyspa | 2
| kauaʻi ( kauaʻi ) | 2
| Znajdujące się | 2
| north | 2
| Ocean | 1, 2, 3
| z | 2
| on |2
| cichy | 4
| Pokoje  | 1, 3
| Zaciszne | 4
| Brzegu | 2
| Przestronne | 1
| względem zasobu | 1, 2
| na | 1
| widok | 1, 2, 3
| Chodzenie | 1
| with | 3


**Dopasowywanie terminów kwerendy do warunków indeksowanych**

Biorąc pod uwagę odwrócone indeksy powyżej, wróćmy do przykładowej kwerendy i zobaczmy, jak pasujące dokumenty znajdują się dla naszej przykładowej kwerendy. Przypomnijmy, że końcowe drzewo kwerend wygląda następująco: 

 ![Kwerenda logiczna z analizowanymi terminami][4]

Podczas wykonywania kwerendy poszczególne zapytania są wykonywane względem pól z możliwością wyszukiwania niezależnie. 

+ TermQuery, "przestronne", pasuje do dokumentu 1 (Hotel Atman). 

+ PrefixQuery, "klimatyzacja*", nie pasuje do żadnych dokumentów. 

  Jest to zachowanie, które czasami myli deweloperów. Chociaż termin klimatyzowany istnieje w dokumencie, jest on podzielony na dwa terminy przez analizatora domyślnego. Przypomnijmy, że kwerendy prefiksu, które zawierają terminy częściowe, nie są analizowane. W związku z tym terminy z prefiksem "klimatyzacja" są wyszukane w indeksie odwróconym i nie znaleziono.

+ PhraseQuery, "widok na ocean", sprawdza terminy "ocean" i "widok" i sprawdza bliskość terminów w oryginalnym dokumencie. Dokumenty 1, 2 i 3 są zgodne z tą kwerendą w polu opisu. Zwróć uwagę, że dokument 4 ma termin ocean w tytule, ale nie jest uważany za pasujący, ponieważ szukamy frazy "widok oceanu", a nie pojedynczych słów. 

> [!Note]
> Kwerenda wyszukiwania jest wykonywana niezależnie od wszystkich pól z możliwością wyszukiwania w `searchFields` indeksie usługi Azure Cognitive Search, chyba że ograniczysz pola ustawione za pomocą parametru, jak pokazano w przykładowym żądaniu wyszukiwania. Zwracane są dokumenty zgodne z dowolnym z wybranych pól. 

Ogólnie rzecz biorąc, dla danego zapytania, dokumenty, które pasują do 1, 2, 3. 

## <a name="stage-4-scoring"></a>Etap 4: Punktacja  

Każdemu dokumentowi w zestawie wyników wyszukiwania jest przypisywany wynik trafności. Funkcja miary jest ranga wyższe te dokumenty, które najlepiej odpowiedzieć na pytanie użytkownika wyrażone przez zapytanie wyszukiwania. Wynik jest obliczany na podstawie właściwości statystycznych terminów, które są dopasowane. Sednem formuły punktacji jest [TF/IDF (term frequency-verse document frequency-verse).](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) W zapytaniach zawierających rzadkie i powszechne terminy TF/IDF promuje wyniki zawierające rzadki termin. Na przykład, w hipotetycznym indeksie ze wszystkimi artykułami Wikipedii, z dokumentów, które pasowały do zapytania *prezydenta*, dokumenty pasujące do *prezydenta* są uważane za bardziej istotne niż dokumenty pasujące *do*.


### <a name="scoring-example"></a>Przykład punktacji

Przypomnij sobie trzy dokumenty, które pasowały do naszej przykładowej kwerendy:
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

Dokument 1 najlepiej pasował do zapytania, ponieważ w polu opisu występuje zarówno termin *przestronny,* jak i wymagany *widok oceanu* frazy. Następne dwa dokumenty pasują tylko do wyrażenia *widok oceanu*. Może to być zaskakujące, że wynik trafności dla dokumentu 2 i 3 jest inny, mimo że dopasowali zapytanie w ten sam sposób. To dlatego, że formuła punktacji ma więcej składników niż tylko TF / IDF. W tym przypadku dokumentowi 3 przypisano nieco wyższy wynik, ponieważ jego opis jest krótszy. Dowiedz się więcej o [lucene's Practical Scoring Formula,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) aby zrozumieć, w jaki sposób długość pola i inne czynniki mogą wpływać na wynik trafności.

Niektóre typy zapytań (symbol wieloznaczny, prefiks, wyrażenie regularne) zawsze przyczyniają się do stałego wyniku do ogólnego wyniku dokumentu. Dzięki temu dopasowania znalezione za pośrednictwem rozszerzenia kwerendy mają być uwzględnione w wynikach, ale bez wpływu na ranking. 

Przykład pokazuje, dlaczego to ma znaczenie. Wyszukiwanie symboli wieloznacznych, w tym wyszukiwania prefiksów, są niejednoznaczne z definicji, ponieważ dane wejściowe są ciągiem częściowym z potencjalnymi dopasowaniami na bardzo dużą liczbę różnych terminów (rozważ wprowadzenie "tour*", z dopasowaniami znalezionymi na "tours", "tourettes" i " tourmaline"). Biorąc pod uwagę charakter tych wyników, nie ma sposobu, aby racjonalnie wywnioskować, które terminy są cenniejsze niż inne. Z tego powodu ignorujemy częstotliwości terminów podczas oceniania wyników w kwerendach typów symboli wieloznacznych, prefiksów i wyrażenia regularnego. W wieloczęściowym żądaniu wyszukiwania, które zawiera częściowe i kompletne terminy, wyniki z częściowego wprowadzania są włączane ze stałym wynikiem, aby uniknąć stronniczości w stosunku do potencjalnie nieoczekiwanych dopasowań.

### <a name="score-tuning"></a>Strojenie wyników

Istnieją dwa sposoby dostrojenia wyników trafności w usłudze Azure Cognitive Search:

1. **Profile oceniania** promują dokumenty na liście rankingowej wyników na podstawie zestawu reguł. W naszym przykładzie możemy uznać dokumenty, które są dopasowane w polu tytułu, są bardziej istotne niż dokumenty dopasowane w polu opisu. Dodatkowo, jeśli nasz indeks miał pole cenowe dla każdego hotelu, moglibyśmy promować dokumenty z niższą ceną. Dowiedz się więcej, jak [dodać profile oceniania do indeksu wyszukiwania.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Zwiększenie terminów** (dostępne tylko w składni pełnej kwerendy lucene) zapewnia zwiększenie operatora, `^` który można zastosować do dowolnej części drzewa kwerend. W naszym przykładzie zamiast szukać *stanu*\*stanu prefiksu, można wyszukać dokładny termin *klimatyzacja* lub prefiks, ale dokumenty pasujące do dokładnego terminu są uszeregowane wyżej, stosując impuls do terminu zapytanie: *klimatyzacja^2|| klimatyzacji**. Dowiedz się więcej o [zwiększaniu terminów](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Punktacja w indeksie rozproszonym

Wszystkie indeksy w usłudze Azure Cognitive Search są automatycznie dzielone na wiele fragmentów, co pozwala nam szybko dystrybuować indeks między wieloma węzłami podczas skalowania usługi w górę lub w dół. Po wystawieniu żądania wyszukiwania jest wydawane dla każdego niezależnego fragmentu. Wyniki z każdego niezależnego fragmentu są następnie scalane i uporządkowane według wyniku (jeśli nie zdefiniowano żadnej innej kolejności). Ważne jest, aby wiedzieć, że funkcja oceniania wagi zapytania częstotliwości terminu względem jego odwrotnej częstotliwości dokumentu we wszystkich dokumentach w obrębie niezależnego fragmentu, a nie we wszystkich fragmentach!

Oznacza to, że wynik trafności *może* być inny dla identycznych dokumentów, jeśli znajdują się one na różnych fragmentów. Na szczęście takie różnice zwykle znikają, ponieważ liczba dokumentów w indeksie rośnie z powodu bardziej równomiernego rozkładu terminów. Nie można założyć, na którym niezależniuszku zostanie umieszczony dowolny dany dokument. Jednak przy założeniu, że klucz dokumentu nie ulegnie zmianie, zawsze zostanie przypisany do tego samego fragmentu.

Ogólnie rzecz biorąc wynik dokumentu nie jest najlepszym atrybutem do zamawiania dokumentów, jeśli stabilność zamówienia jest ważna. Na przykład, biorąc pod uwagę dwa dokumenty o identycznym wyniku, nie ma żadnej gwarancji, który pojawia się najpierw w kolejnych uruchomieniach tego samego zapytania. Wynik dokumentu powinien jedynie dawać ogólne poczucie znaczenia dokumentu w stosunku do innych dokumentów w zestawie wyników.

## <a name="conclusion"></a>Podsumowanie

Sukces wyszukiwarek internetowych wzbudził oczekiwania dotyczące pełnego wyszukiwania tekstu w stosunku do prywatnych danych. W przypadku niemal każdego rodzaju wyszukiwania oczekujemy, że silnik zrozumie nasze intencje, nawet jeśli terminy są błędnie napisane lub niekompletne. Możemy nawet spodziewać się meczów opartych na prawie równoważnych warunkach lub synonimach, których nigdy nie określiliśmy.

Z technicznego punktu widzenia wyszukiwanie pełnotekstowe jest bardzo złożone, co wymaga zaawansowanej analizy językowej i systematycznego podejścia do przetwarzania w sposób, który destyluje, rozszerza i przekształca terminy zapytań, aby uzyskać odpowiedni wynik. Biorąc pod uwagę nieodłączne zawiłości, istnieje wiele czynników, które mogą mieć wpływ na wynik kwerendy. Z tego powodu inwestowanie czasu na zrozumienie mechaniki wyszukiwania pełnotekstowego oferuje wymierne korzyści podczas próby pracy przez nieoczekiwane wyniki.  

W tym artykule zbadano wyszukiwanie pełnotekstowe w kontekście usługi Azure Cognitive Search. Mamy nadzieję, że daje wystarczające tło do rozpoznawania potencjalnych przyczyn i rozwiązań do rozwiązywania typowych problemów z zapytaniami. 

## <a name="next-steps"></a>Następne kroki

+ Skompiluj przykładowy indeks, wypróbuj różne zapytania i przejrzyj wyniki. Aby uzyskać instrukcje, zobacz [Tworzenie i kwerenda indeksu w portalu](search-get-started-portal.md#query-index).

+ Wypróbuj dodatkową składnię kwerendy w sekcji [Przykładowe dokumenty wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) lub w [sekcji Proste zapytanie](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Przejrzyj [profile oceniania,](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) jeśli chcesz dostroić ranking w aplikacji wyszukiwania.

+ Dowiedz się, jak stosować [analizatory leksykalne specyficzne dla języka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Skonfiguruj analizatory niestandardowe](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) do minimalnego przetwarzania lub specjalistycznego przetwarzania w określonych dziedzinach.

## <a name="see-also"></a>Zobacz też

[Interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Obsługa wyników wyszukiwania](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
