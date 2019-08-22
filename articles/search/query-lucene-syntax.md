---
title: Składnia zapytań Lucene-Azure Search
description: Odwołanie do pełnej składni Lucene, używanej z Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: d667588cea5902700c225dd7b597d8f03d93d200
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650044"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Składnia zapytań Lucene w Azure Search
Zapytania dotyczące Azure Search na podstawie rozbudowanej składni [analizatora zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) można pisać dla wyspecjalizowanych formularzy zapytań: symbol wieloznaczny, Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, wyrażenia regularne to kilka przykładów. Większość składni analizatora zapytań Lucene jest [implementowana w Azure Search](search-lucene-query-architecture.md), z wyjątkiem *wyszukiwania zakresu* , które są skonstruowane w Azure Search za pomocą `$filter` wyrażeń. 

## <a name="how-to-invoke-full-parsing"></a>Jak wywołać pełną analizę

Ustaw parametr `queryType` Search, aby określić, który Analizator ma być używany. Prawidłowe wartości to `simple|full`, z `simple` wartościami domyślnymi i `full` dla Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Przykład przedstawiający pełną składnię

Poniższy przykład odnajduje dokumenty w indeksie przy użyciu składni zapytań Lucene, oczywisty `queryType=full` dla parametru. To zapytanie zwraca Hotele, w których pole Category zawiera termin "budżet" i wszystkie pola z możliwością wyszukiwania zawierające frazę "ostatnio Renovated". Dokumenty zawierające frazę "niedawno Renovated" są bardziej klasyfikowane jako wynik okresu zwiększenia wartości (3).  

`searchMode=all` Parametr jest istotny w tym przykładzie. Zawsze, gdy operatory znajdują się w zapytaniu, `searchMode=all` należy ogólnie ustawić, aby upewnić się, że *wszystkie* kryteria są zgodne.

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

