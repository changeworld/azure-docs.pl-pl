---
title: Filtry fasetów do nawigacji wyszukiwania w aplikacjach
titleSuffix: Azure Cognitive Search
description: Filtruj kryteria według tożsamości zabezpieczeń użytkownika, lokalizacji geograficznej lub wartości liczbowych, aby zmniejszyć wyniki wyszukiwania w kwerendach w usłudze Azure Cognitive Search, hostowej usłudze wyszukiwania w chmurze na platformie Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792892"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Jak utworzyć filtr aspektu w usłudze Azure Cognitive Search 

Nawigacja aspektowa służy do samodzielnego filtrowania wyników zapytań w aplikacji wyszukiwania, w której aplikacja oferuje kontrolki interfejsu użytkownika służące do określania zakresu wyszukiwania grupom dokumentów (na przykład kategorii lub marek), a usługa Azure Cognitive Search zapewnia strukturę danych aby poprzeć to doświadczenie. W tym artykule szybko przejrzyj podstawowe kroki tworzenia struktury nawigacji aspektowej, która tworzy środowisko wyszukiwania, które chcesz zapewnić. 

> [!div class="checklist"]
> * Wybieranie pól do filtrowania i fasetowania
> * Ustawianie atrybutów w polu
> * Tworzenie indeksu i ładowanie danych
> * Dodawanie filtrów asetek do kwerendy
> * Obsługa wyników

Aspekty są dynamiczne i zwracane w kwerendzie. Odpowiedzi wyszukiwania przynieść ze sobą kategorie aspekt używany do nawigacji wyniki. Jeśli nie są zaznajomieni z aspektami, poniższy przykład jest ilustracją struktury nawigacji aspektu.

  ![](./media/search-filters-facets/facet-nav.png)

