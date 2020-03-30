---
title: Narzędzie do wyszukiwania explorer w witrynie Azure portal
titleSuffix: Azure Cognitive Search
description: W tym przewodniku Szybki start portalu Azure użyj Eksploratora wyszukiwania, aby nauczyć się składni kwerendy, przetestować wyrażenia zapytania lub sprawdzić dokument wyszukiwania. Eksplorator wyszukiwania kwerend indeksy w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369654"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Szybki start: uruchamianie zapytań w portalu za pomocą Eksploratora wyszukiwania

**Eksplorator wyszukiwania** to wbudowane narzędzie do wyszukiwania używane do uruchamiania zapytań względem indeksu wyszukiwania w usłudze Azure Cognitive Search. To narzędzie ułatwia poznawanie składni kwerendy, testowanie kwerendy lub wyrażenia filtru lub potwierdzanie wyników odświeżania indeksu przez sprawdzenie, czy istnieje nowsza zawartość.

Ten szybki start używa **indeksu przykładowego realestate-us** do wykazania Eksploratora wyszukiwania. Żądania są formułowane przy użyciu [interfejsu API REST wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/)z odpowiedziami zwracanych jako dokumenty JSON.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz skorzystać z bezpłatnej usługi dla tego szybkiego startu.

+ **realestate-us-sample-index** jest używany do tego szybkiego startu. Krok za pomocą [**Kreatora importu danych,**](search-import-data-portal.md) aby wygenerować indeks z wbudowanego źródła danych próbek.

## <a name="start-search-explorer"></a>Rozpocznij eksploratora wyszukiwania

1. W [witrynie Azure portal](https://portal.azure.com)otwórz stronę usługi wyszukiwania z pulpitu nawigacyjnego lub [znajdź usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Otwórz Eksploratora wyszukiwania na pasku poleceń:

   ![Polecenie Eksplorator wyszukiwania w portalu](./media/search-explorer/search-explorer-cmd2.png "Polecenie Eksplorator wyszukiwania w portalu")

    Możesz też użyć osadzonej karty **Eksploratora wyszukiwania** w otwartym indeksie:

   ![Karta Eksploratora wyszukiwania](./media/search-explorer/search-explorer-tab.png "Karta Eksploratora wyszukiwania")

## <a name="unspecified-query"></a>Nieokreślona kwerenda

Aby uzyskać pierwsze spojrzenie na zawartość, wykonaj puste wyszukiwanie, klikając **pozycję Wyszukaj** bez podanych terminów. Puste wyszukiwanie jest przydatne jako pierwsze zapytanie, ponieważ zwraca całe dokumenty, dzięki czemu można przeglądać kompozycję dokumentu. W przypadku wyszukiwania pustego nie ma rangi wyszukiwania,`"@search.score": 1` a dokumenty są zwracane w dowolnej kolejności (dla wszystkich dokumentów). Domyślnie 50 dokumentów jest zwracanych w żądaniu wyszukiwania.

Równoważną składnią pustego `*` `search=*`wyszukiwania jest lub .
   
   ```http
   search=*
   ```

   **Results**
   
   ![Przykład pustej kwerendy](./media/search-explorer/search-explorer-example-empty.png "Przykład zapytania bez kwalifikacji lub pusty")

## <a name="free-text-search"></a>Wyszukiwanie tekstu swobodnego

Zapytania w postaci swobodnej, z operatorami lub bez, są przydatne do symulowania zapytań zdefiniowanych przez użytkownika wysyłanych z aplikacji niestandardowej do usługi Azure Cognitive Search. Tylko te pola przypisane jako **przeszukiwalne** w definicji indeksu są skanowane w poszukiwaniu dopasowań. 

Należy zauważyć, że po podaniu kryteriów wyszukiwania, takich jak terminy kwerendy lub wyrażenia, pozycja wyszukiwania wchodzi w grę. Poniższy przykład ilustruje wyszukiwanie tekstu wolnego.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Za pomocą klawisza Ctrl-F można wyszukiwać w wynikach określone warunki zainteresowania.

   ![Przykład zapytania tekstowego](./media/search-explorer/search-explorer-example-freetext.png "Przykład zapytania tekstowego")

## <a name="count-of-matching-documents"></a>Liczba pasujących dokumentów 

Dodaj **$count=true,** aby uzyskać liczbę dopasowań znalezionych w indeksie. W pustym wyszukiwaniu liczba jest całkowitą liczbą dokumentów w indeksie. W wyszukiwaniu kwalifikowanym jest to liczba dokumentów pasujących do danych wejściowych kwerendy.

   ```http
   $count=true
   ```

   **Results**

   ![Przykład liczby dokumentów](./media/search-explorer/search-explorer-example-count.png "Liczba pasujących dokumentów w indeksie")

## <a name="limit-fields-in-search-results"></a>Ograniczanie pól w wynikach wyszukiwania

Dodaj [**$select,**](search-query-odata-select.md) aby ograniczyć wyniki do jawnie nazwanych pól, aby uzyskać bardziej czytelne dane wyjściowe w **Eksploratorze wyszukiwania**. Aby zachować ciąg wyszukiwania i **$count =true**, **&** argumenty prefiksu z . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Przykład pola ogranicz](./media/search-explorer/search-explorer-example-selectfield.png "Ograniczanie pól w wynikach wyszukiwania")

