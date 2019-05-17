---
title: Architektura aparatu (Lucene) wyszukiwania pełnotekstowego — usługa Azure Search
description: Wyjaśnienie Lucene i przetwarzania dokumentów pobierania kwestie dotyczące zapytań w celu wyszukiwania pełnotekstowego, w odniesieniu do usługi Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: bc183cb8ac2155b8dd31dc603d70506ad3d5e20a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797484"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search

Ten artykuł jest przeznaczony dla deweloperów, którzy potrzebują lepiej zrozumieć, jak działa wyszukiwanie pełnotekstowe Lucene w usłudze Azure Search. Tekst zapytania usługi Azure Search w większości przypadków będzie dostarczać bezproblemowo oczekiwanych wyników, ale niekiedy może otrzymać wynik, który wydaje się jakiś sposób "off". W takich sytuacjach mających w tle w czterech etapach wykonywania zapytań Lucene (zapytanie analizy, leksykalne analizy, dokumentów, dopasowanie, oceniania) może pomóc w zidentyfikowaniu określonej zmiany parametrów zapytania ani konfigurację indeksu, które będzie dostarczać żądaną wynik. 

> [!Note] 
> Usługa Azure Search używa Lucene w celu wyszukiwania pełnotekstowego, ale nie jest wyczerpująca integracji Lucene. Firma Microsoft selektywnie udostępnianie i rozszerzania funkcji Lucene scenariuszy, które są istotne dla usługi Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Omówienie architektury i diagramów

Przetwarzanie zapytania wyszukiwania pełnotekstowego zaczyna się od analizy tekstu zapytania, aby wyodrębnić wyszukiwane terminy. Aparat wyszukiwania używa indeksu do pobierania dokumentów za pomocą pasujące terminy. Warunki poszczególnych zapytań są czasami podziale i odtworzenia w formularzach nowy rzutowanie szersze net przez co może być uznana za możliwe dopasowanie. Zestaw wyników jest następnie są sortowane według istotności, przypisane do oddzielnych pasujących dokumentów. Te, w górnej części listy rangi są zwracane do aplikacji wywołującej.

Powinny zostać przekształcone, wykonanie kwerendy się w czterech etapach: 

1. Zapytanie analizy 
2. Poddawać analizie leksykalnej 
3. Pobieranie dokumentu 
4. Ocenianie 

Na poniższym diagramie przedstawiono składniki używane do przetwarzania żądań wyszukiwania. 

 ![Diagram architektury zapytań Lucene w usłudze Azure Search][1]


| Główne składniki | Opis funkcjonalności | 
|----------------|------------------------|
|**Analizatory składni zapytania** | Oddzielaj terminy kwerendy od operatorów zapytań i Utwórz strukturę zapytania (Drzewo zapytań) do wysłania do aparatu wyszukiwania. |
|**Analizatory** | Analizowanie leksykalne terminów zapytania. Ten proces może obejmować przekształcania, usunięcie lub rozszerzania terminów zapytania. |
|**Index** | Struktura danych wydajne umożliwia przechowywanie i organizowanie można wyszukiwać terminy wyodrębnione z indeksowanych dokumentów. |
|**Aparat wyszukiwania** | Pobiera i wyników pasujących dokumentów na podstawie zawartości odwróconą indeksu. |

## <a name="anatomy-of-a-search-request"></a>Anatomia żądania wyszukiwania

Żądania wyszukiwania jest pełną specyfikację ma zostać zwrócone w zestawie wyników. W najprostszej postaci jest pustego zapytania przy użyciu kryteriów wszelkiego rodzaju. Przykład bardziej realistycznego zawiera parametry, kilka terminów zapytania, może być ograniczone do niektórych pól, prawdopodobnie wyrażenie filtru i kolejność reguł.  

