---
title: 'Przykład: Wielopoziomowe aspekty — Azure Search'
description: Dowiedz się, jak tworzyć struktury aspektów dla taksonomii wielopoziomowej, tworząc zagnieżdżoną strukturę nawigacji, którą można uwzględnić na stronach aplikacji.
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: 5a6fda0157f0f3a4ca5861acd4bcbead7839e451
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649932"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Przykład: Wielopoziomowe aspekty w usłudze Azure Search

Schematy Azure Search nie obsługują jawnie kategorii taksonomii wielopoziomowej, ale można je przybliżać przez manipulowanie zawartością przed indeksowaniem, a następnie zastosowanie specjalnej obsługi wyników. 

## <a name="start-with-the-data"></a>Zacznij od danych

Przykład w tym artykule kompiluje się w poprzednim przykładzie, [modeluje bazę danych spisu AdventureWorks](search-example-adventureworks-modeling.md), aby przedstawić wielopoziomowe aspekty w Azure Search.

Program AdventureWorks ma prostą taksonomię dwupoziomową z relacją nadrzędny-podrzędny. W przypadku głębokości taksonomii o stałej długości tej struktury proste zapytanie sprzężenia SQL może służyć do grupowania taksonomii:

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

## <a name="indexing-to-a-collection-field"></a>Indeksowanie do pola kolekcji

W indeksie zawierającym tę strukturę Utwórz pole **kolekcji (EDM. String)** w schemacie Azure Search, aby zapisać te dane, upewniając się, że atrybuty pola obejmują wyszukiwanie, filtrowanie, tworzenie i pobieranie.

Teraz podczas indeksowania zawartości odwołującej się do określonej kategorii taksonomii Prześlij taksonomię jako tablicę zawierającą tekst z każdego poziomu taksonomii. Na przykład dla jednostki z `ProductCategoryId = 5 (Mountain Bikes)`, Prześlij pole jako`[ "Bikes", "Bikes|Mountain Bikes"]`

Zwróć uwagę na umieszczenie kategorii nadrzędnej "Bikes" w podrzędnej wartości kategorii "Mountain Bikes". Każda Podkategoria powinna osadzić całą ścieżkę względem elementu głównego. Separator znaków potoku jest dowolny, ale musi być spójny i nie powinien znajdować się w tekście źródłowym. Znak separatora będzie używany w kodzie aplikacji do odtworzenia drzewa taksonomii z wyników aspektów.

## <a name="construct-the-query"></a>Konstruowanie zapytania

Podczas wystawiania kwerend należy uwzględnić następujące specyfikacje aspektu (gdzie Taksonomia to pole taksonomii do prezentacji):`facet = taxonomy,count:50,sort:value`

Wartość licznika musi być wystarczająco wysoka, aby można było zwracać wszystkie możliwe wartości taksonomii. Dane AdventureWorks zawierają różne wartości taksonomii 41, co `count:50` jest wystarczające.

  ![Filtr aspektów](./media/search-example-adventureworks/facet-filter.png "Filtr aspektów")

## <a name="build-the-structure-in-client-code"></a>Tworzenie struktury w kodzie klienta

W kodzie aplikacji klienckiej odkonstruowanie drzewa taksonomii przez podzielenie każdej wartości aspektu w potoku.

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

Obiekt **Categories** może być teraz używany do renderowania zwijanego drzewa taksonomii z dokładnymi licznikami:

  ![wielopoziomowy filtr aspektów](./media/search-example-adventureworks/multi-level-facet.png "wielopoziomowy filtr aspektów")

 
Każde łącze w drzewie powinno stosować filtr powiązany. Przykład:

+ **Taksonomia/wszystkie** `(x:x eq 'Accessories')` zwraca wszystkie dokumenty w gałęzi akcesoria
+ **Taksonomia/wszystkie** `(x:x eq 'Accessories|Bike Racks')` zwraca tylko dokumenty z podkategorią stojaków rowerowych w gałęzi akcesoria.

Ta technika będzie skalowana w celu pokrycia bardziej złożonych scenariuszy, takich jak dokładniejsze drzewa taksonomii i zduplikowane podkategorie, które występują w różnych kategoriach `Camping Equipment|Forks`nadrzędnych (na przykład `Bike Components|Forks` i).

> [!TIP]
> Liczba zwróconych zestawów reguł ma wpływ na szybkość zapytania. Aby obsługiwać bardzo duże zestawy taksonomii, należy rozważyć dodanie pola Fronts **EDM. String** w celu utrzymania wartości taksonomii najwyższego poziomu dla każdego dokumentu. Następnie Zastosuj tę samą technikę powyżej, ale tylko wykonaj zapytanie dotyczące zestawu reguł (filtrowane dla głównego pola taksonomii), gdy użytkownik poszerzy węzeł najwyższego poziomu. Lub jeśli 100% odwoływanie nie jest wymagane, wystarczy zmniejszyć liczbę aspektów do rozsądnej liczby i upewnić się, że wpisy aspektu są sortowane według liczby.

## <a name="see-also"></a>Zobacz także

[Przykład: Modelowanie bazy danych magazynu AdventureWorks dla Azure Search](search-example-adventureworks-modeling.md)