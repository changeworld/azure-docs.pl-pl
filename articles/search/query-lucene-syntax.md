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
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258987"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Składnia kwerendy lucene w usłudze Azure Cognitive Search

Można pisać zapytania względem usługi Azure Cognitive Search na podstawie bogatej składni [analizatora zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) dla wyspecjalizowanych formularzy zapytań: symbol wieloznaczny, wyszukiwanie rozmyte, wyszukiwanie zbliżeniowe, wyrażenia regularne to tylko kilka przykładów. Większość składni analizatora zapytań Lucene jest [zaimplementowana w stanie nienaruszonym w usłudze](search-lucene-query-architecture.md)Azure `$filter` Cognitive Search , z wyjątkiem wyszukiwania *zakresu,* które są konstruowane w usłudze Azure Cognitive Search za pomocą wyrażeń. 

> [!NOTE]
> Pełna składnia Lucene jest używana dla wyrażeń kwerend przekazanych w parametrze **wyszukiwania** interfejsu API [dokumentów wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/search-documents) nie należy mylić z [składnią OData](query-odata-filter-orderby-syntax.md) używaną dla [$filter](search-filters.md) parametru tego interfejsu API. Te różne składnie mają własne reguły do konstruowania kwerend, ucieczki ciągów i tak dalej.

## <a name="invoke-full-parsing"></a>Wywoływanie pełnej analizy

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

następujące podstawy składni mają zastosowanie do wszystkich kwerend, które używają składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Ocena operatora w kontekście

Położenie określa, czy symbol jest interpretowany jako operator lub tylko inny znak w ciągu.

Na przykład w pełnej składni Lucene tylda (~) jest używana zarówno do wyszukiwania rozmytego, jak i wyszukiwania zbliżeniowego. Po umieszczeniu po cytowanej frazie ~ wywołuje wyszukiwanie zbliżeniowe. Po umieszczeniu na końcu terminu ~ wywołuje wyszukiwanie rozmyte.

