---
title: Usługa Azure Application Insights dla aplikacji javascript
description: Pobierz liczbę wyświetleń strony i sesji, dane klienta sieci Web, aplikacje jednostronicowe (SPA) i śledzenie wzorców użycia. Wykrywanie wyjątków i problemów z wydajnością na stronach sieci Web w języku JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276077"
---
# <a name="application-insights-for-web-pages"></a>Usługa Application Insights dla stron sieci Web

Poznaj wydajność i użycie strony sieci Web lub aplikacji. Jeśli dodasz [usługi Application Insights](app-insights-overview.md) do skryptu strony, otrzymasz chronometrażu wczytywanych stron i wywołań AJAX, liczby i szczegóły wyjątków przeglądarki i awarii AJAX, a także użytkowników i liczby sesji. Wszystkie te dane możesz rozdzielić według strony, systemu operacyjnego klienta i wersji przeglądarki, lokalizacji geograficznej i innych wymiarów. Możesz ustawić alerty związane z liczbami błędów lub powolnym ładowaniem strony. A wstawiając wywołania śledzenia w kodzie JavaScript, możesz śledzić sposób użycia różnych funkcji aplikacji strony sieci Web.

Usługi Application Insights można używać z dowolnymi stronami sieci Web — wystarczy dodać krótki fragment kodu JavaScript. Jeśli usługa sieci Web to [Java](java-get-started.md) lub [ASP.NET,](asp-net.md)można użyć sdk po stronie serwera w połączeniu z interfejsem SDK JavaScript po stronie klienta, aby uzyskać kompleksową wiedzę na temat wydajności aplikacji.

## <a name="adding-the-javascript-sdk"></a>Dodawanie kodu JavaScript SDK

