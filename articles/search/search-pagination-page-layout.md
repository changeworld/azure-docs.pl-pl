---
title: Jak korzystać z wyników wyszukiwania — Azure Search
description: Tworzenie struktury i sortowanie wyników wyszukiwania, pobieranie liczby dokumentów i Dodawanie nawigacji zawartości do wyników wyszukiwania w Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb86a75be464cb78a16170626bc96778d43bb8b6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974617"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Jak korzystać z wyników wyszukiwania w Azure Search
Ten artykuł zawiera wskazówki dotyczące implementowania standardowych elementów strony wyników wyszukiwania, takich jak łączna liczba, pobieranie dokumentów, kolejność sortowania i nawigacja. Opcje dotyczące strony, które tworzą dane lub informacje w wynikach wyszukiwania, są określane za pomocą żądań przeszukiwania [dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wysyłanych do usługi Azure Search. 

W interfejsie API REST żądania obejmują pobieranie parametrów poleceń, ścieżek i zapytań, które informują o tym, co jest wymagane, i jak sformułować odpowiedź. W zestawie SDK platformy .NET równoważny interfejs API jest [klasą DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Kilka przykładów kodu zawiera interfejs frontonu sieci Web, który można znaleźć tutaj: Aplikacje demonstracyjne i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd)w [Nowym Jorku](https://azjobsdemo.azurewebsites.net/) .

> [!NOTE]
> Prawidłowe żądanie zawiera wiele elementów, takich jak adres URL usługi i ścieżka, czasownik `api-version`http itd. W przypadku zwięzłości zostały przycięte przykłady w celu wyróżnienia tylko składni, która jest istotna dla dzielenia na strony. Aby uzyskać więcej informacji na temat składni żądania, zobacz [Azure Search REST usługi](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Łączna liczba trafień i liczb stron

Pokazywanie łącznej liczby wyników zwróconych z zapytania, a następnie zwrócenie tych wyników w mniejszych fragmentach, ma podstawowe znaczenie dla praktycznie wszystkich stron wyszukiwania.

![][1]

W Azure Search Użyj `$count`parametrów, `$top`i `$skip` , aby zwrócić te wartości. Poniższy przykład przedstawia przykładowe żądanie dla łącznej liczby trafień w indeksie o nazwie "online-Catalog", `@odata.count`zwracane jako:

    GET /indexes/online-catalog/docs?$count=true

Pobierz dokumenty z grup 15, a także Pokaż łączne trafienia, zaczynając od pierwszej strony:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Wyniki paginating wymagają zarówno `$top` , `$skip`jak i `$top` , gdzie określa liczbę elementów do zwrócenia w partii i `$skip` określa liczbę elementów do pominięcia. W poniższym przykładzie każda strona pokazuje następne 15 elementów, wskazywanych przez przyrostowe uskoki w `$skip` parametrze.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Układ

Na stronie Wyniki wyszukiwania możesz chcieć wyświetlić obraz miniatury, podzestaw pól i link do strony kompletnego produktu.

 ![][2]

W Azure Search można użyć `$select` [żądania interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) i zaimplementowania tego środowiska.

Aby zwrócić podzestaw pól dla układu wieloukładowego:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Obrazy i pliki multimedialne nie są bezpośrednio przeszukiwane i powinny być przechowywane na innej platformie magazynu, takiej jak usługa Azure Blob Storage, aby zmniejszyć koszty. W indeksie i dokumentach Zdefiniuj pole przechowujące adres URL zawartości zewnętrznej. Można następnie użyć pola jako odwołania do obrazu. Adres URL obrazu powinien znajdować się w dokumencie.

Aby pobrać stronę opisu produktu dla zdarzenia **onkliknięcia** , użyj [dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) do przekazania klucza dokumentu do pobrania. Typem danych klucza jest `Edm.String`. W tym przykładzie jest to *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortuj według istotności, klasyfikacji lub ceny

Kolejność sortowania jest często określana jako istotna, ale jest powszechna możliwość zapewnienia, że alternatywne zamówienia sortowania są łatwo dostępne, dzięki czemu klienci mogą szybko ponownie wylosować istniejące wyniki w innym porządku rangi.

 ![][3]

W Azure Search sortowanie jest oparte na `$orderby` wyrażeniu, dla wszystkich pól, które są indeksowane `$orderby` jako `"Sortable": true.` klauzula jest wyrażeniem OData. Aby uzyskać informacje na temat składni, zobacz [składnia wyrażenia OData dla filtrów i klauzul ORDER by](query-odata-filter-orderby-syntax.md).

Istotność jest silnie skojarzona z profilami oceniania. Możesz użyć domyślnej oceny, która opiera się na analizie tekstu i statystyce do klasyfikowania wszystkich wyników, z wyższymi wynikami do dokumentów o większej lub silniejszym dopasowań w wyszukiwanym terminie.

Alternatywne zamówienia sortowania są zwykle skojarzone ze  zdarzeniami onkliknięcia, które wywołują z powrotem do metody, która kompiluje porządek sortowania. Na przykład, uwzględniając ten element strony:

 ![][4]

Utworzysz metodę, która akceptuje wybraną opcję sortowania jako dane wejściowe i zwraca listę uporządkowaną dla kryteriów skojarzonych z tą opcją.

 ![][5]

> [!NOTE]
> Chociaż domyślne ocenianie jest wystarczające dla wielu scenariuszy, zalecamy oparcie w niestandardowym profilu oceniania. Niestandardowy profil oceniania umożliwia podniesienie poziomu elementów, które są bardziej korzystne dla Twojej firmy. Aby uzyskać więcej informacji, zobacz [Dodawanie profilów oceniania](index-add-scoring-profiles.md) .
>

## <a name="faceted-navigation"></a>Nawigacja aspektowa

Nawigacja wyszukiwania jest wspólna na stronie wyników, często znajdującej się na stronie lub w górnej części strony. W Azure Search Nawigacja aspektowa udostępnia funkcję wyszukiwania z własnym przekierowaniem na podstawie wstępnie zdefiniowanych filtrów. Aby uzyskać szczegółowe informacje, zobacz [nawigację aspektową w Azure Search](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony

Jeśli projekt rozwiązania zawiera dedykowane strony wyszukiwania dla określonych typów zawartości (na przykład aplikacji detalicznej online zawierającej działy w górnej części strony), można wstawić [wyrażenie filtru](search-filters.md) obok zdarzenia onkliknięcia do  Otwórz stronę w stanie sprzed przefiltrowania.

Filtr można wysłać z wyrażeniem wyszukiwania lub bez niego. Na przykład następujące żądanie przefiltruje nazwę marki, zwracając tylko te dokumenty, które pasują do niego.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Aby uzyskać więcej informacji na temat `$filter` wyrażeń, zobacz [dokumenty wyszukiwania (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

## <a name="see-also"></a>Zobacz też

- [Interfejs API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice)
- [Operacje indeksowania](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operacje dokumentu](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Nawigacja aspektowa w Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
