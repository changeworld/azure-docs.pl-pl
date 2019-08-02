---
title: Azure Application Insights — często zadawane pytania | Microsoft Docs
description: Często zadawane pytania dotyczące Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: 778a95db8ce462d06e2464db56b542f8113a4960
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875374"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Często zadawane pytania

## <a name="configuration-problems"></a>Problemy z konfiguracją
*Mam problem z konfigurowaniem:*

* [Aplikacja platformy .NET](asp-net-troubleshoot-no-data.md)
* [Monitorowanie już uruchomionej aplikacji](monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostyka platformy Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Aplikacje internetowe w języku Java](java-troubleshoot.md)

*Nie otrzymuję żadnych danych z mojego serwera*

* [Ustawianie wyjątków zapory](ip-addresses.md)
* [Konfigurowanie serwera ASP.NET](monitor-performance-live-website-now.md)
* [Konfigurowanie serwera Java](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Czy można użyć Application Insights z...?

* [Aplikacje sieci Web na serwerze IIS na maszynie wirtualnej platformy Azure lub w zestawie skalowania maszyn wirtualnych platformy Azure](azure-vm-vmss-apps.md)
* [Aplikacje sieci Web na serwerze IIS — lokalnie lub na maszynie wirtualnej](asp-net.md)
* [Aplikacje sieci Web Java](java-get-started.md)
* [Aplikacje Node.js](nodejs.md)
* [Aplikacje sieci Web na platformie Azure](azure-web-apps.md)
* [Cloud Services na platformie Azure](cloudservices.md)
* [Serwery aplikacji działające w programie Docker](docker.md)
* [Jednostronicowe aplikacje sieci Web](javascript.md)
* [SharePoint](sharepoint.md)
* [Aplikacja klasyczna systemu Windows](windows-desktop.md)
* [Inne platformy](platforms.md)

## <a name="is-it-free"></a>Czy jest to bezpłatne?

Tak, w przypadku eksperymentalnego użycia. W podstawowym planie cenowym aplikacja może bezpłatnie wysyłać pewne opłaty za dane. Bezpłatny limit jest wystarczająco duży, aby pokryć rozwój i opublikować aplikację dla niewielkiej liczby użytkowników. Można ustawić limit, aby zapobiec przetwarzaniu większej ilości danych.

W przypadku większych ilości danych telemetrycznych są naliczone opłaty za GB. Oferujemy pewne porady dotyczące [ograniczania opłat](pricing.md).

Plan przedsiębiorstwa wiąże się z opłatą za każdy dzień, w którym każdy węzeł serwera sieci Web wysyła dane telemetryczne. Jest to przydatne, jeśli chcesz używać eksportu ciągłego na dużą skalę.

[Zapoznaj się z planem cenowym](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Ile kosztuje?

* Otwórz **stronę użycie i szacowane koszty** w zasobie Application Insights. Istnieje wykres przedstawiający ostatnie użycie. Jeśli chcesz, możesz ustawić limit ilości danych.
* Otwórz [blok rozliczeń platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) , aby wyświetlić rachunki dla wszystkich zasobów.

## <a name="q14"></a>Co Application Insights modyfikować w moim projekcie?
Szczegóły są zależne od typu projektu. Dla aplikacji sieci Web:

* Dodaje te pliki do projektu:

  * ApplicationInsights.config.
  * ai.js
* Instaluje te pakiety NuGet:

  * *Application INSIGHTS API* — podstawowy interfejs API
  * *Application INSIGHTS API dla aplikacji sieci Web* — służy do wysyłania danych telemetrycznych z serwera
  * *Application INSIGHTS API dla aplikacji JavaScript* — służy do wysyłania danych telemetrycznych z klienta

    Pakiety obejmują następujące zestawy:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Wstawia elementy do:

  * Web.config
  * packages.config
* (Tylko nowe projekty — w przypadku [dodania Application Insights do istniejącego projektu][start]należy to zrobić ręcznie). Wstawia fragmenty kodu klienta i serwera, aby je zainicjować przy użyciu identyfikatora zasobu Application Insights. Na przykład w aplikacji MVC kod jest wstawiany do widoku strony głównej/Shared/_Layout. cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak mogę uaktualnić ze starszych wersji zestawu SDK?
Zapoznaj się z [informacjami o wersji](release-notes.md) zestawu SDK, które są odpowiednie dla danego typu aplikacji.

## <a name="update"></a>Jak mogę zmienić zasób platformy Azure, do którego mój projekt wysyła dane?
W Eksplorator rozwiązań kliknij prawym przyciskiem `ApplicationInsights.config` myszy i wybierz polecenie **Aktualizuj Application Insights**. Dane można wysyłać do istniejącego lub nowego zasobu na platformie Azure. Kreator aktualizacji zmienia klucz Instrumentacji w pliku ApplicationInsights. config, który określa, gdzie zestaw SDK serwera wysyła dane. Chyba że usuniesz opcję "Aktualizuj wszystko", zostanie również zmieniony klucz, w którym pojawia się na stronach sieci Web.

## <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, która może być używana na serwerze sieci Web usług IIS w celu ułatwienia konfigurowania Application Insights w usłudze Web Apps. Nie zbiera ona danych telemetrycznych: możesz ją zatrzymać, gdy nie skonfigurujesz aplikacji. 

[Dowiedz się więcej](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Jakie dane telemetryczne są zbierane przez Application Insights?

Z aplikacji serwer sieci Web:

* Żądania HTTP
* [Zależności](asp-net-dependencies.md). Wywołania: Bazy danych SQL; Wywołania HTTP do usług zewnętrznych; Azure Cosmos DB, tabela, Magazyn obiektów blob i kolejka. 
* [Wyjątki](asp-net-exceptions.md) i ślady stosu.
* [Liczniki wydajności](performance-counters.md) — jeśli używasz [Monitor stanu](monitor-performance-live-website-now.md), [monitorowanie platformy Azure dla App Services](azure-web-apps.md), [monitorowanie platformy Azure dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych](azure-vm-vmss-apps.md)oraz [Application Insights zebrany moduł zapisujący](java-collectd.md).
* [Niestandardowe zdarzenia i metryki](api-custom-events-metrics.md) , które kodują.
* [Dzienniki śledzenia](asp-net-trace-logs.md) w przypadku skonfigurowania odpowiedniego modułu zbierającego.

Ze [stron sieci Web klienta](javascript.md):

* [Liczba wyświetleń stron](usage-overview.md)
* [Wywołania AJAX](asp-net-dependencies.md) Żądania wykonane z uruchomionego skryptu.
* Dane ładowania danych w widoku strony
* Liczba użytkowników i sesji
* [Identyfikatory uwierzytelnionych użytkowników](api-custom-events-metrics.md#authenticated-users)

Z innych źródeł, w przypadku ich skonfigurowania:

* [Diagnostyka platformy Azure](../platform/diagnostics-extension-to-application-insights.md)
* [Importuj do analizy](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Czy mogę odfiltrować lub zmodyfikować niektóre dane telemetryczne?

Tak, na serwerze można napisać:

* Procesor telemetrii do filtrowania lub dodawania właściwości do wybranych elementów telemetrii przed ich wysłaniem z aplikacji.
* Inicjator telemetrii, aby dodać właściwości do wszystkich elementów telemetrii.

Dowiedz się więcej na temat [ASP.NET](api-filtering-sampling.md) lub [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak są obliczane dane miejscowości, kraju/regionu i innych lokalizacji geograficznych?

Wyszukujemy adres IP (IPv4 lub IPv6) klienta sieci Web za pomocą [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria przeglądarki: Zbieramy adres IP nadawcy.
* Dane telemetryczne serwera: Moduł Application Insights zbiera adres IP klienta. Nie jest zbierane, jeśli `X-Forwarded-For` jest ustawiony.

Można skonfigurować, `ClientIpHeaderTelemetryInitializer` aby przyjmować adres IP z innego nagłówka. W niektórych systemach jest to na przykład przenoszone przez serwer proxy, moduł równoważenia obciążenia lub sieć CDN do `X-Originating-IP`programu. [Dowiedz się więcej](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Możesz [użyć Power BI](export-power-bi.md ) , aby wyświetlić dane telemetryczne żądania na mapie.


## <a name="data"></a>Jak długo dane są przechowywane w portalu? Czy jest zabezpieczony?
Zapoznaj się z [przechowywaniem i prywatnością danych][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Czy dane osobowe mogą być wysyłane w ramach telemetrii?

Jest to możliwe, jeśli kod wysyła takie dane. Może również wystąpić, jeśli zmienne w śladach stosu obejmują dane osobowe. Zespół programistyczny powinien przeprowadzić ocenę ryzyka, aby upewnić się, że dane osobowe są prawidłowo obsługiwane. [Dowiedz się więcej o przechowywaniu i ochronie danych](data-retention-privacy.md).

**Wszystkie** oktety adresu internetowego klienta zawsze mają ustawioną wartość 0 po wyszukiwaniu atrybutów lokalizacji geograficznej.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mój klucz Instrumentacji jest widoczny w źródle strony sieci Web. 

* Jest to typowa Metoda monitorowania rozwiązań.
* Nie można jej użyć do kradzieży danych.
* Może służyć do pochylania alertów dotyczących danych lub wyzwalaczy.
* Nie wiemy, że każdy klient miał takie problemy.

Możesz:

* Użyj dwóch oddzielnych kluczy Instrumentacji (oddzielnych zasobów Application Insights) dla danych klienta i serwera. Lub
* Napisz serwer proxy, który działa na serwerze i czy klient sieci Web wyśle dane za pomocą tego serwera proxy.

## <a name="post"></a>Jak mogę zobaczyć dane POST w wyszukiwaniu diagnostycznym?
Nie rejestrujemy danych do OPUBLIKOWANia automatycznie, ale można użyć wywołania TrackTrace: Umieść dane w parametrze komunikatu. Ten limit rozmiaru jest dłuższy niż limity właściwości ciągu, ale nie można go filtrować.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Czy należy używać jednego lub wielu zasobów Application Insights?

Użyj pojedynczego zasobu dla wszystkich składników lub ról w jednym systemie biznesowym. Używaj oddzielnych zasobów do tworzenia, testowania i wydawania wersji oraz dla niezależnych aplikacji.

* [Zapoznaj się z dyskusjami tutaj](separate-resources.md)
* [Przykład — usługa w chmurze z rolami procesów roboczych i sieci Web](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak mogę dynamicznie zmienić klucz Instrumentacji?

* [Dyskusja w tym miejscu](separate-resources.md)
* [Przykład — usługa w chmurze z rolami procesów roboczych i sieci Web](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Jakie są liczby użytkowników i sesji?

* Zestaw SDK języka JavaScript ustawia plik cookie użytkownika na kliencie sieci Web, aby zidentyfikować zwracających użytkowników i plik cookie sesji w celu pogrupowania działań.
* Jeśli nie istnieje skrypt po stronie klienta, można [ustawić pliki cookie na serwerze](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Jeśli jeden z rzeczywistych użytkowników korzysta z witryny w różnych przeglądarkach lub korzysta z funkcji przeglądania w trybie prywatnym/incognito lub różnych maszyn, zostaną one zliczone więcej niż jeden raz.
* Aby zidentyfikować zalogowanego użytkownika na maszynach i w przeglądarkach, należy dodać wywołanie do [setAuthenticatedUserContext ()](api-custom-events-metrics.md#authenticated-users).

## <a name="q17"></a>Czy mam wszystko, co jest dostępne w Application Insights?
| Co powinno zostać wyświetlone | Jak uzyskać | Dlaczego chcesz |
| --- | --- | --- |
| Wykresy dostępności |[Testy sieci Web](monitor-web-app-availability.md) |Znajomość aplikacji sieci Web |
| Wydajność aplikacji serwera: czasy odpowiedzi,... |[Dodaj Application Insights do projektu](asp-net.md) lub [Zainstaluj Monitor stanu AI na serwerze](monitor-performance-live-website-now.md) (lub napisz własny kod w celu [śledzenia zależności](api-custom-events-metrics.md#trackdependency)) |Wykrywanie problemów wydajności |
| Telemetria zależności |[Zainstaluj monitor stanu AI na serwerze](monitor-performance-live-website-now.md) |Diagnozowanie problemów z bazami danych lub innymi składnikami zewnętrznymi |
| Pobierz ślady stosu z wyjątków |[Wstaw wywołania instrukcji trackexception w kodzie](asp-net-exceptions.md) (niektóre są raportowane automatycznie) |Wykrywanie i diagnozowanie wyjątków |
| Wyszukaj ślady dziennika |[Dodawanie karty rejestrowania](asp-net-trace-logs.md) |Diagnozowanie wyjątków, problemy z wydajnością |
| Podstawowe informacje dotyczące użycia klientów: wyświetlenie stron, sesje,... |[Inicjator JavaScript na stronach sieci Web](javascript.md) |Analiza użycia |
| Metryki niestandardowe klienta |[Śledzenie wywołań na stronach sieci Web](api-custom-events-metrics.md) |Ulepszanie środowiska użytkownika |
| Metryki niestandardowe serwera |[Śledzenie wywołań na serwerze](api-custom-events-metrics.md) |Analiza biznesowa |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Dlaczego liczniki na wykresach wyszukiwania i metryk są nierówne?

[Próbkowanie](sampling.md) zmniejsza liczbę elementów telemetrycznych (żądań, zdarzeń niestandardowych itd.), które są faktycznie wysyłane z aplikacji do portalu. W obszarze wyszukiwanie zostanie wyświetlona liczba elementów, które faktycznie zostały odebrane. Na wykresach metryk, które wyświetlają liczbę zdarzeń, zobaczysz liczbę oryginalnych zdarzeń, które wystąpiły. 

Każdy element, który jest przesyłany `itemCount` , przenosi właściwość, która pokazuje, ile oryginalnych zdarzeń reprezentuje element. Aby obserwować próbkowanie w operacji, można uruchomić to zapytanie w analizie:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automatyzacja

### <a name="configuring-application-insights"></a>Konfigurowanie Application Insights

[Skrypty programu PowerShell można napisać](powershell.md) przy użyciu usługi Azure Monitor zasobów:

* Tworzenie i aktualizowanie zasobów Application Insights.
* Ustaw plan cenowy.
* Pobierz klucz Instrumentacji.
* Dodaj alert dotyczący metryki.
* Dodaj Test dostępności.

Nie można skonfigurować raportu Eksploratora metryk ani skonfigurować eksportu ciągłego.

### <a name="querying-the-telemetry"></a>Wykonywanie zapytania dotyczącego telemetrii

Użyj [interfejsu API REST](https://dev.applicationinsights.io/) , aby [](analytics.md) uruchomić zapytania analityczne.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Jak ustawić alert dla zdarzenia?

Alerty platformy Azure są tylko na metrykach. Utwórz metrykę niestandardową, która przecina próg wartości przy każdym wystąpieniu zdarzenia. Następnie ustaw alert dla metryki. Należy zauważyć, że: otrzymasz powiadomienie za każdym razem, gdy Metryka przekroczy próg w dowolnym kierunku; nie otrzymasz powiadomienia do momentu pierwszego przekroczenia, niezależnie od tego, czy wartość początkowa jest wysoka, czy niska; zawsze trwa opóźnienie wynoszący kilka minut.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Czy są naliczane opłaty za transfer danych między aplikacją internetową platformy Azure i Application Insights?

* Jeśli Twoja aplikacja internetowa platformy Azure jest hostowana w centrum danych, w którym znajduje się punkt końcowy kolekcji Application Insights, nie jest naliczana opłata. 
* Jeśli w centrum danych hosta nie ma punktu końcowego kolekcji, dane telemetryczne Twojej aplikacji będą powodować naliczanie [opłat za wychodzące platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Nie zależy to od lokalizacji zasobów Application Insights. Zależy to tylko od dystrybucji naszych punktów końcowych.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Czy mogę wysłać dane telemetryczne do portalu Application Insights?

Zalecamy używanie naszych zestawów SDK i korzystanie z [interfejsu API zestawu SDK](api-custom-events-metrics.md). Istnieje wiele wariantów zestawu SDK dla różnych [platform](platforms.md). Te zestawy SDK obsługują buforowanie, kompresję, ograniczanie, ponawianie prób i tak dalej. Jednak [schemat](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) pozyskiwania i [Protokół punktu końcowego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) są publiczne.

## <a name="can-i-monitor-an-intranet-web-server"></a>Czy mogę monitorować intranetowy serwer sieci Web?

Tak, ale musisz zezwolić na ruch do naszych usług przez wyjątki zapory lub przekierowania serwera proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- TelemetryChannel`https://dc.services.visualstudio.com:443` 


Zapoznaj się z pełną listą usług i adresów IP w [tym miejscu](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Wyjątek zapory

Zezwól serwerowi sieci Web na wysyłanie danych telemetrycznych do naszych punktów końcowych. 

### <a name="gateway-redirect"></a>Przekierowanie bramy

Kierowanie ruchu z serwera do bramy w intranecie przez zastępowanie punktów końcowych w konfiguracji.
Jeśli te właściwości "punkt końcowy" nie są obecne w konfiguracji, te klasy będą używały domyślnych wartości przedstawionych poniżej w przykładowym pliku ApplicationInsights. config. 

Twoja Brama powinna kierować ruch do adresu podstawowego tego punktu końcowego. W konfiguracji Zastąp wartości `http://<your.gateway.address>/<relative path>`domyślne wartością.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Przykład ApplicationInsights. config z domyślnymi punktami końcowymi:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
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

_Uwaga ApplicationIdProvider jest dostępna od wersji 2.6.0_

### <a name="proxy-passthrough"></a>Przekazywanie serwera proxy

Przekazywanie serwerów proxy można osiągnąć przez skonfigurowanie serwera proxy poziomu komputera lub aplikacji.
Aby uzyskać więcej informacji, zobacz artykuł dotnet w witrynie [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Przykład Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Czy można uruchamiać testy sieci Web dostępności na serwerze intranetowym?

Nasze [testy sieci Web](monitor-web-app-availability.md) działają w punktach obecności rozmieszczonych na całym świecie. Istnieją dwa rozwiązania:

* Drzwi zapory — Zezwalaj na żądania kierowane do serwera od długiej i możliwej do [zmiany listy agentów testów sieci Web](ip-addresses.md).
* Napisz własny kod, aby wysyłać okresowe żądania do serwera z wnętrza intranetu. W tym celu można uruchomić testy sieci Web programu Visual Studio. Tester może wysłać wyniki do Application Insights przy użyciu interfejsu API TrackAvailability ().

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak długo trwa zbieranie danych telemetrycznych?

Większość Application Insights danych ma opóźnienie poniżej 5 minut. Niektóre dane mogą trwać dłużej; zwykle większe pliki dziennika. Aby uzyskać więcej informacji, zobacz [Application INSIGHTS SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Więcej odpowiedzi
* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