Poniższy przykład przedstawia żądanie wyszukiwania można wysłać do usługi Azure Search przy użyciu [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

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

Dla tego żądania aparatu wyszukiwania wykonuje następujące czynności:

1. Filtry na poziomie dokumentów, których cena jest co najmniej 60 USD i mniej niż 300 USD.
2. Wykonuje zapytanie. W tym przykładzie zapytanie wyszukiwania składa się z wyrażeń i warunków: `"Spacious, air-condition* +\"Ocean view\""` (użytkownicy zwykle nie należy wprowadzić znak interpunkcyjny, ale go w tym w przykładzie pozwala nam wyjaśniają, jak analizatory go obsłużyć). Dla tego zapytania, aparat wyszukiwania skanuje opis i tytuł pola określone w `searchFields` dla dokumentów zawierających "Ocean view" i również termin "obszerne" lub na zasadach, które rozpoczynają się prefiksem "air-condition". `searchMode` Parametr jest używany do dopasowania żadnych warunków (ustawienie domyślne) lub wszystkie z nich w przypadkach, w których nie jest wyraźnie wymagane termin (`+`).
3. Zamówień, wynikowy zestaw hotele, odległości między elementami w danej lokalizacji geograficznej lokalizacji, a następnie wróciło, aby aplikacja wywołująca. 

Większość w tym artykule dotyczy przetwarzania *zapytania wyszukiwania*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrowanie i kolejność znajdą się poza zakresem. Aby uzyskać więcej informacji, zobacz [dokumentację referencyjną interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Etap 1: Zapytanie analizy 

Jak wspomniano, ciąg zapytania jest pierwszy wiersz żądania: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Analizator składni zapytań oddziela operatorów (takich jak `*` i `+` w przykładzie) z wyszukiwania warunki i deconstructs zapytania wyszukiwania do *podzapytań* obsługiwanego typu: 

+ *Zapytanie termin* autonomiczny warunków (np. obszerne)
+ *wyrażenie zapytania* cudzysłowie warunków (np. widok ocean)
+ *Prefiks zapytania* warunków następuje operatorem prefiksowym `*` (np. air-condition)

Aby uzyskać pełną listę obsługiwane typy zapytania zobacz [składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatory skojarzone z podzapytania ustalić, czy zapytanie "musi być" lub "powinien być" spełnione w kolejności dokumentu zostały uznane za dopasowanie. Na przykład `+"Ocean view"` jest "musisz" ze względu na `+` operatora. 

Analizator składni zapytań restrukturyzuje podzapytania w *Drzewo zapytań* (wewnętrznej struktury reprezentujący zapytanie) przekazuje on do aparatu wyszukiwania. W pierwszym etapie zapytania analizy Drzewo zapytań wygląda następująco.  

 ![Atrybut typu wartość logiczna wszelkie zapytania searchmode][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Obsługiwane analizatory składni: Proste i pełne Lucene 

 Usługa Azure Search udostępnia dwa języki inne zapytanie, `simple` (ustawienie domyślne) i `full`. Ustawiając `queryType` parametru do żądania wyszukiwania, wskazujemy analizator składni zapytań języka zapytań, które wybierzesz tak aby wie, jak interpretować, operatorów i składni. [Prostego zapytania języka](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) jest intuicyjna i niezawodne, często odpowiednie interpretowanie danych wprowadzonych przez użytkownika jako — bez przetwarzania po stronie klienta. Obsługuje ona operatorów zapytań, które dobrze znanych z aparatów wyszukiwania w sieci web. [Język zapytań Lucene pełne](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), którego można uzyskać przez ustawienie `queryType=full`, rozszerza język proste zapytanie domyślne przez dodanie obsługi więcej operatorów i typów zapytań, takich jak symbole wieloznaczne, rozmytego, wyrażeń regularnych i zapytania należące do pola. Na przykład wyrażenie regularne wysyłane prosta składnia zapytań może być interpretowany jako ciąg zapytania, a nie wyrażenia. Przykładowe żądanie, w tym artykule używa języka zapytań Lucene pełne.

### <a name="impact-of-searchmode-on-the-parser"></a>Wpływ searchMode parsera 

Jest inny parametr żądania wyszukiwania, który wpływa na analizowanie `searchMode` parametru. Kontroluje, operator domyślny dla kwerendy logiczne: wszelkie (ustawienie domyślne) lub wszystkie.  

Podczas `searchMode=any`, który jest domyślnym ogranicznikiem miejsca między obszerne i air-condition lub (`||`), co odpowiednikiem przykładowy tekst zapytania: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Jawne operatory, takie jak `+` w `+"Ocean view"`, są jednoznaczne konstrukcji logicznych zapytania (termin *musi* zgodne). Mniej oczywistych jest jak interpretować pozostałe postanowienia: obszerne i air-condition. Należy aparatu wyszukiwania Znajdź dopasowania w widoku ocean *i* obszerne *i* air-condition? Lub należy znaleźć widoku ocean oraz *pojedynczo* pozostałych warunków? 

Domyślnie (`searchMode=any`), aparat wyszukiwania zakłada szersze interpretacji. Albo pole *powinien* można dopasować, odzwierciedlający semantykę "or". Drzewo początkowego zapytania przedstawione wcześniej, za pomocą dwóch "powinien" operacje, przedstawiono domyślne.  

Załóżmy, że teraz ustawimy `searchMode=all`. W tym przypadku przestrzeń jest interpretowany jako operację "i". Każdego z pozostałych warunki muszą być obecne w dokumencie, aby zakwalifikować się jako zgodny. Wynikowe zapytanie przykładowe może być interpretowany w następujący sposób: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Drzewo zmodyfikowane zapytanie dla tego zapytania będą następująco, gdzie pasujących dokumentów jest część wspólną wszystkich trzech podzapytaniach: 

 ![Wszystkie searchmode Zapytanie logiczne][3]

> [!Note] 
> Wybieranie `searchMode=any` za pośrednictwem `searchMode=all` jest najlepszych decyzji dotarły za pomocą uruchamiania zapytań językiem. Użytkownicy, którzy mogą zawierać operatorów (wspólne, jeśli przechowuje wyszukiwanie dokumentów) stało się wyniki bardziej intuicyjne `searchMode=all` informuje konstrukcji logicznych zapytania. Aby uzyskać więcej informacji na temat biurowymi między `searchMode` i operatorów, zobacz [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Etap 2: Poddawać analizie leksykalnej 

Proces analizatory leksykalne *termin zapytania* i *frazę zapytania* po mają strukturę drzewa zapytań. Analizator akceptuje dane wejściowe tekstu udzielone przez analizator, przetwarza tekst, a następnie wysyła ponownie podzielić na tokeny warunki, należy włączyć do drzewa zapytań. 

Jest to najczęściej używany typ poddawać analizie leksykalnej *analizy językowej* które przekształcenia zapytania warunków na podstawie reguł określonych dla danego języka: 

* Zmniejszenie warunek kwerendy do formularza głównego programu word 
* Usuwanie ważnymi wyrazów (STOP-słowa, takie jak "" lub "i" w języku angielskim) 
* Podzielenie wyraz złożony na części składowe 
* Małe wielkość liter wyraz wielką 

Wszystkie te operacje mają tendencję do wymazania różnice między wprowadzanie tekstu, dostarczone przez użytkownika wraz ze wszystkimi postanowieniami zapisane w indeksie. Operacje takie wykracza poza przetwarzanie tekstu i wymaga dogłębnej znajomości sam język. Aby dodać tej warstwy językowej świadomości, usługa Azure Search obsługuje długą listę [analizatory języka](https://docs.microsoft.com/rest/api/searchservice/language-support) zarówno Lucene, jak i firmy Microsoft.

> [!Note]
> Wymagania dotyczące analizy do zakresu od minimalnej do rozbudowane w zależności od danego scenariusza. Złożoność poddawać analizie leksykalnej można kontrolować, wybierając jeden z wstępnie zdefiniowanych analizatory lub tworząc własne [analizatora niestandardowego](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analizatory są ograniczone do pola z możliwością wyszukiwania i są określone jako część definicji pola. Dzięki temu można będzie się różnić w analizie leksykalnej na poszczególnych pól. Nie określono tego parametru, *standardowa* służy analizator Lucene.

W naszym przykładzie przed analiza drzewo początkowego zapytania ma termin "Spacious" z wielkimi literami "S" i przecinek, analizator składni zapytań interpretuje jako część wyszukiwanego terminu, (przecinkami nie jest uważany za operator języka zapytań).  

Kiedy analizatora domyślne przetwarza termin, zostanie małe litery "ocean view" i "obszerne" i Usuń znak przecinka. W drzewie zmodyfikowane zapytanie będzie wyglądać w następujący sposób: 

 ![Wartość logiczna zapytania z warunkami analizy][4]

### <a name="testing-analyzer-behaviors"></a>Testowanie zachowania analizatora 

Zachowanie analizator mogą być testowane przy użyciu [analizowanie API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Podaj tekst, który chcesz analizować, aby zobaczyć, co spowoduje wygenerowanie pojęcia analizatora. Na przykład aby zobaczyć, jak standardowy analizator może przetwarzać tekst "air-condition", użytkownik może wydać następujące żądanie:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardowy analizator dzieli tekst wejściowy na następujące dwa tokeny, dodawanie adnotacji do nich za pomocą atrybutów, takich jak rozpoczęcia i zakończenia przesunięcia (używane w celu wyróżnienia trafień), a także ich pozycji (użyty do dopasowania frazy):

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

### <a name="exceptions-to-lexical-analysis"></a>Wyjątki, aby poddawać analizie leksykalnej 

Poddawać analizie leksykalnej dotyczy tylko typów zapytania, które wymagają pełne warunki — zapytania termin lub frazę zapytania. Nie ma zastosowania do typów zapytań z warunkami niekompletne — prefiks zapytania, zapytanie symboli wieloznacznych, wyrażenie regularne zapytania — lub rozmyte zapytania. Zapytania te typy, takie jak zapytanie prefiks z terminem `air-condition*` w naszym przykładzie są dodawane bezpośrednio do drzewa zapytania, z pominięciem na etapie analizy. West jest tylko przekształcenie wykonywane na terminów zapytania z tych typów.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Etap 3: Pobieranie dokumentu 

Pobieranie dokumentu odnosi się do znajdowania dokumentów za pomocą pasujące terminy w indeksie. Ten etap rozumie najlepiej przykładu. Zacznijmy od indeksu hotels, masz następujące prosty schemat: 

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

Dodatkowo założono, że ten indeks zawiera cztery następujące dokumenty: 

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

**Jak warunki są indeksowane**

Aby zrozumieć, pobierania, warto wiedzieć kilka podstawowych informacji o indeksowaniu. Jednostki magazynu jest odwróconą indeks, jeden dla każdego pola, którą można przeszukiwać. W ciągu odwróconym indeksie jest posortowaną listę wszystkich warunków umowy ze wszystkich dokumentów. Każdy termin mapuje do listy dokumentów, w których występuje, oczywiste, jak w poniższym przykładzie.

Aby utworzyć warunki odwróconą indeksu, aparat wyszukiwania wykonuje poddawać analizie leksykalnej zawartość dokumentów, podobnie jak co się dzieje podczas przetwarzania zapytań:

1. *Dane wejściowe tekstu* są przekazywane do analizatora dolnym — z uwzględnieniem wielkości liter, usunięte znaki interpunkcyjne i tak dalej, w zależności od konfiguracji analizatora. 
2. *Tokeny* są dane wyjściowe analizy tekstu.
3. *Warunki* zostaną dodane do indeksu.

Jest wspólnego, ale nie jest to wymagane, na potrzeby tej samej analizatory wyszukiwanie i indeksowanie operacje tak, aby terminów zapytania wyglądała bardziej jak warunki w indeksie.

> [!Note]
> Usługa Azure Search pozwala określić różne analizatory do indeksowania i wyszukiwania za pomocą dodatkowych `indexAnalyzer` i `searchAnalyzer` pola parametrów. Jeśli nie zostanie podany, analizator ustawiany `analyzer` właściwość jest używana do indeksowania i wyszukiwania.  

**Odwrócony indeksu dla dokumentów przykład**

Zwracanie do naszego przykładu dla **tytuł** pola, indeksu odwróconą wygląda w następujący sposób:

| Termin | Lista dokumentów |
|------|---------------|
| atman | 1 |
| Beach | 2 |
| hotel | 1, 3 |
| Ocean | 4  |
| playa | 3 |
| resort | 3 |
| Retreat | 4 |

W polu tytułu tylko *hotelu* pojawia się w dwóch dokumentów: 1, 3.

Aby uzyskać **opis** pola, indeksu jest następująca:

| Termin | Lista dokumentów |
|------|---------------|
| AIR | 3
| oraz | 4
| Beach | 1
| należy przygotować | 3
| comfortable | 3
| odległość | 1
| Wyspa | 2
| kauaʻi | 2
| znajduje się | 2
| Północna | 2
| Ocean | 1, 2, 3
| z | 2
| włączone |2
| quiet | 4
| pokoje  | 1, 3
| secluded | 4
| lądzie | 2
| obszerne | 1
| W | 1, 2
| do | 1
| wyświetl | 1, 2, 3
| zalet | 1
| z | 3


**Pasujące terminy zapytania względem indeksowanych warunki**

Biorąc pod uwagę odwróconą wskaźników powyższego, umożliwia powrót do przykładowego zapytania i zobacz jak pasujących dokumentów znajdują się na nasze przykładowe zapytanie. Pamiętaj, że drzewa ostatecznemu zapytaniu wygląda następująco: 

 ![Wartość logiczna zapytania z warunkami analizy][4]

Podczas wykonywania zapytania pojedynczych zapytań są wykonywane względem pola z możliwością wyszukiwania niezależnie. 

+ Dopasowuje "obszerne" TermQuery, dokumentu 1 (hotelu Atman). 

+ PrefixQuery "air-condition *", nie jest zgodna wszystkich dokumentów. 

  Jest to zachowanie, które czasami confuses deweloperów. Mimo że termin air-conditioned istnieje w dokumencie, to zostanie ona podzielona na dwa wyrazy przez analizatora domyślne. Odwołaj zapytania prefiksu, zawierające warunki częściowe nie są analizowane. W związku z tym warunków z prefiksem "air-condition" są wyszukiwane w odwróconym indeksie i nie można odnaleźć.

+ PhraseQuery, "ocean view", wyszukuje warunki "ocean" i "view" i sprawdza bliskość warunków w oryginalnym dokumencie. Dokumenty, 1, 2 i 3 pasujących do tego zapytania w polu opisu. Zwróć uwagę, dokument 4 ma ocean termin w tytule, ale nie jest uznawane za zgodne, ponieważ szukamy frazę "ocean view", a nie poszczególnych wyrazów. 

> [!Note]
> Zapytanie wyszukiwania jest wykonywane niezależnie względem wszystkie pola z możliwością wyszukiwania w indeksie usługi Azure Search, jeśli nie ograniczysz pola zestawu przy użyciu `searchFields` parametru, jak pokazano w przykładzie żądania wyszukiwania. Dokumenty, które odpowiadają w dowolnym z wybranych pól są zwracane. 

Dla całego dla danego zapytania, dokumenty, które odpowiadają są 1, 2, 3. 

## <a name="stage-4-scoring"></a>Etap 4: Ocenianie  

Każdy dokument w zestawie wyników wyszukiwania jest przypisywany wskaźnikiem istotności. Funkcja istotności jest wyższy stopień tych dokumentów, które najlepiej odpowiadają pytanie użytkownika, wyrażonych w zapytaniu wyszukiwania. Wynik jest obliczana w zależności od właściwości statystyczne warunki pasujących do. Oceny formuły jest [TF/IDF (częstotliwość dokumentu odwrotność częstotliwość termin)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). Zapytania zawierające rzadkich i typowe warunki TF/IDF promuje wyniki zawierające termin rzadkie. Na przykład w indeks hipotetyczny z wszystkie artykuły Wikipedia z dokumentów wynik kwerendy *Prezes*, dokumentów na *prezesa* są uznawane za istotne więcej niż dokumenty *na*.


### <a name="scoring-example"></a>Przykład oceniania

Odwołaj trzy dokumenty, pasujących do nasze przykładowe zapytanie:
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

Dokument 1 dopasować najlepiej zapytania, ponieważ oba termin *obszerne* i wymagane zwrot *widoku ocean* występują w polu opisu. Następne dwa dokumenty pasują tylko frazy *widoku ocean*. Może być Zaskakujące istotności dla dokumentu, 2 i 3 jest inny, nawet jeśli ich wynik kwerendy w taki sam sposób. Jest to spowodowane oceniania formuła zawiera więcej elementów niż tylko TF/IDF. W tym przypadku dokument 3 zostało przydzielone nieco więcej punktów, ponieważ jej opis jest krótszy. Dowiedz się więcej o [firmy Lucene praktyczne oceniania formuły](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) Aby zrozumieć, jak pola długości i inne czynniki mogą mieć wpływ na istotności.

Niektóre zapytania typów (symbol wieloznaczny, prefiksu, wyrażenie regularne) zawsze współtworzyć stałej wynik ogólny wynik dokumentu. Dzięki temu dopasowań za pośrednictwem rozszerzenia zapytania mają zostać uwzględnione w wynikach, ale bez wpływu na kolejność. 

Przykład pokazuje, dlaczego ta ma znaczenie. Wyszukiwania symboli wieloznacznych, prefiks wyszukiwań, w tym są niejednoznaczne, zgodnie z definicją w przypadku, ponieważ dane wejściowe są częściowe ciągu za pomocą potencjalnych dopasowań w bardzo dużej liczby różnych warunków (dane wejściowe "samouczek *", należy wziąć pod uwagę, za pomocą dopasowań na "przewodniki", "tourettes", i " tourmaline"). Ze względu na charakter tych wyników, nie ma możliwości rozsądnie wywnioskowania warunki, które są bardziej przydatne niż inne. Z tego powodu firma Microsoft zignorować określenie częstotliwości oceny wyników w kwerendach typów symboli wieloznacznych, prefiksu i wyrażeń regularnych. W żądaniu wyszukiwania wieloczęściowy, zawierający warunki częściowymi lub całkowitymi wyniki częściowe dane wejściowe są włączone z wynikiem stałej, aby uniknąć odchylenie kierunku potencjalnie nieoczekiwany dopasowań.

### <a name="score-tuning"></a>Dostosowywanie oceny

Istnieją dwa sposoby na dostosowywanie oceny istotności w usłudze Azure Search:

1. **Profile oceniania** promowania dokumentów na liście rangi wyników na podstawie zestawu reguł. W tym przykładzie firma Microsoft może wziąć pod uwagę dokumentów, które są dopasowywane w polu tytułu większe znaczenie niż dokumenty, które są dopasowywane w polu opisu. Ponadto jeśli naszym indeksie były pola Cena dla każdego hotelu, możemy wspierać dokumentów za pomocą niższej cenie. Dowiedz się, jak więcej [dodać profile oceniania do indeksu wyszukiwania.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Promowanie** (dostępne tylko w składni zapytań Lucene pełne) zapewnia zwiększenie operator `^` , który można zastosować do dowolnej części drzewa zapytań. W naszym przykładzie zamiast wyszukiwania na prefiksie *air-condition*\*, jeden może wyszukiwać albo dokładne określenie *air-condition* lub prefiksu, ale dokumentów, które odpowiadają na dokładne określenie wyżej, stosując boost zapytania termin: * warunek air ^ 2 || AIR-Condition **. Dowiedz się więcej o [promowanie](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Ocenianie w rozproszonej indeksu

Wszystkie indeksy w usłudze Azure Search automatycznie są podzielone na wiele fragmentów, dzięki czemu nam na szybkie dystrybuowanie indeksu między wieloma węzłami podczas skalowania usługi w górę lub w dół. Podczas generowania żądania wyszukiwania wystawiony dla każdego fragmentu niezależnie. Wyniki z każdego fragmentu są następnie scalić i uporządkowane według wyników (jeśli inne kolejność nie jest określona). Ważne jest, aby dowiedzieć się, czy oceniania wagi funkcji wysyłać zapytania o częstotliwości termin względem jego częstotliwość odwrotnej dokumentu w wszystkie dokumenty w fragmencie, nie między wszystkimi fragmentami!

Oznacza to, że wskaźnikiem istotności *można* różnić się identyczne dokumentów, jeśli są one przechowywane w różnych fragmentach. Na szczęście takie różnice zwykle znikną ze względu na więcej nawet termin dystrybucji wzrostem liczby dokumentów w indeksie. Nie jest możliwe założył, na który fragment zostaną umieszczone danego dokumentu. Jednak przy założeniu, że klucz dokumentu nie powoduje zmiany, zostanie zawsze do niej przypisany do tego samego fragmentu.

Ogólnie rzecz biorąc wynik dokumentu nie jest najlepszym atrybutu porządkowania dokumentów, jeśli stabilność kolejność jest ważna. Na przykład biorąc pod uwagę dwa dokumenty z oceną identyczne, nie ma żadnej gwarancji, która z nich znajduje się w kolejnych uruchomień tego samego zapytania. Wynik dokumentu należy nadać ogólnym sensie istotne dokumentu względem innych dokumentów w zestawie wyników.

## <a name="conclusion"></a>Podsumowanie

Powodzenie aparaty wyszukiwania w Internecie ma zgłoszonego oczekiwania dotyczące wyszukiwania pełnotekstowego dla prywatnych danych. Dla prawie każdy rodzaj wyszukiwania teraz oczekujemy, że aparat zrozumienie naszym zamiarem, nawet wtedy, gdy warunki są błędnie lub niekompletne. Może nawet oczekujemy, że niemal równoważnych terminów lub synonimów, które nigdy określone na podstawie dopasowań.

Z technicznego punktu widzenia wyszukiwanie pełnotekstowe jest bardzo złożone, zaawansowane analizy językowej i z systematycznego podejścia do przetwarzania w sposób przetwarzania, rozwiń węzeł i Przekształć terminów zapytania w celu dostarczania odpowiednich wyników. Biorąc pod uwagę nieprzerwaną pracę złożoności, istnieje wiele czynników, które mogą wpłynąć na wyniki zapytania. Z tego powodu poświęcić czas na zrozumienie sposobu działania wyszukiwania pełnotekstowego oferuje wymierne korzyści podczas próby trzymać się kolejności nieoczekiwane wyniki.  

W tym artykule zbadano wyszukiwanie pełnotekstowe w kontekście usługi Azure Search. Mamy nadzieję, że zapewnia wystarczającą tła do rozpoznawania możliwe przyczyny i potencjalne rozwiązania na potrzeby adresowania typowe problemy z zapytań. 

## <a name="next-steps"></a>Kolejne kroki

+ Tworzenie przykładowego indeksu, wypróbuj różne zapytania i przejrzeć wyniki. Aby uzyskać instrukcje, zobacz [kompilowanie i tworzenie zapytań względem indeksu w portalu](search-get-started-portal.md#query-index).

+ Spróbuj składnię zapytań dodatkowe [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) sekcji z przykładowym lub [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) w Eksploratorze wyszukiwania w portalu.

+ Przegląd [profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) Jeśli chcesz dostosować klasyfikacji w Twojej aplikacji wyszukiwania.

+ Dowiedz się, jak zastosować [analizatory leksykalne specyficzny dla języka](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Skonfiguruj niestandardowe analizatory](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) minimalnego przetwarzania lub wyspecjalizowanych przetwarzanie w określonych polach.

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