Jesteś nowym aspektem nawigacji i chcesz więcej szczegółów? Zobacz [Jak zaimplementować nawigację aspektową w usłudze Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Wybieranie pól

Aspekty mogą być obliczane za pomocą pól pojedynczej wartości, a także kolekcji. Pola, które najlepiej sprawują się w nawigacji aspektowej, mają niską kardynalność: niewielka liczba różnych wartości powtarzających się w dokumentach w korpusie wyszukiwania (na przykład lista kolorów, krajów/regionów lub nazw marek). 

Fasetowanie jest włączone w zależności od pola podczas tworzenia indeksu przez ustawienie atrybutu `facetable` `true`. Ogólnie należy również ustawić `filterable` atrybut `true` dla takich pól, tak aby aplikacja wyszukiwania można filtrować na tych polach na podstawie aspektów, które wybiera użytkownik końcowy. 

Podczas tworzenia indeksu przy użyciu interfejsu API REST każdy [typ pola,](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) który `facetable` może być używany w nawigacji aspektowej jest domyślnie oznaczony jako:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numeryczne typy `Edm.Int32` `Edm.Int64`pól: ,`Edm.Double`
+ Kolekcje powyższych typów (na `Collection(Edm.String)` `Collection(Edm.Double)`przykład lub)

Nie można `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` używać ani pól w nawigacji aspektowej. Aspekty działają najlepiej na polach o niskiej kardynalności. Ze względu na rozdzielczość współrzędnych geograficznych rzadko zdarza się, że wszystkie dwa zestawy współrzędnych będą równe w danym zestawie danych. W związku z tym aspekty nie są obsługiwane dla współrzędnych geograficznych. Potrzebne jest pole miasta lub regionu do aspektu według lokalizacji.

## <a name="set-attributes"></a>Ustaw atrybuty

Atrybuty indeksu sterujące sposób użycia pola są dodawane do poszczególnych definicji pól w indeksie. W poniższym przykładzie pola o niskiej kardynalności, przydatne do fasetowania, składają się z: `category` (hotel, motel, hostel), `tags`i `rating`. Te pola `filterable` mają `facetable` atrybuty i atrybuty ustawione jawnie w poniższym przykładzie dla celów ilustracyjnych. 

> [!Tip]
> Najlepszym rozwiązaniem dla optymalizacji wydajności i magazynu, należy wyłączyć aspektowanie dla pól, które nigdy nie powinny być używane jako aspekt. W szczególności pola ciągów dla unikatowych wartości, takich jak identyfikator `"facetable": false` lub nazwa produktu, powinny być ustawione, aby zapobiec ich przypadkowemu (i nieskuteczne) użycie w nawigacji aspektowej.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Ta definicja indeksu jest kopiowana z [programu Utwórz indeks usługi Azure Cognitive Search przy użyciu interfejsu API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Jest identyczny, z wyjątkiem powierzchownych różnic w definicjach pól. `filterable` Atrybuty `facetable` i atrybuty `category`są `tags` `parkingIncluded`jawnie dodawane na polach , , `smokingAllowed`i `rating` . W `filterable` praktyce `facetable` i będzie domyślnie włączona w tych polach podczas korzystania z interfejsu API REST. Podczas korzystania z .NET SDK, te atrybuty muszą być włączone jawnie.

## <a name="build-and-load-an-index"></a>Tworzenie i ładowanie indeksu

Pośredni (i być może oczywiste) krok jest, że trzeba [skompilować i wypełnić indeks](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) przed sformułowaniem kwerendy. Wspominamy o tym kroku tutaj dla kompletności. Jednym ze sposobów określenia, czy indeks jest dostępny, jest sprawdzenie listy indeksów w [portalu](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Dodawanie filtrów asetek do kwerendy

W kodzie aplikacji skonstruuj kwerendę określającą wszystkie części prawidłowej kwerendy, w tym wyrażenia wyszukiwania, aspekty, filtry, profile oceniania — wszystko używane do formułowania żądania. Poniższy przykład tworzy żądanie, które tworzy nawigacji aspekt na podstawie typu zakwaterowania, ocena i inne udogodnienia.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Zwracanie filtrowanych wyników w sprawie zdarzeń kliknięcia

Gdy użytkownik końcowy kliknie wartość aspektu, program obsługi dla zdarzenia click należy użyć wyrażenia filtru, aby zrealizować intencje użytkownika. Biorąc `category` pod uwagę aspekt, kliknięcie kategorii "motel" `$filter` jest implementowane z wyrażeniem, które wybiera zakwaterowanie tego typu. Gdy użytkownik kliknie "motel", aby wskazać, że powinny być wyświetlane `$filter=category eq 'motel'`tylko motele, następna kwerenda wysyłana przez aplikację zawiera .

Poniższy fragment kodu dodaje kategorię do filtru, jeśli użytkownik wybierze wartość z aspektu kategorii.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Jeśli użytkownik kliknie wartość aspektu dla `tags`pola kolekcji, takiego jak , na przykład wartość "pula", aplikacja powinna użyć następującej składni filtru:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Porady i obejścia

### <a name="initialize-a-page-with-facets-in-place"></a>Inicjowanie strony z fasetami na miejscu

Jeśli chcesz zainicjować stronę z aspektami w miejscu, można wysłać kwerendę jako część inicjowania strony do materiału siewnego strony z początkowej struktury aspektu.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachowywanie struktury nawigacji aspektu asynchronicznie filtrowanych wyników

Jednym z wyzwań związanych z nawigacją aspektu w usłudze Azure Cognitive Search jest to, że aspekty istnieją tylko dla bieżących wyników. W praktyce jest wspólne zachować statyczny zestaw aspektów, dzięki czemu użytkownik może poruszać się w odwrotnej, śledzenie kroków do eksplorowania alternatywnych ścieżek za pośrednictwem zawartości wyszukiwania. 

Chociaż jest to typowy przypadek użycia, nie jest to coś, co struktura nawigacji aspekt obecnie zapewnia out-of-the-box. Deweloperzy, którzy chcą statyczne aspekty zazwyczaj obejść ograniczenia, wydając dwa zapytania filtrowane: jeden o zakresie do wyników, drugi używany do tworzenia statycznej listy aspektów do celów nawigacji.

## <a name="see-also"></a>Zobacz też

+ [Filtry w usłudze Azure Cognitive Search](search-filters.md)
+ [Tworzenie interfejsu API REST indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents)
