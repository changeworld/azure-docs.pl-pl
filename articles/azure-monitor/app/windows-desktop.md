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
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 95ff8d1a70325357fee4bc24fd96c1a1c7a73845
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371482"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorowanie użycia i wydajności klasycznych aplikacji systemu Windows

Wszystkie aplikacje hostowane lokalnie, na platformie Azure i w innych chmurach mogą korzystać z możliwości usługi Application Insights. Jedynym ograniczeniem jest konieczność [zezwolenia na komunikację](../../azure-monitor/app/ip-addresses.md) z usługą Application Insights. Do monitorowania aplikacji platformy uniwersalnej systemu Windows zalecamy używanie pakietu [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Aby wysłać dane telemetryczne do usługi Application Insights z klasycznej aplikacji systemu Windows
1. W witrynie [Azure Portal](https://portal.azure.com) [utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). Jako typ aplikacji wybierz ASP.NET.
2. Wykonaj kopię klucza instrumentacji. Klucz można znaleźć na liście rozwijanej Podstawy dla nowego zasobu, który właśnie został utworzony. 
3. W programie Visual Studio edytuj pakiety NuGet projektu aplikacji, a następnie dodaj interfejs Microsoft.ApplicationInsights.WindowsServer. (Lub wybierz interfejs Microsoft.ApplicationInsights, jeśli potrzebny jest tylko podstawowy interfejs API bez standardowych modułów gromadzenia telemetrii).
4. Ustaw klucz instrumentacji w kodzie:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *Twój klucz* `";`
   
    albo w pliku ApplicationInsights.config (jeśli został zainstalowany jeden ze standardowych pakietów telemetrii):
   
    `<InstrumentationKey>`*Twój klucz*`</InstrumentationKey>` 
   
    Jeśli używasz pliku ApplicationInsights.config, upewnij się, że jego właściwości w Eksploratorze rozwiązań zostały ustawione na **Akcja kompilacji = Zawartość, Kopiuj do katalogu wyjściowego = Kopiuj**.
5. [Użyj interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md), aby wysłać telemetrię.
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

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie pulpitu nawigacyjnego](../../azure-monitor/app/app-insights-dashboards.md)
* [Wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

