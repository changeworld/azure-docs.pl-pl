---
title: Implementowanie analiza ruchu wyszukiwania — usługa Azure Search
description: Włącz analiza ruchu wyszukiwania dla usługi Azure Search dodać dane telemetryczne oraz zdarzenia zainicjowane przez użytkownika w plikach dziennika.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c30c8bae3e76778a31cdd0695acde52b5b1c6b02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749618"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Implementowanie analiza ruchu wyszukiwania w usłudze Azure Search
Analiza ruchu wyszukiwania jest wzorzec wykonania sprzężenia zwrotnego dla usługi wyszukiwania. Ten wzorzec opisuje niezbędnych danych oraz sposobu zbieranie go za pomocą usługi Application Insights do monitorowania usług w wielu platform lidera w branży.

Analiza ruchu wyszukiwania pozwala uzyskać wgląd w usługi wyszukiwania i odblokowywać ich szczegółowe informacje dotyczące użytkowników i ich zachowania. Mając dane dotyczące dokonanego wyboru przez użytkowników, jest możliwe do podejmowania decyzji, które dodatkowo ulepszyć nasze usługi wyszukiwania i żebyś się odczepił, gdy wyniki są nie oczekuje co.

Usługa Azure Search udostępnia rozwiązanie telemetryczne, w której zintegrowano usługi Azure Application Insights i Power BI, aby zapewnić szczegółowe monitorowanie i śledzenie. Ponieważ interakcji z usługą Azure Search znajduje się tylko za pośrednictwem interfejsów API, należy zaimplementować danych telemetrycznych przez deweloperów, przy użyciu wyszukiwania, postępując zgodnie z instrukcjami na tej stronie.

## <a name="identify-relevant-search-data"></a>Zidentyfikuj odpowiednie wyszukiwanie danych

Aby uzyskać metryki przydatne wyszukiwania, należy się niektóre sygnały od użytkowników aplikacji wyszukiwania. Te sygnały oznaczającego zawartość, która są zainteresowani użytkowników i ich zdaniem odpowiednich do ich potrzeb.

Istnieją dwa sygnały, czego potrzebuje analiza ruchu wyszukiwania:

1. Zdarzenia wyszukiwania wygenerowane przez użytkowników: tylko interesujące zapytania wyszukiwania zainicjowane przez użytkownika. Żądania wyszukiwania używanych do wypełniania zestawu reguł dodatkowej zawartości oraz wszelkich informacji wewnętrznych, nie są istotne i pochylanie oraz bias wyniki.

2. Zdarzenia kliknięcia wygenerowane przez użytkowników: Za pomocą kliknięć w tym dokumencie możemy się odwoływać użytkownikowi wybranie wyniku wyszukiwania określonego zwrócone w wyniku zapytania wyszukiwania. Kliknięcie zazwyczaj oznacza, że dokument odpowiednich wyników zapytania wyszukiwania określonych.

Dzięki połączeniu wyszukiwania i kliknij przycisk zdarzenia z identyfikatorem korelacji, jest możliwe do analizy zachowania użytkowników w swojej aplikacji. Te wyszukiwania szczegółowe dane są możliwe uzyskanie przy użyciu tylko dzienniki ruchu wyszukiwania.

## <a name="add-search-traffic-analytics"></a>Dodaj analiza ruchu wyszukiwania

Sygnały, o których wspomniano w poprzedniej sekcji należy zbierać z poziomu aplikacji wyszukiwania jako użytkownik wchodzi w interakcję z nią. Application Insights jest rozszerzalną rozwiązanie do monitorowania, dostępne dla wielu platform za pomocą Instrumentacji elastyczne opcje. Korzystanie z usługi Application Insights pozwala korzystać z raportów wyszukiwania usługi Power BI, które są tworzone przez usługę Azure Search, aby ułatwić analizę danych.

