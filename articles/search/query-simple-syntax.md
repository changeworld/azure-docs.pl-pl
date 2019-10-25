---
title: Składnia prostego zapytania
titleSuffix: Azure Cognitive Search
description: Odwołanie do prostej składni zapytania używanej na potrzeby zapytań wyszukiwania pełnotekstowego w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792552"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Prosta Składnia zapytania w usłudze Azure Wyszukiwanie poznawcze

Platforma Azure Wyszukiwanie poznawcze implementuje dwa języki zapytań opartych na Lucene: [prosty Analizator zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [Analizator zapytań Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). W przypadku usługi Azure Wyszukiwanie poznawcze prosta Składnia zapytania wyklucza opcje rozmyte/slop.  

> [!NOTE]  
>  Usługa Azure Wyszukiwanie poznawcze udostępnia alternatywną [składnię zapytań Lucene](query-lucene-syntax.md) dla bardziej złożonych zapytań. Aby dowiedzieć się więcej o architekturze analizy zapytań i korzyściach każdej z nich, zobacz [jak działa wyszukiwanie pełnotekstowe w usłudze Azure wyszukiwanie poznawcze](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Jak wywołać prostą analizę

Wartością domyślną jest prosta składnia. Wywołanie jest wymagane tylko w przypadku resetowania składni z pełny na prosty. Aby jawnie ustawić składnię, użyj parametru wyszukiwania `queryType`. Prawidłowe wartości to `simple|full`, z `simple` jako domyślne i `full` dla Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalie zachowania zapytania

Dowolny tekst z co najmniej jednym warunkiem jest uznawany za prawidłowy punkt początkowy wykonywania zapytania. Usługa Azure Wyszukiwanie poznawcze będzie pasować do dokumentów zawierających dowolne lub wszystkie warunki, w tym wszelkie odmiany znalezione podczas analizy tekstu. 

Tak samo jak te dźwięki, istnieje jeden aspekt wykonywania zapytania w usłudze Azure Wyszukiwanie poznawcze, który *może* dawać nieoczekiwane wyniki, zwiększając zamiast zmniejszania wyników wyszukiwania, tak aby więcej warunków i operatorów zostało dodanych do ciągu wejściowego. Czy to rozwinięcie jest zależne od dołączenia operatora NOT, połączonego z `searchMode` ustawieniem parametru, które określa, jak nie jest interpretowane w warunkach i lub lub. Z uwzględnieniem domyślnych, `searchMode=Any`i operatora NOT, operacja jest obliczana jako akcja lub, taka, która `"New York" NOT Seattle` zwraca wszystkie miasta, w których nie ma Seattle.  

Zazwyczaj można zobaczyć te zachowania w wzorcach interakcji użytkownika dla aplikacji, które przeszukują zawartość, w której użytkownicy mogą dołączać operator do zapytania, w przeciwieństwie do witryn handlu elektronicznego, które mają bardziej wbudowaną strukturę nawigacji. Aby uzyskać więcej informacji, zobacz [not operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operatory logiczne (i, nie) 

Operatory można osadzić w ciągu zapytania, aby utworzyć bogaty zestaw kryteriów, względem których znaleziono pasujące dokumenty. 

### <a name="and-operator-"></a>I `+` operatora

Operator AND jest znakiem plus. Na przykład `wifi+luxury` będzie wyszukiwać dokumenty zawierające zarówno `wifi`, jak i `luxury`.

### <a name="or-operator-"></a>LUB `|` operatora

Operator OR jest pionowym znakiem kreski lub potoku. Na przykład `wifi | luxury` będzie wyszukiwać dokumenty zawierające `wifi` lub `luxury` lub oba te elementy.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NIE `-` operatora

Operator NOT jest znakiem minus. Na przykład `wifi –luxury` będzie wyszukiwać dokumenty, które mają `wifi` termin i/lub nie mają `luxury` (i/lub są kontrolowane przez `searchMode`).

> [!NOTE]  
>  Opcja `searchMode` określa, czy termin z operatorem NOT jest ANDed czy logicznie z innymi postanowieniami w zapytaniu w przypadku braku `+` lub `|` operatora. Odwołaj tę `searchMode` można ustawić na `any` (domyślne) lub `all`. Jeśli używasz `any`, zwiększy to odwołanie zapytań, dołączając więcej wyników, a domyślnie `-` będzie interpretowany jako "lub". Na przykład `wifi -luxury` będzie pasować do dokumentów, które zawierają termin `wifi` lub te, które nie zawierają terminu `luxury`. Jeśli używasz `all`, zwiększy to precyzję zapytań, dołączając mniejszą liczbę wyników i domyślnie — będzie interpretowana jako "i nie". Na przykład `wifi -luxury` będzie pasować do dokumentów zawierających termin `wifi` i nie zawierają terminu "możliwość zaprojektowania". Jest to raczej bardziej intuicyjne zachowanie dla operatora `-`. W związku z tym należy rozważyć użycie `searchMode=all` zamiast `searchMode=any`, jeśli chcesz zoptymalizować wyszukiwanie pod kątem precyzji zamiast odwołania, *a* użytkownicy często używają operatora `-` w wyszukiwaniach.

## <a name="suffix-operator"></a>Operator sufiksu

Operatorem sufiksu jest gwiazdka `*`. Na przykład `lux*` będzie wyszukiwać dokumenty z terminem rozpoczynającym się od `lux`, bez uwzględnienia wielkości liter.  

## <a name="phrase-search-operator"></a>Operator wyszukiwania frazy

Operator phrase ujmuje frazę w cudzysłowie `" "`. Na przykład, podczas `Roach Motel` (bez cudzysłowów) Wyszukiwanie dokumentów zawierających `Roach` i/lub `Motel` w dowolnym porządku, `"Roach Motel"` (z cudzysłowami) będzie pasować tylko do dokumentów zawierających całą frazę i w tej kolejności (analiza tekstu nadal ma zastosowanie).

## <a name="precedence-operator"></a>Operator pierwszeństwa

Operator pierwszeństwa ujmuje ciąg w nawiasach `( )`. Na przykład `motel+(wifi | luxury)` będzie wyszukiwać dokumenty zawierające termin Motel i `wifi` lub `luxury` (lub oba te elementy).  

## <a name="escaping-search-operators"></a>Operatory wyszukiwania ucieczki  

 Aby można było użyć powyższych symboli jako rzeczywistej części tekstu wyszukiwania, powinny one zostać poddane ucieczki przy użyciu prefiksu odwróconej kreski ułamkowej. Na przykład `luxury\+hotel` spowoduje `luxury+hotel`terminu. Aby elementy były proste dla bardziej typowych przypadków, istnieją dwa wyjątki od tej reguły, w których ucieczki nie są konieczne:  

- Operatora NOT `-` należy zmienić tylko wtedy, gdy jest to pierwszy znak po znaku, a nie w środku okresu. Na przykład `wi-fi` jest pojedynczym terminem; identyfikatory GUID (takie jak `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) są traktowane jako pojedynczy token.
- `*` operatora sufiksu należy zmienić tylko wtedy, gdy jest to ostatni znak przed odstępem, a nie w środku okresu. Na przykład `wi*fi` jest traktowany jako pojedynczy token.

> [!NOTE]  
>  Mimo że ucieczki przechowuje tokeny, analiza tekstu może je podzielić, w zależności od trybu analizy. Aby uzyskać szczegółowe informacje, zobacz temat [Obsługa &#40;języka Azure wyszukiwanie poznawcze REST API&#41; ](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Zobacz także  

+ [Wyszukaj dokumenty &#40;w interfejsie API REST usługi Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [OData expression syntax (Składnia wyrażenia OData)](query-odata-filter-orderby-syntax.md) 
