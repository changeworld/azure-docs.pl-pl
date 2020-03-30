---
title: Analiza użycia za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Poznaj użytkowników i ich to, co robią z Twoją aplikacją.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e964b1b5b9d5500f2d9f24ed765299389e6dbbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283960"
---
# <a name="usage-analysis-with-application-insights"></a>Analiza użycia za pomocą usługi Application Insights

Które funkcje twojej aplikacji internetowej lub mobilnej są najpopularniejsze? Czy twoi użytkownicy osiągają swoje cele za pomocą aplikacji? Czy wypadają w określonych punktach i czy wracają później?  [Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ułatwia uzyskiwanie szczegółowych informacji na temat sposobu korzystania z aplikacji przez osoby. Za każdym razem, gdy aktualizujesz aplikację, możesz ocenić, jak dobrze działa dla użytkowników. Dzięki tej wiedzy można podejmować decyzje oparte na danych dotyczące kolejnych cykli rozwoju.

## <a name="send-telemetry-from-your-app"></a>Wysyłanie danych telemetrycznych z aplikacji

Najlepsze środowisko uzyskuje się, instalując usługę Application Insights zarówno w kodzie serwera aplikacji, jak i na stronach sieci Web. Składniki klienta i serwera aplikacji wysyłają dane telemetryczne z powrotem do witryny Azure portal w celu analizy.

1. **Kod serwera:** Zainstaluj odpowiedni moduł dla [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md)lub [innej](../../azure-monitor/app/platforms.md) aplikacji.

    * *Nie chcesz instalować kodu serwera? Wystarczy [utworzyć zasób usługi Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Kod strony sieci Web:** Dodaj następujący skrypt do strony internetowej ``</head>``przed zamknięciem . Zamień klucz instrumentacji na odpowiednią wartość dla zasobu usługi Application Insights:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Aby dowiedzieć się więcej zaawansowanych konfiguracji monitorowania witryn sieci Web, zapoznaj się z [artykułem referencyjnym JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Kod aplikacji mobilnej:** Użyj SDK Centrum aplikacji do zbierania zdarzeń z aplikacji, a następnie wysyłaj kopie tych zdarzeń do usługi Application Insights w celu analizy, postępając zgodnie z [tym przewodnikiem.](../../azure-monitor/learn/mobile-center-quickstart.md)

4. **Pobierz dane telemetryczne:** Uruchom projekt w trybie debugowania przez kilka minut, a następnie poszukaj wyników w bloku Przegląd w usłudze Application Insights.

    Opublikuj aplikację, aby monitorować skuteczność aplikacji i dowiedzieć się, co użytkownicy robią z twoją aplikacją.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Uwzględnianie identyfikatora użytkownika i sesji w danych telemetrycznych
Aby śledzić użytkowników w czasie, usługa Application Insights wymaga sposobu ich identyfikacji. Narzędzie Zdarzenia jest jedynym narzędziem użycia, które nie wymaga identyfikatora użytkownika ani identyfikatora sesji.

Rozpocznij wysyłanie identyfikatorów użytkowników i sesji za pomocą [tego procesu](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Poznaj dane demograficzne i statystyki użycia
Dowiedz się, kiedy użytkownicy korzystają z Twojej aplikacji, jakie strony są najbardziej zainteresowani, gdzie znajdują się Twoi użytkownicy, jakich przeglądarek i systemów operacyjnych używają. 

Raporty Użytkownicy i sesje filtrują dane według stron lub zdarzeń niestandardowych i segmentują je według właściwości, takich jak lokalizacja, środowisko i strona. Można również dodać własne filtry.

![Użytkownicy](./media/usage-overview/users.png)  

Spostrzeżenia po prawej stronie wskazują interesujące wzorce w zestawie danych.  

* Raport **Użytkownicy** zlicza liczbę unikatowych użytkowników, którzy uzyskują dostęp do twoich stron w wybranych okresach. W przypadku aplikacji internetowych użytkownicy są liczeni za pomocą plików cookie. Jeśli ktoś uzyskuje dostęp do Twojej witryny za pomocą różnych przeglądarek lub maszyn klienckich lub czyści pliki cookie, zostanie on policzony więcej niż raz.
* Raport **Sesje** zlicza liczbę sesji użytkowników, które uzyskują dostęp do witryny. Sesja to okres aktywności użytkownika, zakończony okresem braku aktywności dłuższym niż pół godziny.

[Więcej informacji o narzędziach Użytkownicy, Sesje i Wydarzenia](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retencja - ilu użytkowników wraca?

Przechowywanie pomaga zrozumieć, jak często użytkownicy wracają do korzystania z aplikacji, na podstawie kohort użytkowników, którzy wykonali pewne działania biznesowe w określonym czasie zasobniku. 

- Dowiedz się, jakie konkretne funkcje powodują, że użytkownicy wracają bardziej niż inni 
- Tworzenie hipotez na podstawie rzeczywistych danych użytkownika 
- Określanie, czy przechowywanie jest problemem w produkcie 

![Przechowywanie](./media/usage-overview/retention.png) 

Formanty przechowywania na górze pozwalają zdefiniować określone zdarzenia i zakres czasu do obliczania przechowywania. Wykres w środku daje wizualną reprezentację ogólnej wartości procentowej retencji przez określony zakres czasu. Wykres na dole reprezentuje indywidualne przechowywania w danym okresie czasu. Ten poziom szczegółowości pozwala zrozumieć, co użytkownicy robią i co może mieć wpływ na powracających użytkowników na bardziej szczegółowe ziarnistość.  

[Więcej informacji o narzędziu Retencja](usage-retention.md)

## <a name="custom-business-events"></a>Niestandardowe wydarzenia biznesowe

Aby uzyskać jasne zrozumienie, co użytkownicy robią z aplikacją, warto wstawić wiersze kodu do rejestrowania zdarzeń niestandardowych. Zdarzenia te mogą śledzić wszystko, od szczegółowych działań użytkownika, takich jak klikanie określonych przycisków, po bardziej znaczące wydarzenia biznesowe, takie jak dokonanie zakupu lub wygranie gry. 

Chociaż w niektórych przypadkach widoki strony mogą reprezentować przydatne zdarzenia, ogólnie nie jest to prawdą. Użytkownik może otworzyć stronę produktu bez kupowania produktu. 

W zależności od wydarzeń biznesowych możesz wytyczyć postęp użytkowników w witrynie. Możesz dowiedzieć się ich preferencje dla różnych opcji, i gdzie wypadają lub mają trudności. Dzięki tej wiedzy można podejmować świadome decyzje dotyczące priorytetów w zaległościach deweloperskich.

Zdarzenia mogą być rejestrowane od strony klienta aplikacji:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Albo od strony serwera:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Wartości właściwości można dołączyć do tych zdarzeń, dzięki czemu można filtrować lub dzielić zdarzenia podczas ich sprawdzania w portalu. Ponadto do każdego zdarzenia dołączony jest standardowy zestaw właściwości, takich jak anonimowy identyfikator użytkownika, który umożliwia śledzenie sekwencji działań poszczególnych użytkowników.

Dowiedz się więcej o zdarzeniach i [właściwościach](../../azure-monitor/app/api-custom-events-metrics.md#properties) [niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) .

### <a name="slice-and-dice-events"></a>Wydarzenia w plasterkach i kościach

W narzędziach Użytkownicy, Sesje i Zdarzenia można ciąć i kdykować zdarzenia niestandardowe według użytkownika, nazwy zdarzenia i właściwości.
![Użytkownicy](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Projektowanie danych telemetrycznych za pomocą aplikacji

Podczas projektowania każdej funkcji aplikacji, należy wziąć pod uwagę, jak zamierzasz zmierzyć jej sukces z użytkownikami. Zdecyduj, jakie zdarzenia biznesowe musisz rejestrować, i koduj wywołania śledzenia tych zdarzeń w aplikacji od samego początku.

## <a name="a--b-testing"></a>A | B Testowanie
Jeśli nie wiesz, który wariant funkcji będzie bardziej udany, zwolnij oba z nich, dzięki czemu każdy z nich będzie dostępny dla różnych użytkowników. Zmierz sukces każdego z nich, a następnie przejdź do ujednoliconej wersji.

Dla tej techniki należy dołączyć różne wartości właściwości do wszystkich danych telemetrycznych, które są wysyłane przez każdą wersję aplikacji. Można to zrobić, definiując właściwości w aktywnym TelemetryContext. Te domyślne właściwości są dodawane do każdego komunikatu telemetrycznego, który wysyła aplikacja — nie tylko wiadomości niestandardowe, ale także standardowe dane telemetryczne.

W portalu usługi Application Insights filtruj i dziel dane na wartości właściwości, aby porównać różne wersje.

Aby to zrobić, [skonfiguruj inicjatora telemetrii:](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**Aplikacje ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

W aplikacji sieci web inicjatora, takich jak Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplikacje platformy ASP.NET Core**

> [!NOTE]
> Dodawanie inicjatora `ApplicationInsights.config` przy `TelemetryConfiguration.Active` użyciu lub przy użyciu jest nieprawidłowe dla aplikacji ASP.NET Core. 

Dla [aplikacji ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) dodawanie `TelemetryInitializer` nowego odbywa się przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Odbywa się `ConfigureServices` to w `Startup.cs` metodzie swojej klasy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Wszystkie nowe TelemetryClients automatycznie dodać wartość właściwości, które określisz. Poszczególne zdarzenia telemetryczne mogą zastąpić wartości domyślne.

## <a name="next-steps"></a>Następne kroki
   - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
   - [Lejki](usage-funnels.md)
   - [Przechowywanie](usage-retention.md)
   - [User Flows (Przepływy użytkowników)](usage-flows.md)
   - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
   - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
