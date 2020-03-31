---
title: Diagnozowanie błędów i wyjątków za pomocą usługi Azure Application Insights
description: Przechwytywanie wyjątków od ASP.NET aplikacji wraz z telemetrią żądania.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccfcb354e27d36f40810b114a1729cf6addf8fb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294686"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Diagnozowanie wyjątków w aplikacjach internetowych za pomocą usługi Application Insights
Wyjątki w aplikacji sieci web na żywo są zgłaszane przez [usługa Application Insights](../../azure-monitor/app/app-insights-overview.md). Można skorelować żądania nie powiodło się z wyjątkami i innych zdarzeń na kliencie i serwerze, dzięki czemu można szybko zdiagnozować przyczyny.

## <a name="set-up-exception-reporting"></a>Konfigurowanie raportowania wyjątków
* Aby zgłaszać wyjątki z aplikacji serwera:
  * Aplikacje sieci Web platformy Azure: dodawanie [rozszerzenia usługi Application Insights](../../azure-monitor/app/azure-web-apps.md)
  * Aplikacje hostowane przez usługi IIS: dodawanie rozszerzenia [monitorowania aplikacji](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * Zainstaluj [sdk usługi Application Insights](../../azure-monitor/app/asp-net.md) w kodzie aplikacji lub
  * Serwery sieci Web usług IIS: [uruchamianie agenta usługi Application Insights](../../azure-monitor/app/monitor-performance-live-website-now.md); Lub
  * Aplikacje internetowe Java: Instalowanie [agenta Java](../../azure-monitor/app/java-agent.md)
* Zainstaluj [fragment kodu JavaScript](../../azure-monitor/app/javascript.md) na swoich stronach internetowych, aby wychwycić wyjątki przeglądarki.
* W niektórych strukturach aplikacji lub z pewnymi ustawieniami należy wykonać kilka dodatkowych kroków, aby złapać więcej wyjątków:
  * [Formularze internetowe](#web-forms)
  * [MVC](#mvc)
  * [Internetowy interfejs API 1.*](#web-api-1x)
  * [Internetowy interfejs API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Ten artykuł koncentruje się w szczególności na .NET Framework aplikacji z punktu widzenia przykładu kodu. Niektóre metody, które działają dla platformy .NET Framework są przestarzałe w pliku .NET Core SDK. Jeśli masz aplikację .NET Core Core, zapoznaj się z [dokumentacją sdk .NET Core SDK.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnozowanie wyjątków przy użyciu programu Visual Studio
Otwórz rozwiązanie aplikacji w programie Visual Studio, aby ułatwić debugowanie.

Uruchom aplikację na serwerze lub na komputerze deweloperskim przy użyciu F5.

Otwórz okno wyszukiwania usługi Application Insights w programie Visual Studio i ustaw je tak, aby wyświetlało zdarzenia z aplikacji. Podczas debugowania, można to zrobić po prostu klikając przycisk Usługi Application Insights.

![Kliknij prawym przyciskiem myszy projekt i wybierz polecenie Usługa Application Insights, Otwórz.](./media/asp-net-exceptions/34.png)

Należy zauważyć, że można filtrować raport, aby wyświetlić tylko wyjątki.

*Brak wyjątków? Zobacz [Przechwytywanie wyjątków](#exceptions).*

Kliknij raport o wyjątkach, aby wyświetlić jego ślad stosu.
Kliknij odwołanie do wiersza w śledzenia stosu, aby otworzyć odpowiedni plik kodu.

W kodzie należy zauważyć, że CodeLens pokazuje dane dotyczące wyjątków:

![CodeLens powiadomienie o wyjątkach.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnozowanie błędów przy użyciu witryny Azure portal
Usługa Application Insights jest wyposażona w wyselekcjonowane środowisko APM ułatwiające diagnozowanie błędów w monitorowanych aplikacjach. Aby rozpocząć, kliknij opcję Błędy w menu zasobów usługi Application Insights znajdującym się w sekcji Badanie.
Powinien zostać wyświetlony widok pełnoekranowy, który pokazuje trendy szybkości awaryjności dla żądań, ile z nich nie powiódł się i ilu użytkowników ma wpływ. Po prawej stronie zobaczysz niektóre z najbardziej przydatnych dystrybucji specyficznych dla wybranej operacji niepowodzenia, w tym trzy najlepsze kody odpowiedzi, trzy pierwsze typy wyjątków i trzy pierwsze typy zależności, które nie działają.

![Widok klasyfikacji błędów (karta operacje)](./media/asp-net-exceptions/failures0719.png)

Za pomocą jednego kliknięcia można następnie przejrzeć reprezentatywne przykłady dla każdej z tych podzbiorów operacji. W szczególności, aby zdiagnozować wyjątki, można kliknąć na liczbę określonego wyjątku, który ma być przedstawiony z end-to-end szczegóły transakcji kartę, takich jak ten:

![Karta Szczegóły transakcji typu end-to-end](./media/asp-net-exceptions/end-to-end.png)

**Alternatywnie zamiast** patrzeć na wyjątki określonej operacji niepowodzenia, można rozpocząć od ogólnego widoku wyjątków, przełączając się na wyjątki kartę u góry. Tutaj możesz zobaczyć wszystkie wyjątki zebrane dla monitorowanej aplikacji.

*Brak wyjątków? Zobacz [Przechwytywanie wyjątków](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Niestandardowe dane śledzenia i dzienników
Aby uzyskać dane diagnostyczne specyficzne dla aplikacji, można wstawić kod do wysyłania własnych danych telemetrycznych. Jest to wyświetlane w wyszukiwaniu diagnostycznym obok żądania, widoku strony i innych automatycznie zebranych danych.

Istnieje kilka rozwiązań:

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) jest zwykle używany do monitorowania wzorców użycia, ale dane, które wysyła również pojawia się w obszarze Zdarzenia niestandardowe w wyszukiwaniu diagnostycznym. Zdarzenia są nazwane i mogą przenosić właściwości ciągu i metryki liczbowe, na których można [filtrować wyszukiwania diagnostyczne](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) umożliwia wysyłanie dłuższych danych, takich jak informacje POST.
* [TrackException()](#exceptions) wysyła ślady stosu. [Więcej o wyjątkach](#exceptions).
* Jeśli już używasz struktury rejestrowania, takich jak Log4Net lub NLog, można [przechwycić te dzienniki](asp-net-trace-logs.md) i wyświetlić je w wyszukiwaniu diagnostycznym obok danych żądań i wyjątków.

Aby wyświetlić te zdarzenia, otwórz [wyszukiwanie](../../azure-monitor/app/diagnostic-search.md) z lewego menu, wybierz menu rozwijane **Typy zdarzeń**, a następnie wybierz pozycję Zdarzenie niestandardowe, Śledzenie lub Wyjątek.

![Przeglądanie szczegółowe](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Jeśli aplikacja generuje wiele danych telemetrycznych, moduł próbkowania adaptacyjnego będzie automatycznie redukować ilość danych wysyłanych do portalu, wysyłając tylko ich reprezentatywną część. Zdarzenia, które są częścią tej samej operacji zostaną wybrane lub odznaczone jako grupa, dzięki czemu można nawigować między powiązanymi zdarzeniami. [Dowiedz się więcej o próbkowaniu.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Jak wyświetlić dane żądania POST
Szczegóły żądania nie obejmują danych wysyłanych do aplikacji w wywołaniu POST. Aby zgłosić te dane:

* [Zainstaluj pakiet SDK](../../azure-monitor/app/asp-net.md) w projekcie aplikacji.
* Wstaw kod do aplikacji, aby wywołać [microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). Wyślij dane POST w parametrze wiadomości. Istnieje ograniczenie dozwolonego rozmiaru, więc należy spróbować wysłać tylko podstawowe dane.
* Podczas badania żądania nie powiodło się, znajdź skojarzone ślady.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>Przechwytywanie wyjątków i powiązanych danych diagnostycznych
Na początku nie zobaczysz w portalu wszystkich wyjątków, które powodują błędy w aplikacji. Zobaczysz wyjątki przeglądarki (jeśli używasz [SDK JavaScript](../../azure-monitor/app/javascript.md) na swoich stronach internetowych). Ale większość wyjątków serwera są przechwytywać przez usługi IIS i trzeba napisać trochę kodu, aby je zobaczyć.

Możesz:

* **Rejestrowanie wyjątków jawnie** przez wstawianie kodu w programach obsługi wyjątków do zgłaszania wyjątków.
* **Przechwytywanie wyjątków automatycznie,** konfigurując ASP.NET framework. Niezbędne dodatki są różne dla różnych typów ram.

## <a name="reporting-exceptions-explicitly"></a>Jawne zgłaszanie wyjątków
Najprostszym sposobem jest wstawienie wywołania TrackException() w programie obsługi wyjątków.

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

Parametry właściwości i pomiarów są opcjonalne, ale są przydatne do [filtrowania i dodawania](../../azure-monitor/app/diagnostic-search.md) dodatkowych informacji. Jeśli na przykład masz aplikację, która może uruchomić kilka gier, możesz znaleźć wszystkie raporty wyjątków związane z określoną grą. Do każdego słownika można dodać dowolną liczbę elementów.

## <a name="browser-exceptions"></a>Wyjątki przeglądarki
Większość wyjątków przeglądarki są zgłaszane.

Jeśli strona sieci web zawiera pliki skryptów z sieci dostarczania zawartości lub ```crossorigin="anonymous"```innych domen, upewnij się, że tag skryptu ma atrybut i że serwer wysyła [nagłówki CORS](https://enable-cors.org/). Pozwoli to uzyskać ślad stosu i szczegóły dla nieobsługiwał wyjątków JavaScript z tych zasobów.

## <a name="reuse-your-telemetry-client"></a>Ponowne używanie klienta telemetrii

> [!NOTE]
> TelemetryClient zaleca się wystąpienia raz i ponownie używane przez cały okres użytkowania aplikacji.

Poniżej znajduje się przykład przy użyciu TelemetryClient poprawnie.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Formularze internetowe
W przypadku formularzy sieci web moduł HTTP będzie mógł zbierać wyjątki, gdy nie ma przekierowań skonfigurowanych za pomocą funkcji CustomErrors.

Jeśli jednak masz aktywne przekierowania, dodaj następujące wiersze do funkcji Application_Error w Global.asax.cs. (Dodaj plik Global.asax, jeśli jeszcze go nie masz).

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
Począwszy od application insights Web SDK w wersji 2.6 (beta3 i nowsze), Usługa Application Insights automatycznie zbiera nieobsługiwane wyjątki generowane w metodach kontrolerów MVC 5+. Jeśli wcześniej dodano niestandardowy program obsługi do śledzenia takich wyjątków (zgodnie z opisem w poniższych przykładach), można go usunąć, aby zapobiec podwójnemu śledzeniu wyjątków.

Istnieje wiele przypadków, których filtry wyjątków nie mogą obsłużyć. Przykład:

* Wyjątki generowane przez konstruktory kontrolerów.
* Wyjątki generowane z programów obsługi komunikatów.
* Wyjątki zgłaszane podczas routingu.
* Wyjątki generowane podczas serializacji zawartości odpowiedzi.
* Wyjątek zgłoszony podczas uruchamiania aplikacji.
* Wyjątek zgłoszony w zadaniach w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsługiwalne wyjątki pochodzące z kontrolerów zazwyczaj powodują 500 odpowiedzi "Wewnętrzny błąd serwera". Jeśli taka odpowiedź jest ręcznie skonstruowana w wyniku obsługiwanego wyjątku (lub w ogóle nie `ResultCode` jest wyjątkiem) jest śledzona w odpowiedniej telemetrii żądania z 500, jednak zestaw SDK usługi Application Insights nie może śledzić odpowiedniego wyjątku.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
Jeśli używasz MVC 4 (i wcześniej) application insights Web SDK 2.5 (i wcześniej), odnoszą się do następujących przykładów do śledzenia wyjątków.

Jeśli konfiguracja [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) jest `Off`, a następnie wyjątki będą dostępne dla [modułu HTTP](https://msdn.microsoft.com/library/ms178468.aspx) do zbierania. Jednak jeśli jest `RemoteOnly` (domyślnie) lub `On`, a następnie wyjątek zostanie wyczyszczone i nie będzie dostępny dla usługi Application Insights do automatycznego zbierania. Można to naprawić, zastępując [klasę System.Web.Mvc.HandleErrorAttribute](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)i stosując zastąpione klasy, jak pokazano dla różnych wersji MVC poniżej[(źródło GitHub):](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)

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
Zastąp atrybut HandleError nowym atrybutem w kontrolerach.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Przykładowe](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Zarejestruj `AiHandleErrorAttribute` się jako filtr globalny w Global.asax.cs:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Przykładowe](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
Zarejestruj AiHandleErrorAttribute jako filtr globalny w FilterConfig.cs:

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

[Przykładowe](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Interfejs API sieci Web
Począwszy od application insights Web SDK w wersji 2.6 (beta3 i nowsze), Usługa Application Insights zbiera nieobsługiwane wyjątki generowane w metodach kontrolera automatycznie dla webapi 2+. Jeśli wcześniej dodano niestandardowy program obsługi do śledzenia takich wyjątków (zgodnie z opisem w poniższych przykładach), można go usunąć, aby zapobiec podwójnemu śledzeniu wyjątków.

Istnieje wiele przypadków, których filtry wyjątków nie mogą obsłużyć. Przykład:

* Wyjątki generowane przez konstruktory kontrolerów.
* Wyjątki generowane z programów obsługi komunikatów.
* Wyjątki zgłaszane podczas routingu.
* Wyjątki generowane podczas serializacji zawartości odpowiedzi.
* Wyjątek zgłoszony podczas uruchamiania aplikacji.
* Wyjątek zgłoszony w zadaniach w tle.

Wszystkie wyjątki *obsługiwane* przez aplikację nadal muszą być śledzone ręcznie.
Nieobsługiwalne wyjątki pochodzące z kontrolerów zazwyczaj powodują 500 odpowiedzi "Wewnętrzny błąd serwera". Jeśli taka odpowiedź jest ręcznie skonstruowana w wyniku obsługiwanego wyjątku (lub w ogóle nie jest `ResultCode` wyjątkiem) jest śledzona w odpowiedniej telemetrii żądania z 500, jednak zestaw SDK usługi Application Insights nie może śledzić odpowiedniego wyjątku.

### <a name="prior-versions-support"></a>Obsługa wcześniejszych wersji
Jeśli używasz webapi 1 (i wcześniej) aplikacji Application Insights Web SDK 2.5 (i wcześniej), zapoznaj się z poniższymi przykładami, aby śledzić wyjątki.

#### <a name="web-api-1x"></a>Internetowy interfejs API 1.x
Zastąp atrybut System.Web.Http.Filters.ExceptionFilterAttribute:

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

Ten atrybut można dodać do określonych kontrolerów lub dodać go do globalnej konfiguracji filtru w klasie WebApiConfig:

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

[Przykładowe](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Internetowy interfejs API 2.x
Dodaj implementację IExceptionLogger:

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

Dodaj to do usług w WebApiConfig:

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

[Przykładowe](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Jako alternatywy, można:

1. Zastąp tylko ExceptionHandler z niestandardowej implementacji IExceptionHandler. Jest to wywoływane tylko wtedy, gdy struktura jest nadal w stanie wybrać komunikat odpowiedzi do wysłania (nie wtedy, gdy połączenie zostanie przerwane na przykład)
2. Filtry wyjątków (zgodnie z opisem w sekcji dotyczącej kontrolerów interfejsu API sieci Web 1.x powyżej) — nie są wywoływane we wszystkich przypadkach.

## <a name="wcf"></a>WCF
Dodaj klasę, która rozszerza Atrybut i implementuje IErrorHandler i IServiceBehavior.

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

[Przykładowe](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Liczniki wydajności wyjątków
Jeśli agent [aplikacji aplikacji jest zainstalowany](../../azure-monitor/app/monitor-performance-live-website-now.md) na serwerze, można uzyskać wykres współczynnika wyjątków mierzonego przez .NET. Obejmuje to zarówno obsługiwane, jak i nieobsługiwały wyjątki .NET.

Otwórz kartę Eksplorator metryk, dodaj nowy wykres i wybierz **pozycję Współczynnik wyjątków**, wymieniony w obszarze Liczniki wydajności.

Struktura .NET oblicza stawkę, licząc liczbę wyjątków w interwale i dzieląc przez długość interwału.

Różni się to od liczby "Wyjątków" obliczonej przez raporty portalu usługi Application Insights z inwentaryzacji TrackException. Interwały próbkowania są różne, a SDK nie wysyła raportów TrackException dla wszystkich obsługiwanych i nieobsługiwał wyjątków.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie REST, SQL i innych wywołań zależności](../../azure-monitor/app/asp-net-dependencies.md)
* [Monitorowanie czasów ładowania strony, wyjątków przeglądarki i wywołań AJAX](../../azure-monitor/app/javascript.md)
* [Monitorowanie liczników wydajności](../../azure-monitor/app/performance-counters.md)
