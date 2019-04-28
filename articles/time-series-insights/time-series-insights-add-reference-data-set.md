---
title: Jak dodać zestawu danych referencyjnych do środowiska usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania zestawu danych referencyjnych, aby rozszerzyć dane w danym środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.custom: seodec18
ms.openlocfilehash: 17ba15ea12efc80dcc830f6338a837d1abb77a71
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764447"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Tworzenie zestawu danych referencyjnych dla środowiska usługi Time Series Insights przy użyciu witryny Azure portal

W tym artykule opisano sposób dodawania zestawu danych referencyjnych do środowiska usługi Azure Time Series Insights. Dane referencyjne przydaje się do dołączenia do do danych źródłowych, aby rozszerzyć wartości.

Zestawu danych referencyjnych jest kolekcją elementów, które polepszają zdarzenia ze źródła zdarzenia. Aparat transferu danych przychodzących Series Insights czasu łączy każde zdarzenie ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To połączenie jest oparte na kolumny klucza podstawowego, zdefiniowane w zestawie danych referencyjnych.

Dane referencyjne nie jest przyłączona wstecznie. Oznacza to, że tylko danych przychodzących bieżących i przyszłych jest dopasowane i przyłączone do odwołania zestawu daty, po jego skonfigurowaniu i przekazać.

## <a name="video"></a>Wideo: 

### <a name="in-this-video-we-cover-time-series-insights-reference-data-modelbr"></a>W tym filmie omówione modelu danych usługi Time Series Insights odwołania.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Dodawanie zestawu danych referencyjnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejącego środowiska usługi Time Series Insights. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi Time Series Insights.

3. Wybierz **Przegląd** strony. Znajdź **URL Eksploratora usługi Time Series Insights** i otwórz link.  

   Wyświetl Eksploratora dla danego środowiska usługi TSI.

4. Rozwiń selektor środowiska w Eksploratorze TSI. Wybierz aktywnego środowiska. Wybierz ikonę danych odwołania w prawym górnym rogu, na stronie programu explorer.

   ![Dodaj dane referencyjne](media/add-reference-data-set/add_reference_data.png)

5. Wybierz **+ Dodaj zestaw danych** przycisk, aby rozpocząć dodawanie nowego zestawu danych.

   ![Dodawanie zestawu danych](media/add-reference-data-set/add_data_set.png)

6. Na **nowy zestaw danych referencyjnych** wybierz format danych: 
   - Wybierz **CSV** danych rozdzielonych przecinkami. Pierwszy wiersz jest traktowana jako wiersz nagłówków. 
   - Wybierz **tablicę JSON** dla języka javascript object notation (JSON) sformatowanych danych.

   ![Wybierz format danych.](media/add-reference-data-set/add_data.png)

7. Zawiera dane przy użyciu jednej z dwóch metod:
   - Wklej je do edytora tekstu. Następnie wybierz **analizy danych referencyjnych** przycisku.
   - Wybierz **wybierz plik** przycisk, aby dodać dane z pliku tekstowego lokalnego. 

   Na przykład Wklej dane w formacie CSV: ![Wklejonych danych CSV](media/add-reference-data-set/csv_data_pasted.png)

   Na przykład Wklej dane tablicy JSON: ![Wklej dane JSON](media/add-reference-data-set/json_data_pasted.png)

   Jeśli występuje błąd podczas analizowania wartości danych, ten błąd jest wyświetlana na czerwono w dolnej części strony, takich jak `CSV parsing error, no rows extracted`.

8. Po danych zostanie pomyślnie przetworzona, siatkę danych jest wyświetlany, wyświetlanie kolumnami i wierszami reprezentujący dane.  Przejrzyj siatkę danych, aby sprawdzić ich poprawność.

   ![Dodaj dane referencyjne](media/add-reference-data-set/parse_data.png)

9. Przejrzyj każdej kolumny, aby zobaczyć zakłada, że typ danych, a zmiana typu danych, jeśli to konieczne.  Wybierz symbol typu danych w nagłówku kolumny: **#** dla podwójnej precyzji (dane liczbowe) **T | F** na wartość logiczną, lub **Abc** ciągu.

   ![Wybierz typy danych w nagłówkach kolumn.](media/add-reference-data-set/choose_datatypes.png)

10. Jeśli to konieczne, należy zmienić nazwy nagłówków kolumn. Nazwa kolumny klucza jest niezbędne do przyłączenia do odpowiedniej właściwości w źródle zdarzenia. Upewnij się, że nazwy kolumny klucza danych odwołania pasują dokładnie do nazwy zdarzenia przychodzące dane, w tym uwzględnianie wielkości liter. Nazwy kolumny niebędącej kluczem są używane rozszerzyć dane przychodzące z odpowiednimi wartościami danych odwołania.

11. Kliknij przycisk **Dodaj wiersz** lub **Dodaj kolumnę** można dodać więcej wartości danych referencyjnych, zgodnie z potrzebami.

12. Wpisz wartość w **filtrować wiersze...**  pola, aby przejrzeć określonych wierszy, zgodnie z potrzebami. Filtr jest przydatne podczas przeglądania danych, ale nie została zastosowana podczas przekazywania danych.
 
13. Nazwa zestawu danych, wypełniając **Nazwa zestawu danych** pole powyżej siatki danych.

    ![Nazwa zestawu danych.](media/add-reference-data-set/name_reference_dataset.png)

14. Podaj **klucz podstawowy** kolumn w zestawie danych, wybierając przycisk listy rozwijanej powyżej siatki danych.

    ![Wybierz kolumny klucza.](media/add-reference-data-set/set_primary_key.png)

    Opcjonalnie można zaznaczyć **+** przycisk, aby dodać kolumnę klucza pomocniczego jako złożony klucz podstawowy. Jeśli potrzebujesz cofnąć zaznaczenie, wybierz pustą wartość z listy rozwijanej można usunąć klucza pomocniczego.

15. Aby przekazać dane, wybierz **przekazywania wierszy** przycisku.

    ![Upload](media/add-reference-data-set/upload_rows.png)

    Strona potwierdza ukończoną przekazywanie i wyświetli komunikat **pomyślnie przekazano zestaw danych**.

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie danymi referencyjnymi](time-series-insights-manage-reference-data-csharp.md) na drodze programowej.
* Aby uzyskać pełną dokumentację interfejsu API, zobacz dokument [Interfejs API danych referencyjnych](/rest/api/time-series-insights/ga-reference-data-api).
