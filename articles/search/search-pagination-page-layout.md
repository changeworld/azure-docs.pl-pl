---
title: Jak pracować z wynikami wyszukiwania
titleSuffix: Azure Cognitive Search
description: Strukturyzuj i sortuj wyniki wyszukiwania, uzyskaj liczbę dokumentów i dodaj nawigację zawartości do wyników wyszukiwania w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481606"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search
Ten artykuł zawiera wskazówki dotyczące implementowania standardowych elementów strony wyników wyszukiwania, takich jak całkowita liczba, pobieranie dokumentów, sortowanie zamówień i nawigacja. Opcje związane ze stroną, które współtworzyą dane lub informacje do wyników wyszukiwania, są określane za pomocą żądań [dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wysyłanych do usługi Azure Cognitive Search. 

W interfejsie API REST żądania zawierają polecenie GET, ścieżkę i parametry kwerendy, które informują usługę o żądanym żądaniu i jak sformułować odpowiedź. W pliku .NET SDK równoważnym interfejsem API jest [klasa DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Aby szybko wygenerować stronę wyszukiwania dla klienta, zapoznaj się z tymi opcjami:

+ Użyj [generatora aplikacji](search-create-app-portal.md) w portalu, aby utworzyć stronę HTML z paskiem wyszukiwania, nawigacją aspektową i obszarem wyników.
+ Postępuj zgodnie [z Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-create-first-app.md) samouczek, aby utworzyć klienta funkcjonalnego.

Kilka przykładów kodu zawiera interfejs frontonu sieci Web, który można znaleźć tutaj: [Aplikacja demonstracyjna New York City Jobs,](https://aka.ms/azjobsdemo) [przykładowy kod JavaScript z witryną demonstracyjnej na żywo](https://github.com/liamca/azure-search-javascript-samples)i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Prawidłowe żądanie zawiera szereg elementów, takich jak adres URL `api-version`usługi i ścieżka, zlecenie HTTP i tak dalej. Dla zwięzłości przycinamy przykłady, aby wyróżnić tylko składnię, która jest istotna dla podziałki na strony. Aby uzyskać więcej informacji na temat składni żądań, zobacz [Interfejsy API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Łączna liczba trafień i liczba stron

Wyświetlanie całkowitej liczby wyników zwróconych z kwerendy, a następnie zwracanie tych wyników w mniejszych fragmentach, ma podstawowe znaczenie dla praktycznie wszystkich stron wyszukiwania.

![][1]

W usłudze Azure Cognitive `$count` `$top`Search `$skip` używasz programu , i parametrów do zwrócenia tych wartości. Poniższy przykład przedstawia przykładowe żądanie całkowitej liczby trafień w `@odata.count`indeksie o nazwie "katalog online", zwrócone jako:

    GET /indexes/online-catalog/docs?$count=true

Pobierz dokumenty w grupach po 15, a także pokaż łączną liczbę trafień, zaczynając od pierwszej strony:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Wyniki podziałania na `$top` `$skip`strony `$top` wymagają zarówno, jak i , gdzie `$skip` określa, ile elementów do zwrócenia w partii i określa, ile elementów pominąć. W poniższym przykładzie każda strona pokazuje następne 15 elementów, wskazywanych przez przyrostowe skoki w parametrze. `$skip`

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Układ

Na stronie wyników wyszukiwania możesz chcieć wyświetlić obraz miniatury, podzbiór pól i łącze do pełnej strony produktu.

 ![][2]

W usłudze Azure Cognitive `$select` Search należy użyć i [żądanie interfejsu API wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/search-documents) aby zaimplementować to środowisko.

Aby zwrócić podzbiór pól dla układu sąsiadującego:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Obrazy i pliki multimedialne nie są bezpośrednio przeszukiwalne i powinny być przechowywane w innej platformie magazynu, takiej jak magazyn obiektów Blob platformy Azure, aby zmniejszyć koszty. W indeksie i dokumentach zdefiniuj pole, w które przechowuje adres URL zawartości zewnętrznej. Następnie można użyć pola jako odniesienia do obrazu. Adres URL obrazu powinien znajdować się w dokumencie.

Aby pobrać stronę opisu produktu dla zdarzenia **onClick,** użyj [dokumentu odnośnego,](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) aby przekazać klucz dokumentu do pobrania. Typ danych klucza `Edm.String`to . W tym przykładzie jest *to 246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortowanie według trafności, klasyfikacji lub ceny

Sortuj zamówienia często domyślnie do istotności, ale jest to wspólne, aby alternatywne zamówienia sortowania łatwo dostępne, tak aby klienci mogą szybko przetasowania istniejących wyników w innej kolejności.

 ![][3]

W usłudze Azure Cognitive Search `$orderby` sortowanie jest oparte na wyrażeniu, dla wszystkich pól, które są indeksowane jako `"Sortable": true.` klauzula An `$orderby` jest wyrażeniem OData. Aby uzyskać informacje o składni, zobacz [Składnia wyrażenia OData dla filtrów i klauzul typu "kolejność według".](query-odata-filter-orderby-syntax.md)

Trafność jest silnie związana z profilami oceniania. Można użyć domyślnego oceniania, który opiera się na analizie tekstu i statystykach, aby uszeregować kolejność wszystkich wyników, przy czym wyższe wyniki będą trafiać do dokumentów z większą lub silniejszą dopasowaniami w wyszukiwanym terminie.

Alternatywne zamówienia sortowania są zazwyczaj skojarzone ze zdarzeniami **onClick,** które odwołują się do metody, która tworzy kolejność sortowania. Na przykład, biorąc pod uwagę ten element strony:

 ![][4]

Należy utworzyć metodę, która akceptuje wybraną opcję sortowania jako dane wejściowe i zwraca uporządkowaną listę kryteriów skojarzonych z tą opcją.

 ![][5]

> [!NOTE]
> Podczas gdy domyślne ocenianie jest wystarczające dla wielu scenariuszy, zaleca się oparcie trafności na niestandardowym profilu oceniania. Niestandardowy profil punktacji umożliwia promowanie przedmiotów, które są bardziej korzystne dla Twojej firmy. Aby uzyskać więcej [informacji, zobacz Dodawanie profili oceniania.](index-add-scoring-profiles.md)
>

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Formatowanie można zastosować do pasujących terminów w wynikach wyszukiwania, co ułatwia wykrycie dopasowania. Instrukcje wyróżniania trafienia znajdują się w [żądaniu kwerendy](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Formatowanie jest stosowane do kwerend cały termin. Kwerendy na warunkach częściowych, takich jak wyszukiwanie rozmyte lub wieloznaczne wyszukiwania, które powodują rozszerzenie kwerendy w silniku, nie można użyć wyróżniania trafień.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Usługi utworzone po 15 lipca 2020 r. zapewnią inne wrażenia z wyróżniania. Usługi utworzone przed tą datą nie zmienią się w ich zachowaniu wyróżniania. Dzięki tej zmianie zostaną zwrócone tylko frazy, które pasują do kwerendy pełnej frazy. Ponadto będzie można określić rozmiar fragmentu zwrócony dla podświetlenia.
>
> Podczas pisania kodu klienta, który implementuje naciśnięcie wyróżniania, należy pamiętać o tej zmianie. Należy zauważyć, że nie będzie to miało wpływu na ciebie, chyba że utworzysz zupełnie nową usługę wyszukiwania.

## <a name="faceted-navigation"></a>Nawigacja aspektowa

Nawigacja w wynikach jest często spotykana na stronie wyników, często znajdującej się z boku lub u góry strony. W usłudze Azure Cognitive Search nawigacja aspektowa zapewnia wyszukiwanie kierowane samodzielnie na podstawie wstępnie zdefiniowanych filtrów. Zobacz [nawigacji aspektowej w usłudze Azure Cognitive Search, aby](search-faceted-navigation.md) uzyskać szczegółowe informacje.

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony

Jeśli projekt rozwiązania zawierał dedykowane strony wyszukiwania dla określonych typów zawartości (na przykład aplikacja sieci sprzedaży online, która ma działy wymienione w górnej części strony), można wstawić [wyrażenie filtru](search-filters.md) obok zdarzenia **onClick,** aby otworzyć stronę w stanie wstępnie filtrowanym.

Filtr można wysyłać z wyrażeniem wyszukiwania lub bez niego. Na przykład następujące żądanie będzie filtrować nazwę marki, zwracając tylko te dokumenty, które go odpowiadają.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Aby uzyskać więcej informacji na temat `$filter` wyrażeń, zobacz Dokumenty wyszukiwania [(azure cognitive search API).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

## <a name="see-also"></a>Zobacz też

- [Interfejs API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice)
- [Operacje indeksu](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operacje na dokumentach](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Nawigacja aspektowa w wyszukiwarce cognitive azure](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
