---
title: Jak dodać zestawy danych referencyjnych do środowiska — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób dodawania zestawu danych referencyjnych w celu zwiększenia ilości danych w środowisku usługi Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087247"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Tworzenie zestawu danych referencyjnych dla środowiska usługi Time Series Insights przy użyciu portalu Azure

W tym artykule opisano sposób dodawania zestawu danych referencyjnych do środowiska usługi Azure Time Series Insights. Dane referencyjne są przydatne do przyłączenia się do danych źródłowych, aby zwiększyć wartości.

Reference Data Set to zbiór elementów, które rozszerzają zdarzenia ze źródła zdarzeń. Aparat danych przychodzących usługi Time Series Insights łączy każde zdarzenie ze źródła zdarzeń z odpowiednim wierszem danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To sprzężenie jest oparte na kolumnach klucza podstawowego zdefiniowanych w zestawie danych referencyjnych.

Dane referencyjne nie są dołączane z mocą wsteczną. W związku z tym tylko bieżące i przyszłe dane transferu danych przychodzących są dopasowywać i łączyć się z ustawą daty odwołania, po skonfigurowaniu i przekazaniu.

## <a name="video"></a>Film wideo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Dowiedz się więcej o modelu danych referencyjnych usługi Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Dodawanie zestawu danych referencyjnych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Znajdź istniejące środowisko usługi Azure Time Series Insights. Wybierz **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi Time Series Insights.

1. Wybierz stronę **Przegląd.** Rozwiń sekcję **Podstawowe informacje** u góry strony, aby zlokalizować adres **URL Eksploratora Wgląd w statystyki szeregów czasowych** i otworzyć łącze.  

   [![Rozwiń sekcję Podstawowe informacje](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Wyświetl eksploratora dla środowiska usługi Time Series Insights.

1. Rozwiń selektor środowiska w Eksploratorze Usługi Time Series Insights. Wybierz aktywne środowisko. Wybierz ikonę danych referencyjnych w prawym górnym rogu na stronie eksploratora.

   [![Dodawanie danych referencyjnych](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Wybierz przycisk **+ Dodaj zestaw danych,** aby rozpocząć dodawanie nowego zestawu danych.

   [![Dodawanie zestawu danych](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na stronie **Nowy zestaw danych referencyjnych** wybierz format danych:

   - Wybierz **plik CSV** dla danych rozdzielanych przecinkami. Pierwszy wiersz jest traktowany jako wiersz nagłówka.
   - Wybierz **pozycję JSON Array** for JavaScript object notation (JSON) formatowane dane.

   [![Wybierz format danych.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Podaj dane, korzystając z jednej z dwóch metod:

   - Wklej dane do edytora tekstu. Następnie wybierz przycisk **Analizuj dane referencyjne.**
   - Wybierz przycisk **Wybierz plik,** aby dodać dane z lokalnego pliku tekstowego.

   Na przykład wklejenie danych CSV: [ ![Wklejone dane CSV](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Na przykład wklejenie danych tablicy JSON: [ ![wklejenie danych JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Jeśli występuje błąd podczas analizowania wartości danych, błąd pojawia się na czerwono `CSV parsing error, no rows extracted`u dołu strony, na przykład .

1. Po pomyślnym przeanalizowaniu danych zostanie wyświetlona siatka danych wyświetlająca kolumny i wiersze reprezentujące dane. Przejrzyj siatkę danych, aby zapewnić poprawność.

   [![Przeglądanie danych referencyjnych](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Przejrzyj każdą kolumnę, aby zrozumieć typ danych przyjętego i w razie potrzeby zmień typ danych.  Wybierz symbol typu danych w **#** nagłówku kolumny: dla double (dane liczbowe), **T| F** dla wartości logicznej lub **Abc** dla ciągu.

   [![Wybierz typy danych w nagłówkach kolumn.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. W razie potrzeby zmień nazwę nagłówków kolumn. Nazwa kolumny klucza jest konieczne, aby dołączyć do odpowiedniej właściwości w źródle zdarzeń. 

   > [!IMPORTANT]
   > Upewnij się, że nazwy kolumn klucza danych referencyjnych są dokładnie zgodne z nazwą zdarzenia do danych przychodzących, w tym z uwzględnieniem wielkości liter. Nazwy kolumn innych niż klucz są używane do powiększania danych przychodzących o odpowiednie wartości danych referencyjnych.

1. Wpisz wartość w polu **Filtruj wiersze...** aby w razie potrzeby przejrzeć określone wiersze. Filtr jest przydatny do przeglądania danych, ale nie jest stosowany podczas przekazywania danych.

1. Nazwij zestaw danych, wypełniając pole **Nazwa zestawu danych** nad siatką danych.

    [![Nazwij zestaw danych.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Podaj kolumnę **Klucz podstawowy** w zestawie danych, wybierając pozycję rozwijaną nad siatką danych.

    [![Wybierz kolumny kluczy.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Opcjonalnie)** Wybierz **+** przycisk, aby dodać kolumnę klucza pomocniczego jako złożony klucz podstawowy. Jeśli chcesz cofnąć zaznaczenie, wybierz pustą wartość z listy rozwijanej, aby usunąć klucz pomocniczy.

1. Aby przekazać dane, wybierz przycisk **Przekaż wiersze.**

    [![Prześlij wiersze i potwierdź dane.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Strona potwierdza zakończone przekazywanie i wyświetla komunikat **Pomyślnie przesłany zestaw danych**.

    > [!WARNING]
    > Kolumny lub właściwości współużytkowane między zestawami danych referencyjnych będą wyświetlać błąd przekazywania **nazwy właściwości Duplikat.** Błąd nie uniemożliwi pomyślnego przekazania zestawów danych referencyjnych. Można go usunąć, łącząc wiersze współużytkowane zduplikowanej nazwy właściwości.

1. Wybierz **pozycję Dodaj wiersz**, **Zbiorcze wiersze importu**lub **Dodaj kolumnę,** aby dodać więcej wartości danych referencyjnych, zgodnie z potrzebami.

    [![Dodaj wiersz, Zbiorczo importuj wiersze lub Dodaj kolumnę.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Każdy wiersz, który dzieli unikatowy klucz z innym wierszem, będzie miał swoje kolumny zastąpione przez ostatni wiersz dodany, który udostępnia ten unikatowy klucz.

   > [!NOTE]
   > Dodane wiersze **nie** muszą być *prostokątne* — mogą mieć mniej, większe lub różne kolumny z innych wpisów w zestawie danych odwołania.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie danymi referencyjnymi](time-series-insights-manage-reference-data-csharp.md) na drodze programowej.

* Aby uzyskać pełne odwołanie do interfejsu API, przeczytaj dokument [interfejsu API danych referencyjnych.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
