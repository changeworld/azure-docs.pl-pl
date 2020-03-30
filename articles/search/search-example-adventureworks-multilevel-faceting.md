---
title: 'Przykład: Aspekty wielopoziomowe'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak tworzyć struktury aspektowe dla taksonomii wielopoziomowych, tworząc zagnieżdżoną strukturę nawigacji, którą można uwzględnić na stronach aplikacji.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792940"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Przykład: Wielopoziomowe aspekty w usłudze Azure Cognitive Search

Schematy usługi Azure Cognitive Search nie obsługują jawnie wielopoziomowych kategorii taksonomii, ale można je przybliżyć, manipulując zawartością przed indeksowania, a następnie stosując niektóre specjalne obsługi do wyników. 

## <a name="start-with-the-data"></a>Zacznij od danych

W tym artykule w tym artykule opiera się na poprzednim przykładzie [Model AdventureWorks Inventory bazy danych](search-example-adventureworks-modeling.md), aby zademonstrować wielopoziomowe aspektowanie w usłudze Azure Cognitive Search.

AdventureWorks ma prostą dwupoziomową taksonomię z relacją nadrzędny-podrzędny. W przypadku głębokości taksonomii o stałej długości tej struktury można użyć prostego zapytania sprzężenia SQL do grupowanie taksonomii:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Wyniki zapytania](./media/search-example-adventureworks/prod-query-results.png "Wyniki zapytania")

## <a name="indexing-to-a-collection-field"></a>Indeksowanie do pola Kolekcja

W indeksie zawierającym tę strukturę utwórz pole **Kolekcja(Edm.String)** w schemacie usługi Azure Cognitive Search, aby przechowywać te dane, upewniając się, że atrybuty pól obejmują przeszukiwalne, filtrowalne, możliwe do przefiltrowania i możliwe do pobrania.

Teraz podczas indeksowania zawartości, która odnosi się do określonej kategorii taksonomii, prześlij taksonomię jako tablicę zawierającą tekst z każdego poziomu taksonomii. Na przykład dla jednostki z `ProductCategoryId = 5 (Mountain Bikes)`, prześlij pole jako`[ "Bikes", "Bikes|Mountain Bikes"]`

Zwróć uwagę na włączenie kategorii nadrzędnej "Rowery" do wartości kategorii podrzędnej "Rowery górskie". Każda podkategoria powinna osadzić całą ścieżkę względem elementu głównego. Separator znaków potoku jest dowolny, ale musi być spójny i nie powinien pojawiać się w tekście źródłowym. Znak separatora będzie używany w kodzie aplikacji do rekonstrukcji drzewa taksonomii z wyników aspektu.

## <a name="construct-the-query"></a>Konstruowanie kwerendy

Podczas wystawiania zapytań należy uwzględnić następującą specyfikację aspektu (gdzie taksonomia jest polem taksonomii, które można fasonomować):`facet = taxonomy,count:50,sort:value`

Wartość liczby musi być wystarczająco wysoka, aby zwrócić wszystkie możliwe wartości taksonomii. Dane AdventureWorks zawiera 41 różnych wartości `count:50` taksonomii, więc jest wystarczająca.

  ![Filtr fasetowany](./media/search-example-adventureworks/facet-filter.png "Filtr fasetowany")

## <a name="build-the-structure-in-client-code"></a>Tworzenie struktury w kodzie klienta

W kodzie aplikacji klienta zrekonstruuj drzewo taksonomii przez podzielenie każdej wartości aspektu na znak potoku.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

Obiekt kategorii może być teraz używany do renderowania zwijanego drzewa **taksonomii** z dokładnymi liczbami:

  ![wielopoziomowy filtr fasetowany](./media/search-example-adventureworks/multi-level-facet.png "wielopoziomowy filtr fasetowany")

 
Każde łącze w drzewie należy zastosować powiązany filtr. Przykład:

+ **taksonomii/wszelkie** `(x:x eq 'Accessories')` zwraca wszystkie dokumenty w oddziale Akcesoria
+ **taksonomii/dowolny** `(x:x eq 'Accessories|Bike Racks')` zwraca tylko dokumenty z podkategorią stojaków rowerowych w gałęzi Akcesoria.

Technika ta będzie skalowana w celu uwzględnienia bardziej złożonych scenariuszy, `Bike Components|Forks` takich jak głębsze drzewa taksonomii i zduplikowane podkategorie, które występują w różnych kategoriach nadrzędnych (na przykład i `Camping Equipment|Forks`).

> [!TIP]
> Szybkość kwerendy zależy od liczby zwróconych aspektów. Aby obsługiwać bardzo duże zestawy taksonomii, należy rozważyć dodanie facetable **Edm.String** pola do przechowywania najwyższego poziomu wartości taksonomii dla każdego dokumentu. Następnie zastosuj tę samą technikę powyżej, ale tylko wykonać kwerendę aspekt kolekcji (filtrowane w polu taksonomii głównej), gdy użytkownik rozwija węzeł najwyższego poziomu. Lub, jeśli 100% przypomnieć nie jest wymagane, po prostu zmniejszyć liczbę facetów do rozsądnej liczby i upewnij się, że wpisy aspektu są sortowane według liczby.

## <a name="see-also"></a>Zobacz też

[Przykład: Modelowanie bazy danych AdventureWorks Inventory dla usługi Azure Cognitive Search](search-example-adventureworks-modeling.md)