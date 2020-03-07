---
title: Interpretacja semantyczna — interfejs API Knowledge Exploration Service
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interpretacji semantyki w interfejsie API Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385657"
---
# <a name="semantic-interpretation"></a>Interpretacja semantyczna

Interpretacja semantyczna kojarzy dane wyjściowe semantyczne z każdą interpretowaną ścieżką za pomocą gramatyki.  W szczególności usługa szacuje sekwencję instrukcji w `tag` elementy, które przechodzą przez interpretację, aby obliczyć końcowe dane wyjściowe.  

Instrukcja może być przypisaniem literału lub zmiennej do innej zmiennej.  Może również przypisać dane wyjściowe funkcji z 0 lub więcej parametrów do zmiennej.  Każdy parametr funkcji można określić przy użyciu literału lub zmiennej.  Jeśli funkcja nie zwróci żadnych danych wyjściowych, przypisanie zostanie pominięte.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Zmienna jest określana przy użyciu identyfikatora nazwy, który rozpoczyna się od litery i zawiera tylko litery (A – Z), cyfry (0-9) i podkreślenia (\_).  Jego typ jest niejawnie wnioskowany z przypisanej do niego wartości wyjściowej literału lub funkcji. 

Poniżej znajduje się lista obecnie obsługiwanych typów danych:

|Typ|Opis|Przykłady|
|----|----|----|
|Ciąg|Sekwencja 0 lub więcej znaków|"Hello world!"<br/>""|
|Bool|Wartość logiczna|true<br/>false|
|Int32|32-bitowa liczba całkowita ze znakiem.  -2.1 E9 do 2.1 E9|123<br/>-321|
|Int64|64-bitowa liczba całkowita ze znakiem. -9.2 E18 i 9.2 E18|9876543210|
|Podwójne|Liczba zmiennoprzecinkowa o podwójnej precyzji. 1.7 e +/– 308 (15 cyfr)|123,456789<br/>1.23456789 E2|
|Guid|Unikatowy identyfikator globalny|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Zapytanie|Wyrażenie zapytania określające podzestaw obiektów danych w indeksie|Wszystkie ()<br/>I (*Q1*, *Q2*)|

## <a name="semantic-functions"></a>Funkcje semantyczne

Istnieje wbudowany zestaw funkcji semantycznych.  Umożliwiają one konstruowanie zaawansowanych zapytań i zapewniają kontrolę kontekstową na interpretacji gramatyki.

### <a name="and-function"></a>And — funkcja

`query = And(query1, query2);`

Zwraca zapytanie złożone z przecięcia dwóch zapytań wejściowych.

### <a name="or-function"></a>Or — funkcja

`query = Or(query1, query2);`

Zwraca zapytanie składające się z Unii dwóch zapytań wejściowych.

### <a name="all-function"></a>All — funkcja

`query = All();`

Zwraca zapytanie, które zawiera wszystkie obiekty danych.

W poniższym przykładzie używamy funkcji All () w celu iteracyjnego kompilowania zapytania na podstawie przecięcia z 1 lub więcej słów kluczowych.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None — funkcja

`query = None();`

Zwraca zapytanie, które nie zawiera żadnych obiektów danych.

W poniższym przykładzie używamy funkcji None () w celu iteracyjnego kompilowania zapytania w oparciu o Unię 1 lub więcej słów kluczowych.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Funkcja zapytania

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Zwraca zapytanie, które zawiera tylko obiekty danych, których atrybut *attrName* pasuje do *wartości* wartości zgodnie z *określoną operacją*operacji, której wartością domyślną jest "EQ".  Zazwyczaj Użyj elementu `attrref`, aby utworzyć zapytanie na podstawie dopasowanego ciągu zapytania wejściowego.  Jeśli wartość jest określona lub uzyskana za pośrednictwem innych metod, można użyć funkcji Query (), aby utworzyć zapytanie pasujące do tej wartości.

W poniższym przykładzie użyto funkcji Query () w celu zaimplementowania obsługi określania publikacji akademickich z określonej dekady.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Funkcja złożona

`query = Composite(innerQuery);`

Zwraca zapytanie, które hermetyzuje *innerQuery* złożone z dopasowań względem atrybutów podrzędnych *wspólnego atrybutu złożonej atrybutów.*  Hermetyzacja wymaga, *Aby atrybut złożonego atrybutu każdego* pasującego obiektu danych miał co najmniej jedną wartość, która indywidualnie spełnia *innerQuery*.  Należy pamiętać, że zapytanie dotyczące atrybutów podrzędnych atrybutu złożonego musi być hermetyzowane przy użyciu funkcji złożonej (), aby można było łączyć ją z innymi zapytaniami.

Na przykład następujące zapytanie zwraca publikacje naukowe według "Harry Shuma", podczas gdy miało "Microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Z drugiej strony następujące zapytanie zwraca Publikacje akademickie, w których jeden z autorów ma wartość "Harry Shuma" i jeden z przynależności do "Microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Getvariable — funkcja

`value = GetVariable(name, scope);`

Zwraca wartość *nazwy* zmiennej zdefiniowanej w określonym *zakresie*.  *Nazwa* jest identyfikatorem rozpoczynającym się od litery i zawiera tylko litery (a – z), cyfry (0-9) i znak podkreślenia (_).  *zakres* może być ustawiony na "Request" lub "system".  Należy zauważyć, że zmienne zdefiniowane w różnych zakresach różnią się od siebie, w tym zdefiniowane za pośrednictwem danych wyjściowych funkcji semantycznych.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacje w ramach bieżącego żądania interpretacji.  Mogą służyć do sterowania wyszukiwaniem interpretacji na potrzeby gramatyki.

Zmienne systemowe są wstępnie zdefiniowane przez usługę i mogą być używane do pobierania różnych statystyk dotyczących bieżącego stanu systemu.  Poniżej znajduje się zestaw obecnie obsługiwanych zmiennych systemowych:

|Name (Nazwa)|Typ|Opis|
|----|----|----|
|IsAtEndOfQuery|Bool|prawda, jeśli bieżąca interpretacja pasuje do całego tekstu zapytania wejściowego|
|IsBeyondEndOfQuery|Bool|prawda, jeśli bieżąca interpretacja ma sugerowane zakończenia poza tekstem zapytania wejściowego|

### <a name="setvariable-function"></a>Setvariable — funkcja

`SetVariable(name, value, scope);`

Przypisuje *wartość* do *nazwy* zmiennej w określonym *zakresie*.  *Nazwa* jest identyfikatorem rozpoczynającym się od litery i zawiera tylko litery (a – z), cyfry (0-9) i znak podkreślenia (_).  Obecnie jedyną prawidłową wartością dla *zakresu* jest "Request".  Nie istnieją żadne zmienne systemowe settable.

Zmienne zakresu żądania są współużytkowane przez wszystkie interpretacje w ramach bieżącego żądania interpretacji.  Mogą służyć do sterowania wyszukiwaniem interpretacji na potrzeby gramatyki.

### <a name="assertequals-function"></a>AssertEquals, funkcja

`AssertEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* są równoważne, funkcja się powiedzie i nie ma żadnych efektów ubocznych.  W przeciwnym razie funkcja kończy się niepowodzeniem i odrzuca interpretację.

### <a name="assertnotequals-function"></a>AssertNotEquals, funkcja

`AssertNotEquals(value1, value2);`

Jeśli *wartość1* i *wartość2* nie są równoważne, funkcja powiedzie się i nie ma żadnych efektów ubocznych.  W przeciwnym razie funkcja kończy się niepowodzeniem i odrzuca interpretację.


