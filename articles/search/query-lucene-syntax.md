---
title: Składnia zapytań Lucene
titleSuffix: Azure Cognitive Search
description: Informacje dotyczące pełnej składni zapytań Lucene w usłudze Azure Wyszukiwanie poznawcze w przypadku symboli wieloznacznych, wyszukiwania rozmytego, wyrażenia regularnego i innych zaawansowanych konstrukcji zapytań.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379642"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Składnia zapytań Lucene w usłudze Azure Wyszukiwanie poznawcze

Zapytania dotyczące usługi Azure Wyszukiwanie poznawcze można pisać w oparciu o rozbudowana składnia [analizatora zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) dla wyspecjalizowanych formularzy zapytań: symbol wieloznaczny, Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, wyrażenia regularne to kilka przykładów. Większość składni analizatora zapytań Lucene jest [zaimplementowana w usłudze azure wyszukiwanie poznawcze](search-lucene-query-architecture.md), z wyjątkiem *wyszukiwań zakresu* , które są zbudowane na platformie Azure Wyszukiwanie poznawcze za pomocą wyrażeń `$filter`. 

> [!NOTE]
> Pełna składnia Lucene jest używana dla wyrażeń zapytania, które przechodzą w parametr **wyszukiwania** interfejsu API [dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) , nie należy mylić ze [składnią OData](query-odata-filter-orderby-syntax.md) UŻYTĄ dla parametru [$Filter](search-filters.md) tego interfejsu API. Te różne składni mają własne reguły tworzenia zapytań, ciągów ucieczki i tak dalej.

## <a name="how-to-invoke-full-parsing"></a>Jak wywołać pełną analizę

Ustaw parametr wyszukiwania `queryType`, aby określić, który Analizator ma być używany. Prawidłowe wartości to `simple|full`, z `simple` jako domyślne i `full` dla Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Przykład przedstawiający pełną składnię

Poniższy przykład odnajduje dokumenty w indeksie przy użyciu składni zapytań Lucene, oczywisty w parametrze `queryType=full`. To zapytanie zwraca Hotele, w których pole Category zawiera termin "budżet" i wszystkie pola z możliwością wyszukiwania zawierające frazę "ostatnio Renovated". Dokumenty zawierające frazę "niedawno Renovated" są bardziej klasyfikowane jako wynik okresu zwiększenia wartości (3).  

