---
title: Interpretacja semantyczna — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interpretacja semantyczna w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814471"
---
# <a name="semantic-interpretation"></a>Interpretacja semantyczna

Interpretacja semantyczna kojarzy semantycznego dane wyjściowe z każdej ścieżki interpretowanych za pośrednictwem gramatyki.  W szczególności usługa oblicza sekwencja instrukcji w `tag` przejść elementów interpretacji do obliczenia z końcowych danych wyjściowych.  

Instrukcja może być przypisanie literałem lub zmienną do innej zmiennej.  On również przypisać dane wyjściowe funkcji z atrybutem 0 lub większą liczbę parametrów do zmiennej.  Każdy parametr funkcji może być określona za pomocą literałem lub zmienną.  Jeśli funkcja nie zwraca żadnych danych wyjściowych, przypisanie zostanie pominięty.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Zmienna jest określona za pomocą identyfikator nazwy, który rozpoczyna się od litery i zawiera tylko litery (A – Z), cyfry (0 – 9) i znak podkreślenia (\_).  Niejawnie wywnioskować jej typu z literału lub funkcja wyjścia wartości przypisanej do niego. 

Poniżej znajduje się lista aktualnie obsługiwanych typów danych:

|Typ|Opis|Przykłady|
|----|----|----|
|String|Sekwencja 0 lub więcej znaków|"Hello World!"<br/>""|
|Bool|Wartość logiczna|true<br/>false|
|Int32|całkowita 32-bitowych.  -2.1e9 do 2.1e9|123<br/>-321|
|Int64|Całkowita 64-bitowych. -9.2e18 i 9.2e18|9876543210|
|Double|Zmiennoprzecinkowe podwójnej precyzji. 308 (15 cyfr) 1, 7e|123.456789<br/>1.23456789e2|
|Guid|Unikatowy identyfikator globalny|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Zapytanie|Wyrażenie zapytania, które określa podzbiór obiektów danych w indeksie|All()<br/>I (*q1*, *q2*)|

## <a name="semantic-functions"></a>Funkcje semantyczne

Istnieje zestaw wbudowanych funkcji semantycznego.  Zezwalaj na construction zaawansowanych zapytań i zapewniają kontekstowe kontrolę nad interpretacji gramatyki.

### <a name="and-function"></a>I — funkcja

`query = And(query1, query2);`

Zwraca zapytanie składające się z część wspólną dwóch danych wejściowych zapytań.

### <a name="or-function"></a>Lub — funkcja

`query = Or(query1, query2);`

Zwraca zapytanie składające się z sumę dwóch danych wejściowych zapytań.

### <a name="all-function"></a>Wszystkie funkcje

`query = All();`

Zwraca zapytanie, które zawiera wszystkie obiekty danych.

W poniższym przykładzie używamy funkcji All() iteracyjne zaczynały być zapytania opartego na przecięciu 1 lub więcej słów kluczowych.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Brak funkcji

`query = None();`

Zwraca zapytanie, które zawiera żadnych obiektów danych.

W poniższym przykładzie używamy funkcji None() iteracyjne zaczynały być zapytań, w zależności od Unii 1 lub więcej słów kluczowych.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Zapytania — funkcja

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Zwraca zapytanie, które zawiera tylko obiekty danych, których atrybut *attrName* zgodna z wartością *wartość* według określonej operacji *op*, którego wartość domyślna to "eq".  Zazwyczaj używane `attrref` element, aby utworzyć zapytanie na podstawie ciągu kwerendy wejściowej dopasowane.  Jeśli wartość jest podany lub uzyskane w inny sposób, funkcja Query() można utworzyć kwerendę dopasowania tę wartość.

W poniższym przykładzie używamy funkcji Query() zaimplementować obsługę określania akademickich publikacje z dziesiętnym.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Composite — funkcja

`query = Composite(innerQuery);`

Zwraca zapytanie, która hermetyzuje *innerQuery* składa się z odpowiedników podrzędnych atrybuty wspólny atrybut złożonego *attr*.  Hermetyzacja wymaga złożonej atrybutu *attr* dowolnego zgodnego obiektu danych, mieć co najmniej jedną wartość, która spełnia indywidualnie *innerQuery*.  Pamiętaj, że zapytania podrzędne atrybutów złożonego atrybutu musi być zhermetyzowany przy użyciu funkcji Composite(), zanim można łączyć z innymi zapytaniami.

Na przykład następujące zapytanie zwraca akademickich publikacje według "harry shum" podczas zajmował w "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Z drugiej strony następujące zapytanie zwraca akademickich publikacje, gdzie jest on autorzy "harry shum" i jednym przynależności to "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable — funkcja

`value = GetVariable(name, scope);`

Zwraca wartość zmiennej *nazwa* zdefiniowanego w określonym *zakres*.  *Nazwa* to identyfikator zaczynał się literą, która zawiera tylko litery (A – Z), cyfry (0 – 9) i podkreślenia (_).  *zakres* może być równa "żądanie" lub "system".  Należy pamiętać, że zmienne zdefiniowane w różnych zakresach różnią się od siebie nawzajem, tym zdefiniowane za pomocą danych wyjściowych funkcji semantycznego.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacji w bieżącym żądaniu interpretację.  One może służyć do kontrolowania Wyszukaj interpretacji za pośrednictwem gramatyki.

Zmienne systemowe są wstępnie zdefiniowane przez usługę i może służyć do pobierania różne statystyki dotyczące bieżącego stanu systemu.  Poniżej znajduje się zestaw zmiennych aktualnie obsługiwany system:

|Name (Nazwa)|Typ|Opis|
|----|----|----|
|IsAtEndOfQuery|Bool|wartość true, jeśli bieżący interpretacji spowodowało dopasowanie całego tekstu kwerendy wejściowej|
|IsBeyondEndOfQuery|Bool|wartość true, jeśli bieżący interpretacji proponuje uzupełnienia poza tekst wejściowy zapytania|

### <a name="setvariable-function"></a>SetVariable — funkcja

`SetVariable(name, value, scope);`

Przypisuje *wartość* do zmiennej *nazwa* o określonej nazwie *zakres*.  *Nazwa* to identyfikator zaczynał się literą, która zawiera tylko litery (A – Z), cyfry (0 – 9) i podkreślenia (_).  Obecnie jedyną poprawną wartością dla *zakres* jest "żądania".  Nie ma żadnych zmiennych systemowych do ustawienia.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacji w bieżącym żądaniu interpretację.  One może służyć do kontrolowania Wyszukaj interpretacji za pośrednictwem gramatyki.

### <a name="assertequals-function"></a>AssertEquals — funkcja

`AssertEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* są równoważne, funkcja zakończy się pomyślnie i zawiera żadnych efektów ubocznych.  W przeciwnym wypadku funkcja zakończy się niepowodzeniem i odrzuca interpretacji.

### <a name="assertnotequals-function"></a>AssertNotEquals — funkcja

`AssertNotEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* czy nie jest równorzędny, funkcja zakończy się pomyślnie i nie ma żadnych efektów ubocznych.  W przeciwnym wypadku funkcja zakończy się niepowodzeniem i odrzuca interpretacji.


