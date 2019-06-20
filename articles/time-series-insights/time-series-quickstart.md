---
title: 'Szybki start: Eksplorator usługi Azure Time Series Insights | Microsoft Docs'
description: Ten przewodnik Szybki Start pokazano, jak rozpocząć pracę z Eksploratorem usługi Azure Time Series Insights w przeglądarce sieci web, aby wizualizować duże ilości danych IoT. Poznaj najważniejsze funkcje w środowisku pokazu.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 415ce28a7cab77c538a7dfb8f387900ff515dd0e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164562"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Szybki start: Poznawanie usługi Azure Time Series Insights

Ten przewodnik Szybki Start Eksplorator usługi Azure Time Series Insights pomaga wprowadzenie do usługi Time Series Insights w bezpłatnym środowisku pokazowym. Za jego pośrednictwem dowiesz się, jak za pomocą przeglądarki internetowej wizualizować duże ilości IoT danych i samouczek kluczowych funkcji, które są teraz ogólnie dostępna.

Usługa Azure Time Series Insights jest w pełni zarządzana usługa analizy, magazynowania i wizualizacji, która upraszcza sposób eksplorować i analizować miliardy zdarzeń IoT jednocześnie. Zapewnia globalny wgląd w dane tak, aby szybkie weryfikowanie rozwiązań IoT i uniknięcie kosztownych przestojów krytycznych urządzeń. Usługa Azure Time Series Insights pomaga wykryć ukryte trendy, zauważyć anomalie i przeprowadzanie analiz głównych przyczyn niemal w czasie rzeczywistym.

Dodatkowej elastyczności usługi Azure Time Series Insights można dodać do istniejących aplikacji za pośrednictwem jego zaawansowane [interfejsów API REST](./time-series-insights-update-tsq.md) i [zestawu SDK klienta](./tutorial-create-tsi-sample-spa.md). Interfejsy API służy do przechowywania zapytania i korzystania z danych szeregów czasowych w aplikacji klienckiej wybranego. Możesz również użyć zestawu SDK klienta można dodać składniki interfejsu użytkownika do istniejącej aplikacji.

Ten przewodnik Szybki Start Eksplorator usługi Time Series Insights oferuje Przewodnik po funkcji, które są teraz ogólnie dostępna.

## <a name="prepare-the-demo-environment"></a>Przygotuj środowisko wersji demonstracyjnej

1. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Jeśli nie zostały one utworzone już.

1. W przeglądarce przejdź do [pokaz ogólnie](https://insights.timeseries.azure.com/demo).

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

1. Na stronie szybki przegląd usługi Time Series Insights są wyświetlane. Wybierz **dalej** aby zacząć szybki przewodnik.

   [![Wybierz przycisk Dalej](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Zapoznaj się z środowisku pokazu

1. **Panelu wyboru czasu** Wyświetla. Użyj tego panelu, aby wybrać przedział czasu do wizualizacji.

   [![Panel wyboru czasu](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Wybierz przedział czasu i przeciągnij go w regionie. Następnie wybierz pozycję **wyszukiwania**.

   [![Wybierz przedział czasu](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Usługa Time Series Insights wyświetli wizualizację wykresu dla określonego przedziału czasu. Możesz wykonywać różne czynności w ramach wykresu liniowego. Na przykład można filtrować, przypinanie, sortowania i stosu.

   Aby powrócić do **panelu wyboru czasu**, wybierz strzałkę w dół, jak pokazano:

   [![Wykres](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Wybierz **Dodaj** w **panel terminów** Aby dodać nowy termin wyszukiwania.

   [![Dodaj element](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Na wykresie możesz wybrać region, kliknąć prawym przyciskiem myszy ten region i wybrać polecenie **Eksploruj zdarzenia**.

   [![Eksploruj zdarzenia](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Siatka danych pierwotnych Wyświetla z regionu znajdującego się, że badania.

   [![Widok siatki](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Wybieranie i filtrowanie danych

1. Edytuj terminy, aby zmienić wartości na wykresie. Dodaj kolejny termin w celu krzyżowej korelacji różnych typów wartości.

   [![Dodaj termin](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Wprowadź termin filtrowania w **Filtruj szeregi** pola do filtrowania zaimprowizowanego serii. Na potrzeby tego przewodnika Szybki start wprowadź termin **Station5** (Stacja5), aby skorelować temperaturę i ciśnienie dla tej stacji.

   [![Filtruj szeregi](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Po zakończeniu przewodnika Szybki start możesz poeksperymentować z zestawem danych przykładowych, aby tworzyć różne wizualizacje.

## <a name="next-steps"></a>Kolejne kroki

Możesz przystąpić do tworzenia środowiska usługi Time Series Insights:
> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights](time-series-insights-environment-planning.md)
