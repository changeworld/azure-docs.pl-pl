---
title: Narzędzie Eksplorator wyszukiwania do wykonywania zapytań dotyczących danych w Azure Portal-Azure Search
description: Użyj narzędzi Azure Portal, takich jak Eksplorator wyszukiwania, aby wykonywać zapytania dotyczące indeksów w Azure Search. Wprowadź wyszukiwane terminy lub w pełni kwalifikowane ciągi wyszukiwania, używając składni zaawansowanej.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 87e5ec82299ef9ddc8bc8756196bb2ace3d1f6f3
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414241"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Eksplorator wyszukiwania do wykonywania zapytań dotyczących danych w Azure Search 

W tym artykule pokazano, jak zbadać istniejący indeks Azure Search przy użyciu **Eksploratora wyszukiwania** w Azure Portal. Możesz użyć Eksploratora wyszukiwania do przesyłania prostych lub pełnych ciągów zapytań Lucene do dowolnego istniejącego indeksu w usłudze. 

   ![Polecenie Search Explorer w portalu](./media/search-explorer/search-explorer-cmd2.png "Polecenie Search Explorer w portalu")


Aby uzyskać pomoc dotyczącą rozpoczynania pracy, zobacz [Uruchamianie Eksploratora wyszukiwania](#start-search-explorer).

## <a name="basic-search-strings"></a>Podstawowe ciągi wyszukiwania

W poniższych przykładach założono wbudowany indeks przykładowej Realestate. Aby uzyskać pomoc dotyczącą tworzenia tego [indeksu, zobacz Szybki Start: Importowanie, indeksowanie i zapytanie w Azure Portal](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Przykład 1 — puste wyszukiwanie

Aby najpierw przyjrzeć się zawartości, wykonaj puste wyszukiwanie, klikając przycisk **Wyszukaj** bez podanych warunków. Puste wyszukiwanie jest przydatne jako pierwsze zapytanie, ponieważ zwraca całe dokumenty, aby można było sprawdzić kompozycję dokumentu. W przypadku pustego wyszukiwania nie ma rangi wyszukiwania i dokumenty są zwracane w dowolnej kolejności (`"@search.score": 1` dla wszystkich dokumentów). Domyślnie w żądaniu wyszukiwania są zwracane dokumenty 50.

Odpowiednikiem składni pustego wyszukiwania jest `*` lub `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Przykład pustego zapytania](./media/search-explorer/search-explorer-example-empty.png "Przykład niekwalifikowanego lub pustego zapytania")

### <a name="example-2---free-text-search"></a>Przykład 2 — Wyszukiwanie swobodne tekstu

Zapytania o dowolnej postaci, z operatorami lub bez, są przydatne do symulowania zapytań zdefiniowanych przez użytkownika, które są wysyłane z niestandardowej aplikacji do Azure Search. Zwróć uwagę, że w przypadku podania terminów lub wyrażeń zapytania, ranga wyszukiwania jest dostępna. Poniższy przykład ilustruje wyszukiwanie swobodne tekstu.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Możesz użyć kombinacji Ctrl-F, aby przeszukać wyniki dla określonych warunków zainteresowania.

   ![Przykład zapytania Free Text](./media/search-explorer/search-explorer-example-freetext.png "Przykład zapytania Free Text")

### <a name="example-3---count-of-matching-documents"></a>Przykład 3 — Liczba pasujących dokumentów 

Dodaj **$Count** , aby uzyskać liczbę dopasowań znalezionych w indeksie. W przypadku pustego wyszukiwania liczba to łączna liczba dokumentów w indeksie. W przypadku kwalifikowanego wyszukiwania jest to liczba dokumentów pasujących do danych wejściowych zapytania.

   ```Input1
   $count=true
   ```
   **Results**

   ![Przykład liczby dokumentów](./media/search-explorer/search-explorer-example-count.png "Liczba pasujących dokumentów w indeksie")

### <a name="example-4---restrict-fields-in-search-results"></a>Przykład 4 — ograniczanie pól w wynikach wyszukiwania

Dodaj **$SELECT** , aby ograniczyć wyniki do jawnie nazwanych pól, aby uzyskać bardziej czytelny wynik w **Eksploratorze wyszukiwania**. Aby zachować ciąg wyszukiwania i **$Count = true**, prefiks argumentów z **&** . 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Przykład pola ograniczenia](./media/search-explorer/search-explorer-example-selectfield.png "Ograniczanie pól w wynikach wyszukiwania")

### <a name="example-5---return-next-batch-of-results"></a>Przykład 5 — zwracanie następnej partii wyników

Azure Search zwraca górne dopasowania 50 w oparciu o rangę wyszukiwania. Aby uzyskać następny zestaw pasujących dokumentów, Dołącz **$Top = 100, & $Skip = 50** , aby zwiększyć wynik z 100 dokumentów (wartość domyślna to 50, maksimum to 1000), pomijając pierwsze dokumenty 50. Odwołaj, że musisz podać kryteria wyszukiwania, takie jak termin lub wyrażenie zapytania, aby uzyskać rankingowe wyniki. Zwróć uwagę, że wyniki wyszukiwania zmniejszają głębię osiągniętą w wynikach wyszukiwania.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Wyniki wyszukiwania wsadowego](./media/search-explorer/search-explorer-example-topskip.png "Zwróć następną partię wyników wyszukiwania")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Wyrażenia filtru (większe niż, mniejsze niż, równe)

Użyj parametru **$Filter** , aby określić dokładne kryteria zamiast wyszukiwania tekstu wolnego. Ten przykład wyszukuje sypialniami większe niż 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Wyrażenie filtru](./media/search-explorer/search-explorer-example-filter.png "Filtruj według kryteriów")

## <a name="order-by-expressions"></a>Wyrażenia order-by

Dodaj **$OrderBy** , aby sortować wyniki według innego pola niż wynik wyszukiwania. Przykładowe wyrażenie, którego można użyć do przetestowania:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![Wyrażenie OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Zmiana kolejności sortowania")

Wyrażenia **$Filter** i **$OrderBy** są konstrukcjami OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Jak uruchomić Eksploratora wyszukiwania

1. W [Azure Portal](https://portal.azure.com)Otwórz stronę usługi wyszukiwania na pulpicie nawigacyjnym lub [Znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na liście usług.

2. Na stronie Przegląd usługi kliknij pozycję **Eksplorator wyszukiwania**.

   ![Polecenie Search Explorer w portalu](./media/search-explorer/search-explorer-cmd2.png "Polecenie Search Explorer w portalu")

3. Wybierz indeks do zapytania.

   ![Wybierz indeks do zapytania](./media/search-explorer/search-explorer-changeindex-se2.png "Wybierz indeks")

4. Opcjonalnie Ustaw wersję interfejsu API. Domyślnie wybrana jest bieżąca ogólnie dostępna wersja interfejsu API, ale można wybrać Podgląd lub starszy interfejs API, Jeśli składnia, która ma być używana, jest specyficzna dla wersji.

5. Po wybraniu indeksu i wersji interfejsu API wprowadź terminy wyszukiwania lub w pełni kwalifikowane wyrażenia zapytania na pasku wyszukiwania, a następnie kliknij przycisk **Wyszukaj** , aby wykonać.

   ![Wprowadź terminy wyszukiwania i kliknij pozycję Wyszukaj](./media/search-explorer/search-explorer-query-string-example.png "Wprowadź terminy wyszukiwania i kliknij pozycję Wyszukaj")

Porady dotyczące wyszukiwania w **Eksploratorze wyszukiwania**:

+ Wyniki są zwracane jako pełne dokumenty JSON, dzięki czemu można w całości przeglądać Konstruowanie i zawartość dokumentu. Można użyć wyrażeń zapytania, pokazanych w przykładach, aby ograniczyć liczbę zwracanych pól.

+ Dokumenty składają się ze wszystkich pól oznaczonych jako możliwy do **pobierania** w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij pozycję *realestate-US — przykład* na liście **indeksy** na stronie Przegląd wyszukiwania.

+ Zapytania o dowolnej postaci, podobne do tego, co można wprowadzać w komercyjnej przeglądarce internetowej, są przydatne do testowania środowiska użytkownika końcowego. Na przykład przy założeniu wbudowanego przykładowego indeksu realestate można wprowadzić wartość "Seattle apartamentach jeziora Waszyngton", a następnie użyć kombinacji klawiszy Ctrl-F, aby znaleźć warunki w wynikach wyszukiwania. 

+ Wyrażenia zapytania i filtru muszą być łączone w składni obsługiwane przez Azure Search. Wartość domyślna to [prosta składnia](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale opcjonalnie można użyć pełnych [Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) dla bardziej zaawansowanych zapytań. [Wyrażenia filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) są składnią OData.


## <a name="next-steps"></a>Kolejne kroki

W następujących zasobach udostępniono dodatkowe informacje o składni oraz przykłady.

 + [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Przykładowe zapytania Lucene](search-query-lucene-examples.md) 
 + [Składnia wyrażeń filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
