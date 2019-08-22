---
title: Implementowanie analizy ruchu wyszukiwania — Azure Search
description: Włącz analizę ruchu wyszukiwania dla Azure Search, aby dodać dane telemetryczne i zdarzenia zainicjowane przez użytkownika do plików dziennika.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb12ed2f18df100ab3f679e7a8a3ef1e7c1aca45
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647815"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementowanie analizy ruchu wyszukiwania w Azure Search
Analiza ruchu wyszukiwania jest wzorcem do wdrażania pętli zwrotnej dla usługi wyszukiwania. Ten wzorzec zawiera opis niezbędnych danych i sposobu ich zbierania przy użyciu Application Insights, lidera branżowego do monitorowania usług na wielu platformach.

Analiza ruchu wyszukiwania umożliwia uzyskanie wglądu w usługę wyszukiwania oraz odblokowanie szczegółowych informacji o użytkownikach i ich zachowaniu. Mając dane dotyczące wybranych użytkowników, można podejmować decyzje, które dodatkowo ulepszają środowisko wyszukiwania i wyłączać, gdy wyniki nie są oczekiwane.

Azure Search oferuje rozwiązanie telemetryczne, które integruje usługę Azure Application Insights i Power BI w celu zapewnienia dokładnego monitorowania i śledzenia. Ponieważ interakcja z Azure Search jest tylko za pośrednictwem interfejsów API, dane telemetryczne muszą być implementowane przez deweloperów przy użyciu funkcji wyszukiwania, postępując zgodnie z instrukcjami na tej stronie.

## <a name="identify-relevant-search-data"></a>Zidentyfikuj odpowiednie dane wyszukiwania

Aby korzystać z przydatnych metryk wyszukiwania, należy rejestrować niektóre sygnały od użytkowników aplikacji wyszukiwania. Sygnalizują one zawartość, którą interesują użytkownicy, i że są uważane za istotne dla ich potrzeb.

Istnieją dwa sygnały dotyczące Analiza ruchu wyszukiwania:

1. Wygenerowane przez użytkownika zdarzenia wyszukiwania: interesujące są tylko zapytania wyszukiwania inicjowane przez użytkownika. Żądania wyszukiwania używane do wypełniania aspektów, dodatkowej zawartości lub wszelkich informacji wewnętrznych nie są ważne i powodują pochylenie i odchylenia wyników.

2. Wygenerowane przez użytkownika zdarzenia kliknięcia: Kliknięcie w tym dokumencie oznacza, że użytkownik wybiera konkretny wynik wyszukiwania zwrócony przez zapytanie wyszukiwania. Kliknięcie na ogół oznacza, że dokument jest odpowiednim wynikiem dla konkretnego zapytania wyszukiwania.

Łącząc wyszukiwanie i klikaj zdarzenia z identyfikatorem korelacji, możliwe jest przeanalizowanie zachowań użytkowników w aplikacji. Szczegółowe informacje wyszukiwania są niedostępne tylko w przypadku dzienników ruchu wyszukiwania.

## <a name="add-search-traffic-analytics"></a>Dodawanie analizy ruchu wyszukiwania

Sygnały wymienione w poprzedniej sekcji muszą być zbierane z aplikacji wyszukiwania, gdy użytkownik pracuje z nią. Application Insights to rozszerzalne rozwiązanie do monitorowania dostępne dla wielu platform przy użyciu elastycznych opcji instrumentacji. Użycie Application Insights pozwala korzystać z raportów wyszukiwania Power BI utworzonych przez Azure Search, aby ułatwić analizę danych.