Aby uzyskać więcej przykładów, zobacz [przykłady składni zapytań Lucene dla tworzenia zapytań w Azure Search](search-query-lucene-examples.md). Aby uzyskać szczegółowe informacje na temat określania pełnego uwarunkowania parametrów zapytania, zobacz [Wyszukiwanie dokumentów &#40;Azure Search Interfejs&#41;API REST usługi](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Search obsługuje także [prostą składnię zapytań](query-simple-syntax.md), prosty i niezawodny język zapytań, który może służyć do prostego wyszukiwania słów kluczowych.  

##  <a name="bkmk_syntax"></a>Podstawy składni  
 Poniższe podstawowe informacje o składni dotyczą wszystkich zapytań, które używają składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Obliczanie operatora w kontekście

Umieszczanie określa, czy symbol jest interpretowany jako operator, czy po prostu innego znaku w ciągu.

Na przykład w pełnej składni Lucene jest wyrażenie tyldy (~) używane do wyszukiwania rozmytego i wyszukiwania bliskości. Po umieszczeniu w cudzysłowie, ~ wywołuje wyszukiwanie zbliżeniowe. Po umieszczeniu na końcu okresu, ~ wywołuje rozmyte wyszukiwanie.

W ramach terminu, takiego jak "Business ~ analityk", znak nie jest obliczany jako operator. W takim przypadku zakładając, że zapytanie jest wyrażeniem terminu lub frazy, [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [analizą leksykalną](search-lucene-query-architecture.md#stage-2-lexical-analysis) powoduje przełączenie ~ i przerywa okres "Business" analityka w dwóch: Business lub analityku.

Powyższym przykładem jest Tylda (~), ale ta sama zasada ma zastosowanie do każdego operatora.

### <a name="escaping-special-characters"></a>Znaki specjalne ucieczki

 Znaki specjalne należy zmienić, aby można było używać ich jako części wyszukiwanego tekstu. Można je zmienić, umieszczając ich prefiksy ukośnikiem odwrotnym (\\). Należy wprowadzić następujące znaki specjalne:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Na przykład, aby wypróbować symbol wieloznaczny, użyj \\. \*

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków niebezpiecznych i zastrzeżonych w adresach URL

 Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowane są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest fragement/identyfikatorem zakotwiczenia w adresie URL. Znak musi być zakodowany `%23` w przypadku użycia w adresie URL. "&" i "=" są przykładami znaków zarezerwowanych, ponieważ oddzielają parametry i określają wartości w Azure Search. Zobacz [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) , aby uzyskać więcej szczegółów.

 Niebezpieczne znaki to ``" ` < > # % { } | \ ^ ~ [ ]``. Znaki zarezerwowane `; / ? : @ = + &`są.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operatory pierwszeństwa: grupowanie i grupowanie pól  
 Za pomocą nawiasów można tworzyć podzapytania, w tym operatory w instrukcji języka nawiasów. Program przeszuka na `motel+(wifi||luxury)` przykład dokumenty zawierające termin "Motel" i "Wi-Fi" lub "możliwość zaprojektowania" (lub oba te elementy).

Grupowanie pól jest podobne, ale zakresy grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` szuka pola "hotelAmenities" dla "treningów" i "Wi-Fi", "treningów" i "Pool".  

### <a name="searchmode-parameter-considerations"></a>Zagadnienia dotyczące parametrów searchmode  
 Wpływ `searchMode` na zapytania, zgodnie z opisem w temacie [prosta Składnia zapytania w Azure Search](query-simple-syntax.md), dotyczy również składni zapytań Lucene. Mianowicie, `searchMode` w połączeniu z operatorami not, może wynikać z wyników zapytania, które mogą wydawać się nietypowe, jeśli nie są jasne w konsekwencji sposobu ustawienia parametru. Jeśli zachowasz wartość domyślną `searchMode=any`, i użyjesz operatora not, operacja jest obliczana jako akcja lub, tak że "New York" nie "Seattle" zwraca wszystkie miasta, które nie są Seattle.  

##  <a name="bkmk_boolean"></a>Operatory logiczne (i, nie) 
 Zawsze określaj operatory wartości tekstowych (i, lub, nie) we wszystkich wersalikach.  

### <a name="or-operator-or-or-"></a>Operator `OR` or lub`||`

Operator OR jest pionowym znakiem kreski lub potoku. Na przykład: `wifi || luxury` program przeszuka dokumenty zawierające "Wi-Fi" lub "możliwość zaprojektowania". Ponieważ lub jest domyślnym operatorem połączenia, można go również pozostawić, tak aby `wifi luxury` był `wifi || luxuery`odpowiednikiem.

### <a name="and-operator-and--or-"></a>`AND` Operator`&&` and lub`+`

Operator i jest znakiem handlowego "i". Na przykład: `wifi && luxury` program przeszuka dokumenty zawierające zarówno "Wi-Fi", jak i "możliwość zaprojektowania". Znak plus (+) jest używany dla wymaganych warunków. Na przykład program `+wifi +luxury` określa, że oba warunki muszą znajdować się gdzieś w polu jednego dokumentu.


### <a name="not-operator-not--or--"></a>Not — `NOT` `!` operator lub`-`

Operator NOT jest wykrzyknikiem lub znakiem minus. Na przykład: `wifi !luxury` program przeszuka dokumenty z terminem "Wi-Fi" i/lub nie mają "możliwość zaprojektowania". Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed czy logicznie z innymi postanowieniami w zapytaniu w przypadku braku operatora + lub | |. Odwołaj tę `searchMode` wartość można ustawić na wartość `any`(domyślnie) lub `all`.

Użycie `searchMode=any` zwiększa odwoływanie zapytań przez dołączenie większej liczby wyników i domyślnie — będzie interpretowane jako "lub" nie ". Na przykład `wifi -luxury` program będzie pasował do dokumentów, które zawierają termin *Wi-Fi* lub te, które nie zawierają terminu *możliwość zaprojektowania.*

Użycie `searchMode=all` zwiększa precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowane jako "i not". Na przykład `wifi -luxury` program będzie pasował do dokumentów zawierających termin `wifi` i nie zawiera warunków `luxury`. Jest to raczej bardziej intuicyjne zachowanie dla operatora. W związku z tym należy rozważyć `searchMode=all` wybranie `searchMode=any` opcji powyżej, jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji zamiast odwołania `-` *, a* użytkownicy często używają operatora w wyszukiwaniach.

##  <a name="bkmk_querysizelimits"></a>Ograniczenia rozmiaru zapytania  
 Istnieje limit rozmiaru zapytań, które można wysłać do Azure Search. W szczególności można mieć co najwyżej 1024 klauzul (wyrażenia oddzielone znakami i, lub itd.). Obowiązuje również limit około 32 KB na rozmiar każdego pojedynczego okresu zapytania. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zalecamy zaprojektowanie go w taki sposób, aby nie generował zapytań o nieograniczonego rozmiaru.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Ocenianie symboli wieloznacznych i wyrażeń regularnych
 Azure Search używa oceny opartej na częstotliwościach ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) dla zapytań tekstowych. Jednakże w przypadku zapytań wieloznacznych i wyrażeń regularnych, w których zakres terminów może być bardzo szeroki, współczynnik częstotliwości jest ignorowany, aby zapobiec rozliczeniu na dopasowania od rzadkich warunków. Wszystkie dopasowania są traktowane równo w przypadku wyszukiwania symboli wieloznacznych i wyrażeń regularnych.

##  <a name="bkmk_fields"></a>Wyszukiwanie polowe  
Można zdefiniować operację wyszukiwania w polu z `fieldName:searchExpression` składnią, gdzie wyrażenie wyszukiwania może być pojedynczym słowem lub frazą lub bardziej skomplikowanym wyrażeniem w nawiasach, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:  

- Gatunek: nie historia Jazz  

- artyści:("mile Davis" "Jan Coltrane")

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane jako pojedyncze jednostki, w tym przypadku wyszukiwanie dwóch odrębnych artystów w `artists` polu.  

Pole określone w `fieldName:searchExpression` elemencie musi `searchable` być polem.  Aby uzyskać szczegółowe informacje na temat sposobu używania atrybutów indeksu w definicjach pól, zobacz [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) .  

> [!NOTE]
> W przypadku korzystania z `searchFields` wyrażeń wyszukiwania w polu nie trzeba używać parametru, ponieważ każde wyrażenie wyszukiwania w polu ma jawnie określoną nazwę pola. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić kwerendę, w której niektóre części są objęte zakresem określonego pola, a reszta może mieć zastosowanie do kilku pól. `search=genre:jazz NOT history&searchFields=description` Na przykład zapytanie byłoby zgodne `NOT history` `genre` `jazz` tylko z`description` polem, podczas gdy byłoby zgodne z polem. Nazwa pola podana `fieldName:searchExpression` w zawsze ma pierwszeństwo przed `searchFields` parametrem, co oznacza, że w tym przykładzie nie `searchFields` trzeba dołączać `genre` do parametru.

##  <a name="bkmk_fuzzy"></a>Wyszukiwanie rozmyte  
 Wyszukiwanie rozmyte wyszukuje dopasowania w warunkach, które mają podobną konstrukcję. W [dokumentacji](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)dotyczącej Lucene wyszukiwania rozmyte są oparte na [odległości Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Wyszukiwania rozmyte mogą rozszerzać termin do maksymalnie 50 warunków, które spełniają kryteria odległości. 

 Aby wykonać Wyszukiwanie rozmyte, Użyj symbolu "~" na końcu pojedynczego słowa z opcjonalnym parametrem, liczbą z przedziału od 0 do 2 (wartość domyślna), która określa odległość edycji. Na przykład "Blue ~" lub "Blue ~ 1" zwróci "Blue", "Blues" i "Glue".

 Wyszukiwanie rozmyte może być stosowane tylko do warunków, a nie fraz, ale do każdego terminu można dołączać pojedyncze części nazwy lub frazy. Na przykład "Unviersty ~ of ~" Wshington ~ "byłoby zgodne z" University of Waszyngton ".
 

##  <a name="bkmk_proximity"></a>Wyszukiwanie w sąsiedztwie  
 Wyszukiwania w sąsiedztwie są używane do znajdowania terminów blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, a po niej liczbę słów, które tworzą granicę bliskości. Na przykład `"hotel airport"~5` w dokumencie znajdą się terminy "Hotel" i "Lotnisko" w 5 wyrazach innych.  


##  <a name="bkmk_termboost"></a>Zwiększenie warunków  
 Zwiększenie warunków dotyczy klasyfikacji dokumentu, jeśli zawiera on podwyższony termin względem dokumentów, które nie zawierają warunków. Różni się to od profilów oceniania w tych profilach oceniania, a nie konkretnych warunków.  

Poniższy przykład pomaga zilustrować różnice. Załóżmy, że istnieje profil oceniania, który zwiększa zgodność w określonym polu, podyktuj *gatunek* w przykładowym [musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Zwiększenie okresu może służyć do dalszej promocji niektórych wyszukiwanych terminów wyższych niż inne. Na przykład `rock^2 electronic` program będzie ulepszał dokumenty, które zawierają terminy wyszukiwania w polu gatunek powyżej innych pól, które można wyszukiwać w indeksie. Ponadto dokumenty zawierające wyszukiwany termin *skały* są wyższe niż w przypadku innych wyszukiwanych warunków w postaci *elektronicznej* w wyniku okresu zwiększenia wartości (2).  

 Aby zwiększyć okres korzystania z karetki, "^", symbol z współczynnikem wzrostu (liczbą) na końcu wyszukiwanego okresu. Możesz również poprawić frazy. Im wyższy współczynnik zwiększania wydajności, tym bardziej istotny termin będzie odnosić się do innych wyszukiwanych terminów. Domyślnie współczynnik zwiększania wynosi 1. Chociaż współczynnik zwiększania wartości musi być dodatni, może być mniejszy niż 1 (na przykład 0,20).  

##  <a name="bkmk_regex"></a>Wyszukiwanie wyrażeń regularnych  
 Wyszukiwanie w wyrażeniu regularnym wyszukuje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Na przykład, aby znaleźć dokumenty zawierające "Motel" lub "Hotel", określ `/[mh]otel/`.  Wyszukiwania wyrażeń regularnych są dopasowywane do pojedynczych wyrazów.   

##  <a name="bkmk_wildcard"></a>Wyszukiwanie symboli wieloznacznych  
 Można użyć ogólnie rozpoznanej składni dla wielu symboli wieloznacznych (*) lub pojedynczych znaków (?). Zwróć uwagę, że Analizator zapytań Lucene obsługuje używanie tych symboli z pojedynczym terminem, a nie frazą.  

 Na przykład aby znaleźć dokumenty zawierające słowa z prefiksem "Uwaga", na przykład "Notes" lub "Notepad", należy określić "Uwaga *".  

> [!NOTE]  
>  Nie można użyć znaku * ani? Symbol jako pierwszy znak wyszukiwania.  
>  Nie jest przeprowadzana analiza tekstu dla zapytań wyszukiwania symboli wieloznacznych. W czasie zapytania symbole wieloznacznego zapytania są porównywane z przeanalizowanymi terminami w indeksie wyszukiwania i rozwinięte.

## <a name="see-also"></a>Zobacz także  

+ [Wyszukaj dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData dla filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Prosta Składnia zapytania w Azure Search](query-simple-syntax.md)   
