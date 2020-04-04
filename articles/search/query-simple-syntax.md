---
title: Prosta składnia zapytań
titleSuffix: Azure Cognitive Search
description: Odwołanie do składni kwerendy prostej używane dla kwerend wyszukiwania pełnotekstowego w usłudze Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
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
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656164"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Składnia kwerend prostych w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search implementuje dwa języki zapytań oparte na lucene: [analizator prostych zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [analizator zapytania lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). W usłudze Azure Cognitive Search składnia kwerendy proste wyklucza opcje rozmyte/niechlujstwa.  

> [!NOTE]
> Składnia kwerendy prostej jest używana dla wyrażeń kwerend przekazanych w parametrze **wyszukiwania** interfejsu API [dokumentów wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/search-documents) nie należy mylić z [składnią OData](query-odata-filter-orderby-syntax.md) używaną dla [$filter](search-filters.md) parametru tego interfejsu API. Te różne składnie mają własne reguły do konstruowania kwerend, ucieczki ciągów i tak dalej.
>
> Usługa Azure Cognitive Search udostępnia alternatywną [pełną składnię zapytania Lucene](query-lucene-syntax.md) dla bardziej złożonych zapytań w parametrze **wyszukiwania.** Aby dowiedzieć się więcej na temat architektury analizowania zapytań i zalet każdej składni, zobacz [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search.](search-lucene-query-architecture.md)

## <a name="how-to-invoke-simple-parsing"></a>Jak wywołać proste analizowanie

Prosta składnia jest domyślna. Wywołanie jest konieczne tylko wtedy, gdy resetujesz składnię od pełnej do prostej. Aby jawnie ustawić składnię, `queryType` należy użyć parametru wyszukiwania. Prawidłowe `simple|full`wartości `simple` obejmują , z `full` jako domyślne i Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie zachowania kwerendy

Każdy tekst z co najmniej jednym terminem jest uważany za prawidłowy punkt początkowy dla wykonywania kwerendy. Usługa Azure Cognitive Search będzie dopasowywanie dokumentów zawierających dowolne lub wszystkie terminy, w tym wszelkie odmiany znalezione podczas analizy tekstu. 

Tak proste, jak to brzmi, istnieje jeden aspekt wykonywania zapytań w usłudze Azure Cognitive Search, które *mogą* powodować nieoczekiwane wyniki, zwiększenie, a nie zmniejsza wyniki wyszukiwania, jak więcej terminów i operatorów są dodawane do ciągu wejściowego. Czy to rozszerzenie faktycznie występuje zależy od włączenia NOT `searchMode` operatora, w połączeniu z ustawieniem parametru, który określa, jak NIE jest interpretowany w kategoriach i lub lub zachowań. Biorąc pod `searchMode=Any`uwagę domyślne i OPERATOR NOT, operacja jest obliczana `"New York" NOT Seattle` jako akcja OR, w wyniku która zwraca wszystkie miasta, które nie są Seattle.  

