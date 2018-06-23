---
title: Struktura wyrażenia zapytania w interfejsie API usługi eksploracji wiedzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać wyrażeń structured query w wiedzy eksploracji usługi (KES) interfejsu API w usługach kognitywnych.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 070ee311a1153bc9fb59870dce68f385a43b15f1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347092"
---
# <a name="structured-query-expression"></a>Structured Query — wyrażenie
Wyrażenia zapytania strukturalnych określa zestaw operacji do porównania indeks danych.  Składa się z atrybutu zapytania wyrażeń i funkcji wyższego poziomu.  Użyj [ *oceny* ](evaluateMethod.md) metodę w celu obliczenia obiektów zgodnego z wyrażeniem.  Poniżej przedstawiono przykład z domeny academic publikacji, która zwraca publikacje utworzone przez Jaime Teevan od 2013 roku.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Wyrażenia Structured query mogą być uzyskane z [ *interpretowania* ](interpretMethod.md) żądania, gdzie semantycznego dane wyjściowe każdego interpretacji jest strukturalnych zapytanie zwracające obiekty indeksu pasujące zapytania języka naturalnego.  Alternatywnie one mogą być ręcznie utworzone przy użyciu składni opisane w tej sekcji.

## <a name="attribute-query-expression"></a>Atrybut wyrażenia zapytania
Wyrażenia zapytania atrybut identyfikuje zestaw obiektów na podstawie zgodności z określonym atrybutem.  Różne pasujących operacji są obsługiwane w zależności od typu atrybutu i indeksowanej operacji określone w [schematu](SchemaFormat.md):

| Typ | Operacja | Przykłady |
|------|-------------|------------|
| Ciąg | równa się | Title = "ukryty semantyczny analizy" (canonical + synonimy) |
| Ciąg | równa się | Author.Name=='susan t dumais (canonical tylko)|
| Ciąg | starts_with | Title = 'ukryty s'... |
| Int32/Int64/podwójnej precyzji | równa się | Roku = 2000 |
| Int32/Int64/podwójnej precyzji | starts_with | Rok = "20"... (wartości dziesiętnej począwszy od "20") |
| Int32/Int64/podwójnej precyzji | is_between | Rok&lt;2000 <br/> Rok&lt;= 2000 <br/> Rok&gt;2000 <br/> Rok&gt;= 2000 <br/> Year=[2010,2012) *(zawiera wartość tylko lewej granicy: 2010, 2011)* <br/> Rok = [2000,2012] *(obejmuje zarówno wartości granicznych: 2010, 2011, 2012)* |
| Date | równa się | Data urodzenia = "1984-05-14" |
| Date | is_between | Data urodzenia&lt;= "2008/03/14' <br/> PublishDate = ["2000-01-01", "2009-12-31"] |
| Identyfikator GUID | równa się | ID = "602DD052-CC47-4B23-A16A-26B52D30C05B" |


Na przykład wyrażenie "Title ="s ukryty"..." dopasowuje wszystkie publikacje academic, których nazwa rozpoczyna się od ciągu "ukryty s".  Aby obliczyć to wyrażenie, atrybut tytułu musi określać operacji "starts_with" w schemacie używany do tworzenia indeksu.

Dla atrybutów o skojarzone synonimy wyrażeniu zapytania może określić obiektów, których wartości kanonicznej zgodny dany ciąg znaków, za pomocą operatora "==" lub obiektów gdzie jego wartości kanonicznej/synonim odpowiada za pomocą operatora "=".  Wymaga operatora "equals" należy określić w definicji atrybutu.


## <a name="functions"></a>Funkcje
Istnieje zestaw wbudowanych funkcji stosowanie konstrukcji bardziej złożonych wyrażeń zapytania z zapytań atrybutu podstawowego.

### <a name="and-function"></a>I — funkcja
`And(expr1, expr2)`

Zwraca część wspólną dwóch wyrażeń zapytania.

Poniższy przykład zwraca academic publikacji opublikowane w 2000 roku o pobieraniu informacji:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Lub funkcja
`Or(expr1, expr2)`

Zwraca złożenie dwóch wyrażeń zapytania.

Poniższy przykład zwraca academic publikacji opublikowane w 2000 roku o modelowaniu użytkownika lub pobieranie informacji:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite — funkcja
`Composite(expr)`

Zwraca wyrażenie hermetyzuje wewnętrzny wyrażenia składające się z zapytań dotyczących podrzędne atrybuty wspólne atrybutu złożonego.  Hermetyzacja wymaga atrybutu złożonego wszystkich zgodnych obiektów danych ma co najmniej jedną wartość, która spełnia indywidualnie wewnętrzny wyrażenie.  Należy pamiętać, że wyrażenia zapytania dla atrybutów podrzędne złożonego atrybutu ma możliwość hermetyzacji przy użyciu funkcji Composite() przed można łączyć z innymi wyrażenia zapytania.

Na przykład poniższe wyrażenie zwraca academic publikacji przez "harry shum" on to "Microsoft":

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Z drugiej strony poniższe wyrażenie zwraca academic publikacji, w przypadku, gdy autorów jest "harry shum" i jednym przynależności jest "microsoft":

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
