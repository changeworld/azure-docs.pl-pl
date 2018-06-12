---
title: Diagnozowanie błędów i wyjątków w aplikacji sieci web za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Przechwytywanie wyjątków w aplikacji ASP.NET oraz dane telemetryczne żądania.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: a3dcf4211df5d40c4b174fd9a818d3268ffaa3a0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295714"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnozowanie wyjątków w aplikacjach sieci web za pomocą usługi Application Insights
Wyjątki w aplikacji sieci web na żywo są zgłaszane przez [usługi Application Insights](app-insights-overview.md). Żądań zakończonych niepowodzeniem może być zgodne z wyjątków i innych zdarzeń klienta i serwera, dzięki czemu można szybko diagnozowania przyczyn.

## <a name="set-up-exception-reporting"></a>Konfigurowanie zgłoszenie wyjątku
* Istnieć wyjątki zgłoszone z aplikacji serwera:
  * Zainstaluj [zestaw SDK usługi Application Insights](app-insights-asp-net.md) w kodzie aplikacji lub
  * Serwery sieci web usług IIS: Uruchom [agenta Application Insights](app-insights-monitor-performance-live-website-now.md); lub
  * Aplikacje sieci web platformy Azure: Dodaj [rozszerzenie usługi Application Insights](app-insights-azure-web-apps.md)
  * Aplikacje sieci web Java: Zainstaluj [agenta Java](app-insights-java-agent.md)
