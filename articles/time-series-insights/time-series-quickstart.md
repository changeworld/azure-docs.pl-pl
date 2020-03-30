---
title: 'Szybki start: Eksplorator usługi Azure Time Series Insights — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: Dowiedz się, jak rozpocząć pracę z Eksploratorem usługi Azure Time Series Insights. Wizualizuj duże ilości danych IoT i zapoznaj się z kluczowymi funkcjami środowiska.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: a905054b1b2a04fa2b7865d2c1065ccee37cffc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75860434"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Szybki start: poznawanie usługi Azure Time Series Insights

Ten szybki start usługi Azure Time Series Insights ułatwia rozpoczęcie pracy z usługą Time Series Insights w bezpłatnym środowisku demonstracyjnym. W tym przewodniku Szybki start dowiesz się, jak używać przeglądarki sieci Web do wizualizacji dużych ilości danych IoT i kluczowych funkcji przewodnika, które są obecnie ogólnie dostępne.

Usługa Azure Time Series Insights to w pełni zarządzana usługa analizy, magazynu i wizualizacji, która upraszcza jednoczesne eksplorowanie i analizowanie miliardów zdarzeń IoT. Zapewnia globalny widok danych, dzięki czemu można szybko zweryfikować rozwiązanie IoT i uniknąć kosztownych przestojów na urządzeniach o znaczeniu krytycznym. Usługa Azure Time Series Insights ułatwia odkrywanie ukrytych trendów, wykrywanie anomalii i przeprowadzanie analiz przyczyn źródłowych w czasie zbliżonym do rzeczywistego.

Aby uzyskać dodatkową elastyczność, można dodać usługę Azure Time Series Insights do istniejącej wcześniej aplikacji za pośrednictwem [zaawansowanych interfejsów API REST](./time-series-insights-update-tsq.md) i [sdk klienta.](https://github.com/microsoft/tsiclient) Interfejsy API można używać do przechowywania, wykonywania zapytań i używania danych szeregów czasowych w wybranej aplikacji klienckiej. Można również użyć sdk klienta, aby dodać składniki interfejsu użytkownika do istniejącej aplikacji.

Ten szybki start usługi Time Series Insights explorer oferuje przewodnik po funkcjach, które są ogólnie dostępne.

> [!IMPORTANT]
> Utwórz [bezpłatne konto platformy Azure,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) jeśli jeszcze go nie utworzyłeś.

## <a name="prepare-the-demo-environment"></a>Przygotowanie środowiska demonstracyjnego

1. W przeglądarce przejdź do [wersji demonstracyjnej ogólnej dostępności](https://insights.timeseries.azure.com/demo).

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

1. Zostanie wyświetlona skrócona strona szybkiego przewodnika Usługi Time Series Insights. Wybierz **przycisk Dalej,** aby rozpocząć szybką wycieczkę.

   [![Zapraszamy do szybkiego startu - wybierz dalej](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Poznaj środowisko demonstracyjne

1. Zostanie wyświetlony **panel wyboru czasu.** Użyj tego panelu, aby wybrać przedział czasu do wizualizacji.

   [![Panel wyboru czasu](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Wybierz przedział czasu i przeciągnij go w regionie. Następnie wybierz **pozycję Wyszukaj**.

   [![Wybieranie przedziału czasu](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Usługa Time Series Insights wyświetli wizualizację wykresu dla określonego przedziału czasu. Można wykonywać różne czynności na wykresie liniowym. Na przykład można filtrować, przypinać, sortować i układać stos.

   Aby powrócić do **panelu wyboru Czas,** wybierz strzałkę w dół, jak pokazano na rysunku:

   [![Wykres](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Wybierz **pozycję Dodaj** w panelu **Warunki,** aby dodać nowy wyszukiwany termin.

   [![Dodawanie panelu wyszukiwanych haseł](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. Na wykresie możesz wybrać region, kliknąć prawym przyciskiem myszy ten region i wybrać polecenie **Eksploruj zdarzenia**.

   [![Eksploruj zdarzenia](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Siatka nieprzetworzonych danych jest wyświetlana z regionu, który eksplorujesz.

   [![Eksploruj zdarzenia — widok danych siatki](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Wybieranie i filtrowanie danych

1. Edytuj terminy, aby zmienić wartości na wykresie. Dodaj inny termin do krzyżowego skorelowania różnych typów wartości.

   [![Dodaj termin](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Pozostaw puste pole **Seria Filtruj,** aby wyświetlić wszystkie wybrane wyszukiwane terminy, lub wprowadź termin filtru w polu **Filtruj serię** do filtrowania serii improwizowanych.

   [![Filtrowanie szeregów](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Na potrzeby tego przewodnika Szybki start wprowadź termin **Station5** (Stacja5), aby skorelować temperaturę i ciśnienie dla tej stacji.

Po zakończeniu przewodnika Szybki start możesz poeksperymentować z zestawem danych przykładowych, aby tworzyć różne wizualizacje.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu samouczka posprzątaj utworzone zasoby:

1. Z lewego menu w [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Wszystkie zasoby**— znajdź grupę zasobów usługi Azure Time Series Insights.
1. Usuń całą grupę zasobów (i wszystkie zasoby w niej zawarte), wybierając **pozycję Usuń** lub usuń każdy zasób indywidualnie.

## <a name="next-steps"></a>Następne kroki

Możesz utworzyć własne środowisko usługi Time Series Insights:
> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights](time-series-insights-environment-planning.md)
