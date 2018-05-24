---
title: Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows
description: Analizowanie użycia i wydajności klasycznej aplikacji systemu Windows za pomocą usługi Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/16/2018
ms.author: mbullwin
ms.openlocfilehash: 5b325fd4326f2594a7386c65dea17a3da19abde8
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows

Chociaż telemetria może być wysyłana do usługi Application Insights z klasycznej aplikacji, jest to głównie przydatne podczas debugowania i do celów doświadczalnych.

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Aby wysłać dane telemetryczne do usługi Application Insights z aplikacji systemu Windows
1. W witrynie [Azure Portal](https://portal.azure.com) [utwórz zasób usługi Application Insights](app-insights-create-new-resource.md). Jako typ aplikacji wybierz ASP.NET.
2. Wykonaj kopię klucza instrumentacji. Klucz można znaleźć na liście rozwijanej Podstawy dla nowego zasobu, który właśnie został utworzony. 
3. W programie Visual Studio edytuj pakiety NuGet projektu aplikacji, a następnie dodaj interfejs Microsoft.ApplicationInsights.WindowsServer. (Lub wybierz interfejs Microsoft.ApplicationInsights, jeśli potrzebny jest tylko podstawowy interfejs API bez standardowych modułów gromadzenia telemetrii).
4. Ustaw klucz instrumentacji w kodzie:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *Twój klucz* `";` 
   
    albo w pliku ApplicationInsights.config (jeśli został zainstalowany jeden ze standardowych pakietów telemetrii):
   
    `<InstrumentationKey>`*Twój klucz*`</InstrumentationKey>` 
   
    Jeśli używasz pliku ApplicationInsights.config, upewnij się, że jego właściwości w Eksploratorze rozwiązań zostały ustawione na **Akcja kompilacji = Zawartość, Kopiuj do katalogu wyjściowego = Kopiuj**.
5. [Użyj interfejsu API](app-insights-api-custom-events-metrics.md), aby wysłać telemetrię.
6. Uruchom aplikację i wyświetl dane telemetryczne w zasobie utworzonym w witrynie Azure Portal.

## <a name="telemetry"></a>Przykładowy kod
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pulpitu nawigacyjnego](app-insights-dashboards.md)
* [Wyszukiwanie diagnostyczne](app-insights-diagnostic-search.md)
* [Eksplorowanie metryk](app-insights-metrics-explorer.md)
* [Pisanie zapytań analitycznych](app-insights-analytics.md)

