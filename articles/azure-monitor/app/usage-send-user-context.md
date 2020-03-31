---
title: Identyfikatory kontekstu użytkownika do śledzenia aktywności — usługa Azure Application Insights
description: Śledź, jak użytkownicy poruszają się po usłudze, przypisując każdemu z nich unikatowy, trwały ciąg identyfikatorów w usłudze Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: beb5a0f7ad3733aaf12b0880af4fba23a705a7e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670937"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Wysyłanie identyfikatorów kontekstu użytkownika w celu umożliwienia korzystania z usługi Azure Application Insights

## <a name="tracking-users"></a>Śledzenie użytkowników

Usługa Application Insights umożliwia monitorowanie i śledzenie użytkowników za pomocą zestawu narzędzi użycia produktu:

- [Użytkownicy, sesje, zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Lejki](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Retencja](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) Kohorty
- [Skoroszyty](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Aby śledzić, co użytkownik robi w czasie, usługa Application Insights potrzebuje identyfikatora dla każdego użytkownika lub sesji. Uwzględnij następujące identyfikatory w każdym niestandardowym zdarzeniu lub widoku strony.

- Użytkownicy, ścieżki, retencji i kohorty: uwzględnij identyfikator użytkownika.
- Sesje: uwzględnij identyfikator sesji.

> [!NOTE]
> Jest to zaawansowany artykuł przedstawiający ręczne kroki śledzenia aktywności użytkownika za pomocą usługi Application Insights. W wielu aplikacjach internetowych **te kroki mogą nie być wymagane,** ponieważ domyślne sdk po stronie serwera w połączeniu z [SDK JavaScript po stronie klienta/przeglądarki](../../azure-monitor/app/website-monitoring.md )często wystarczą do automatycznego śledzenia aktywności użytkowników. Jeśli oprócz zestawu SDK po stronie serwera nie skonfigurowano [monitorowania po stronie klienta,](../../azure-monitor/app/website-monitoring.md ) wykonaj to najpierw i sprawdź, czy narzędzia do analizy zachowań użytkowników działają zgodnie z oczekiwaniami.

## <a name="choosing-user-ids"></a>Wybieranie identyfikatorów użytkowników

Identyfikatory użytkowników powinny zachowywać się w sesjach użytkowników, aby śledzić zachowanie użytkowników w czasie. Istnieją różne podejścia do utrwalania identyfikatora.

- Definicja użytkownika, który masz już w usłudze.
- Jeśli usługa ma dostęp do przeglądarki, może przekazać przeglądarkę pliki cookie z identyfikatorem w nim. Identyfikator będzie się powtarzał tak długo, jak długo plik cookie pozostanie w przeglądarce użytkownika.
- W razie potrzeby można użyć nowego identyfikatora każdej sesji, ale wyniki dotyczące użytkowników będą ograniczone. Na przykład nie będzie można zobaczyć, jak zachowanie użytkownika zmienia się w czasie.

Identyfikator powinien być Guid lub inny kompleks ciągu wystarczy, aby zidentyfikować każdego użytkownika w unikatowy sposób. Na przykład może to być długa liczba losowa.

Jeśli identyfikator zawiera informacje umożliwiające identyfikację użytkownika, nie jest odpowiednią wartością do wysyłania do usługi Application Insights jako identyfikator użytkownika. Można wysłać taki identyfikator jako [uwierzytelniony identyfikator użytkownika,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users)ale nie spełnia wymagań dotyczących identyfikatora użytkownika dla scenariuszy użycia.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET aplikacji: Ustawianie kontekstu użytkownika w itelemetryInitializer

Utwórz inicjatora telemetrii, jak opisano szczegółowo [w tym miejscu](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer). Przekaż identyfikator sesji za pośrednictwem telemetrii żądania i ustaw Context.User.Id i Context.Session.Id.

W tym przykładzie ustawia identyfikator użytkownika na identyfikator, który wygasa po sesji. Jeśli to możliwe, należy użyć identyfikatora użytkownika, który będzie się powtarzał w sesjach.

### <a name="telemetry-initializer"></a>Inicjator telemetrii

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowisko użycia, zacznij wysyłać [niestandardowe zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [widoki strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli wysyłasz już niestandardowe zdarzenia lub widoki stron, zapoznaj się z narzędziami Użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Omówienie użycia](usage-overview.md)
    - [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](usage-retention.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
