---
title: Monitorowanie działającej aplikacji internetowej platformy ASP.NET za pomocą usługi Application Insights | Microsoft Docs
description: Monitorowanie wydajności witryny sieci Web bez jej ponownego wdrażania. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 8de7ed00114a11b4c765bfa9f9ae42529ef53f7f
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024029"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentowanie aplikacji internetowych w czasie wykonywania za pomocą usługi Application Insights

Możliwe jest instrumentowanie działającej aplikacji internetowej za pomocą usługi Azure Application Insights bez konieczności modyfikowania kodu ani jego ponownego wdrażania. Jeśli Twoje aplikacje są hostowane na lokalnym serwerze IIS, zainstaluj monitor stanu. Jeśli są to aplikacje internetowe platformy Azure lub jeśli działają one na maszynie wirtualnej platformy Azure, możesz włączyć monitorowanie usługi Application Insights z poziomu panelu sterowania platformy Azure. Istnieją także osobne artykuły na temat instrumentacji [działających aplikacji internetowych w technologii J2EE](../../azure-monitor/app/java-live.md) i [usług Azure Cloud Services](../../azure-monitor/app/cloudservices.md). Potrzebna jest subskrypcja platformy [Microsoft Azure](https://azure.com).

![Zrzut ekranu usługi App Insights — Omówienie wykresów zawierających informacje dotyczące żądań zakończonych niepowodzeniem, czas odpowiedzi serwera i żądań serwera](./media/monitor-performance-live-website-now/overview-graphs.png)

Dostępne są trzy trasy zastosowania usługi Application Insights do aplikacji internetowych platformy .NET:

* **Czas kompilacji:** [Dodaj zestaw Application Insights SDK] [ greenbrown] do kodu aplikacji sieci web.
* **Czas wykonywania:** Instrumentacja aplikacji sieci web na serwerze, zgodnie z opisem poniżej, bez ponownego kompilowania lub wdrażania kodu.
* **Zarówno:** Skompiluj zestaw SDK do kodu aplikacji sieci web, a także Zastosuj rozszerzenia czasu wykonywania. Połącz korzyści z obu opcji.

Poniżej przedstawiono podsumowanie tego, co można uzyskać, korzystając z danej trasy:

|  | W czasie kompilacji | W czasie wykonywania |
| --- | --- | --- |
| Żądania i wyjątki |Yes |Yes |
| [Bardziej szczegółowe wyjątki](../../azure-monitor/app/asp-net-exceptions.md) | |Yes |
| [Diagnostyka zależności](../../azure-monitor/app/asp-net-dependencies.md) |Na platformie .NET 4.6 +, ale mniej szczegółów |Tak, kompletne szczegóły: kody wyników, tekst polecenia SQL, czasownik HTTP|
| [Liczniki wydajności sytemu](../../azure-monitor/app/performance-counters.md) |Yes |Yes |
| [Interfejs API dla telemetrii niestandardowej][api] |Yes |Nie |
| [Integracja dziennika śledzenia](../../azure-monitor/app/asp-net-trace-logs.md) |Yes |Nie |
| [Widok strony i dane użytkownika](../../azure-monitor/app/javascript.md) |Yes |Nie |
| Konieczność ponownej kompilacji kodu |Yes | Nie |


## <a name="monitor-a-live-azure-web-app"></a>Monitorowanie działającej aplikacji internetowej platformy Azure

Jeśli aplikacja działa jako usługa sieci Web platformy Azure, monitorowanie należy włączyć w następujący sposób:

* Wybierz usługę Application Insights w panelu sterowania aplikacji na platformie Azure.

    ![Konfigurowanie usługi Application Insights dla aplikacji internetowej platformy Azure](./media/monitor-performance-live-website-now/azure-web-setup.png)
* Gdy zostanie otwarta strona podsumowania usługi Application Insights, kliknij link w dolnej części, aby otworzyć pełny zasób usługi Application Insights.

    ![Klikaj elementy, aż przejdziesz do usługi Application Insights](./media/monitor-performance-live-website-now/azure-web-view-more.png)

[Monitorowanie aplikacji w chmurze i na maszynie wirtualnej](../../application-insights/app-insights-overview.md).

### <a name="enable-client-side-monitoring-in-azure"></a>Włączanie monitorowania po stronie klienta na platformie Azure

Po włączeniu usługi Application Insights na platformie Azure możesz dodać telemetrię widoku strony i użytkownika.

1. Wybierz kolejno pozycje Ustawienia > Ustawienia aplikacji
2.  W obszarze Ustawienia aplikacji dodaj nową parę klucz-wartość: 
   
    Klucz: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Wartość:`true`
3. **Zapisz** ustawienia i **ponownie uruchom** aplikację.

Zestaw SDK języka JavaScript usługi Application Insights został teraz wprowadzony do każdej strony sieci Web.

## <a name="monitor-a-live-iis-web-app"></a>Monitorowanie działającej aplikacji internetowej usług IIS

Jeśli aplikacja jest hostowana na serwerze usług IIS, włącz usługę Application Insights przy użyciu monitora stanu.

1. Zaloguj się na serwerze sieci Web usług IIS, używając poświadczeń administratora.
2. Jeśli monitor stanu usługi Application Insights nie został jeszcze zainstalowany, pobierz i uruchom [Instalator monitora stanu](https://go.microsoft.com/fwlink/?LinkId=506648) (lub uruchom [Instalator platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx) i wyszukaj w nim monitor stanu usługi Application Insights).
3. W monitorze stanu wybierz zainstalowaną aplikację internetową lub witrynę internetową, którą chcesz monitorować. Zaloguj się przy użyciu poświadczeń platformy Azure.

    Skonfiguruj zasób, w którym mają być wyświetlane wyniki w portalu usługi Application Insights. (Zazwyczaj najlepiej jest utworzyć nowy zasób. Wybierz istniejący zasób, jeśli masz już [testy sieci Web][availability] lub [monitorowanie klienta][client] dla tej aplikacji). 

    ![Wybór aplikacji i zasobu.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Uruchom ponownie usługi IIS.

    ![Wybór opcji Uruchom ponownie w górnej części okna dialogowego.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Działanie usługi sieci Web zostanie na krótko przerwane.

## <a name="customize-monitoring-options"></a>Dostosowywanie opcji monitorowania

Włączenie usługi Application Insights powoduje dodanie plików DLL i pliku ApplicationInsights.config do aplikacji internetowej. Możesz [edytować plik config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), aby zmienić niektóre opcje.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Po ponownym opublikowaniu aplikacji ponownie włącz usługę Application Insights

Przed ponownym opublikowaniem aplikacji rozważ [dodanie usługi Application Insights do kodu w programie Visual Studio][greenbrown]. Uzyskasz bardziej szczegółowe dane telemetryczne oraz możliwość zapisywania niestandardowych danych telemetrycznych.

Jeśli chcesz ponownie przeprowadzić publikację bez dodawania usługi Application Insights do kodu, pamiętaj, że proces wdrażania może spowodować usunięcie plików DLL i pliku Application Insights z opublikowanej witryny sieci Web. Zatem:

1. Jeśli edytowano plik ApplicationInsights.config, utwórz jego kopię przed ponownym opublikowaniem aplikacji.
2. Ponownie opublikuj aplikację.
3. Ponownie włącz monitorowanie za pomocą usługi Application Insights. (Użyj odpowiedniej metody: panelu sterowania aplikacji internetowej platformy Azure lub monitora stanu na hoście usług IIS).
4. Przywróć wszystkie zmiany wprowadzone w pliku config.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Rozwiązywanie problemów z konfiguracją środowiska uruchomieniowego usługi Application Insights

### <a name="cant-connect-no-telemetry"></a>Nie można nawiązać połączenia? Brak telemetrii?

* Aby umożliwić działanie monitora stanu, na zaporze serwera otwórz [wymagane porty wychodzące](../../azure-monitor/app/ip-addresses.md#outgoing-ports).

* Otwórz monitor stanu i wybierz swoją aplikację w lewym okienku. Sprawdź, czy w sekcji „Powiadomienia konfiguracyjne” występują komunikaty diagnostyczne dotyczące tej aplikacji:

  ![Otwórz blok Wydajność, aby zobaczyć żądanie, czas odpowiedzi, zależności i inne dane](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Jeśli na serwerze zostanie wyświetlony komunikat o „niewystarczających uprawnieniach”, spróbuj wykonać następujące kroki:
  * W Menedżerze usług IIS wybierz pulę aplikacji, otwórz **Ustawienia zaawansowane** i zapamiętaj tożsamość w obszarze **Model procesu**.
  * W panelu sterowania Zarządzanie komputerem dodaj tę tożsamość do grupy Użytkownicy monitora wydajności.
* Jeśli na serwerze jest zainstalowany agent MMA/SCOM (Systems Center Operations Manager), niektóre wersje mogą powodować konflikt. Odinstaluj oprogramowanie SCOM i monitor stanu, a następnie ponownie zainstaluj najnowsze wersje.
* Stan monitora Dzienniki znajdują się w tej lokalizacji domyślnie: "C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log"
* Zobacz [Rozwiązywanie problemów][qna].

## <a name="system-requirements"></a>Wymagania systemu
Serwerowe systemy operacyjne obsługiwane przez monitor stanu usługi Application Insights:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

z najnowszym dodatkiem SP oraz platformą .NET Framework 4.5

Po stronie klienta: Windows 7, 8, 8.1 i 10, ponownie przy użyciu programu .NET Framework 4.5

Obsługa usług IIS jest: Usługi IIS 7, 7.5, 8, 8.5 (usługi IIS są wymagane)

## <a name="automation-with-powershell"></a>Automatyzacja przy użyciu programu PowerShell
Monitorowanie można uruchomić i zatrzymać przy użyciu programu PowerShell na serwerze usług IIS.

Najpierw zaimportuj moduł Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Dowiedz się, które aplikacje są monitorowane:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` Nazwa aplikacji internetowej (opcjonalnie).
* Wyświetla stan monitorowania usługi Application Insights dla każdej (lub wskazanej z nazwy) aplikacji internetowej na tym serwerze usług IIS.
* Zwraca obiekt `ApplicationInsightsApplication` dla każdej aplikacji:

  * `SdkState==EnabledAfterDeployment`: Aplikacja jest monitorowana, a Instrumentacja została przeprowadzona w czasie wykonywania za pomocą narzędzia Monitor stanu lub przez `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Nie ma instrumentacji aplikacji dla usługi Application Insights. Instrumentacja nie została nigdy przeprowadzona albo monitorowanie czasu wykonywania zostało wyłączone za pomocą monitora stanu lub polecenia `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: Wykonano Instrumentację aplikacji przez dodanie zestawu SDK do kodu źródłowego. Nie można zaktualizować ani zatrzymać tego zestawu SDK.
  * Zestaw `SdkVersion` wyświetla wersję używaną do monitorowania tej aplikacji.
  * Zestaw `LatestAvailableSdkVersion` wyświetla wersję aktualnie dostępną w galerii NuGet. Aby uaktualnić aplikację do tej wersji, użyj polecenia `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Nazwa aplikacji w usługach IIS
* `-InstrumentationKey` Klucz instrumentacji zasobu usługi Application Insights, w którym mają być wyświetlane wyniki.
* To polecenie cmdlet dotyczy tylko aplikacji, dla których nie przeprowadzono jeszcze instrumentacji (czyli SdkState == NotInstrumented).

    To polecenie cmdlet nie ma wpływu na aplikację, dla której przeprowadzono już instrumentację. Nie ma znaczenia, czy instrumentacji aplikacji dokonano w czasie kompilacji przez dodanie zestawu SDK do kodu, czy w czasie wykonywania przez wcześniejsze użycie tego polecenia cmdlet.

    Wersja zestawu SDK użyta do instrumentacji aplikacji to wersja, która została ostatnio pobrana na ten serwer.

    Aby pobrać najnowszą wersję, użyj polecenia cmdlet Update-ApplicationInsightsVersion.
* Zwraca obiekt `ApplicationInsightsApplication` w przypadku powodzenia. W razie niepowodzenia rejestruje ślad do stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Nazwa aplikacji w usługach IIS
* `-All` Zatrzymuje monitorowanie wszystkich aplikacji na tym serwerze usług IIS, dla których `SdkState==EnabledAfterDeployment`
* Zatrzymuje monitorowanie określonych aplikacji i usuwa instrumentację. Działa to tylko w przypadku aplikacji, które zostały zinstrumentowane w czasie wykonywania za pomocą monitora stanu lub polecenia cmdlet Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Zwraca obiekt ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Nazwa aplikacji sieci web w usługach IIS.
* `-InstrumentationKey` (opcjonalnie). umożliwia zmianę zasobu, do którego wysyłana jest telemetria aplikacji.
* To polecenie cmdlet:
  * Uaktualnia wskazaną aplikację do ostatniej wersji zestawu SDK pobranej na ten komputer. (Działa tylko wtedy, gdy `SdkState==EnabledAfterDeployment`)
  * Jeśli zostanie wprowadzony klucz instrumentacji, wskazana aplikacja jest konfigurowana ponownie do wysłania telemetrii do zasobu dotyczącego tego klucza. (Działa, jeśli `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Pobiera na serwer najnowszy zestaw SDK usługi Application Insights.

## <a name="questions"></a>Pytania dotyczące monitora stanu

### <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, która jest instalowana na serwerze sieci Web usług IIS. Ułatwia ona instrumentację i konfigurowanie aplikacji internetowych. 

### <a name="when-do-i-use-status-monitor"></a>Kiedy należy używać monitora stanu?

* Do instrumentacji dowolnej aplikacji internetowej uruchamianej na serwerze usług IIS — nawet jeśli już działa.
* Aby włączyć dodatkową telemetrię dla aplikacji internetowych, które zostały [skompilowane przy użyciu zestawu SDK usługi Application Insights](../../azure-monitor/app/asp-net.md) w czasie kompilacji. 

### <a name="can-i-close-it-after-it-runs"></a>Czy można zamknąć go po uruchomieniu?

Tak. Możesz zamknąć go po zakończeniu instrumentacji wybranych witryn sieci Web.

Nie zbiera on telemetrii samodzielnie. Po prostu konfiguruje aplikacje internetowe i ustawia niektóre uprawnienia.

### <a name="what-does-status-monitor-do"></a>Co robi monitor stanu?

Po wybraniu aplikacji internetowej do instrumentacji za pomocą monitora stanu:

* Pobiera i umieszcza zestawy usługi Application Insights i pliku ApplicationInsights.config w folderze plików binarnych aplikacji sieci web.
* Umożliwia profilowanie aparatu CLR w celu gromadzenia wywołań zależności.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Jakie wersje zestawu SDK usługi Application Insights Zainstaluj Monitor stanu

Aktualnie Monitora stanu można zainstalować tylko zestaw SDK usługi Application Insights w wersji 2.3 lub 2.4.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Czy monitor stanu należy uruchamiać podczas każdej aktualizacji aplikacji?

Nie, jeśli ponowne wdrażanie odbywa się przyrostowo. 

Jeśli w procesie publikowania wybierzesz opcję usuwania istniejących plików, trzeba będzie ponownie uruchomić monitor stanu w celu skonfigurowania usługi Application Insights.

### <a name="what-telemetry-is-collected"></a>Jakie dane telemetrii są zbierane?

W przypadku aplikacji z instrumentacją tylko w czasie wykonywania za pomocą monitora stanu:

* Żądania HTTP
* Wywołania do zależności
* Wyjątki
* Liczniki wydajności

W przypadku aplikacji już instrumentowanych w czasie kompilacji:

 * Liczniki procesu.
 * Wywołania zależności (.NET 4.5); wartości zwracane w wywołaniach zależności (.NET 4.6).
 * Wartości śladu stosu wyjątków.

[Dowiedz się więcej](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next"></a>Następne kroki

Wyświetlanie telemetrii:

* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
* [Wyszukiwanie zdarzeń i dzienników][diagnostic] w celu diagnozowania problemów
* [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań
* [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/app-insights-dashboards.md)

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci Web][availability], aby upewnić się, że witryna pozostaje aktywna.
* [Dodawanie telemetrii klienta sieci Web][usage], aby zobaczyć wyjątki pochodzące z kodu strony sieci Web i umożliwić wstawianie wywołań śladu.
* [Dodawanie zestawu SDK usługi Application Insights do kodu][greenbrown], aby móc wstawić ślad i rejestrować wywołania.

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../application-insights/app-insights-troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