* Zainstaluj [fragment kodu JavaScript](app-insights-javascript.md) na stronach sieci web, aby przechwytywać wyjątki przeglądarki.
* W niektórych struktur aplikacji lub z niektórych ustawień należy wykonać kilka dodatkowych czynności, aby przechwytywać wyjątki więcej:
  * [Formularze sieci Web](#web-forms)
  * [MVC](#mvc)
  * [1.* interfejsu API sieci Web](#web-api-1x)
  * [2.* interfejsu API sieci Web](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnozowanie wyjątków przy użyciu programu Visual Studio
Otwórz rozwiązanie aplikacji w programie Visual Studio, aby pomóc w debugowaniu.

Uruchom aplikację, na serwerze lub na komputerze deweloperskim za pomocą F5.

Otwórz okno wyszukiwania usługi Application Insights w programie Visual Studio i ustawić go, aby wyświetlić zdarzenia z aplikacji. Podczas debugowania kodu, możesz to zrobić, klikając przycisk Application Insights.

![Kliknij prawym przyciskiem myszy projekt i wybierz polecenie usługi Application Insights, Otwórz.](./media/app-insights-asp-net-exceptions/34.png)

Zwróć uwagę, czy raport ma zawierać tylko wyjątki można filtrować.

*Żadne wyjątki przedstawiający? Zobacz [przechwytywania wyjątków](#exceptions).*

Kliknij raport do wyświetlenia jego ślad stosu.
Kliknij przycisk informacje w wierszu w ślad stosu, aby otworzyć plik odpowiedni kod.  

W kodzie Zwróć uwagę, że CodeLens zawiera dane dotyczące wyjątków:

![Powiadomienie CodeLens wyjątków.](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnozowanie błędów przy użyciu portalu Azure
Usługa Application Insights jest dostarczany z wyselekcjonowanych środowisko APM, aby ułatwić diagnozowanie błędów w monitorowanej aplikacji. Aby rozpocząć, kliknij opcję błędów w menu zasobu usługi Application Insights znajduje się w sekcji Zbadaj. Widok pełnoekranowy, pokazujący trendów szybkość awarii swoje żądania, ile z nich kończą się niepowodzeniem i wpływ na ilu użytkowników powinny być widoczne. Po prawej stronie pojawi się niektóre specyficzne dla wybranego dystrybucje najbardziej przydatne niepowodzenia operacji, łącznie z 3 pierwszych kody odpowiedzi, typów wyjątków 3 pierwszych i 3 najlepszych niepowodzeniem typów zależności. 

![Błędy klasyfikowanie widoku (karta operacji)](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

W jednym kliknięciem można następnie przejrzeć reprezentatywnej próbki dla każdego z tych podzbiorów operacji. W szczególności diagnozowanie wyjątków, możesz kliknąć opcję Liczba określonego wyjątku będą widoczne z bloku szczegóły wyjątków, taką jak:

![Wyjątek szczegóły blok](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**Alternatywnie** zamiast patrzeć wyjątki operacji określonych niepowodzenie, można uruchomić z ogólny widok wyjątków, przełączając kartę wyjątki:

![Błędy klasyfikowanie widoku (karta wyjątków)](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

Tutaj można zobaczyć wszystkie wyjątki, które są zbierane dla monitorowanej aplikacji.

*Żadne wyjątki przedstawiający? Zobacz [przechwytywania wyjątków](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>I śledzenie niestandardowe dane dziennika
Aby uzyskać dane diagnostyczne specyficzne dla aplikacji, można wstawić kod, aby wysłać dane telemetryczne. To wyświetlane w diagnostycznych wyszukiwania obok żądania, widok strony i inne dane zbierane automatycznie.

Istnieje kilka opcji:

* [Funkcji TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) jest zwykle używana do monitorowania wzorce użycia, ale dane wysyła również są wyświetlane w obszarze niestandardowych zdarzeń diagnostycznych wyszukiwania. Zdarzenia są nazywane i mogą przenosić właściwości ciągu lub liczbowego metryki, w których można [filtru wyszukiwania diagnostycznych](app-insights-diagnostic-search.md).
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) pozwala wysyłać dane dłużej, takie jak informacje POST.
* [Funkcji TrackException()](#exceptions) wysyła stos danych śledzenia. [Więcej informacji na temat wyjątki](#exceptions).
* Jeśli używasz już struktury rejestrowania, takich jak Log4Net lub NLog, możesz [przechwytywania tych dzienników](app-insights-asp-net-trace-logs.md) i wyświetlać je w diagnostycznych wyszukiwania obok danych żądania i wyjątków.

Aby wyświetlić te zdarzenia, otwórz [wyszukiwania](app-insights-diagnostic-search.md), otwórz filtru, a następnie wybierz Custom Event śledzenia i wyjątków.

![Drążenie wskroś](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Jeśli aplikacja generuje wiele danych telemetrycznych, moduł próbkowania adaptacyjnego będzie automatycznie redukować ilość danych wysyłanych do portalu, wysyłając tylko ich reprezentatywną część. Zdarzenia, które są częścią tej samej operacji zostanie wybrana lub zostanie usunięte zaznaczenie jako grupa, dzięki czemu można przechodzić między powiązanych zdarzeń. [Więcej informacji na temat pobierania próbek.](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak wyświetlić dane POST na żądanie
Szczegóły żądania nie zawierają danych wysłanych do aplikacji w wywołaniu POST. Te dane są raportowane:

* [Zainstaluj zestaw SDK](app-insights-asp-net.md) w projekcie aplikacji.
* Wstawianie kodu w aplikacji do wywołania [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace). Wyślij dane POST w parametrze wiadomości. Istnieje limit dozwolony rozmiar, dlatego powinna próbować wysłać tylko istotne dane.
* Podczas badania, żądanie nie powiodło się, Znajdź skojarzone dane śledzenia.  

![Drążenie wskroś](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Przechwytywanie wyjątków i powiązane dane diagnostyczne
Na początku nie będą widzieć w portalu wszystkie wyjątki, które spowodować awarię aplikacji. Zobaczysz wszystkie wyjątki przeglądarki (Jeśli używasz [JavaScript SDK](app-insights-javascript.md) na stronach sieci web). Jednak większość serwera wyjątki są przechwytywane przez usługi IIS i trzeba napisać z bitowego kodu w celu zapoznania się z nimi.

Możesz:

* **Wyjątki rejestru jawnie** przez wstawianie kodu do obsługi wyjątków, aby zgłosić wyjątki.
* **Przechwytywanie wyjątków automatycznie** przez skonfigurowanie struktury programu ASP.NET. Dodatki niezbędne są różne dla różnych typów framework.

## <a name="reporting-exceptions-explicitly"></a>Raportowanie jawnie wyjątków
Najprostszym sposobem jest aby wstawić wywołań funkcji trackexception() obsługi wyjątków.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

Właściwości i pomiarów parametry są opcjonalne, ale są przydatne w przypadku [filtrowanie i dodawanie](app-insights-diagnostic-search.md) dodatkowe informacje. Na przykład jeśli aplikację można uruchomić kilka gier, można znaleźć wszystkie raporty wyjątek związane z określonym gier. Możesz dodać dowolną liczbę elementów jak do każdego słownika.

## <a name="browser-exceptions"></a>Wyjątki przeglądarki
Większość przeglądarki wyjątki są zgłaszane.

Jeśli strony sieci web zawiera pliki skryptów z sieci dostarczania zawartości lub inne domeny, upewnij się, Twoje tag skryptu ma atrybut ```crossorigin="anonymous"```, oraz że serwer wysyła [nagłówki CORS](http://enable-cors.org/). Pozwoli to uzyskać ślad stosu i szczegóły dla nieobsłużonych wyjątków JavaScript z tych zasobów.

## <a name="web-forms"></a>Formularze sieci Web
W przypadku formularzy sieci web modułu HTTP będzie można zebrać wyjątki, kiedy nie ma żadnych przekierowuje skonfigurowano CustomErrors.

Ale jeśli masz active przekierowania, Dodaj następujące wiersze do funkcji Application_Error w Global.asax.cs. (Dodaj plik Global.asax, jeśli nie został wcześniej).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Począwszy od zestawu SDK aplikacji informacje na temat technologii sieci Web w wersji 2.6 (beta3 i nowszych), usługi Application Insights zbiera nieobsługiwany wyjątki zgłaszane w metodach 5 + kontrolerów MVC automatycznie. Jeśli wcześniej dodano niestandardowego programu obsługi, aby śledzić takie wyjątki (zgodnie z opisem w poniższych przykładach), można usunąć go w celu uniemożliwienia podwójne śledzenia wyjątków.

Istnieje wiele przypadków, które nie obsługują filtry wyjątków. Na przykład:

* Wyjątki generowane z konstruktorami kontrolera.
* Wyjątków zgłaszanych przez programy obsługi wiadomości.
* Wyjątki generowane podczas routingu.
* Wyjątki generowane podczas serializacji treści odpowiedzi.
* Zgłoszono wyjątek podczas uruchamiania aplikacji.
* Wystąpił wyjątek przy zadania w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie. Nieobsługiwane wyjątki pochodzące z kontrolerów zazwyczaj wynikiem odpowiedzi 500 "Wewnętrzny błąd serwera". Jeśli takie odpowiedzi jest ręcznie utworzone w wyniku obsłużył wyjątek (lub żaden wyjątek wcale) są śledzone w odpowiednie dane telemetryczne żądania z `ResultCode` 500, jednak zestaw SDK usługi Application Insights nie może śledzić odpowiednich wyjątków.

### <a name="prior-versions-support"></a>Obsługuje poprzednie wersje
Jeśli używasz MVC 4 (i przed) Application Insights sieci Web zestawu SDK 2.5 (i przed), zapoznaj się następujące przykłady śledzić wyjątki.

Jeśli [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfiguracja jest `Off`, a następnie wyjątki będą dostępne dla [moduł HTTP](https://msdn.microsoft.com/library/ms178468.aspx) do zbierania. Jednak jeśli jest `RemoteOnly` (ustawienie domyślne) lub `On`, a następnie wyjątek zostanie wyczyszczony i nie są dostępne dla usługi Application Insights w celu automatycznego zbierania. Możesz rozwiązać ten problem, zastępowanie [klasy System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)i stosowanie przesłoniętych klasy, jak pokazano poniżej różnych wersji MVC ([źródła github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
Zastąp atrybutu HandleError Twojego nowy atrybut w kontrolerach.

```csharp
    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...
```

[próbki](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Zarejestruj `AiHandleErrorAttribute` jako filtr globalny w Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[próbki](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
AiHandleErrorAttribute rejestru jako filtr globalny w FilterConfig.cs:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[próbki](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Interfejs API sieci Web
Począwszy od zestawu SDK aplikacji informacje na temat technologii sieci Web w wersji 2.6 (beta3 i nowszych), usługi Application Insights zbiera nieobsługiwanych wyjątków zgłoszonych w metodach kontrolera automatycznie dla WebAPI 2 +. Jeśli wcześniej dodano niestandardowego programu obsługi, aby śledzić takie wyjątki (zgodnie z opisem w poniższych przykładach), można usunąć go w celu uniemożliwienia podwójne śledzenia wyjątków.

Istnieje wiele przypadków, które nie obsługują filtry wyjątków. Na przykład:

* Wyjątki generowane z konstruktorami kontrolera.
* Wyjątków zgłaszanych przez programy obsługi wiadomości.
* Wyjątki generowane podczas routingu.
* Wyjątki generowane podczas serializacji treści odpowiedzi.
* Zgłoszono wyjątek podczas uruchamiania aplikacji.
* Wystąpił wyjątek przy zadania w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie. Nieobsługiwane wyjątki pochodzące z kontrolerów zazwyczaj wynikiem odpowiedzi 500 "Wewnętrzny błąd serwera". Jeśli takie odpowiedzi jest ręcznie utworzone w wyniku obsłużył wyjątek (lub żaden wyjątek wcale) są śledzone w odpowiednie dane telemetryczne żądania z `ResultCode` 500, jednak zestaw SDK usługi Application Insights nie może śledzić odpowiednich wyjątków.

### <a name="prior-versions-support"></a>Obsługuje poprzednie wersje
Jeśli używasz WebAPI 1 (i przed) Application Insights sieci Web zestawu SDK 2.5 (i przed), zapoznaj się następujące przykłady śledzić wyjątki.

#### <a name="web-api-1x"></a>Interfejs API sieci Web 1.x
Override System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Możesz dodać ten atrybut przesłoniętej do określonych kontrolerów lub dodać je do konfiguracji filtrów globalnych klasy WebApiConfig:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[próbki](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Interfejs API sieci Web 2.x
Dodaj implementacji interfejsu IExceptionLogger:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Dodaj ją do usług w WebApiConfig:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[próbki](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Opis rozwiązań alternatywnych można:

1. Zamień tylko ExceptionHandler niestandardowej implementacji IExceptionHandler. Jest to nazywane tylko, gdy platformę jest nadal mogli wybrać które komunikat odpowiedzi do odesłania, (nie gdy połączenie zostało przerwane dla wystąpienia)
2. Filtry wyjątków (zgodnie z opisem w sekcji kontrolery 1.x interfejsu API sieci Web powyżej) — nie jest wywoływana we wszystkich przypadkach.

## <a name="wcf"></a>WCF
Dodaj klasę Attribute i implementującą interfejsy IErrorHandler i IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[próbki](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Liczniki wydajności wyjątku
Jeśli masz [zainstalować agenta programu Application Insights](app-insights-monitor-performance-live-website-now.md) na serwerze, można uzyskać wykres wyjątki szybkości, mierząc .NET. Dotyczy to zarówno obsłużonych i nieobsłużonych wyjątków .NET.

Otwiera blok Explorer metryki, Dodaj nowy wykres i wybierz **szybkość wyjątek**, to wymienione w obszarze liczników wydajności.

.NET framework oblicza stopę przez liczenie wyjątków w interwale i podzielenie przez długość interwału.

To jest inna niż liczba "Wyjątków" obliczana na podstawie zliczania TrackException raporty portalu Application Insights. Interwałami próbkowania są różne, a zestaw SDK nie wysyła raporty TrackException wszystkie obsługiwane i nieobsługiwane wyjątki.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie REST, SQL i innych wywołania zależności](app-insights-asp-net-dependencies.md)
* [Czas ładowania strony monitora, wyjątki przeglądarki i wywołania AJAX](app-insights-javascript.md)
* [Liczniki Monitora wydajności](app-insights-performance-counters.md)
