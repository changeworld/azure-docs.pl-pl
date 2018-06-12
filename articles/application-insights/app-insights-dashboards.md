---
title: Pulpity nawigacyjne i nawigacja w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Utwórz widoki kwerend i wykresy APM klucza.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 72859f68fc1bb76a6c71bbd7b98cd713f1f0fe02
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296404"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Nawigacji i pulpitów nawigacyjnych w portalu usługi Application Insights
Po utworzeniu [skonfiguruj usługę Application Insights w projekcie](app-insights-overview.md), dane telemetryczne dotyczące wydajności i użycia aplikacji będą wyświetlane w projektu zasobu usługi Application Insights w [portalu Azure](https://portal.azure.com).

## <a name="find-your-telemetry"></a>Znajdź telemetrii
Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do zasobu usługi Application Insights, który został utworzony dla aplikacji.

![Kliknij przycisk Przeglądaj, wybierz usługę Application Insights, a następnie aplikacji.](./media/app-insights-dashboards/00-start.png)

Omówienie bloku (strona) aplikacji przedstawiono podsumowanie kluczowych metryk diagnostyki aplikacji i jest bramy do innych funkcji w portalu.

![Głównych tras, aby wyświetlić telemetrii](./media/app-insights-dashboards/010-oview.png)

Można dostosować żadnego z wykresami i siatki i przypiąć je na pulpicie nawigacyjnym. W ten sposób można zebrać razem klucza dane telemetryczne z różnych aplikacji na pulpicie nawigacyjnym centralnej.

## <a name="dashboards"></a>Pulpity nawigacyjne
Najpierw Zobacz po zalogowaniu się do [portalu Microsoft Azure](https://portal.azure.com) jest pulpit nawigacyjny. W tym miejscu można zebrać razem wykresy, które są najważniejsze dla Ciebie we wszystkich zasobów platformy Azure, w tym dane telemetryczne z [Azure Application Insights](app-insights-overview.md).

![Dostosowany pulpit nawigacyjny.](./media/app-insights-dashboards/31.png)

1. **Przejdź do określonych zasobów** takich jak aplikacji w usłudze Application Insights: Użyj pasku po lewej stronie.
2. **Wróć do bieżącego pulpitu nawigacyjnego**, lub przełącz się do innych widoków ostatnie: Użyj menu rozwijane u góry po lewej.
3. **Przełącz pulpity nawigacyjne**: Użyj menu rozwijanego tytuł pulpitu nawigacyjnego
4. **Tworzenie, edytowanie i udostępnianie pulpitów nawigacyjnych** na pasku nawigacyjnym.
5. **Edytuj pulpitu nawigacyjnego**: Umieść kursor nad kafelka, a następnie użyć jego górnym pasku do przenoszenia, dostosować lub usuń go.

## <a name="add-to-a-dashboard"></a>Dodaj do pulpitu nawigacyjnego
Podczas wyszukiwania w bloku lub zbiór wykresy, która ma zastosowanie szczególnie, można przypiąć kopii do pulpitu nawigacyjnego. Zobaczysz go następnym razem, gdy istnieje powrocie.

![Aby przypiąć wykres, umieść kursor nad jego, a następnie kliknij przycisk "..." w nagłówku.](./media/app-insights-dashboards/33.png)

1. Numer PIN wykres do pulpitu nawigacyjnego. Kopię wykres zostanie wyświetlony na pulpicie nawigacyjnym.
2. Przypnij cały blok do pulpitu nawigacyjnego — pojawia się na pulpicie nawigacyjnym jako kafelków, które można kliknąć, za pośrednictwem.
3. Kliknij lewym górnym rogu, aby powrócić do bieżącego pulpitu nawigacyjnego. Następnie można użyć listy rozwijanej, aby powrócić do bieżącego widoku.

Zwróć uwagę, że wykresów są podzielone na kafelkach: kafelka może zawierać więcej niż jeden wykres. Można przypiąć całego kafelka pulpitu nawigacyjnego.

Wykres są automatycznie odświeżane z częstotliwością, która zależy od zakresu czasu wykresu:

* Maksymalnie 1 godzina przedziałów czasu: Odśwież co 5 minut
* Zakres czasu 1-24 godzin: Odśwież co 15 minut
* Zakres powyżej 24 godzin czasu: (zakres czasu) / 60.

### <a name="pin-any-query-in-analytics"></a>Przypnij dowolnej kwerendy w module analiz
Możesz również [przypiąć Analytics](app-insights-analytics-using.md#pin-to-dashboard) wykresy do [udostępnionego](#share-dashboards-with-your-team) pulpitu nawigacyjnego. Dzięki temu można Dodaj wykresy każde zapytanie dowolnego obok standardowe metryki. 

Wyniki są automatycznie obliczane ponownie co godzinę. Kliknij ikonę odświeżania na wykresie, aby ponownie obliczyć natychmiast. (Odśwież w przeglądarce nie oblicza ponownie.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Dostosuj kafelka na pulpicie nawigacyjnym
Po kafelka na pulpicie nawigacyjnym można dostosować.

![Umieść kursor nad wykresu, aby go edytować.](./media/app-insights-dashboards/36.png)

1. Dodaj wykres do fragmentu.
2. Ustaw metryki, Grupuj według wymiaru i styl (tabeli, wykres) wykresu.
3. Przeciągnij w diagramie, aby powiększyć; Kliknij przycisk Cofnij, aby zresetować timespan; Ustawianie właściwości filtru dla wykresów na kafelku.
4. Ustaw tytułem kafelka.

Kafelki przypięte z Eksploratora metryk bloków ma więcej opcji edycji niż Kafelki przypięte z bloku omówienie.

Kafelek oryginalnego przypięte nie ma wpływu zmiany.

## <a name="switch-between-dashboards"></a>Przełączanie między pulpity nawigacyjne
Można zapisać więcej niż jednym pulpicie nawigacyjnym i przełączać się między nimi. Po przypięciu wykres lub bloku, są one dodawane do bieżącego pulpitu nawigacyjnego.

![Aby przełączyć między pulpitami nawigacyjnymi, kliknij pozycję pulpit nawigacyjny i wybierz zapisany pulpitu nawigacyjnego. Aby utworzyć i zapisać nowego pulpitu nawigacyjnego, kliknij przycisk Nowy. Aby zmienić kolejność, kliknij przycisk Edytuj.](./media/app-insights-dashboards/32.png)

Na przykład może być jednym pulpicie nawigacyjnym do wyświetlania pełnego ekranu w pokoju zespołu i drugi dla rozwoju ogólne.

Na pulpicie nawigacyjnym zostanie wyświetlony blok jako Kafelek: kliknij go, aby przejść do bloku. Wykres replikuje wykresu w jej oryginalnej lokalizacji.

![Kliknij Kafelek, aby otworzyć blok, który reprezentuje](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Udostępnianie pulpitów nawigacyjnych
Po utworzeniu pulpitu nawigacyjnego, można go udostępniać innym użytkownikom.

![W nagłówku pulpitu nawigacyjnego kliknij przycisk Udostępnij](./media/app-insights-dashboards/41.png)

Dowiedz się więcej o [ról i kontroli dostępu](app-insights-resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Programowe tworzenie pulpitów nawigacyjnych
Można zautomatyzować za pomocą tworzenia pulpitu nawigacyjnego [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) i proste edytora JSON.

## <a name="app-navigation"></a>Aplikacja nawigacji
Omówienie bloku jest bramy, aby uzyskać więcej informacji o aplikacji.

* **Kafelek lub wykres** — kliknij Kafelek lub wykresu, aby zobaczyć więcej szczegółów na temat jego wartość.

### <a name="overview-blade-buttons"></a>Przyciski bloku — omówienie
![Omówienie bloku górnym pasku nawigacyjnym](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Eksploratora metryk** ](app-insights-metrics-explorer.md) — tworzenie własnych wykresów wydajności i użycia.
* [**Wyszukiwanie** ](app-insights-diagnostic-search.md) — Zbadaj określone wystąpienia zdarzenia, takie jak żądań, wyjątków, lub dziennika śledzenia.
* [**Analiza** ](app-insights-analytics.md) -wydajnych zapytań za pośrednictwem telemetrii.
* **Zakres czasu** — Dostosuj zakres wyświetlanych przez wszystkich schematów w bloku.
* **Usuń** — Usuwanie zasobu usługi Application Insights dla tej aplikacji. Należy również albo usunąć pakietów usługi Application Insights w kodzie aplikacji, lub edytować [klucza Instrumentacji](app-insights-create-new-resource.md#copy-the-instrumentation-key) w aplikacji, aby przekierować dane telemetryczne do innego zasobu usługi Application Insights.

### <a name="essentials-tab"></a>Karta Essentials
* [Klucz Instrumentacji](app-insights-create-new-resource.md#copy-the-instrumentation-key) -identyfikuje tego zasobu aplikacji.

### <a name="app-navigation-bar"></a>Pasek nawigacyjny aplikacji
![Lewy pasek nawigacyjny](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Omówienie** -powrócić do bloku Omówienie aplikacji.
* **Dziennik aktywności** — alerty i zdarzenia administracyjne platformy Azure.
* [**Kontrola dostępu** ](app-insights-resources-roles-access-control.md) — zapewnianie dostępu do członków zespołu i inne.
* [**Tagi** ](../azure-resource-manager/resource-group-using-tags.md) -używaj tagów do grupy aplikacji z innymi osobami.

ZBADAJ

* [**Mapowanie aplikacji** ](app-insights-app-map.md) -aktywna mapa przedstawiająca składniki aplikacji, pochodzących z informacji o zależnościach.
* [**Inteligentne wykrywania** ](app-insights-proactive-diagnostics.md) — Przejrzyj ostatnie alerty dotyczące wydajności.
* [**Strumień na żywo** ](app-insights-live-stream.md) — A ustalony zbiór metryki niemal natychmiastowe przydatne podczas wdrażania nowej kompilacji lub debugowania.
* [**Dostępność / testów sieci Web** ](app-insights-monitor-web-app-availability.md) -wysyłać żądania regularne do aplikacji sieci web z wokół world.*
* [**Błędów, wydajności** ](app-insights-web-monitor-performance.md) -wyjątki, awariami i czas odpowiedzi dla żądań kierowanych do aplikacji i żądania od aplikacji [zależności](app-insights-asp-net-dependencies.md).
* [**Wydajność** ](app-insights-web-monitor-performance.md) — czas odpowiedzi, czas odpowiedzi zależności.
* [Serwery](app-insights-web-monitor-performance.md) -liczników wydajności. Jeśli dostępne możesz [Zainstaluj Monitor stanu](app-insights-monitor-performance-live-website-now.md).
* **Przeglądarka** — strona widoku i wydajności AJAX. Jeśli dostępne możesz [Instrumentacja stron sieci web](app-insights-javascript.md).
* **Użycie** — strona widoku, użytkownika i sesji liczby. Jeśli dostępne możesz [Instrumentacja stron sieci web](app-insights-javascript.md).

KONFIGURUJ

* **Wprowadzenie** — samouczek wbudowanego.
* **Właściwości** -klucza instrumentacji, subskrypcji i identyfikator zasobu.
* [Alerty](app-insights-alerts.md) -metryki konfiguracji alertu.
* [Eksport ciągły](app-insights-export-telemetry.md) — Konfigurowanie eksportowania danych telemetrycznych do magazynu Azure.
* [Testowanie wydajności](app-insights-monitor-web-app-availability.md#performance-tests) -konfigurowania syntetycznego obciążenia w witrynie sieci Web.
* [Limit przydziału i cenach](app-insights-pricing.md) i [próbkowania wprowadzanie](app-insights-sampling.md).
* **Dostępu do interfejsu API** — tworzenie [wersji adnotacje](app-insights-annotations.md) i interfejsu API dostępu do danych.
* [**Elementy robocze** ](app-insights-diagnostic-search.md#create-work-item) -nawiązać połączenia z pracy, w systemie śledzenia, dzięki czemu można tworzyć błędy podczas sprawdzania telemetrii.

USTAWIENIA

* [**Blokuje** ](../azure-resource-manager/resource-group-lock-resources.md) — blokowanie zasobów platformy Azure
* [**Skrypt automatyzacji** ](app-insights-powershell.md) — Eksportowanie definicji zasobów platformy Azure, aby jako szablon służy do tworzenia nowych zasobów.


## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki

|  |  |
| --- | --- |
| [Eksploratora metryk](app-insights-metrics-explorer.md)<br/>Filtr i segmentu metryki |![Przykładowe wyszukiwania](./media/app-insights-dashboards/64.png) |
| [Wyszukiwanie diagnostycznych](app-insights-diagnostic-search.md)<br/>Znajdowanie i Sprawdź zdarzenia, zdarzenia powiązane i tworzenie usterki |![Przykładowe wyszukiwania](./media/app-insights-dashboards/61.png) |
| [Analiza](app-insights-analytics.md)<br/>Język zapytania zaawansowane |![Przykładowe wyszukiwania](./media/app-insights-dashboards/63.png) |