Na stronie [portalu](https://portal.azure.com) usługi Azure Search, blok Analiza ruchu wyszukiwania zawiera arkusz Ściągawka dla tego wzorca telemetrii. Możesz również wybrać lub utworzyć zasób Application Insights i zobaczyć niezbędne dane, wszystkie w jednym miejscu.

![Instrukcje Analiza ruchu wyszukiwania][1]

## <a name="1---select-a-resource"></a>1 — Wybieranie zasobu

Musisz wybrać zasób Application Insights, który ma być używany, lub utworzyć go, jeśli jeszcze go nie masz. Możesz użyć zasobu, który jest już używany do rejestrowania wymaganych zdarzeń niestandardowych.

Podczas tworzenia nowego zasobu Application Insights wszystkie typy aplikacji są prawidłowe dla tego scenariusza. Wybierz ten, który najlepiej pasuje do używanej platformy.

Do utworzenia klienta telemetrii dla aplikacji potrzebny jest klucz Instrumentacji. Możesz pobrać go z pulpitu nawigacyjnego portalu Application Insights lub pobrać ze strony Analiza ruchu wyszukiwania, wybierając wystąpienie, którego chcesz użyć.

## <a name="2---add-instrumentation"></a>2 — Dodawanie Instrumentacji

Ta faza polega na tym, że tworzysz instrument swojej własnej aplikacji wyszukiwania przy użyciu zasobu Application Insights utworzonego w powyższym kroku. Ten proces obejmuje cztery kroki:

**Krok 1. Utwórz klienta** telemetrii jest to obiekt, który wysyła zdarzenia do Application Insights zasobu.

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

W przypadku innych języków i platform zapoznaj się [](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)z pełną listą.

**Krok 2. Zażądaj identyfikatora wyszukiwania dla** korelacji, aby skorelować żądania wyszukiwania z kliknięciami, musisz mieć identyfikator korelacji, który odnosi się do tych dwóch oddzielnych zdarzeń. Azure Search zapewnia identyfikator wyszukiwania, gdy zostanie on zażądany przy użyciu nagłówka:

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

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

**Krok 3. Zdarzenia przeszukiwania dzienników**

Za każdym razem, gdy użytkownik wystawia żądanie wyszukiwania, należy je zarejestrować jako zdarzenie wyszukiwania z następującym schematem na Application Insights zdarzeniu niestandardowym:

**SearchServiceName**: (String) nazwa usługi wyszukiwania **SearchId**: (GUID) unikatowy identyfikator zapytania wyszukiwania (znajduje się w odpowiedzi wyszukiwania) IndexName: (String) indeks usługi wyszukiwania, który ma być badany **QueryTerms**: (ciąg) Wyszukiwanie warunki wprowadzone przez użytkownika **Właściwości resultcount dla**: (int) liczba zwróconych dokumentów (znajduje się w odpowiedzi na wyszukiwanie) **ScoringProfile**: (String) nazwa używanego profilu oceniania, jeśli istnieje

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

**Krok 4. Zdarzenia kliknięcia dziennika**

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

Po przydzieleniu aplikacji i zweryfikowaniu, że aplikacja została prawidłowo połączona z Application Insights, można użyć wstępnie zdefiniowanego szablonu utworzonego przez Azure Search dla programu Power BI Desktop. 

Usługa Azure Search udostępnia [pakiet zawartości monitorowania Power BI](https://app.powerbi.com/getdata/services/azure-search) , dzięki czemu można analizować dane dziennika. Pakiet zawartości dodaje wstępnie zdefiniowane wykresy i tabele przydatne do analizowania dodatkowych danych przechwyconych na potrzeby analizy ruchu wyszukiwania. Aby uzyskać więcej informacji, zobacz [stronę pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. W okienku nawigacji po lewej stronie pulpitu nawigacyjnego Azure Search w obszarze **Ustawienia**kliknij pozycję **Analiza ruchu wyszukiwania**.

2. Na stronie **Analiza ruchu wyszukiwania** w kroku 3 kliknij pozycję **Pobierz Power BI Desktop** , aby zainstalować Power BI.

   ![Pobierz raporty Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Pobierz raporty Power BI")

2. Na tej samej stronie kliknij pozycję **Pobierz raport usługi PowerBI**.

3. Raport zostanie otwarty w Power BI Desktop i zostanie wyświetlony monit o połączenie z Application Insights. Te informacje można znaleźć na Azure Portal stronach Application Insights zasobów.

   ![Połącz z Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Połącz z Application Insights")

4. Kliknijprzycisk Załaduj.

Raport zawiera wykresy i tabele, które ułatwiają podejmowanie bardziej świadomych decyzji dotyczących poprawy wydajności i przydatności wyszukiwania.

Metryki obejmują następujące elementy:

* Kliknij pozycję za pomocą rate (Rob): współczynnik dla użytkowników, którzy klikają określony dokument, do całkowitej liczby wyszukiwań.
* Wyszukiwania bez kliknięć: warunki dla najważniejszych zapytań, które rejestrują brak kliknięć
* Najczęściej kliknięte dokumenty: ostatnio kliknięte dokumenty według identyfikatora w ciągu ostatnich 24 godzin, 7 dni i 30 dni.
* Popularne pary dokumentów: warunki, które powodują kliknięcie tego samego dokumentu, uporządkowane według kliknięć.
* Czas kliknięcia: kliknięcia przedziału według czasu od czasu zapytania wyszukiwania

Na poniższym zrzucie ekranu przedstawiono wbudowane raporty i wykresy umożliwiające analizowanie analizy ruchu wyszukiwania.

![Pulpit nawigacyjny Power BI dla Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Pulpit nawigacyjny Power BI dla Azure Search")

## <a name="next-steps"></a>Następne kroki
Instrumentacja aplikacji wyszukiwania pozwala uzyskać zaawansowane i szczegółowe dane dotyczące usługi wyszukiwania.

Więcej informacji na temat [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/application-insights/) , aby dowiedzieć się więcej o różnych warstwach usług.

Dowiedz się więcej na temat tworzenia zachwycającymi raportami. Zobacz [wprowadzenie do usługi Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) Aby uzyskać szczegółowe informacje

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
