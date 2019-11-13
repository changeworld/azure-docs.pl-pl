---
title: Jak dodać zestaw danych referencyjnych do środowiska — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób dodawania zestawu danych referencyjnych do rozszerzania danych w środowisku Azure Time Series Insightsu.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 31ae271a4ad6aa1370828640884a54eb2669804d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012719"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Utwórz zestaw danych referencyjnych dla środowiska Time Series Insights przy użyciu Azure Portal

W tym artykule opisano sposób dodawania zestawu danych referencyjnych do środowiska Azure Time Series Insightsowego. Dane referencyjne są przydatne do łączenia się z danymi źródłowymi w celu rozszerzenia wartości.

Zestaw danych referencyjnych to kolekcja elementów, które rozszerzają zdarzenia ze źródła zdarzenia. Time Series Insights aparat transferu danych przychodzących łączy każde zdarzenie ze źródła zdarzeń z odpowiednim wierszem danych w zestawie danych referencyjnych. To rozszerzone zdarzenie jest następnie dostępne dla zapytania. To sprzężenie jest oparte na kolumnach kluczy podstawowych zdefiniowanych w zestawie danych referencyjnych.

Dane referencyjne nie są przyłączone wstecz. W takim przypadku tylko bieżące i przyszłe dane przychodzące są dopasowywane i dołączane do zestawu dat odwołania po jego skonfigurowaniu i przekazaniu.

## <a name="video"></a>Połączenia wideo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Dowiedz się więcej o modelu danych referencyjnych usługi Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Dodawanie zestawu danych referencyjnych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejące środowisko Azure Time Series Insights. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie Azure Portal. Wybierz środowisko usługi Time Series Insights.

1. Wybierz stronę **Przegląd** . Znajdź **adres URL eksploratora Time Series Insights** i Otwórz link.  

   Wyświetl Eksploratora dla środowiska Time Series Insightsowego.

1. Rozwiń selektor środowiska w Eksploratorze Time Series Insights. Wybierz aktywne środowisko. Wybierz ikonę dane referencyjne w prawym górnym rogu strony Eksploratora.

   [![Dodaj dane referencyjne](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Wybierz przycisk **+ Dodaj zestaw danych** , aby rozpocząć dodawanie nowego zestawu danych.

   [![dodać zestawu danych](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Na stronie **Nowy zestaw danych referencyjnych** wybierz format danych:

   - Wybierz opcję **CSV** dla danych rozdzielanych przecinkami. Pierwszy wiersz jest traktowany jako wiersz nagłówka.
   - Wybierz **tablicę JSON** dla danych sformatowanych w formacie JavaScript Object Notation (JSON).

   [![wybierz format danych.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Podaj dane przy użyciu jednej z dwóch metod:

   - Wklej dane do edytora tekstu. Następnie wybierz przycisk **Analizuj dane referencyjne** .
   - Wybierz przycisk **Wybierz plik** , aby dodać dane z lokalnego pliku tekstowego.

   Na przykład wklej dane CSV: [![wklejone dane CSV](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Na przykład wklej dane tablicy JSON: [![wkleić dane JSON](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Jeśli wystąpi błąd podczas analizowania wartości danych, błąd pojawia się na czerwono u dołu strony, na przykład `CSV parsing error, no rows extracted`.

1. Po pomyślnym przeanalizowaniu danych zostanie wyświetlona siatka danych wyświetlająca kolumny i wiersze reprezentujące dane.  Przejrzyj siatkę danych, aby upewnić się, że jest to prawidłowa.

   [![Dodaj dane referencyjne](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Przejrzyj każdą kolumnę, aby zobaczyć przyjęty typ danych, a jeśli to konieczne, w razie potrzeby zmień typ danych.  Wybierz symbol typu danych w nagłówku kolumny: **#** dla podwójne (dane liczbowe), **t | F** dla wartości logicznej lub **ABC** dla ciągu.

   [![wybierz typy danych w nagłówkach kolumn.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. W razie konieczności zmień nazwy nagłówków kolumn. Nazwa kolumny klucza jest konieczna do przyłączenia do odpowiedniej właściwości w źródle zdarzenia. Upewnij się, że nazwy kolumn klucza danych referencyjnych są zgodne z nazwą zdarzenia do danych przychodzących, z uwzględnieniem wielkości liter. Nazwy kolumn niebędących kluczami służą do rozszerzania danych przychodzących z odpowiednimi wartościami danych referencyjnych.

1. Wybierz pozycję **Dodaj wiersz** lub **Dodaj kolumnę** , aby dodać więcej wartości danych referencyjnych w razie potrzeby.

1. Wpisz wartość w polu **Filtruj wiersze...** , aby przejrzeć określone wiersze zgodnie z wymaganiami. Filtr jest przydatny do przeglądania danych, ale nie jest stosowany podczas przekazywania danych.

1. Nazwij zestaw danych, wypełniając pole **Nazwa zestawu danych** powyżej siatki danych.

    [![nazwę zestawu danych.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Podaj kolumnę **klucza podstawowego** w zestawie danych, wybierając listę rozwijaną powyżej siatki danych.

    [![wybrać kolumny klucza.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Opcjonalnie wybierz przycisk **+** , aby dodać kolumnę klucza pomocniczego jako złożony klucz podstawowy. Jeśli musisz cofnąć zaznaczenie, wybierz z listy rozwijanej wartość pustą, aby usunąć klucz pomocniczy.

1. Aby przekazać dane, wybierz przycisk **Przekaż wiersze** .

    [Przekazywanie ![](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Strona potwierdza ukończone przekazywanie i wyświetla komunikat **pomyślnie przekazano zestaw danych**.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie danymi referencyjnymi](time-series-insights-manage-reference-data-csharp.md) na drodze programowej.

* Aby uzyskać pełną dokumentację interfejsu API, zobacz dokument [Interfejs API danych referencyjnych](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
