---
title: Usługa Azure Application Insights dla aplikacji internetowych w języku JavaScript | Microsoft Docs
description: Pobieranie liczników wyświetleń stron i sesji, danych klienta sieci Web oraz śledzenie wzorców użycia. Wykrywanie wyjątków i problemów z wydajnością na stronach sieci Web w języku JavaScript.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2019
ms.openlocfilehash: 17765910b379bd4212d171cce6643de561db23ad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819381"
---
# <a name="application-insights-for-web-pages"></a>Usługa Application Insights dla stron sieci Web

Poznaj wydajność i użycie strony sieci Web lub aplikacji. Jeśli dodasz [Application Insights](app-insights-overview.md) do skryptu strony, uzyskasz chronometraż ładowania strony i wywołań AJAX, liczniki i szczegóły dotyczące wyjątków przeglądarki i błędów AJAX, a także liczby użytkowników i sesji. Wszystkie te dane możesz rozdzielić według strony, systemu operacyjnego klienta i wersji przeglądarki, lokalizacji geograficznej i innych wymiarów. Możesz ustawić alerty związane z liczbami błędów lub powolnym ładowaniem strony. A wstawiając wywołania śledzenia w kodzie JavaScript, możesz śledzić sposób użycia różnych funkcji aplikacji strony sieci Web.

Usługi Application Insights można używać z dowolnymi stronami sieci Web — wystarczy dodać krótki fragment kodu JavaScript. Jeśli usługa sieci Web jest w [języku Java](java-get-started.md) lub [ASP.NET](asp-net.md), można użyć zestawów SDK po stronie serwera w połączeniu z zestawem JavaScript SDK po stronie klienta, aby uzyskać kompleksowe zrozumienie wydajności aplikacji.

## <a name="adding-the-javascript-sdk"></a>Dodawanie zestawu SDK języka JavaScript

