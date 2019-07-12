---
title: Jak to zrobić w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Często zadawane pytania w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 9f80edf18a531d6c2850658ddef9c7007edb350f
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795525"
---
# <a name="how-do-i--in-application-insights"></a>Jak mogę (...) w usłudze Application Insights?
## <a name="get-an-email-when-"></a>Otrzymaj wiadomość e-mail po...
### <a name="email-if-my-site-goes-down"></a>Wiadomość e-mail, jeśli Moja witryna ulegnie awarii
Ustaw [dostępności testu sieci web](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>Wyślij wiadomość e-mail, jeśli Moja witryna jest przeciążony.
Ustaw [alert](../../azure-monitor/app/alerts.md) na **czas odpowiedzi serwera**. Próg od 1 do 2 sekund powinny działać.

![](./media/how-do-i/030-server.png)

Aplikacja może również wyświetlać podpisuje obciążenie zwracanie kodów błędów. Ustawianie alertu na **żądań zakończonych niepowodzeniem**.

Jeśli chcesz ustawić alert na **wyjątki serwera**, być może trzeba [pewne dodatkowe ustawienia](../../azure-monitor/app/asp-net-exceptions.md) w celu wyświetlenia danych.

### <a name="email-on-exceptions"></a>Wiadomość e-mail dotycząca wyjątków
1. [Konfigurowanie monitorowania wyjątku bez agenta](../../azure-monitor/app/asp-net-exceptions.md)
2. [Ustawianie alertu](../../azure-monitor/app/alerts.md) w drodze wyjątku liczba metryki

### <a name="email-on-an-event-in-my-app"></a>Wiadomość e-mail dotycząca zdarzenia w mojej aplikacji
Załóżmy, że chcesz otrzymaj wiadomość e-mail po wystąpieniu określonego zdarzenia. Usługa Application Insights nie zapewnia tej funkcji bezpośrednio, ale jest to możliwe [Wyślij alert, gdy Metryka przekracza próg](../../azure-monitor/app/alerts.md).

Alerty można ustawić na [metryki niestandardowe](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), ale nie niestandardowych zdarzeń. Pisanie kodu w celu zwiększenia metrykę, gdy wystąpi zdarzenie:

    telemetry.TrackMetric("Alarm", 10);

lub:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Ponieważ alerty mają dwa stany, konieczne będzie wysyłać niską wartość, gdy należy wziąć pod uwagę alert, aby zostać zakończone:

    telemetry.TrackMetric("Alarm", 0.5);

Tworzenie wykresu w [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) Aby wyświetlić swoje alarmu:

![](./media/how-do-i/010-alarm.png)

Teraz można ustawić alert wyzwolenie, gdy Metryka przekroczy wartość mid przez krótki okres:

![](./media/how-do-i/020-threshold.png)

Ustaw okres obliczania średniej do minimum.

Będziesz otrzymywać wiadomości e-mail, gdy Metryka przekroczy i niższy od wartości progowej.

Oto niektóre ważne kwestie:

* Alert ma dwa stany ("alert" i "w dobrej kondycji"). Stan jest oceniane tylko wtedy, gdy Metryka została odebrana.
* Tylko wtedy, gdy zmieni się stan zostanie wysłana wiadomość e-mail. Jest to, dlaczego należy wysyłać wysokiej i niskiej wartości metryk.
* Aby ocenić ten alert, średnia jest zajęta odebranej wartości w poprzednim okresie. Dzieje się za każdym razem, gdy Metryka zostanie odebrana, dzięki czemu częściej niż okres, w którym można ustawić mogą być wysyłane wiadomości e-mail.
* Ponieważ wiadomości e-mail są wysyłane zarówno na "alert" i "w dobrej kondycji", można rozważyć ponowne myśl jednorazowej zdarzenia jako warunek dwoma stanami. Na przykład zamiast "zadanie ukończone" zdarzenia, mają warunek "zadanie w toku", gdzie otrzymywać wiadomości e-mail na początku i końcu zadania.

### <a name="set-up-alerts-automatically"></a>Automatyczne konfigurowanie alertów
[Tworzenie nowych alertów za pomocą programu PowerShell](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Za pomocą programu PowerShell do zarządzania usługi Application Insights
* [Tworzenie nowych zasobów](../../azure-monitor/app/powershell-script-create-resource.md)
* [Tworzenie nowych alertów](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Osobne dane telemetryczne z różnych wersji

* Wiele ról w aplikacji: Użyj pojedynczego zasobu usługi Application Insights i przefiltruj [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Oddzielanie programowania, testowania i wersji: Używać różnych zasobów usługi Application Insights. Wybierz kluczy Instrumentacji z pliku web.config. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)
* Raportowanie wersji kompilacji: Dodaj właściwość za pomocą inicjatora telemetrii. [Dowiedz się więcej](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Monitorowanie serwerów wewnętrznej bazy danych i aplikacji klasycznych
[Użyć modułu programu Windows Server SDK](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Wizualizowanie danych
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Pulpit nawigacyjny z metrykami z wieloma aplikacjami
* W [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md), dostosować wykres i zapisz go jako ulubione. Przypnij go do pulpitu nawigacyjnego platformy Azure.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Pulpit nawigacyjny z danymi z innych źródeł i usługi Application Insights
* [Eksportowanie telemetrii do usługi Power BI](../../azure-monitor/app/export-power-bi.md ).

Lub

* Korzystaj z programu SharePoint jako pulpit nawigacyjny, wyświetlanie danych w składnikach web Part programu SharePoint. [Eksportowanie do bazy danych SQL za pomocą Eksport ciągły i Stream Analytics](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Użyj PowerView, aby sprawdzić bazy danych i Utwórz składnik web part programu SharePoint dla PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrowanie anonimowe lub uwierzytelnionych użytkowników
Jeśli użytkownicy logują się, możesz ustawić [uwierzytelniony identyfikator użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Go nie jest realizowane automatycznie.)

Następnie możesz:

* Wyszukiwanie identyfikatorów określonego użytkownika

![](./media/how-do-i/110-search.png)

* Filtruj metryki do anonimowych lub uwierzytelnionych użytkowników

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Modyfikowanie właściwości nazwy lub wartości
Tworzenie [filtru](../../azure-monitor/app/api-filtering-sampling.md#filtering). Dzięki temu można zmodyfikować lub filtrowanie danych telemetrycznych przed wysłaniem go z aplikacji do usługi Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista konkretnych użytkowników i ich użycia
Jeśli chcesz, aby [wyszukiwanie określonych użytkowników](#search-specific-users), można ustawić [uwierzytelniony identyfikator użytkownika](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Jeśli chcesz, aby listę użytkowników z danych, takich jak strony przeglądania lub jak często logowania, masz dwie opcje:

* [Identyfikator użytkownika uwierzytelnionego zestawu](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [eksportowanie do bazy danych](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) i korzystać z odpowiednich narzędzi do analizowania danych użytkownika.
* Jeśli masz tylko niewielka liczba użytkowników, wysyłać zdarzenia niestandardowe lub metryk, za pomocą interesujących danych jako nazwa metryki wartość lub zdarzeń i ustawianie identyfikator użytkownika jako właściwość. Aby analizować wyświetleń stron, Zastąp standardowe wywołanie wywołania trackPageView języka JavaScript. Aby analizować dane telemetryczne po stronie serwera, Dodaj identyfikator użytkownika do wszystkich danych telemetrycznych z serwera za pomocą inicjatora telemetrii. Możesz następnie filtrowania i dzielenia metryki i wyszukiwania identyfikatora użytkownika.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Zmniejszyć ruch z mojej aplikacji do usługi Application Insights
* W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), wyłącz wszystkie moduły nie jest konieczne, taki moduł zbierający licznika wydajności.
* Użyj [próbkowanie i filtrowanie](../../azure-monitor/app/api-filtering-sampling.md) w zestawie SDK.
* Na stronach sieci web należy ograniczyć liczbę tych wywołań Ajax dla każdego widoku strony. Fragment kodu skryptu po `instrumentationKey:...` , Wstaw: `,maxAjaxCallsPerView:3` (lub odpowiednia ilość).
* Jeśli używasz [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), obliczenia agregacji partie wartości metryk przed wysłaniem wynik. Przeciążenia funkcji TrackMetric(), która oferuje funkcje, nie istnieje.

Dowiedz się więcej o [cenami i limitami przydziału](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Wyłączanie telemetrii
Aby **dynamicznie zatrzymywania i uruchamiania** zbierania i przekazywania danych telemetrycznych z serwera:

### <a name="aspnet-classic-applications"></a>Aplikacje klasyczne platformy ASP.NET

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Inne aplikacje
Nie zaleca się używania `TelemetryConfiguration.Active` singleton w konsoli lub aplikacje platformy ASP.NET Core.
Jeśli utworzono `TelemetryConfiguration` wystąpienia — ustaw `DisableTelemetry` do `true`.

W przypadku aplikacji platformy ASP.NET Core mogą uzyskiwać dostęp do `TelemetryConfiguration` przy użyciu [wstrzykiwanie zależności platformy ASP.NET Core](/aspnet/core/fundamentals/dependency-injection/). Możesz znaleźć więcej szczegółów w [dotycząca usługi Application Insights dla aplikacji platformy ASP.NET Core](../../azure-monitor/app/asp-net-core.md) artykułu.

## <a name="disable-selected-standard-collectors"></a>Wyłącz wybrane standardowych modułów zbierających dzienniki
Można wyłączyć standardowych modułów zbierających dzienniki (na przykład liczniki wydajności, żądania HTTP lub zależności)

* **Aplikacje ASP.NET** — Usuń lub komentarz odpowiednie wiersze w [pliku ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **Aplikacje platformy ASP.NET Core** -postępuj zgodnie z opcji konfiguracji modułów danych telemetrycznych w [dotycząca usługi Application Insights platformy ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Liczniki wydajności sytemu widoku
Wśród metryk, które można wyświetlić w Eksploratorze metryk to zbiór systemu liczników wydajności. Ma wstępnie zdefiniowanych blok pod tytułem **serwerów** wyświetlającą kilka z nich.

![Otwórz zasób usługi Application Insights, a następnie kliknij pozycję serwery](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Jeśli są wyświetlane żadne dane licznika wydajności
* **Serwer IIS** na własnym komputerze lub na maszynie Wirtualnej. [Zainstaluj Monitor stanu](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Witryny sieci web platformy Azure** — nie obsługujemy jeszcze liczników wydajności. Istnieje kilka metryk, które można uzyskać w ramach standardowego witrynie internetowej platformy Azure w Panelu sterowania.
* **Serwer UNIX** - [zainstalować zebrane](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Aby wyświetlić więcej liczników wydajności
* Po pierwsze, [Dodaj nowy wykres](../../azure-monitor/app/metrics-explorer.md) i sprawdzić, czy licznik jest do zestawu, firma Microsoft oferuje.
* W przeciwnym razie [dodać licznika do zestawu, który został zebrany przez moduł licznika wydajności](../../azure-monitor/app/performance-counters.md).
