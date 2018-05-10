---
title: Wyślij napotyka kontekstu użytkownika identyfikatory, aby umożliwić użycie w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Śledzenie, jak przenieść użytkowników za pośrednictwem usługi przez przypisanie każdego z nich unikatowe, trwałe ciąg Identyfikatora w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin;abgreg
ms.openlocfilehash: 196eeb7b5a817ff932f99c7db86ead5625b5f206
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Wyślij kontekstu użytkownika identyfikatory, aby umożliwić korzystanie z użycia w usłudze Azure Application Insights

## <a name="tracking-users"></a>Śledzenie użytkowników

Usługa Application Insights umożliwia monitorowanie i śledzenie użytkowników za pomocą zestawu narzędzi użycia produktu: 
* [Użytkownicy, sesje, zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Lejki](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Przechowywanie](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Kohorty
* [Skoroszyty](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Aby móc śledzić zmiany w czasie działania użytkownika, usługi Application Insights wymaga Identyfikatora dla każdego użytkownika lub sesję. Uwzględnij następujące identyfikatory w każdy widok niestandardowy zdarzeń lub strony.
- Użytkownicy, Lejki, przechowywania i stado: zawiera identyfikatora użytkownika.
- Sesje: Zawiera identyfikatora sesji.

Jeśli aplikacja jest zintegrowana z [JavaScript SDK](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), automatycznie śledzenia identyfikator użytkownika.

## <a name="choosing-user-ids"></a>Wybieranie nazwy użytkowników

Identyfikatory powinny zachowywane między sesjami użytkownika do śledzenia zachowania użytkowników w czasie. Istnieją różne podejścia do utrwalanie identyfikator.
- Definicja użytkownika, która już istnieje w usłudze.
- Jeśli usługa ma dostęp do przeglądarki, jego można przekazać przeglądarki pliku cookie z Identyfikatorem w nim. Identyfikator będzie utrzymywać tak długo, jak plik cookie pozostanie w przeglądarce.
- W razie potrzeby nowego Identyfikatora można użyć w każdej sesji, ale wyniki dotyczące użytkowników będą ograniczone. Na przykład nie będzie można zobaczyć, jak zmiany zachowania użytkownika w czasie.

Identyfikator powinien być identyfikatorem Guid lub innego ciągu dostatecznie złożone, aby jednoznacznie zidentyfikować każdego użytkownika. Na przykład może to być liczba długo.

Jeśli identyfikator zawiera identyfikujących informacje o użytkowniku, nie jest odpowiednią wartość do wysłania do usługi Application Insights jako identyfikator użytkownika. Możesz wysłać identyfikator jako [uwierzytelniony identyfikator użytkownika](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), ale nie spełnia wymagań identyfikator użytkownika dla scenariuszy użycia.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplikacje ASP.NET: Ustawianie kontekstu użytkownika w ITelemetryInitializer

Utwórz inicjator telemetrii w sposób opisany szczegółowo [tutaj](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)i ustaw Context.User.Id i Context.Session.Id.

W tym przykładzie identyfikator użytkownika do identyfikatora wygasa po sesji. Jeśli to możliwe Użyj Identyfikatora użytkownika, który będzie się powtarzał między sesjami.

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
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić korzystanie z użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeżeli już zdarzeń niestandardowych lub wyświetleń strony, Poznaj narzędzia użycia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    * [Przegląd wykorzystania](app-insights-usage-overview.md)
    * [Użytkownikami, sesjami i zdarzenia](app-insights-usage-segmentation.md)
    * [Lejki](usage-funnels.md)
    * [Przechowywanie](app-insights-usage-retention.md)
    * [Skoroszyty](app-insights-usage-workbooks.md)
