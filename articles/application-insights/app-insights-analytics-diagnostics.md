---
title: Inteligentne diagnostyki zmiany wydajności aplikacji sieci web w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Automatyczne diagnostyki nagłego lub kroków w danych telemetrycznych wydajności z aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: 08b1da205797d663191dde45f8c091dba91fc573
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294371"
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnozowanie nagłych zmian telemetrii aplikacji

*Ta funkcja jest dostępna w wersji zapoznawczej.*

Diagnozowanie nagłych zmian wydajności aplikacji sieci web lub użycia za pomocą jednego kliknięcia! Funkcja diagnostyki inteligentne jest dostępna, gdy utworzysz wykres czasu [Analytics](app-insights-analytics.md) w [usługi Application Insights](app-insights-overview.md). Wszędzie tam, gdzie nietypowe zachowanie podczas zmiany z trend wyników, na przykład kolekcji lub dip, inteligentne diagnostyki identyfikuje wzorzec wymiarów oraz powiązanych wartości, które może wyjaśnić zmiany. Ułatwia to szybkie diagnozowanie problemów. 

W tym przykładzie inteligentne diagnostyki zidentyfikował wzorzec wartości właściwości skojarzonych z zmiany i zaznacza różnica między wyników z lub bez tego wzorca:

![wyniki diagnostyki przykład analityka](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnozowanie zmian danych

1.  Uruchamianie kwerendy w module analiz i renderować ją jako wykres czasu. 
2.  Kliknij przycisk dowolnego punktu wyróżnione szczytu, jeśli istnieje.
 
    ![punkt godzinami szczytu](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnostyka zajmuje kilka sekund, aby wykryć wzorzec.

3. Na karcie wyników diagnostyki zawiera wzorzec, który może wyjaśnić przerwa Twoje dane.

    ![wynik](./media/app-insights-analytics-diagnostics/result.png)
 
    Tekst zawiera wyświetlane służące do skorelowania z zmiany wartości wymiaru. W tym przykładzie jest ona skojarzona z określonym żądaniem i wersji przeglądarki.

    Zauważ również dwa składniki wykresu, przy użyciu filtru true i false. Składnik false pokazuje trend bez zmian. Innymi słowy nie została zmieniona w wynikach telemetrii, jeśli Wyłączamy problematyczne kombinacji wymiarów zidentyfikowanych diagnostyki. Z kolei wyniki w obrębie tej kombinacji Pokaż znaczne zmiany w obszarze wyróżnione postępowania. Oznacza to, że diagnostyki znalazł kombinację właściwości opisano zmianę.

4.  Jeśli wzorzec jest złożony, musisz umieść kursor nad **Pokaż wszystkie** Aby wyświetlić wymiary.

    ![pokaż wszystko](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  W przypadku diagnostyki wykryje nie znaczących wzorzec do powiadamiania o, zostanie wyświetlone na stronie nie wyników. W tym momencie możesz zmienić zapytania. Na przykład można zawęzić zakres czasu i binning w zapytaniu analizy, w celu dalszej analizy i potencjalnie lepsze wyniki.

Dzięki wiedzy czy określonej strony witryny sieci Web ma problem w szczególności przeglądarki, można teraz przejść bezpośrednio do strony problem i zbadaj ostatnio wprowadzone zmiany.

## <a name="try-the-demo"></a>Wypróbuj wersję demonstracyjną

[Kliknij tutaj, aby wyświetlić pokaz](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) na przykładowych danych.

## <a name="how-it-works"></a>Jak to działa

Diagnostyka inteligentne używa nieobsługiwanego algorytmu uczenia zaawansowane nienadzorowanych maszyny, na podstawie [DiffPatterns](app-insights-analytics-reference.md) operacji. Wyszukuje wzorce kandydujących, które może wyjaśnić zmian danych. Analizy wpływu każdego kandydata na metryki i zawiera wzorzec, że najlepiej są powiązane zmiany.

## <a name="no-diagnostic-points"></a>Brak punktów diagnostyczne?

Inteligentnych diagnostyki działa tylko wtedy, gdy są spełnione następujące kryteria:

 * Inteligentne ustawienia diagnostyki jest włączone. Sprawdź w obszarze ikony ustawienia w module analiz.
 * Opcja inteligentne diagnostyki w ustawieniach Analytics jest zaznaczona. 
 * Oś czasu: osi x wykresu musi być typu `datetime`.
 * Wiersz lub obszaru wykresu: Diagnostyka działa tylko te typy wykresów. Użyj `| render timechart` lub `| render areachart` na końcu kwerendy; lub wybierz z listy rozwijanej selektora wiersza lub obszaru wykresu.
 * Brak ciągłości: Musi istnieć znaczne brak ciągłości w danych.
 * Punkty wystarczające do analizy.
 * Podsumowanie nie więcej niż jedną klauzulę w zapytaniu.
 * Klauzula nie projektu zawiera definicję nazwa przed klauzulą podsumowanie.

 
 ## <a name="related-articles"></a>Pokrewne artykuły:

 * [Samouczek analityka](app-insights-analytics-tour.md)
 * [Inteligentne wykrywania](app-insights-proactive-diagnostics.md) automatycznie ostrzega o problemy z wydajnością.