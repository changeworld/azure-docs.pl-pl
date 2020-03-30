---
title: Składnia zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Odwołanie do pełnej składni zapytania Lucene, używane w usłudze Azure Cognitive Search dla symboli wieloznacznych, wyszukiwania rozmytego, RegEx i innych zaawansowanych konstrukcji zapytań.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: d35c96657f48905f37c9ebe246d81ebb9545cf27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283136"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Składnia kwerendy lucene w usłudze Azure Cognitive Search

Można pisać zapytania względem usługi Azure Cognitive Search na podstawie bogatej składni [analizatora zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) dla wyspecjalizowanych formularzy zapytań: symbol wieloznaczny, wyszukiwanie rozmyte, wyszukiwanie zbliżeniowe, wyrażenia regularne to tylko kilka przykładów. Większość składni analizatora zapytań Lucene jest [zaimplementowana w stanie nienaruszonym w usłudze](search-lucene-query-architecture.md)Azure `$filter` Cognitive Search , z wyjątkiem wyszukiwania *zakresu,* które są konstruowane w usłudze Azure Cognitive Search za pomocą wyrażeń. 

> [!NOTE]
> Pełna składnia Lucene jest używana dla wyrażeń kwerend przekazanych w parametrze **wyszukiwania** interfejsu API [dokumentów wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/search-documents) nie należy mylić z [składnią OData](query-odata-filter-orderby-syntax.md) używaną dla [$filter](search-filters.md) parametru tego interfejsu API. Te różne składnie mają własne reguły do konstruowania kwerend, ucieczki ciągów i tak dalej.

## <a name="how-to-invoke-full-parsing"></a>Jak wywołać pełną analizowanie

Ustaw `queryType` parametr wyszukiwania, aby określić, który analizator ma być używany. Prawidłowe `simple|full`wartości `simple` obejmują , z `full` jako domyślne i Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Przykład przedstawiający pełną składnię

Poniższy przykład znajduje dokumenty w indeksie przy użyciu składni `queryType=full` zapytania Lucene, widoczne w parametrze. Ta kwerenda zwraca hotele, w których pole kategorii zawiera termin "budżet" i wszystkie pola z możliwością wyszukiwania zawierające frazę "niedawno odnowiony". Dokumenty zawierające wyrażenie "niedawno odnowiony" są wyżej oceniane w wyniku terminu wartość dodana (3).  

