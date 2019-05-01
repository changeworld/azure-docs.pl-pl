---
title: 'Szybki start: Eksplorator usługi Azure Time Series Insights | Microsoft Docs'
description: W tym przewodniku Szybki start pokazano, jak rozpocząć pracę z eksploratorem usługi Azure Time Series Insights w przeglądarce internetowej, aby wizualizować duże ilości danych IoT. Poznaj najważniejsze funkcje w środowisku pokazu.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696955"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Szybki start: Poznawanie usługi Azure Time Series Insights

Przewodnik Szybki Start Eksplorator znajduje się na wprowadzenie do usługi Azure Time Series Insights w bezpłatnym środowisku pokazowym. Za jego pośrednictwem dowiesz się, jak za pomocą przeglądarki internetowej wizualizować duże ilości IoT danych i przewodnik po najważniejszych funkcjach obecnie w ogólnej dostępności.

Usługa Azure Time Series Insights jest w pełni zarządzana usługa analizy, magazynowania i wizualizacji, która upraszcza sposób eksplorować i analizować miliardy zdarzeń IoT jednocześnie. Daje ona globalny widok Twoich danych, umożliwiając szybkie weryfikowanie rozwiązań IoT i uniknięcie kosztownych przestojów krytycznych urządzeń. Za pomocą usługi Azure Time Series Insights można wykryć ukryte trendy, zauważyć anomalie i przeprowadzanie analiz głównych przyczyn niemal w czasie rzeczywistym.

Dodatkowej elastyczności usługi Azure Time Series Insights można dodać do istniejących aplikacji za pośrednictwem jego zaawansowane [interfejsów API REST](./time-series-insights-update-tsq.md) i [zestawu SDK klienta](./tutorial-create-tsi-sample-spa.md). Interfejsy API umożliwiają przechowywanie danych szeregów czasowych zapytań i używanie danych szeregów czasowych w aplikacji klienckiej wybranych przez użytkownika. Można również użyć zestawu SDK klienta, aby dodać składniki interfejsu użytkownika do istniejącej aplikacji.

Ogólnie rzecz biorąc dostępności usługi Time Series Insights, który explorer jest przewodnik po polecanych obecnie.

## <a name="prepare-the-demo-environment"></a>Przygotuj środowisko wersji demonstracyjnej

1. Tworzenie [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Jeśli nie zostało utworzone.

1. W przeglądarce przejdź do [pokaz ogólnie](https://insights.timeseries.azure.com/demo).

1. Jeśli zostanie wyświetlony monit, zaloguj się do Eksploratora usługi Time Series Insights przy użyciu poświadczeń konta platformy Azure.

1. Zostanie wyświetlona strona szybkiego przewodnika po usłudze Time Series Insights. Kliknij przycisk **Dalej**, aby zacząć szybki przewodnik.

   [![Kliknij przycisk Dalej](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Zapoznaj się z środowisku pokazu

1. Zostanie wyświetlony **panel wyboru czasu**. Użyj tego panelu, aby wybrać przedział czasu do wizualizacji.

   [![Panel wyboru czasu](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Kliknij i przeciągnij w wybranym regionie, a następnie kliknij przycisk **Wyszukaj**.

   [![Wybierz przedział czasu](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Usługa Time Series Insights wyświetli wizualizację wykresu dla określonego przedziału czasu. W ramach wykresu liniowego możesz wykonywać różne czynności, takie jak filtrowanie, przypinanie, sortowania i nakładanie.

   Aby powrócić do **panelu wyboru czasu**, kliknij strzałkę w dół, jak pokazano na ilustracji:

   [![Wykres](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Kliknij przycisk **Dodaj** na **panelu terminów**, aby dodać nowy termin wyszukiwania.

   [![Dodaj element](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Na wykresie możesz wybrać region, kliknąć prawym przyciskiem myszy ten region i wybrać polecenie **Eksploruj zdarzenia**.

   [![Eksploruj zdarzenia](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Zostanie wyświetlona siatka danych pierwotnych z badanego regionu:

   [![Widok siatki](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Wybieranie i filtrowanie danych

1. Edytuj terminy, aby zmienić wartości na wykresie, oraz dodaj kolejny termin w celu krzyżowej korelacji różnych typów wartości:

   [![Dodaj termin](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Wprowadź termin filtrowania w **Filtruj szeregi...**  pola do filtrowania zaimprowizowanego serii. Na potrzeby tego przewodnika Szybki start wprowadź termin **Station5** (Stacja5), aby skorelować temperaturę i ciśnienie dla tej stacji.

   [![Filtruj szeregi](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Po zakończeniu przewodnika Szybki start możesz poeksperymentować z zestawem danych przykładowych, aby tworzyć różne wizualizacje.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz utworzyć własne środowisko usługi Time Series Insights:
> [!div class="nextstepaction"]
> [Planowanie środowiska usługi Time Series Insights](time-series-insights-environment-planning.md)
