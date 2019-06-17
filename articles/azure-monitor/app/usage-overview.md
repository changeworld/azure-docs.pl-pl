---
title: Analiza użycia za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, użytkownicy i co robią z aplikacją.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: f2539d5250ff436a720fe10f748f40db29b0ee25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60783430"
---
# <a name="usage-analysis-with-application-insights"></a>Analiza użycia za pomocą usługi Application Insights

Które funkcje aplikacji mobilnej lub sieci web, na których są najbardziej popularne? Czy użytkownicy osiągnąć cele związane z Twoją aplikacją? Czy rezygnują w szczególności punktach i są one później powrócić?  [Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) pomaga uzyskać pełen wgląd w jaki sposób użytkownicy używają Twojej aplikacji. Za każdym razem, gdy aktualizujesz aplikację, możesz ocenić, jak dobrze działa w przypadku użytkowników. Za pomocą tej wiedzy ułatwia decyzje dotyczące następnego cykle rozwojowe w oparciu o dane.

## <a name="send-telemetry-from-your-app"></a>Wysyłanie danych telemetrycznych z Twojej aplikacji

Aby uzyskiwać najlepsze wyniki są uzyskiwane przez zainstalowanie usługi Application Insights, zarówno w kodzie serwera aplikacji, jak i na stronach sieci web. Składniki klienta i serwera aplikacji wysyłają dane telemetryczne do portalu Azure w celu analizy.

1. **Kod serwera:** Zainstaluj moduł odpowiednie dla Twojego [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md), lub [innych](../../azure-monitor/app/platforms.md) aplikacja.

    * *Nie chcesz zainstalować kod serwera? Po prostu [Utwórz zasób usługi Azure Application Insights](../../azure-monitor/app/create-new-resource.md ).*

