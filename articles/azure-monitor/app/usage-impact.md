---
title: Wpływ użycia usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Przeanalizuj, jak różne właściwości mogą mieć wpływ na współczynniki konwersji części aplikacji.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671039"
---
# <a name="impact-analysis-with-application-insights"></a>Analiza wpływu za pomocą usługi Application Insights

Impact analizuje, jak czasy ładowania i inne właściwości wpływają na współczynniki konwersji dla różnych części aplikacji. Mówiąc dokładniej, odkrywa, jak **dowolny wymiar** **widoku strony,** **zdarzenia niestandardowego**lub **żądania** wpływa na użycie innego **widoku strony** lub **zdarzenia niestandardowego.** 

![Narzędzie udarowe](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Nadal nie wiesz, jaki wpływ ma?

Jednym ze sposobów myślenia o Wpływ jest jako ostateczne narzędzie do rozstrzygania sporów z kimś w zespole o tym, jak powolność w jakimś aspekcie witryny wpływa na to, czy użytkownicy trzymają się. Podczas gdy użytkownicy mogą tolerować pewną powolność, Wpływ daje wgląd w to, jak najlepiej zrównoważyć optymalizację i wydajność, aby zmaksymalizować konwersję użytkowników.

Ale analizowanie wydajności to tylko podzbiór możliwości Impact. Ponieważ Impact obsługuje niestandardowe zdarzenia i wymiary, odpowiadając na pytania, takie jak to, w jaki sposób wybór przeglądarki użytkownika koreluje z różnymi współczynnikami konwersji, znajduje się zaledwie kilka kliknięć.

![Konwersja zrzutu ekranu przez przeglądarki](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Zasób usługi Application Insights musi zawierać widoki strony lub zdarzenia niestandardowe, aby użyć narzędzia Wpływ. [Dowiedz się, jak skonfigurować aplikację do automatycznego zbierania widoków stron za pomocą zestawu SDK JavaScript aplikacji Insights](../../azure-monitor/app/javascript.md). Należy również pamiętać, że ponieważ analizujesz korelację, rozmiar próbki ma znaczenie.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Czy czas ładowania strony ma wpływ na liczbę osób konwertujących na mojej stronie?

Aby rozpocząć odpowiadanie na pytania za pomocą narzędzia Wpływ, wybierz początkowy widok strony, zdarzenie niestandardowe lub żądanie.

![Narzędzie udarowe](./media/usage-impact/0002-dropdown.png)

1. Wybierz widok strony z listy rozwijanej **Dla widoku strony.**
2. Pozostaw **analizę, jak jego** listy rozwijanej na domyślny wybór **czas trwania** (W tym kontekście **Czas trwania** jest aliasem czasu **ładowania strony**.)
3. Aby **uzyskać wpływ na użycie listy rozwijanej,** wybierz zdarzenie niestandardowe. To zdarzenie powinno odpowiadać element interfejsu użytkownika w widoku strony wybranym w kroku 1.

![Zrzut ekranu przedstawiający wyniki](./media/usage-impact/0003-results.png)

W tym przypadku, gdy czas ładowania **strony produktu** zwiększa współczynnik konwersji do **kliknięcia produktu zakupu,** spada. Na podstawie powyższego rozkładu optymalny czas ładowania strony wynoszący 3,5 sekundy może być ukierunkowany na osiągnięcie potencjalnego współczynnika konwersji wynoszącego 55%. Dalsze ulepszenia wydajności w celu skrócenia czasu ładowania poniżej 3,5 sekundy nie są obecnie skorelowane z dodatkowymi korzyściami konwersji.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co zrobić, jeśli śledzę widoki stron lub czasy ładowania w niestandardowy sposób?

Impact obsługuje zarówno standardowe, jak i niestandardowe właściwości i pomiary. Użyj tego, co chcesz. Zamiast czasu trwania, użyj filtrów na zdarzenia podstawowe i pomocnicze, aby uzyskać bardziej szczegółowe.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Czy użytkownicy z różnych krajów lub regionów konwertują się po różnym tempie?

1. Wybierz widok strony z listy rozwijanej **Dla widoku strony.**
2. Wybierz "Kraj lub region" w **analizowaniu sposobu jego** listy rozwijanej
3. Aby **uzyskać wpływ na użycie listy rozwijanej,** wybierz zdarzenie niestandardowe, które odpowiada elementowi interfejsu użytkownika w widoku strony wybranym w kroku 1.

W takim przypadku wyniki nie pasują już do ciągłego modelu osi x, tak jak w pierwszym przykładzie. Zamiast tego przedstawiona jest wizualizacja podobna do ścieżki segmentowanej. Sortuj według **użycia,** aby wyświetlić odmianę konwersji na wydarzenie niestandardowe na podstawie kraju/regionu.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>W jaki sposób narzędzie Impact oblicza te współczynniki konwersji?

Pod maską narzędzie Impact opiera się na [współczynniku korelacji Pearsona.](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient) Wyniki są obliczane między -1 i 1 z -1 reprezentujący ujemną korelację liniową i 1 reprezentujący dodatnią korelację liniową.

Podstawowy podział działania analizy wpływu jest następujący:

Let _A_ = widok strony głównej/niestandardowe zdarzenie/żądanie wybrane w pierwszym menu rozwijanym. (Dla**widoku strony**).

Let _B_ = pomocniczy widok strony/zdarzenie**niestandardowe, które wybierzesz**( wpływa na użycie ).

Impact analizuje próbkę wszystkich sesji od użytkowników w wybranym zakresie czasu. Dla każdej sesji wyszukuje każde wystąpienie _A_.

Sesje są następnie podzielone na dwa różne rodzaje _podsesji_ na podstawie jednego z dwóch warunków:

- Przekonwertowana podsesja składa się z sesji kończącej się zdarzeniem _B_ i obejmuje wszystkie zdarzenia _A_ występujące przed _B_.
- Nieprzekonwertowana podsesja występuje, gdy wszystkie _A_występują bez zacisku _B_.

Sposób ostatecznego obliczania wpływu zależy od tego, czy analizujemy według metryki, czy według wymiaru. Dla metryki wszystkie _A_'s w podsesji są uśredniania. W przypadku wymiarów wartość każdego _A_ przyczynia się _1/N_ do wartości przypisanej do _B,_ gdzie _N_ jest liczbą _A_w podsesji.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowisko użycia, zacznij wysyłać [niestandardowe zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [widoki strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli wysyłasz już niestandardowe zdarzenia lub widoki stron, zapoznaj się z narzędziami Użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](usage-retention.md)
    - [User Flows (Przepływy użytkowników)](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
