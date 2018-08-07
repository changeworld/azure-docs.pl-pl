---
title: Jak wyświetlić informacje o użyciu interfejsu API usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlać metryki dla wywołania interfejsu API usługi Azure Maps w portalu.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9eb58f41ec89d084cc388eeadb335046fb587bc3
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581575"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Jak wyświetlić informacje o użyciu interfejsu API usługi Azure Maps
W tym artykule przedstawiono sposób wyświetlania metryk użycia interfejsu API dla konta usługi Azure Maps w [portal](https://portal.azure.com). Metryki są wyświetlane w postaci wykresu wygodne wzdłuż można dostosować czas trwania. 

## <a name="view-metric-snapshot"></a>Wyświetl migawkę metryki 

Niektóre typowe metryki może zobaczyć na **Przegląd** strona konta usługi Maps. Obecnie pokazuje *łączna liczba żądań*, *całkowita liczba błędów*, i *dostępności* za pośrednictwem można wybrać czas trwania. 

![Przegląd metryk usługi Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Przejdź do następnej sekcji Jeżeli trzeba dostosować te wykresy dla konkretnej analizy.


## <a name="view-detailed-metrics"></a>Wyświetl szczegółowe metryki

1. Zaloguj się do subskrypcji platformy Azure w [portal](https://portal.azure.com). 

2. Kliknij przycisk **wszystkie zasoby** menu elementu po lewej stronie i przejdź do swojej *konta usługi Azure Maps*.

3. Po otwarciu kontem mapy kliknij **metryki** menu po lewej stronie.

4. Na **metryki** okienku wybrać jedną z następujących czynności:

    1. **Dostępność**, który pokazuje *średni* dostępność interfejsu API w danym okresie czasu, lub 
    2. **Użycie**, który pokazuje sposób użycia *liczba* dla swojego konta. 

    ![Okienko usługi Azure Maps metryki](media/how-to-view-api-usage/portal-metrics.png)

5. Po wybraniu metryki można wybrać *zakres czasu* , wybierając na **ostatnie 12 godzin (automatycznie)** co jest wartością domyślną. Można również wybrać *stopień szczegółowości czasu*, jak również wybrać wyświetlanie godziny jako *lokalnego* lub *GMT* w tej samej listy rozwijanej. Kliknij przycisk **Zastosuj**.

    ![Usługi Azure Maps metryki okresie](media/how-to-view-api-usage/time-range.png)
 
6. Po dodaniu swoje metryki, możesz następnie **Dodaj filtr** z poziomu tych właściwości, które są istotne, metryki, a następnie wybierz wartość właściwości, który chcesz wyświetlić wykresu dla. 

    ![Filtr metryk usługi Azure Maps](media/how-to-view-api-usage/filter.png)

7. Możesz również **zastosować podział** swoje metryki na podstawie Twojej wybranej właściwości metryki. Dzięki temu wykres, który ma być podzielony na wiele wykresów, jeden dla każdej wartości tej właściwości. Na przykład na poniższej ilustracji, kolor każdego wykresu odnosi się do wartości właściwości wyświetlane w dolnej części wykresu.

    ![Dzielenie metryk usługi Azure Maps](media/how-to-view-api-usage/splitting.png)
 
8. Możesz też zapoznać wiele metryk na tym samym wykresie poprzez kliknięcie **Dodaj metrykę** przycisk u góry.


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak śledzić użycie usługi Azure Maps, można przejść do informacji bardziej zaawansowane funkcje, takie jak:

* [Użycie w pojeździe](consumption-model.md), lub
* [Rozszerzanie GeoJSON](extend-geojson.md)

Ponadto Dowiedz się więcej o interfejsach API za pomocą:
* [Dokumentacja interfejsu API REST usługi mapy platformy Azure](https://docs.microsoft.com/rest/api/maps)
