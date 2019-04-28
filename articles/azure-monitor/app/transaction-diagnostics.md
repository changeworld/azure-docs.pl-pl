---
title: Usługi Azure Application Insights transakcji diagnostyki | Dokumentacja firmy Microsoft
description: Usługa Application Insights transakcji end-to-end diagnostics
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c6c44525018e2115f1df8ed2d3f15432b95490c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783714"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostyka ujednoliconego transakcji między składnikami

Diagnostyka ujednolicone środowisko automatycznie odpowiada telemetrii po stronie serwera, z dotyczące wszystkich składników usługi Application Insights monitorowane w jednym widoku. Nie ma znaczenia w przypadku wielu zasobów przy użyciu kluczy Instrumentacji oddzielne. Usługa Application Insights wykrywa relacji podstawowej i pozwala na łatwe diagnozowanie składnika aplikacji, zależność lub wyjątek, który spowodował transakcji spowolnienia lub niepowodzenia.

## <a name="what-is-a-component"></a>Co to jest składnikiem?

Składniki są niezależne wdrażanym poszczególnych części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacyjne mają widoczność na poziomie kodu lub dostęp do telemetrii wygenerowanej przez te składniki aplikacji.

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, itp. Centrum zdarzeń, które zespół/organizacji nie mogą mieć dostęp do (kod lub telemetrii).
* Składniki są uruchomione na dowolną liczbę wystąpień kontenera server/roli.
* Składniki mogą być oddzielnych kluczy Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role, raportowanie do jednego klucza Instrumentacji usługi Application Insights. Nowe środowisko zawiera szczegóły dotyczące wszystkich składników, niezależnie od tego, jak one zostały skonfigurowane.

