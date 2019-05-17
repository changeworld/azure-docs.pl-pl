---
title: Konfigurowanie analizy aplikacji internetowej w technologii ASP.NET za pomocą usługi Azure Application Insights | Microsoft Docs
description: Konfigurowanie wydajności, dostępności i narzędzia do analizy zachowania użytkowników dla witryny sieci Web ASP.NET hostowanej lokalnie lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: c69e925913d4dcb3a8bec34cbf25dac184ea65c5
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595559"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Konfigurowanie usługi Application Insights dla witryny sieci Web ASP.NET.

Niniejsza procedura umożliwia skonfigurowanie aplikacji internetowej w technologii ASP.NET do wysyłania danych telemetrii do usługi [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Działa to w przypadku aplikacji w technologii ASP.NET, które są hostowane na serwerze usług IIS lokalnie albo w chmurze. Uzyskasz wykresy i zaawansowany język zapytań, które pomogą Ci zrozumieć wydajność aplikacji i sposób korzystania z niej przez użytkowników oraz dodatkowo automatyczne alerty w razie błędów lub problemów z wydajnością. Wielu programistów uważa, że te funkcje są doskonałe takie, jakie są, ale możesz również w razie potrzeby rozszerzyć i dostosować dane telemetryczne.

Konfiguracja wymaga tylko kilku kliknięć w programie Visual Studio. Masz możliwość uniknąć naliczania opłat, ograniczając ilość danych telemetrii. Ta funkcja pozwala to wypróbowanie i debugowanie lub monitorowanie witryny, która nie ma zbyt wielu użytkownikom. Gdy zdecydujesz, że chcesz kontynuować i monitorować witrynę produkcyjną, późniejsze zwiększenie limitu jest łatwe.

## <a name="prerequisites"></a>Wymagania wstępne
Aby dodać usługę Application Insights do witryny internetowej:

- Zainstaluj [Visual Studio 2019 r dla Windows](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
    - ASP.NET i tworzenie aplikacji internetowych (nie Usuń zaznaczenie pola wyboru składniki opcjonalne)
    - Programowanie na platformie Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="ide"></a> Krok 1: Dodawanie zestawu SDK usługi Application Insights

> [!IMPORTANT]
> Zrzuty ekranu, w tym przykładzie są oparte na programie Visual Studio 2017 w wersji 15.9.9 lub nowszy. Środowisko Aby dodać usługę Application Insights różni się w różnych wersjach programu Visual Studio, a także typu szablonu platformy ASP.NET. Starsze wersje mogą mieć tekst alternatywny, taki jak "Konfiguruj usługę Application Insights".

Kliknij prawym przyciskiem myszy nazwę aplikacji sieci web w Eksploratorze rozwiązań, a następnie wybierz **Dodaj** > **Telemetria usługi Application Insights**

![Zrzut ekranu Eksploratora rozwiązań z wyróżnioną opcją Konfiguruj usługę Application Insights](./media/asp-net/add-telemetry-new.png)

(W zależności od używanej wersji zestawu SDK usługi Application Insights może pojawić się monit o przeprowadzenie uaktualnienia do najnowszej wersji zestawu SDK. Po wyświetleniu takiego monitu wybierz pozycję **Zaktualizuj zestaw SDK**).

![Zrzut ekranu: Dostępna jest nowa wersja zestawu SDK usługi Microsoft Application Insights. Wyróżniona pozycja Zaktualizuj zestaw SDK](./media/asp-net/0002-update-sdk.png)

Ekran konfiguracji usługi Application Insights:

Wybierz **wprowadzenie**.

![Zrzut ekranu strony Zarejestruj swoją aplikację w usłudze Application Insights](./media/asp-net/00004-start-free.png)

Jeśli chcesz ustawić grupę zasobów lub lokalizację, w której dane są przechowywane, kliknij pozycję **Konfiguruj ustawienia**. Grupy zasobów są używane do kontrolowania dostępu do danych. Jeśli na przykład masz kilka aplikacji, które stanowią część tego samego systemu, możesz umieścić ich dane usługi Application Insights w tej samej grupie zasobów.

 Wybierz pozycję **Zarejestruj**.

![Zrzut ekranu strony Zarejestruj swoją aplikację w usłudze Application Insights](./media/asp-net/00005-register-ed.png)

 Dane telemetryczne będą wysyłane do witryny [Azure Portal](https://portal.azure.com) zarówno podczas debugowania, jak i po opublikowaniu aplikacji.
> [!NOTE]
> Jeśli nie chcesz wysłać danych telemetrii do portalu podczas debugowania, wystarczy, że dodasz zestaw SDK usługi Application Insights do aplikacji, ale nie konfiguruj zasobu w portalu. Dane telemetryczne możesz wyświetlać w programie Visual Studio podczas debugowania. Później możesz powrócić do tej strony konfiguracji lub po wdrożeniu aplikacji [włączyć telemetrię w czasie wykonywania](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a> Krok 2: Uruchamianie aplikacji
Uruchom aplikację, naciskając klawisz F5. Otwórz różne strony w celu wygenerowania telemetrii.

W programie Visual Studio zobaczysz liczbę zarejestrowanych zdarzeń.

![Zrzut ekranu programu Visual Studio. Przycisk usługi Application Insights jest widoczny podczas debugowania.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Krok 3: Wyświetlanie telemetrii
Telemetrię można wyświetlić w programie Visual Studio lub w portalu sieci Web usługi Application Insights. Wyszukaj telemetrię w programie Visual Studio, aby ułatwić debugowanie własnej aplikacji. Monitoruj wydajność i użycie w portalu sieci Web, gdy system działa. 

### <a name="see-your-telemetry-in-visual-studio"></a>Wyświetlanie telemetrii w programie Visual Studio

Wyświetlanie danych usługi Application Insights w programie Visual Studio.  Wybierz pozycje **Eksplorator rozwiązań** > **Usługi połączone**, kliknij prawym przyciskiem myszy pozycję **Application Insights**, a następnie kliknij pozycję **Wyszukaj telemetrię na żywo**.

W oknie wyszukiwania usługi Visual Studio Application Insights zobaczysz dane ze swojej aplikacji dla telemetrii wygenerowanej po stronie serwera aplikacji. Poeksperymentuj z filtrami, a następnie kliknij dowolne zdarzenie, aby wyświetlić więcej szczegółów.

![Zrzut ekranu przedstawiający widok Dane z sesji debugowania w oknie Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Jeśli nie są wyświetlane żadne dane, upewnij się, że zakres czasu jest poprawny, a następnie kliknij ikonę wyszukiwania.

[Dowiedz się więcej o narzędziach usługi Application Insights w programie Visual Studio](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Wyświetlanie telemetrii w portalu sieci Web

Telemetrię można również wyświetlić w portalu sieci Web usługi Application Insights (chyba że wybrano opcję zainstalowania tylko zestawu SDK). Portal udostępnia więcej wykresów, narzędzi analitycznych i widoków międzyskładnikowych niż program Visual Studio. Portal oferuje również alerty.

Otwórz zasób usługi Application Insights. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) i znajdź telemetrię tam albo wybierz pozycję **Eksplorator rozwiązań** > **Usługi połączone**, kliknij prawym przyciskiem myszy pozycję **Application Insights** > **Otwórz portal usługi Application Insights** i zaczekaj na otworzenie portalu.

W portalu zostanie otwarty widok danych telemetrycznych z Twojej aplikacji.

![Zrzut ekranu przedstawiający stronę przeglądu usługi Application Insights](./media/asp-net/007.png)

W portalu kliknij dowolny kafelek lub wykres, aby wyświetlić więcej szczegółów.

[Dowiedz się więcej o korzystaniu z usługi Application Insights w portalu Azure](../../azure-monitor/app/app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Krok 4: Publikowanie aplikacji
Opublikuj aplikacje na serwerze IIS lub na platformie Azure. Obejrzyj [transmisję strumieniową metryk na żywo](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream), aby upewnić się, że wszystko działa bez problemów.

Dane telemetryczne są gromadzone w portalu Application Insights, w którym można monitorować metryki, przeszukiwać telemetrię i konfigurować [pulpity nawigacyjne](../../azure-monitor/app/app-insights-dashboards.md). Można także użyć zaawansowanego [język zapytania Kusto](/azure/kusto/query/) do przeanalizowania użycia i wydajności lub znalezienia określonych zdarzeń.

Można również analizować telemetrię w programie [Visual Studio](../../azure-monitor/app/visual-studio.md) za pomocą narzędzi, takich jak wyszukiwanie diagnostyczne i [trendy](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Jeśli Twoja aplikacja wysyła taką ilość telemetrii, że bliskie jest osiągnięcie [limitów ograniczania przepustowości](../../azure-monitor/app/pricing.md#limits-summary), włączone zostanie [próbkowanie](../../azure-monitor/app/sampling.md) automatyczne. Próbkowania powoduje zmniejszenie ilości telemetrii wysyłanych z aplikacji przy jednoczesnym zachowaniu skorelowanych danych w celach diagnostycznych.
>
>

## <a name="land"></a> Wszystko jest gotowe

Gratulacje! Udało Ci się zainstalować pakiet usługi Application Insights w swojej aplikacji i skonfigurować go do wysłania danych telemetrii do usługi Application Insights na platformie Azure.

Zasób platformy Azure, który otrzymuje dane telemetryczne z Twojej aplikacji, jest identyfikowany przez *klucz instrumentacji*. Ten klucz znajduje się w pliku ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Uaktualnianie do przyszłych wersji zestawu SDK
Aby przeprowadzić uaktualnienie do [nowej wersji zestawu SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), otwórz **menedżera pakietów NuGet** i przefiltruj zainstalowane pakiety. Wybierz **Microsoft.ApplicationInsights.Web** i kliknij pozycję **Uaktualnij**.

Jeśli plik ApplicationInsights.config został dostosowany, zapisz jego kopię przed uaktualnieniem. Następnie scal zmiany w nowej wersji.

## <a name="video"></a>Wideo

* Zewnętrzne wideo krok po kroku dotyczące [Konfigurowanie usługi Application Insights za pomocą aplikacji platformy .NET od podstaw](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>Kolejne kroki

Istnieją jeszcze inne tematy, które warto przejrzeć, jeśli interesują Cię następujące kwestie:

* [Instrumentacja aplikacji internetowej w czasie wykonywania](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>Dalsze funkcje telemetrii

* **[Przeglądarka i dane ładowania strony](../../azure-monitor/app/javascript.md)** — wstawianie fragmentu kodu na stronach sieci Web.
* **[Korzystanie z bardziej szczegółowego monitorowania zależności i wyjątków](../../azure-monitor/app/monitor-performance-live-website-now.md)** — instalowanie monitora stanu na własnym serwerze.
* **[Kodowanie zdarzeń niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md)** w celu liczenia lub mierzenia akcji użytkownika bądź sprawdzania ich czasu.
* **[Pobieranie danych dziennika](../../azure-monitor/app/asp-net-trace-logs.md)** — korelowanie danych dziennika z danymi telemetrycznymi.

### <a name="analysis"></a>Analiza

* **[Praca z usługą Application Insights w programie Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Zawiera informacje o debugowaniu przy użyciu telemetrii, wyszukiwaniu diagnostycznym i przechodzeniu do szczegółów kodu.
* **[Praca z portalem usługi Application Insights](../../azure-monitor/app/app-insights-dashboards.md)**<br/> Zawiera informacje o pulpitach nawigacyjnych, zaawansowanych narzędziach diagnostycznych i analitycznych, alertach, mapie zależności aplikacji na żywo oraz eksportowaniu telemetrii.
* **[Analiza](../../azure-monitor/log-query/get-started-portal.md)** — zaawansowany język zapytań.

### <a name="alerts"></a>Alerty

* [Testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md): Utwórz testy, aby upewnić się, że Twoja witryna jest widoczna w sieci web.
* [Inteligentne diagnostyki](../../azure-monitor/app/proactive-diagnostics.md): Te testy są uruchamiane automatycznie, dzięki czemu nie trzeba nic robić, aby je skonfigurować. Ta funkcja powiadomi Cię, jeśli w aplikacji występuje nietypowa liczba nieudanych żądań.
* [Alerty metryki](../../azure-monitor/app/alerts.md): Ustawianie alertów w celu otrzymania ostrzeżenia, gdy Metryka przekroczy próg. Możesz je ustawić dla metryk niestandardowych, które zakodujesz w aplikacji.

### <a name="automation"></a>Automatyzacja

* [Automatyczne tworzenie zasobu usługi Application Insights](../../azure-monitor/app/powershell.md)
