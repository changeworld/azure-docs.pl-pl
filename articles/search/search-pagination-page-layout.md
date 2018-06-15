---
title: Jak strony wyników wyszukiwania w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Podział na strony w usłudze Azure Search, Usługa wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 516760031918c667b39cc8b3dd94d91c42623efc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186882"
---
# <a name="how-to-page-search-results-in-azure-search"></a>Jak stronicować wyniki wyszukiwania w usłudze Azure Search
Ten artykuł zawiera wskazówki dotyczące sposobu używania interfejsu API REST usługi wyszukiwanie Azure do zaimplementowania standardowe elementy strony wyników wyszukiwania, takie jak całkowitej liczby, pobierania dokumentu, sortowania i nawigacji.

W każdym przypadku wymienione poniżej podano związanych ze stronami opcje, które danych lub informacji do strony wyników wyszukiwania za pomocą [wyszukiwania dokumentu](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) żądania wysyłane do usługi Azure Search. Żądania obejmują polecenie GET, parametry zapytania, które informują usługę, co jest wymagany i ścieżkę oraz sposobu sformułować odpowiedzi.

> [!NOTE]
> Nieprawidłowa żądania zawiera wiele elementów, takich jak adres URL usługi i ścieżkę, Zlecenie HTTP, `api-version`i tak dalej. Jednak firma Microsoft usuwane przykłady, aby wyróżnić składnię związany podział na strony. Zobacz [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) dokumentacji, aby uzyskać więcej informacji o składni żądania.
> 
> 

## <a name="total-hits-and-page-counts"></a>Całkowita liczba trafień i stroną
Wyświetlana jest całkowita liczba wyników zwróconych w wyniku zapytania, a następnie zwracanie uzyskanych w mniejsze fragmenty jest niezbędne, aby niemal wszystkie strony wyszukiwania.

![][1]

W usłudze Azure Search, możesz użyć `$count`, `$top`, i `$skip` parametrów, aby zwrócić te wartości. W poniższym przykładzie przedstawiono przykładowe żądanie dla całkowita liczba trafień zwracane jako `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Pobrać dokumentów w grupach, 15, a także wyświetlać całkowita liczba trafień, zaczynając od pierwszej strony:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Dzielenie na strony wyników wymaga obu `$top` i `$skip`, gdzie `$top` określa liczbę elementów do zwrócenia w partii, a `$skip` określa liczbę elementów do pominięcia. W poniższym przykładzie, strona zawiera elementy obok 15 każdego wskazywanym przez przyrostowe przechodzi w `$skip` parametru.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Układ
Na stronie wyników wyszukiwania można pokazać obraz miniatury, podzbiór pól i łącze do strony produkt w pełnym.

 ![][2]

W usłudze Azure Search, należy użyć `$select` i polecenie wyszukiwania w celu wykonania tej czynności.

Aby przywrócić podzbiór pól dla układzie sąsiadującym:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Obrazów i plików multimedialnych na pliki nie są bezpośrednio można wyszukiwać i powinny być przechowywane w innej platformie magazynu, takie jak magazyn obiektów Blob platformy Azure, aby zmniejszyć koszty. Indeks i dokumenty zdefiniuj pole, które przechowuje adres URL zawartości zewnętrznej. Pole można następnie użyć jako odwołanie do obrazu. Adres URL obrazu, należy w dokumencie.

Do pobrania na stronie opisu produktu **onClick** zdarzenia, użyj [wyszukiwania dokumentu](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) do przekazania w kluczu dokumentu do pobrania. Typ danych klucza jest `Edm.String`. W tym przykładzie jest *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortuj według przydatności, klasyfikacji lub cen
Często domyślnie istotność porządki sortowania, ale często, aby udostępnić alternatywny sortowania zamówień łatwo tak, aby klienci można szybko zamieniać istniejące wyniki w innej kolejności rangi.

 ![][3]

W wyszukiwanie, sortowanie jest oparta na `$orderby` wyrażenie, dla wszystkich pól, które są indeksowane jako `"Sortable": true.`

Istotne jest silnie skojarzony z oceniania profilów. Umożliwia oceniania domyślny, który korzysta z analizy tekstu i statystyki rank kolejności wszystkie wyniki z wyższej wyniki przejściem do dokumentów za pomocą więcej lub silniejszych dopasowań wyszukiwanego terminu.

Alternatywne sortowania są zwykle skojarzone z **onClick** zdarzenia, które wywołanie zwrotne metody, która tworzy kolejności sortowania. Na przykład, dla danego elementu tej strony:

 ![][4]

Należy utworzyć metodę, która akceptuje opcję sortowania jako dane wejściowe i zwraca listy uporządkowanej dla kryteria skojarzone z tej opcji.

 ![][5]

> [!NOTE]
> Podczas oceniania domyślne są wystarczające w różnych scenariuszach, firma Microsoft zaleca tworzony istotność na niestandardowego profilu oceniania zamiast tego. Niestandardowy profil oceniania umożliwia zwiększenie wydajności elementów, które są w firmie. Zobacz [Dodaj profil oceniania](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) Aby uzyskać więcej informacji. 
> 
> 

## <a name="faceted-navigation"></a>Nawigacja aspektowa
Wyszukiwanie nawigacji jest często na stronę wyników często znajdujący się w górnej części strony lub po stronie. W usłudze Azure Search nawigacji aspektowej zapewnia samodzielnego wyszukiwanie oparte na wstępnie zdefiniowanych filtrów. Zobacz [nawigacji Aspektowej w usłudze Azure Search](search-faceted-navigation.md) szczegółowe informacje.

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony
Jeśli projektowanego rozwiązania uwzględnione dedykowane wyszukiwanie dla określonych typów zawartości (na przykład aplikacja online sprzedaży detalicznej, która ma działów wymienionych w górnej części strony), można wstawić wyrażenie filtru obok **onClick** zdarzeń, aby otworzyć stronę w stanie wstępnie przefiltrowanej. 

Możesz wysłać z lub bez wyrażenia filtru. Na przykład następujące żądanie zostanie filtrowanie według marką zwracanie tylko tych dokumentów, które jest zgodny.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Zobacz [wyszukiwania dokumentów (interfejsu API usługi Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) uzyskać więcej informacji o `$filter` wyrażenia.

## <a name="see-also"></a>Zobacz też
* [Interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operacje na indeksie](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operacje dokumentu](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Wideo i samouczki dotyczące usługi Azure Search](search-video-demo-tutorial-list.md)
* [Nawigacji aspektowej w usłudze Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