> [!NOTE]
> * **Brak łączy powiązanych elementów?** Wszystkie powiązane dane telemetryczne są w [górnej](#cross-component-transaction-chart) i [dolnej](#all-telemetry-with-this-operation-id) sekcje po lewej stronie. 

## <a name="transaction-diagnostics-experience"></a>Środowisko Diagnostyka transakcji
Ten widok składa się z czterech części klucza: wyniki listy, wykres transakcji międzyskładnikowych, listę sekwencję czasową wszystkie dane telemetryczne dotyczące tej operacji i okienka szczegółów dla każdego elementu wybranego telemetrii po lewej stronie.

![Kluczowe elementy](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Wykres transakcji między składnikami

Ten wykres zawiera oś czasu z poziome paski na czas trwania żądania i zależności między składnikami. Wszelkie wyjątki, które są zbierane są również oznaczane na osi czasu.

* Górny wiersz na tym wykresie reprezentuje punkt wejścia żądania przychodzącego, aby pierwszy składnik o nazwie w tej transakcji. Czas trwania to łączny czas na ukończenie transakcji.
* Wszystkie wywołania zależności zewnętrznych są proste-zwijany wiersze, w których ikony reprezentującej typ zależności.
* Wywołania do innych składników są zwijane wierszy. Każdy wiersz odpowiada określonej operacji wywołać składnika.
* Domyślne żądania, zależności lub wyjątku czy zaznaczone jest wyświetlany po prawej stronie.
* Wybierz wszystkie wiersze, aby wyświetlić jego [szczegółów po prawej stronie](#details-of-the-selected-telemetry). 

> [!NOTE]
> Wywołania do innych składników mają dwa wiersze: jeden wiersz reprezentuje wywołanie ruchu wychodzącego (zależności) ze składnika obiektu wywołującego, a innych wierszy odpowiada na żądanie dla ruchu przychodzącego na składnik o nazwie. Ikona wiodące i odrębnych stylów pasków czas trwania pomóc je odróżnić.

## <a name="all-telemetry-with-this-operation-id"></a>Wszystkie dane telemetryczne z tym identyfikatorem operacji

W tej sekcji przedstawiono widok listy niezhierarchizowanej w czasie sekwencji wszystkie dane telemetryczne dotyczące tej transakcji. Zawiera również niestandardowe zdarzenia i dane śledzenia, które nie są wyświetlane na wykresie transakcji. Można filtrować dane telemetryczne wygenerowane przez składnik/wywołań dla tej listy. Można wybrać dowolny element danych telemetrycznych na tej liście, aby wyświetlić odpowiadające [szczegółów po prawej stronie](#details-of-the-selected-telemetry).

![Sekwencja czas wszystkie dane telemetryczne](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Szczegóły wybranego telemetrii

W tym okienku zwijany przedstawiono szczegóły każdego wybranego elementów z wykresu transakcji lub z listy. "Pokaż wszystkie" zawiera listę wszystkich standardowych atrybutów, które są zbierane. Wszelkie atrybuty niestandardowe zostały oddzielnie wymienione poniżej standardowego zestawu. Kliknij pozycję "..." poniżej okna śledzenia stosu, aby uzyskać możliwość kopiowania śledzenia. "Ślady narzędzia profiler Otwórz" lub "Otwórz migawkę debugowania" przedstawia Diagnostyka na poziomie kodu w odpowiedniej okienka szczegółów.

![Szczegóły wyjątku](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Wyniki wyszukiwania

W tym okienku zwijany przedstawia wyniki, które spełniają kryteria filtru. Kliknij dowolny wynik można zaktualizować szczegółów odpowiednich 3 sekcji wymienionych powyżej. Spróbujemy znaleźć przykłady, które z największym prawdopodobieństwem mogą mieć szczegóły dostępny w wszystkich składników, nawet jeśli jest stosowane w dowolnym z nich. Są one wyświetlane jako "sugerowane" przykłady.

![Wyniki wyszukiwania](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler i snapshot debugger

[Narzędzie Application Insights profiler](../../azure-monitor/app/profiler.md) lub [rozszerzenia snapshot debugger](snapshot-debugger.md) pomoc przy użyciu diagnostyki kodu poziom wydajności i niepowodzeń problemów. Z tego środowiska można wyświetlić ślady narzędzia profiler, lub kliknij przycisk migawki z dowolnego składnika za pomocą jednego.

Jeśli nie można pobrać Profiler pracy, skontaktuj się z **serviceprofilerhelp\@microsoft.com**

Jeśli nie można pobrać rozszerzenia Snapshot Debugger pracy, skontaktuj się z **snapshothelp\@microsoft.com**

![Integracja Profiler](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Często zadawane pytania

*Na wykresie widać jeden składnik, a pozostałe są pokazywane są tylko jako zależności zewnętrzne bez żadnych szczegółów, co się stało, w ramach tych składników.*

Potencjalne przyczyny:

* Inne składniki działają z usługą Application Insights?
* Są one przy użyciu najnowszej stabilnej SDK usługi Application Insights?
* Jeśli te składniki są oddzielne zasoby usługi Application Insights, czy masz wymagany dostęp do swoich danych telemetrycznych?

Jeśli masz dostęp do składników są wyposażone w najnowszych zestawów SDK Application Insights, Daj nam znać za pośrednictwem kanału górnego prawego sprzężenia.

*Czy mogę zobaczyć zduplikowane wiersze zależności. Jest to oczekiwane?*

W tej chwili będziemy są wyświetlane wywołanie wychodzące zależności oddzielony od ruchu przychodzącego żądania. Zazwyczaj dwóch wywołań się taka sama, jak tylko wartości czasu trwania są różne ze względu na sieć obiegu. Ikona wiodące i odrębnych stylów pasków czas trwania pomóc je odróżnić. Ta prezentacja danych jest myląca ze? Przekaż nam swoją opinię!

*Co zegara pochyla między wystąpieniami innego składnika?*

Osie czasu są dopasowane do wynikających z przesunięcia czasowego zegara na wykresie transakcji. Możesz zobaczyć dokładną sygnatury czasowe, w okienku szczegółów lub za pomocą analizy.

*Dlaczego nowe środowisko brakuje większość zapytań powiązanych elementów?*

Jest to celowe. Wszystkie elementy powiązane, dotyczące wszystkich składników, są już dostępne po lewej stronie (z góry i u dołu sekcji). Nowe środowisko ma dwa powiązane elementy, które nie obejmują po lewej stronie: wszystkie dane telemetryczne w ciągu pięciu minut przed i po nim to zdarzenie i oś czasu użytkownika.
