---
title: Usługi Azure Application Insights — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: 815388db673673a3802f8e5e515b7e16cb180a29
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323358"
---
# <a name="application-insights-frequently-asked-questions"></a>Usługa Application Insights: Często zadawane pytania

## <a name="configuration-problems"></a>Problemy z konfiguracją
*Mam problem z ustawienie mojej:*

* [Aplikacja platformy .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorowanie aplikacji już działa](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnostyka Azure](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Aplikacje internetowe w języku Java](app-insights-java-troubleshoot.md)

*Pojawia się żadne dane z serwera*

* [Wyjątki zapory dla zestawu](app-insights-ip-addresses.md)
* [Konfigurowanie serwera ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Konfigurowanie serwera języka Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Można używać usługi Application Insights za pomocą...?

* [Aplikacje sieci Web na serwerze usług IIS — lokalnie lub na maszynie wirtualnej](app-insights-asp-net.md)
* [Aplikacje sieci web w języku Java](app-insights-java-get-started.md)
* [Aplikacje Node.js](app-insights-nodejs.md)
* [Aplikacje sieci Web na platformie Azure](app-insights-azure-web-apps.md)
* [Usługi w chmurze na platformie Azure](app-insights-cloudservices.md)
* [Serwery aplikacji działające w platformy Docker](app-insights-docker.md)
* [Aplikacje jednej strony sieci web](app-insights-javascript.md)
* [Program SharePoint](app-insights-sharepoint.md)
* [Aplikacja klasyczna Windows](app-insights-windows-desktop.md)
* [Inne platformy](app-insights-platforms.md)

## <a name="is-it-free"></a>Jest to bezpłatne?

Tak — Użyj eksperymentalne. W podstawowego planu cenowego aplikację można wysyłać pewne przydział danych każdego miesiąca bezpłatnie. Bezpłatny przydział jest wystarczająco duży, aby cover tworzenia i publikowania aplikacji dla małej liczby użytkowników. Możesz ustawić limit, aby uniemożliwić więcej niż określoną ilość danych przetwarzanych.

Większe ilości danych telemetrycznych są naliczane za Gb. Firma Microsoft zapewnia kilka porad na temat sposobu [ograniczenie opłat](app-insights-pricing.md).

Enterprise plan jest naliczana opłata za każdy dzień, w którym każdego węzła serwera sieci web wysyła dane telemetryczne. Jest on odpowiedni, jeśli chcesz użyć funkcji eksportu ciągłego na dużą skalę.

[Odczyt planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Jaka jest wyceny?

* Otwórz **użycie i szacowane koszty strony** strony zasobu usługi Application Insights. Brak wykres ostatnie informacje o użyciu. Możesz ustawić limit ilości danych, jeśli chcesz.
* Otwórz [blok rozliczeń platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) wyświetlić rachunków wszystkich zasobów.

## <a name="q14"></a>Co to usługa Application Insights zmodyfikować w projekcie?
Szczegółowe informacje są zależne od typu projektu. Dla aplikacji sieci web:

* Dodaje te pliki do projektu:

  * ApplicationInsights.config.
  * ai.js
* Instaluje te pakiety NuGet:

  * *Interfejs API usługi Application Insights* -podstawowego interfejsu API
  * *Interfejs API usługi Application Insights dla aplikacji sieci Web* — używane do wysyłania danych telemetrycznych z serwera
  * *Interfejs API usługi Application Insights dla aplikacji JavaScript* — umożliwia wysyłanie danych telemetrycznych z klienta

    Pakiety zawierają te zestawy:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Wstawia elementy do:

  * Web.config
  * packages.config
* (Nowe wyłącznie dla projektów — Jeśli użytkownik [Dodaj usługę Application Insights do istniejącego projektu][start], trzeba to zrobić ręcznie.) Wstawia fragmentów kodu do kodu klienta i serwera, do ich inicjowania identyfikatorem zasobu usługi Application Insights Na przykład w aplikacji MVC kod jest wstawiany do strony wzorcowej Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak uaktualnić ze starszych wersji zestawu SDK
Zobacz [informacje o wersji](app-insights-release-notes.md) dla zestawu SDK, które są odpowiednie dla danego typu aplikacji.

## <a name="update"></a>Jak zmienić mój projekt wysyła dane do zasobów platformy Azure?
W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy `ApplicationInsights.config` i wybierz polecenie **aktualizacji Application Insights**. Możesz wysłać dane do istniejącego lub nowego zasobu na platformie Azure. Kreator aktualizacji zmienia klucz Instrumentacji w pliku ApplicationInsights.config, który określa, gdzie zestaw SDK wysyła dane. O ile nie wyłączysz "Aktualizuj wszystkie", spowoduje również zmianę klucza, gdzie pojawia się on na stronach sieci web.

## <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, używanej na serwerze sieci web usług IIS w celu skonfigurowania usługi Application Insights w aplikacji sieci web. Nie zbiera on telemetrii: zostanie ona zatrzymana, gdy nie konfigurujesz aplikację. 

[Dowiedz się więcej](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jakie dane telemetrii są zbierane przez usługę Application Insights?

Z aplikacji sieci web server:

* Żądania HTTP
* [Zależności](app-insights-asp-net-dependencies.md). Wywołania: Bazy danych SQL; Połączeń HTTP z usług zewnętrznych. Azure Cosmos DB, tabeli, magazynu obiektów blob i kolejki. 
* [Wyjątki](app-insights-asp-net-exceptions.md) i stos danych śledzenia.
* [Liczniki wydajności](app-insights-performance-counters.md) — Jeśli używasz [Monitora stanu](app-insights-monitor-performance-live-website-now.md), [monitorowania platformy Azure](app-insights-azure-web-apps.md) lub [składnika usługi Application Insights zebrane zapisywania](app-insights-java-collectd.md).
* [Niestandardowe zdarzenia i metryki](app-insights-api-custom-events-metrics.md) czy kodu.
* [Dzienniki śledzenia](app-insights-asp-net-trace-logs.md) w przypadku skonfigurowania odpowiedniego modułu zbierającego.

Z [stron sieci web klienta](app-insights-javascript.md):

* [Liczba wyświetleń strony](app-insights-usage-overview.md)
* [Wywołania AJAX](app-insights-asp-net-dependencies.md) żądania wysłane z uruchamianie skryptu.
* Załaduj dane dotyczące wyświetleń stron
* Liczby użytkowników i sesji
* [Uwierzytelniony użytkownik identyfikatorów](app-insights-api-custom-events-metrics.md#authenticated-users)

Z innych źródeł, jeśli można je skonfigurować:

* [Diagnostyka Azure](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Kontenery platformy docker](app-insights-docker.md)
* [Importuj tabele do analizy](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Program Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Można odfiltrować lub zmodyfikować dane telemetryczne?

Tak, na serwerze można napisać:

* Procesor danych telemetrycznych do filtrowania lub dodać właściwości do elementów wybranych danych telemetrycznych, przed wysłaniem ich z poziomu aplikacji.
* Inicjator telemetrii w celu dodania właściwości dla wszystkich elementów telemetrii.

Dowiedz się więcej na [ASP.NET](app-insights-api-filtering-sampling.md) lub [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Jak miasto, kraj i inne dane lokalizacji geograficznej obliczane są?

Możemy wyszukać adres IP (IPv4 lub IPv6) klienta sieci web przy użyciu [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria przeglądarki: Firma Microsoft gromadzi adres IP nadawcy.
* Dane telemetryczne serwera: Moduł usługi Application Insights zbiera informacje o adres IP klienta. Nie są zbierane, jeśli `X-Forwarded-For` jest ustawiona.

Można skonfigurować `ClientIpHeaderTelemetryInitializer` można przyjąć adresu IP z różnych nagłówka. W niektórych systemach, na przykład, jest przenoszony przez serwer proxy, należy załadować równoważenia lub w sieci CDN `X-Originating-IP`. [Dowiedz się więcej](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Możesz [wykorzystać usługę Power BI](app-insights-export-power-bi.md) Aby wyświetlić dane telemetryczne żądania na mapie.


## <a name="data"></a>Jak długo dane są przechowywane w portalu? Czy jest ono bezpieczne?
Przyjrzyj się [retencji danych i prywatności][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Dane osobowe (PII) zostaną wysłane dane telemetryczne?

Jest to możliwe, jeśli kod wysyła takich danych. Również możliwe, że zmienne w śladach stosu zawierają dane osobowe. Twój zespół deweloperów należy przeprowadzić ocenę ryzyka, aby upewnić się, czy też danych osobowych zapewniona jest prawidłowa obsługa. [Dowiedz się więcej na temat przechowywania danych i prywatności](app-insights-data-retention-privacy.md).

**Wszystkie** oktety adresu sieci web klienta są zawsze ustawione na 0, po są wyszukiwane atrybutów lokalizacji geograficznej.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Mój klucz Instrumentacji jest widoczna w źródle strony sieci web. 

* Jest to powszechną praktyką w rozwiązania do monitorowania.
* Nie może służyć do kradzieży danych.
* Może służyć fałszować alertów danych lub wyzwalacza.
* Firma Microsoft nie wiesz, że każdy klient miał takie problemy.

Można wykonać następujące akcje:

* Użyj dwóch oddzielnych kluczy Ikey (oddzielne zasoby usługi Application Insights), w przypadku danych klienta i serwera. Lub
* Napisz serwera proxy, który jest uruchamiany na serwerze, a klient sieci web, wysyłanie danych za pośrednictwem tego serwera proxy.

## <a name="post"></a>Jak sprawdzić danych POST w wyszukiwaniu diagnostycznym?
Firma Microsoft nie rejestruj danych POST automatycznie, ale można użyć wywołania TrackTrace: Umieść dane w parametrze wiadomości. Ma ograniczenie dłużej niż limity właściwości ciągów, chociaż nie można filtrować na nim.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Należy użyć jednego lub wielu zasobów usługi Application Insights?

Użyj pojedynczego zasobu dla wszystkich składników lub ról w systemie biznesowej. Użyj oddzielne zasoby dla rozwoju, testowania i wersji, a niezależnie od aplikacji.

* [Zobacz Omówienie w tym miejscu](app-insights-separate-resources.md)
* [Przykład — usługa w chmurze przy użyciu ról sieci web i proces roboczy](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak dynamicznie zmienić klucz Instrumentacji?

* [W tym miejscu dyskusji](app-insights-separate-resources.md)
* [Przykład — usługa w chmurze przy użyciu ról sieci web i proces roboczy](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Co to są użytkowników i sesji jest liczone?

* Zestaw SDK JavaScript ustawia plik cookie użytkownika na komputerze klienckim w sieci web, do identyfikowania użytkowników zwracanych i plik cookie sesji do grupowania działań.
* Jeśli nie ma żadnych skryptu po stronie klienta, możesz to zrobić [ustawiać pliki cookie na serwerze](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Jeśli jeden rzeczywistego użytkownika używa witryny w różnych przeglądarkach lub za pomocą przeglądania w-trybie prywatnym/incognito lub różnych komputerach, a następnie ich będzie zliczenia więcej niż jeden raz.
* Aby zidentyfikować zalogowanego użytkownika, maszyn i przeglądarek, dodaj wywołanie [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> Czy włączono wszystkiego w usłudze Application Insights?
| Co powinno zostać wyświetlone | Jak przygotować | Dlaczego chcesz |
| --- | --- | --- |
| Wykresy dostępności |[Testy sieci Web](app-insights-monitor-web-app-availability.md) |O tym, że Twoja aplikacja sieci web jest włączony |
| Serwer aplikacji danych o wydajności: czasy odpowiedzi... |[Dodaj usługę Application Insights do projektu](app-insights-asp-net.md) lub [Instalowanie Monitora stanu sztucznej Inteligencji na serwerze](app-insights-monitor-performance-live-website-now.md) (lub Napisz własny kod do [śledzić zależności](app-insights-api-custom-events-metrics.md#trackdependency)) |Wykrywanie problemom z wydajnością |
| Telemetria zależności usługi |[Zainstaluj Monitor stanu usługi sztucznej Inteligencji na serwerze](app-insights-monitor-performance-live-website-now.md) |Diagnozowanie problemów z bazy danych lub innymi składnikami zewnętrznymi |
| Uzyskiwanie śladów stosu wyjątków |[Wstawianie wywołań metoda TrackException w kodzie](app-insights-asp-net-exceptions.md) (ale niektóre są zgłaszane automatycznie) |Wykrywanie i diagnozowanie wyjątków |
| Wyszukiwanie śledzenia dzienników |[Dodaj kartę rejestrowania](app-insights-asp-net-trace-logs.md) |Diagnozowanie wyjątków, problemom z wydajnością |
| Podstawowe informacje dotyczące klienta użycia: wyświetleń strony, sesje,... |[Inicjator JavaScript na stronach sieci web](app-insights-javascript.md) |Analiza użycia |
| Metryki niestandardowe klienta |[Śledzenie wywołań na stronach sieci web](app-insights-api-custom-events-metrics.md) |Ulepszanie środowiska użytkownika |
| Metryki niestandardowe serwera |[Wywołania śledzenia na serwerze](app-insights-api-custom-events-metrics.md) |Analiza biznesowa |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Liczników na wykresach wyszukiwania i metryki są nierówne

[Próbkowanie](app-insights-sampling.md) zmniejsza liczbę elementów danych telemetrycznych (żądań, niestandardowe zdarzenia i tak dalej), które faktycznie są wysyłane z aplikacji do portalu. W polu wyszukiwania zobaczysz liczbę elementów w rzeczywistości odebrane. Wykresy metryk, które wyświetla liczbę zdarzeń zobaczysz liczbę oryginalnego zdarzenia, które wystąpiły. 

Każdy element, który jest przesyłane wykonuje `itemCount` reprezentuje właściwość, która zawiera liczbę zdarzeń oryginalnego tego elementu. Aby obserwować próbkowania w operacji, można uruchomić tego zapytania w usłudze Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatyzacja

### <a name="configuring-application-insights"></a>Konfigurowanie usługi Application Insights

Możesz [pisanie skryptów programu PowerShell](app-insights-powershell.md) przy użyciu usługi Azure Monitor zasobów do:

* Tworzenie i aktualizowanie zasobów usługi Application Insights.
* Ustaw plan cenowy.
* Uzyskiwanie klucza instrumentacji.
* Dodaj alert metryki.
* Dodaj test dostępności.

Nie można skonfigurować raportów Eksploratora metryk lub skonfigurować Eksport ciągły.

### <a name="querying-the-telemetry"></a>Badanie danych telemetrycznych

Użyj [interfejsu API REST](https://dev.applicationinsights.io/) do uruchomienia [Analytics](app-insights-analytics.md) zapytania.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak ustawić alert na zdarzenie

Alerty platformy Azure znajdują się tylko na metryki. Utwórz Metryka niestandardowa, która przekracza próg wartości, przy każdym wystąpieniu zdarzenia. Następnie Ustawianie alertu na metrykę. Należy pamiętać, że: będziesz otrzymywać powiadomienie, gdy Metryka przekracza wartość progową w dowolnym kierunku; nie otrzymasz powiadomień do momentu przekroczenia pierwszy, niezależnie od tego, czy wartość początkowa wysokiej lub niskiej; zawsze jest opóźnienie za kilka minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Czy istnieją opłaty za transfer danych między aplikacją sieci web platformy Azure i usługi Application Insights?

* Jeśli aplikacja internetowa platformy Azure znajduje się w centrum danych w przypadku punktu końcowego zbierania danych usługi Application Insights, nie ma opłat. 
* Jeśli istnieje punkt końcowy zbierania danych nie w centrum danych hosta, a następnie telemetrię aplikacji spowoduje naliczenie opłaty za [platformy Azure, opłaty za wychodzący](https://azure.microsoft.com/pricing/details/bandwidth/).

To nie są zależne od hostujące zasobu usługi Application Insights. Zależy to po prostu dystrybucji naszych punktów końcowych.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Czy mogę wysyłać dane telemetryczne do portalu usługi Application Insights?

Firma Microsoft zaleca korzystanie z naszych zestawów SDK i użyj [interfejs API zestawu SDK](app-insights-api-custom-events-metrics.md). Istnieje wariantów zestawu SDK dla różnych [platform](app-insights-platforms.md). Te zestawy SDK obsługują buforowania, kompresji, ograniczanie przepustowości, ponownych prób i tak dalej. Jednak [schematu pozyskiwania](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) i [punktu końcowego protokołu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) były publiczne.

## <a name="can-i-monitor-an-intranet-web-server"></a>Czy mogę monitorować serwer sieci web do sieci intranet

Poniżej przedstawiono dwie metody:

### <a name="firewall-door"></a>Drzwi zapory

Zezwalaj na serwerze sieci web do wysyłania telemetrii do naszych punktów końcowych https://dc.services.visualstudio.com:443 i https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Serwer proxy

Kierować ruch z serwera do bramy w sieci intranet overwritting tych ustawień w przykładzie plik ApplicationInsights.config. Jeśli te właściwości "Punktu końcowego" nie są obecne w pliku config, w ramach tych zajęć będzie używać wartości domyślne, pokazana w poniższym przykładzie.

#### <a name="example-applicationinsightsconfig"></a>Przykładowy plik ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <TelemetryChannel>
         <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
    </TelemetryChannel>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

_Uwaga ApplicationIdProvider jest dostępna, począwszy od v2.6.0_

Brama powinna kierować ruch do https://dc.services.visualstudio.com:443

Zastąp wartości powyżej za pomocą: `http://<your.gateway.address>/<relative path>`
 
Przykład: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Na serwerze sieci intranet można uruchomić testy sieci web dostępności?

Nasze [testy sieci web](app-insights-monitor-web-app-availability.md) systemem punktów obecności, które są dystrybuowane na całym świecie. Istnieją dwa rozwiązania:

* Zapory drzwi — zezwalanie na żądania do serwera z [długich i mogły być zmieniane listę agentów testów sieci web](app-insights-ip-addresses.md).
* Napisz własny kod, aby wysyłać okresowe żądania do serwera z wewnątrz sieci intranet. Można uruchomić testów sieci web programu Visual Studio, w tym celu. Tester można wysłać wyniki do usługi Application Insights przy użyciu interfejsu API TrackAvailability().

## <a name="more-answers"></a>Więcej odpowiedzi
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
