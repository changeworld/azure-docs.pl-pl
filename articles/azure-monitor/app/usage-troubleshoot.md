---
title: Rozwiązywanie problemów z narzędziami do analizy użytkowników — usługa Azure Application Insights
description: Przewodnik rozwiązywania problemów — analizowanie użycia witryny i aplikacji za pomocą usługi Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670920"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Rozwiązywanie problemów z narzędziami do analizy zachowań użytkowników w usłudze Application Insights
Masz pytania dotyczące [narzędzi do analizy zachowań użytkowników w usłudze Application Insights:](usage-overview.md) [Użytkownicy, Sesje, Zdarzenia,](usage-segmentation.md) [Ścieżki, Przepływy](usage-funnels.md) [użytkowników,](usage-flows.md) [Retencja](usage-retention.md)lub Kohorty? Oto kilka odpowiedzi.

## <a name="counting-users"></a>Liczenie użytkowników
**Narzędzia do analizy zachowań użytkowników pokazują, że moja aplikacja ma jednego użytkownika/sesję, ale wiem, że moja aplikacja ma wielu użytkowników/sesje. Jak mogę naprawić te nieprawidłowe liczby?**

Wszystkie zdarzenia telemetryczne w usłudze Application Insights mają [anonimowy identyfikator użytkownika](../../azure-monitor/app/data-model-context.md) i [identyfikator sesji](../../azure-monitor/app/data-model-context.md) jako dwie ich standardowe właściwości. Domyślnie wszystkie narzędzia analizy użycia zliczają użytkowników i sesje na podstawie tych identyfikatorów. Jeśli te standardowe właściwości nie są wypełniane unikatowymi identyfikatorami dla każdego użytkownika i sesji aplikacji, zobaczysz niepoprawną liczbę użytkowników i sesji w narzędziach analizy użycia.

Jeśli monitorujesz aplikację sieci web, najprostszym rozwiązaniem jest dodanie [sdk JavaScript aplikacji](../../azure-monitor/app/javascript.md) do aplikacji i upewnij się, że fragment kodu skryptu jest ładowany na każdej stronie, którą chcesz monitorować. JavaScript SDK automatycznie generuje anonimowe identyfikatory użytkowników i sesji, a następnie wypełnia zdarzenia telemetryczne z tych identyfikatorów, ponieważ są one wysyłane z aplikacji.

Jeśli monitorujesz usługę sieci web (bez interfejsu użytkownika), [utwórz inicjatora telemetrii, który wypełnia anonimowy identyfikator użytkownika i właściwości identyfikatora sesji](usage-send-user-context.md) zgodnie z pojęciami usługi unikatowych użytkowników i sesji.

Jeśli aplikacja wysyła [uwierzytelnione identyfikatory użytkowników,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)możesz liczyć na podstawie uwierzytelnionych identyfikatorów użytkowników w narzędziu Użytkownicy. Z listy rozwijanej "Pokaż" wybierz opcję "Uwierzytelnieni użytkownicy".

Narzędzia do analizy zachowań użytkowników nie obsługują obecnie inwentaryzacji użytkowników lub sesji na podstawie właściwości innych niż anonimowy identyfikator użytkownika, uwierzytelniony identyfikator użytkownika lub identyfikator sesji.

## <a name="naming-events"></a>Nazywanie zdarzeń
**Moja aplikacja ma tysiące różnych widoków stron i niestandardowe nazwy zdarzeń. Trudno jest odróżnić je, a narzędzia do analizy zachowań użytkowników często przestają odpowiadać. Jak rozwiązać te problemy z nazewnictwem?**

Widok strony i niestandardowe nazwy zdarzeń są używane w narzędziach do analizy zachowań użytkowników. Dobre nazywanie zdarzeń ma kluczowe znaczenie dla uzyskania wartości z tych narzędzi. Celem jest równowaga między posiadaniem zbyt mało, zbyt ogólne nazwy ("Button kliknięty") i o zbyt\/wiele, zbyt konkretne nazwy ("Edytuj przycisk kliknął na http: /www.contoso.com/index").

Aby wprowadzić zmiany w widoku strony i niestandardowe nazwy zdarzeń, które wysyła aplikacja, należy zmienić kod źródłowy aplikacji i ponownie wdrożyć. **Wszystkie dane telemetryczne w usłudze Application Insights są przechowywane przez 90 dni i nie można ich usunąć,** więc zmiany wprowadzone w nazwach zdarzeń będą trwać 90 dni, aby w pełni zamanifestować. Przez 90 dni po dokonaniu zmiany nazwy, zarówno stare, jak i nowe nazwy zdarzeń będą wyświetlane w danych telemetrycznych, więc odpowiednio dostosuj zapytania i komunikuj się w obrębie zespołów.

Jeśli aplikacja wysyła zbyt wiele nazw widoku strony, sprawdź, czy te nazwy widoku strony są określone ręcznie w kodzie lub czy są wysyłane automatycznie przez SDK JavaScript aplikacji:

* Jeśli nazwy widoku strony są ręcznie określone w kodzie przy użyciu [ `trackPageView` interfejsu API,](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)zmień nazwę na mniej szczegółową. Unikaj typowych błędów, takich jak umieszczanie adresu URL w nazwie widoku strony. Zamiast tego należy użyć parametru adresu URL `trackPageView` interfejsu API. Przenieś inne szczegóły z nazwy widoku strony do właściwości niestandardowych.

* Jeśli sdk JavaScript aplikacji aplikacji automatycznie wysyła nazwy widoku strony, można zmienić tytuły stron lub przełączyć się na ręczne wysyłanie nazw widoku strony. SDK domyślnie wysyła [tytuł](https://developer.mozilla.org/docs/Web/HTML/Element/title) każdej strony jako nazwę widoku strony. Możesz zmienić swoje tytuły, aby być bardziej ogólne, ale należy pamiętać o SEO i innych skutków tej zmiany może mieć. Ręczne określanie nazw `trackPageView` widoku strony za pomocą interfejsu API zastępuje automatycznie zbierane nazwy, dzięki czemu można wysyłać więcej nazw ogólnych w telemetrii bez zmiany tytułów stron.   

Jeśli aplikacja wysyła zbyt wiele niestandardowych nazw zdarzeń, zmień nazwę w kodzie, aby była mniej szczegółowa. Ponownie należy unikać umieszczania adresów URL i innych informacji na stronie lub dynamicznych w nazwach zdarzeń niestandardowych bezpośrednio. Zamiast tego przenieś te szczegóły do właściwości niestandardowych `trackEvent` zdarzenia niestandardowego za pomocą interfejsu API. Na przykład, zamiast `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, proponujemy `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`coś takiego .

## <a name="next-steps"></a>Następne kroki

* [Omówienie narzędzi do analizy zachowań użytkowników](usage-overview.md)

## <a name="get-help"></a>Uzyskiwanie pomocy
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

