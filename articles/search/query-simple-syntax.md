---
title: Prosta Składnia zapytania — Azure Search
description: Odwołanie do prostej składni zapytania używanej na potrzeby zapytań wyszukiwania pełnotekstowego w Azure Search.
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
ms.openlocfilehash: e6c5ea86534001e0e5de2b02c4151af70631e4ef
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650008"
---
# <a name="simple-query-syntax-in-azure-search"></a>Prosta Składnia zapytania w Azure Search
Azure Search implementuje dwa języki zapytań opartych na Lucene: [Prosty Analizator zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [Analizator zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). W Azure Search, prosta Składnia zapytania wyklucza opcje rozmyte/slop.  

> [!NOTE]  
>  Azure Search oferuje alternatywną [składnię zapytań Lucene](query-lucene-syntax.md) dla bardziej złożonych zapytań. Aby dowiedzieć się więcej o architekturze analizy zapytań i korzyściach każdej z nich, zobacz [jak działa wyszukiwanie pełnotekstowe w Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Jak wywołać prostą analizę

Wartością domyślną jest prosta składnia. Wywołanie jest wymagane tylko w przypadku resetowania składni z pełny na prosty. Aby jawnie ustawić składnię, użyj `queryType` parametru wyszukiwania. Prawidłowe wartości to `simple|full`, z `simple` wartościami domyślnymi i `full` dla Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie zachowania zapytania

Dowolny tekst z co najmniej jednym warunkiem jest uznawany za prawidłowy punkt początkowy wykonywania zapytania. Azure Search będzie pasować do dokumentów zawierających dowolne lub wszystkie warunki, w tym wszelkie odmiany znalezione podczas analizy tekstu. 

Tak samo jak te dźwięki, istnieje jeden aspekt wykonywania zapytania w Azure Search, który *może* dawać nieoczekiwane wyniki, zwiększając zamiast zmniejszania wyników wyszukiwania, tak aby więcej warunków i operatorów zostało dodanych do ciągu wejściowego. Czy to rozwinięcie jest zależne od dołączenia operatora not, połączonego z `searchMode` ustawieniem parametru, które określa, jak nie jest interpretowane w warunkach i lub lub. Z uwzględnieniem operatora `searchMode=Any`default,, i not, operacja jest obliczana jako akcja or, która `"New York" NOT Seattle` zwraca wszystkie miasta, w których nie ma Seattle.  

Zazwyczaj można zobaczyć te zachowania w wzorcach interakcji użytkownika dla aplikacji, które przeszukują zawartość, w której użytkownicy mogą dołączać operator do zapytania, w przeciwieństwie do witryn handlu elektronicznego, które mają bardziej wbudowaną strukturę nawigacji. Aby uzyskać więcej informacji, zobacz [not operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatory logiczne (i, nie) 

Operatory można osadzić w ciągu zapytania, aby utworzyć bogaty zestaw kryteriów, względem których znaleziono pasujące dokumenty. 

### <a name="and-operator-"></a>AND — operator`+`

Operator AND jest znakiem plus. Na przykład `wifi+luxury` program przeszuka dokumenty zawierające zarówno `wifi` elementy, jak `luxury`i.

### <a name="or-operator-"></a>OR — operator`|`

Operator OR jest pionowym znakiem kreski lub potoku. Na przykład `wifi | luxury` program przeszuka dokumenty zawierające jeden `wifi` lub `luxury` oba te elementy.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT — operator`-`

Operator NOT jest znakiem minus. Program przeszuka na `wifi –luxury` przykład dokumenty, które `wifi` mają termin i/lub nie mają `luxury` (i/lub są kontrolowane przez `searchMode`program).

> [!NOTE]  
>  Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed, czy logicznie z innymi postanowieniami w zapytaniu w przypadku braku `+` operatora or `|` . Odwołaj tę `searchMode` wartość można ustawić na wartość `any` (domyślnie) lub `all`. Jeśli używasz `any`programu, spowoduje to zwiększenie odwołania do zapytań przez dołączenie większej liczby wyników, a `-` domyślnie będzie interpretowany jako "lub" nie ". Na przykład `wifi -luxury` program będzie pasował do dokumentów, które zawierają termin `wifi` lub te, które nie zawierają warunków. `luxury` Jeśli używasz `all`, zwiększy to precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowana jako "i nie". Na przykład `wifi -luxury` program będzie pasował do dokumentów zawierających termin `wifi` i nie zawiera terminu "możliwość zaprojektowania". Jest to raczej bardziej intuicyjne zachowanie `-` operatora. W związku z `searchMode=any` tym należy rozważyć `searchMode=all` użycie zamiast tego, jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji zamiast odwołania , `-` a użytkownicy często używają operatora w wyszukiwaniach.

## <a name="suffix-operator"></a>Operator sufiksu

Operatorem sufiksu jest gwiazdka `*`. Na przykład `lux*` program przeszuka wyszukiwanie dokumentów, które `lux`zaczynają się od, bez uwzględnienia wielkości liter.  

## <a name="phrase-search-operator"></a>Operator wyszukiwania frazy

Operator phrase umieszcza frazę w `" "`cudzysłowie. Na przykład podczas `Roach Motel` (bez cudzysłowów) Wyszukiwanie dokumentów zawierających `Roach` i/lub `Motel` wszędzie w dowolnej kolejności `"Roach Motel"` (z cudzysłowami) będzie pasować tylko do dokumentów, które zawierają całą frazę i w tym kolejność (analiza tekstu nadal dotyczy).

## <a name="precedence-operator"></a>Operator pierwszeństwa

Operator pierwszeństwa ujmuje ciąg w nawiasach `( )`. Na przykład `motel+(wifi | luxury)` program przeszuka dokumenty zawierające termin Motel `wifi` i `luxury` albo (lub oba).  

## <a name="escaping-search-operators"></a>Operatory wyszukiwania ucieczki  

 Aby można było użyć powyższych symboli jako rzeczywistej części tekstu wyszukiwania, powinny one zostać poddane ucieczki przy użyciu prefiksu odwróconej kreski ułamkowej. Na przykład `luxury\+hotel` spowoduje to powstanie terminu `luxury+hotel`. Aby elementy były proste dla bardziej typowych przypadków, istnieją dwa wyjątki od tej reguły, w których ucieczki nie są konieczne:  

- Operatora `-` not nie należy określać tylko wtedy, gdy jest to pierwszy znak po znaku, a nie w środku okresu. Na przykład jest `wi-fi` pojedynczym terminem, a identyfikatory GUID (takie jak `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) są traktowane jako pojedynczy token.
- Operatora `*` sufiksu należy zmienić tylko wtedy, gdy jest to ostatni znak przed odstępem, a nie w środku okresu. Na przykład, `wi*fi` jest traktowany jako pojedynczy token.

> [!NOTE]  
>  Mimo że ucieczki przechowuje tokeny, analiza tekstu może je podzielić, w zależności od trybu analizy. Aby uzyskać szczegółowe informacje, zobacz temat [Obsługa &#40;języków Azure Search Interfejs&#41; API REST usługi](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Zobacz także  

+ [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [OData expression syntax (Składnia wyrażenia OData)](query-odata-filter-orderby-syntax.md) 