## <a name="return-next-batch-of-results"></a>Zwraca następną partię wyników

Usługa Azure Cognitive Search zwraca 50 najlepszych dopasowań na podstawie rangi wyszukiwania. Aby uzyskać następny zestaw pasujących dokumentów, dołącz **$top=100,&$skip=50,** aby zwiększyć wynik ustawiony na 100 dokumentów (wartość domyślna to 50, maksymalna to 1000), pomijając pierwsze 50 dokumentów. Przypomnijmy, że aby uzyskać wyniki rankingowe, należy podać kryteria wyszukiwania, takie jak termin zapytania lub wyrażenie. Zwróć uwagę, że wyniki wyszukiwania zmniejszają się im głębiej, tym do wyników wyszukiwania.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Wyniki wyszukiwania wsadowego](./media/search-explorer/search-explorer-example-topskip.png "Zwracanie następnej partii wyników wyszukiwania")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Wyrażenia filtru (większe niż, mniejsze niż, równe)

Parametr [**$filter**](search-query-odata-filter.md) służy do określania dokładnych kryteriów, a nie wyszukiwania w postaci tekstu swobodnego. Pole musi być przypisane jako **filtrowane** w indeksie. W tym przykładzie wyszukuje sypialnie większe niż 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Wyrażenie filtru](./media/search-explorer/search-explorer-example-filter.png "Filtrowanie według kryteriów")

## <a name="order-by-expressions"></a>Wyrażenia typu "kolejność według"

Dodaj [**$orderby,**](search-query-odata-orderby.md) aby posortować wyniki według innego pola oprócz wyniku wyszukiwania. Pole musi być przypisane jako **sortowalne** w indeksie. Przykładowe wyrażenie, którego można użyć do przetestowania tego jest:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![Wyrażenie Orderby](./media/search-explorer/search-explorer-example-ordery.png "Zmiana kolejności sortowania")

Zarówno **wyrażenia $filter,** jak i **$orderby** są konstrukcjami OData. Aby uzyskać więcej informacji, zobacz [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Składnia filtrowania OData).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Wnioski

W tym przewodniku Szybki start użyto **Eksploratora wyszukiwania** do wykonywania kwerend do kwerendy indeksu przy użyciu interfejsu API REST.

+ Wyniki są zwracane jako pełne dokumenty JSON, dzięki czemu można wyświetlić konstrukcję dokumentu i zawartość w całości. Można użyć wyrażeń kwerendy, pokazane w przykładach, aby ograniczyć, które pola są zwracane.

+ Dokumenty składają się ze wszystkich pól oznaczonych jako **możliwe do pobrania** w indeksie. Aby wyświetlić atrybuty indeksu w portalu, kliknij *przykład nieruchomości-us* na liście **Indeksy** na stronie przegląd wyszukiwania.

+ Zapytania o postaci swobodne, podobne do tych, które można wprowadzić w komercyjnej przeglądarce internetowej, są przydatne do testowania środowiska użytkownika końcowego. Na przykład przy założeniu wbudowanego indeksu przykładowego stanu nieruchomości można wprowadzić "Seattle apartments lake washington", a następnie można użyć ctrl-F, aby znaleźć terminy w wynikach wyszukiwania. 

+ Wyrażenia zapytań i filtrów są artykułowane w składni obsługiwanej przez usługę Azure Cognitive Search. Domyślna jest [prosta składnia](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale opcjonalnie można użyć [pełnego Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) dla bardziej zaawansowanych zapytań. [Wyrażenia filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) są składnią OData.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli pracujesz w ramach własnej subskrypcji, dobrym pomysłem po zakończeniu projektu jest sprawdzenie, czy dalej potrzebujesz utworzonych zasobów. Nadal uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć i zarządzać nimi w portalu, korzystając z łącza **Wszystkie zasoby** lub **Grupy zasobów** w lewym okienku nawigacji.

Jeśli korzystasz z bezpłatnej usługi, należy pamiętać, że są ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby pozostać poniżej limitu. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o strukturach zapytań i składni, użyj postmana lub równoważnego narzędzia do tworzenia wyrażeń zapytań, które wykorzystują więcej części interfejsu API. [Interfejs API rest wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) jest szczególnie przydatne do nauki i eksploracji.

> [!div class="nextstepaction"]
> [Tworzenie kwerendy podstawowej w programie Postman](search-query-simple-examples.md)