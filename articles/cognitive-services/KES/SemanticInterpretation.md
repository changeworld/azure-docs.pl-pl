---
title: Interpretacji semantyki w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interpretacji semantyki w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 022188464eb7269b69f96a058b444167b587387c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347157"
---
# <a name="semantic-interpretation"></a>Interpretacji semantyki
Interpretacji semantyki kojarzy semantycznego dane wyjściowe z poszczególnych ścieżek interpretowany przez gramatyki.  W szczególności usługa oblicza sekwencja instrukcji w `tag` przejść elementów interpretacji do obliczenia ostateczne dane wyjściowe.  

Instrukcja może być przypisanie literałem lub zmienną do innej zmiennej.  On również przypisać dane wyjściowe funkcji 0 lub więcej parametrów do zmiennej.  Każdy parametr funkcji można określić za pomocą literałem lub zmienną.  Jeśli funkcja nie zwraca żadnych danych wyjściowych, przypisanie zostanie pominięty.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Zmienna jest określona za pomocą identyfikatora nazwy, która rozpoczyna się od litery i zawiera tylko litery (A-Z), cyfry (0-9) i znak podkreślenia (\_).  Jego typ jest niejawnie wywnioskować na podstawie literału lub wartość przypisana do niego dane wyjściowe funkcji. 

Poniżej znajduje się lista obecnie obsługiwane typy danych:

|Typ|Opis|Przykłady|
|----|----|----|
|Ciąg|Sekwencja 0 lub więcej znaków|"Witaj świecie!"<br/>""|
|wartość logiczna|Wartość logiczna|true<br/>false|
|Int32|32-bitowej podpisanej liczby całkowitej.  -2.1e9 do 2.1e9|123<br/>-321|
|Int64|64-bitowej podpisanej liczby całkowitej. -9.2e18 i 9.2e18|9876543210|
|podwójne|Zmiennoprzecinkowe podwójnej precyzji. 1.7e +/-308 (15 cyfr)|123.456789<br/>1.23456789e2|
|Identyfikator GUID|Unikatowy identyfikator globalny|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Zapytanie|Wyrażenia zapytania, które określa podzbiór obiektów danych w indeksie|All()<br/>I (*K1*, *K2*)|

<a name="semantic-functions"></a>
## <a name="semantic-functions"></a>Funkcje semantycznego
Istnieje zestaw wbudowanych funkcji semantycznego.  Zezwalaj konstrukcji zaawansowane zapytania i zapewniają kontekstowej kontrolę nad interpretacje gramatyki.

### <a name="and-function"></a>I — funkcja
`query = And(query1, query2);`

Zwraca zapytanie składające się z część wspólną dwóch wejściowych zapytania.

### <a name="or-function"></a>Lub funkcja
`query = Or(query1, query2);`

Zwraca zapytanie składające się pochodzących z Unii dwa zapytania wejściowego.

### <a name="all-function"></a>Wszystkie — funkcja
`query = All();`

Zwraca kwerendę, która obejmuje wszystkie obiekty danych.

W poniższym przykładzie używamy funkcja All() stworzenie wielokrotnie powtarzane zapytania oparte na przecięciu 1 lub więcej słów kluczowych.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Brak funkcji
`query = None();`

Zwraca kwerendę, która obejmuje ma obiektów danych.

W poniższym przykładzie używamy funkcja None() stworzenie wielokrotnie powtarzane zapytania oparte na Unii 1 lub więcej słów kluczowych.

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

Zwraca kwerendę, która obejmuje tylko obiekty danych, którego atrybut *attrName* zgodna z wartością *wartość* zgodnie z określoną operacją *op*, która jest równa "eq".  Zazwyczaj korzystają `attrref` elementu, aby utworzyć kwerendę oparte na ciąg dopasowane zapytanie wejściowe.  Jeśli wartość jest podany lub uzyskane w inny sposób, funkcja Query() można utworzyć zapytanie dopasowania tej wartości.

W poniższym przykładzie używamy funkcja Query() Obsługa określania academic publikacje z dziesiętnym.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

<a name="composite-function"/>
### <a name="composite-function"></a>Composite — funkcja
`query = Composite(innerQuery);`

Zwraca zapytanie, który hermetyzuje *innerQuery* składa się z odpowiedników podrzędne atrybuty wspólne atrybutu złożonego *attr*.  Hermetyzacja wymaga, aby atrybut złożonego *attr* wszystkich zgodnych obiektów danych ma co najmniej jedną wartość, która spełnia indywidualnie *innerQuery*.  Należy pamiętać, że zapytania podrzędne atrybuty złożonego atrybutu ma możliwość hermetyzacji przy użyciu funkcji Composite(), zanim można go łączyć z innych zapytań.

Na przykład następujące zapytanie zwraca academic publikacji przez "harry shum" on to "Microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Z drugiej strony następujące zapytanie zwraca academic publikacji, w przypadku, gdy autorów jest "harry shum" i jednym przynależności jest "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Funkcja GetVariable
`value = GetVariable(name, scope);`

Zwraca wartość zmiennej *nazwa* zdefiniowany w ramach określonego *zakres*.  *Nazwa* jest identyfikatorem, który rozpoczyna się od litery i zawiera tylko litery (A-Z), cyfry (0-9) i znak podkreślenia (_).  *zakres* może mieć wartości "żądania" lub "system".  Należy pamiętać, że zmienne zdefiniowane w obszarze różne zakresy różnią się od siebie, w tym te, zdefiniowane przez dane wyjściowe funkcji semantycznego.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacje w ramach bieżącego żądania interpret.  Ich można kontrolować Wyszukaj interpretacje gramatyki.

Zmienne systemowe są wstępnie zdefiniowane przez usługę i może służyć do pobierania różnych statystyki dotyczące bieżącego stanu systemu.  Poniżej znajduje się zestaw zmiennych aktualnie obsługiwany system:

|Name (Nazwa)|Typ|Opis|
|----|----|----|
|IsAtEndOfQuery|wartość logiczna|wartość true, jeśli bieżący interpretacji spowodowało dopasowanie cały tekst zapytania|
|IsBeyondEndOfQuery|wartość logiczna|wartość true, jeśli bieżący interpretacji ma sugerowane zakończeń poza tekst zapytania|

### <a name="setvariable-function"></a>Funkcja SetVariable
`SetVariable(name, value, scope);`

Przypisuje *wartość* do zmiennej *nazwa* o określonej nazwie *zakres*.  *Nazwa* jest identyfikatorem, który rozpoczyna się od litery i zawiera tylko litery (A-Z), cyfry (0-9) i znak podkreślenia (_).  Obecnie jedyną poprawną wartością dla *zakres* jest "żądania".  Nie ma zmiennych nie można ustawić systemu.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacje w ramach bieżącego żądania interpret.  Ich można kontrolować Wyszukaj interpretacje gramatyki.

### <a name="assertequals-function"></a>Funkcja AssertEquals
`AssertEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* są równoważne funkcja powiedzie się i nie ma skutków ubocznych.  W przeciwnym razie funkcja kończy się niepowodzeniem i odrzuca interpretacji.

### <a name="assertnotequals-function"></a>Funkcja AssertNotEquals
`AssertNotEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* czy nie jest równorzędny, funkcja powiedzie się i nie ma skutków ubocznych.  W przeciwnym razie funkcja kończy się niepowodzeniem i odrzuca interpretacji.


