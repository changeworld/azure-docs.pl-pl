---
title: Wyszukaj analizy ruchu w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Włącz analizy ruchu wyszukiwania dla usługi wyszukiwanie Azure, Usługa wyszukiwania w chmurze, obsługiwanych w systemie Microsoft Azure, aby odblokować informacjami na temat użytkowników i danych.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2017
ms.author: heidist
ms.openlocfilehash: 4b40e8c9f681b7489c0ab2ffe7b369cc869c73e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777263"
---
# <a name="what-is-search-traffic-analytics"></a>Co to jest analiza ruchu wyszukiwania
Analiza ruchu wyszukiwania to wzorzec wykonania sprzężenia zwrotnego dla usługi wyszukiwania. Ten wzorzec opisano niezbędnych danych i jak zbierać za pomocą usługi Application Insights, wiodące branży monitorowania usług w wielu platform.

Analizy ruchu wyszukiwania umożliwia wgląd we usługi wyszukiwania i odblokować informacjami na temat użytkowników i ich zachowanie. Dzięki użyciu dane dotyczące użytkowników wybierz, jest możliwe do podjęcia decyzji, które dodatkowo udoskonalić wyszukiwania i wycofania, gdy wyniki są nie oczekiwano co.

Usługa Azure Search udostępnia rozwiązanie telemetrii integrującą Azure Application Insights i usługi Power BI, aby zapewnić szczegółowe monitorowanie i śledzenie. Ponieważ interakcji z usługi Azure Search jest tylko za pośrednictwem interfejsów API, telemetrii musi być implementowana przez deweloperów w wyszukiwaniu, postępując zgodnie z instrukcjami na tej stronie.

## <a name="identify-the-relevant-search-data"></a>Zidentyfikuj odpowiednie wyszukiwanie danych

Aby wyszukiwania przydatne metryki, jest niezbędne do logowania niektórych sygnały od użytkowników aplikacji wyszukiwania. Te sygnały wskazują zawartość, która jest zainteresowanych użytkowników i ich zdaniem ich potrzebom.

Istnieją dwa sygnałów, których potrzebuje analizy ruchu wyszukiwania:

1. Zdarzenia wyszukiwania użytkownika wygenerowany: tylko zapytania wyszukiwania zainicjowane przez użytkownika są interesujące. Żądania wyszukiwania służące do wypełniania aspektów, dodatkową zawartość lub informacje o wewnętrznych, nie są ważne i pochylanie i bias wyniki.

2. Zdarzenia kliknięcia użytkownika wygenerowany: przez kliknięć w tym dokumencie, możemy odwoływać się do użytkownika, wybierając wynik wyszukiwania określonego zwrócone w wyniku zapytania wyszukiwania. Kliknięcie zazwyczaj oznacza, że dokument odpowiednich wyników zapytania wyszukiwania określonych.

Łączenie zdarzeń wyszukiwania i kliknij przycisk z identyfikatorem korelacji, istnieje możliwość do analizy zachowania użytkowników w aplikacji. Te szczegółowe dane wyszukiwania będą możliwe uzyskanie z tylko dzienników ruchu wyszukiwania.

## <a name="how-to-implement-search-traffic-analytics"></a>Jak zaimplementować analizy ruchu wyszukiwania

Sygnałów, o których wspomniano w poprzedniej sekcji musi można zbierać z aplikacji wyszukiwania, ponieważ użytkownik wchodzi w interakcję z nią. Usługa Application Insights jest rozszerzalne rozwiązanie monitorowania, dostępne dla wielu platform przy użyciu opcji elastyczne instrumentacji. Użycie usługi Application Insights pozwala korzystać z raportów wyszukiwania usługi Power BI utworzonych przez usługi Azure Search, aby ułatwić analizę danych.

