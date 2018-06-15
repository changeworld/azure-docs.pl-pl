---
title: Aspekt filtrów w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Filtrowanie kryteria tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowe, aby ograniczyć wyniki wyszukiwania na zapytania w usłudze Azure Search, Usługa wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 3f2cfea52d3c3f4bfc75364d0662a4218219152d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792413"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Jak utworzyć filtr aspektu w usłudze Azure Search 

Nawigacji aspektowej służy do samodzielnego filtrowania wyników kwerendy w aplikacji wyszukiwania, gdzie aplikacja udostępnia kontrolek interfejsu użytkownika dla zakresu wyszukiwania do grup dokumentów (na przykład, kategorii lub marek) i usługi Azure Search udostępnia struktury danych, aby utworzyć kopię środowisko. W tym artykule szybko przejrzeć podstawowe kroki tworzenia kopii środowiska wyszukiwania, które chcesz zapewnić strukturze nawigacji aspektowej. 

> [!div class="checklist"]
> * Wybierz pola do filtrowania i tworzenie aspektów
> * Ustaw atrybuty w polu
> * Tworzenie indeksu i ładowanie danych
> * Dodaj filtry aspektu do zapytania
> * Obsługa wyników

Aspekty są dynamiczne i zwracane w zapytaniu. Wyszukiwanie Przełącz z nimi kategorie aspektów służące do nawigacji wyniki. Jeśli nie znasz z aspektami, poniższy przykład jest ilustrację strukturze nawigacji zestawu reguł.

  ![](./media/search-filters-facets/facet-nav.png)

Jesteś nowym użytkownikiem aspektowej nawigacji i chcesz bardziej szczegółowo? Zobacz [implementowania nawigacji aspektowej w usłudze Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Wybierz pola

Aspekty może być obliczany przez pojedynczą wartość pola, a także kolekcje. Pola, które najlepiej w nawigacji aspektowej ma Kardynalność niski: niewielką liczbę unikatowych wartości, które Powtórz w całym dokumentów w Boże Twojego wyszukiwania (na przykład lista kolorów, krajach lub firmowe). 

Tworzenie aspektów jest włączona na podstawie pola — podczas tworzenia indeksu, ustawiając następujące atrybuty true: `filterable`, `facetable`. Aspekty mogą być tworzone tylko na podstawie pól z możliwością filtrowania.

Wszelkie [typ pola](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) które prawdopodobnie mogą być używane w nawigacji aspektowej jest oznaczony jako "aspektów":

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Typy pól liczbowych: Edm.Double typem Edm.Int32, Edm.Int64,

Nie można użyć Edm.GeographyPoint w nawigacji aspektowej. Aspekty są tworzone na podstawie tekst czytelny dla człowieka lub liczby. Tak aspektów nie są obsługiwane dla współrzędnych geo. Będzie potrzebny miasta lub regionu pola do aspektu według lokalizacji.

## <a name="set-attributes"></a>Ustaw atrybuty

Atrybuty indeksu, które kontrolować sposób używania pola są dodawane do definicje poszczególnych pól w indeksie. W poniższym przykładzie pola z niskim Kardynalność, ułatwia tworzenie aspektów, składają się z: kategorii (hoteli, motel, hostel), infrastruktury i klasyfikacji. 

Interfejsu API programu .NET filtrowania atrybutów musi być ustawiony w sposób jawny. W interfejsie API REST tworzenie aspektów i filtrowania są domyślnie włączone, co oznacza, że należy jawnie ustawić atrybutów, aby je wyłączyć. Chociaż nie jest to technicznie wymagane, pierwsza zostanie przedstawiony w następującym przykładzie REST do celów instrukcji. 

> [!Tip]
> Najlepszym rozwiązaniem dla pakietu performance and optymalizacji magazynowania wyłączyć tworzenie aspektów, dla pola, które nie mogą być używane jako zestaw reguł. W szczególności pól ciągów dla pojedynczych wartości, takich jak identyfikator lub nazwa produktu, należy wybrać opcję "Aspektów": wartość FAŁSZ, aby uniemożliwić ich przypadkowe (i nieskuteczne) używanych w nawigacji aspektowej.


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
> Ta definicja indeksu jest kopiowana z [utworzyć indeks usługi Azure Search przy użyciu interfejsu API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Jest on identyczny z wyjątkiem powierzchni różnice w definicji pola. Atrybuty można filtrować i tworzenia aspektów jawnie zostaną dodane do kategorii, tagi parkingIncluded, smokingAllowed i pól. W praktyce możesz uzyskać filtrowanie oraz o wolnym aspektów dla na typy pól typem Edm.String, typem Edm.Boolean i typem Edm.Int32. 

## <a name="build-and-load-an-index"></a>Tworzenie i załadować indeks

Etap pośredni (i prawdopodobnie oczywiste) to, czy masz do [kompilacji i wypełnić indeks](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) przed formułowanie zapytania. Firma Microsoft wspomina ten krok tutaj aby informacje były kompletne. Jednym ze sposobów ustalenia, czy dostępne jest indeks jest sprawdzanie listy indeksów w [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Dodaj filtry aspektu do zapytania

W kodzie aplikacji należy utworzyć kwerendę, która określa wszystkie części prawidłowe zapytanie, takich jak wyrażenia wyszukiwania, aspekty, filtry oceniania profile — wszystko można sformułować żądanie. Poniższy przykład tworzy żądanie, które tworzy aspektu nawigacji na podstawie typu zakwaterowania, klasyfikacji i innych obiektów.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Zwracany filtrowane wyniki na zdarzenia kliknięcia

Wyrażenie filtru obsługuje zdarzenie click na wartość zestawu reguł. Biorąc pod uwagę aspektu kategorii, klikając pozycję kategorii "motel" jest implementowane za pośrednictwem `$filter` wyrażenie wybierające dostosowania danego typu. Gdy użytkownik kliknie "motele", aby wskazać, że mają być wyświetlane tylko motele, zapytanie dalej wysyła aplikacji obejmuje $filter = kategorii eq 'motele".

Poniższy fragment kodu dodaje do filtr kategorii, jeśli użytkownik wybierze wartość z aspekt kategorii.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Przy użyciu interfejsu API REST, żądanie będzie przegubowe jako `$filter=category eq 'c1'`. Aby określić kategorię pola wielowartościowe, należy użyć następującej składni: `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Porady i rozwiązania

### <a name="initialize-a-page-with-facets-in-place"></a>Inicjowanie strony z aspektami w miejscu

Jeśli chcesz zainicjować strony z aspektami w miejscu, można wysyłać zapytania jako część inicjowania strony do inicjatora strony o strukturze początkowego zestawu reguł.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachowaj aspektu struktury nawigacji asynchronicznie filtrowane wyniki

Jest jednym z wyzwań związanych z nawigacją aspektu w usłudze Azure Search, czy aspekty istnieją dla bieżącego wyniki tylko. W praktyce jest często zachowania statyczny zestaw aspekty tak, aby użytkownik można przejść wstecz, odtworzyć kroki, aby eksplorować alternatywnej ścieżki wyszukiwania zawartości. 

Jest to typowe przypadek użycia, nie jest element strukturze nawigacji aspektu zawiera obecnie poza pole. Deweloperów, którzy mają zwykle statycznych aspekty obejść ograniczenie dotyczące wysyłając dwa zapytania filtrowane: jeden zakres na wynikach, innych pozwala utworzyć statyczną listę zestawów reguł do celów nawigacji.

## <a name="see-also"></a>Zobacz także

+ [Filtry w usłudze Azure Search](search-filters.md)
+ [Tworzenie indeksu interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)

