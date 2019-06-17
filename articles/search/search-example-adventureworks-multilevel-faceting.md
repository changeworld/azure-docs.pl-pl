---
title: 'Przykład: Wielopoziomowe aspekty — usługa Azure Search'
description: Dowiedz się, jak tworzyć struktur kategoryzowanie wielopoziomowe taksonomii, tworzenia struktury zagnieżdżonej nawigacji, który może zawierać na stronach aplikacji.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: chstone
ms.openlocfilehash: e17a91a35b69102e4e0ac6025559bbc32e71d8fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024127"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Przykład: Wielopoziomowe aspekty w usłudze Azure Search

Usługa Azure Search schematów nie obsługują jawnie wielopoziomowe Taksonomia kategorii, ale możesz przybliżyć się je przez manipulowanie zawartością przed indeksowania, a następnie zastosowanie niektórych specjalnej obsługi do wyników. 

## <a name="start-with-the-data"></a>Rozpocznij od danych

W przykładzie w tym artykule opiera się na poprzednim przykładzie [Model bazy danych AdventureWorks spisu](search-example-adventureworks-modeling.md), aby zademonstrować wielopoziomowe w usłudze Azure Search.

AdventureWorks ma proste taksonomii dwupoziomowej z relacją nadrzędny podrzędny. Taksonomia o stałej długości głębokości tej struktury proste zapytanie sprzężenia SQL może służyć do grupowania taksonomii:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Wyniki zapytania](./media/search-example-adventureworks/prod-query-results.png "wyniki zapytania")

## <a name="indexing-to-a-collection-field"></a>Indeksowanie w celu pola kolekcji

W indeksie zawierające tej struktury, należy utworzyć **Collection(Edm.String)** pola w schemacie usługi Azure Search do przechowywania danych, upewniając się, że atrybuty pola zawierają wyszukiwanie, filtrowanie, tworzenie aspektów, a pobieranie.

Teraz podczas indeksowania zawartości, która odwołuje się do kategorii określonych taksonomii, Prześlij taksonomii jako tablica zawierająca tekst z poszczególnych poziomów taksonomii. Na przykład dla jednostki z `ProductCategoryId = 5 (Mountain Bikes)`, Prześlij pola jako `[ "Bikes", "Bikes|Mountain Bikes"]`

Zwróć uwagę, włączenie kategorii nadrzędnej "Rowery" wartości kategorii podrzędnych "Rowery górskie". Każdej podkategorii należy osadzić jego pełną ścieżkę względem elementu głównego. Znak separatora potoku jest określana, ale muszą być zgodne i nie powinien pojawić się w tekście źródłowym. Znak separatora będzie służyć w kodzie aplikacji odtworzenie drzewa taksonomii aspekt wyników.

## <a name="construct-the-query"></a>Konstrukcja zapytania

Podczas wysyłania zapytań, zawiera specyfikację aspekt (gdzie taksonomii jest pole taksonomii aspektów): `facet = taxonomy,count:50,sort:value`

Wartość licznika musi być wystarczająco wysoka, aby zwrócić wszystkie wartości możliwe taksonomii. Danych AdventureWorks zawiera 41 taksonomii różne wartości, więc `count:50` jest wystarczająca.

  ![Filtr aspektowa](./media/search-example-adventureworks/facet-filter.png "Aspektowa filtru")

## <a name="build-the-structure-in-client-code"></a>Tworzenie struktury w kodzie klienta

W kodzie aplikacji klienta należy odtworzyć drzewa taksonomii, dzieląc wartość każdego aspektu na znaku kreski pionowej.

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

**Kategorie** obiektu może teraz służyć do renderowania drzewie taksonomii zwijany z dokładne liczniki:

  ![Wielopoziomowe filtru aspektowa](./media/search-example-adventureworks/multi-level-facet.png "wielopoziomowej aspektowa filtru")

 
Każde łącze w drzewie, należy zastosować filtr powiązane. Na przykład:

+ **Taksonomia/any** `(x:x eq 'Accessories')` zwraca wszystkie dokumenty w gałęzi Akcesoria
+ **Taksonomia/any** `(x:x eq 'Accessories|Bike Racks')` zwraca tylko dokumenty z Podkategoria Stojakami roweru gałęzi Akcesoria.

Ta metoda będzie skalowana w celu bardziej złożonych scenariuszy takich jak bardziej drzew taksonomii i zduplikowane podkategorii, które występują w obszarze kategorii innego elementu nadrzędnego (na przykład `Bike Components|Forks` i `Camping Equipment|Forks`).

> [!TIP]
> Prędkość wysyłania zapytań ma wpływ liczba aspektami zwracane. Aby obsługiwać taksonomii bardzo dużych zestawów, należy rozważyć tworzenie aspektów **Edm.String** pola do przechowywania wartości taksonomii najwyższego poziomu dla każdego dokumentu. Zastosuj tę samą technikę powyżej, ale tylko wtedy wykonać zapytania kolekcji reguł (filtrowane według pola taksonomii głównego) po użytkownik rozwija węzeł najwyższego poziomu. Lub, jeśli 100% odwołania nie jest wymagana, po prostu zmniejszenia liczby reguł do numeru uzasadnione i upewnij się, że wpisy reguł są sortowane według liczby.

## <a name="see-also"></a>Zobacz także

[Przykład: Model bazy danych AdventureWorks magazynu na potrzeby usługi Azure Search](search-example-adventureworks-modeling.md)