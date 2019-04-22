---
title: Składnia zapytań Lucene — usługa Azure Search
description: Odwołanie do pełnej składni Lucene, które jest używane z usługą Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 64a688df3b6ed8602bb440d72e7f061c5f5893d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885608"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Składnia zapytań Lucene w usłudze Azure Search
Można napisać zapytań względem usługi Azure Search oparte na zaawansowanych [analizator składni zapytań Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) składnia wyspecjalizowane kwerendy forms: symbole wieloznaczne, Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie wyrażeń regularnych przedstawiono kilka przykładów. Jest wiele składni analizatora zapytań Lucene [zaimplementowane opublikowane w usłudze Azure Search](search-lucene-query-architecture.md), z wyjątkiem produktów *zakresu wyszukiwania* zbudowanych w usłudze Azure Search przy użyciu `$filter` wyrażenia. 

## <a name="how-to-invoke-full-parsing"></a>Jak wywołać pełnej analizy

Ustaw `queryType` wyszukiwania parametru, aby określić które analizatora składni do użycia. Prawidłowe wartości to `simple|full`, za pomocą `simple` jako domyślne, a `full` dla Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Przykład przedstawiający pełnej składni

Poniższy przykład znajduje dokumenty do indeksu przy użyciu składni zapytań Lucene, widoczne w `queryType=full` parametru. Ta kwerenda zwraca hotele, gdzie pole kategorii zawiera termin "budget" i wszystkie pola z możliwością wyszukiwania zawierające frazę "ostatnio renovated". Dokumenty zawierające frazę "ostatnio renovated" są wyżej w wyniku wartość boost termin (3).  