Parametr `searchMode=all` jest istotne w tym przykładzie. Zawsze, gdy operatory są w kwerendzie, należy ogólnie ustawić, `searchMode=all` aby upewnić się, że *wszystkie* kryteria są zgodne.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternatywnie, należy użyć POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Aby uzyskać dodatkowe przykłady, zobacz [przykłady składni kwerend lucene do tworzenia zapytań w usłudze Azure Cognitive Search.](search-query-lucene-examples.md) Aby uzyskać szczegółowe informacje na temat określania pełnego kontyngentu parametrów kwerendy, zobacz [&#41;interfejsu API interfejsu API usługi Azure Cognitive Search REST &#40;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Usługa Azure Cognitive Search obsługuje również [prostą składnię zapytań](query-simple-syntax.md), prosty i niezawodny język zapytań, który może być używany do prostego wyszukiwania słów kluczowych.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Podstawy składni  
 Następujące podstawy składni mają zastosowanie do wszystkich kwerend, które używają składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Ocena operatora w kontekście

Położenie określa, czy symbol jest interpretowany jako operator lub tylko inny znak w ciągu.

Na przykład w pełnej składni Lucene tylda (~) jest używana zarówno do wyszukiwania rozmytego, jak i wyszukiwania zbliżeniowego. Po umieszczeniu po cytowanej frazie ~ wywołuje wyszukiwanie zbliżeniowe. Po umieszczeniu na końcu terminu ~ wywołuje wyszukiwanie rozmyte.

W ramach terminu, takiego jak "business~analyst", znak nie jest oceniany jako operator. W takim przypadku przy założeniu, że kwerenda jest terminem lub kwerendą frazy, [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [analizą leksykalne](search-lucene-query-architecture.md#stage-2-lexical-analysis) usuwa ~ i przerywa termin "business~analyst" w dwóch: analityk biznesowy LUB.

Powyższy przykład to tylda (~), ale ta sama zasada dotyczy każdego operatora.

### <a name="escaping-special-characters"></a>Ucieczka znaków specjalnych

 Znaki specjalne muszą być zmienione, aby były używane jako część wyszukiwanego tekstu. Można ich uniknąć, prefiksując je\\ukośnikiem odwrotnym ( ). Znaki specjalne, które muszą zostać zmienione, są następujące:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Na przykład, aby uniknąć symbolu \\ \*wieloznacznego, użyj programu .

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie niebezpiecznych i zastrzeżonych znaków w adresach URL

 Upewnij się, że wszystkie niebezpieczne i zastrzeżone znaki są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest to identyfikator fragement/anchor w adresie URL. Znak musi być zakodowany, `%23` jeśli jest używany w adresie URL. '&' i '=' są przykładami znaków zastrzeżonych, ponieważ rozdzielają parametry i określają wartości w usłudze Azure Cognitive Search. Zobacz [RFC1738: Uniform Resource Locators (URL), aby](https://www.ietf.org/rfc/rfc1738.txt) uzyskać więcej informacji.

 Niebezpieczne znaki ``" ` < > # % { } | \ ^ ~ [ ]``to . Znaki zastrzeżone to `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operatory pierwszeństwa: grupowanie i grupowanie pól  
 Nawiasy można używać do tworzenia podksyrii, w tym operatorów w instrukcji w nawiasie. Na przykład, `motel+(wifi||luxury)` będzie wyszukiwać dokumenty zawierające termin "motel" i "wifi" lub "luksus" (lub oba).

Grupowanie pól jest podobne, ale obejmuje grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` przeszukuje pole "hotelMenities" dla "siłownia" i "wifi", lub "siłownia" i "basen".  

### <a name="searchmode-parameter-considerations"></a>Zagadnienia dotyczące parametrów trybu wyszukiwania  
 Wpływ na `searchMode` kwerendy, zgodnie z opisem w [składni prostej kwerendy w usłudze Azure Cognitive Search,](query-simple-syntax.md)ma zastosowanie w równym stopniu do składni zapytania Lucene. Mianowicie w `searchMode` połączeniu z operatorami NOT może spowodować wyniki kwerendy, które mogą wydawać się niezwykłe, jeśli nie są jasne, na temat wpływu sposobu ustawiania parametru. Jeśli zachowasz wartość `searchMode=any`domyślną i użyjesz operatora NOT, operacja jest obliczana jako akcja OR, w taki sposób, że "Nowy Jork" NIE "Seattle" zwraca wszystkie miasta, które nie są Seattle.  

##  <a name="boolean-operators-and-or-not"></a><a name="bkmk_boolean"></a>Operatory logiczne (AND, OR, NOT) 
 Zawsze określ operatory logiczne tekstu (AND, OR, NOT) we wszystkich wersach.  

### <a name="or-operator-or-or-"></a>lub `OR` operator lub`||`

Operator OR jest pionowym znakiem pręta lub rury. Na przykład: `wifi || luxury` będzie wyszukiwać dokumenty zawierające "wifi" lub "luksus" lub oba. Ponieważ OR jest domyślnym operatorem spójnika, można `wifi luxury` również pominąć go, taki, który jest odpowiednikiem `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>i `AND`operator `&&` , lub`+`

Operator AND jest znakiem ampersand lub plus. Na przykład: `wifi && luxury` będzie wyszukiwać dokumenty zawierające zarówno "wifi" i "luksus". Znak plus (+) jest używany dla wymaganych terminów. Na przykład `+wifi +luxury` stanowi, że oba terminy muszą pojawić się gdzieś w polu pojedynczego dokumentu.


### <a name="not-operator-not--or--"></a>NIE `NOT`operator `!` , lub`-`

Operator NOT jest wykrzyknikiem lub znakiem minus. Na przykład: `wifi !luxury` wyszuka dokumenty, które mają termin "wifi" i/lub nie mają "luksusu". Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed lub ORed z innymi warunkami w kwerendzie w przypadku braku + lub || Operator. Przypomnijmy, że `searchMode` można `any`ustawić albo `all`(domyślnie) lub .

Korzystanie `searchMode=any` zwiększa wycofywanie zapytań, dołączając więcej wyników i domyślnie - będą interpretowane jako "LUB NIE". Na przykład `wifi -luxury` będzie dopasować dokumenty, które zawierają termin *wifi* lub te, które nie zawierają terminu *luksus.*

Użycie `searchMode=all` zwiększa precyzję zapytań, dołączając mniej wyników i domyślnie - będzie interpretowany jako "I NIE". Na przykład `wifi -luxury` będzie pasować do `wifi` dokumentów, które `luxury`zawierają termin i nie zawierają terminu . Jest to prawdopodobnie bardziej intuicyjne zachowanie dla - operatora. W związku z tym `searchMode=all` `searchMode=any` należy rozważyć wybranie ponad, jeśli chcesz zoptymalizować wyszukiwanie dla `-` precyzji zamiast odwołania *i* użytkownicy często używają operatora w wyszukiwaniach.

##  <a name="query-size-limitations"></a><a name="bkmk_querysizelimits"></a>Ograniczenia rozmiaru kwerendy  
 Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Cognitive Search. W szczególności można mieć co najwyżej 1024 klauzule (wyrażenia oddzielone AND, OR, i tak dalej). Istnieje również limit około 32 KB na rozmiar dowolnego pojedynczego terminu w kwerendzie. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zaleca się projektowanie go w taki sposób, aby nie generować kwerend o rozmiarze nieograniczonym.  

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Ocenianie kwerend z symbolami wieloznacznych i wyrażenia regularnego
 Usługa Azure Cognitive Search używa oceniania opartego na częstotliwości[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)dla zapytań tekstowych. Jednak w przypadku zapytań wieloznacznych i grupek wyeksureży, w których zakres terminów może być potencjalnie szeroki, współczynnik częstotliwości jest ignorowany, aby zapobiec stronniczości rankingu w stosunku do dopasowań z rzadszych terminów. Wszystkie mecze są traktowane jednakowo dla wyszukiwań symboli wieloznacznych i wyekspek.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Wyszukiwanie w polu wymówionym  
Operację wyszukiwania z polem pola `fieldName:searchExpression` można zdefiniować ze składnią, w której wyrażeniem wyszukiwania może być pojedynczy wyraz lub fraza, lub bardziej złożonym wyrażeniem w nawiasach, opcjonalnie za pomocą operatorów logicznych. Oto kilka przykładów:  

- gatunek:jazz NIE historia  

- artyści:("Miles Davis" "John Coltrane")

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były `artists` oceniane jako pojedyncza jednostka, w tym przypadku wyszukiwanie dwóch różnych wykonawców w polu.  

Pole określone `fieldName:searchExpression` w musi `searchable` być polem.  Zobacz [Tworzenie indeksu, aby](https://docs.microsoft.com/rest/api/searchservice/create-index) uzyskać szczegółowe informacje na temat sposobu, w jaki atrybuty indeksu są używane w definicjach pól.  

> [!NOTE]
> Podczas korzystania z fielded wyrażeń wyszukiwania, `searchFields` nie trzeba używać parametru, ponieważ każde wyrażenie wyszukiwania fielded ma nazwę pola jawnie określone. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić kwerendę, gdzie niektóre części są ograniczone do określonego pola, a reszta może mieć zastosowanie do kilku pól. Na przykład `search=genre:jazz NOT history&searchFields=description` kwerenda `jazz` będzie pasować tylko do `genre` `NOT history` pola, `description` podczas gdy będzie pasować do pola. Nazwa pola podana `fieldName:searchExpression` w zawsze `searchFields` ma pierwszeństwo przed parametrem, dlatego w `genre` tym `searchFields` przykładzie nie musimy uwzględniać w parametrze.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Wyszukiwanie rozmyte  
 Rozmyte wyszukiwanie znajduje dopasowania w kategoriach, które mają podobną konstrukcję. Na [lucene dokumentacji,](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)rozmyte wyszukiwania są oparte na [Damerau-Levenshtein Odległość](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Rozmyte wyszukiwania można rozszerzyć termin do maksymalnie 50 terminów, które spełniają kryteria odległości. 

 Aby wykonać wyszukiwanie rozmyte, użyj symbolu tyldy "~" na końcu pojedynczego wyrazu z opcjonalnym parametrem, liczbą z 0 do 2 (domyślnie), która określa odległość edycji. Na przykład "blue~" lub "blue~1" zwróci "niebieski", "blues" i "glue".

 Wyszukiwanie rozmyte można stosować tylko do terminów, a nie do fraz, ale tylda można dołączyć do każdego terminu indywidualnie w wieloczęściowej nazwie lub frazie. Na przykład "Unviersty~ of~ "Wshington~" będzie pasować do "University of Washington".
 

##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Wyszukiwanie zbliżeniowe  
 Wyszukiwanie zbliżeniowe służy do znajdowania terminów, które znajdują się blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, po której następuje liczba słów tworzących granicę bliskości. Na przykład, `"hotel airport"~5` znajdzie terminy "hotel" i "lotnisko" w ciągu 5 słów od siebie w dokumencie.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Zwiększenie terminów  
 Termin zwiększenie odnosi się do rankingu dokumentu wyżej, jeśli zawiera wzmocniony termin, w stosunku do dokumentów, które nie zawierają terminu. Różni się to od profili oceniania tym, że profile punktacji zwiększają niektóre pola, a nie konkretne terminy.  

Poniższy przykład pomaga zilustrować różnice. Załóżmy, że istnieje profil punktacji, który zwiększa dopasowania w określonym polu, powiedzmy *gatunek* w [przykładzie musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Zwiększenie terminów może być wykorzystane do dalszego zwiększenia niektórych wyszukiwanych terminów wyższych niż inne. Na przykład `rock^2 electronic` zwiększy dokumenty, które zawierają wyszukiwane terminy w polu gatunku wyższe niż inne pola z wyszukujem w indeksie. Ponadto dokumenty zawierające wyszukiwane hasło *rock* będą klasyfikowane wyżej niż inne wyszukiwane hasło *elektroniczne* w wyniku wartości dodanej terminu (2).  

 Aby zwiększyć termin użyj daszka "^", symbol z współczynnikiem wzmocnienia (liczba) na końcu wyszukiwanego terminu. Możesz również promować frazy. Im wyższy współczynnik wzmocnienia, tym bardziej odpowiedni termin będzie w stosunku do innych wyszukiwanych haseł. Domyślnie współczynnik wzmocnienia wynosi 1. Chociaż współczynnik wzmocnienia musi być dodatni, może być mniejszy niż 1 (na przykład 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Wyszukiwanie wyrażeń regularnych  
 Wyszukiwanie wyrażeń regularnych znajduje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z dokumentami w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Na przykład, aby znaleźć dokumenty zawierające "motel" `/[mh]otel/`lub "hotel", określ .  Wyszukiwania wyrażeń regularnych są dopasowywane do pojedynczych słów.   

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Wyszukiwanie symboli wieloznacznych  
 Można użyć ogólnie rozpoznaną składni dla wielu (*) lub pojedynczych symboli wieloznacznych. Należy zauważyć, że analizator zapytania Lucene obsługuje użycie tych symboli z jednym terminem, a nie frazą.  

 Na przykład, aby znaleźć dokumenty zawierające słowa z prefiksem "uwaga", takie jak "notatnik" lub "notatnik", określ "uwaga*".  

> [!NOTE]  
>  Nie można użyć * lub ? jako pierwszy znak wyszukiwania.  
>  W kwerendach wyszukiwania z symbolami wieloznacznych nie jest przeprowadzana żadna analiza tekstu. W czasie kwerendy terminy kwerendy wieloznaczne są porównywane z analizowanymi terminami w indeksie wyszukiwania i rozwiniętymi.

## <a name="see-also"></a>Zobacz też  

+ [Szukaj dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData dla filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Składnia kwerend prostych w usłudze Azure Cognitive Search](query-simple-syntax.md)   