Parametr `searchMode=all` ma zastosowanie w tym przykładzie. Za każdym razem, gdy operatory znajdują się w zapytaniu, należy ogólnie ustawić `searchMode=all`, aby upewnić się, że *wszystkie* kryteria są zgodne.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternatywnie możesz użyć wpisu:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Aby uzyskać więcej przykładów, zobacz [przykłady składni zapytań Lucene dotyczące kompilowania zapytań w usłudze Azure wyszukiwanie poznawcze](search-query-lucene-examples.md). Aby uzyskać szczegółowe informacje na temat określania pełnego uwarunkowania parametrów zapytania, zobacz [Wyszukiwanie dokumentów &#40;Azure wyszukiwanie poznawcze&#41;REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Usługa Azure Wyszukiwanie poznawcze obsługuje także [prostą składnię zapytań](query-simple-syntax.md), prosty i niezawodny język zapytań, który może służyć do prostego wyszukiwania słów kluczowych.  

##  <a name="bkmk_syntax"></a>Podstawy składni  
 Poniższe podstawowe informacje o składni dotyczą wszystkich zapytań, które używają składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Obliczanie operatora w kontekście

Umieszczanie określa, czy symbol jest interpretowany jako operator, czy po prostu innego znaku w ciągu.

Na przykład w pełnej składni Lucene jest wyrażenie tyldy (~) używane do wyszukiwania rozmytego i wyszukiwania bliskości. Po umieszczeniu w cudzysłowie, ~ wywołuje wyszukiwanie zbliżeniowe. Po umieszczeniu na końcu okresu, ~ wywołuje rozmyte wyszukiwanie.

W ramach terminu, takiego jak "Business ~ analityk", znak nie jest obliczany jako operator. W takim przypadku zakładając, że zapytanie jest wyrażeniem terminu lub frazy, [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [analizą leksykalną](search-lucene-query-architecture.md#stage-2-lexical-analysis) powoduje przełączenie ~ i przerywa okres "Business" analityka w dwóch: Business lub analityku.

Powyższym przykładem jest Tylda (~), ale ta sama zasada ma zastosowanie do każdego operatora.

### <a name="escaping-special-characters"></a>Znaki specjalne ucieczki

 Znaki specjalne należy zmienić, aby można było używać ich jako części wyszukiwanego tekstu. Można je zmienić, umieszczając je za pomocą ukośnika odwrotnego (\\). Należy wprowadzić następujące znaki specjalne:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Na przykład, aby wypróbować symbol wieloznaczny, użyj \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków niebezpiecznych i zastrzeżonych w adresach URL

 Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowane są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest fragement/identyfikatorem zakotwiczenia w adresie URL. Znak musi być zakodowany w `%23`, jeśli jest używany w adresie URL. "&" i "=" są przykładami znaków zarezerwowanych, ponieważ oddzielają one parametry i określają wartości na platformie Azure Wyszukiwanie poznawcze. Aby uzyskać więcej informacji, zobacz [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

 Niebezpieczne znaki są ``" ` < > # % { } | \ ^ ~ [ ]``. Zarezerwowane znaki są `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operatory pierwszeństwa: grupowanie i grupowanie pól  
 Za pomocą nawiasów można tworzyć podzapytania, w tym operatory w instrukcji języka nawiasów. Na przykład `motel+(wifi||luxury)` będzie wyszukiwać dokumenty zawierające termin "Motel" i "Wi-Fi" lub "możliwość zaprojektowania" (lub oba te elementy).

Grupowanie pól jest podobne, ale zakresy grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` przeszukuje pole "hotelAmenities" dla "treningów" i "Wi-Fi", "treningów" i "Pool".  

### <a name="searchmode-parameter-considerations"></a>Zagadnienia dotyczące parametrów searchmode  
 Wpływ `searchMode` na zapytania, zgodnie z opisem w temacie [prosta Składnia zapytania w usłudze Azure wyszukiwanie poznawcze](query-simple-syntax.md), ma taką samą wartość jak składnia zapytań Lucene. W związku z tym, `searchMode` w połączeniu z operatorami NOT nie może skutkować wynikami zapytania, które mogą wydawać się nietypowe, jeśli nie są jasne w konsekwencji sposobu ustawienia parametru. Jeśli zostanie zachowana wartość domyślna, `searchMode=any`i użyj operatora NOT, operacja zostanie obliczona jako akcja lub, taka jak "New York" nie "Seattle" zwróci wszystkie miasta, które nie są w Seattle.  

##  <a name="bkmk_boolean"></a>Operatory logiczne (i, nie) 
 Zawsze określaj operatory wartości tekstowych (i, lub, nie) we wszystkich wersalikach.  

### <a name="or-operator-or-or-"></a>LUB `OR` operatora lub `||`

Operator OR jest pionowym znakiem kreski lub potoku. Na przykład: `wifi || luxury` przeszuka dokumenty zawierające "Wi-Fi" lub "możliwość zaprojektowania". Ponieważ lub jest domyślnym operatorem połączenia, można go również pozostawić, tak że `wifi luxury` jest odpowiednikiem `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>Operatory i `AND`, `&&` lub `+`

Operator i jest znakiem handlowego "i". Na przykład: `wifi && luxury` będzie szukać dokumentów zawierających zarówno ciąg "Wi-Fi", jak i "możliwość zaprojektowania". Znak plus (+) jest używany dla wymaganych warunków. Na przykład `+wifi +luxury` określa, że oba warunki muszą znajdować się gdzieś w polu jednego dokumentu.


### <a name="not-operator-not--or--"></a>NIE operator `NOT`, `!` lub `-`

Operator NOT jest wykrzyknikiem lub znakiem minus. Na przykład: `wifi !luxury` będzie wyszukiwać dokumenty, które mają termin "Wi-Fi" i/lub nie mają "możliwość zaprojektowania". Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed czy logicznie z innymi postanowieniami w zapytaniu w przypadku braku + lub | | zakład. Odwołaj tę `searchMode` można ustawić na `any`(domyślne) lub `all`.

Użycie `searchMode=any` zwiększa odwoływanie zapytań przez dołączenie większej liczby wyników i domyślnie — będzie interpretowane jako "lub" nie ". Na przykład `wifi -luxury` będzie pasować do dokumentów, które zawierają termin *Wi-Fi* lub te, które nie zawierają terminu *możliwość zaprojektowania.*

Użycie `searchMode=all` zwiększa precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowane jako ", a nie". Na przykład `wifi -luxury` będą pasować do dokumentów zawierających termin `wifi` i nie zawierają terminu `luxury`. Jest to raczej bardziej intuicyjne zachowanie dla operatora. W związku z tym należy rozważyć wybranie `searchMode=all` przekroczenia `searchMode=any`, jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji, a nie odwołania *, a* użytkownicy często używają operatora `-` w wyszukiwaniach.

##  <a name="bkmk_querysizelimits"></a>Ograniczenia rozmiaru zapytania  
 Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Wyszukiwanie poznawcze. W szczególności można mieć co najwyżej 1024 klauzul (wyrażenia oddzielone znakami i, lub itd.). Obowiązuje również limit około 32 KB na rozmiar każdego pojedynczego okresu zapytania. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zalecamy zaprojektowanie go w taki sposób, aby nie generował zapytań o nieograniczonego rozmiaru.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Ocenianie symboli wieloznacznych i wyrażeń regularnych
 Usługa Azure Wyszukiwanie poznawcze używa oceniania opartego na częstotliwościach ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) na potrzeby zapytań tekstowych. Jednakże w przypadku zapytań wieloznacznych i wyrażeń regularnych, w których zakres terminów może być bardzo szeroki, współczynnik częstotliwości jest ignorowany, aby zapobiec rozliczeniu na dopasowania od rzadkich warunków. Wszystkie dopasowania są traktowane równo w przypadku wyszukiwania symboli wieloznacznych i wyrażeń regularnych.

##  <a name="bkmk_fields"></a>Wyszukiwanie polowe  
Można zdefiniować pole operacji wyszukiwania z `fieldName:searchExpression` składnią, gdzie wyrażenie wyszukiwania może być pojedynczym słowem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:  

- Gatunek: nie historia Jazz  

- artyści:("mile Davis" "Jan Coltrane")

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, w tym przypadku wyszukiwanie dwóch odrębnych artystów w polu `artists`.  

Pole określone w `fieldName:searchExpression` musi być polem `searchable`.  Aby uzyskać szczegółowe informacje na temat sposobu używania atrybutów indeksu w definicjach pól, zobacz [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) .  

> [!NOTE]
> W przypadku korzystania z wyrażeń wyszukiwania w polu nie trzeba używać `searchFields` parametru, ponieważ każde wyrażenie wyszukiwania w polu ma jawnie określoną nazwę pola. Jednak nadal można użyć parametru `searchFields`, jeśli chcesz uruchomić zapytanie, w którym niektóre części są objęte zakresem określonego pola, a reszta może mieć zastosowanie do kilku pól. Na przykład `search=genre:jazz NOT history&searchFields=description` kwerendy będzie pasować `jazz` tylko do pola `genre`, podczas gdy będzie pasować `NOT history` z polem `description`. Nazwa pola podana w `fieldName:searchExpression` zawsze ma pierwszeństwo przed parametrem `searchFields`, co oznacza, że w tym przykładzie nie trzeba uwzględniać `genre` w parametrze `searchFields`.

##  <a name="bkmk_fuzzy"></a>Wyszukiwanie rozmyte  
 Wyszukiwanie rozmyte wyszukuje dopasowania w warunkach, które mają podobną konstrukcję. W [dokumentacji dotyczącej Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)wyszukiwania rozmyte są oparte na [odległości Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Wyszukiwania rozmyte mogą rozszerzać termin do maksymalnie 50 warunków, które spełniają kryteria odległości. 

 Aby wykonać Wyszukiwanie rozmyte, Użyj symbolu "~" na końcu pojedynczego słowa z opcjonalnym parametrem, liczbą z przedziału od 0 do 2 (wartość domyślna), która określa odległość edycji. Na przykład "Blue ~" lub "Blue ~ 1" zwróci "Blue", "Blues" i "Glue".

 Wyszukiwanie rozmyte może być stosowane tylko do warunków, a nie fraz, ale do każdego terminu można dołączać pojedyncze części nazwy lub frazy. Na przykład "Unviersty ~ of ~" Wshington ~ "byłoby zgodne z" University of Waszyngton ".
 

##  <a name="bkmk_proximity"></a>Wyszukiwanie w sąsiedztwie  
 Wyszukiwania w sąsiedztwie są używane do znajdowania terminów blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości. Na przykład w `"hotel airport"~5` znajdą się terminy "Hotel" i "Lotnisko" w 5 wyrazach innych elementów w dokumencie.  


##  <a name="bkmk_termboost"></a>Zwiększenie warunków  
 Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Różni się to od profilów oceniania w tych profilach oceniania, a nie konkretnych warunków.  

Poniższy przykład pomaga zilustrować różnice. Załóżmy, że istnieje profil oceniania, który zwiększa zgodność w określonym polu, podyktuj *gatunek* w [przykładowym musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Zwiększenie okresu może służyć do dalszej promocji niektórych wyszukiwanych terminów wyższych niż inne. Na przykład `rock^2 electronic` będzie ulepszał dokumenty zawierające terminy wyszukiwania w polu gatunek powyżej innych pól, które można wyszukiwać w indeksie. Ponadto dokumenty zawierające wyszukiwany termin *skały* są wyższe niż w przypadku innych wyszukiwanych warunków w postaci *elektronicznej* w wyniku okresu zwiększenia wartości (2).  

 Aby zwiększyć okres korzystania z karetki, "^", symbol z współczynnikem wzrostu (liczbą) na końcu wyszukiwanego okresu. Możesz również poprawić frazy. Im wyższy współczynnik zwiększania wydajności, tym bardziej istotny termin będzie odnosić się do innych wyszukiwanych terminów. Domyślnie współczynnik zwiększania wynosi 1. Chociaż współczynnik zwiększania wartości musi być dodatni, może być mniejszy niż 1 (na przykład 0,20).  

##  <a name="bkmk_regex"></a>Wyszukiwanie wyrażeń regularnych  
 Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Na przykład aby znaleźć dokumenty zawierające "Motel" lub "Hotel", określ `/[mh]otel/`.  Wyszukiwania wyrażeń regularnych są dopasowywane do pojedynczych wyrazów.   

##  <a name="bkmk_wildcard"></a>Wyszukiwanie symboli wieloznacznych  
 Można użyć ogólnie rozpoznanej składni dla wielu symboli wieloznacznych (*) lub pojedynczych znaków (?). Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.  

 Na przykład aby znaleźć dokumenty zawierające słowa z prefiksem "Uwaga", na przykład "Notes" lub "Notepad", należy określić "Uwaga *".  

> [!NOTE]  
>  Nie można użyć znaku * ani? Symbol jako pierwszy znak wyszukiwania.  
>  Nie jest przeprowadzana analiza tekstu dla zapytań wyszukiwania symboli wieloznacznych. W czasie zapytania symbole wieloznacznego zapytania są porównywane z przeanalizowanymi terminami w indeksie wyszukiwania i rozwinięte.

## <a name="see-also"></a>Zobacz też  

+ [Wyszukaj dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData dla filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze](query-simple-syntax.md)   
