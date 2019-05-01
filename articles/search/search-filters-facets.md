---
title: Filtry aspektów nawigacji wyszukiwania w aplikacjach — usługa Azure Search
description: Filtruj kryteria, według tożsamości zabezpieczeń użytkownika, lokalizacji geograficznej lub wartości liczbowych, aby ograniczyć wyniki wyszukiwania dla zapytań w usłudze Azure Search, Usługa wyszukiwania w hostowanej chmurze Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: ec87bdadc0e7f77cdeebb16403758026fd956c30
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939859"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Jak utworzyć filtr zestawu reguł w usłudze Azure Search 

Nawigacja aspektowa jest używany podczas samodzielnego filtrowania na wyniki zapytania w aplikacji wyszukiwania, gdzie aplikacja udostępnia kontrolki interfejsu użytkownika dla zakresu wyszukiwania do grup dokumentów (na przykład, kategorii lub marki) i usługi Azure Search udostępnia struktury danych, aby utworzyć kopię środowisko. W tym artykule szybko przejrzeć podstawowe kroki tworzenia struktury nawigacji aspektowej, tworzenie kopii środowiska wyszukiwania, które chcesz udostępnić. 

> [!div class="checklist"]
> * Wybierz pola do filtrowania i tworzenia kategorii wyszukanych informacji
> * Ustaw atrybuty pola
> * Tworzenie indeksu i ładowanie danych
> * Dodaj filtry aspektów do kwerendy
> * Obsługa wyników

Zestawy reguł są dynamiczne i zwracane w zapytaniu. Odpowiedzi wyszukiwania w Przenieś im kategorie aspektów służące do nawigacji wyniki. Jeśli nie jesteś zaznajomiony z aspektami, poniższy przykład jest ilustrację aspektów struktury nawigacji.

  ![](./media/search-filters-facets/facet-nav.png)