W ramach terminu, takiego jak "business~analyst", znak nie jest oceniany jako operator. W takim przypadku przy założeniu, że kwerenda jest terminem lub kwerendą frazy, [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [analizą leksykalne](search-lucene-query-architecture.md#stage-2-lexical-analysis) usuwa ~ i przerywa termin "business~analyst" w dwóch: analityk biznesowy LUB.

Powyższy przykład to tylda (~), ale ta sama zasada dotyczy każdego operatora.

### <a name="escaping-special-characters"></a>Ucieczka znaków specjalnych

Aby użyć dowolnego z operatorów wyszukiwania jako części wyszukiwanego tekstu, uniknij znaku, prefiksując go pojedynczym ukośnikiem odwrotnym (`\`). Na przykład w przypadku wyszukiwania `https://`symboli wieloznacznych w obszarze , gdzie `://` jest częścią ciągu zapytania, należy określić `search=https\:\/\/*`. Podobnie, wzór numeru telefonu uciekł może `\+1 \(800\) 642\-7676`wyglądać tak .

Znaki specjalne, które wymagają ucieczki, są następujące:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Chociaż ucieczka utrzymuje tokeny razem, [leksykalne analizy](search-lucene-query-architecture.md#stage-2-lexical-analysis) podczas indeksowania może pozbawić je. Na przykład standardowy analizator Lucene będzie przerywać wyrazy na łączniki, odstępy i inne znaki. Jeśli potrzebujesz znaków specjalnych w ciągu zapytania, może być potrzebny analizator, który zachowuje je w indeksie. Niektóre opcje obejmują [analizatory języka](index-add-language-analyzers.md)naturalnego firmy Microsoft, który zachowuje wyrazy dzielenia wyrazów lub analizatora niestandardowego dla bardziej złożonych wzorców. Aby uzyskać więcej informacji, zobacz [Częściowe terminy, wzorce i znaki specjalne](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie niebezpiecznych i zastrzeżonych znaków w adresach URL

Upewnij się, że wszystkie niebezpieczne i zastrzeżone znaki są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest to identyfikator fragmentu/zakotwiczenia w adresie URL. Znak musi być zakodowany, `%23` jeśli jest używany w adresie URL. '&' i '=' są przykładami znaków zastrzeżonych, ponieważ rozdzielają parametry i określają wartości w usłudze Azure Cognitive Search. Zobacz [RFC1738: Uniform Resource Locators (URL), aby](https://www.ietf.org/rfc/rfc1738.txt) uzyskać więcej informacji.

Niebezpieczne znaki ``" ` < > # % { } | \ ^ ~ [ ]``to . Znaki zastrzeżone to `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limity rozmiaru kwerendy

 Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Cognitive Search. W szczególności można mieć co najwyżej 1024 klauzule (wyrażenia oddzielone AND, OR, i tak dalej). Istnieje również limit około 32 KB na rozmiar dowolnego pojedynczego terminu w kwerendzie. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zaleca się projektowanie go w taki sposób, aby nie generować kwerend o rozmiarze nieograniczonym.  

### <a name="precedence-operators-grouping"></a>Operatory pierwszeństwa (grupowanie)

 Nawiasy można używać do tworzenia podksyrii, w tym operatorów w instrukcji w nawiasie. Na przykład, `motel+(wifi||luxury)` będzie wyszukiwać dokumenty zawierające termin "motel" i "wifi" lub "luksus" (lub oba).

Grupowanie pól jest podobne, ale obejmuje grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` przeszukuje pole "hotelMenities" dla "siłownia" i "wifi", lub "siłownia" i "basen".  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Wyszukiwanie logiczne

 Zawsze określ operatory logiczne tekstu (AND, OR, NOT) we wszystkich wersach.  

### <a name="or-operator-or-or-"></a>lub `OR` operator lub`||`

Operator OR jest pionowym znakiem pręta lub rury. Na przykład: `wifi || luxury` będzie wyszukiwać dokumenty zawierające "wifi" lub "luksus" lub oba. Ponieważ OR jest domyślnym operatorem spójnika, można `wifi luxury` również pominąć go, taki, który jest odpowiednikiem `wifi || luxury`.

### <a name="and-operator-and--or-"></a>i `AND`operator `&&` , lub`+`

Operator AND jest znakiem ampersand lub plus. Na przykład: `wifi && luxury` będzie wyszukiwać dokumenty zawierające zarówno "wifi" i "luksus". Znak plus (+) jest używany dla wymaganych terminów. Na przykład `+wifi +luxury` stanowi, że oba terminy muszą pojawić się gdzieś w polu pojedynczego dokumentu.

### <a name="not-operator-not--or--"></a>NIE `NOT`operator `!` , lub`-`

Operator NOT jest znakiem minus. Na przykład `wifi –luxury` wyszuka dokumenty, `wifi` które mają ten `luxury`termin i/lub nie mają .

Parametr **searchMode** w żądaniu zapytania określa, czy termin z operatorem NOT jest ANDed lub `+` ORed z innymi terminami w kwerendzie (przy założeniu, że nie ma lub `|` operatora na innych warunkach). Prawidłowe `any` wartości `all`obejmują lub .

`searchMode=any`zwiększa wycofywanie zapytań o więcej wyników, `-` a domyślnie będą interpretowane jako "LUB NIE". Na przykład `wifi -luxury` będzie pasować do dokumentów, które zawierają termin `wifi` `luxury`lub te, które nie zawierają terminu .

`searchMode=all`zwiększa precyzję zapytań, włączając mniejszą liczbę wyników, a domyślnie - będzie interpretowana jako "I NIE". Na przykład `wifi -luxury` będzie pasować do `wifi` dokumentów, które zawierają termin i nie zawierają terminu "luksus". Jest to prawdopodobnie bardziej intuicyjne zachowanie `-` dla operatora. W związku z tym `searchMode=all` należy `searchMode=any` rozważyć użycie zamiast, jeśli chcesz zoptymalizować wyszukiwania dla precyzji `-` zamiast odwołania, *a* użytkownicy często używają operatora w wyszukiwaniach.

Decydując się na **ustawienie searchMode,** należy wziąć pod uwagę wzorce interakcji użytkownika dla zapytań w różnych aplikacjach. Użytkownicy, którzy szukają informacji są bardziej prawdopodobne, aby uwzględnić operatora w kwerendzie, w przeciwieństwie do witryn e-commerce, które mają więcej wbudowanych struktur nawigacji.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Wyszukiwanie w polu wymówionym

Operację wyszukiwania z polem pola `fieldName:searchExpression` można zdefiniować ze składnią, w której wyrażeniem wyszukiwania może być pojedynczy wyraz lub fraza, lub bardziej złożonym wyrażeniem w nawiasach, opcjonalnie za pomocą operatorów logicznych. Oto kilka przykładów:  

- gatunek:jazz NIE historia  

- artyści:("Miles Davis" "John Coltrane")

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były `artists` oceniane jako pojedyncza jednostka, w tym przypadku wyszukiwanie dwóch różnych wykonawców w polu.  

Pole określone `fieldName:searchExpression` w musi `searchable` być polem.  Zobacz [Tworzenie indeksu, aby](https://docs.microsoft.com/rest/api/searchservice/create-index) uzyskać szczegółowe informacje na temat sposobu, w jaki atrybuty indeksu są używane w definicjach pól.  

> [!NOTE]
> Podczas korzystania z fielded wyrażeń wyszukiwania, `searchFields` nie trzeba używać parametru, ponieważ każde wyrażenie wyszukiwania fielded ma nazwę pola jawnie określone. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić kwerendę, gdzie niektóre części są ograniczone do określonego pola, a reszta może mieć zastosowanie do kilku pól. Na przykład `search=genre:jazz NOT history&searchFields=description` kwerenda `jazz` będzie pasować tylko do `genre` `NOT history` pola, `description` podczas gdy będzie pasować do pola. Nazwa pola podana `fieldName:searchExpression` w zawsze `searchFields` ma pierwszeństwo przed parametrem, dlatego w `genre` tym `searchFields` przykładzie nie musimy uwzględniać w parametrze.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Wyszukiwanie rozmyte

Rozmyte wyszukiwanie znajduje dopasowania w kategoriach, które mają podobną konstrukcję, rozszerzając termin do maksymalnie 50 terminów, które spełniają kryteria odległości dwóch lub mniej. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie rozmyte](search-query-fuzzy.md).

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

 Na przykład, aby znaleźć dokumenty zawierające "motel" `/[mh]otel/`lub "hotel", określ . Wyszukiwania wyrażeń regularnych są dopasowywane do pojedynczych słów.

Niektóre narzędzia i języki nakładają dodatkowe wymagania dotyczące znaków ucieczki. Dla JSON ciągi, które zawierają ukośnik do przodu są wysuwane z ukośnikiem wstecznym: "microsoft.com/azure/" staje się, `search=/.*microsoft.com\/azure\/.*/` gdy `search=/.* <string-placeholder>.*/` konfiguruje wyrażenie regularne i `microsoft.com\/azure\/` jest ciągiem z ukośnikiem do przodu.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Wyszukiwanie symboli wieloznacznych  

Można użyć ogólnie rozpoznaną składni dla wielu (*) lub pojedynczych symboli wieloznacznych. Należy zauważyć, że analizator zapytania Lucene obsługuje użycie tych symboli z jednym terminem, a nie frazą.

Wyszukiwanie prefiksów używa również`*`znaku gwiazdki ( ). Na przykład wyrażenie kwerendy `search=note*` zwraca "notebook" lub "notatnik". Pełna składnia lucene nie jest wymagana do wyszukiwania prefiksów. Prosta składnia obsługuje ten scenariusz.

Wyszukiwanie sufiksów, gdzie `*` lub `?` poprzedza ciąg, wymaga pełnej składni Lucene i wyrażenia regularnego (nie można użyć * lub ? jako pierwszy znak wyszukiwania). Biorąc pod uwagę termin "alfanumeryczny",`search=/.*numeric.*/`wyrażenie zapytania ( ) znajdzie dopasowanie.

> [!NOTE]  
> Podczas analizowania kwerend kwerend kwerendy sformułowane jako prefiks, sufiks, symbol wieloznaczny lub wyrażenia regularne są przekazywane jako —jest do drzewa kwerend, z pominięciem [analizy leksykalne](search-lucene-query-architecture.md#stage-2-lexical-analysis). Dopasowania zostaną znalezione tylko wtedy, gdy indeks zawiera ciągi w formacie określonym przez zapytanie. W większości przypadków będzie potrzebny alternatywny analizator podczas indeksowania, który zachowuje integralność ciągu, dzięki czemu częściowe dopasowanie termin i wzorzec powiedzie się. Aby uzyskać więcej informacji, zobacz [Częściowe wyszukiwanie terminów w kwerendach usługi Azure Cognitive Search](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Ocenianie kwerend z symbolami wieloznacznych i wyrażenia regularnego

Usługa Azure Cognitive Search używa oceniania opartego na częstotliwości[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)dla zapytań tekstowych. Jednak w przypadku zapytań wieloznacznych i grupek wyeksureży, w których zakres terminów może być potencjalnie szeroki, współczynnik częstotliwości jest ignorowany, aby zapobiec stronniczości rankingu w stosunku do dopasowań z rzadszych terminów. Wszystkie mecze są traktowane jednakowo dla wyszukiwań symboli wieloznacznych i wyekspek.

## <a name="see-also"></a>Zobacz też

+ [Przykłady zapytań dotyczące wyszukiwania prostego](search-query-simple-examples.md)
+ [Przykłady zapytań dotyczące pełnego wyszukiwania lucene](search-query-lucene-examples.md)
+ [Szukaj dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData dla filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Składnia kwerend prostych w usłudze Azure Cognitive Search](query-simple-syntax.md)   
