---
title: Diagnozowanie awarii i wyjątków w aplikacjach sieci web za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Przechwytywanie wyjątków w aplikacji ASP.NET oraz dane telemetryczne żądania.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb32069de295b883cdc6d3a9fa495b1bea719c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691856"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnozowanie wyjątków w aplikacjach sieci web za pomocą usługi Application Insights
Wyjątki w aplikacji internetowej na żywo są zgłaszane przez [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Żądania zakończone niepowodzeniem można skorelować wyjątki i inne zdarzenia, u klienta i serwera, dzięki czemu można szybko diagnozować ich przyczyny.

## <a name="set-up-exception-reporting"></a>Skonfiguruj raportowanie wyjątków
* Zapewnienie wyjątków zgłoszonych z aplikacji serwera:
  * Zainstaluj [zestawu SDK usługi Application Insights](../../azure-monitor/app/asp-net.md) w kodzie aplikacji lub
  * Serwery sieci web usług IIS: Uruchom [Application Insights Agent](../../azure-monitor/app/monitor-performance-live-website-now.md); lub
  * Aplikacje sieci web platformy Azure: Dodaj [rozszerzenie usługi Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Aplikacje sieci web Java: Zainstaluj [agenta Java](../../azure-monitor/app/java-agent.md)
* Zainstaluj [fragment kodu JavaScript](../../azure-monitor/app/javascript.md) na stronach sieci web, aby przechwytywać wyjątki przeglądarki.
* W niektórych struktur aplikacji lub z niektórymi ustawieniami należy wykonać dodatkowe kroki, aby przechwytywać wyjątki więcej:
  * [Formularze sieci Web](#web-forms)
  * [MVC](#mvc)
  * [1.* interfejsu API sieci Web](#web-api-1x)
  * [2.* interfejsu API sieci Web](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnozowanie wyjątków za pomocą programu Visual Studio
Otwórz rozwiązanie aplikacji w programie Visual Studio, aby pomóc w debugowaniu.

Uruchom aplikację, na serwerze lub na komputerze deweloperskim, naciskając klawisz F5.

Otwórz okno wyszukiwania usługi Application Insights w programie Visual Studio i ustaw go, aby wyświetlić zdarzenia z aplikacji. Podczas debugowania, możesz to zrobić, klikając przycisk usługi Application Insights.

![Kliknij prawym przyciskiem myszy projekt i wybierz pozycję Application Insights, Otwórz.](./media/asp-net-exceptions/34.png)

Należy zauważyć, że możesz filtrować raport, aby pokazać tylko wyjątki.

*Żadne wyjątki nie jest wyświetlane? Zobacz [przechwytywanie wyjątków](#exceptions).*

Kliknij przycisk raport do wyświetlenia jego ślad stosu.
Kliknij przycisk linii odwołania w ślad stosu, aby otworzyć plik odpowiedni kod.

W kodzie Zwróć uwagę, że wskaźniki CodeLens pokazują dane dotyczące wyjątków:

![Powiadomienia funkcji CodeLens wyjątków.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnozowanie błędów przy użyciu witryny Azure portal
Usługa Application Insights jest powiązana z nadzorowanych środowisko APM, aby ułatwić diagnozowanie błędów w monitorowanej aplikacji. Aby rozpocząć, kliknij opcję błędy w menu zasobów usługi Application Insights znajduje się w sekcji badań.
Powinien zostać wyświetlony widok pełnoekranowy, która pokazuje trendy współczynnik błędów dla żądań, ile z nich kończy się niepowodzeniem i ilu użytkowników jest dotkniętych problemem. Po prawej stronie zobaczysz niektóre z najbardziej przydatnych specyficzne dla wybranej dystrybucji kończy się niepowodzeniem operacji, łącznie z kodów odpowiedzi pierwsze 3, 3 pierwszych typów wyjątków i 3 najlepszych kończy się niepowodzeniem typów zależności.

![Błędy klasyfikowanie widoku (karta Operacje)](./media/asp-net-exceptions/FailuresTriageView.png)

Jednym kliknięciem można następnie przejrzyj reprezentatywnej próbki dla każdego z tych podzbiorów operacji. W szczególności diagnozowanie wyjątków, możesz kliknąć na podstawie liczby określonego wyjątku przedstawiane za pomocą bloku szczegóły wyjątków, taką jak ta:

![Bloku szczegółów wyjątku](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**Alternatywnie** zamiast patrząc wyjątki operacji szczególnych, można uruchomić z ogólny widok wyjątki, przełączając kartę wyjątki:

![Błędy klasyfikowanie widoku (karta wyjątków)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Tutaj można zobaczyć wszystkie wyjątki, które są zbierane dla monitorowanej aplikacji.

*Żadne wyjątki nie jest wyświetlane? Zobacz [przechwytywanie wyjątków](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Niestandardowe śledzenia i danych dziennika
Aby uzyskać dane diagnostyczne są specyficzne dla aplikacji, można wstawić kodu w celu wysyłania danych telemetrycznych. To wyświetlane w wyszukiwaniu diagnostycznym wraz z żądaniem, widok strony i inne dane zbierane automatycznie.

Istnieje kilka opcji:

* [Poleceń TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) jest zazwyczaj używana do monitorowania wzorce użycia danych wysyła on także jest wyświetlana w obszarze zdarzenia niestandardowe w wyszukiwaniu diagnostycznym. Zdarzenia są nazywane i mogą przenosić właściwości ciągu lub liczbowe metryki, na którym możesz [filtrowania wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md).
* [Metody TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) pozwala wysyłać dłużej dane, takie jak informacje POST.
* [Funkcji TrackException()](#exceptions) wysyła stos danych śledzenia. [Więcej informacji na temat wyjątków](#exceptions).
* Jeśli już używasz struktury rejestrowania, takich jak Log4Net i NLog, możesz to zrobić [Przechwytywanie tych dzienników](asp-net-trace-logs.md) i wyświetlać je w wyszukiwaniu diagnostycznym obok danych żądań i wyjątków.

Aby wyświetlić te zdarzenia, otwórz [wyszukiwania](../../azure-monitor/app/diagnostic-search.md), Otwórz filtr, a następnie wybierz niestandardowe zdarzenie śledzenia i wyjątków.

![Drążenie wskroś](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Jeśli aplikacja generuje wiele danych telemetrycznych, moduł próbkowania adaptacyjnego będzie automatycznie redukować ilość danych wysyłanych do portalu, wysyłając tylko ich reprezentatywną część. Zdarzenia, które są częścią tej samej operacji, zostaną wybrane lub pominięte jako grupa, dzięki czemu można nawigować między powiązanymi zdarzeniami. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak wyświetlić żądania POST dane
Szczegóły żądania nie zawierają danych wysłanych do aplikacji na wywołanie metody POST. Te dane są zgłoszone:

* [Zainstaluj zestaw SDK](../../azure-monitor/app/asp-net.md) w projekcie aplikacji.
* Wstaw kod w aplikacji, aby wywołać [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Wysyłanie danych POST w parametrze wiadomości. Obowiązuje limit dozwolony rozmiar, dzięki czemu powinna próbować wysłać tylko niezbędne dane.
* Podczas badania, żądanie nie powiodło się, należy znaleźć skojarzone dane śledzenia.

![Drążenie wskroś](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Przechwytywanie wyjątków i powiązane dane diagnostyczne
Na początku nie będzie widoczny w portalu wszystkie wyjątki, które powodują błędy w swojej aplikacji. Zostaną wyświetlone wszystkie wyjątki przeglądarki (Jeśli używasz [zestaw JavaScript SDK](../../azure-monitor/app/javascript.md) na stronach sieci web). Jednak większość wyjątków serwera są przechwytywane przez usługi IIS i trzeba napisać ilość kodu, aby je zobaczyć.

Możesz:

* **Rejestrowania wyjątków jawnie** , zaznaczając kod obsługi wyjątków do zgłaszania wyjątków.
* **Przechwytywanie wyjątków automatycznie** , konfigurując preferowanej struktury ASP.NET. Dodatki niezbędne są różne dla różnych typów framework.

## <a name="reporting-exceptions-explicitly"></a>Raportowanie jawnie wyjątków
Najprostszym sposobem jest wstawianie wywołań metody trackexception() w obsługi wyjątków.

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

Właściwości i wartości parametrów są opcjonalne, ale są przydatne w przypadku [filtrowania i dodawanie](../../azure-monitor/app/diagnostic-search.md) dodatkowych informacji. Na przykład jeśli masz aplikację, która może uruchomić kilka gier, możesz znaleźć wszystkie raporty wyjątek związane z określonym gry. Możesz dodać dowolną liczbę elementów, jak chcesz, aby każdy słownik.

## <a name="browser-exceptions"></a>Wyjątki przeglądarki
Większość wyjątków przeglądarki są zgłaszane.

Jeśli strona sieci web obejmuje pliki skryptów z sieci dostarczania zawartości lub z innych domen, upewnij się, tag skryptu ma atrybut ```crossorigin="anonymous"```, oraz że serwer wysyła [nagłówków CORS](https://enable-cors.org/). Pozwoli to uzyskać ślad stosu i szczegóły dla nieobsłużonych wyjątków JavaScript z tych zasobów.

## <a name="web-forms"></a>Formularze sieci Web
Moduł HTTP dla formularzy sieci web będzie do zbierania wyjątków, gdy istnieją nie przekierowuje skonfigurowano CustomErrors.

Ale jeśli masz aktywne przekierowań, Dodaj następujące wiersze do funkcji Application_Error w Global.asax.cs. (Dodaj plik Global.asax, jeśli nie masz jeszcze jeden).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Począwszy od zestaw Application Insights Web SDK w wersji 2.6 (beta3 i nowszych), usługa Application Insights zbiera nieobsługiwane wyjątki zgłaszane w metodach 5 + kontrolerów MVC automatycznie. Jeśli wcześniej zostały dodane niestandardowe procedury obsługi do śledzenia wyjątków (zgodnie z opisem w poniższych przykładach), możesz usunąć go w celu uniemożliwienia double śledzenia wyjątków.

Istnieje kilka przypadków, które nie obsługują filtry wyjątków. Na przykład:

* Wyjątków zgłaszanych przez konstruktory kontrolera.
* Wyjątków zgłaszanych przez programy obsługi komunikatów.
* Wyjątki zgłoszone podczas routingu.
* Wyjątki zgłoszone podczas serializacji treści odpowiedzi.
* Zgłoszono wyjątek podczas uruchamiania aplikacji.
* Wystąpił wyjątek zadania w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsługiwane wyjątki, zwykle pochodzących z kontrolerów doprowadzić do 500 odpowiedzi "Wewnętrzny błąd serwera". Jeśli takie odpowiedzi ręcznie jest tworzona w wyniku obsługiwanego wyjątku (lub żaden wyjątek w ogóle) jest śledzona w odpowiednie dane telemetryczne żądania z `ResultCode` 500, jednak zestaw SDK usługi Application Insights jest w stanie śledzić odpowiednich wyjątków.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
Jeśli używasz MVC 4 (lub starszy) z Application Insights Web zestawu SDK 2.5 (lub starszy) odnoszą się do poniższych przykładów, aby śledzić wyjątki.

Jeśli [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfiguracja jest `Off`, a następnie wyjątki będą dostępne dla [moduł HTTP](https://msdn.microsoft.com/library/ms178468.aspx) do zbierania. Jednak jeśli jest `RemoteOnly` (ustawienie domyślne) lub `On`, wówczas wyjątek zostanie wyczyszczony i nie są dostępne dla usługi Application Insights były zbierane automatycznie. Użytkownik może rozwiązać ten problem, zastępowanie [klasy System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)i stosowanie zgodnym z przesłoniętą klasy, jak pokazano poniżej różnych wersji platformy MVC ([źródła GitHub](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Zamień ten atrybut HandleError Twojego nowego atrybutu w kontrolerach.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Przykład](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Zarejestruj `AiHandleErrorAttribute` jako globalny filtr w Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Przykład](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Zarejestruj AiHandleErrorAttribute jako globalny filtr w FilterConfig.cs:

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

[Przykład](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Interfejs API sieci Web
Począwszy od zestaw Application Insights Web SDK w wersji 2.6 (beta3 i nowszych), usługa Application Insights zbiera nieobsługiwanych wyjątków zgłoszonych w metodach kontrolera automatycznie dla WebAPI 2 +. Jeśli wcześniej zostały dodane niestandardowe procedury obsługi do śledzenia wyjątków (zgodnie z opisem w poniższych przykładach), możesz usunąć go w celu uniemożliwienia double śledzenia wyjątków.

Istnieje kilka przypadków, które nie obsługują filtry wyjątków. Na przykład:

* Wyjątków zgłaszanych przez konstruktory kontrolera.
* Wyjątków zgłaszanych przez programy obsługi komunikatów.
* Wyjątki zgłoszone podczas routingu.
* Wyjątki zgłoszone podczas serializacji treści odpowiedzi.
* Zgłoszono wyjątek podczas uruchamiania aplikacji.
* Wystąpił wyjątek zadania w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsługiwane wyjątki, zwykle pochodzących z kontrolerów doprowadzić do 500 odpowiedzi "Wewnętrzny błąd serwera". Jeśli takie odpowiedzi ręcznie jest tworzona w wyniku obsługiwanego wyjątku (lub żaden wyjątek w ogóle) jest śledzona w odpowiednie dane telemetryczne żądania z `ResultCode` 500, jednak zestaw SDK usługi Application Insights jest w stanie śledzić odpowiednich wyjątków.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
Jeśli używasz WebAPI 1 (lub starszy) z Application Insights Web zestawu SDK 2.5 (lub starszy) odnoszą się do poniższych przykładów, aby śledzić wyjątki.

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

Możesz dodać ten atrybut zgodnym z przesłoniętą na określone kontrolery lub dodać go do konfiguracji filtru globalnego klasy WebApiConfig:

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

[Przykład](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Internetowy interfejs API 2.x
Dodaj implementacji interfejsu iexceptionlogger:

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

Dodaj do usług w WebApiConfig:

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

[Przykład](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Jako alternatywy można wykonać następujące akcje:

1. Zastąp tylko ExceptionHandler niestandardową implementację IExceptionHandler. Jest to nazywane tylko wtedy, gdy struktura jest nadal można wybrać, które komunikat odpowiedzi do wysłania (nie gdy połączenie zostało przerwane dla wystąpienia)
2. Filtry wyjątków (zgodnie z opisem w sekcji powyżej kontrolerów 1.x internetowych interfejsów API) — nie jest wywoływana we wszystkich przypadkach.

## <a name="wcf"></a>WCF
Dodaj klasę Attribute i implementującą interfejsy IErrorHandler i IServiceBehavior w sposób.

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

[Przykład](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Liczniki wydajności wyjątków
Jeśli masz [zainstalowano agenta programu Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md) na serwerze, można uzyskać wykres współczynnik wyjątków, mierzone .NET. Dotyczy to zarówno obsługiwanych i nieobsługiwanych wyjątków .NET.

Otwórz blok Eksploratora metryk, Dodaj nowy wykres i wybierz **częstotliwość występowania wyjątków**przepływu pracy opisanego w obszarze liczników wydajności.

.NET framework stawka jest obliczana przez zliczanie liczby wyjątków w odstępie i podzielenie przez długość interwału.

To różni się od liczby "Wyjątków" obliczana na podstawie portalu Application Insights, metoda TrackException raporty zliczania. Okresy próbkowania są różne, i zestawu SDK nie wysyła raportów metoda TrackException wszystkie obsługiwane i nieobsługiwane wyjątki.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie REST, SQL i inne wywołania zależności](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorowania czasów ładowania stron, wyjątków przeglądarki i wywołania AJAX](../../azure-monitor/app/javascript.md)
* [Monitorowanie liczników wydajności](../../azure-monitor/app/performance-counters.md)
