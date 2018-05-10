---
title: Rozwiązywanie problemów z analizy użycia w usłudze Azure Application Insights
description: Przewodnik rozwiązywania problemów — analizowanie danych użycia witryny i aplikacji z usługą Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin;daviste
ms.openlocfilehash: 654b99085c406f13fe95476457234761bf840422
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Rozwiązywanie problemów z analizy użycia w usłudze Application Insights
Masz pytania dotyczące [użycia narzędzia do analizy w usłudze Application Insights](app-insights-usage-overview.md): [zdarzenia użytkowników, sesji,](app-insights-usage-segmentation.md), [Lejki](usage-funnels.md), [przepływu użytkownika](app-insights-usage-flows.md), [Przechowywania](app-insights-usage-retention.md), lub stado? Poniżej przedstawiono niektóre odpowiedzi.

## <a name="counting-users"></a>Zliczanie użytkowników
**Analiza użycia, które narzędzia pokazują, że Moja aplikacja jednej sesji użytkownika /, jednak sprawdzić Moja aplikacja ma wiele użytkowników/sesji. Jak rozwiązać te nieprawidłowe liczby?**

Wszystkie zdarzenia telemetrii w usłudze Application Insights mają [identyfikator użytkownika anonimowego](application-insights-data-model-context.md) i [identyfikator sesji](application-insights-data-model-context.md) jako dwa ich właściwości standardowych. Domyślnie wszystkie narzędzia analizy użycia liczba użytkowników i sesje, w oparciu o te identyfikatory. Jeśli te standardowe właściwości nie są wypełniane przy użyciu unikatowych identyfikatorów dla każdego użytkownika i sesji aplikacji, zostanie wyświetlone niepoprawna liczba użytkowników i sesji w narzędziach do analiz użycia.

Jeśli w przypadku monitorowania aplikacji sieci web, najlepszym rozwiązaniem jest dodanie [zestaw SDK usługi Application Insights JavaScript](app-insights-javascript.md) do aplikacji i upewnij się, że załadowano fragment skrypt na każdej stronie, które chcesz monitorować. Zestaw SDK JavaScript automatycznie generuje użytkownika anonimowego i identyfikatory sesji, a następnie wypełnia zdarzenia telemetrii te identyfikatory, ponieważ są one wysyłane z aplikacji.

Jeśli w przypadku monitorowania usługi sieci web (bez interfejsu użytkownika), [utworzyć inicjatora dane telemetryczne, które wypełnia użytkownik anonimowy identyfikator sesji Identyfikatora właściwości i](app-insights-usage-send-user-context.md) zgodnie z usługą koncepcje unikatowych użytkowników i sesji.

Jeśli aplikacja jest wysyła [uwierzytelniony identyfikatory użytkowników](app-insights-api-custom-events-metrics.md#authenticated-users), może być liczona uwierzytelnionego użytkownika na podstawie identyfikatorów w narzędziu użytkownicy. Na liście rozwijanej "Pokaż" wybierz pozycję "Użytkownicy uwierzytelnieni".

Narzędzia analizy użycia nie obsługuje obecnie, zliczania użytkowników lub sesji na podstawie właściwości niż identyfikator użytkownika anonimowego, Identyfikatora uwierzytelnionego użytkownika lub identyfikator sesji.

## <a name="naming-events"></a>Zdarzenia nazewnictwa
**Moja aplikacja ma tysiące zdarzenie niestandardowe nazwy i innej strony widoku. Trudno ich rozróżnienia i narzędzi analizy użycia często przestać odpowiadać. Jak rozwiązać te problemy nazewnictwa**

Widok strony i nazwy zdarzenie niestandardowe są używane w całej narzędzia analizy użycia. Dobrze nazw zdarzeń jest krytyczna do pobierania wartości z tych narzędzi. Celem jest kompromis między o zbyt mało zbyt ogólnym nazw ("kliknięto element Button") oraz o zbyt wiele, zbyt określonej nazwy ("kliknięty przycisk Edytuj http://www.contoso.com/index").

Aby wprowadzić zmiany widoku i nazwy niestandardowych zdarzeń, które wysyła aplikacji, musisz zmienić kod źródłowy aplikacji i wdróż go ponownie. **Wszystkie dane telemetryczne dane w usłudze Application Insights są przechowywane przez 90 dni i nie można usunąć**, więc zmiany nazwy zdarzenia potrwa 90 dni do pełni manifestu. 90 dni po wprowadzeniu zmiany nazwy nazwy starych i nowych zdarzeń zostanie pojawiają się w obrębie telemetrii, więc Dostosuj zapytania i komunikacji w obrębie zespołów, w związku z tym.

Jeśli aplikacja wysyła zbyt wiele nazw widoku strony, sprawdź, czy te nazwy widoku strony zostały ręcznie określone w kodzie, lub jeśli kody są wysyłane automatycznie przez zestaw SDK usługi Application Insights JavaScript:

* Jeśli nazwy widoku strony ręcznie zostały określone w kodu za pomocą [ `trackPageView` interfejsu API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), Zmień nazwę, aby być mniej dokładny. Unikaj typowych pomyłek, takich jak wprowadzenie adresu URL, nazwę widoku strony. Zamiast tego należy użyć parametru adresu URL `trackPageView` interfejsu API. Przenieś inne szczegóły z nazwy widoku strony do właściwości niestandardowych.

* Zestaw SDK usługi Application Insights JavaScript jest automatycznie wysyła nazwy widoku strony, można zmienić tytuły stron sieci lub przełącz się do ręczne wysyłanie nazwy widoku strony. Zestaw SDK wysyła [tytuł](https://developer.mozilla.org/docs/Web/HTML/Element/title) każdej strony jako nazwa widoku strony, domyślnie. Można zmienić Twojego tytułów bardziej ogólne, ale zachować ostrożność, optymalizację dla aparatów wyszukiwania i inne ta zmiana może mieć wpływ na środowisko. Ręczne określanie widok strony nazwy z `trackPageView` interfejsu API zastępuje nazwy zbierane automatycznie, więc może wysłać więcej ogólnych nazw w danych telemetrycznych bez zmiany tytułów.   

Jeśli aplikacja wysyła zbyt wiele nazw zdarzenie niestandardowe, należy zmienić nazwę w szerszym kodu. Ponownie należy unikać umieszczania adresy URL i inne na stronie lub dynamiczne informacje w nazwach niestandardowych zdarzeń w bezpośrednio. Zamiast tego należy przenieść te informacje do niestandardowej właściwości niestandardowe zdarzenie z `trackEvent` interfejsu API. Na przykład zamiast z `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, zalecamy przypominać `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie analizy użycia](app-insights-usage-overview.md)

## <a name="get-help"></a>Uzyskiwanie pomocy
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

