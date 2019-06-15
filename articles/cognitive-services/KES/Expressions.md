---
title: Wyrażenia zapytań strukturalnych — interfejs API usługi Eksploracja wiedzy
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak używać wyrażeń zapytań ze strukturą w wiedzy Exploration Service (KES) interfejsu API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815104"
---
# <a name="structured-query-expression"></a>Wyrażenia zapytań ze strukturą

Wyrażenia zapytań ze strukturą określa zestaw operacji do oceny na podstawie indeksu danych.  Składa się z wyrażeń zapytania atrybutu i wyższego poziomu funkcji.  Użyj [ *oceny* ](evaluateMethod.md) metodę w celu obliczenia obiektów dopasowania wyrażenia.  Oto przykład z domeny akademickich publikacji, która zwraca publikacje opracowane przez Teevan trzyczęściową od roku 2013.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Wyrażenia zapytań strukturalnych, które mogą być uzyskane z [ *interpretacji* ](interpretMethod.md) żądania, semantycznego dane wyjściowe każdego interpretacji wyrażenie zapytań ze strukturą, które zwraca obiekty indeksu dopasowania Zapytanie danych wejściowych języka naturalnego.  Alternatywnie one mogą być ręcznie tworzone za pomocą składni opisane w tej sekcji.

## <a name="attribute-query-expression"></a>Atrybut wyrażenia zapytania.

Wyrażenie zapytania atrybut identyfikuje zestaw obiektów, w oparciu o dopasowanie względem określonego atrybutu.  Różne pasujących operacji są obsługiwane w zależności od typu atrybutu i indeksowanej operacji określone w [schematu](SchemaFormat.md):

| Typ | Operacja | Przykłady |
|------|-------------|------------|
| String | równa się | Title = "ukryte analizy semantycznej" (canonical + synonimy) |
| String | równa się | Author.Name=='susan t dumais (canonical tylko)|
| String | starts_with | Title = "ukryte s"... |
| Int32/Int64/podwójnej precyzji | równa się | Roku = 2000 |
| Int32/Int64/podwójnej precyzji | starts_with | Rok = "20"... (wartości dziesiętnej począwszy od "20") |
| Int32/Int64/podwójnej precyzji | is_between | Rok&lt;2000 <br/> Rok&lt;= 2000 <br/> Rok&gt;2000 <br/> Rok&gt;= 2000 <br/> Year=[2010,2012) *(zawiera tylko lewej granicy, wartość: 2010, 2011)* <br/> Rok = [2000,2012] *(obejmuje zarówno wartości granic: 2010, 2011, 2012)* |
| Date | równa się | BirthDate='1984-05-14' |
| Date | is_between | Data urodzenia&lt;= "2008/03/14' <br/> PublishDate = ["01-01-2000", "2009-12-31'] |
| Guid | równa się | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Na przykład, wyrażenie "Title ="ukryte s"..." dopasowuje wszystkie publikacje typu akademickich, których tytuł zaczyna się od ciągu "ukryte s".  Aby można było obliczyć to wyrażenie, atrybut tytułu należy określić operację "starts_with" w schemacie, używany do tworzenia indeksu.

Dla atrybutów o skojarzone synonimy wyrażenia zapytania może określić obiekty, którego wartość canonical odpowiada danym ciągu za pomocą operatora "==" lub obiekty Jeśli dowolna z jego wartości canonical/synonim są takie same za pomocą operatora "=".  Oba wymagają operatora "equals" określonych w definicji atrybutów.


## <a name="functions"></a>Funkcje

Istnieje wbudowany zestaw funkcji, umożliwiając tworzenia bardziej zaawansowanych wyrażeń zapytań z kwerend dotyczących podstawowych atrybutów.

### <a name="and-function"></a>I — funkcja

`And(expr1, expr2)`

Zwraca część wspólną dwóch wyrażeń kwerendy wejściowej.

Poniższy przykład zwraca akademickich publikacje opublikowane w roku 2000 o pobieraniu informacji:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Lub — funkcja

`Or(expr1, expr2)`

Zwraca sumę dwóch wyrażeń kwerendy wejściowej.

Poniższy przykład zwraca akademickich publikacje opublikowane w roku 2000 dotyczące pobierania informacji lub modelowania użytkownika:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite — funkcja

`Composite(expr)`

Zwraca wyrażenie, który hermetyzuje wyrażenie inner składa się z zapytania względem podrzędnych atrybuty wspólny atrybut złożonego.  Hermetyzacja wymaga złożonej atrybutu dowolnego zgodnego obiektu danych, mieć co najmniej jedną wartość, która indywidualnie spełnia wewnętrzne wyrażenie.  Pamiętaj, że wyrażenia zapytania dla atrybutów podrzędnych złożonego atrybutu musi być zhermetyzowany przy użyciu funkcji Composite(), zanim można łączyć z innymi wyrażeniami zapytania.

Na przykład poniższe wyrażenie zwraca akademickich publikacje według "harry shum" podczas zajmował w "microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Z drugiej strony następujące wyrażenie zwraca akademickich publikacje, gdzie jest on autorzy "harry shum" i jednym przynależności to "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
