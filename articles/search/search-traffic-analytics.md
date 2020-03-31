---
title: Telemetria do analizy ruchu w wyszukiwarce
titleSuffix: Azure Cognitive Search
description: Włącz analizę ruchu wyszukiwania dla usługi Azure Cognitive Search, zbieraj dane telemetryczne i zdarzenia inicjowane przez użytkownika przy użyciu usługi Application Insights, a następnie analizuj wyniki w raporcie usługi Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258213"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Zbieranie danych telemetrycznych do analizy ruchu w sieci wyszukiwania

Analiza ruchu wyszukiwania to wzorzec do zbierania danych telemetrycznych dotyczących interakcji użytkownika z aplikacją Usługi Azure Cognitive Search, takich jak zdarzenia kliknięć inicjowane przez użytkownika i dane wejściowe klawiatury. Korzystając z tych informacji, można określić skuteczność rozwiązania wyszukiwania, w tym popularnych wyszukiwanych haseł, współczynnik klikalności i dane wejściowe kwerendy dają zero wyników.

Ten wzorzec ma zależność od [usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (funkcja usługi Azure [Monitor)](https://docs.microsoft.com/azure/azure-monitor/)do zbierania danych użytkownika. Wymaga dodania instrumentacji do kodu klienta, zgodnie z opisem w tym artykule. Na koniec potrzebny będzie mechanizm raportowania do analizowania danych. Zalecamy program Power BI, ale można użyć pulpitu nawigacyjnego aplikacji lub dowolnego narzędzia łączącego się z usługą Application Insights.

> [!NOTE]
> Wzorzec opisany w tym artykule jest dla zaawansowanych scenariuszy i danych clickstream generowane przez kod, który można dodać do klienta. Z drugiej strony dzienniki usług są łatwe do skonfigurowania, zapewniają zakres metryk i można to zrobić w portalu bez konieczności stosowania kodu. Włączenie rejestrowania diagnostycznego jest zalecane dla wszystkich scenariuszy. Aby uzyskać więcej informacji, zobacz [Zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Identyfikowanie odpowiednich danych wyszukiwania

Aby mieć przydatne metryki analizy ruchu wyszukiwania, należy rejestrować niektóre sygnały od użytkowników aplikacji wyszukiwania. Sygnały te oznaczają treści, które użytkownicy są zainteresowani i które uznają za istotne. W przypadku analizy ruchu w wyszukiwarce obejmują one:

+ Zdarzenia wyszukiwania generowane przez użytkownika: interesujące są tylko zapytania zainicjowane przez użytkownika. Żądania wyszukiwania używane do wypełniania aspektów, dodatkowej zawartości lub jakichkolwiek informacji wewnętrznych nie są ważne i wypaczają i wypaczają wyniki.

+ Zdarzenia kliknięcia generowane przez użytkownika: na stronie wyników wyszukiwania zdarzenie kliknięcia zazwyczaj oznacza, że dokument jest odpowiednim wynikiem dla określonego zapytania wyszukiwania.

Łącząc zdarzenia wyszukiwania i kliknięć z identyfikatorem korelacji, uzyskasz głębsze zrozumienie skuteczności funkcji wyszukiwania aplikacji.

## <a name="add-search-traffic-analytics"></a>Dodawanie analizy ruchu w wyszukiwarce

Na stronie [portalu](https://portal.azure.com) usługi Azure Cognitive Search strona Analiza ruchu wyszukiwania zawiera ściągawki do obserwowania tego wzorca telemetrii. Na tej stronie można wybrać lub utworzyć zasób usługi Application Insights, uzyskać klucz instrumentacji, skopiować fragmenty kodu, które można dostosować do rozwiązania, oraz pobrać raport usługi Power BI utworzony na podstawie schematu odzwierciedlonego we wzorcu.

![Szukaj strony Analizy ruchu w portalu](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Szukaj strony Analizy ruchu w portalu")

## <a name="1---set-up-application-insights"></a>1 - Konfigurowanie usługi Application Insights

Wybierz istniejący zasób usługi Application Insights lub [utwórz go,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) jeśli jeszcze go nie masz. Jeśli używasz search traffic analytics strony, można skopiować klucz instrumentacji aplikacji musi połączyć się z usługi Application Insights.

Po uzyskaniu zasobu usługi Application Insights można postępować zgodnie z [instrukcjami dotyczącymi obsługiwanych języków i platform,](https://docs.microsoft.com/azure/azure-monitor/app/platforms) aby zarejestrować aplikację. Rejestracja jest po prostu dodanie klucza instrumentacji z usługi Application Insights do kodu, który konfiguruje skojarzenia. Klucz można znaleźć w portalu lub na stronie Analiza ruchu wyszukiwania po wybraniu istniejącego zasobu.

Skrót, który działa dla niektórych typów projektów programu Visual Studio jest odzwierciedlony w poniższych krokach. Tworzy zasób i rejestruje aplikację za pomocą kilku kliknięć.

1. W programie Visual Studio i ASP.NET programowaniu otwórz rozwiązanie i wybierz pozycję **Project** > **Add Application Insights Telemetry**.

1. Kliknij pozycję **Wprowadzenie**.

1. Zarejestruj aplikację, udostępniając konto Microsoft, subskrypcję platformy Azure i zasób usługi Application Insights (nowy zasób jest domyślny). Kliknij **pozycję Zarejestruj**.

W tym momencie aplikacja jest skonfigurowana do monitorowania aplikacji, co oznacza, że wszystkie obciążenia strony są śledzone za pomocą domyślnych metryk. Aby uzyskać więcej informacji na temat poprzednich kroków, zobacz [Włączanie telemetrii po stronie serwera usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 - Dodaj oprzyrządowanie

Ten krok jest, gdy instrument własnej aplikacji wyszukiwania, przy użyciu zasobu usługi Application Insights utworzonego w kroku powyżej. Istnieją cztery kroki do tego procesu, począwszy od tworzenia klienta telemetrii.

### <a name="step-1-create-a-telemetry-client"></a>Krok 1: Tworzenie klienta telemetrii

Utwórz obiekt, który wysyła zdarzenia do usługi Application Insights. Instrumentację można dodać do kodu aplikacji po stronie serwera lub kodu po stronie klienta uruchomionego w przeglądarce, wyrażonego w tym miejscu jako warianty C# i JavaScript (w przypadku innych języków zobacz pełną listę [obsługiwanych platform i struktur](https://docs.microsoft.com/azure/application-insights/app-insights-platforms). Wybierz podejście, które zapewnia żądaną głębię informacji.

Telemetria po stronie serwera przechwytuje metryki w warstwie aplikacji, na przykład w aplikacjach uruchomionych jako usługa sieci web w chmurze lub jako aplikacja lokalna w sieci firmowej. Telemetria po stronie serwera przechwytuje zdarzenia wyszukiwania i klikania, położenie dokumentu w wynikach i informacje o zapytaniach, ale zbieranie danych będzie ograniczone do wszelkich informacji dostępnych w tej warstwie.

Na kliencie może mieć dodatkowy kod, który manipuluje wprowadzania kwerend, dodaje nawigacji lub zawiera kontekst (na przykład kwerendy zainicjowane ze strony głównej w porównaniu do strony produktu). Jeśli opisano rozwiązanie, można zdecydować się na instrumentacji po stronie klienta, tak aby dane telemetryczne odzwierciedla dodatkowe szczegóły. Sposób zbierania tych dodatkowych szczegółów wykracza poza zakres tego wzorca, ale można przejrzeć [usługa Application Insights dla stron sieci web,](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) aby uzyskać więcej kierunku. 

**Użyj C #**

Dla języka **C#InstrumentationKey** znajduje się w konfiguracji aplikacji, takich jak appsettings.json, jeśli projekt jest ASP.NET. Jeśli nie masz pewności co do kluczowej lokalizacji, zapoznaj się z instrukcjami rejestracji.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**Use JavaScript** (Korzystanie z kodu JavaScript)

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2: Żądanie identyfikatora wyszukiwania dla korelacji

Aby skorelować żądania wyszukiwania z kliknięciami, konieczne jest, aby mieć identyfikator korelacji, który odnosi się do tych dwóch różnych zdarzeń. Usługa Azure Cognitive Search udostępnia identyfikator wyszukiwania, gdy zażądasz go za pomocą nagłówka HTTP.

Posiadanie identyfikatora wyszukiwania umożliwia korelację metryk emitowanych przez usługę Azure Cognitive Search dla samego żądania, z metrykami niestandardowymi, które logujesz w usłudze Application Insights.  

**Użyj C #**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**Używanie języka JavaScript (wywoływanie interfejsów API REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Krok 3: Rejestrowanie zdarzeń wyszukiwania

Za każdym razem, gdy żądanie wyszukiwania jest wystawiane przez użytkownika, należy zalogować to jako zdarzenie wyszukiwania z następującym schematem w zdarzeniu niestandardowym usługi Application Insights. Pamiętaj, aby rejestrować tylko zapytania generowane przez użytkownika.

+ **SearchServiceName**: (ciąg) nazwa usługi wyszukiwania
+ **SearchId**: (guid) unikatowy identyfikator zapytania wyszukiwania (jest w odpowiedzi wyszukiwania)
+ **IndexName**: (ciąg) indeks usługi wyszukiwania do kwerendy
+ **QueryTerms**: (ciąg) wyszukiwane terminy wprowadzone przez użytkownika
+ **WynikCount**: (int) liczba dokumentów, które zostały zwrócone (jest w odpowiedzi wyszukiwania)
+ **ScoringProfile**: (ciąg) nazwa używanego profilu oceniania, jeśli istnieje

> [!NOTE]
> Poproś o liczbę zapytań generowanych przez użytkownika, dodając $count=true do zapytania wyszukiwania. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie dokumentów (REST)](/rest/api/searchservice/search-documents#counttrue--false).
>

**Użyj C #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**Use JavaScript** (Korzystanie z kodu JavaScript)

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Krok 4: Zaloguj zdarzenia kliknięcia

Za każdym razem, gdy użytkownik kliknie dokument, jest to sygnał, który musi być rejestrowany do celów analizy wyszukiwania. Użyj zdarzeń niestandardowych usługi Application Insights, aby rejestrować te zdarzenia przy użyciu następującego schematu:

+ **Nazwa**usługi: (ciąg) nazwa usługi wyszukiwania
+ **SearchId**: (guid) unikatowy identyfikator powiązanej kwerendy wyszukiwania
+ **DocId**: (ciąg) identyfikator dokumentu
+ **Pozycja**: (int) ranga dokumentu na stronie wyników wyszukiwania

> [!NOTE]
> Pozycja odnosi się do kolejności kardynalskiej w aplikacji. Możesz ustawić tę liczbę, o ile zawsze jest taka sama, aby umożliwić porównanie.
>

**Użyj C #**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**Use JavaScript** (Korzystanie z kodu JavaScript)

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Analizowanie w usłudze Power BI

Po przyrządzeniu aplikacji i zweryfikowaniu, że aplikacja jest poprawnie połączona z usługą Application Insights, pobierz wstępnie zdefiniowany szablon raportu do analizy danych na pulpicie usługi Power BI. Raport zawiera wstępnie zdefiniowane wykresy i tabele przydatne do analizowania dodatkowych danych przechwyconych do analizy ruchu wyszukiwania.

1. W lewym okienku nawigacji na pulpicie nawigacyjnym usługi Azure Cognitive Search w obszarze **Ustawienia**kliknij pozycję **Analiza ruchu wyszukiwania**.

1. Na stronie **Analiza ruchu wyszukiwania** w kroku 3 kliknij pozycję Pobierz program Power BI **Desktop,** aby zainstalować usługę Power BI.

   ![Pobierz raporty usługi Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Pobierz raporty usługi Power BI")

1. Na tej samej stronie kliknij pozycję **Pobierz raport usługi Power BI**.

1. Raport zostanie otwarty w programie Power BI Desktop, a użytkownik zostanie poproszony o połączenie z usługą Application Insights i podanie poświadczeń. Informacje o połączeniu można znaleźć na stronach portalu Azure dla zasobu usługi Application Insights. W przypadku poświadczeń podaj tę samą nazwę użytkownika i hasło, których używasz do logowania się do portalu.

   ![Połączenie z usługą Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Połączenie z usługą Application Insights")

1. Kliknij **przycisk Wczytaj**.

Raport zawiera wykresy i tabele, które pomagają podejmować bardziej świadome decyzje w celu zwiększenia skuteczności wyszukiwania i trafności.

Dane obejmowały następujące elementy:

+ Liczba wyszukiwań i najpopularniejsze pary term-document: terminy, które powodują kliknięcie tego samego dokumentu, uporządkowane według kliknięć.
+ Wyszukiwanie bez kliknięć: terminy dla najważniejszych zapytań, które rejestrują bez kliknięć

Poniższy zrzut ekranu pokazuje, jak może wyglądać wbudowany raport, jeśli użyto wszystkich elementów schematu.

![Pulpit nawigacyjny usługi Power BI dla usługi Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Pulpit nawigacyjny usługi Power BI dla usługi Azure Cognitive Search")

## <a name="next-steps"></a>Następne kroki

Instrument aplikacji wyszukiwania, aby uzyskać zaawansowane i wnikliwe dane o usłudze wyszukiwania.

Więcej informacji na temat [usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) można znaleźć na stronie z [cennikiem,](https://azure.microsoft.com/pricing/details/application-insights/) aby dowiedzieć się więcej o różnych warstwach usług.

Dowiedz się więcej o tworzeniu niesamowitych raportów. Aby uzyskać szczegółowe [informacje, zobacz Wprowadzenie do usługi Power BI Desktop.](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)