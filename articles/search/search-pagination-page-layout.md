---
title: Jak pracować z wyników wyszukiwania — usługa Azure Search
description: Struktury i sortowanie wyników wyszukiwania, Pobierz liczbę dokumentów i Dodaj nawigowania po zawartości do wyników wyszukiwania w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: dc208f3231882a0726733c897e70557c657cddf3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024524"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Jak pracować z usługą wyszukiwania wyników w usłudze Azure Search
Ten artykuł zawiera wskazówki dotyczące implementowania standardowe elementy strony wyników wyszukiwania, takie jak całkowitej liczby, pobierania dokumentu, porządek sortowania i nawigacji. Określono opcje związanych ze stronami, które przyczyniają się do danych lub informacji do wyników wyszukiwania za pomocą [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) żądań wysyłanych do usługi Azure Search. 

W interfejsie API REST żądania obejmują polecenie GET, parametry zapytania, które informują usługę, co jest wymagana i ścieżki oraz jak sformułować odpowiedzi. W zestawie SDK platformy .NET jest równoważnego API [klasy DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult?view=azure-dotnet).

Kilka przykładów kodu obejmują interfejs frontonu sieci web, który można znaleźć tutaj: [Aplikacja demonstracyjna, z nowego Jorku zadania](https://azjobsdemo.azurewebsites.net/) i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Prawidłowemu żądaniu obejmuje pewną liczbę elementów, takich jak adres URL usługi i ścieżkę, czasownik HTTP `api-version`i tak dalej. Celu skrócenia programu firma Microsoft spacje przykłady, aby wyróżnić składnię która jest odpowiednia do dzielenia na strony. Aby uzyskać więcej informacji na temat składni żądania zobacz [interfejsu REST usługi Search Azure](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Łączna liczba trafień i liczby stron
Wyświetlanie całkowita liczba wyników zwróconych w wyniku zapytania, a następnie zwracanie tych wyników, na mniejsze fragmenty jest niezbędne do praktycznie wszystkich stron wyszukiwania.

![][1]

W usłudze Azure Search, możesz użyć `$count`, `$top`, i `$skip` parametry, aby zwracać następujące wartości. W poniższym przykładzie pokazano przykładowe żądanie dla łączna liczba trafień dla indeksu o nazwie "onlineCatalog", zwracane jako `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Możesz pobierać dokumenty w grupach, 15, a także pokazać łączna liczba trafień, zaczynając od pierwszej strony:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Podział na strony wyników wymaga zarówno `$top` i `$skip`, gdzie `$top` Określa, ile elementów do zwrócenia w partii i `$skip` Określa, ile elementów do pominięcia. W poniższym przykładzie strona pokazuje elementy następne 15, każdy wskazywanym przez przyrostowe przechodzi w `$skip` parametru.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout
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

W usłudze Azure Search sortowanie opiera się na `$orderby` wyrażenia dla wszystkich pól, które są indeksowane jako `"Sortable": true.` `$orderby` klauzula jest wyrażenia OData. Aby uzyskać informacje dotyczące składni, zobacz [składnia wyrażenia OData, filtry i klauzule w klauzuli order by](query-odata-filter-orderby-syntax.md).

Wg istotności jest zdecydowanie skojarzony z profile oceniania. Umożliwia domyślne oceniania opiera się na Analiza tekstu i statystyki rank kolejności wszystkie wyniki z wyższym wyniki przejściem do dokumentów za pomocą więcej lub silniejsze dopasowania terminu wyszukiwania.

Kolejność sortowania alternatywne są zwykle skojarzone z **onClick** zdarzenia, które wywołanie zwrotne metody, która tworzy kolejność sortowania. Na przykład biorąc pod uwagę ten element na stronie:

 ![][4]

Należy utworzyć metodę, która akceptuje opcję sortowania jako dane wejściowe i zwraca uporządkowaną listę kryteriów skojarzone z tej opcji.

 ![][5]

> [!NOTE]
> Podczas oceniania domyślny jest wystarczające w wielu scenariuszach, firma Microsoft zaleca, zamiast tego tworzony istotności na niestandardowy profil oceniania. Niestandardowy profil oceniania umożliwia elementów zwiększenie wydajności, które są bardziej korzystne dla Twojej firmy. Zobacz [dodać profile oceniania](index-add-scoring-profiles.md) Aby uzyskać więcej informacji. 
> 
> 

## <a name="faceted-navigation"></a>Nawigacja aspektowa
Nawigacja po wyszukiwaniu jest powszechne na stronę wyników, często znajduje się na stronie lub u góry strony. W usłudze Azure Search nawigacji aspektowej umożliwia wyszukiwanie kierunkowane samodzielnie przez użytkowników, oparte na wstępnie zdefiniowanych filtrów. Zobacz [nawigację Aspektową w usłudze Azure Search](search-faceted-navigation.md) Aby uzyskać szczegółowe informacje.

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony
Jeśli projektowanego rozwiązania zawiera strony wyszukiwania dedykowane dla określonych typów zawartości (na przykład aplikacja handlu detalicznego w trybie online, która ma działów, wyświetlonym u góry strony), można wstawić [wyrażenie filtru](search-filters.md) obok **onClick** zdarzenie, aby otworzyć stronę w stanie wstępnie przefiltrowanej. 

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
