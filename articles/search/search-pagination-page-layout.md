---
title: Jak korzystać z wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: Tworzenie struktury i sortowanie wyników wyszukiwania, pobieranie liczby dokumentów i Dodawanie nawigacji zawartości do wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721578"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Jak korzystać z wyników wyszukiwania w usłudze Azure Wyszukiwanie poznawcze
Ten artykuł zawiera wskazówki dotyczące implementowania standardowych elementów strony wyników wyszukiwania, takich jak łączna liczba, pobieranie dokumentów, kolejność sortowania i nawigacja. Opcje dotyczące strony, które tworzą dane lub informacje w wynikach wyszukiwania, są określane za pomocą żądań [przeszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) wysyłanych do usługi Azure wyszukiwanie poznawcze. 

W interfejsie API REST żądania obejmują pobieranie parametrów poleceń, ścieżek i zapytań, które informują o tym, co jest wymagane, i jak sformułować odpowiedź. W zestawie SDK platformy .NET równoważny interfejs API jest [klasą DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Aby szybko wygenerować stronę wyszukiwania dla klienta, zapoznaj się z następującymi opcjami:

+ Użyj [generatora aplikacji](search-create-app-portal.md) w portalu, aby utworzyć stronę HTML z paskiem wyszukiwania, nawigacją aspektową i obszarem wyników.
+ Postępuj zgodnie z samouczkiem [Tworzenie C# pierwszej aplikacji w](tutorial-csharp-create-first-app.md) celu utworzenia klienta funkcjonalnego.

Kilka przykładów kodu obejmuje interfejs frontonu sieci Web, który można znaleźć tutaj: [aplikacja demonstracyjna zadań w Nowym Jorku](https://azjobsdemo.azurewebsites.net/), [przykładowy kod JavaScript z aktywną witryną demonstracyjną](https://github.com/liamca/azure-search-javascript-samples)i [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Prawidłowe żądanie zawiera wiele elementów, takich jak adres URL usługi i ścieżka, zlecenie HTTP, `api-version`itd. W przypadku zwięzłości zostały przycięte przykłady w celu wyróżnienia tylko składni, która jest istotna dla dzielenia na strony. Aby uzyskać więcej informacji na temat składni żądania, zobacz [interfejsy API REST platformy Azure wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Łączna liczba trafień i liczb stron

Pokazywanie łącznej liczby wyników zwróconych z zapytania, a następnie zwrócenie tych wyników w mniejszych fragmentach, ma podstawowe znaczenie dla praktycznie wszystkich stron wyszukiwania.

![][1]

W przypadku usługi Azure Wyszukiwanie poznawcze należy użyć parametrów `$count`, `$top`i `$skip`, aby zwrócić te wartości. Poniższy przykład przedstawia przykładowe żądanie dla łącznej liczby trafień w indeksie o nazwie "online-Catalog", zwracane jako `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

Pobierz dokumenty z grup 15, a także Pokaż łączne trafienia, zaczynając od pierwszej strony:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Wyniki paginating wymagają zarówno `$top`, jak i `$skip`, gdzie `$top` określa liczbę elementów do zwrócenia w partii, a `$skip` określa liczbę elementów do pominięcia. W poniższym przykładzie każda strona pokazuje następne 15 elementów, wskazywanych przez przyrostowe uskoki w parametrze `$skip`.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

Na stronie Wyniki wyszukiwania możesz chcieć wyświetlić obraz miniatury, podzestaw pól i link do strony kompletnego produktu.

 ![][2]

W przypadku usługi Azure Wyszukiwanie poznawcze należy użyć `$select` i [żądania interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) do zaimplementowania tego środowiska.

Aby zwrócić podzestaw pól dla układu wieloukładowego:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Obrazy i pliki multimedialne nie są bezpośrednio przeszukiwane i powinny być przechowywane na innej platformie magazynu, takiej jak usługa Azure Blob Storage, aby zmniejszyć koszty. W indeksie i dokumentach Zdefiniuj pole przechowujące adres URL zawartości zewnętrznej. Można następnie użyć pola jako odwołania do obrazu. Adres URL obrazu powinien znajdować się w dokumencie.

Aby pobrać stronę opisu produktu dla zdarzenia **onkliknięcia** , użyj [dokumentu wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) do przekazania klucza dokumentu do pobrania. Typ danych klucza jest `Edm.String`. W tym przykładzie jest to *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortuj według istotności, klasyfikacji lub ceny

Kolejność sortowania jest często określana jako istotna, ale jest powszechna możliwość zapewnienia, że alternatywne zamówienia sortowania są łatwo dostępne, dzięki czemu klienci mogą szybko ponownie wylosować istniejące wyniki w innym porządku rangi.

 ![][3]

Na platformie Azure Wyszukiwanie poznawcze sortowanie opiera się na wyrażeniu `$orderby`, dla wszystkich pól, które są indeksowane jako `"Sortable": true.` klauzula `$orderby` jest wyrażeniem OData. Aby uzyskać informacje na temat składni, zobacz [składnia wyrażenia OData dla filtrów i klauzul ORDER by](query-odata-filter-orderby-syntax.md).

Istotność jest silnie skojarzona z profilami oceniania. Możesz użyć domyślnej oceny, która opiera się na analizie tekstu i statystyce do klasyfikowania wszystkich wyników, z wyższymi wynikami do dokumentów o większej lub silniejszym dopasowań w wyszukiwanym terminie.

Alternatywne zamówienia sortowania są zwykle skojarzone ze zdarzeniami **onkliknięcia** , które wywołują z powrotem do metody, która kompiluje porządek sortowania. Na przykład, uwzględniając ten element strony:

 ![][4]

Utworzysz metodę, która akceptuje wybraną opcję sortowania jako dane wejściowe i zwraca listę uporządkowaną dla kryteriów skojarzonych z tą opcją.

 ![][5]

> [!NOTE]
> Chociaż domyślne ocenianie jest wystarczające dla wielu scenariuszy, zalecamy oparcie w niestandardowym profilu oceniania. Niestandardowy profil oceniania umożliwia podniesienie poziomu elementów, które są bardziej korzystne dla Twojej firmy. Aby uzyskać więcej informacji, zobacz [Dodawanie profilów oceniania](index-add-scoring-profiles.md) .
>

## <a name="hit-highlighting"></a>Wyróżnianie trafień

Możesz zastosować formatowanie do dopasowywania terminów w wynikach wyszukiwania, co ułatwia dopasowanie do dopasowania. Instrukcje wyróżniania trafień są dostępne w [żądaniu zapytania](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Formatowanie jest stosowane do zapytań w całym okresie. Zapytania dotyczące częściowych warunków, takich jak Wyszukiwanie rozmyte lub wyszukiwanie przy użyciu symboli wieloznacznych, które powodują rozwinięcie zapytania w aparacie, nie mogą używać wyróżniania trafień.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>Nawigacja aspektowa

Nawigacja wyszukiwania jest wspólna na stronie wyników, często znajdującej się na stronie lub w górnej części strony. Na platformie Azure Wyszukiwanie poznawcze Nawigacja aspektowa udostępnia funkcję wyszukiwania na podstawie wstępnie zdefiniowanych filtrów. Aby uzyskać szczegółowe informacje, zobacz [nawigację aspektową w usłudze Azure wyszukiwanie poznawcze](search-faceted-navigation.md) .

## <a name="filters-at-the-page-level"></a>Filtry na poziomie strony

Jeśli projekt rozwiązania zawiera dedykowane strony wyszukiwania dla określonych typów zawartości (na przykład aplikacji detalicznej online zawierającej działy w górnej części strony), można wstawić [wyrażenie filtru](search-filters.md) obok zdarzenia **onkliknięcia** , aby otworzyć stronę w stanie sprzed odfiltrowania.

Filtr można wysłać z wyrażeniem wyszukiwania lub bez niego. Na przykład następujące żądanie przefiltruje nazwę marki, zwracając tylko te dokumenty, które pasują do niego.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Więcej informacji na temat wyrażeń `$filter` można znaleźć w temacie [Wyszukiwanie dokumentów (Azure wyszukiwanie POZNAWCZE API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) .

## <a name="see-also"></a>Zobacz też

- [Interfejs API REST usługi Azure Wyszukiwanie poznawcze](https://docs.microsoft.com/rest/api/searchservice)
- [Operacje indeksowania](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Operacje dokumentu](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Nawigacja aspektowa na platformie Azure Wyszukiwanie poznawcze](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