Zazwyczaj są bardziej prawdopodobne, aby zobaczyć te zachowania w wzorcach interakcji z użytkownikami dla aplikacji, które wyszukują zawartość, gdzie użytkownicy są bardziej prawdopodobne, aby uwzględnić operatora w kwerendzie, w przeciwieństwie do witryn e-commerce, które mają więcej wbudowanych struktur nawigacji. Aby uzyskać więcej informacji, zobacz [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatory logiczne (AND, OR, NOT) 

Operatory można osadzać w ciągu zapytania, aby utworzyć bogaty zestaw kryteriów, względem których znajdują się pasujące dokumenty. 

### <a name="and-operator-"></a>Operator I`+`

Operator AND jest znakiem plus. Na przykład, `wifi+luxury` będzie wyszukiwać `wifi` dokumenty `luxury`zawierające zarówno i .

### <a name="or-operator-"></a>Operator OR`|`

Operator OR jest pionowym znakiem pręta lub rury. Na przykład `wifi | luxury` będzie wyszukiwać dokumenty `wifi` zawierające `luxury` jedną lub obie strony.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NIE operator`-`

Operator NOT jest znakiem minus. Na przykład, będzie `wifi –luxury` wyszukiwać `wifi` dokumenty, które mają `luxury` termin i/lub nie `searchMode`mają (i/lub jest kontrolowane przez ).

> [!NOTE]  
>  Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed lub ORed z `+` innymi `|` warunkami w kwerendzie w przypadku braku lub operatora. Przypomnijmy, że `searchMode` można `any` ustawić albo `all`(domyślnie) lub . Jeśli używasz `any`, zwiększy wycofywanie zapytań, dołączając więcej `-` wyników, a domyślnie będą interpretowane jako "LUB NIE". Na przykład `wifi -luxury` będzie pasować do dokumentów, które zawierają termin `wifi` `luxury`lub te, które nie zawierają terminu . Jeśli użyjesz `all`, zwiększy dokładność zapytań, dołączając mniej wyników, a domyślnie - będą interpretowane jako "I NIE". Na przykład `wifi -luxury` będzie pasować do `wifi` dokumentów, które zawierają termin i nie zawierają terminu "luksus". Jest to prawdopodobnie bardziej intuicyjne zachowanie `-` dla operatora. W związku z tym `searchMode=all` należy `searchMode=any` rozważyć użycie zamiast, jeśli chcesz zoptymalizować wyszukiwania dla precyzji `-` zamiast odwołania, *a* użytkownicy często używają operatora w wyszukiwaniach.

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>Operator sufiksu `*` wyszukiwania prefiksów

Operator sufiksu jest `*`gwiazdką . Na przykład `cap*` wyszuka dokumenty, które mają `cap`termin rozpoczynający się od , ignorując przypadek. 

Podobnie jak filtry, kwerenda prefiks szuka dopasowania dokładnego. W związku z tym nie ma znaczenia punktacji (wszystkie wyniki otrzymują wynik wyszukiwania 1.0). Kwerendy prefiksu może być powolny, zwłaszcza jeśli indeks jest duży, a prefiks składa się z niewielkiej liczby znaków. 

Jeśli chcesz wykonać kwerendę sufiks, pasujące w ostatniej części ciągu, użyj [wyszukiwania symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard) i pełnej składni Lucene.

## <a name="phrase-search-operator"></a>Operator wyszukiwania fraz

Operator frazy umieszcza frazę w `" "`cudzysłowie . Na przykład `Roach Motel` podczas gdy (bez cudzysłowów) `Motel` będzie wyszukiwać `"Roach Motel"` dokumenty zawierające `Roach` i/lub w dowolnym miejscu w dowolnej kolejności (z cudzysłowami) będzie pasować tylko do dokumentów, które zawierają tę całość frazy razem i w tej kolejności (analiza tekstu nadal ma zastosowanie).

## <a name="precedence-operator"></a>Operator pierwszeństwa

Operator pierwszeństwa otacza ciąg w nawiasach `( )`. Na przykład `motel+(wifi | luxury)` będzie wyszukiwać dokumenty zawierające termin `wifi` motelu i albo albo `luxury` (lub oba).  

## <a name="escaping-search-operators"></a>Ucieczka operatorów wyszukiwania  

 Aby użyć powyższych symboli jako rzeczywistej części wyszukiwanego tekstu, należy je zniknąć, prefiksując je ukośnikiem odwrotnym. Na przykład `luxury\+hotel` spowoduje to `luxury+hotel`termin . Aby ułatwić sprawy w bardziej typowych przypadkach, istnieją dwa wyjątki od tej reguły, w których ucieczka nie jest potrzebna:  

- Operator `-` NOT musi być tylko wysunął się, jeśli jest to pierwszy znak po odstępach, a nie, jeśli jest w środku terminu. Na przykład `wi-fi` jest to pojedynczy termin; mając na uwadze, że `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`identyfikatory GUID (takie jak) są traktowane jako pojedynczy token.
- Operator `*` sufiksu musi zostać zmieniony tylko wtedy, gdy jest to ostatni znak przed odstępami, a nie jeśli znajduje się w środku terminu. Na przykład `wi*fi` jest traktowany jako pojedynczy token.

> [!NOTE]  
>  Chociaż ucieczka utrzymuje tokeny razem, analiza tekstu może podzielić je, w zależności od trybu analizy. Zobacz [pomoc techniczna &#40;interfejsu API usługi Azure Cognitive Search REST&#41;,](index-add-language-analyzers.md) aby uzyskać szczegółowe informacje.  

## <a name="see-also"></a>Zobacz też  

+ [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia wyrażenia OData](query-odata-filter-orderby-syntax.md) 
