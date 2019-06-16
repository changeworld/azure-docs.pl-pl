---
title: Monitorowanie działającej aplikacji internetowej platformy ASP.NET za pomocą usługi Application Insights | Microsoft Docs
description: Monitorowanie wydajności witryny sieci Web bez jej ponownego wdrażania. Współpracuje z ASP.NET sieci web aplikacji hostowanych lokalnie lub na maszynach wirtualnych.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mbullwin
ms.openlocfilehash: 6ad2ab00060528557f618eb684ccfa710c3f09b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074175"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Instrumentacja aplikacji sieci web w czasie wykonywania za pomocą Monitora stanu usługi Application Insights

Możliwe jest instrumentowanie działającej aplikacji internetowej za pomocą usługi Azure Application Insights bez konieczności modyfikowania kodu ani jego ponownego wdrażania. Potrzebna jest subskrypcja platformy [Microsoft Azure](https://azure.com).

Monitor stanu jest używany do instrumentacji aplikacji .NET hostowanych w usługach IIS lokalnie lub na maszynie wirtualnej.

- Jeśli Twoja aplikacja jest wdrożona w usłudze Azure app services, postępuj zgodnie z [w instrukcjach](azure-web-apps.md).
- Jeśli Twoja aplikacja jest wdrożona w Maszynie wirtualnej platformy Azure, możesz przełączyć się na monitorowanie usługi Application Insights z poziomu Panelu sterowania platformy Azure.
- (Istnieją także osobne artykuły na temat Instrumentacji [usług Azure Cloud Services](../../azure-monitor/app/cloudservices.md).)


![Zrzut ekranu usługi App Insights — Omówienie wykresów zawierających informacje dotyczące żądań zakończonych niepowodzeniem, czas odpowiedzi serwera i żądań serwera](./media/monitor-performance-live-website-now/overview-graphs.png)

Dostępne są dwie trasy zastosowania usługi Application Insights do aplikacji sieci web platformy .NET:

* **Czas kompilacji:** [Dodaj zestaw Application Insights SDK] [ greenbrown] do kodu aplikacji sieci web.
* **Czas wykonywania:** Instrumentacja aplikacji sieci web na serwerze, zgodnie z opisem poniżej, bez ponownego kompilowania lub wdrażania kodu.

> [!NOTE]
> Jeśli używasz Instrumentacja w czasie kompilacji, należy uruchomić razem, gdy Instrumentacji nie będzie działać nawet wtedy, gdy jest włączona.

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



## <a name="monitor-a-live-iis-web-app"></a>Monitorowanie działającej aplikacji internetowej usług IIS

Jeśli aplikacja jest hostowana na serwerze usług IIS, włącz usługę Application Insights przy użyciu monitora stanu.

1. Zaloguj się na serwerze sieci Web usług IIS, używając poświadczeń administratora.
2. Jeśli Monitor stanu usługi Application Insights nie jest już zainstalowany, [Pobierz i uruchom Instalatora](#download)
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


## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="confirm-a-valid-installation"></a>Upewnij się, prawidłowej instalacji 

Oto kilka kroków, które należy wykonać, aby potwierdzić, że Twoja instalacja zakończyła się pomyślnie.

- Upewnij się, że plik applicationInsights.config znajduje się w katalogu aplikacji docelowej i zawiera ikey.

- Jeśli podejrzewasz, że brakuje danych można uruchomić proste zapytanie [Analytics](../log-query/get-started-portal.md) Aby wyświetlić listę wszystkich ról chmura obecnie wysyłania danych telemetrycznych.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Jeśli potrzebujesz upewnić się, że usługa Application Insights jest pomyślnie dołączono można uruchomić [uchwyt Sysinternals](https://docs.microsoft.com/sysinternals/downloads/handle) za pomocą polecenia okna, aby upewnić się, że applicationinsights.dll został załadowany przez usługi IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nie można nawiązać połączenia? Brak telemetrii?

* Aby umożliwić działanie monitora stanu, na zaporze serwera otwórz [wymagane porty wychodzące](../../azure-monitor/app/ip-addresses.md#outgoing-ports).

### <a name="unable-to-login"></a>Nie można się zalogować

* Jeśli Monitor stanu nie mogą się zalogować, należy instalować wiersza polecenia zamiast tego. Monitor stanu próbuje zalogować się do zbierania z klucza ikey, ale można zapewnić to ręcznie przy użyciu polecenia:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nie można załadować pliku lub zestawu "System.Diagnostics.DiagnosticSource"

Ten błąd może wystąpić po włączeniu usługi Application Insights. Jest to spowodowane Instalator zastępuje tę bibliotekę dll w katalogu bin.
Aby naprawić aktualizacji z pliku web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Śledzimy ten problem [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Komunikaty diagnostyczne dla aplikacji

* Otwórz monitor stanu i wybierz swoją aplikację w lewym okienku. Sprawdź, czy w sekcji „Powiadomienia konfiguracyjne” występują komunikaty diagnostyczne dotyczące tej aplikacji:

  ![Otwórz blok Wydajność, aby zobaczyć żądanie, czas odpowiedzi, zależności i inne dane](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Szczegółowe dzienniki

* Domyślnie Monitor stanu zwróci dzienniki diagnostyczne na: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* W danych wyjściowych pełne dzienniki, zmodyfikuj plik konfiguracji: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` i Dodaj `<add key="TraceLevel" value="All" />` do `appsettings`.
Następnie należy ponownie uruchomić monitor stanu.

* Monitor stanu jest aplikacji .NET można również włączyć [śledzenia .net, dodając odpowiednie diagnostyki do pliku config](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). Na przykład w niektórych scenariuszach może być przydatne zobaczyć, co dzieje się na poziomie sieci przez [Konfigurowanie śledzenia sieci](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Niewystarczające uprawnienia
  
* Jeśli na serwerze zostanie wyświetlony komunikat o „niewystarczających uprawnieniach”, spróbuj wykonać następujące kroki:
  * W Menedżerze usług IIS wybierz pulę aplikacji, otwórz **Ustawienia zaawansowane** i zapamiętaj tożsamość w obszarze **Model procesu**.
  * W panelu sterowania Zarządzanie komputerem dodaj tę tożsamość do grupy Użytkownicy monitora wydajności.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt z programem Systems Center Operations Manager

* Jeśli na serwerze jest zainstalowany agent MMA/SCOM (Systems Center Operations Manager), niektóre wersje mogą powodować konflikt. Odinstaluj oprogramowanie SCOM i monitor stanu, a następnie ponownie zainstaluj najnowsze wersje.

### <a name="failed-or-incomplete-installation"></a>Nie powiodło się lub niekompletnej instalacji

Jeśli Monitor stanu zakończy się niepowodzeniem podczas instalacji, może się pozostać instalację niekompletne, który Monitor stanu nie jest w stanie odzyskać z. Będzie to wymagało resetowania ręcznego.

Usuń wszystkie te pliki w katalogu aplikacji:
- Każdej biblioteki dll w katalogu bin, począwszy od albo "Microsoft.AI". lub "Microsoft.ApplicationInsights.".
- Ta biblioteka DLL w katalogu bin "Microsoft.Web.Infrastructure.dll"
- Ta biblioteka DLL w katalogu bin "System.Diagnostics.DiagnosticSource.dll"
- W katalogu aplikacji Usuń "App_Data\packages"
- W katalogu aplikacji Usuń "w pliku applicationinsights.config"


### <a name="additional-troubleshooting"></a>Dodatkowe procedury rozwiązywania problemów

* Zobacz dodatkowe [Rozwiązywanie problemów z][qna].

## <a name="system-requirements"></a>Wymagania systemowe
Serwerowe systemy operacyjne obsługiwane przez monitor stanu usługi Application Insights:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

z najnowszym dodatkiem SP oraz .NET Framework 4.5 (Monitora stanu jest wbudowany w ramach tej wersji)

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

Application Insights SDK wersji 2.4 jest [ostatniej wersji do działu pomocy technicznej programu .NET 4.0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) co było [EOL stycznia 2016 roku](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Dlatego teraz Monitor stanu może służyć do instrumentacji aplikacji .NET 4.0. 

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

## <a name="download"></a>Pobierz Monitor stanu

- Pobierz i uruchom [Instalator Monitora stanu](https://go.microsoft.com/fwlink/?LinkId=506648)
- Lub uruchom [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx) i wyszukaj w nim Monitor stanu usługi Application Insights.

## <a name="next"></a>Następne kroki

Wyświetlanie telemetrii:

* [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
* [Wyszukiwanie zdarzeń i dzienników][diagnostic] w celu diagnozowania problemów
* [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań

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
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
