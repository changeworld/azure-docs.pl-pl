---
title: Jak modelować złożone typy danych
titleSuffix: Azure Cognitive Search
description: Struktury danych zagnieżdżonych lub hierarchicznych mogą być modelowane w indeksie usługi Azure Cognitive Search przy użyciu typów danych ComplexType i Collections.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283058"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Jak modelować złożone typy danych w usłudze Azure Cognitive Search

Zewnętrzne zestawy danych używane do wypełniania indeksu usługi Azure Cognitive Search mogą być w wielu kształtach. Czasami obejmują one hierarchiczne lub zagnieżdżone podstruktury. Przykłady mogą obejmować wiele adresów dla jednego klienta, wiele kolorów i rozmiarów dla pojedynczej jednostki SKU, wielu autorów pojedynczej książki i tak dalej. W kategoriach modelowania mogą być widoczne te struktury określane jako *złożone,* *złożone,* *złożone*lub *zagregowane* typy danych. Termin Azure Cognitive Search używa dla tej koncepcji jest **typ złożony**. W usłudze Azure Cognitive Search typy złożone są modelowane przy użyciu **złożonych pól**. Pole złożone to pole zawierające elementy podrzędne (podpola), które może mieć dowolny typ danych, w tym inne typy złożone. Działa to w podobny sposób jak typy danych strukturalnych w języku programowania.

Pola złożone reprezentują pojedynczy obiekt w dokumencie lub tablicę obiektów, w zależności od typu danych. Pola typu `Edm.ComplexType` reprezentują pojedyncze obiekty, `Collection(Edm.ComplexType)` podczas gdy pola typu reprezentują tablice obiektów.

Usługa Azure Cognitive Search natywnie obsługuje złożone typy i kolekcje. Te typy umożliwiają modelowanie prawie dowolnej struktury JSON w indeksie usługi Azure Cognitive Search. W poprzednich wersjach interfejsów API usługi Azure Cognitive Search można było zaimportować tylko spłaszczone zestawy wierszy. W najnowszej wersji indeks może teraz bardziej odpowiadać danym źródłowym. Innymi słowy, jeśli dane źródłowe ma złożone typy, indeks może mieć również złożone typy.

Aby rozpocząć, zalecamy [zestaw danych hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), który można załadować w Kreatorze **importu danych** w witrynie Azure portal. Kreator wykrywa złożone typy w źródle i sugeruje schemat indeksu na podstawie wykrytych struktur.

