---
title: Wyświetlanie metryk użycia interfejsu API usługi Azure Maps | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlić metryki dla wywołań interfejsu API usługi Microsoft Azure Maps w witrynie Azure portal.
author: philmea
ms.author: philmea
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 0eb117af712b3b1f63a3f99c96cba9775f8e3996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335164"
---
# <a name="view-azure-maps-api-usage-metrics"></a>Wyświetlanie metryk użycia interfejsu API usługi Azure Maps

W tym artykule pokazano, jak wyświetlić metryki użycia interfejsu API dla konta usługi Azure Maps w [witrynie Azure portal.](https://portal.azure.com) Metryki są wyświetlane w wygodnym formacie wykresu wzdłuż konfigurowalny czas trwania.

## <a name="view-metric-snapshot"></a>Wyświetl migawkę metryki

Na stronie **Przegląd** konta Mapy możesz zobaczyć kilka typowych danych. Obecnie pokazuje *całkowitą liczbę żądań,* *całkowita liczba błędów*i *dostępność* w wybranym czasie trwania.

![Omówienie metryk użycia usługi Azure Maps](media/how-to-view-api-usage/portal-overview.png)

Przejdź do następnej sekcji, jeśli chcesz dostosować te wykresy do konkretnej analizy.

## <a name="view-detailed-metrics"></a>Wyświetlanie szczegółowych danych

1. Zaloguj się do subskrypcji platformy Azure w [portalu](https://portal.azure.com).

2. Kliknij element menu **Wszystkie zasoby** po lewej stronie i przejdź do konta usługi *Azure Maps*.

3. Po otwarciu konta Mapy kliknij menu **Metryki** po lewej stronie.

4. W okienku **Metryki** wybierz jedną z następujących opcji:

   1. **Dostępność** — która pokazuje *średnią* dostępności interfejsu API w określonym czasie.
   2. **Użycie** — co pokazuje, jak *liczy się* użycie dla Twojego konta.

      ![Okienko metryki użycia usługi Azure Maps](media/how-to-view-api-usage/portal-metrics.png)

5. Następnie możesz wybrać *zakres czasu,* klikając **przycisk Ostatnie 24 godziny (Automatyczne)**. Domyślnie zakres czasu jest ustawiony na 24 godziny. Po kliknięciu zobaczysz wszystkie możliwe do wyboru zakresy czasu. Można wybrać *szczegółowość czasu* i wybrać wyświetlanie czasu jako *lokalnego* lub *GMT* w tej samej rozwijanej. Kliknij przycisk **Zastosuj**.

    ![Zakres czasu metryk usługi Azure Maps](media/how-to-view-api-usage/time-range.png)

6. Po dodaniu danych można **dodać filtr** z właściwości istotnych dla tej metryki. Następnie wybierz wartość właściwości, która ma być widoczna na wykresie.

    ![Filtr metryk użycia usługi Azure Maps](media/how-to-view-api-usage/filter.png)

7. Możesz również **zastosować podział** dla danych na podstawie wybranej właściwości metryki. Umożliwia wykres podzielić na wiele wykresów, dla każdej wartości tej właściwości. Na poniższym rysunku kolor każdego wykresu odpowiada wartości właściwości wyświetlanej w dolnej części wykresu.

    ![Dzielenie metryk użycia usługi Azure Maps](media/how-to-view-api-usage/splitting.png)

8. Możesz również zaobserwować wiele danych na tym samym wykresie, po prostu klikając przycisk **Dodaj metrykę** u góry.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o interfejsach API usługi Azure Maps, dla których chcesz śledzić użycie:
> [!div class="nextstepaction"] 
> [Instrukcje dotyczące zestawów SDK usługi Azure Maps w sieci Web](how-to-use-map-control.md)

> [!div class="nextstepaction"] 
> [Instrukcje dotyczące zestawów SDK dla systemu Android usługi Azure Maps](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi Azure Maps REST](https://docs.microsoft.com/rest/api/maps)
