---
title: Prosta składnia zapytań — usługa Azure Search
description: Dokumentacja prosta składnia zapytań umożliwiający zapytaniach wyszukiwania pełnotekstowego w usłudze Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
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
ms.openlocfilehash: 75e2d7c493b535c984b0ef61dd9a9fae53aee80a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024202"
---
# <a name="simple-query-syntax-in-azure-search"></a>Prosta składnia zapytań w usłudze Azure Search
Usługa Azure Search implementuje dwóch języków zapytań Lucene: [Prosty analizator zapytań](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [analizator składni zapytań Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). W usłudze Azure Search prosta składnia zapytań nie obejmuje opcje rozmyte/odstojnika.  

> [!NOTE]  
>  Usługa Azure Search jest alternatywą [składnia zapytań Lucene](query-lucene-syntax.md) dla bardziej złożonych zapytań. Aby dowiedzieć się więcej na temat analizowania architektury i zalety każdego składni zapytania, zobacz [jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Jak wywołać prostą analizy

Prosta składnia jest ustawieniem domyślnym. Wywołanie jest niezbędny tylko jeśli są Resetowanie składni z pełnego na prosty. Aby jawnie ustawić składni, użyj `queryType` parametru wyszukiwania. Prawidłowe wartości to `simple|full`, za pomocą `simple` jako domyślne, a `full` dla Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie zachowanie zapytania

Dowolny tekst z jednego lub większej liczby terminów jest uważany za prawidłowy punkt początkowy w celu wykonywania zapytań. Usługa Azure Search będzie pasować do dokumentów zawierających dowolne lub wszystkie warunki, włącznie ze wszystkimi wariantami znaleziony podczas analizy tekstu. 

Jako ten dźwięki utrudnione jest jednym z aspektów wykonywania zapytań w usłudze Azure Search, *może* produktu nieoczekiwanych wyników, zwiększając, a nie zmniejsza wyszukiwanie wyników, ponieważ coraz więcej warunków i operatory są dodawane do danych wejściowych ciąg. Czy to rozwinięcie rzeczywiście występuje zależy od włączenia operatora NOT, w połączeniu z `searchMode` ustawienie parametru, który określa sposób nie jest interpretowany na podstawie i lub OR zachowania. Podana wartość domyślna `searchMode=Any`, i operator NOT operacji jest obliczana jako akcję lub takie, że `"New York" NOT Seattle` zwraca wszystkich miast, które nie są Seattle.  

Zwykle jest bardziej prawdopodobne wyświetlić te zachowania we wzorcach interakcji użytkownika dla aplikacji, które przeszukiwać zawartość, której użytkownicy są bardziej prawdopodobne uwzględnić operator w zapytaniu, w przeciwieństwie do witryny handlu elektronicznego, które mają więcej wbudowanych struktury nawigacji. Aby uzyskać więcej informacji, zobacz [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatory logiczne (AND, OR, NOT) 

Operatorzy mogą osadzać w ciągu zapytania, aby zbudować bogaty zestaw kryteriów, wobec których zostaną znalezione pasujących dokumentów. 

### <a name="and-operator-"></a>AND — operator `+`

Operator i jest znak plus. Na przykład `wifi+luxury` wyszuka dokumentów zawierających zarówno `wifi` i `luxury`.

### <a name="or-operator-"></a>OR — operator `|`

OR operator jest pionowy pasek lub znaku kreski pionowej. Na przykład `wifi | luxury` wyszuka dokumenty zawierające jedną `wifi` lub `luxury` lub obu.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT — operator `-`

Operator nie jest znakiem minus. Na przykład `wifi –luxury` wyszukiwanie dokumentów, których `wifi` termin i/lub nie masz `luxury` (i/lub jest kontrolowana przez `searchMode`).

> [!NOTE]  
>  `searchMode` Opcji kontrolki, czy termin za pomocą operatora nie jest wykonywana operacja logiczna inne warunki zapytania w przypadku braku `+` lub `|` operatora. Pamiętamy `searchMode` może być ustawiony na `any` (ustawienie domyślne) lub `all`. Jeśli używasz `any`, zwiększy odwołania zapytań, tym więcej wyników i domyślnie `-` będzie interpretowana jako "Lub NOT". Na przykład `wifi -luxury` będzie pasował do dokumentów, albo zawierają wyrażenia `wifi` lub tych, które nie zawierają termin `luxury`. Jeśli używasz `all`, zwiększy dokładność zapytania umieszczając mniejszej liczby wyników i domyślnie -, będzie interpretowany jako ", a nie". Na przykład `wifi -luxury` będzie pasował do dokumentów, które zawierają termin `wifi` i nie zawierają termin "luksusowe". Prawdopodobnie jest to bardziej intuicyjne zachowanie `-` operatora. W związku z tym, należy rozważyć użycie `searchMode=all` zamiast `searchMode=any` Jeśli chcesz zoptymalizować wyszukuje dokładności zamiast odwołania, *i* użytkownicy często korzystają z `-` operatora w wynikach wyszukiwania.

## <a name="suffix-operator"></a>Operator sufiks

Operator sufiks jest znak gwiazdki `*`. Na przykład `lux*` wyszukiwanie dokumentów, które mają termin, który rozpoczyna się od `lux`, bez uwzględnienia wielkości liter.  

## <a name="phrase-search-operator"></a>Operator wyszukiwania fraz

Operator frazy otacza frazę w cudzysłów `" "`. Na przykład `Roach Motel` (bez cudzysłowu) będzie wyszukiwać dokumenty zawierające `Roach` i/lub `Motel` w dowolnej kolejności, w dowolnym miejscu `"Roach Motel"` (z cudzysłowami) spowoduje dopasowanie tylko dokumentów zawierających tego cała fraza razem i w tym kolejność (nadal obowiązuje ograniczenie Analiza tekstu).

## <a name="precedence-operator"></a>Pierwszeństwo operatorów

Operator pierwszeństwo dodaje ciągu w nawiasach `( )`. Na przykład `motel+(wifi | luxury)` wyszuka dokumenty zawierające termin motel, a następnie `wifi` lub `luxury` (lub obu).  

## <a name="escaping-search-operators"></a>Anulowanie operatory wyszukiwania  

 Aby można było używać powyżej symbole w ramach rzeczywistego wyszukiwanego tekstu, powinny być wyjściowym przez dodanie przedrostka je znakiem kreski ułamkowej odwróconej. Na przykład `luxury\+hotel` spowoduje termin `luxury+hotel`. W celu rzeczy prosty bardziej typowe przypadki, istnieją dwa wyjątki związane z tą regułą gdzie anulowania zapewnianego element nie jest potrzebne:  

- NOT operator `-` tylko musi być poprzedzone znakiem zmiany znaczenia jeśli pierwszym znakiem jest po godzinie odstępu, nie wtedy, gdy jest w trakcie czasu trwania umowy. Na przykład `wi-fi` jest terminem pojedynczego; dlatego identyfikatory GUID (takie jak `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) są traktowane jako pojedynczy token.
- Operatora sufiksowego `*` musi być poprzedzone znakiem zmiany znaczenia tylko wtedy, gdy jest to ostatni znak przed odstępu, nie wtedy, gdy jest w trakcie czasu trwania umowy. Na przykład `wi*fi` jest traktowany jako pojedynczy token.

> [!NOTE]  
>  Mimo że anulowania zapewnianego element przechowuje tokeny ze sobą, analiza tekstu może podzielić je, w zależności od trybu analizy. Zobacz [języki &#40;interfejsu API REST usługi Azure Search&#41; ](index-add-language-analyzers.md) Aby uzyskać szczegółowe informacje.  

## <a name="see-also"></a>Zobacz także  

+ [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [OData expression syntax (Składnia wyrażenia OData)](query-odata-filter-orderby-syntax.md) 