`searchMode=all` Parametr ma zastosowanie w tym przykładzie. Zawsze, gdy operatory są na tym zapytaniu, zazwyczaj należy ustawić `searchMode=all` do upewnij się, że *wszystkich* kryteriów jest zgodny.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full
```

 Można również użyć POST:  

```
POST /indexes/hotels/docs/search?api-version=2015-02-28
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Aby uzyskać więcej przykładów, zobacz [przykłady składni zapytań Lucene do tworzenia zapytań w usłudze Azure Search](search-query-lucene-examples.md). Aby uzyskać szczegółowe informacje dotyczące określania pełnej warunkowe parametrów zapytania, zobacz [wyszukiwania dokumentów &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Usługa Azure Search obsługuje również [prosta składnia zapytań](query-simple-syntax.md), prostego i niezawodnego zapytania języka, który może służyć do wyszukiwania słów kluczowych proste.  

##  <a name="bkmk_syntax"></a> Podstawy składni  
 Następujące podstawy składni mają zastosowanie do wszystkich zapytań, które należy użyć składni Lucene.  

### <a name="operator-evaluation-in-context"></a>Operator oceny w kontekście

Umieszczanie Określa, czy symbol jest interpretowany jako operator lub po prostu inny znak w ciągu.

Na przykład w pełnej składni Lucene tyldy (~) jest używany zarówno Wyszukiwanie rozmyte i wyszukiwanie w sąsiedztwie. Po umieszczeniu za frazę w cudzysłowie, ~ wywołuje wyszukiwanie w sąsiedztwie. Jeśli znajduje się na koniec okresu obowiązywania, ~ wywołuje wyszukiwania rozmytego.

W ramach termin takie jak "firm ~ analityka", znak nie jest oceniany jako operator. W tym przypadku zakładając, że zapytanie jest zapytaniem termin lub frazę [wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md) z [poddawać analizie leksykalnej](search-lucene-query-architecture.md#stage-2-lexical-analysis) musiał ~ i przerywa termin "firm ~ analityka" w dwóch: biznesowych lub analityka.

W powyższym przykładzie jest tyldy (~), ale ta sama zasada ma zastosowanie do każdego operatora.

### <a name="escaping-special-characters"></a>Znaki specjalne wyjścia

 Znaki specjalne, należy użyć znaków ucieczki ma być używany jako część tekstu wyszukiwania. Można je pominąć dodając kreski ułamkowej odwróconej (\\). Następujące znaki specjalne, które muszą być poprzedzone znakiem zmiany znaczenia:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Na przykład jako znak ucieczki dla symbolu wieloznacznego, należy użyć \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie znaków ryzykowne i zarezerwowane adresy URL

 Upewnij się, że wszystkie znaki niebezpieczne i zarezerwowanych są zakodowane w adresie URL. Na przykład '#' jest znakiem niebezpieczne, ponieważ jest to identyfikator fragement/zakotwiczenia w adresie URL. Znak musi być zakodowany na `%23` Jeśli używany w adresie URL. "&" i "=" to przykłady znaków zastrzeżonych zgodnie z ich ograniczania parametrów i określ wartości w usłudze Azure Search. Zobacz [RFC1738: Jednolity lokalizatorów zasobów (URL)](https://www.ietf.org/rfc/rfc1738.txt) Aby uzyskać więcej informacji.

 Niebezpieczne znaki są ``" ` < > # % { } | \ ^ ~ [ ]``. Zastrzeżone znaki są `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Pierwszeństwo operatorów: grupowanie i grupowanie pól  
 Nawiasy umożliwia tworzenie podzapytań, łącznie z operatorów w nawiasach instrukcji. Na przykład `motel+(wifi||luxury)` wyszuka dokumenty zawierające termin "motel" oraz "Wi-Fi" lub "luksusowe" (lub obu).

Grupowanie pola jest podobne, ale zakresy grupowania do pojedynczego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` wyszukuje pole "hotelAmenities" "w przypadku treningów" i "Wi-Fi" lub "siłowni" i "puli".  

### <a name="searchmode-parameter-considerations"></a>Zagadnienia dotyczące parametru SearchMode  
 Wpływ `searchMode` zapytań, zgodnie z opisem w [prosta składnia zapytań w usłudze Azure Search](query-simple-syntax.md), stosuje się jednakowo do składni zapytań Lucene. To znaczy `searchMode` w połączeniu z użyciem NOT operatorów może spowodować wyników zapytania, które mogą wydawać się nietypowe, jeśli nie masz jasno na efekty jak ustawić parametr. Jeśli Zachowaj ustawienie domyślne, `searchMode=any`i użyj operatora NOT, operacji jest obliczana jako akcję lub w taki sposób, że "New York" nie "Seattle" zwraca wszystkich miast, które nie są Seattle.  

##  <a name="bkmk_boolean"></a> Operatory logiczne (AND, OR, NOT) 
 Zawsze należy określić tekst operatorów logicznych (AND, OR, NOT) w całości wielkimi.  

### <a name="or-operator-or-or-"></a>OR operator `OR` lub `||`

OR operator jest pionowy pasek lub znaku kreski pionowej. Na przykład: `wifi || luxury` wyszuka dokumenty zawierające "Wi-Fi" lub "luksusowe" lub obu. Ponieważ lub jest operatorem połączeniu domyślne, można także pozostawić ją tak, aby `wifi luxury` jest odpowiednikiem `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>AND — operator `AND`, `&&` lub `+`

Operator i jest handlowe "i" lub znak plus. Na przykład: `wifi && luxury` wyszuka dokumenty zawierające "Wi-Fi" i "luksusowe". Znak plus (+) jest używany dla wymaganych warunków. Na przykład `+wifi +luxury` stanowi, że oba warianty pojęć musi znajdować się gdzieś w polu pojedynczego dokumentu.


### <a name="not-operator-not--or--"></a>NOT operator `NOT`, `!` lub `-`

Operator nie jest znakiem wykrzyknika lub znaku minus. Na przykład: `wifi !luxury` będzie wyszukiwał dokumenty zawierające "sieć Wi-Fi" termin i/lub nie masz "luksusowe". `searchMode` Opcji kontrolki, czy termin za pomocą operatora nie jest wykonywana operacja logiczna inne warunki zapytania w przypadku braku + lub || — operator. Pamiętamy `searchMode` może być ustawiony na `any`(ustawienie domyślne) lub `all`.

Za pomocą `searchMode=any` zwiększa odwołania zapytań, umieszczając więcej wyników i domyślnie — będzie interpretowana jako "Lub NOT". Na przykład `wifi -luxury` będzie pasował do dokumentów, albo zawierają wyrażenia *sieci Wi-Fi* lub tych, które nie zawierają termin *luksusowe.*

Za pomocą `searchMode=all` zwiększa dokładność zapytań, umieszczając mniejszej liczby wyników i domyślnie — będzie interpretowana jako ", a nie". Na przykład `wifi -luxury` będzie pasował do dokumentów, które zawierają termin `wifi` i nie może zawierać termin `luxury`. Prawdopodobnie jest to bardziej intuicyjne zachowanie operatora. W związku z tym, należy rozważyć, wybierając `searchMode=all` za pośrednictwem `searchMode=any` Jeśli chcesz zoptymalizować wyszukuje dokładności zamiast odwołania *i* użytkownicy często korzystają z `-` operatora w wynikach wyszukiwania.

##  <a name="bkmk_querysizelimits"></a> Ograniczenia rozmiaru zapytania  
 Obowiązuje limit rozmiaru zapytania, których można wysłać do usługi Azure Search. Ściślej mówiąc możesz mieć maksymalnie 1024 klauzule (wyrażeń oddzielonych AND, OR i tak dalej). Istnieje również limit wynosi około 32 KB rozmiaru okresu poszczególnych w zapytaniu. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zaleca się projektowanie ją w taki sposób, że nie generuje zapytania o rozmiarze niepowiązane.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Ocenianie zapytań symboli wieloznacznych i wyrażeń regularnych
 Usługa Azure Search korzysta z oceniania na podstawie częstotliwości ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) dla tekstu zapytania. Jednak dla symboli wieloznacznych i wyrażeń regularnych zapytań, gdzie zakres warunków potencjalnie może być szerokie, współczynnik częstotliwości jest ignorowany, aby zapobiec klasyfikacji z odchylenia opóźnienia kierunku zgodne z warunkami rzadkich. Wszystkie dopasowania są traktowane identycznie do symboli wieloznacznych i wyrażeń regularnych wyszukiwania.

##  <a name="bkmk_fields"></a> Zapytania należące do pola  
 Można określić `fieldname:searchterm` konstrukcji, aby zdefiniować operacji fielded zapytania, gdzie pole jest pojedynczego wyrazu, a termin wyszukiwania jest również pojedynczego wyrazu lub frazy, opcjonalnie wraz z operatorami logicznymi. Oto kilka przykładów:  

- gatunku: jazz nie historii  

- artists:("Miles Davis" "John Coltrane")

  Pamiętaj umieścić wielu ciągów w cudzysłowach, chcąc obu ciągów, które ma zostać obliczone jako pojedyncza jednostka, w tym przypadku wyszukiwanie dwa odrębne artystów w `artists` pola.  

  Pole określone w `fieldname:searchterm` musi być `searchable` pola.  Zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) szczegółowe informacje na temat używania atrybuty indeksu w definicji pola.  

##  <a name="bkmk_fuzzy"></a> Wyszukiwanie rozmyte  
 Wyszukiwanie rozmyte znajduje dopasowań w warunkach, które mają podobne konstrukcji. Na [dokumentacji Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), Wyszukiwanie rozmyte opierają się na [odległość Damerau Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Wyszukiwanie rozmyte można rozwinąć termin maksymalnie 50 warunki, które spełniają kryteria odległości. 

 Aby wykonać wyszukiwanie rozmyte, należy użyć tylda "~" symbolu na końcu pojedynczego wyrazu z opcjonalnym parametrem, liczbą z zakresu od 0 do 2 (ustawienie domyślne), określającą odległość edycji. Na przykład "niebieski ~" lub "niebieski ~ 1" zwróci "niebieski", "blues" i "skleić".

 Wyszukiwanie rozmyte można stosować tylko na warunki, nie fraz, ale możesz dołączyć tylda na każdym kolejnym indywidualnie nazwy wieloczęściowej lub frazy. Na przykład "Unviersty ~ z ~" Wshington ~ "umożliwi dopasowanie na"Uniwersytet Warszawski".
 

##  <a name="bkmk_proximity"></a> Wyszukiwanie w sąsiedztwie  
 Wyszukiwanie w sąsiedztwie służą do znajdowania warunki znajdujących się blisko siebie w dokumencie. Wstaw tyldy "~" symbolu na końcu frazy następuje liczbę słów, które Utwórz granicę odległości między elementami. Na przykład `"hotel airport"~5` znajdują się warunki "hotel" oraz "port lotniczy" w ciągu 5 słów od siebie w dokumencie.  


##  <a name="bkmk_termboost"></a> promowanie  
 Promowanie terminów odnosi się do dokumentu większe, jeśli zawiera on wzmocnione termin względem dokumentów, które nie zawierają termin klasyfikacji. To różni się od profile oceniania, w tym profile oceniania zwiększania niektóre pola, a nie konkretne terminy.  

Poniższy przykład pomoże zilustrować różnice. Załóżmy, że istnieje oceniania profilu, co zwiększa przywiązanie jest zgodna w polu Załóżmy, że *gatunku* w [przykład musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). Promowanie terminów może służyć do dalszych Zwiększ niektórych wyszukiwania wyższe niż inne postanowienia. Na przykład `rock^2 electronic` spowoduje zwiększenie dokumentów, które zawierają terminy wyszukiwania w polu gatunku wyższa niż inne pola z możliwością wyszukiwania w indeksie. Dalsze dokumentów zawierających wyszukiwany termin *rock* zostanie wyznaczona ranga wyższe niż wyszukiwany termin *elektronicznych* w wyniku wartość boost termin (2).  

 Aby zwiększyć możliwości termin, użyj daszek, "^", symbol ze współczynnikiem boost (liczba) na końcu okresu, w przypadku wyszukiwania. Może również zwiększyć fraz. Im wyższa współczynnika zwiększenie wydajności, tym większe znaczenie termin będzie względem innych terminy wyszukiwania. Domyślnie współczynnik boost wynosi 1. Chociaż współczynnik boost musi być dodatnią, może być mniejsza niż 1 (na przykład 0.20 lub nowszej).  

##  <a name="bkmk_regex"></a> wyszukiwanie wyrażenia regularnego  
 Wyszukiwanie wyrażenia regularnego znalezienia dopasowania na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasy RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Na przykład, aby znaleźć dokumenty zawierające "motel" lub "hotel", należy określić `/[mh]otel/`.  Wyrażenie regularne wyszukiwania są dopasowywane do pojedynczego słowa.   

##  <a name="bkmk_wildcard"></a> Wyszukiwanie symboli wieloznacznych  
 Możesz użyć powszechnie składni pojedynczych lub do wielu (*) (?) znak symbolu wieloznacznego wyszukiwania. Należy pamiętać, że analizator składni zapytań Lucene obsługuje korzystanie z tych symboli za pomocą pojedynczego terminu i nie frazę.  

 Na przykład aby znaleźć dokumenty zawierające słowa z prefiksem "note", takich jak "Notes" lub "Notatnik", określ "Uwaga *".  

> [!NOTE]  
>  Nie można użyć * lub? symbol jako pierwszego znaku wyszukiwania.  
>  Żadnej analizy tekstu jest wykonywana na zapytania wyszukiwania symboli wieloznacznych. Podczas przeszukiwania terminów zapytania symboli wieloznacznych są porównywane przeanalizowany warunków w indeksie wyszukiwania i rozszerzona.

## <a name="see-also"></a>Zobacz także  

+ [Wyszukaj dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia wyrażenia OData do filtrów i sortowania](query-odata-filter-orderby-syntax.md)   
+ [Prosta składnia zapytań w usłudze Azure Search](query-simple-syntax.md)   