1. Najpierw potrzebujesz zasobu Application Insights. Jeśli nie masz jeszcze klucza zasobu i instrumentacji, postępuj zgodnie z [instrukcjami dotyczącymi tworzenia nowego zasobu](create-new-resource.md).
2. Skopiuj klucz Instrumentacji z zasobu, w którym chcesz wysłać dane telemetryczne języka JavaScript.
3. Dodaj Application Insights JavaScript SDK do strony lub aplikacji sieci Web przy użyciu jednej z następujących dwóch opcji:
    * [Konfiguracja npm](#npm-based-setup)
    * [Fragment kodu JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Musisz użyć jednej z poniższych metod, aby dodać do aplikacji Application Insights zestaw SDK języka JavaScript. W przypadku korzystania z konfiguracji opartej na npm nie należy używać Instalatora opartego na fragmentach kodu. Ten sam przebiega w odniesieniu do scenariusza zwrotnego przy użyciu podejścia opartego na fragmentach kodu. nie należy również używać konfiguracji opartej na npm. 

### <a name="npm-based-setup"></a>Konfiguracja oparta na npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>Konfiguracja oparta na fragmentach kodu

Jeśli aplikacja nie korzysta z npm, możesz bezpośrednio instrumentować strony sieci Web za pomocą Application Insights, wklejając ten fragment w górnej części każdej strony. Najlepiej, gdy powinien to być pierwszy skrypt w sekcji `<head>`, aby można było monitorować ewentualne potencjalne problemy ze wszystkimi zależnościami. Jeśli używasz aplikacji Blazor Server, Dodaj fragment kodu w górnej części pliku `_Host.cshtml` w sekcji `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Wysyłanie danych telemetrycznych do Azure Portal

Domyślnie Application Insights zestaw SDK języka JavaScript zbiera wiele elementów telemetrycznych, które są przydatne podczas określania kondycji aplikacji i środowiska użytkownika. Należą do nich:

- **Nieprzechwycone wyjątki** w aplikacji, w tym informacje o
    - Ślad stosu
    - Szczegóły wyjątku i komunikat z błędem
    - Numer kolumny & wiersza (error)
    - Adres URL, pod który został zgłoszony błąd
- Żądania **zależności sieci** wykonane przez aplikację **XHR** i **Fetch** (pobieranie kolekcji jest wyłączone domyślnie) żądania, zawierają informacje
    - Adres URL źródła zależności
    - Metoda & polecenia używana do żądania zależności
    - Czas trwania żądania
    - Kod wyniku i stan powodzenia żądania
    - Identyfikator (jeśli istnieje) użytkownika wysyłającego żądanie
    - Kontekst korelacji (jeśli istnieje), w którym jest wykonywane żądanie
- **Informacje o użytkowniku** (na przykład lokalizacja, Sieć, adres IP)
- **Informacje o urządzeniu** (na przykład przeglądarka, system operacyjny, wersja, język, rozwiązanie, model)
- **Informacje o sesji**

### <a name="telemetry-initializers"></a>Inicjatory telemetrii
Inicjatory telemetrii są używane do modyfikowania zawartości zebranej telemetrii przed wysłaniem z przeglądarki użytkownika. Można ich również użyć, aby zapobiec wysyłaniu pewnych danych telemetrycznych przez zwrócenie `false`. Do wystąpienia Application Insights można dodać wiele inicjatorów telemetrii i są one wykonywane w celu ich dodania.

Argument wejściowy do `addTelemetryInitializer` jest wywołaniem zwrotnym, które przyjmuje [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API.md#addTelemetryInitializer) jako argument i zwraca `boolean` lub `void`. W przypadku zwrócenia `false` element telemetrii nie jest wysyłany, w przeciwnym razie przechodzi do następnego inicjatora telemetrii, jeśli istnieje, lub jest wysyłany do punktu końcowego zbierania danych telemetrycznych.

Przykład użycia inicjatorów telemetrii:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Konfigurowanie
Większość pól konfiguracji ma takie nazwy, że można je domyślnie określić jako FAŁSZ. Wszystkie pola są opcjonalne z wyjątkiem `instrumentationKey`.

| Nazwa | Domyślne | Opis |
|------|---------|-------------|
| instrumentationKey | null | **Wymagane**<br>Klucz Instrumentacji uzyskany w Azure Portal. |
| accountId | null | Opcjonalny identyfikator konta, jeśli aplikacja grupuje użytkowników na kontach. Bez spacji, przecinków, średników, równości lub pionowych słupków |
| sessionRenewalMs | 1800000 | Sesja jest rejestrowana, jeśli użytkownik jest nieaktywny przez ten czas w milisekundach. Wartość domyślna to 30 minut |
| sessionExpirationMs | 86400000 | Sesja jest rejestrowana, jeśli będzie trwać przez ten czas w milisekundach. Wartość domyślna to 24 godziny |
| maxBatchSizeInBytes | 10 000 | Maksymalny rozmiar wsadu danych telemetrycznych. Jeśli partia przekracza ten limit, zostanie ona natychmiast wysłana i zostanie uruchomiona nowa Partia zadań |
| maxBatchInterval | 15000 | Jak długo należy wykonać partię danych telemetrycznych przed wysłaniem (w milisekundach) |
| disableExceptionTracking | false | W przypadku wartości true wyjątki nie są zbierane. Wartość domyślna to false. |
| disableTelemetry | false | Jeśli wartość jest równa true, dane telemetryczne nie są zbierane ani wysyłane. Wartość domyślna to false. |
| enableDebug | false | W przypadku wartości true dane debugowania **wewnętrznego** są generowane jako wyjątek **zamiast** rejestrowania, niezależnie od ustawień rejestrowania zestawu SDK. Wartość domyślna to false. <br>***Uwaga:*** Włączenie tego ustawienia spowoduje porzucenie danych telemetrycznych w przypadku wystąpienia błędu wewnętrznego. Może to być przydatne do szybkiego identyfikowania problemów z konfiguracją lub użyciem zestawu SDK. Jeśli nie chcesz utracić danych telemetrycznych podczas debugowania, rozważ użycie `consoleLoggingLevel` lub `telemetryLoggingLevel` zamiast `enableDebug`. |
| loggingLevelConsole | 0 | Rejestruje **wewnętrzne** błędy Application Insights w konsoli programu. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) |
| loggingLevelTelemetry | 1 | Wysyła **wewnętrzne** błędy Application Insights jako dane telemetryczne. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) |
| diagnosticLogInterval | 10 000 | wewnętrz Interwał sondowania (w MS) dla wewnętrznej kolejki rejestrowania |
| samplingPercentage | 100 | Procent zdarzeń, które będą wysyłane. Wartość domyślna to 100, co oznacza, że wszystkie zdarzenia są wysyłane. Ustaw tę opcję, jeśli chcesz zachować limit danych dla aplikacji o dużej skali. |
| autoTrackPageVisitTime | false | Jeśli wartość jest równa true, w pageview czas wyświetlania poprzedniej strony z instrumentacją jest śledzony i wysyłany jako Telemetria i uruchamiany jest nowy czasomierz dla bieżącego pageview. Wartość domyślna to false. |
| disableAjaxTracking | false | W przypadku wartości true wywołania AJAX nie są autozbierane. Wartość domyślna to false. |
| disableFetchTracking | true | W przypadku wartości true żądania pobrania nie są gromadzone w sposób autozbierany. Wartość domyślna to true |
| overridePageViewDuration | false | Jeśli wartość jest równa true, domyślne zachowanie trackPageView jest zmieniane na koniec interwału czasu trwania wyświetlania strony w przypadku wywołania trackPageView. Jeśli wartość jest równa false i nie ma niestandardowego czasu trwania do trackPageView, wydajność wyświetlania stron jest obliczana przy użyciu interfejsu API czasu nawigacji. Wartość domyślna to false. |
| maxAjaxCallsPerView | 500 | Domyślnie 500 — określa, ile wywołań AJAX będzie monitorowanych dla każdego widoku strony. Ustaw wartość-1, aby monitorować wszystkie (nieograniczone) wywołania AJAX na stronie. |
| disableDataLossAnalysis | true | W przypadku wartości false bufory wewnętrznych nadawców telemetrii będą sprawdzane przy uruchamianiu elementów, które nie zostały jeszcze wysłane. |
| disableCorrelationHeaders | false | W przypadku wartości false zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań zależności w celu skorelowania ich z odpowiednimi żądaniami po stronie serwera. Wartość domyślna to false. |
| correlationHeaderExcludedDomains |  | Wyłącz nagłówki korelacji dla określonych domen |
| correlationHeaderDomains |  | Włącz nagłówki korelacji dla określonych domen |
| disableFlushOnBeforeUnload | false | Wartość domyślna to false. W przypadku wartości true Metoda Flush nie zostanie wywołana, gdy wyzwalane są wyzwalacze zdarzeń onBeforeUnload |
| enableSessionStorageBuffer | true | Wartość domyślna to true. W przypadku wartości true bufor z całą niewysłaną telemetrię jest przechowywany w magazynie sesji. Bufor jest przywracany podczas ładowania strony |
| isCookieUseDisabled | false | Wartość domyślna to false. Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywać ani odczytywać żadnych danych z plików cookie.|
| cookieDomain | null | Niestandardowa domena plików cookie. Jest to przydatne, jeśli chcesz udostępnić pliki cookie Application Insights w poddomenach. |
| isRetryDisabled | false | Wartość domyślna to false. W przypadku wartości false ponów próbę 206 (częściowe powodzenie), 408 (limit czasu), 429 (zbyt wiele żądań), 500 (wewnętrzny błąd serwera), 503 (Usługa niedostępna) i 0 (offline, tylko jeśli wykryto) |
| isStorageUseDisabled | false | Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywał ani nie odczytał żadnych danych z magazynu lokalnego i w ramach sesji. Wartość domyślna to false. |
| isBeaconApiDisabled | true | Jeśli wartość jest równa false, zestaw SDK wyśle wszystkie dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Wartość domyślna to false. gdy karta zostanie ZAMKNIĘTA, zestaw SDK wyśle wszystkie pozostałe dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Ustawia nazwę rozszerzenia zestawu SDK. Dozwolone są tylko znaki alfanumeryczne. Nazwa rozszerzenia jest dodawana jako prefiks do tagu "AI. Internal. sdkVersion" (na przykład "ext_javascript: 2.0.0"). Wartość domyślna to null. |
| isBrowserLinkTrackingEnabled | false | Wartość domyślna to false. Jeśli wartość jest równa true, zestaw SDK będzie śledził wszystkie żądania [linku do przeglądarki](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) . |
| appId | null | Identyfikator AppId jest używany dla korelacji między zależnościami AJAX, które są wykonywane po stronie klienta z żądaniami po stronie serwera. Gdy interfejs API sygnałów jest włączony, nie można go użyć automatycznie, ale można ustawić go ręcznie w konfiguracji. Wartość domyślna to null |
| enableCorsCorrelation | false | Jeśli wartość jest równa true, zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań CORS do skorelowania wychodzących zależności AJAX z odpowiednimi żądaniami po stronie serwera. Wartość domyślna to false |
| namePrefix | definicji | Opcjonalna wartość, która będzie używana jako przyrostkowa nazwa dla localStorage i nazwy pliku cookie.
| enableAutoRouteTracking | false | Automatyczne śledzenie zmian trasy w aplikacjach jednostronicowych (SPA). Jeśli wartość jest równa true, każda zmiana trasy wyśle nowy pageview do Application Insights. Zmiany trasy skrótu (`example.com/foo#bar`) są również rejestrowane jako nowe widoki stron.
| enableRequestHeaderTracking | false | W przypadku wartości true są śledzone nagłówki żądań dla programu AJAX &. wartość domyślna to false.
| enableResponseHeaderTracking | false | W przypadku wartości true są śledzone nagłówki odpowiedzi żądania pobrania & AJAX, wartość domyślna to false.
| distributedTracingMode | `DistributedTracingModes.AI` | Ustawia tryb śledzenia rozproszonego. Jeśli ustawiono tryb AI_AND_W3C lub W3C, nagłówki kontekstowe śledzenia W3C (traceparent/tracestate) zostaną wygenerowane i uwzględnione we wszystkich żądaniach wychodzących. AI_AND_W3C zapewnia zgodność z poprzednimi wersjami z dowolnymi starszymi usługami Application Insights Instrumentacją.

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Domyślnie ten zestaw SDK **nie** będzie obsługiwał zmiany trasy opartej na stanie, która występuje w aplikacjach jednostronicowych. Aby włączyć automatyczne śledzenie zmian trasy dla aplikacji jednostronicowej, można dodać `enableAutoRouteTracking: true` do konfiguracji instalacji.

Obecnie oferujemy osobne wtyczki do [reagowania](#react-extensions) , które można zainicjować za pomocą tego zestawu SDK. Spowoduje to również przekazanie śledzenia zmian trasy, a także zebranie [innych danych telemetrycznych związanych z reagowaniem](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Przereaguj rozszerzenia

| Rozszerzenia |
|---------------|
| [Biern](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Zareaguj na natywny](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Eksplorowanie danych po stronie przeglądarki i klienta

Dane przeglądarki/strony klienta można wyświetlać, przechodząc do **metryk** i dodając indywidualne metryki, które Cię interesują: 

![](./media/javascript/page-view-load-time.png)

Możesz również wyświetlać dane z zestawu SDK języka JavaScript za pomocą środowiska przeglądarki w portalu.

Wybierz pozycję **przeglądarka** , a następnie wybierz pozycję **Błędy** lub **wydajność**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Wydajność 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Zależności

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analiza 

Aby wysłać zapytanie do telemetrii zebranej przez zestaw JavaScript SDK, wybierz przycisk **Wyświetl w dziennikach (analiza)** . Dodanie `where` instrukcji `client_Type == "Browser"`powoduje, że będą widoczne tylko dane z zestawu SDK języka JavaScript, a wszystkie Telemetria po stronie serwera zebrane przez inne zestawy SDK zostaną wykluczone.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Obsługa mapy źródłowej

Zminimalizowanego stosu wywołań danych telemetrycznych dotyczących wyjątków może być unminified w Azure Portal. Wszystkie istniejące integracje w panelu Szczegóły wyjątku będą działały z nowo unminified stosu wywołań. Przeciąganie i upuszczanie mapy źródłowej unminifying obsługuje wszystkie istniejące i przyszłe zestawy SDK JS (+ Node. JS), więc nie trzeba uaktualniać wersji zestawu SDK. Aby wyświetlić unminified stosu wywołań,
1. Wybierz element telemetrii wyjątku w Azure Portal, aby wyświetlić jego "kompleksowe" Szczegóły transakcji.
2. Określ, które mapy źródłowe odpowiadają temu stosowi wywołań. Mapa źródłowa musi być zgodna z plikiem źródłowym ramki stosu, ale z sufiksem `.map`
3. Przeciągnij i upuść mapy źródłowe na stos wywołań w Azure Portal ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Aby zapoznać się z lekkim doświadczeniem, możesz zamiast tego zainstalować wersję podstawową Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ta wersja zawiera minimalną liczbę funkcji i funkcjonalności systemu operacyjnego i polega na tym, że można ją skompilować w taki sam sposób, jak to możliwe. Na przykład nie wykonuje autokolekcjonowania (nieprzechwycone wyjątki, AJAX itp.). Interfejsy API służące do wysyłania określonych typów telemetrii, takich jak `trackTrace`, `trackException`, itp., nie są uwzględnione w tej wersji, więc trzeba będzie udostępnić własne otoki. Jedynym dostępnym interfejsem API jest `track`. [Przykład](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) znajduje się tutaj.

## <a name="examples"></a>Przykłady

Przykłady możliwy do uruchomienia można znaleźć w temacie [Application Insights przykłady kodu JavaScript SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uaktualnianie ze starszej wersji Application Insights

Istotne zmiany w wersji zestawu SDK V2:
- Aby umożliwić lepsze sygnatury interfejsu API, niektóre wywołania interfejsu API, takie jak trackPageView, trackexception, zostały zaktualizowane. Uruchamianie w programie IE8 lub w niższych wersjach przeglądarki nie jest obsługiwane.
- Koperta telemetrii ma zmiany nazwy pola i struktury ze względu na aktualizacje schematu danych.
- Przeniesiono `context.operation` do `context.telemetryTrace`. Niektóre pola zostały również zmienione (`operation.id` --> `telemetryTrace.traceID`)
  - Jeśli chcesz ręcznie odświeżyć bieżący identyfikator pageview (na przykład w aplikacjach SPA), można to zrobić za pomocą `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`

Jeśli używasz bieżącego zestawu SDK PRODUKCYJNEgo usługi Application Insights (1.0.20) i chcesz sprawdzić, czy nowy zestaw SDK działa w środowisku uruchomieniowym, zaktualizuj adres URL w zależności od bieżącego scenariusza ładowania zestawu SDK.

- Pobierz za pośrednictwem scenariusza usługi CDN: zaktualizuj fragment kodu, którego obecnie używasz, aby wskazać następujący adres URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- scenariusz npm: Wywołaj `downloadAndSetup`, aby pobrać pełny skrypt ApplicationInsights z sieci CDN i zainicjuj go za pomocą klucza Instrumentacji:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test w środowisku wewnętrznym, aby sprawdzić, czy dane telemetryczne monitorowania działają zgodnie z oczekiwaniami. Jeśli wszystko działa, zaktualizuj podpisy interfejsów API odpowiednio do wersji zestawu SDK V2 i Wdróż ją w środowiskach produkcyjnych.

## <a name="sdk-performanceoverhead"></a>Wydajność/obciążenie zestawu SDK

Po zaledwie 25 KB formacie gzip i zainicjowaniu tylko ~ 15 MS, Application Insights dodaje nieznaczną ilość loadtime do witryny sieci Web. Za pomocą fragmentu kodu można szybko ładować minimalne składniki biblioteki. W międzyczasie pełny skrypt zostanie pobrany w tle.

Podczas pobierania skryptu z usługi CDN wszystkie śledzenie strony jest umieszczane w kolejce. Gdy pobrany skrypt zakończy asynchroniczne inicjowanie, wszystkie zdarzenia, które zostały dodane do kolejki, są śledzone. W związku z tym nie utracisz żadnych danych telemetrycznych w całym cyklu życia strony. Ten proces instalacji zapewnia użytkownikom niezauważalny system, niewidoczny dla użytkowników.

> Podsumowanie
> - **25 KB** formacie gzip
> - Całkowity czas inicjowania ( **15 MS** )
> - Brak śledzenia **zerowej** w cyklu życia strony

## <a name="browser-support"></a>Obsługa przeglądarki

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Najnowsza ✔ | Najnowsza ✔ | 9 + ✔ | Najnowsza ✔ | Najnowsza ✔ |

## <a name="open-source-sdk"></a>Zestaw SDK open source

Application Insights zestawu SDK języka JavaScript jest otwartym źródłem do wyświetlania kodu źródłowego lub do współtworzenia projektu, odwiedzając [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Następne kroki
* [Śledzenie użycia](usage-overview.md)
* [Niestandardowe zdarzenia i metryki](api-custom-events-metrics.md)
* [Tworzenie — pomiar— nauka](usage-overview.md)