W [portal](https://portal.azure.com) strona dla usługi Azure Search blok analiza ruchu wyszukiwania zawiera przydatną ściągawkę do korzystania z tego wzorca danych telemetrycznych. Można również wybrać lub utworzyć zasób usługi Application Insights i zobacz niezbędnych danych w jednym miejscu.

![Instrukcje dotyczące rozwiązania analiza ruchu wyszukiwania][1]

## <a name="1---select-a-resource"></a>1 — Wybierz zasób

Należy wybrać zasób usługi Application Insights lub jeśli nie masz już po jego utworzeniu. Można użyć z zasobem, który jest już używany do rejestrowania zdarzeń niestandardowych wymagane.

Podczas tworzenia nowego zasobu usługi Application Insights, wszystkie typy aplikacji są prawidłowe dla tego scenariusza. Wybierz jedną, która najlepiej pasuje do platformy, którego używasz.

Wymagany jest klucz Instrumentacji do tworzenia klienta telemetrii dla aplikacji. Możesz pobrać go na pulpicie nawigacyjnym portalu usługi Application Insights, lub możesz pobrać go ze strony Analiza ruchu wyszukiwania, wybierając wystąpienie, którego chcesz użyć.

## <a name="2---add-instrumentation"></a>2 — Dodawanie Instrumentacji

Ta faza to, gdzie Instrumentacji własną aplikację wyszukiwania przy użyciu zasobów usługi Application Insights utworzonej w poprzednim kroku. Istnieją cztery kroki, aby ten proces:

**Krok 1. Tworzenie klienta telemetrii** jest obiekt, który wysyła zdarzenia do zasobu usługi Application Insights.

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

**Krok 2. Wyszukaj identyfikator korelacji żądania** korelowanie żądań wyszukiwania za pomocą kliknięć, należy mieć identyfikator korelacji, który odnosi się te dwa następujące zdarzenia. Usługa Azure Search umożliwia identyfikator wyszukiwania na żądanie przy użyciu nagłówka:

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

**Krok 3. Rejestruj zdarzenia wyszukiwania**

Ilekroć dany wystawiono żądanie wyszukiwania przez użytkownika, należy rejestrować, jako zdarzenie wyszukiwania za pomocą następujących schemat niestandardowego zdarzenia usługi Application Insights:

**ServiceName**: nazwy usługi wyszukiwania (ciąg) **SearchId**: Unikatowy identyfikator (globalny guid) zapytanie wyszukiwania (osiągnięty w odpowiedzi wyszukiwania) **IndexName**: indeks usługi wyszukiwania (ciąg) jako Kwerenda **QueryTerms**: terminy wyszukiwania (ciąg), wprowadzone przez użytkownika **ResultCount**: (int) liczba dokumentów, które zostały zwrócone (osiągnięty w odpowiedzi wyszukiwania)  **ScoringProfile**: nazwę profilu oceniania używane, jeśli istnieje (ciąg)

> [!NOTE]
> Liczba żądań na zapytaniach wygenerowane przez użytkowników, dodając $count = true zapytania wyszukiwania. Zobacz więcej informacji na [tutaj](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Pamiętaj, aby zalogować się wyłącznie kwerend wyszukiwania, które są generowane przez użytkowników.
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

**Krok 4. Kliknij przycisk Zaloguj zdarzenia**

Każdym kliknięciu w dokumencie, który jest sygnałem, który musi być zalogowany na potrzeby analizy wyszukiwania. Zdarzenia niestandardowe z usługi Application Insights umożliwia rejestruje te zdarzenia za pomocą następującego schematu:

**ServiceName**: nazwy usługi wyszukiwania (ciąg) **SearchId**: Unikatowy identyfikator (globalny guid) zapytania powiązane z wyszukiwaniem **identyfikator**: identyfikator dokumentu (ciąg) **pozycji**: strona wyników rangi (int) dokumentu w wyszukiwaniu

> [!NOTE]
> Pozycja odnosi się do kardynalnej kolejność, w aplikacji. Jest bezpłatna ustawić tę liczbę, tak długo, jak jest zawsze taki sam, aby zezwolić na porównania.
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

## <a name="3---analyze-in-power-bi"></a>3\. analiza w usłudze Power BI

Po instrumentacji aplikacji i sprawdzono, czy aplikacja jest prawidłowo podłączone do usługi Application Insights, można użyć wstępnie zdefiniowany szablon utworzony przez usługę Azure Search dla usługi Power BI desktop. 

Usługa Azure search oferuje funkcje monitorowania [pakiet zawartości usługi Power BI](https://app.powerbi.com/getdata/services/azure-search) tak, aby analizować dane dzienników. T pakietu zawartości dodaje wstępnie zdefiniowanych wykresów i tabel przydatne podczas analizowania dodatkowe dane przechwycone dla analiza ruchu wyszukiwania. Aby uzyskać więcej informacji, zobacz [stronę pomocy pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/). 

1. Na platformie Azure wyszukiwania w okienku nawigacji po lewej stronie pulpitu nawigacyjnego, w obszarze **ustawienia**, kliknij przycisk **analiza ruchu wyszukiwania**.

2. Na **analiza ruchu wyszukiwania** strony, w kroku 3, kliknij przycisk **pobieranie programu Power BI Desktop** do zainstalowania usługi Power BI.

   ![Pobieranie raportów usługi Power BI](./media/search-traffic-analytics/get-use-power-bi.png "raportów usługi Power BI uzyskać")

2. W tej samej stronie, kliknij polecenie **raportu usługi Power BI z Pobierz**.

3. Raport zostanie otwarty w programie Power BI Desktop, a następnie zostanie wyświetlony monit o połączenie z usługą Application Insights. Te informacje można znaleźć na stronach portalu platformy Azure dla Ciebie zasobem usługi Application Insights.

   ![Połączenie z usługą Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "połączenie z usługą Application Insights")

4. Kliknij przycisk **obciążenia**.

Raport zawiera wykresy i tabele, które ułatwiają podejmowanie bardziej świadomych decyzji, aby zwiększyć swoją wydajność wyszukiwania i znaczenie dla.

Metryki zawierają następujące elementy:

* Kliknij przycisk przy użyciu stawki kont (.): współczynnik użytkowników, którzy klikną określonego dokumentu do liczby całkowitej wyszukiwania.
* Wyszukiwania bez kliknięcia: warunki dla pierwszych zapytań, które nie klika przycisk Zarejestruj
* Najbardziej kliknięto dokumentów: najbardziej kliknięto dokumentów za pomocą Identyfikatora w ostatnich 24 godzin, 7 dni i 30 dni.
* Pary popularnych dokumentów termin: warunki, które wynikają z tego samego dokumentu kliknięto, uporządkowane według kliknięć.
* Czas kliknij: kliknięć zasobnikach, w okresie od zapytania wyszukiwania

Poniższy zrzut ekranu przedstawia wbudowanych raportów i wykresów na potrzeby analizowania, analiza ruchu wyszukiwania.

![Pulpit nawigacyjny usługi Power BI dla usługi Azure Search](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "nawigacyjnym usługi Power BI dla usługi Azure Search")

## <a name="next-steps"></a>Kolejne kroki
Instrumentuj swoją aplikację wyszukiwania, aby uzyskać zaawansowane i wnikliwe dane o usługi wyszukiwania.

Więcej informacji można znaleźć na [usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) i odwiedź stronę [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-insights/) Aby dowiedzieć się więcej na temat ich różnych warstwach usług.

Dowiedz się więcej na temat tworzenia zachwycającymi raportami. Zobacz [wprowadzenie do usługi Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) Aby uzyskać szczegółowe informacje

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