1. Najpierw potrzebujesz zasobu usługi Application Insights. Jeśli nie masz jeszcze klucza zasobu i instrumentacji, postępuj zgodnie z [instrukcjami tworzenia nowego zasobu](create-new-resource.md).
2. Skopiuj klucz instrumentacji z zasobu, do którego ma zostać wysłana dane telemetryczne JavaScript.
3. Dodaj sdk JavaScript aplikacji do strony internetowej lub aplikacji za pomocą jednej z następujących dwóch opcji:
    * [Konfiguracja npm](#npm-based-setup)
    * [Fragment kodu JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Użyj tylko jednej metody, aby dodać javascript SDK do aplikacji. Jeśli używasz Instalatora npm, nie należy używać fragmentu kodu i odwrotnie.

> [!NOTE]
> Instalator serwera NPM instaluje pakiet JavaScript SDK jako zależność od projektu, włączając program IntelliSense, podczas gdy urywek pobiera pakiet SDK w czasie wykonywania. Oba obsługują te same funkcje. Jednak deweloperzy, którzy pragną więcej niestandardowych zdarzeń i konfiguracji zazwyczaj wybierają konfigurację NPM, podczas gdy użytkownicy szukający szybkiego włączania out-of-the-box analityki internetowej wybierają Urywek.

### <a name="npm-based-setup"></a>konfiguracja oparta na npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Konfiguracja oparta na urywek

Jeśli aplikacja nie korzysta z npm, można bezpośrednio instrumentować strony internetowe za pomocą usługi Application Insights, wklejając ten fragment kodu u góry każdej strony. Najlepiej, powinien być pierwszym skryptem `<head>` w sekcji, dzięki czemu może monitorować wszelkie potencjalne problemy ze wszystkimi zależnościami. Jeśli używasz aplikacji Blazor Server, dodaj fragment kodu u `_Host.cshtml` góry `<head>` pliku w sekcji.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Wysyłanie danych telemetrycznych do witryny Azure portal

Domyślnie SDK JavaScript aplikacji aplikacji automatyczniezechniuje szereg elementów telemetrycznych, które są przydatne w określaniu kondycji aplikacji i podstawowego środowiska użytkownika. Należą do nich:

- **Nieprzechodnie wyjątki** w aplikacji, w tym informacje o
    - Śledzenie stosu
    - Szczegóły wyjątku i komunikat towarzyszący błędowi
    - Numer błędu & kolumny wiersza
    - Adres URL, w którym został zgłoszony błąd
- **Żądania zależności sieciowej** wykonane przez aplikację **XHR** i **Fetch** (pobieranie kolekcji jest domyślnie wyłączone), zawierają informacje na temat
    - Adres URL źródła zależności
    - Metoda & polecenia używana do żądania zależności
    - Czas trwania wniosku
    - Kod wyniku i stan sukcesu żądania
    - Identyfikator (jeśli istnieje) użytkownika składającego żądanie
    - Kontekst korelacji (jeśli istnieje) w przypadku złożenia wniosku
- **Informacje o użytkowniku** (na przykład lokalizacja, sieć, adres IP)
- **Informacje o urządzeniu** (na przykład przeglądarka, system operacyjny, wersja, język, rozdzielczość, model)
- **Informacje o sesji**

### <a name="telemetry-initializers"></a>Inicjatory telemetrii
Inicjatory telemetrii są używane do modyfikowania zawartości zebranych danych telemetrycznych przed wysłaniem z przeglądarki użytkownika. Mogą być również używane, aby zapobiec wysyłaniu niektórych danych telemetrycznych, zwracając `false`. Inicjalizatory wielu telemetrii można dodać do wystąpienia usługi Application Insights i są one wykonywane w kolejności ich dodawania.

Argument input `addTelemetryInitializer` to jest wywołaniem [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) zwrotnym, który `boolean` przyjmuje `void`jako argument i zwraca lub . Jeśli zwraca `false`, element telemetrii nie jest wysyłany, w przeciwnym razie przechodzi do następnego inicjatora telemetrii, jeśli istnieje, lub jest wysyłany do punktu końcowego kolekcji telemetryczności.

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
Większość pól konfiguracji są nazwane w taki sposób, że mogą one być domyślnie false. Wszystkie pola są `instrumentationKey`opcjonalne z wyjątkiem .

| Nazwa | Domyślne | Opis |
|------|---------|-------------|
| InstrumentacjaKlucz | wartość null | **Wymagane**<br>Klucz instrumentacji uzyskany z witryny Azure portal. |
| accountId | wartość null | Opcjonalny identyfikator konta, jeśli aplikacja grupuje użytkowników na konta. Brak spacji, przecinków, średników, równych lub pionowych pasków |
| sesjaRenewalMs | 1800000 | Sesja jest rejestrowana, jeśli użytkownik jest nieaktywny przez ten czas w milisekundach. Wartość domyślna to 30 minut |
| sesjaWyspiracjeM | 86400000 | Sesja jest rejestrowana, jeśli została kontynuowana przez ten czas w milisekundach. Wartość domyślna to 24 godziny |
| maxBatchSizeInBytes | 10 000 | Maksymalny rozmiar partii telemetrii. Jeśli partia przekroczy ten limit, jest natychmiast wysyłana i uruchamiana jest nowa partia |
| maxBatchInterval (1000000000 | 15000 | Jak długo dane telemetryczne partii przed wysłaniem (milisekundy) |
| wyłączanieśmiękkitracking | false | Jeśli true, wyjątki nie są automatycznie zespoprawki. Wartość domyślna to false. |
| disableTelemetria | false | Jeśli true, telemetria nie jest zbierane lub wysyłane. Wartość domyślna to false. |
| enableDebug | false | Jeśli true, **wewnętrzne** debugowanie danych jest zgłaszany jako wyjątek **zamiast** rejestrowane, niezależnie od ustawień rejestrowania SDK. Wartość domyślna to false. <br>***Uwaga:*** Włączenie tego ustawienia spowoduje upuszczenie danych telemetrycznych za każdym razem, gdy wystąpi błąd wewnętrzny. Może to być przydatne do szybkiego identyfikowania problemów z konfiguracją lub użyciem sdk. Jeśli nie chcesz utracić danych telemetrycznych podczas `consoleLoggingLevel` `telemetryLoggingLevel` debugowania, rozważ użycie lub zamiast `enableDebug`programu . |
| logowaniePoziomowy pochyłej | 0 | Rejestruje **wewnętrzne** błędy usługi Application Insights do konsoli. <br>0: wył., <br>1: Tylko błędy krytyczne, <br>2: Wszystko (błędy & ostrzeżenia) |
| logowanieTelemetria poziomu | 1 | Wysyła **wewnętrzne** błędy usługi Application Insights jako dane telemetryczne. <br>0: wył., <br>1: Tylko błędy krytyczne, <br>2: Wszystko (błędy & ostrzeżenia) |
| diagnosticLogInterval (Dziennik diagnostyczny) | 10 000 | (wewnętrzny) Interwał sondowania (w ms) dla wewnętrznej kolejki rejestrowania |
| pobieranie próbekPrzestłań | 100 | Procent zdarzeń, które zostaną wysłane. Wartość domyślna to 100, co oznacza, że wszystkie zdarzenia są wysyłane. Ustaw to, jeśli chcesz zachować limit danych dla aplikacji na dużą skalę. |
| autoTrackPageVisitTime | false | Jeśli true, na odsłonę strony, czas wyświetlania poprzedniej instrumentowane strony jest śledzony i wysyłany jako dane telemetryczne i nowy czasomierz jest uruchamiany dla bieżącego widoku strony. Wartość domyślna to false. |
| disableJajaxTracking | false | Jeśli to prawda, wywołania Ajax nie są automatycznie zespociane. Wartość domyślna to false. |
| disableFetchTracking (śledzenie wyłączone) | true | Jeśli true, żądania pobierania nie są automatycznie pobierane. Wartość domyślna jest true |
| zastępowaniePageViewDuration | false | Jeśli true, domyślne zachowanie trackPageView jest zmieniany do rekordu koniec interwału czasu trwania widoku strony, gdy trackPageView jest wywoływana. Jeśli false i nie niestandardowy czas trwania jest dostarczany trackPageView, wydajność widoku strony jest obliczana przy użyciu interfejsu API chronometrażu nawigacji. Wartość domyślna to false. |
| maxAjaxCallsPerView | 500 | Domyślnie 500 - określa, ile wywołań Ajax będzie monitorowane na widok strony. Ustaw na -1, aby monitorować wszystkie (nieograniczone) połączenia Ajax na stronie. |
| disableDanelossAnalysis | true | Jeśli false, bufory nadawcy wewnętrznej telemetrii będą sprawdzane podczas uruchamiania dla elementów, które nie zostały jeszcze wysłane. |
| disableCorrelationHeaders | false | Jeśli false, SDK doda dwa nagłówki ("Request-Id" i "Request-Context") do wszystkich żądań zależności, aby skorelować je z odpowiednimi żądaniami po stronie serwera. Wartość domyślna to false. |
| correlationHeaderExcludedDomains |  | Wyłączanie nagłówków korelacji dla określonych domen |
| korelacjaHeaderDomains |  | Włączanie nagłówków korelacji dla określonych domen |
| disableFlushOnBeforeUnload | false | Domyślne false. Jeśli true, flush metoda nie zostanie wywołana, gdy onBeforeUnload wyzwala zdarzenia |
| enableSessionStorageBuffer | true | Wartość domyślna true. Jeśli true, bufor ze wszystkimi niewysłane dane telemetryczne są przechowywane w magazynie sesji. Bufor zostanie przywrócony przy wczytyniu strony |
| isCookieUseDisabled | false | Domyślne false. Jeśli true, SDK nie będzie przechowywać ani odczytywać żadnych danych z plików cookie.|
| CookieDomena | wartość null | Niestandardowa domena plików cookie. Jest to przydatne, jeśli chcesz udostępniać pliki cookie usługi Application Insights w poddomenach. |
| isRetryDisabled ( IsRetryDisabled) | false | Domyślne false. Jeśli false, ponów próbę na 206 (częściowy sukces), 408 (limit czasu), 429 (zbyt wiele żądań), 500 (wewnętrzny błąd serwera), 503 (usługa niedostępna) i 0 (w trybie offline, tylko wtedy, gdy wykryto) |
| isStorageUseDisabled | false | Jeśli true, zestaw SDK nie będzie przechowywać ani odczytywać żadnych danych z magazynu lokalnego i sesji. Wartość domyślna to false. |
| isBeaconApiDisabled | true | Jeśli false, SDK wyśle wszystkie dane telemetryczne przy użyciu [interfejsu API beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | false | Domyślne false. po zamknięciu karty SDK wyśle wszystkie pozostałe dane telemetryczne za pomocą [interfejsu API sygnału nawigacyjnego](https://www.w3.org/TR/beacon) |
| sdkRozcisknięcie | wartość null | Ustawia nazwę rozszerzenia sdk. Dozwolone są tylko znaki alfabetyczne. Nazwa rozszerzenia jest dodawana jako prefiks do tagu 'ai.internal.sdkVersion' (na przykład "ext_javascript:2.0.0"). Wartość domyślna to wartość null. |
| isBrowserLinkTrackingWłasna | false | Wartość domyślna to false. Jeśli true, zestaw SDK będzie śledzić wszystkie żądania [łącza przeglądarki.](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) |
| appId | wartość null | Identyfikator AppId jest używany do korelacji między zależnościami AJAX dzieje się po stronie klienta z żądaniami po stronie serwera. Gdy interfejs API beacon jest włączony, nie może być używany automatycznie, ale można ustawić ręcznie w konfiguracji. Wartość domyślna to wartość null |
| enableCorsCorrelation (włączcorscorrelation) | false | Jeśli true, SDK doda dwa nagłówki ("Request-Id" i "Request-Context") do wszystkich żądań CORS do skorelowania wychodzących zależności AJAX z odpowiednimi żądaniami po stronie serwera. Wartość domyślna jest false |
| namePrefix | Niezdefiniowane | Opcjonalna wartość, która będzie używana jako poprawka pocztowa nazwy dla nazwy localStorage i cookie.
| enableAutoRouteTracking | false | Automatyczne śledzenie zmian trasy w aplikacjach jednostronicowych (SPA). Jeśli true, każda zmiana trasy wyśle nowy odsłonę do usługi Application Insights. Zmiany trasy skrótu (`example.com/foo#bar`) są również rejestrowane jako nowe widoki strony.
| enableRequestHeaderTracking | false | Jeśli true, AJAX & Fetch nagłówki żądania jest śledzony, domyślnie jest false.
| enableResponseHeaderTracking | false | Jeśli true, AJAX & Nagłówki odpowiedzi żądania pobierania jest śledzony, domyślnie jest false.
| distributedTracingMode | `DistributedTracingModes.AI` | Ustawia tryb śledzenia rozproszonego. Jeśli AI_AND_W3C tryb lub tryb W3C jest ustawiony, nagłówki kontekstu śledzenia W3C (traceparent/tracestate) zostaną wygenerowane i uwzględnione we wszystkich żądaniach wychodzących. AI_AND_W3C jest przewidziane dla zgodności z powrotem z wszystkich starszych usług instrumentowanych usługi usługi usługi aplikacji Insights.

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Domyślnie ten sdk **nie** będzie obsługiwać trasy oparte na stanie zmiany, która występuje w aplikacjach jednostronicowych. Aby włączyć automatyczne śledzenie zmian trasy dla aplikacji `enableAutoRouteTracking: true` jednostronicowej, można dodać do konfiguracji konfiguracji.

Obecnie oferujemy osobną [wtyczkę React,](#react-extensions) którą możesz zainicjować za pomocą tego SDK. Będzie również osiągnąć śledzenie zmian trasy dla Ciebie, a także zebrać [inne react konkretnych telemetrii](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Rozszerzenia reakcji

| Rozszerzenia |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reaguj natywne](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Eksplorowanie danych przeglądarki/klienta

Dane po stronie przeglądarki/klienta można przeglądać, przechodząc do **metryki** i dodając poszczególne dane, które Cię interesują:

![](./media/javascript/page-view-load-time.png)

Możesz również przeglądać dane z javascript SDK za pośrednictwem przeglądarki w portalu.

Wybierz **pozycję Przeglądarka,** a następnie wybierz pozycję **Błędy** lub **Wydajność**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Wydajność

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Zależności

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analiza

Aby zbadać dane telemetryczne zebrane przez sdk JavaScript, wybierz przycisk **Wyświetl w dziennikach (Analytics).** Przez dodanie `where` instrukcji `client_Type == "Browser"`, zobaczysz tylko dane z javascript SDK i wszelkie dane telemetryczne po stronie serwera zebrane przez inne zestawy SDK zostaną wykluczone.
 
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

### <a name="source-map-support"></a>Obsługa mapy źródłowego

Minified callstack danych telemetrycznych wyjątku można unminified w witrynie Azure portal. Wszystkie istniejące integracje w panelu Szczegóły wyjątku będą działać z nowo niesminifikowanym pakietem wywoławczym.

#### <a name="link-to-blob-storage-account"></a>Łącze do konta magazynu obiektów Blob

Zasób usługi Application Insights można połączyć z własnym kontenerem usługi Azure Blob Storage, aby automatycznie ujednolicić stosy wywołań. Aby rozpocząć, zobacz [obsługa automatycznej mapy źródłej](./source-map-support.md).

### <a name="drag-and-drop"></a>Przeciąganie i upuszczanie

1. Wybierz element Telemetrii wyjątków w witrynie Azure Portal, aby wyświetlić jego "Szczegóły transakcji end-to-end"
2. Określ, które mapy źródłowe odpowiadają temu stosowi wywołań. Mapa źródłowa musi być zgodna z plikiem źródłowym ramki stosu, ale`.map`
3. Przeciąganie i upuszczanie map źródłowych na stos wywołań w witrynie Azure portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Podstawowe informacje o aplikacji

Aby uzyskać lekkie środowisko, można zamiast tego zainstalować podstawową wersję usługi Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ta wersja jest wyposażona w minimalną liczbę funkcji i polega na tobie, aby ją zbudować według własnego uznania. Na przykład nie wykonuje autokolekcji (nieprzechwyty wyjątki, AJAX, itp.). Interfejsy API do wysyłania niektórych `trackTrace` `trackException`typów danych telemetrycznych, takich jak , itp., nie są uwzględnione w tej wersji, więc należy podać własne otoki. Jedynym dostępnym interfejsem `track`API jest . [Próbka](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) znajduje się tutaj.

## <a name="examples"></a>Przykłady

Aby zapoznać się z przykładami, zobacz [Przykłady zestawów SDK javascript usługi Application Insights, zobacz Przykłady zestawów SDK usługi Application Insights](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uaktualnianie ze starej wersji usługi Application Insights

Przełomowe zmiany w wersji SDK V2:
- Aby umożliwić lepsze podpisy interfejsu API, niektóre wywołania interfejsu API, takie jak trackPageView i trackException, zostały zaktualizowane. Uruchamianie w programie Internet Explorer 8 i wcześniejszych wersjach przeglądarki nie jest obsługiwane.
- Koperta telemetrii ma nazwę pola i zmiany struktury z powodu aktualizacji schematu danych.
- `context.operation` Przeniesiono `context.telemetryTrace`do . Niektóre pola również`operation.id` --> `telemetryTrace.traceID`zostały zmienione ( ).
  - Aby ręcznie odświeżyć bieżący identyfikator odsłony (na `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`przykład w aplikacjach SPA), użyj .
    > [!NOTE]
    > Aby zachować unikatowy identyfikator śledzenia, `Util.newId()`w którym `Util.generateW3CId()`wcześniej był używany, teraz użyj . Oba ostatecznie kończy się identyfikator operacji.

Jeśli używasz bieżącego zestawu SDK (1.0.20) i chcesz sprawdzić, czy nowy pakiet SDK działa w czasie wykonywania, zaktualizuj adres URL w zależności od bieżącego scenariusza ładowania zestawu SDK.

- Pobierz za pośrednictwem scenariusza usługi CDN: Zaktualizuj fragment kodu, którego obecnie używasz, aby wskazać następujący adres URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm scenariusz: `downloadAndSetup` Wywołanie pobrać pełny skrypt ApplicationInsights z sieci CDN i zainicjować go za pomocą klucza instrumentacji:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test w środowisku wewnętrznym, aby sprawdzić, czy monitorowanie danych telemetrycznych działa zgodnie z oczekiwaniami. Jeśli wszystko działa, należy odpowiednio zaktualizować podpisy interfejsu API do wersji SDK V2 i wdrożyć w środowiskach produkcyjnych.

## <a name="sdk-performanceoverhead"></a>Wydajność/obciążenie SDK

Przy zaledwie 25 KB gzipped i biorąc tylko ~ 15 ms do zainicjowania, Application Insights dodaje nieznaczną ilość czasu ładowania do witryny sieci Web. Za pomocą fragmentu kodu, minimalne składniki biblioteki są szybko ładowane. W międzyczasie pełny skrypt jest pobierany w tle.

Podczas pobierania skryptu z sieci CDN wszystkie śledzenie strony jest umieszczane w kolejce. Po zakończeniu inicjowania asynchronicznie pobranego skryptu wszystkie zdarzenia, które zostały umieszczone w kolejce, są śledzone. W rezultacie nie utracisz żadnych danych telemetrycznych podczas całego cyklu życia strony. Ten proces konfiguracji zapewnia stronie bezproblemowy system analityczny, niewidoczny dla użytkowników.

> Podsumowanie:
> - **25 KB** gzipped
> - **15 ms** całkowity czas inicjowania
> - **Brak** śledzenia nieodebranych w cyklu życia strony

## <a name="browser-support"></a>Obsługa przeglądarki

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Najnowsze ✔ Chrome |  Firefox Najnowsze ✔ | IE 9+ & ✔ krawędzi | Opera Najnowsze ✔ | Safari Najnowsze ✔ |

## <a name="open-source-sdk"></a>SDK typu open source

Application Insights JavaScript SDK jest open-source, aby wyświetlić kod źródłowy lub przyczynić się do projektu odwiedzić [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Kolejne kroki
* [Śledzenie użycia](usage-overview.md)
* [Niestandardowe zdarzenia i metryki](api-custom-events-metrics.md)
* [Tworzenie — pomiar— nauka](usage-overview.md)