W [portal](https://portal.azure.com) arkusz wskazówkami na następujących tego wzorca telemetrii zawiera strony dla usługi Azure Search, bloku analizy ruchu wyszukiwania. Można również wybrać lub utworzyć zasobu usługi Application Insights i zobacz niezbędnych danych, w jednym miejscu.

![Instrukcje dotyczące analizy ruchu wyszukiwania][1]

### <a name="1-select-an-application-insights-resource"></a>1. Wybierz zasób usługi Application Insights

Musisz wybrać zasobu usługi Application Insights do użycia lub utwórz taki, jeśli nie masz już. Korzystając z zasobem, który jest już używany do rejestrowania zdarzeń niestandardowych wymagane.

Tworząc nowy zasób usługi Application Insights, wszystkie typy aplikacji są prawidłowe dla tego scenariusza. Wybierz jedną, która najlepiej pasuje do używanej platformy.

Do tworzenia klienta telemetrii w aplikacji potrzebny jest klucz instrumentacji. Możesz pobrać go z pulpitu nawigacyjnego portalu Application Insights, lub możesz pobrać go ze strony analizy ruchu wyszukiwania, wybierając wystąpienie, którego chcesz użyć.

### <a name="2-instrument-your-application"></a>2. Instrumentacja aplikacji

W tej fazie jest, gdzie możesz Instrumentacja własnych aplikacji wyszukiwania przy użyciu zasobu usługi Application Insights z utworzonego w poprzednim kroku. Istnieją cztery kroki, aby ten proces:

**I. Tworzenie klienta telemetrii** jest obiekt, który wysyła zdarzenia do zasobu usługi Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Dla innych języków i platform, zobacz pełne [listy](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**II. Żądaj Identyfikatora wyszukiwania dla korelacji** służące do skorelowania żądania wyszukiwania kliknięć, należy mieć identyfikator korelacji, które odnoszą się te dwa następujące zdarzenia. Usługa wyszukiwanie Azure umożliwia identyfikatora wyszukiwania możesz poprosić o nagłówka:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<ServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**III. Zdarzenia dziennika wyszukiwania**

Żądania wyszukiwania wystawiony przez użytkownika, zawsze należy rejestrować który jako zdarzenie wyszukiwania z następującego schematu na zdarzenie niestandardowe usługi Application Insights:

**ServiceName**: Nazwa usługi wyszukiwania (ciąg) **SearchId**: Unikatowy identyfikator (guid) z zapytania wyszukiwania (jest dostarczany w odpowiedzi wyszukiwania) **IndexName**: indeks usługi wyszukiwania (ciąg) jako Kwerenda **QueryTerms**: terminy wyszukiwania (ciąg) wprowadzony przez użytkownika **ResultCount**: (int) liczba dokumentów, które zostały zwrócone (jest dostarczany w odpowiedzi wyszukiwania)  **ScoringProfile**: (ciąg) Nazwa profilu oceniania używane, jeśli istnieje

> [!NOTE]
> Liczba żądań na kwerendach użytkownika wygenerowany przez dodanie $count = true, aby zapytania wyszukiwania. Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Pamiętaj, aby zalogować się wyłącznie zapytania wyszukiwania, które są generowane przez użytkowników.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**IV. Kliknij dziennika zdarzeń**

Zawsze użytkownik kliknie dokumentu, który jest sygnał, który musi być zalogowany na potrzeby analizy wyszukiwania. Niestandardowe zdarzenia usługi Application Insights umożliwia rejestrowanie zdarzeń z następującego schematu:

**ServiceName**: Nazwa usługi wyszukiwania (ciąg) **SearchId**: Unikatowy identyfikator (globalny guid) zapytania wyszukiwania powiązanych **identyfikator**: identyfikator dokumentu (ciąg) **pozycji**: strona wyników rangę (int) dokumentu w wyszukiwaniu

> [!NOTE]
> Pozycja odwołuje się do kardynalnej kolejności w aplikacji. Jesteś mogą określać ten numer, tak długo, jak jest zawsze taki sam, aby umożliwić do porównania.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

### <a name="3-analyze-with-power-bi-desktop"></a>3. Analizowanie z Power BI Desktop

Po instrumentacji aplikacji i sprawdzić, czy aplikacja jest poprawnie podłączony do usługi Application Insights, można użyć wstępnie zdefiniowanego szablonu utworzonych przez usługi Azure Search dla usługi Power BI desktop.
Ten szablon zawiera wykresów i tabel, które ułatwiają podejmowanie bardziej świadomych decyzji aby poprawić wydajność wyszukiwania, a znaczenie dla.

Do utworzenia wystąpienia szablonu pulpitu usługi Power BI, potrzebne są trzy informacje na temat usługi Application Insights. Te dane można znaleźć na stronie analizy ruchu wyszukiwania po wybraniu zasobu należy użyć

![Dane usługi Application Insights w bloku analizy ruchu wyszukiwania][2]

Metryki zawarte w szablonie pulpitu usługi Power BI:

*   Kliknij przycisk za pośrednictwem szybkość kont (.): współczynnik użytkowników, którzy kliknij na konkretny dokument do liczby całkowitej wyszukiwania.
*   Wyszukuje bez kliknięć: warunki dla zapytań, które zarejestrować kliknięć nie z góry
*   Najbardziej kliknięty dokumentów: najbardziej kliknięty dokumentów za pomocą Identyfikatora w ostatnich 24 godzin, 7 dni i 30 dni.
*   Pary popularnych dokumentu termin: warunki wynikających z tego samego dokumentu kliknięty, uporządkowanych według kliknięć.
*   Czas kliknij: zasobnikach kliknięć w okresie od zapytania wyszukiwania

![Power BI szablonu do odczytywania z usługi Application Insights][3]


## <a name="next-steps"></a>Następne kroki
Instrumentacja aplikacji wyszukiwania można pobrać wydajny i interesującego dane dotyczące usługi wyszukiwania.

Można znaleźć więcej informacji na temat usługi Application Insights [tutaj](https://go.microsoft.com/fwlink/?linkid=842905). Odwiedź stronę usługi Application Insights [cennikiem](https://azure.microsoft.com/pricing/details/application-insights/) Aby dowiedzieć się więcej na temat ich różnych warstwach usług.

Dowiedz się więcej na temat tworzenia wspaniałych raportów. Zobacz [wprowadzenie Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) Aby uzyskać więcej informacji

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
