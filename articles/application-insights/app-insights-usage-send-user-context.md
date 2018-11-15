---
title: Wyślij kontekstu użytkownika identyfikatorów, aby umożliwić użycie środowisk w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Śledzić, jak użytkownik przejdzie przez usługę, przypisując każdego z nich unikatowy, trwały ciąg Identyfikatora w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/02/2017
ms.reviewer: abgreg;mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 5a224d7a78e35776b36b162228f6ba4c72677069
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636207"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Wyślij kontekstu użytkownika identyfikatorów, aby zapewnić użycie w usłudze Azure Application Insights

## <a name="tracking-users"></a>Śledzenie użytkowników

Usługa Application Insights umożliwia monitorowanie i śledzenie użytkowników za pomocą zestawu narzędzi do użycia produktu:

- [Użytkownicy, sesje, zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
- [Lejki](https://docs.microsoft.com/azure/application-insights/usage-funnels)
- [Przechowywanie](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention) kohorty
- [Skoroszyty](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Aby móc śledzić zmiany w czasie działania użytkownika, usługi Application Insights wymaga Identyfikatora dla każdego użytkownika lub sesję. Uwzględnij następujące identyfikatory w każdej niestandardowe zdarzenie lub wyświetlenie strony.

- Użytkownicy, Lejki, przechowywania i kohorty: obejmują identyfikator użytkownika.
- Sesje: Obejmują identyfikator sesji.

Jeśli aplikacja jest zintegrowana z usługą [zestaw JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), są automatycznie śledzone Identyfikatora użytkownika.

## <a name="choosing-user-ids"></a>Wybieranie nazw użytkowników

Identyfikatory użytkowników ma utrwalić między sesjami użytkownika, aby śledzić zachowanie użytkowników wraz z upływem czasu. Istnieją różne podejścia do utrwalania identyfikatora.

- Definicja użytkownika, która już istnieje w usłudze.
- Jeśli usługa ma dostęp do przeglądarki, jego można przekazać przeglądarki pliku cookie z Identyfikatorem w nim. Identyfikator będzie utrzymywać się WE, tak długo, jak plik cookie pozostanie w przeglądarce użytkownika.
- Jeśli to konieczne, nowego Identyfikatora można użyć w każdej sesji, ale wyniki dotyczące użytkowników będzie ograniczona. Na przykład nie będzie można zobaczyć, jak zmienia zachowanie użytkownika wraz z upływem czasu.

Identyfikator powinien być identyfikatorem Guid lub inny ciąg dostatecznie złożone i jednoznacznie zidentyfikować każdego użytkownika. Na przykład można długo losową liczbę.

Jeśli identyfikator zawiera identyfikujących informacje o użytkowniku, nie jest odpowiednia wartość do wysłania do usługi Application Insights jako identyfikator użytkownika. Możesz wysłać identyfikator jako [uwierzytelniony identyfikator użytkownika](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), ale nie spełnia wymagań identyfikator użytkownika dla scenariuszy użycia.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplikacje ASP.NET: Ustawianie kontekstu użytkownika w ITelemetryInitializer

Utwórz inicjatora telemetrii w sposób opisany szczegółowo [tutaj](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer). Przekaż identyfikator sesji za pośrednictwem danych telemetrycznych żądań i ustaw Context.User.Id i Context.Session.Id.

W tym przykładzie Ustawia identyfikator użytkownika identyfikator, który wygasa po sesji. Jeśli to możliwe Użyj Identyfikatora użytkownika, która utrwala między sesjami.

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

## <a name="next-steps"></a>Kolejne kroki

- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Przegląd wykorzystania](app-insights-usage-overview.md)
    - [Użytkownicy, sesje i zdarzenia](app-insights-usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [Skoroszyty](app-insights-usage-workbooks.md)
