---
title: Raport dotyczący danych analizy ruchu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz analizę ruchu wyszukiwania dla platformy Azure Wyszukiwanie poznawcze, Zbieraj dane telemetryczne i zdarzenia zainicjowane przez użytkownika przy użyciu Application Insights, a następnie Analizuj wyniki w Power BI raport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 53fd02856a805f8bb5d7261cc9e6e32861b2b4fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426988"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementowanie analizy ruchu wyszukiwania na platformie Azure Wyszukiwanie poznawcze

Analiza ruchu wyszukiwania jest wzorcem do wdrażania pętli zwrotnej dla usługi wyszukiwania. Celem jest zbieranie danych telemetrycznych na temat zdarzeń inicjowanych przez użytkownika, a następnie wejść z klawiatury. Korzystając z tych informacji, można określić efektywność rozwiązania wyszukiwania, w tym popularne terminy wyszukiwania, częstotliwość kliknięć i dane wejściowe zapytania, które zwracają zero wyników.

Ten wzorzec przyjmuje zależność od [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (funkcji [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) do zbierania danych użytkownika. Należy również dodać instrumentację do kodu klienta, zgodnie z opisem w tym artykule. Na koniec konieczne będzie przeanalizowanie danych przy użyciu mechanizmu raportowania. Zalecamy Power BI, ale można użyć dowolnego narzędzia, które nawiązuje połączenie z Application Insights.

> [!NOTE]
> Wzorzec opisany w tym artykule dotyczy zaawansowanych scenariuszy i strumienia kliknięć danych generowanych przez klienta. Alternatywnie możesz raportować informacje dziennika wygenerowane przez usługę wyszukiwania. Aby uzyskać więcej informacji na temat raportów dziennika usługi, zobacz [monitorowanie użycia zasobów i działania zapytań](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Zidentyfikuj odpowiednie dane wyszukiwania

Aby korzystać z przydatnych metryk wyszukiwania, należy rejestrować niektóre sygnały od użytkowników aplikacji wyszukiwania. Sygnalizują one zawartość, którą interesują użytkownicy, i że są uważane za istotne dla ich potrzeb.

Istnieją dwa sygnały dotyczące Analiza ruchu wyszukiwania:

+ Zdarzenia wyszukiwania generowane przez użytkownika: interesujące są tylko zapytania wyszukiwania inicjowane przez użytkownika. Żądania wyszukiwania używane do wypełniania aspektów, dodatkowej zawartości lub wszelkich informacji wewnętrznych nie są ważne i powodują pochylenie i odchylenia wyników.

+ Zdarzenia kliknięcia generowane przez użytkownika: po kliknięciu w tym dokumencie następuje wybranie określonego wyniku wyszukiwania zwróconego przez zapytanie wyszukiwania. Kliknięcie na ogół oznacza, że dokument jest odpowiednim wynikiem dla konkretnego zapytania wyszukiwania.

Łącząc wyszukiwanie i klikaj zdarzenia z IDENTYFIKATORem korelacji, możliwe jest przeanalizowanie zachowań użytkowników w aplikacji. Szczegółowe informacje wyszukiwania są niedostępne tylko w przypadku dzienników ruchu wyszukiwania.

## <a name="add-search-traffic-analytics"></a>Dodawanie analizy ruchu wyszukiwania

Sygnały wymienione w poprzedniej sekcji muszą być zbierane z aplikacji wyszukiwania, gdy użytkownik pracuje z nią. Application Insights to rozszerzalne rozwiązanie do monitorowania dostępne dla wielu platform przy użyciu elastycznych opcji instrumentacji. Użycie Application Insights pozwala korzystać z raportów wyszukiwania Power BI utworzonych przez usługę Azure Wyszukiwanie poznawcze w celu ułatwienia analizy danych.

Na stronie [portalu](https://portal.azure.com) usługi Azure wyszukiwanie poznawcze strona wyszukiwania Analiza ruchu zawiera arkusz Ściągawka, który jest następujący: wzorzec telemetrii. Możesz również wybrać lub utworzyć zasób Application Insights i zobaczyć niezbędne dane, wszystkie w jednym miejscu.

![Instrukcje Analiza ruchu wyszukiwania][1]

## <a name="1---select-a-resource"></a>1 — Wybieranie zasobu

Musisz wybrać zasób Application Insights, który ma być używany, lub utworzyć go, jeśli jeszcze go nie masz. Możesz użyć zasobu, który jest już używany do rejestrowania wymaganych zdarzeń niestandardowych.

Podczas tworzenia nowego zasobu Application Insights wszystkie typy aplikacji są prawidłowe dla tego scenariusza. Wybierz ten, który najlepiej pasuje do używanej platformy.

Do utworzenia klienta telemetrii dla aplikacji potrzebny jest klucz Instrumentacji. Możesz pobrać go z pulpitu nawigacyjnego portalu Application Insights lub pobrać ze strony Analiza ruchu wyszukiwania, wybierając wystąpienie, którego chcesz użyć.

## <a name="2---add-instrumentation"></a>2 — Dodawanie Instrumentacji

Ten krok to miejsce, w którym można tworzyć własne aplikacje do wyszukiwania przy użyciu zasobów Application Insights utworzonych w powyższym kroku. Ten proces obejmuje cztery kroki:

**Krok 1. Tworzenie klienta telemetrii**

Jest to obiekt, który wysyła zdarzenia do zasobu Application Insights.

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

W przypadku innych języków i platform zapoznaj się z pełną [listą](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Krok 2. żądanie identyfikatora wyszukiwania dla korelacji**

Aby skorelować żądania wyszukiwania z kliknięciami, należy mieć identyfikator korelacji, który odnosi się do tych dwóch oddzielnych zdarzeń. Usługa Azure Wyszukiwanie poznawcze zapewnia identyfikator wyszukiwania, gdy zostanie on zażądany przy użyciu nagłówka:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
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

**Krok 3. rejestrowanie zdarzeń wyszukiwania**

Za każdym razem, gdy użytkownik wystawia żądanie wyszukiwania, należy je zarejestrować jako zdarzenie wyszukiwania z następującym schematem na Application Insights zdarzeniu niestandardowym:

**SearchServiceName**: (String) nazwa usługi wyszukiwania **SearchId**: (GUID) unikatowy identyfikator zapytania wyszukiwania (znajduje się w odpowiedzi wyszukiwania) **IndexName**: (String) indeks usługi wyszukiwania do zapytania **QueryTerms**: (String) warunki wyszukiwania podane przez użytkownika **Właściwości resultcount dla**: (int) liczba zwróconych dokumentów (znajduje się w odpowiedzi na wyszukiwanie) **ScoringProfile**: (ciąg) nazwa używanego profilu oceniania, jeśli istnieje

> [!NOTE]
> Liczba żądań dla zapytań generowanych przez użytkownika przez dodanie $count = true do zapytania wyszukiwania. Zobacz więcej informacji [tutaj](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Pamiętaj, aby rejestrować tylko zapytania wyszukiwania, które są generowane przez użytkowników.
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

**Krok 4. Dziennik zdarzeń kliknięcia**

Za każdym razem, gdy użytkownik kliknie dokument, jest to sygnał, który musi być zarejestrowany na potrzeby analizy wyszukiwania. Użyj niestandardowych zdarzeń Application Insights, aby rejestrować te zdarzenia z następującym schematem:

**ServiceName**: (String) nazwa usługi wyszukiwania **SearchId**: (GUID) unikatowy identyfikator powiązanego zapytania wyszukiwania **Maperze identyfikatorów dokumentów**: (String) **pozycja**identyfikatora dokumentu: (int) ranga dokumentu na stronie wyników wyszukiwania

> [!NOTE]
> Pozycja odwołuje się do kolejności kardynalnej w aplikacji. Możesz ustawić tę liczbę, o ile jest ona zawsze taka sama, aby umożliwić porównywanie.
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

## <a name="3---analyze-in-power-bi"></a>3 — analizowanie w Power BI

Po przydzieleniu aplikacji i zweryfikowaniu, że aplikacja jest prawidłowo połączona z Application Insights, można pobrać wstępnie zdefiniowany szablon raportu w celu przeanalizowania danych w programie Power BI Desktop. Raport zawiera wstępnie zdefiniowane wykresy i tabele przydatne do analizowania dodatkowych danych przechwyconych na potrzeby analizy ruchu wyszukiwania. 

1. W lewym okienku nawigacyjnym pulpitu nawigacyjnego usługi Azure Wyszukiwanie poznawcze w obszarze **Ustawienia**kliknij pozycję **Analiza ruchu wyszukiwania**.

2. Na stronie **Analiza ruchu wyszukiwania** w kroku 3 kliknij pozycję **Pobierz Power BI Desktop** , aby zainstalować Power BI.

   ![Pobierz raporty Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Pobierz raporty Power BI")

2. Na tej samej stronie kliknij pozycję **pobierz Power BI raport**.

3. Raport zostanie otwarty w Power BI Desktop i zostanie wyświetlony monit o połączenie z Application Insights i podanie poświadczeń. Informacje o połączeniu można znaleźć na Azure Portal stronach dla zasobu Application Insights. W polu poświadczenia podaj tę samą nazwę użytkownika i hasło, które są używane podczas logowania do portalu.

   ![Połącz z Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Połączenie z usługą Application Insights")

4. Kliknij przycisk **Załaduj**.

Raport zawiera wykresy i tabele, które ułatwiają podejmowanie bardziej świadomych decyzji dotyczących poprawy wydajności i przydatności wyszukiwania.

Metryki obejmują następujące elementy:

* Wyszukaj wolumin i najpopularniejsze pary dokumentów terminowych: warunki, które powodują, że ten sam dokument kliknięto, uporządkowany według kliknięć.
* Wyszukiwania bez kliknięć: warunki dla najważniejszych zapytań, które rejestrują brak kliknięć

Na poniższym zrzucie ekranu przedstawiono wbudowane raporty i wykresy umożliwiające analizowanie analizy ruchu wyszukiwania.

![Pulpit nawigacyjny Power BI dla platformy Azure Wyszukiwanie poznawcze](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Pulpit nawigacyjny Power BI dla platformy Azure Wyszukiwanie poznawcze")

## <a name="next-steps"></a>Następne kroki
Instrumentacja aplikacji wyszukiwania pozwala uzyskać zaawansowane i szczegółowe dane dotyczące usługi wyszukiwania.

Więcej informacji na temat [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/application-insights/) , aby dowiedzieć się więcej o różnych warstwach usług.

Dowiedz się więcej na temat tworzenia zachwycającymi raportami. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) .

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
