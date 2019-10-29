---
title: 'Szybki Start: Eksplorator Azure Time Series Insights | Microsoft Docs'
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z Eksploratorem Azure Time Series Insights w przeglądarce sieci Web, aby wizualizować duże ilości danych IoT. Poznaj najważniejsze funkcje w środowisku pokazu.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: 156e95c88bc3c66890ba9c8baf9b281330b60186
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990906"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Szybki start: poznawanie usługi Azure Time Series Insights

Ten Azure Time Series Insights przewodniku szybki start ułatwia rozpoczęcie pracy z usługą Time Series Insights w wolnym środowisku demonstracyjnym. W tym przewodniku szybki start dowiesz się, jak za pomocą przeglądarki sieci Web wizualizować duże ilości danych IoT i funkcji kluczowych przewodników, które są ogólnie dostępne.

Azure Time Series Insights to w pełni zarządzana usługa analizy, magazynu i wizualizacji, która upraszcza, jak eksplorować i analizować miliardy zdarzeń IoT jednocześnie. Zapewnia ona globalny widok danych, dzięki czemu możesz szybko zweryfikować swoje rozwiązanie IoT i uniknąć kosztownych przestojów w przypadku urządzeń o krytycznym znaczeniu. Azure Time Series Insights pomaga w odnajdywaniu ukrytych trendów, wykrytych anomalii i przeprowadzaniu analiz głównych przyczyn w czasie zbliżonym do rzeczywistego.

Aby uzyskać dodatkową elastyczność, można dodać Azure Time Series Insights do istniejącej aplikacji za pomocą jej zaawansowanych [interfejsów API REST](./time-series-insights-update-tsq.md) i [zestawu SDK klienta](https://github.com/microsoft/tsiclient). Za pomocą interfejsów API można przechowywać dane szeregów czasowych, wykonywać zapytania i korzystać z nich w wybranej aplikacji klienckiej. Można również użyć zestawu SDK klienta, aby dodać składniki interfejsu użytkownika do istniejącej aplikacji.

Ten przewodnik Szybki Start dla programu Time Series Insights Explorer oferuje funkcje, które są ogólnie dostępne.

> [!IMPORTANT]
> Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , jeśli jeszcze tego nie zrobiono.

## <a name="prepare-the-demo-environment"></a>Przygotowanie środowiska demonstracyjnego

1. W przeglądarce przejdź do [głównej wersji demonstracyjnej](https://insights.timeseries.azure.com/demo).

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora Time Series Insights przy użyciu poświadczeń konta platformy Azure.

1. Zostanie wyświetlona strona Time Series Insights Quick przewodnika. Wybierz pozycję **dalej** , aby rozpocząć szybkie samouczki.

   [![wybierz pozycję Dalej](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Eksplorowanie środowiska demonstracyjnego

1. Zostanie wyświetlony **panel wybór czasu** . Użyj tego panelu, aby wybrać przedział czasu do wizualizacji.

   [Panel wyboru![czas](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Wybierz przedział czasu i przeciągnij go w regionie. Następnie wybierz pozycję **Wyszukaj**.

   [![wybrać przedział czasu](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Usługa Time Series Insights wyświetli wizualizację wykresu dla określonego przedziału czasu. Na wykresie liniowym można wykonywać różne czynności. Na przykład można filtrować, przypinać, sortować i stosu.

   Aby powrócić do **panelu wyboru czasu**, wybierz strzałkę w dół, jak pokazano:

   [Wykres![](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Wybierz pozycję **Dodaj** na **panelu warunki** , aby dodać nowy termin wyszukiwania.

   [![dodać elementu](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Na wykresie możesz wybrać region, kliknąć prawym przyciskiem myszy ten region i wybrać polecenie **Eksploruj zdarzenia**.

   [![Eksplorowanie zdarzeń](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Siatka danych pierwotnych jest wyświetlana w regionie, w którym jest przeprowadzana Eksplorowanie.

   [Widok siatki![](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Wybieranie i filtrowanie danych

1. Edytuj swoje warunki, aby zmienić wartości na wykresie. Dodaj kolejny termin, aby skorelować krzyżowo różne typy wartości.

   [![dodać termin](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Wprowadź termin filtru w polu **Seria filtru** dla filtrowania serii Improvised. Na potrzeby tego przewodnika Szybki start wprowadź termin **Station5** (Stacja5), aby skorelować temperaturę i ciśnienie dla tej stacji.

   [Seria filtru![](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Po zakończeniu przewodnika Szybki start możesz poeksperymentować z zestawem danych przykładowych, aby tworzyć różne wizualizacje.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

Wszystko jest gotowe do utworzenia własnego środowiska Time Series Insightsowego:
> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights](time-series-insights-environment-planning.md)
