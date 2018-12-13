---
title: Jak stronicować elementów na stronie wyników wyszukiwania — usługa Azure Search
description: Podział na strony w usłudze Azure Search, Usługa wyszukiwania w hostowanej chmurze Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f36dbb72e2518f7e3a27ef3aadec85312d751c2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309346"
---
# <a name="how-to-page-search-results-in-azure-search"></a>Jak stronicować wyniki wyszukiwania w usłudze Azure Search
Ten artykuł zawiera wskazówki dotyczące sposobu używania interfejsu API REST usługi Azure Search Service do zaimplementowania standardowe elementy strony wyników wyszukiwania, takie jak całkowitej liczby, pobierania dokumentu, porządek sortowania i nawigacji.

W każdym przypadku wymienionych poniżej podano związanych ze stronami opcje, które przyczyniają się do danych i informacji na stronie wyników wyszukiwania za pomocą [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) żądań wysyłanych do usługi Azure Search. Żądania obejmują polecenie GET, parametry zapytania, które informują usługę, co jest wymagana i ścieżki oraz jak sformułować odpowiedzi.

> [!NOTE]
> Prawidłowemu żądaniu obejmuje pewną liczbę elementów, takich jak adres URL usługi i ścieżkę, czasownik HTTP `api-version`i tak dalej. Celu skrócenia programu firma Microsoft spacje przykłady, aby wyróżnić składnię która jest odpowiednia do dzielenia na strony. Zobacz [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice) dokumentacji, aby uzyskać szczegółowe informacje o składni żądania.
> 
> 

## <a name="total-hits-and-page-counts"></a>Łączna liczba trafień i liczby stron
Wyświetlanie całkowita liczba wyników zwróconych w wyniku zapytania, a następnie zwracanie tych wyników, na mniejsze fragmenty jest niezbędne do praktycznie wszystkich stron wyszukiwania.

![][1]

W usłudze Azure Search, możesz użyć `$count`, `$top`, i `$skip` parametry, aby zwracać następujące wartości. W poniższym przykładzie przedstawiono przykładowe żądanie dla łączna liczba trafień, zwracane jako `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Możesz pobierać dokumenty w grupach, 15, a także pokazać łączna liczba trafień, zaczynając od pierwszej strony:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Podział na strony wyników wymaga zarówno `$top` i `$skip`, gdzie `$top` Określa, ile elementów do zwrócenia w partii i `$skip` Określa, ile elementów do pominięcia. W poniższym przykładzie strona pokazuje elementy następne 15, każdy wskazywanym przez przyrostowe przechodzi w `$skip` parametru.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Układ
Na stronie wyników wyszukiwania możesz chcieć wyświetlić obraz miniatury, podzestaw pól i link do strony pełnej wersji produktu.

 ![][2]

W usłudze Azure Search, należy użyć `$select` i polecenie wyszukiwania w celu wdrożenia tego środowiska.

Aby zwrócić podzestaw pól fragmentacji układu:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Obrazy i pliki multimedialne nie są bezpośrednio można wyszukiwać i powinny być przechowywane w innej platformie magazynu, takich jak usługi Azure Blob storage, aby zmniejszyć koszty. Indeks i dokumenty zdefiniuj pole, które przechowuje adres URL zawartości zewnętrznej. Można następnie użyć pola jako odwołanie do obrazu. Adres URL obrazu, należy w dokumencie.

Do pobrania na stronie opisu produktu **onClick** zdarzenia, użyj [wyszukiwania dokumentu](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) do przekazania w kluczu dokumentu do pobrania. Typ danych klucza jest `Edm.String`. W tym przykładzie jest *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortuj według istotności, ocena ani ceny
Sortuj porządkuje często domyślnie istotności, ale często jest utworzenie alternatywnych sortowania zamówień łatwo dostępne, dzięki czemu klienci mogą szybko zamieniać istniejące wyniki w kolejności innej rangi.

 ![][3]

W usłudze Azure Search sortowanie opiera się na `$orderby` wyrażenia dla wszystkich pól, które są indeksowane jako `"Sortable": true.`

Wg istotności jest zdecydowanie skojarzony z profile oceniania. Umożliwia domyślne oceniania opiera się na Analiza tekstu i statystyki rank kolejności wszystkie wyniki z wyższym wyniki przejściem do dokumentów za pomocą więcej lub silniejsze dopasowania terminu wyszukiwania.

Kolejność sortowania alternatywne są zwykle skojarzone z **onClick** zdarzenia, które wywołanie zwrotne metody, która tworzy kolejność sortowania. Na przykład biorąc pod uwagę ten element na stronie:

 ![][4]

Należy utworzyć metodę, która akceptuje opcję sortowania jako dane wejściowe i zwraca uporządkowaną listę kryteriów skojarzone z tej opcji.

 ![][5]

> [!NOTE]
> Podczas oceniania domyślny jest wystarczające w wielu scenariuszach, firma Microsoft zaleca, zamiast tego tworzony istotności na niestandardowy profil oceniania. Niestandardowy profil oceniania umożliwia elementów zwiększenie wydajności, które są bardziej korzystne dla Twojej firmy. Zobacz [Dodaj profil oceniania](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) Aby uzyskać więcej informacji. 
> 
> 

## <a name="faceted-navigation"></a>Nawigacja aspektowa
Nawigacja po wyszukiwaniu jest powszechne na stronę wyników, często znajduje się na stronie lub u góry strony. W usłudze Azure Search nawigacji aspektowej umożliwia wyszukiwanie kierunkowane samodzielnie przez użytkowników, oparte na wstępnie zdefiniowanych filtrów. Zobacz [nawigację Aspektową w usłudze Azure Search](search-faceted-navigation.md) Aby uzyskać szczegółowe informacje.

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony
Projektowanego rozwiązania zawiera strony wyszukiwania dedykowane dla określonych typów zawartości (na przykład aplikacja handlu detalicznego w trybie online, która ma działów, wyświetlonym u góry strony), można wstawić wyrażenie filtru, wraz z **onClick**zdarzenie, aby otworzyć stronę w stanie wstępnie przefiltrowanej. 

Możesz wysłać z lub bez wyrażenia filtru. Na przykład następujące żądanie będzie filtru marką zwracanie tylko tych dokumentów, które odpowiadają go.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Zobacz [wyszukiwania dokumentów (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) Aby uzyskać więcej informacji na temat `$filter` wyrażenia.

## <a name="see-also"></a>Zobacz też
* [Interfejs API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operacje indeksowania](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operacje na dokumentach](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Nawigacja aspektowa w usłudze Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