Jesteś nowym użytkownikiem aspektowa nawigacji i chcesz więcej szczegółów? Zobacz [jak wdrożyć nawigację aspektową w usłudze Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Wybierz pola

Aspektami może zostać obliczona za pośrednictwem pojedynczej wartości pola, jak również kolekcje. Pola, które najlepiej działać w nawigacji aspektowej ma niski kardynalności: niewielkiej liczby różne wartości, które powtarzają się w całym dokumentów w głównej części usługi wyszukiwania (na przykład lista kolorów, kraje/regiony lub firmowe). 

Wielopoziomowe jest włączona na podstawie pól pola podczas tworzenia indeksu, ustawiając następujące atrybuty na wartość TRUE: `filterable`, `facetable`. Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania.

Wszelkie [typ pola](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , prawdopodobnie można używać w nawigacji aspektowej jest oznaczony jako "Tworzenie aspektów":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Typy pól liczbowych: Edm.Int32, Edm.Int64, Edm.Double

Nie można używać Edm.GeographyPoint nawigacji aspektowej. Zestawy reguł są konstruowane na podstawie ludzi można odczytać tekstu lub liczb. Jako takie aspekty, nie są obsługiwane dla współrzędnych geograficznych. Będziesz potrzebować miasta lub regionu pola do reguł według lokalizacji.

## <a name="set-attributes"></a>Ustaw atrybuty

Atrybuty indeksu, które kontrolować sposób używania pola zostaną dodane do definicji poszczególnych pól w indeksie. W poniższym przykładzie pola z niskim Kardynalność, przydatne w przypadku tworzenia kategorii wyszukanych informacji, składają się z: kategorii (hotelu, motel hostel), pozwalającego i oceny. 

W interfejsie API .NET filtrowania atrybutów muszą być jawnie ustawione. W interfejsie API REST tworzenia kategorii wyszukanych informacji i filtrowania są domyślnie włączone, co oznacza, że musisz jawnie ustawić atrybuty, jeśli chcesz je wyłączyć. Chociaż nie jest wymagana z technicznego punktu widzenia, pokazujemy pierwsza w poniższym przykładzie REST do celów szkoleniowych. 

> [!Tip]
> Najlepszym rozwiązaniem jest wydajność i Optymalizacja magazynu tworzenia kategorii wyszukanych informacji wyłączymy funkcję dla pola, które nigdy nie powinny być używane jako zestaw reguł. W szczególności pól ciągów dla wartości pojedynczego wystąpienia, takich jak identyfikator lub nazwa produktu, powinna być równa "Tworzenie aspektów": wartość false, aby zapobiec ich przypadkowym (i nieskuteczne) używanych w nawigacji aspektowej.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Ta definicja indeksu jest kopiowana z [Tworzenie indeksu usługi Azure Search przy użyciu interfejsu API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Jest on identyczny, z wyjątkiem różnic będzie pozornie w definicjach pól. Możliwość filtrowania i tworzenia aspektów atrybuty są jawnie dodawane w kategorii, tagi, parkingIncluded, smokingAllowed i pól. W praktyce otrzymasz można filtrować i tworzenia aspektów, aby uzyskać bezpłatne na typy pól Edm.String, typem Edm.Boolean i typem Edm.Int32. 

## <a name="build-and-load-an-index"></a>Tworzenie i ładowanie indeksu

Etap pośredni (i prawdopodobnie oczywisty) jest, że trzeba [kompilacji i wypełnić indeks](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) przed formułowanie zapytania. Firma Microsoft wspominać o tym kroku, w tym miejscu aby informacje były kompletne. Jednym ze sposobów, aby ustalić, czy indeks jest dostępna jest sprawdzanie listy indeksów w [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Dodaj filtry aspektów do kwerendy

W kodzie aplikacji należy utworzyć kwerendę, która określa wszystkie części prawidłowe zapytanie, w tym wyrażeniach wyszukiwania, aspektami, filtry i oceniania profile — wszystko umożliwia Sformułuj żądanie. Poniższy przykład tworzy żądanie, które powoduje nawigacji reguł na podstawie typu, zakwaterowanie, ocena i innych obiektów.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Zwracany wyfiltrowanych wyników na zdarzenia kliknięcia

Wyrażenie filtru obsługuje zdarzenie kliknięcia na wartość zestawu reguł. Biorąc pod uwagę zestaw reguł kategorii, klikając kategorii "motel" jest implementowane za pomocą `$filter` wyrażenia, który wybiera zakwaterowaniem tego typu. Gdy użytkownik kliknie "motele", aby wskazać, że mają być wyświetlane tylko motele, następne zapytanie, aplikacja wysyła obejmuje $filter = kategorii eq 'motele".

Poniższy fragment kodu dodaje kategorię do filtru, jeśli użytkownik wybierze wartości z zestawu reguł kategorii.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Za pomocą interfejsu API REST, żądanie będzie przegubowe jako `$filter=category eq 'c1'`. Aby określić kategorię pól wielowartościowych, należy użyć następującej składni: `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Porady i obejścia

### <a name="initialize-a-page-with-facets-in-place"></a>Inicjowanie strony z aspektami w miejscu

Jeśli chcesz zainicjować strony z aspektami w miejscu, możesz wysłać zapytanie jako część inicjowania strony w celu umieszczenia na stronie o strukturze początkowego zestawu reguł.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachowaj aspektów struktury nawigacji asynchronicznie filtrowane wyniki

Jest jednym z wyzwań przy użyciu reguł nawigacji w usłudze Azure Search, że aspektami istnieje dla tylko aktualne wyniki. W praktyce jest często zachować statyczne zbiór zestawu reguł, dzięki czemu użytkownik może przejść w odwrotnej kolejności, odtwarzanie kroki, aby zapoznać się z alternatywnych ścieżek przy użyciu wyszukiwania zawartości. 

Chociaż jest to typowy przypadek użycia, nie jest coś, co aspektów struktury nawigacji udostępnia obecnie poza pole. Deweloperów, którzy chcą statyczne aspektami zazwyczaj obejść ograniczenie dotyczące przez wysłanie dwóch zapytań filtrowane: jeden zakres wyniki, inne użyte do utworzenia statyczną listę zestawów reguł na potrzeby nawigacji.

## <a name="see-also"></a>Zobacz także

+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Tworzenie interfejsu API REST indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)