> [!Note]
> Obsługa typów złożonych jest `api-version=2019-05-06`ogólnie dostępna w pliku . 
>
> Jeśli rozwiązanie wyszukiwania jest oparte na wcześniejszych obejściach spłaszczonych zestawów danych w kolekcji, należy zmienić indeks, aby uwzględnić typy złożone, jak są obsługiwane w najnowszej wersji interfejsu API. Aby uzyskać więcej informacji na temat uaktualniania wersji interfejsu API, zobacz [Uaktualnianie do najnowszej wersji interfejsu API REST](search-api-migration.md) lub [uaktualnienie do najnowszej wersji SDK .NET](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Przykład złożonej struktury

Poniższy dokument JSON składa się z prostych pól i złożonych pól. Pola złożone, `Address` takie `Rooms`jak i , mają podpola. `Address`ma jeden zestaw wartości dla tych podalet, ponieważ jest to pojedynczy obiekt w dokumencie. Natomiast `Rooms` ma wiele zestawów wartości dla jego pól podrzędnych, po jednym dla każdego obiektu w kolekcji.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Tworzenie złożonych pól

Podobnie jak w przypadku dowolnej definicji indeksu, można użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [.NET SDK,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) aby utworzyć schemat zawierający typy złożone. 

W poniższym przykładzie przedstawiono schemat indeksu JSON z prostymi polami, kolekcjami i typami złożonymi. Należy zauważyć, że w obrębie typu złożonego każde podpole ma typ i może mieć atrybuty, podobnie jak pola najwyższego poziomu. Schemat odpowiada powyższym przykładom danych. `Address`to złożone pole, które nie jest kolekcją (hotel ma jeden adres). `Rooms`jest złożonym polem zbiórki (hotel ma wiele pokoi).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Aktualizowanie złożonych pól

Wszystkie [reguły ponownego indeksowania,](search-howto-reindex.md) które mają zastosowanie do pól w ogóle, nadal mają zastosowanie do złożonych pól. Ponowienie kilku głównych reguł w tym miejscu, dodanie pola nie wymaga przebudowy indeksu, ale większość modyfikacji zrobić.

### <a name="structural-updates-to-the-definition"></a>Aktualizacje strukturalne definicji

Nowe pola podrzędne można dodawać do złożonego pola w dowolnym momencie bez konieczności przebudowy indeksu. Na przykład dodawanie "ZipCode" do `Address` lub `Rooms` "Udogodnienia" jest dozwolone, podobnie jak dodanie pola najwyższego poziomu do indeksu. Istniejące dokumenty mają wartość null dla nowych pól, dopóki nie będzie jawnie wypełniać te pola, aktualizując dane.

Należy zauważyć, że w obrębie typu złożonego każde podpole ma typ i może mieć atrybuty, podobnie jak pola najwyższego poziomu

### <a name="data-updates"></a>Aktualizacje danych

Aktualizowanie istniejących dokumentów w `upload` indeksie z akcją działa w ten sam sposób w przypadku złożonych i prostych pól — wszystkie pola są zastępowane. Jednak `merge` (lub `mergeOrUpload` po zastosowaniu do istniejącego dokumentu) nie działa tak samo we wszystkich polach. W szczególności `merge` nie obsługuje scalania elementów w kolekcji. To ograniczenie istnieje dla kolekcji typów pierwotnych i kolekcji złożonych. Aby zaktualizować kolekcję, musisz pobrać pełną wartość kolekcji, wprowadzić zmiany, a następnie dołączyć nową kolekcję w żądaniu interfejsu API indeksu.

## <a name="searching-complex-fields"></a>Wyszukiwanie złożonych pól

Wyrażenia wyszukiwania w postaci swobodnej działają zgodnie z oczekiwaniami z typami złożonymi. Jeśli dowolne pole lub podobszerne z wyszukujem w dowolnym miejscu dokumentu jest zgodne, sam dokument jest zgodny.

Zapytania stają się bardziej zniuansowane, gdy masz wiele terminów i operatorów, a niektóre terminy mają określone nazwy pól, jak to możliwe w [przypadku składni Lucene](query-lucene-syntax.md). Na przykład ta kwerenda próbuje dopasować dwa terminy, "Portland" i "OR", do dwóch poda fields pola Adres:

    search=Address/City:Portland AND Address/State:OR

Zapytania takie jak ten są *nieskorelowane* do wyszukiwania pełnotekstowego, w przeciwieństwie do filtrów. W filtrach kwerendy dotyczące podobaborców złożonej kolekcji są [ `any` `all` ](search-query-odata-collection-operators.md)skorelowane przy użyciu zmiennych zakresu w pliku lub . Zapytanie Lucene powyżej zwraca dokumenty zawierające zarówno "Portland, Maine" i "Portland, Oregon", wraz z innymi miastami w Stanie Oregon. Dzieje się tak, ponieważ każda klauzula ma zastosowanie do wszystkich wartości jego pola w całym dokumencie, więc nie ma pojęcia "bieżącego poddokuszu". Aby uzyskać więcej informacji na ten temat, zobacz [Opis filtrów kolekcji OData w usłudze Azure Cognitive Search.](search-query-understand-collection-filters.md)

## <a name="selecting-complex-fields"></a>Wybieranie pól złożonych

Parametr `$select` służy do wybierania pól zwracanych w wynikach wyszukiwania. Aby użyć tego parametru do wybrania określonych poda fields pola złożonego, należy`/`uwzględnić pole nadrzędne i podpole oddzielone ukośnikiem ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

Pola muszą być oznaczone jako możliwe do pobrania w indeksie, jeśli chcesz je w wynikach wyszukiwania. Tylko pola oznaczone jako możliwe do `$select` pobrania mogą być używane w instrukcji.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrowanie, fasetowanie i sortowanie złożonych pól

Ta sama [składnia ścieżki OData](query-odata-filter-orderby-syntax.md) używana do filtrowania i wyszukiwania fielded może być również używana do fasetowania, sortowania i wybierania pól w żądaniu wyszukiwania. W przypadku typów złożonych stosuje się reguły regulujące, które podpola mogą być oznaczone jako sortowalne lub typowalne. Aby uzyskać więcej informacji na temat tych reguł, zobacz [tworzenie interfejsu API indeksu odwołania](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Podobamy fasetowania

Dowolne podobsze można oznaczyć `Edm.GeographyPoint` jako `Collection(Edm.GeographyPoint)`typu lub .

Liczby dokumentów zwrócone w wynikach aspektu są obliczane dla dokumentu nadrzędnego (hotelu), a nie poddokudów w złożonej kolekcji (pokoje). Załóżmy na przykład, że hotel ma 20 pokoi typu "suite". Biorąc pod `facet=Rooms/Type`uwagę ten parametr aspektu, liczba facetów będzie jeden dla hotelu, a nie 20 dla pokoi.

### <a name="sorting-complex-fields"></a>Sortowanie złożonych pól

Operacje sortowania mają zastosowanie do dokumentów (hoteli), a nie do dokumentów podrzędnych (pokoje). Jeśli masz złożoną kolekcję typów, taką jak Pokoje, ważne jest, aby zdać sobie sprawę, że nie możesz w ogóle sortować pokoi. W rzeczywistości nie można sortować na żadnej kolekcji.

Operacje sortowania działają, gdy pola mają pojedynczą wartość dla dokumentu, niezależnie od tego, czy pole jest polem prostym, czy podpolem w typie złożonym. Na przykład `Address/City` może być sortowalny, ponieważ na hotel jest `$orderby=Address/City` tylko jeden adres, więc posortuje hotele według miasta.

### <a name="filtering-on-complex-fields"></a>Filtrowanie pól złożonych

W wyrażeniu filtru można odwoływać się do podołek pola złożonego. Wystarczy użyć tej samej [składni ścieżki OData,](query-odata-filter-orderby-syntax.md) która jest używana do fasetowania, sortowania i wybierania pól. Na przykład następujący filtr zwróci wszystkie hotele w Kanadzie:

    $filter=Address/Country eq 'Canada'

Aby filtrować na złożonym polu kolekcji, można użyć **wyrażenia lambda** z [ `any` operatorami `all` i operatorami](search-query-odata-collection-operators.md). W takim przypadku **zmienną zakresu** wyrażenia lambda jest obiektem z podobakami. Można odwoływać się do tych pól podrzędnych ze standardową składnią ścieżki OData. Na przykład następujący filtr zwróci wszystkie hotele z co najmniej jednym pokojem typu deluxe i wszystkie pokoje dla niepalących:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Podobnie jak w przypadku prostych pól najwyższego poziomu, proste pola podrzędne złożonych pól mogą być `true` uwzględniane w filtrach tylko wtedy, gdy w definicji indeksu ustawiono atrybut **do filtrowania.** Aby uzyskać więcej informacji, zobacz [tworzenie interfejsu API indeksu odwołania](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Następne kroki

Wypróbuj [zestaw danych Hoteli](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) w Kreatorze **importu danych.** Aby uzyskać dostęp do danych, potrzebne będą informacje o połączeniu usługi Cosmos DB podane w pliku readme.

Mając te informacje w ręku, pierwszym krokiem w kreatorze jest utworzenie nowego źródła danych usługi Azure Cosmos DB. Dalej w kreatorze, gdy dojdziesz do strony indeksu docelowego, zobaczysz indeks ze złożonymi typami. Utwórz i załaduj ten indeks, a następnie wykonaj kwerendy, aby zrozumieć nową strukturę.

> [!div class="nextstepaction"]
> [Szybki start: kreator portalu do importowania, indeksowania i kwerend](search-get-started-portal.md)