2. **Kodu strony sieci Web:** Dodaj następujący skrypt do strony sieci web przed tagiem zamykającym ``</head>``. Zastąp klucz Instrumentacji wartość odpowiednią dla zasobu usługi Application Insights:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Aby zapoznać się z bardziej zaawansowanymi konfiguracjami do monitorowania witryn internetowych, zobacz [dokumentacja interfejsu API zestawu SDK języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Kod aplikacji mobilnej:** Użyj zestawu SDK Centrum aplikacji, aby zbierać zdarzenia z aplikacji, a następnie wysłać kopii tych zdarzeń do usługi Application Insights do analizy przez [wykonaj czynności przedstawione w tym przewodniku](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Pobierz dane telemetryczne:** Uruchamianie projektu w trybie debugowania przez kilka minut, a następnie poszukaj wyników w bloku przeglądu w usłudze Application Insights.

    Opublikuj aplikację w celu monitorowania wydajności aplikacji i dowiedzieć się, co użytkownicy robią z aplikacją.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Dodać użytkowników i sesji identyfikator telemetrii
Aby śledzić użytkowników wraz z upływem czasu, usługa Application Insights wymaga sposób umożliwiający ich zidentyfikowanie. Narzędzie zdarzeń jest jedynym narzędziem użycia, które nie wymagają Identyfikatora użytkownika lub identyfikator sesji.

Rozpocznij wysyłanie użytkowników i identyfikatorów sesji przy użyciu [ten proces](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Eksploruj dane demograficzne użycia i statystyk
Dowiedz się, gdy użytkownicy używają Twojej aplikacji, które strony są najbardziej interesują, gdzie są Twoi użytkownicy, jakie przeglądarek i systemów operacyjnych używają. 

Raporty użytkowników i sesji filtrowania danych, stron lub zdarzeń niestandardowych i podzielić je za pomocą właściwości, takie jak lokalizacja, środowiska i strony. Można również dodać własne filtry.

![Użytkownicy](./media/usage-overview/users.png)  

Szczegółowe informacje po prawej stronie punktu interesujących wzorców w zestawie danych.  

* **Użytkowników** raport Zlicza liczby unikatowych użytkowników uzyskujących dostęp do stron sieci w ramach Twojej wybrany czas. W przypadku aplikacji sieci web użytkownicy są liczone za pomocą plików cookie. Jeśli ktoś uzyskuje dostęp do witryny przy użyciu innej przeglądarki lub komputerów klienckich lub czyści ich pliki cookie, następnie one będą zliczane więcej niż jeden raz.
* **Sesje** raport zlicza liczbę sesji użytkowników, uzyskujących dostęp do witryny. Sesja jest okres aktywności przez użytkownika został przerwany przez okres bezczynności ponad pół godziny.

[Więcej informacji na temat narzędzia Użytkownicy, sesje i zdarzenia](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Przechowywanie — ilu użytkowników możesz wrócić?

Przechowywanie pomaga zrozumieć, jak często użytkownicy wraca do używania aplikacji w oparciu o kohorty użytkowników, którzy będą wykonywane niektóre akcje biznesowych w przedziale czasowym. 

- Zrozumienie, jakie określonych funkcji spowodować, że użytkownicy są ponownie więcej niż inne 
- Hipotezy formularza na podstawie rzeczywistego użytkownika danych 
- Ustal, czy przechowywania jest problem z produktu 

![Przechowywanie](./media/usage-overview/retention.png) 

Formanty przechowywania na górze umożliwiają definiowanie określonych zdarzeń i zakres czasu, aby obliczyć przechowywania. Wykresu w środkowej części oferuje wizualną reprezentację ogólny procent przechowywania przez określony zakres czasu. Wykres u dołu reprezentuje poszczególne przechowywania w danym okresie czasu. Taki poziom szczegółowości pozwala zrozumieć, co robią użytkownicy i co może mieć wpływ na użytkowników zwracanych w bardziej szczegółowy poziom szczegółowości.  

[Więcej informacji na temat narzędzia przechowywanie](usage-retention.md)

## <a name="custom-business-events"></a>Zdarzenia niestandardowe biznesowe

Aby zrozumieć co użytkownicy robią aplikacji, jest przydatne wstawić wiersze kodu do rejestrowania zdarzeń niestandardowych. Zdarzenia te można śledzić żadnych użytkownika szczegółowe akcje, takie jak kliknięcie określonego przycisków bardziej znaczące zdarzeń biznesowych, takich jak dokonaniem zakupu lub winning grę. 

Chociaż w niektórych przypadkach, wyświetleń stron może reprezentować przydatne zdarzeń, nie jest wartość true, ogólnie rzecz biorąc. Użytkownik może otworzyć stronę produktu bez kupowania produktu. 

Ze zdarzeniami firmy mogą wykresu postępu użytkowników za pośrednictwem swojej witryny. Można sprawdzić swoje preferencje dotyczące różnych opcji i w przypadku, gdy ich porzucić out lub ma trudności. Za pomocą tej wiedzy ułatwia świadomych decyzji dotyczących priorytety w swojej zaległości rozwoju.

Zdarzenia mogą być rejestrowane po stronie klienta aplikacji:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Lub po stronie serwera:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Wartości właściwości może dołączać do tych zdarzeń, tak, aby filtrować lub podzielić zdarzenia inspekcji w portalu. Ponadto program standardowy zestaw właściwości jest dołączany do każdego zdarzenia, takie jak identyfikator użytkownika anonimowego, co pozwala na śledzenie sekwencji działań użytkownika.

Dowiedz się więcej o [zdarzenia niestandardowe](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) i [właściwości](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Zdarzenia metod selekcji i projekcji

W narzędziach użytkownicy, sesje i zdarzenia użytkownik może kątami niestandardowe zdarzenia według użytkownika, Nazwa zdarzenia i właściwości.
![Użytkownicy](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Projekt danych telemetrycznych z aplikacji

Podczas projektowania każdej funkcji aplikacji, należy wziąć pod uwagę, jak ma być mierzony jej sukces z użytkownikami. Zdecyduj, jakie zdarzenia biznesowe, które trzeba zarejestrować i kodu, śledzenia wywołań dla tych zdarzeń w swojej aplikacji od samego początku.

## <a name="a--b-testing"></a>A | Testowanie B
Jeśli nie wiesz, który wariant funkcji będzie łatwiejsza, zwolnij obu z nich, dzięki czemu każdy dostępne dla różnych użytkowników. Ma być mierzony sukces każdego z nich, a następnie przesuń do ujednoliconego wersji.

Tej techniki wartości różnych właściwości dołączyć się do wszystkich danych telemetrycznych wysyłanych przez każdą wersję aplikacji. Możesz tworzyć, definiując właściwości w active TelemetryContext. Te właściwości domyślne są dodawane do każdego komunikaty telemetryczne wysyłający aplikacji — nie tylko komunikaty niestandardowe, ale także standardowa telemetria.

W portalu Application Insights filtrować i podzielić dane według wartości właściwości, tak aby porównywać różne wersje.

Aby to zrobić, [skonfigurować inicjator telemetrii](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

W inicjatorze aplikacji sieci web takich jak Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Wszystkie nowe TelemetryClients automatyczne dodanie wartości właściwości, które określisz. Zdarzenia telemetrii mogą zastępować wartości domyślne.

## <a name="next-steps"></a>Kolejne kroki
   - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
   - [Lejki](usage-funnels.md)
   - [Przechowywanie](usage-retention.md)
   - [User Flows (Przepływy użytkowników)](usage-flows.md)
   - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
   - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
