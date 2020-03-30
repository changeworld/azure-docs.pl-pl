---
title: Rozwiązywanie problemów z brakiem danych — usługa Application Insights dla platformy .NET
description: Nie widzisz danych w usłudze Azure Application Insights? Spróbuj tutaj.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 2627fde55f4177798d04aab02db169f3117d32dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665905"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Rozwiązywanie problemów z braku danych — usługa Application Insights for .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Brakuje niektórych moich danych telemetrycznych
*W usłudze Application Insights widzę tylko ułamek zdarzeń generowanych przez moją aplikację.*

* Jeśli konsekwentnie widzisz tę samą frakcję, prawdopodobnie jest to spowodowane adaptacyjnym [pobieraniem próbek.](../../azure-monitor/app/sampling.md) Aby to potwierdzić, otwórz wyszukiwanie (z bloku przeglądu) i spójrz na wystąpienie żądania lub innego zdarzenia. W dolnej części sekcji właściwości kliknij "..." , aby uzyskać pełne informacje o nieruchomości. Jeśli liczba żądań > 1, pobieranie próbek jest w trybie pracy.
* W przeciwnym razie możliwe jest, że wytrzymasz [limit szybkości transmisji danych](../../azure-monitor/app/pricing.md#limits-summary) dla planu cenowego. Limity te są stosowane na minutę.

*Doświadczam utraty danych losowo.*

* Sprawdź, czy w kanale [telemetrii](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) występuje utrata danych

* Sprawdź, czy nie ma znanych problemów w [repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) kanału telemetrii

*Doświadczam utraty danych w aplikacji konsoli lub w aplikacji Sieci Web, gdy aplikacja ma się zatrzymać.*

* Kanał SDK przechowuje dane telemetryczne w buforze i wysyła je w partiach. Jeśli aplikacja jest zamykana, może być konieczne jawne wywołanie [Flush()](api-custom-events-metrics.md#flushing-data). Zachowanie `Flush()` zależy od rzeczywistego [używanego kanału.](telemetry-channels.md#built-in-telemetry-channels)

## <a name="no-data-from-my-server"></a>Brak danych z mojego serwera
*Zainstalowałem aplikację na moim serwerze internetowym, a teraz nie widzę z niej żadnych danych telemetrycznych. Ono pracował W PORZĄDKU u mój dev maszyna.*

* Prawdopodobnie problem z zaporą. [Ustaw wyjątki zapory dla aplikacji Insights do wysyłania danych](../../azure-monitor/app/ip-addresses.md).
* Na serwerze usług IIS może brakować pewnych wymagań wstępnych: .NET Extensibility 4.5 i ASP.NET 4.5.

*[Monitor stanu został zainstalowany](../../azure-monitor/app/monitor-performance-live-website-now.md) na serwerze internetowym w celu monitorowania istniejących aplikacji. Nie widzę żadnych wyników.*

* Zobacz [Monitor stanu rozwiązywania problemów](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Brak opcji "Dodaj aplikację Insights" w programie Visual Studio
*Po kliknięciu prawym przyciskiem myszy istniejącego projektu w Eksploratorze rozwiązań nie widzę żadnych opcji usługi Application Insights.*

* Nie wszystkie typy projektu platformy .NET są obsługiwane przez narzędzia. Obsługiwane są projekty sieci Web i WCF. W przypadku innych typów projektów, takich jak aplikacje klasyczne lub usługi, nadal można [ręcznie dodać sdk usługi Application Insights do projektu.](../../azure-monitor/app/windows-desktop.md)
* Upewnij się, że masz [visual studio 2013 Update 3 lub nowsze](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Jest fabrycznie zainstalowany z narzędziami analizy deweloperów, które zapewniają zestaw SDK usługi Application Insights.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **narzędzia do analizy dla deweloperów** są zainstalowane i włączone. Jeśli tak, kliknij pozycję **Aktualizacje,** aby sprawdzić, czy jest dostępna aktualizacja.
* Otwórz okno dialogowe Nowy projekt i wybierz ASP.NET aplikacji sieci Web. Jeśli zostanie tam widoczna opcja Usługi Application Insights, narzędzia zostaną zainstalowane. Jeśli nie, spróbuj odinstalować, a następnie ponownie zainstalować narzędzia do analizy dla deweloperów.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Dodawanie usługi Application Insights nie powiodło się
*Podczas próby dodania usługi Application Insights do istniejącego projektu pojawia się komunikat o błędzie.*

Prawdopodobne przyczyny:

* Komunikacja z portalem usługi Application Insights nie powiodła się; Lub
* Wystąpił problem z kontem platformy Azure;
* Masz tylko [dostęp do odczytu do subskrypcji lub grupy, w której próbowano utworzyć nowy zasób](../../azure-monitor/app/resources-roles-access-control.md).

Naprawić:

* Sprawdź, czy podano poświadczenia logowania dla odpowiedniego konta platformy Azure.
* W przeglądarce sprawdź, czy masz dostęp do [witryny Azure portal](https://portal.azure.com). Otwórz ustawienia i sprawdź, czy nie ma żadnych ograniczeń.
* [Dodaj usługa Application Insights do istniejącego projektu:](../../azure-monitor/app/asp-net.md)w Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie "Dodaj wgląd w aplikacje".

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Pojawia się komunikat o błędzie "Klucz instrumentacji nie może być pusty"
Wygląda na to, że coś poszło nie tak podczas instalowania usługi Application Insights lub może karty rejestrowania.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Application Insights > Configure Application Insights**. Pojawi się okno dialogowe, które zachęca do logowania się na platformie Azure i utworzenia zasobu usługi Application Insights lub ponownego użycia istniejącego.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a>"NuGet pakiet (s) brakuje" na moim serwerze kompilacji
*Wszystko tworzy OK, gdy jestem debugowania na moim komputerze deweloperskim, ale pojawia się błąd NuGet na serwerze kompilacji.*

Zobacz [NuGet Package Restore](https://docs.nuget.org/Consume/Package-Restore) i [Automatyczne przywracanie pakietu](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Brak polecenia menu do otwierania aplikacji Usługi Insights z programu Visual Studio
*Gdy klikam prawym przyciskiem myszy Eksploratora rozwiązań projektu, nie widzę żadnych poleceń usługi Application Insights lub nie widzę polecenia Otwórz usługa Application Insights.*

Prawdopodobne przyczyny:

* Jeśli zasób usługi Application Insights został utworzony ręcznie lub jeśli projekt jest typu, który nie jest obsługiwany przez narzędzia usługi Application Insights.
* Narzędzia analizy deweloperów są wyłączone w programie Visual Studio.
* Program Visual Studio jest starszy niż aktualizacja 3 z 2013 r.

Naprawić:

* Upewnij się, że wersja programu Visual Studio jest 2013 aktualizacja 3 lub nowsza.
* Wybierz **narzędzia**, **rozszerzenia i aktualizacje** i sprawdź, czy **narzędzia do analizy deweloperów** są zainstalowane i włączone. Jeśli tak, kliknij pozycję **Aktualizacje,** aby sprawdzić, czy jest dostępna aktualizacja.
* Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań. Jeśli zostanie wyświetlene polecenie **Application Insights > Configure Application Insights**, użyj go do połączenia projektu z zasobem w usłudze Usługi Application Insights.

W przeciwnym razie typ projektu nie jest bezpośrednio obsługiwany przez narzędzia analizy deweloperów. Aby wyświetlić dane telemetryczne, zaloguj się do [portalu Azure](https://portal.azure.com), wybierz pozycję Usługa Application Insights na lewym pasku nawigacyjnym i wybierz aplikację.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Odmowa dostępu" przy otwieraniu aplikacji insights z programu Visual Studio
*Polecenie menu "Otwórz usługę Application Insights" przenosi mnie do witryny Azure portal, ale pojawia się błąd "odmowa dostępu".*

Logowanie firmy Microsoft ostatnio używane w przeglądarce domyślnej nie ma dostępu do [zasobu, który został utworzony podczas dodawania usługi Application Insights do tej aplikacji.](../../azure-monitor/app/asp-net.md) Istnieją dwa prawdopodobne powody:

* Masz więcej niż jedno konto Microsoft - może praca i osobiste konto Microsoft? Logowanie ostatnio używane w domyślnej przeglądarce dotyczyło innego konta niż konto, które ma dostęp do [dodatku Usługi Application Insights do projektu.](../../azure-monitor/app/asp-net.md)
  * Poprawka: kliknij swoje imię i nazwisko w prawym górnym rogu okna przeglądarki i wyloguj się. Następnie zaloguj się przy tym za pomocą konta, do które ma dostęp. Następnie na lewym pasku nawigacyjnym kliknij pozycję Usługa Application Insights i wybierz aplikację.
* Ktoś inny dodał usługi Application Insights do projektu i zapomniał udzielić [dostępu do grupy zasobów,](../../azure-monitor/app/resources-roles-access-control.md) w której został utworzony.
  * Poprawka: Jeśli używali konta organizacyjnego, mogą dodać Cię do zespołu; lub mogą udzielić ci indywidualnego dostępu do grupy zasobów.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Nie znaleziono zasobu" przy otwieraniu aplikacji w programie Visual Studio
*Polecenie menu "Otwórz usługę Aplikacji" przenosi mnie do witryny Azure portal, ale otrzymuję błąd "nie znaleziono zasobu".*

Prawdopodobne przyczyny:

* Usługa Application Insights dla aplikacji została usunięta; Lub
* Klucz instrumentacji został ustawiony lub zmieniony w AplikacjiInsights.config, edytując go bezpośrednio, bez aktualizowania pliku projektu.

Klucz instrumentacji w aplikacjiInsights.config kontroluje, gdzie jest wysyłana dane telemetryczne. Wiersz w pliku projektu określa, który zasób jest otwierany podczas korzystania z polecenia w programie Visual Studio.

Naprawić:

* W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie Usługa Application Insights, Configure Application Insights .In Solution Explorer, right-click the project and choose Application Insights, Configure Application Insights. W oknie dialogowym można wysłać dane telemetryczne do istniejącego zasobu lub utworzyć nowy. Lub:
* Otwórz zasób bezpośrednio. Zaloguj się do [witryny Azure portal](https://portal.azure.com), kliknij pozycję Usługa Application Insights na lewym pasku nawigacyjnym, a następnie wybierz aplikację.

## <a name="where-do-i-find-my-telemetry"></a>Gdzie znajdę dane telemetryczne?
*Zalogowałem się do [portalu Microsoft Azure](https://portal.azure.com)i przeglądam domowy pulpit nawigacyjny platformy Azure. Gdzie więc znaleźć moje dane usługi Application Insights?*

* Na lewym pasku nawigacyjnym kliknij pozycję Usługa Application Insights, a następnie nazwę aplikacji. Jeśli nie masz tam żadnych projektów, musisz [dodać lub skonfigurować aplikację Application Insights w projekcie internetowym.](../../azure-monitor/app/asp-net.md)  
  Zobaczysz tam kilka wykresów podsumowujących. Możesz je kliknąć, aby zobaczyć więcej szczegółów.
* W programie Visual Studio podczas debugowania aplikacji kliknij przycisk Usługi Application Insights.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>Brak danych serwera (lub brak danych)
*Uruchomiłem aplikację, a następnie otworzyłem usługę Application Insights na platformie Microsoft Azure, ale wszystkie wykresy pokazują "Dowiedz się, jak zbierać...". lub "Nie skonfigurowano".* Lub *tylko widok strony i dane użytkownika, ale bez danych serwera.*

* Uruchom aplikację w trybie debugowania w programie Visual Studio (F5). Użyj aplikacji, aby wygenerować niektóre dane telemetryczne. Sprawdź, czy można zobaczyć zdarzenia zarejestrowane w oknie danych wyjściowych programu Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* W portalu usługi Application Insights otwórz obszar [Wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md). Dane zwykle pojawiają się tutaj jako pierwsze.
* Kliknij przycisk Odśwież. Ostrze odświeża się okresowo, ale można to zrobić również ręcznie. Interwał odświeżania jest dłuższy dla większych zakresów czasu.
* Sprawdź, czy klawisze oprzyrządowania są zgodne. Na głównym bloku aplikacji w portalu Application Insights w menu rozwijany **Essentials** zajrzyj na **klucz instrumentacji**. Następnie w projekcie w programie Visual Studio otwórz plik ApplicationInsights.config i znajdź plik `<instrumentationkey>`. Sprawdź, czy dwa klawisze są równe. Jeśli nie:  
  * W portalu kliknij pozycję Usługa Application Insights i poszukaj zasobu aplikacji przy pomocy odpowiedniego klucza; Lub
  * W Eksploratorze rozwiązań programu Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie Usługa Application Insights, Configure. Zresetuj aplikację, aby wysłać dane telemetryczne do odpowiedniego zasobu.
  * Jeśli nie można znaleźć pasujących kluczy, sprawdź, czy używasz tych samych poświadczeń logowania w programie Visual Studio, jak w portalu.
* Na [pulpicie nawigacyjnym platformy Microsoft Azure](https://portal.azure.com)można sprawdzić na mapie Kondycja usługi. Jeśli istnieją pewne wskazania alertów, poczekaj, aż powrócą do OK, a następnie zamknij i ponownie otwórz blok aplikacji usługi Application Insights.
* Sprawdź również [nasz blog status](https://blogs.msdn.microsoft.com/servicemap-status/).
* Czy napisałeś kod [sdk po stronie serwera,](../../azure-monitor/app/api-custom-events-metrics.md) który może `TelemetryClient` zmienić `TelemetryContext`klucz instrumentacji w instancjach lub w? A może napisałeś [filtr lub konfigurację próbkowania,](../../azure-monitor/app/api-filtering-sampling.md) która może być zbyt nio odfiltrowywania?
* Jeśli edytowano Plik ApplicationInsights.config, dokładnie sprawdź konfigurację [telemetryinilizatorów i telemetriiProcessorów](../../azure-monitor/app/api-filtering-sampling.md). Niepoprawnie nazwany typ lub parametr może spowodować, że zestaw SDK nie będzie wysyłał żadnych danych.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Brak danych na temat wyświetleń strony, przeglądarek, użycia
*Widzę dane na wykresach Czas odpowiedzi serwera i Żądania serwera, ale nie ma danych w czasie ładowania widoku strony lub w blokach Przeglądarka lub Użycie.*

Dane pochodzą ze skryptów na stronach internetowych. 

* Jeśli usługa Application Insights została dodana do istniejącego projektu sieci Web, [należy ręcznie dodać skrypty.](../../azure-monitor/app/javascript.md)
* Upewnij się, że program Internet Explorer nie wyświetla witryny w trybie zgodności.
* Użyj funkcji debugowania przeglądarki (F12 w niektórych przeglądarkach, a następnie wybierz `dc.services.visualstudio.com`pozycję Sieć), aby sprawdzić, czy dane są wysyłane do .

## <a name="no-dependency-or-exception-data"></a>Brak zależności lub danych wyjątków
Zobacz [dane telemetryczne zależności](../../azure-monitor/app/asp-net-dependencies.md) i dane [telemetryczne wyjątków](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Brak danych dotyczących wydajności
Dane dotyczące wydajności (procesora CPU, szybkość we/wy itd.) są dostępne dla [usług internetowych Java,](../../azure-monitor/app/java-collectd.md) [aplikacji klasycznych systemu Windows,](../../azure-monitor/app/windows-desktop.md) [aplikacji i usług internetowych usług IIS po zainstalowaniu monitora stanu](../../azure-monitor/app/monitor-performance-live-website-now.md)i usług Azure Cloud [Services](../../azure-monitor/app/app-insights-overview.md). znajdziesz go w ustawieniach, serwerach.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Brak danych (serwera) od czasu opublikowania aplikacji na moim serwerze
* Sprawdź, czy rzeczywiście skopiowano wszystkie firmy Microsoft. Biblioteki DLL aplikacji są wysuwane na serwer wraz z plikami Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* W zaporze może być trzeba [otworzyć niektóre porty TCP](../../azure-monitor/app/ip-addresses.md).
* Jeśli musisz użyć serwera proxy do wysyłania z sieci firmowej, ustaw [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) w Web.config
* Windows Server 2008: Upewnij się, że zainstalowano następujące aktualizacje: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Kiedyś widziałem dane, ale przestał
* Sprawdź [blog o stanie](https://blogs.msdn.com/b/applicationinsights-status/).
* Czy osiągnąłeś miesięczny limit punktów danych? Otwórz ustawienia/przydział i ceny, aby się dowiedzieć. Jeśli tak, możesz uaktualnić plan lub zapłacić za dodatkową pojemność. Zobacz [schemat cenowy](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nie widzę wszystkich danych, których się spodziewam
Jeśli aplikacja wysyła dużo danych i używasz SDK usługi Application Insights dla ASP.NET wersji 2.0.0-beta3 lub nowszej, [adaptacyjna funkcja próbkowania](../../azure-monitor/app/sampling.md) może działać i wysyłać tylko procent danych telemetrycznych.

Można go wyłączyć, ale nie jest to zalecane. Próbkowanie jest zaprojektowane tak, aby powiązane dane telemetryczne były prawidłowo przesyłane do celów diagnostycznych.

## <a name="client-ip-address-is-0000"></a>Adres IP klienta to 0.0.0.0

W dniu 5 lutego 2018 r. ogłosiliśmy, że usunęliśmy rejestrowanie adresu IP klienta. Nie ma to wpływu na lokalizację geograficzną.

> [!NOTE]
> Jeśli potrzebujesz pierwszych 3 oktetów adresu IP, możesz użyć [inicjatora telemetrii,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) aby dodać atrybut niestandardowy.
> Nie ma to wpływu na dane zebrane przed 5 lutego 2018 r.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Nieprawidłowe dane geograficzne w danych telemetrycznych użytkownika
Wymiary miasta, regionu i kraju pochodzą z adresów IP i nie zawsze są dokładne. Te adresy IP są najpierw przetwarzane dla lokalizacji, a następnie zmieniane na 0.0.0.0 do przechowywania.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek „nie można odnaleźć metody” podczas uruchamiania w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Wersja 4.6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj wersję 4.6 w każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="troubleshooting-logs"></a>Dzienniki rozwiązywania problemów

Postępuj zgodnie z tymi instrukcjami, aby przechwycić dzienniki rozwiązywania problemów dla twojej struktury.

### <a name="net-framework"></a>.NET Framework

1. Zainstaluj pakiet [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z nuget. Instalowana wersja musi być zgodna z bieżącą zainstalowaną wersją`Microsoft.ApplicationInsighs`

2. Zmodyfikuj plik applicationinsights.config, aby uwzględnić następujące elementy:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Aplikacja musi mieć uprawnienia do zapisu w skonfigurowanym miejscu

3. Uruchom ponownie proces, aby te nowe ustawienia były pobierane przez SDK

4. Przywróć te zmiany po zakończeniu.

### <a name="net-core"></a>.NET Core

1. Zainstaluj pakiet [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) z nuget. Instalowana wersja musi być zgodna z bieżącą zainstalowaną wersją`Microsoft.ApplicationInsights`

Najnowsza wersja programu Microsoft.ApplicationInsights.AspNetCore jest 2.8.2 i odnosi się do microsoft.ApplicationInsights w wersji 2.11.2. Stąd wersja Microsoft.AspNet.ApplicationInsights.HostingStartup do zainstalowania powinna wynosić 2.11.2

2. `ConfigureServices` Modyfikuj `Startup.cs` metodę w swojej klasie.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Aplikacja musi mieć uprawnienia do zapisu w skonfigurowanym miejscu

3. Uruchom ponownie proces, aby te nowe ustawienia były pobierane przez SDK

4. Przywróć te zmiany po zakończeniu.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Zbieranie dzienników za pomocą PerfView
[PerfView](https://github.com/Microsoft/perfview) to bezpłatne narzędzie do diagnostyki i analizy wydajności, które pomaga wyizolować procesor, pamięć i inne problemy, zbierając i wizualizując informacje diagnostyczne z wielu źródeł.

Dziennik SDK usługi Application Insights EventSource samorozwiązujący się z usługą, które mogą zostać przechwycone przez Program PerfView.

Aby zebrać dzienniki, pobierz PerfView i uruchom to polecenie:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

W razie potrzeby można zmodyfikować następujące parametry:
- **MaxCollectSec**. Ustaw ten parametr, aby zapobiec programowi PerfView działającemu przez czas nieokreślony i wpływającemu na wydajność serwera.
- **TylkoZapewniacze**. Ustaw ten parametr tylko zbierać dzienniki z zestawu SDK. Tę listę można dostosować na podstawie konkretnych badań. 
- **NoGui**. Ustaw ten parametr tak, aby zbierał dzienniki bez Gui.


Aby uzyskać więcej informacji,
- [Rejestrowanie śladów wydajności za pomocą programu PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Źródła zdarzeń usługi Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Nadal nie działa...
* [Forum usługi Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
