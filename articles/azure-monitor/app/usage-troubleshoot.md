---
title: Rozwiązywanie problemów z narzędzi analizy zachowania użytkownika w usłudze Azure Application Insights
description: Przewodnik rozwiązywania problemów — analizowanie danych użycia witryny i aplikacji z usługą Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: eabc47c2acb33d8c6ee03477b5e8c7783edebbb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371856"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Rozwiązywanie problemów z narzędzi analizy zachowania użytkownika w usłudze Application Insights
Masz pytania dotyczące [narzędzi analizy zachowania użytkownika w usłudze Application Insights](usage-overview.md): [Użytkownicy, sesje, zdarzenia](usage-segmentation.md), [Lejki](usage-funnels.md), [przepływy użytkownika](usage-flows.md), [przechowywania](usage-retention.md), lub kohorty? Poniżej przedstawiono odpowiedzi.

## <a name="counting-users"></a>Zliczanie użytkowników
**Narzędzia analizy zachowania użytkowników pokazują, że Moja aplikacja ma jednej sesji użytkownika /, ale wiadomo, że Moja aplikacja ma wiele użytkowników/sesji. Jak naprawić te liczbę niepoprawnych zliczeń:**

Wszystkie zdarzenia telemetrii w usłudze Application Insights mają [identyfikator użytkownika anonimowego](../../azure-monitor/app/data-model-context.md) i [identyfikator sesji](../../azure-monitor/app/data-model-context.md) jako dwa ich właściwości standardowych. Domyślnie wszystkie narzędzia analizy użycia liczba użytkowników i sesji, w oparciu o tych identyfikatorów. Jeśli te właściwości standardowe nie jest wypełnione za pomocą unikatowych identyfikatorów dla każdego użytkownika i sesji aplikacji, zobaczysz niepoprawna liczba użytkowników i sesji w narzędziach analizy użycia.

Jeśli monitorujesz aplikacji sieci web Najprostszym rozwiązaniem jest dodać [zestaw JavaScript SDK Application Insights](../../azure-monitor/app/javascript.md) do aplikacji i upewnij się, że fragment kodu skryptu jest ładowany na każdej stronie, którą chcesz monitorować. Zestaw SDK JavaScript automatycznie generuje użytkownika anonimowego i identyfikatorów sesji, a następnie wypełnia zdarzeń telemetrii za pomocą tych identyfikatorów, ponieważ są one wysyłane z aplikacji.

Jeśli monitorujesz usługi sieci web (bez interfejsu użytkownika), [tworzenie inicjatora telemetrii, który wypełnia właściwości Identyfikatora identyfikator i sesji użytkownika anonimowego](usage-send-user-context.md) zgodnie z usługi zarządzania unikatowych użytkowników i sesji.

Jeśli Twoja aplikacja wysyła [uwierzytelniony użytkownik identyfikatory](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), możesz liczyć na podstawie uwierzytelnionego użytkownika identyfikatorów w narzędziu użytkownicy. Na liście rozwijanej "Pokaż" Wybierz "Uwierzytelnieni użytkownicy".

Narzędzia do analizy zachowania użytkownika aktualnie nie obsługuje zliczania użytkowników lub sesji na podstawie właściwości innego niż identyfikator użytkownika anonimowego, identyfikator użytkownika uwierzytelnionego lub identyfikator sesji.

## <a name="naming-events"></a>Nadawanie nazw zdarzeń
**Moja aplikacja zawiera tysiące innej strony widoku i nazwy zdarzeń niestandardowych. Jest trudny do rozróżnienia między nimi i narzędzia analizy zachowania użytkowników często przestanie odpowiadać. Jak naprawić te problemy nazewnictwa**

Widok strony i zdarzeń niestandardowych nazw są używane w całym narzędzia analizy zachowania użytkowników. Nazewnictwo zdarzeń oraz do pobierania wartości z tych narzędzi ma krytyczne znaczenie. Celem jest kompromis między masz za mało, zbyt ogólne nazw ("kliknięto przycisk") i równoważy zbyt wiele, nadmiernie określonej nazwy ("przycisk Edytuj kliknięty http:\//www.contoso.com/index").

Aby wprowadzić zmiany do widoku strony i nazwy zdarzeń niestandardowych, które Twoja aplikacja wysyła, musisz zmienić kod źródłowy i ponownego wdrażania aplikacji. **Wszystkie dane telemetryczne danych w usłudze Application Insights są przechowywane przez 90 dni i nie można usunąć**, dzięki czemu zmiany wprowadzone do nazw zdarzeń zajmie 90 dni do w pełni manifestu. Przez 90 dni po wprowadzeniu zmiany nazwy nazwy zdarzeń stary i nowy będzie pojawiają się w ramach telemetrii, więc dostosować zapytania i komunikacji w obrębie zespołów, odpowiednio.

Jeśli Twoja aplikacja wysyła za dużo nazw widoku strony, należy sprawdzić, czy te nazwy widoku strony są ręcznie określić w kodzie, lub jeśli kody są wysyłane automatycznie przez zestaw SDK języka JavaScript usługi Application Insights:

* Jeśli nazwy widoku strony są określane ręcznie kod przy użyciu [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), Zmień nazwę aby być mniej dokładny. Należy unikać typowych pomyłek, takich jak wprowadzenie adresu URL, nazwę widoku strony. Zamiast tego należy użyć parametru adresu URL `trackPageView` interfejsu API. Przenieś inne szczegóły od nazwy widoku strony do właściwości niestandardowe.

* Zestaw SDK języka JavaScript usługi Application Insights automatycznie wysyła nazwy widoku strony, można zmienić tytułów stron sieci lub przełączyć się na wysyłanie ręcznie nazwy widoku strony. Zestaw SDK wysyła [tytuł](https://developer.mozilla.org/docs/Web/HTML/Element/title) każdej strony jako nazwa widoku strony, domyślnie. Można zmienić swoje tytułów do bardziej ogólnych, ale być w trosce o optymalizacji dla aparatów wyszukiwania i innych wpływu na środowisko, które ta zmiana nie może mieć. Ręczne określenie widoku strony nazwy z `trackPageView` API zastępuje nazw automatycznie zbierane, dzięki czemu będzie można wysłać bardziej ogólnych nazw w danych telemetrycznych, bez konieczności zmieniania tytułów stron.   

Jeśli Twoja aplikacja wysyła za dużo nazw zdarzeń niestandardowych, należy zmienić nazwę w kod, aby być mniej dokładny. Ponownie Unikaj umieszczania adresy URL i inne na stronie lub informacji dynamicznych nazw niestandardowych zdarzeń bezpośrednio. Zamiast tego należy przenieść te informacje do właściwości niestandardowe zdarzenie niestandardowe z `trackEvent` interfejsu API. Na przykład, zamiast z `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, sugerujemy podobny `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Kolejne kroki

* [Omówienie narzędzia analizy zachowania użytkowników](usage-overview.md)

## <a name="get-help"></a>Uzyskiwanie pomocy
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

