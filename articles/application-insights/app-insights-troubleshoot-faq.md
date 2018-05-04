---
title: Informacje na temat technologii aplikacji Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: mbullwin
ms.openlocfilehash: d7abfd1ac6f914c75297ff49462590e5b6169dbd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="application-insights-frequently-asked-questions"></a>Usługa Application Insights: Często zadawane pytania

## <a name="configuration-problems"></a>Problemy z konfiguracją
*Mam problemy ustawienie mojej:*

* [Aplikacja .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorowanie aplikacji już uruchomione](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Diagnostyka Azure](app-insights-azure-diagnostics.md)
* [Aplikacje sieci Web w języku Java](app-insights-java-troubleshoot.md)

*Pobrać żadnych danych z serwerem*

* [Wyjątki zapory zestawu](app-insights-ip-addresses.md)
* [Konfigurowanie serwera ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Konfigurowanie serwera Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Czy za pomocą usługi Application Insights z...?

* [Aplikacje sieci Web na serwerze IIS — lokalnie lub na maszynie wirtualnej](app-insights-asp-net.md)
* [Aplikacje sieci web Java](app-insights-java-get-started.md)
* [Aplikacje Node.js](app-insights-nodejs.md)
* [Aplikacje sieci Web na platformie Azure](app-insights-azure-web-apps.md)
* [Usługi w chmurze na platformie Azure](app-insights-cloudservices.md)
* [Serwery aplikacji uruchomionych w Docker](app-insights-docker.md)
* [Aplikacje jednej strony sieci web](app-insights-javascript.md)
* [Program SharePoint](app-insights-sharepoint.md)
* [Aplikacji klasycznej systemu Windows](app-insights-windows-desktop.md)
* [Inne platformy](app-insights-platforms.md)

## <a name="is-it-free"></a>Jest bezpłatny?

Tak, użyj eksperymentalne. W podstawowego planu cenowego aplikację można wysyłać niektóre dodatku danych miesięcznie bezpłatnie. Bezpłatne dodatek jest wystarczająco duży, obejmują programowanie i publikowanie aplikacji dla niewielkiej liczby użytkowników. Można ustawić zakończenia, aby uniemożliwić więcej niż określonej ilości danych przetwarzanych.

Większych ilości danych telemetrycznych są naliczane za Gb. Firma Microsoft udostępnia porady na temat [ograniczyć Twojej opłat](app-insights-pricing.md).

Enterprise plan wiąże opłat codziennie każdego węzła serwera sieci web wysyła dane telemetryczne. Jest on odpowiedni, jeśli chcesz użyć funkcji eksportu ciągłego na dużą skalę.

[Przeczytaj planu cenowego](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Jaka jest wyceny?

* Otwórz **strona szacowanych kosztów i użycia** strony w zasobu usługi Application Insights. Brak wykres ostatnie informacje o użyciu. Ograniczenie wolumin danych, można ustawić, jeśli chcesz.
* Otwórz [bloku rozliczenia Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) aby zobaczyć rachunków we wszystkich zasobów.

## <a name="q14"></a>Co to usługa Application Insights zmodyfikować w projekcie?
Szczegóły są zależne od typu projektu. Dla aplikacji sieci web:

* Dodaje te pliki do projektu:

  * ApplicationInsights.config.
  * ai.js
* Instaluje te pakiety NuGet:

  * *Interfejsu API usługi Application Insights* -core API
  * *Interfejsu API usługi Application Insights dla aplikacji sieci Web* — używane do wysyłania danych telemetrycznych z serwera
  * *Aplikacja interfejsu API Insights dla aplikacji JavaScript* — używane do wysyłania danych telemetrycznych z klienta

    Pakiety są te zestawy:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Wstawia elementy do:

  * Web.config
  * packages.config
* (Nowe projekty tylko — jeśli możesz [dodać usługi Application Insights do istniejącego projektu][start], należy to zrobić ręcznie.) Wstawia wstawki kodu klienta i serwera zainicjować o identyfikatorze zasobu usługi Application Insights. Na przykład w aplikacji MVC kodu zostaną wstawione do strony głównej Views/Shared/_Layout.cshtml

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak uaktualnić ze starszych wersji zestawu SDK?
Zobacz [informacje o wersji](app-insights-release-notes.md) dla zestawu SDK, które są odpowiednie do typu aplikacji.

## <a name="update"></a>Jak zmienić projektu wysyła dane do zasobów platformy Azure?
W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy `ApplicationInsights.config` i wybierz polecenie **usługi Application Insights dla aktualizacji**. Możesz wysłać dane do istniejącego lub nowego zasobu platformy Azure. Kreator aktualizacji zmiany klucza Instrumentacji w ApplicationInsights.config, która określa, gdzie serwer SDK wysyła dane. Jeśli nie możesz usunąć zaznaczenie "Aktualizuj wszystkie", spowoduje również zmianę klucza, gdzie jest dostępny na stronach sieci web.

## <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna można użyć w serwerze sieci web usług IIS w celu skonfigurowania usługi Application Insights w aplikacjach sieci web. Nie zbieraj danych telemetrii: zostanie ona zatrzymana, gdy nie konfigurujesz aplikacji. 

[Dowiedz się więcej](app-insights-monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jakie dane telemetryczne są zbierane przez usługę Application Insights?

Z aplikacji sieci web serwera:

* Żądania HTTP
* [Zależności](app-insights-asp-net-dependencies.md). Wywołania: baz danych; Wywołania HTTP usług zewnętrznych; Azure DB rozwiązania Cosmos, tabeli magazynu obiektów blob i kolejki. 
* [Wyjątki](app-insights-asp-net-exceptions.md) i ślady stosu.
* [Liczniki wydajności](app-insights-performance-counters.md) — Jeśli używasz [Monitor stanu](app-insights-monitor-performance-live-website-now.md), [monitorowania Azure](app-insights-azure-web-apps.md) lub [zapisywania collectd usługi Application Insights](app-insights-java-collectd.md).
* [Niestandardowe zdarzenia i metryki](app-insights-api-custom-events-metrics.md) czy kodu.
* [Dzienniki śledzenia](app-insights-asp-net-trace-logs.md) konfigurując odpowiednie modułu zbierającego.

Z [stron sieci web klienta](app-insights-javascript.md):

* [Zlicza widoku strony](app-insights-web-track-usage.md)
* [Wywołania AJAX](app-insights-asp-net-dependencies.md) żądań z uruchamianie skryptu.
* Dane ładowania stron widoku
* Liczba użytkowników i sesji
* [Uwierzytelniony użytkownik identyfikatorów](app-insights-api-custom-events-metrics.md#authenticated-users)

Z innych źródeł, jeśli je skonfigurować:

* [Diagnostyka Azure](app-insights-azure-diagnostics.md)
* [Kontenery docker](app-insights-docker.md)
* [Importowanie tabel do analityka](app-insights-analytics-import.md)
* [Log Analytics](https://azure.microsoft.com/blog/omssolutionforappinsightspublicpreview/)
* [Logstash](app-insights-analytics-import.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Można odfiltrować lub zmodyfikować niektóre telemetrii?

Tak, na serwerze można napisać:

* Procesor telemetrii filtrowanie, lub dodać właściwości do telemetrii wybranych elementów przed ich wysłaniem z aplikacji.
* Inicjator telemetrii pozwala dodać właściwości dla wszystkich elementów telemetrii.

Dowiedz się więcej na [ASP.NET](app-insights-api-filtering-sampling.md) lub [Java](app-insights-java-filter-telemetry.md).

## <a name="how-are-city-country-and-other-geo-location-data-calculated"></a>Sposób Miasto, kraj i innych danych lokalizacji geo obliczania?

Możemy odszukać adres IP (IPv4 lub IPv6) za pomocą klienta sieci web [GeoLite2](http://dev.maxmind.com/geoip/geoip2/geolite2/).

* Dane telemetryczne przeglądarki: zbieramy adres IP nadawcy.
* Serwer telemetrii: moduł usługi Application Insights zbiera adres IP klienta. Nie są zbierane, jeśli `X-Forwarded-For` jest ustawiona.

Można skonfigurować `ClientIpHeaderTelemetryInitializer` można pobrać adresu IP z różnych nagłówka. W niektórych systemach, na przykład jest przenoszony przez serwer proxy, załadowanie modułu równoważenia lub sieć CDN ma `X-Originating-IP`. [Dowiedz się więcej](http://apmtips.com/blog/2016/07/05/client-ip-address/).

Możesz [przy użyciu usługi Power BI](app-insights-export-power-bi.md) do wyświetlenia telemetrii żądania na mapie.


## <a name="data"></a>Jak długo dane są przechowywane w portalu Czy jest bezpieczna?
Spójrz na [przechowywanie danych i ochrona prywatności][data].

## <a name="might-personally-identifiable-information-pii-be-sent-in-the-telemetry"></a>Identyfikowalne dane osobowe (dane osobowe) zostaną wysłane dane telemetryczne?

Jest to możliwe, jeśli takie dane są wysyłane kodu. Możliwe również, czy zmienne w śladów stosu zawierają dane osobowe. Zespół deweloperów należy przeprowadzić ocenę ryzyka, aby upewnić się, czy dane osobowe poprawną obsługę. [Dowiedz się więcej o przechowywanie danych i ochrony prywatności](app-insights-data-retention-privacy.md).

**Wszystkie** oktety adresu internetowego klient zawsze są ustawione na 0, po atrybuty lokalizacji geo są wyszukiwane.

## <a name="my-ikey-is-visible-in-my-web-page-source"></a>Moje iKey jest widoczna w źródle strony sieci web. 

* To jest typowym rozwiązaniem w monitorowanie rozwiązań.
* Nie można użyć do kradzieży danych.
* Może służyć fałszować alerty danych lub wyzwalacza.
* Firma Microsoft nie podano miał żadnych odbiorca takich problemów.

Można:

* Dwa oddzielne iKeys (oddzielnie zasobów usługi Application Insights), należy użyć danych klienta i serwera. Lub
* Napisz serwer proxy, który jest uruchamiany na serwerze, a ma wysyłać dane przy użyciu tego serwera proxy klienta sieci web.

## <a name="post"></a>Jak wyświetlić dane POST w wyszukiwaniu diagnostyczne?
Dane POST nie rejestrowane automatycznie, ale może użyć wywołania TrackTrace: Umieść dane w parametrze wiadomości. Ma ograniczenie dłużej niż limity właściwości ciągów, chociaż nie można filtrować na nim.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Należy użyć jednego lub wielu zasobów usługi Application Insights?

Użyj pojedynczego zasobu dla wszystkich składników lub ról w systemie biznesowej. Rozwoju, testów oraz wersji i niezależnie od aplikacji należy używać oddzielnych zasobów.

* [Zawiera omówienie tutaj](app-insights-separate-resources.md)
* [Przykład — usługa w chmurze z rolami sieci web i proces roboczy](app-insights-cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak dynamicznie zmienić klucza Instrumentacji?

* [Omówienie tutaj](app-insights-separate-resources.md)
* [Przykład — usługa w chmurze z rolami sieci web i proces roboczy](app-insights-cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Co to jest użytkownik i sesji liczby?

* Zestaw SDK JavaScript ustawia plik cookie użytkownika na komputerze klienckim w sieci web do identyfikowania użytkowników przekazujących, a plik cookie sesji grupy działań.
* Jeśli nie ma żadnego skryptu po stronie klienta, możesz [umieszczania plików cookie na serwerze](http://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Jeśli jeden rzeczywistych użytkownik korzysta z witryną w różnych przeglądarkach, albo za pomocą przeglądania w prywatnego/incognito lub różnych komputerach, a następnie ich będzie zliczenia więcej niż raz.
* Aby zidentyfikować zalogowanego użytkownika wiele maszyn i przeglądarki, dodaj wywołanie do [setAuthenticatedUserContext()](app-insights-api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a> I włączono wszystko w usłudze Application Insights?
| Należy wyświetlić | Jak przygotować | Dlaczego chcesz |
| --- | --- | --- |
| Wykresy dostępności |[Testy sieci Web](app-insights-monitor-web-app-availability.md) |Wiedzieć, że aplikacja sieci web |
| Wydajności aplikacji serwera: czas reakcji... |[Dodawanie usługi Application Insights do projektu](app-insights-asp-net.md) lub [Zainstaluj Monitor stanu AI na serwerze](app-insights-monitor-performance-live-website-now.md) (lub napisać własny kod do [śledzić zależności](app-insights-api-custom-events-metrics.md#trackdependency)) |Wykryj problemy dotyczące wydajności |
| Dane telemetryczne zależności |[Zainstaluj Monitor stanu usługi AI na serwerze](app-insights-monitor-performance-live-website-now.md) |Diagnozowanie problemów z bazy danych lub innych składników zewnętrznych |
| Uzyskanie śladów stosu wyjątków |[Wstawianie wywołania TrackException w kodzie](app-insights-asp-net-exceptions.md) (ale niektóre są raportowane automatycznie) |Wykrywanie i diagnozowanie wyjątków |
| Ślady dziennika wyszukiwania |[Dodaj kartę rejestrowania](app-insights-asp-net-trace-logs.md) |Diagnozowanie wyjątków, problemy dotyczące wydajności |
| Podstawowe informacje dotyczące użycia klienta: wyświetleń strony, sesje,... |[Inicjator JavaScript na stronach sieci web](app-insights-javascript.md) |Analiza użycia |
| Metryki niestandardowe klienta |[Śledzenie wywołuje na stronach sieci web](app-insights-api-custom-events-metrics.md) |Ulepszanie środowiska użytkownika |
| Metryki niestandardowe serwera |[Śledzenie wywołań serwera](app-insights-api-custom-events-metrics.md) |Analiza biznesowa |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Liczby na wykresach wyszukiwania i metryki są równe

[Próbkowanie](app-insights-sampling.md) zmniejsza liczbę elementów telemetrii (żądania, niestandardowych zdarzeń itd.), które faktycznie są wysyłane z aplikacji do portalu. W wyszukiwaniu zostanie wyświetlony liczba elementów w rzeczywistości odebrane. Na wykresach metryki, które wyświetla liczbę zdarzeń znajduje się numer oryginalnego zdarzeń, które wystąpiły. 

Każdy element, który jest przesyłane posiada `itemCount` reprezentuje właściwość, która zawiera liczbę zdarzeń oryginalnego elementu. Aby przyjrzeć się próbkowania w operacji, możesz uruchomić to zapytanie w module analiz:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatyzacja

### <a name="configuring-application-insights"></a>Konfigurowanie usługi Application Insights

Możesz [pisanie skryptów PowerShell](app-insights-powershell.md) za pomocą Monitora zasobów platformy Azure do:

* Tworzenie i aktualizowanie zasobów usługi Application Insights.
* Ustaw planu cenowego.
* Pobierz klucz instrumentacji.
* Dodawanie metryki alertu.
* Dodaj test dostępności.

Nie można skonfigurować raportu metryki Explorer lub ustawienie Eksport ciągły.

### <a name="querying-the-telemetry"></a>Wykonywanie zapytania telemetrii

Użyj [interfejsu API REST](https://dev.applicationinsights.io/) do uruchomienia [Analytics](app-insights-analytics.md) zapytania.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak ustawić alert na zdarzenia?

Azure alerty są tylko dla metryki. Utwórz niestandardową metrykę przecina wartości progowej, przy każdym wystąpieniu wydarzenia. Następnie należy ustawić alert na metryki. Należy pamiętać, że: otrzymasz powiadomienie, zawsze, gdy metryki przekracza wartość progową w żadnym kierunku; użytkownik nie będzie otrzymywał powiadomienia do pierwszego przecięcia, niezależnie od tego, czy wartość początkowa to wysokie lub niskie; jest zawsze opóźnienie za kilka minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Czy istnieją opłat za transfer danych między aplikacją sieci web platformy Azure i usługi Application Insights?

* Jeśli aplikacja sieci Azure web znajduje się w centrum danych w przypadku punktu końcowego zbierania danych usługi Application Insights jest bez dodatkowych opłat. 
* Jeśli istnieje żaden punkt końcowy z kolekcji w centrum danych hosta, a następnie telemetrii aplikacji będą naliczane [Azure wychodzące opłat](https://azure.microsoft.com/pricing/details/bandwidth/).

To nie jest zależny od gdzie jest hostowana zasobu usługi Application Insights. Zależy tylko dystrybucji naszych punktów końcowych.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Czy można wysłać dane telemetryczne w portalu usługi Application Insights?

Firma Microsoft zaleca Użyj nasze zestawy SDK i użyj [interfejsu API zestawu SDK](app-insights-api-custom-events-metrics.md). Istnieje zestaw SDK dla różnych wariantów [platform](app-insights-platforms.md). Te zestawy SDK obsługuje buforowanie, kompresji, ograniczania przepustowości, ponownych prób i tak dalej. Jednak [schematu wprowadzanie](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) i [punktu końcowego protokołu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) są publiczne.

## <a name="can-i-monitor-an-intranet-web-server"></a>Można monitorować intranetowego serwera sieci web?

Poniżej przedstawiono dwie metody:

### <a name="firewall-door"></a>Drzwi zapory

Zezwalaj na serwerze sieci web wysłać dane telemetryczne do naszej punktów końcowych https://dc.services.visualstudio.com:443 i https://rt.services.visualstudio.com:443. 

### <a name="proxy"></a>Serwer proxy

Kierować ruchem z serwera do bramy w sieci intranet przy overwritting tych ustawień w przykładzie ApplicationInsights.config. Jeśli te właściwości "Punktu końcowego" nie są obecne w pliku config, te klasy będzie używać wartości domyślne pokazano w poniższym przykładzie.

#### <a name="example-applicationinsightsconfig"></a>Przykład ApplicationInsights.config:
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

Bramy należy kierować ruchem do https://dc.services.visualstudio.com:443

Zastąp wartości powyżej z: `http://<your.gateway.address>/<relative path>`
 
Przykład: 
```
http://<your.gateway.endpoint>/v2/track 
http://<your.gateway.endpoint>/api/profiles/{0}/apiId
```




## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Na serwerze sieci intranet można uruchomić testów sieci web dostępności?

Nasze [testów sieci web](app-insights-monitor-web-app-availability.md) uruchomić w punktach obecności, które są dystrybuowane na całym świecie. Istnieją dwa rozwiązania:

* Zapory drzwi — Zezwalaj na żądania do serwera z [długich i zmienić listę agentów testu sieci web](app-insights-ip-addresses.md).
* Napisać własny kod do wysyłania okresowych żądań do serwera z wewnątrz sieci intranet. Można uruchomić testów sieci web programu Visual Studio, w tym celu. Tester można wysyłać wyniki do usługi Application Insights, przy użyciu interfejsu API TrackAvailability().

## <a name="more-answers"></a>Więcej odpowiedzi
* [Application Insights forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md