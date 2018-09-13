---
title: Monitorowanie wydajności aplikacji internetowej platformy Azure | Microsoft Docs
description: Monitorowanie wydajności aplikacji dla aplikacji internetowych platformy Azure. Udostępnianie wykresów czasu ładowania i odpowiedzi oraz informacji o zależnościach oraz ustawianie alertów dotyczących wydajności.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: 0d9001834a33675dc4aab8d78282fb2987419ec3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649274"
---
# <a name="monitor-azure-web-app-performance"></a>Monitorowanie wydajności aplikacji internetowej platformy Azure
W witrynie [Azure Portal](https://portal.azure.com) możesz skonfigurować monitorowanie wydajności [aplikacji internetowych platformy Azure](../app-service/app-service-web-overview.md). [Usługa Azure Application Insights](app-insights-overview.md) umożliwia instrumentację aplikacji w celu wysyłania danych telemetrii do usługi Application Insights, gdzie są one przechowywane i analizowane. W usłudze tej można używać wykresów metryki i narzędzi wyszukiwania do ułatwiania diagnozowania problemów, zwiększania wydajności i oceny użycia.

## <a name="run-time-or-build-time"></a>W czasie wykonywania lub czasie kompilacji
Możesz skonfigurować monitorowanie, instrumentując aplikację na jeden z dwóch sposobów:

* **Czas wykonywania** — możesz wybrać rozszerzenie monitorowania wydajności, gdy aplikacja internetowa już działa. Nie trzeba ponownie kompilować ani instalować aplikacji. Uzyskujesz standardowy zestaw pakietów, które monitorują czasy odpowiedzi, współczynniki sukcesu, wyjątki, zależności itd. 
* **Czas kompilacji** — możesz zainstalować pakiet w aplikacji podczas programowania. Ta opcja jest bardziej wszechstronna. Oprócz tych samych pakietów standardowych możesz napisać kod w celu dostosowania telemetrii lub wysłania własnych danych telemetrii. Możesz rejestrować określone działania lub zdarzenia zgodnie z semantyką domeny aplikacji. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentacja w czasie wykonywania za pomocą usługi Application Insights
Jeśli już masz uruchomioną aplikację internetową na platformie Azure, otrzymujesz pewne informacje monitorowania: żądania i współczynniki błędów. Dodaj usługę Application Insights, aby uzyskać więcej informacji, na przykład czasy odpowiedzi, monitorowanie wywołań dla zależności, inteligentne wykrywanie i zaawansowany język zapytań usługi Log Analytics. 

1. **Wybierz usługę Application Insights** w Panelu sterowania platformy Azure dla aplikacji internetowej.
   
    ![W obszarze Monitorowanie wybierz pozycję Application Insights](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Wybierz opcję tworzenia nowego zasobu, chyba że zasób usługi Application Insights dla tej aplikacji został już skonfigurowany inną drogą.
2. **Zastosuj instrumentację aplikacji internetowej** po zainstalowaniu usługi Application Insights. 
   
    ![Instrumentacja aplikacji internetowej](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Włącz monitorowanie po stronie klienta** dla telemetrii widoku strony i użytkownika.

   * Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
   * W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość: 
   
    Klucz: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Wartość:`true`
   * **Zapisz** ustawienia i **ponownie uruchom** aplikację.
3. **Monitoruj aplikację**.  [Eksplorowanie danych](#explore-the-data).

W razie potrzeby możesz później utworzyć aplikację za pomocą usługi Application Insights.

*Jak usunąć usługę Application Insights lub przełączyć się na wysyłanie do innego zasobu?*

* Na platformie Azure otwórz blok sterowania aplikacji internetowej i w obszarze Narzędzia programistyczne otwórz pozycję **Rozszerzenia**. Usuń rozszerzenie usługi Application Insights. Następnie w obszarze Monitorowanie wybierz pozycję Application Insights i utwórz lub wybierz żądany zasób.

## <a name="build-the-app-with-application-insights"></a>Skompiluj aplikację za pomocą usługi Application Insights
Usługa Application Insights może zapewnić bardziej szczegółową telemetrię dzięki instalacji zestawu SDK w Twojej aplikacji. W szczególności możesz gromadzić dzienniki śledzenia, [zapisywać niestandardową telemetrię](app-insights-api-custom-events-metrics.md) i uzyskiwać bardziej szczegółowe raporty o wyjątkach.

1. **W programie Visual Studio** (2013 Update 2 lub nowszym) skonfiguruj usługę Application Insights dla projektu.

    Kliknij prawym przyciskiem myszy projekt sieci Web i wybierz pozycje **Dodaj > Application Insights** lub **Konfiguruj usługę Application Insights**.
   
    ![Kliknij prawym przyciskiem myszy projekt sieci Web i pozycję Dodaj lub Konfiguruj usługę Application Insights](./media/app-insights-azure-web-apps/03-add.png)
   
    Jeśli pojawi się prośba o zalogowanie, użyj poświadczeń konta platformy Azure.
   
    Operacja ma dwa skutki:
   
   1. Tworzy zasób usługi Application Insights na platformie Azure, gdzie telemetria jest przechowywana, analizowana i wyświetlana.
   2. Dodaje pakiet aplikacji NuGet usługi Application Insights do kodu (jeśli jeszcze go tam nie ma) i konfiguruje go do wysyłania telemetrii do zasobu platformy Azure.
2. **Przetestuj telemetrię**, uruchamiając aplikację na komputerze deweloperskim (F5).
3. **Opublikuj aplikację** na platformie Azure w zwykły sposób. 

*Jak przełączyć się na wysyłanie do innego zasobu usługi Application Insights?*

* W programie Visual Studio kliknij prawym przyciskiem myszy projekt, wybierz pozycję **Konfiguruj usługę Application Insights**, a następnie wybierz żądany zasób. Uzyskasz opcję tworzenia nowego zasobu. Ponownie skompiluj i wdróż.

## <a name="explore-the-data"></a>Eksplorowanie danych
1. W bloku usługi Application Insights panelu sterowania aplikacji internetowej pojawi się obszar Metryki na żywo, który pokazuje żądania i błędy po upływie sekundy lub dwóch od chwili ich wystąpienia. Jest to bardzo przydatny widok w przypadku ponownego publikowania aplikacji — natychmiast zobaczysz wszelkie problemy.
2. Kliknij elementy, aby przejść do pełnego zasobu usługi Application Insights.

    ![Kliknij elementy](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Możesz tam przejść również bezpośrednio z nawigacji zasobów platformy Azure.

1. Kliknij elementy któregokolwiek z wykresów, aby uzyskać więcej szczegółów:
   
    ![W bloku przeglądu usługi Application Insights kliknij wykres](./media/app-insights-azure-web-apps/07-dependency.png)
   
    Możesz [dostosować bloki metryk](app-insights-metrics-explorer.md).
2. Klikaj dalej elementy, aby zobaczyć poszczególne zdarzenia i ich właściwości:
   
    ![Kliknij typ zdarzenia, aby otworzyć wyszukiwanie filtrowane według danego typu](./media/app-insights-azure-web-apps/08-requests.png)
   
    Zwróć uwagę na link „...”, aby otworzyć wszystkie właściwości.
   
    Możesz [dostosować wyszukiwania](app-insights-diagnostic-search.md).

Aby skorzystać z bardziej zaawansowanego wyszukiwania w ramach telemetrii, użyj [języka zapytań usługi Log Analytics](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii

* [Dane ładowania strony sieci Web](app-insights-javascript.md)
* [Telemetria niestandardowa](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Kolejne kroki
* [Uruchom profilera aplikacji na żywo](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) — monitorowanie usługi Azure Functions za pomocą usługi Application Insights
* [Włącz diagnostykę platformy Azure](app-insights-azure-diagnostics.md), która ma być wysyłana do usługi Application Insights.
* [Monitoruj metryki kondycji usługi](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md), aby upewnić się, że usługa jest dostępna i szybko reaguje.
* [Odbieraj powiadomienia o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) zawsze, gdy wystąpią zdarzenia operacyjne lub metryki przekroczą próg.
* Użyj pozycji [Usługa Application Insights dla aplikacji JavaScript i stron sieci Web](app-insights-javascript.md), aby pobrać telemetrię klienta z przeglądarek, w których odwiedzono stronę sieci Web.
* [Konfiguruj testy sieci Web dostępności](app-insights-monitor-web-app-availability.md), aby otrzymywać alerty, gdy witryna nie działa.

