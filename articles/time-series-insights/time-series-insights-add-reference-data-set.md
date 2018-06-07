---
title: Jak dodać odwołanie do zestawu danych do środowiska Azure czas serii Insights
description: W tym artykule opisano sposób dodawania zestawem danych odwołania do danych w środowisku Azure czas serii Insights rozszerzyć.
ms.service: time-series-insights
services: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 7da2393bb5114de20747581e366a8f416c9ff9a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653641"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Tworzenie zestawu danych odwołania dla danego środowiska Insights serii czasu przy użyciu portalu Azure

W tym artykule opisano sposób dodawania zestawem danych odwołania do środowiska Azure czas serii Insights. Dane referencyjne przydaje się do dołączenia do danych źródłowych w celu dostosowania wartości.

Odwołanie do zestawu danych to kolekcja elementów, które rozszerzyć zdarzenia ze źródła zdarzeń. Czas Insights serii wejściowych aparat łączy każdego zdarzenia ze źródła zdarzeń z odpowiednich wiersza danych w zestawie danych odwołania. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. Tego sprzężenia jest oparta na kolumny klucza podstawowego zdefiniowany w zestawie danych odwołania.

Dane referencyjne nie jest przyłączona Wstecz. Oznacza to, że tylko danych wejściowych aktualnych i przyszłych jest zgodny i dołączony do zestawu odwołania data po jej skonfigurowaniu i przekazać.

## <a name="add-a-reference-data-set"></a>Dodaj odwołanie do zestawu danych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejące środowisko Insights serii czasu. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie portalu Azure. Wybierz środowisko usługi Time Series Insights.

3. Wybierz **omówienie** strony. Zlokalizuj **adres URL Eksploratora czasu serii Insights** i otworzyć łącze.  

   Wyświetl Eksploratora dla danego środowiska TSI.

4. Rozwiń selektora środowiska w Eksploratorze TSI. Wybierz aktywnego środowiska. Wybierz ikonę danych odwołania w prawym górnym rogu strony Eksploratora.

   ![Dodaj odwołanie do danych](media/add-reference-data-set/add_reference_data.png)

5. Wybierz **+ Dodaj zestaw danych** przycisk, aby rozpocząć dodawanie nowego zestawu danych.

   ![Dodaj zestaw danych](media/add-reference-data-set/add_data_set.png)

6. Na **nowego odwołania zestawu danych** wybierz format danych: 
   - Wybierz **CSV** danych rozdzielonych przecinkami. Pierwszy wiersz jest traktowany jako wiersz nagłówków. 
   - Wybierz **tablicy JSON** JavaScript object notation (JSON) sformatowany danych.

   ![Wybierz format danych.](media/add-reference-data-set/add_data.png)

7. Zawiera dane przy użyciu jednej z dwóch metod:
   - Wkleić do edytora tekstu. Następnie wybierz opcję **analizy danych referencyjnych** przycisku.
   - Wybierz **wybierz plik** przycisk, aby dodać dane z pliku tekstowego lokalnego. 

   Na przykład Wklej dane w formacie CSV: ![CSV wkleić danych](media/add-reference-data-set/csv_data_pasted.png)

   Na przykład Wklej dane JSON tablicy: ![dane JSON Wklej](media/add-reference-data-set/json_data_pasted.png)

   W przypadku wystąpił błąd podczas analizowania wartości danych, ten błąd pojawia się na czerwono w dolnej części strony, takich jak `CSV parsing error, no rows extracted`.

8. Po pomyślnie analizowania danych, jest wyświetlana siatka danych, wyświetlania kolumn i wierszy reprezentujący dane.  Przejrzyj Siatka danych w celu zapewnienia poprawności.

   ![Dodaj odwołanie do danych](media/add-reference-data-set/parse_data.png)

9. Przejrzyj każdej kolumny, aby wyświetlić założono typ danych i zmienić typ danych w razie potrzeby.  Wybierz symbol typu danych w nagłówku kolumny: **#** dla typu double (dane liczbowe), **T | F** dla typu boolean, lub **Abc** ciągu.

   ![Wybierz typy danych w nagłówkach kolumn.](media/add-reference-data-set/choose_datatypes.png)

10. W razie potrzeby zmień nazwę nagłówki kolumn. Nazwa kolumny klucza jest niezbędne do przyłączenia do odpowiadających im właściwości w źródle zdarzeń. Upewnij się, czy nazwy kolumny klucza danych odwołania pasują dokładnie do nazwy zdarzenia do przychodzących danych, łącznie z rozróżnianiem wielkości liter. Nazwy kolumn klucza są używane do rozszerzyć danych przychodzących z odpowiednimi wartościami danych odwołania.

11. Kliknij przycisk **Dodaj wiersz** lub **Dodaj kolumnę** można dodać więcej wartości danych odwołania, zgodnie z potrzebami.

12. Wpisz wartość w **filtrować wiersze...**  pola, aby przejrzeć określonych wierszy, zgodnie z potrzebami. Filtr jest przydatne w przypadku przeglądania danych, ale nie została zastosowana podczas przekazywania danych.
 
13. Nazwa zestawu danych, wypełniając **Nazwa zestawu danych** pole powyżej siatki danych.

   ![Nazwa zestawu danych.](media/add-reference-data-set/name_reference_dataset.png)

14. Podaj **klucz podstawowy** kolumn w zestawie danych, wybierając z listy rozwijanej powyżej siatki danych.

   ![Wybierz kolumny klucza.](media/add-reference-data-set/set_primary_key.png)

   Opcjonalnie wybierz **+** przycisk, aby dodać dodatkowej kolumny klucza złożonego klucza podstawowego. Aby cofnąć zaznaczenie należy wybrać pustą wartość z listy rozwijanej można usunąć klucza pomocniczego.

15.  Aby przekazać dane, wybierz **przekazać wierszy** przycisku.

   ![Upload](media/add-reference-data-set/upload_rows.png)

   Strona potwierdza ukończonej przekazywanie i wyświetlania komunikatu o **pomyślnie przekazano dataset**.

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie danymi referencyjnymi](time-series-insights-manage-reference-data-csharp.md) na drodze programowej.
* Aby uzyskać pełną dokumentację interfejsu API, zobacz dokument [Interfejs API danych referencyjnych](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
