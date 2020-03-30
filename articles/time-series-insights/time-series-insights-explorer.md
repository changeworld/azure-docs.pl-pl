---
title: Eksploruj dane przy użyciu eksploratora — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać Eksploratora usługi Azure Time Series Insights do wyświetlania danych IoT.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046179"
---
# <a name="azure-time-series-insights-explorer"></a>Eksplorator usługi Azure Time Series Insights

W tym artykule opisano funkcje i opcje w ogólnej dostępności [dla aplikacji internetowej eksploratora](https://insights.timeseries.azure.com/)usługi Azure Time Series Insights . Eksplorator usługi Time Series Insights demonstruje zaawansowane funkcje wizualizacji danych udostępniane przez usługę i są dostępne w obrębie własnego środowiska.

Azure Time Series Insights to w pełni zarządzana usługa związana z analizą, przechowywaniem i wizualizacją, dzięki której z łatwością można badać i analizować miliardy zdarzeń IoT jednocześnie. Zapewnia globalny widok danych, co pozwala szybko zweryfikować rozwiązanie IoT i uniknąć kosztownych przestojów na urządzeniach o znaczeniu krytycznym. Możesz odkrywać ukryte trendy, wykrywać anomalie i przeprowadzać analizy przyczyn źródłowych w czasie zbliżonym do rzeczywistego. Eksplorator usługi Time Series Insights jest obecnie w publicznej wersji zapoznawczej.

> [!TIP]
> Aby zapoznać się z przewodnikiem po środowisku demonstracyjnym, przeczytaj [przewodnik szybki start usługi Azure Time Series Insights](time-series-quickstart.md).

## <a name="video"></a>Film wideo

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Dowiedz się więcej o kwerendach danych przy użyciu Eksploratora Usługi Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Odtwórz poprzedni film <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Wprowadzenie do usługi Time Series Insights przy użyciu akceleratora rozwiązań Usługi Azure IoT".</a>

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z Eksploratora usługi Time Series Insights, należy:

- Tworzenie środowiska usługi Time Series Insights. Aby uzyskać więcej informacji, zobacz [Jak rozpocząć pracę z usługi Time Series Insights](./time-series-insights-get-started.md).
- [Zapewnij dostęp](time-series-insights-data-access.md) do swojego konta w środowisku.
- Dodaj do niego [centrum IoT](time-series-insights-how-to-add-an-event-source-iothub.md) lub źródło zdarzeń [centrum zdarzeń.](time-series-insights-how-to-add-an-event-source-eventhub.md)

## <a name="explore-and-query-data"></a>Eksplorowanie i wyszukiwanie danych

W ciągu kilku minut od połączenia źródła zdarzeń ze środowiskiem usługi Time Series Insights można eksplorować i wysyłać zapytania do danych szeregów czasowych.

1. Aby rozpocząć, otwórz [Eksploratora Usługi Time Series Insights](https://insights.timeseries.azure.com/) w przeglądarce internetowej. Po lewej stronie okna wybierz środowisko. Wszystkie środowiska, do których masz dostęp, są wyświetlane w kolejności alfabetycznej.

1. Po wybraniu środowiska użyj konfiguracji **Od** i **Do** u góry lub wybierz i przeciągnij przez odpowiedni czas. Wybierz lupę w prawym górnym rogu lub kliknij prawym przyciskiem myszy wybrany czas i wybierz **polecenie Wyszukaj**.

1. Dostępność można również odświeżać automatycznie co minutę, wybierając przycisk **Auto On.** Przycisk **Auto On** dotyczy tylko wykresu dostępności, a nie zawartości wizualizacji głównej.

1. Ikona chmury platformy Azure przeniesie Cię do środowiska w witrynie Azure portal.

   [![Wybór środowiska usługi Time Series Insights](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Następnie zostanie wyświetlony wykres przedstawiający liczbę wszystkich zdarzeń w wybranym czasie. Tutaj masz kilka formantów:

    - **Edytor terminów panel:** Termin spacja jest, gdzie kwerendy środowiska. Znajduje się po lewej stronie ekranu:
      - **MIARA:** Ta lista rozwijana zawiera wszystkie kolumny liczbowe (**Podwaja**).
      - **SPLIT BY**: Ta lista rozwijana zawiera kolumny kategorii (**Ciągi**).
      - Można włączyć interpolację kroków, pokazać minimalną i maksymalną oraz wyregulować oś y z panelu sterowania obok **pozycji MEASURE**. Można również dostosować, czy wyświetlane dane są liczbą, średnią czy sumą danych.
      - Można dodać maksymalnie pięć terminów do wyświetlenia na tej samej osi x. Wybierz **dodaj,** aby dodać nowy termin lub użyj przycisku **Klonuj ten termin,** aby dodać kopię istniejącego terminu.

        [![Wybór terminów, filtrowanie i panel zapytań](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predykat:** Predykat umożliwia szybkie filtrowanie zdarzeń przy użyciu zestawu operandów wymienionych w poniższej tabeli. Jeśli przeprowadzasz wyszukiwanie, zaznaczając lub klikając, predykat automatycznie aktualizuje się na podstawie tego wyszukiwania. Obsługiwane typy operandów obejmują:

         |Operacja  |Obsługiwane typy  |Uwagi  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Podwójne**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **Ciąg**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**Cala**     | **Ciąg**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Wszystkie argumenty powinny być tego samego typu lub być stałą **NULL.**        |
         |**Hsa**     | **Ciąg**        |  Po prawej stronie dozwolone są tylko stałe literały ciągów. Pusty ciąg i **wartość NULL** nie są dozwolone.       |

      - **Przykładowe zapytania**

         [![Przykładowe kwerendy GA](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. Za pomocą narzędzia Suwak **Rozmiar interwału** można powiększać i pomniejszać interwały w tym samym przedziale czasu. Suwak zapewnia bardziej precyzyjną kontrolę ruchu między dużymi wycinkami czasu, które pokazują płynne trendy w dół do plasterków tak małych jak milisekunda, które umożliwiają wyświetlanie i analizowanie szczegółowych cięć danych o wysokiej rozdzielczości. Domyślny punkt początkowy suwaka jest ustawiony jako najbardziej optymalny widok danych z wyboru, aby zrównoważyć rozdzielczość, szybkość zapytania i szczegółowość.

1. Narzędzie **Pędzel czasu** ułatwia nawigację z jednej rozpiętości czasu do drugiej.

1. Wybierz ikonę **Zapisz,** aby zapisać bieżącą kwerendę i udostępnić ją innym użytkownikom środowiska. Po wybraniu ikony **Otwórz** można przejrzeć wszystkie zapisane zapytania i wszystkie zapytania udostępnione innych użytkowników w środowiskach, do których masz dostęp.

   [![Kwerendy](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Wizualizowanie danych

1. Narzędzie **Widok perspektywy** służy do jednoczesnego wyświetlania maksymalnie czterech unikatowych zapytań. Przycisk **Widok perspektywy** znajduje się w prawym górnym rogu wykresu.

   [![Wybieranie kwerend do dodania do okienka perspektywy](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Wyświetlanie wykresu w celu wizualnego eksplorowania danych i korzystanie z narzędzi **Wykres:**

    - **Zaznacz** lub **kliknij** określony czas lub pojedynczą serię danych.
    - W ramach wyboru czasu można powiększać lub eksplorować wydarzenia.
    - W serii danych można podzielić serię przez inną kolumnę, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, pingować tę serię lub eksplorować zdarzenia z wybranej serii.
    - W obszarze filtru po lewej stronie wykresu można przejrzeć wszystkie wyświetlane serie danych i zamiesorem według wartości lub nazwy. Można również wyświetlić wszystkie serie danych lub dowolną przypiętą lub nieprzypiętą serię. Można wybrać pojedynczą serię danych i podzielić serię przez inną kolumnę, dodać serię jako nowy termin, wyświetlić tylko wybraną serię, wykluczyć wybraną serię, przypiąć tę serię lub eksplorować zdarzenia z wybranej serii.
    - Podczas wyświetlania wielu terminów jednocześnie można układać, odłączać, przeglądać dodatkowe dane dotyczące serii danych i używać tej samej osi y we wszystkich terminach. Użyj przycisków w prawym górnym rogu wykresu.

    [![Ustawienia opcji narzędzia Wykres w prawym górnym rogu](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Użyj **mapy cieplnej,** aby szybko wykryć unikatową lub nietypową serię danych w danym zapytaniu. Tylko jeden wyszukiwany termin można wizualizować jako mapę cieplną.

    [![Wykresy map cieplnych eksploratora GA](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Podczas eksplorowania zdarzeń, zaznaczając lub klikając prawym przyciskiem myszy, panel **EVENTS** jest udostępniany. Tutaj możesz przejrzeć wszystkie surowe zdarzenia i wyeksportować wydarzenia jako pliki JSON lub CSV. Usługa Time Series Insights przechowuje wszystkie nieprzetworzone dane.

    [![Zdarzenia](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Wybierz kartę **STATYSTYKI** po eksplorowanie zdarzeń, aby udostępnić wzorce i statystyki kolumn.

    - **Wzorce:** Ta funkcja proaktywnie powierzchni najbardziej statystycznie istotne wzorce w wybranym regionie danych. Nie musisz patrzeć na tysiące wydarzeń, aby zrozumieć, jakie wzorce wymagają najwięcej czasu i energii. Dzięki usłudze Time Series Insights można przejść bezpośrednio do tych statystycznie istotnych wzorców, aby kontynuować analizę. Ta funkcja jest również przydatna w przypadku badań pośmiertnych danych historycznych.
    - **Statystyki kolumn:** Statystyki kolumn zawierają wykresy i tabele, które rozkładają dane z każdej kolumny wybranej serii danych w wybranym czasie.

      [![Wykresy kolumnowe i opcje statystyk](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Teraz dowiesz się o kluczowych funkcjach, ustawieniach konfiguracji i opcjach wyświetlania dostępnych w aplikacji internetowej Eksploratora usługi Time Series Insights.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [diagnozować i rozwiązywać problemy](time-series-insights-diagnose-and-solve-problems.md) w środowisku usługi Time Series Insights.

- Zapoznaj się z przewodnikiem Przewodnik po usłudze Azure Time Series Insights przewodnik [przewodnika po usłudze Szybki start.](time-series-quickstart.md)
