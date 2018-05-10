---
title: Usługa Azure Application Insights migawki debuger dla aplikacji .NET | Dokumentacja firmy Microsoft
description: Debugowanie migawki są zbierane automatycznie, gdy wyjątki zostaną zgłoszone w aplikacjach .NET produkcji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mbullwin; pharring
ms.openlocfilehash: 0721fa42a8d770b82a4b18865b513569bcc8807f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugowanie migawek na wyjątków w aplikacji .NET

Po wystąpieniu wyjątku, można automatycznie zbierać migawki debugowania aplikacji sieci web na żywo. Migawki przedstawia stan kodu źródłowego i zmiennych w momencie utworzenia zgłoszono wyjątek. Debuger migawki (wersja zapoznawcza) w [Azure Application Insights](app-insights-overview.md) monitoruje dane telemetryczne wyjątku z aplikacji sieci web. Zbiera migawki na listy wyjątków zgłaszanie top, dzięki czemu masz informacje potrzebne do diagnozowania problemów w środowisku produkcyjnym. Obejmują [pakietu NuGet modułu zbierającego migawki](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w aplikacji i opcjonalnie skonfigurować parametry kolekcji w [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Migawki są wyświetlane na [wyjątki](app-insights-asp-net-exceptions.md) w portalu usługi Application Insights.

Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Aby uzyskać bardziej wydajne działanie debugowania z kodem źródłowym, otwórz migawki z programu Visual Studio Enterprise 2017 przez [pobieranie rozszerzenia migawki debugera programu Visual Studio](https://aka.ms/snapshotdebugger). W programie Visual Studio, możesz również [ustawić Snappoints do interaktywnego migawek](https://aka.ms/snappoint) bez oczekiwania na wyjątek.

Kolekcja migawki jest dostępny dla:
* Aplikacje środowiska .NET framework i program ASP.NET systemem .NET Framework 4.5 lub nowszej.
* .NET core 2.0 i ASP.NET Core 2.0 aplikacji uruchomionych w systemie Windows.

Są obsługiwane w następujących środowiskach:
* Usługa aplikacji Azure.
* Usługa w chmurze Azure programu rodziny systemów operacyjnych, 4 lub nowszego.
* Usług Azure Service Fabric uruchomiony w systemie Windows Server 2012 R2 lub nowszym.
* Azure maszyn wirtualnych z systemem Windows Server 2012 R2 lub nowszym.
* Lokalnych maszyn wirtualnych lub fizycznych z systemem Windows Server 2012 R2 lub nowszym.

> [!NOTE]
> Aplikacje klienckie (na przykład WPF i formularze systemu Windows platformy uniwersalnej systemu Windows) nie są obsługiwane.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET

1. [Włącz usługę Application Insights w aplikacji sieci web](app-insights-asp-net.md), jeśli nie jeszcze go jeszcze gotowe.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w aplikacji.

3. Przejrzyj domyślne opcje dodane do pakietu [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>24:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. W niektórych przypadkach (na przykład starszych wersji platformy .NET), konieczne może być [Konfigurowanie zbierania wyjątków](app-insights-asp-net-exceptions.md#exceptions) aby zobaczyć wyjątki z migawkami w portalu.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurowanie zbierania migawek dla aplikacji platformy ASP.NET Core 2.0

1. [Włącz usługę Application Insights w aplikacji sieci web platformy ASP.NET Core](app-insights-asp-net-core.md), jeśli nie jeszcze go jeszcze gotowe.

    > [!NOTE]
    > Należy się upewnić, że aplikacja odwołuje się do wersji 2.1.1 lub nowszego, Microsoft.ApplicationInsights.AspNetCore pakietu.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w aplikacji.

3. Modyfikowanie aplikacji `Startup` klasy, aby dodać i skonfigurować migawki modułu zbierającego dane telemetryczne procesora.

    Dodaj następujące instrukcje using `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Dodaj następujące `SnapshotCollectorTelemetryProcessorFactory` klasy do `Startup` klasy.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Dodaj `SnapshotCollectorConfiguration` i `SnapshotCollectorTelemetryProcessorFactory` usług do potoku uruchamiania:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Skonfiguruj moduł zbierający migawki przez dodanie sekcji SnapshotCollectorConfiguration do appsettings.json. Na przykład:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"24:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie zbierania migawek dla innych aplikacji .NET

1. Jeśli aplikacja nie jest już instrumentowane przy użyciu usługi Application Insights, rozpoczęcie pracy przez [Włączanie usługi Application Insights i ustawienie klucza Instrumentacji](app-insights-windows-desktop.md).

2. Dodaj [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w aplikacji.

3. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. Konieczne może być swój kod, aby zgłosić je modyfikować. Kod obsługi wyjątków jest zależna od struktury aplikacji, ale przykładem jest poniżej:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="grant-permissions"></a>Udziel uprawnień

Właściciele subskrypcji platformy Azure można sprawdzić migawki. Inni użytkownicy muszą mieć uprawnienie przez właściciela.

Aby przyznać uprawnienia, Przypisz `Application Insights Snapshot Debugger` roli do użytkowników, którzy będzie przeprowadzał inspekcję migawki. Tę rolę można przypisać do poszczególnych użytkowników lub grup właściciele subskrypcji dla zasobu usługi Application Insights docelowej lub grupy zasobów lub subskrypcji.

1. Przejdź do zasobu usługi Application Insights w portalu Azure.
1. Kliknij przycisk **(IAM) kontroli dostępu**.
1. Kliknij przycisk **+ Dodaj** przycisku.
1. Wybierz **Application Insights migawki debugera** z **ról** listy rozwijanej.
1. Wyszukaj, a następnie wprowadź nazwę użytkownika do dodania.
1. Kliknij przycisk **zapisać** przycisk, aby dodać użytkownika do roli.


> [!IMPORTANT]
> Migawki potencjalnie może zawierać informacje osobiste i innych poufnych w zmiennej i wartości parametrów.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Debugowanie migawek w portalu usługi Application Insights

Jeśli migawka jest dostępna dla danego wyjątku lub identyfikator problemu **Otwórz migawki debugowania** na pojawi się przycisk [wyjątek](app-insights-asp-net-exceptions.md) w portalu usługi Application Insights.

![Przycisk Otwórz migawki debugowania na wyjątek](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

W widoku debugowania migawek Zobacz stosu wywołań i okienku zmiennych. Po wybraniu ramki stosu wywołań w okienku Stos wywołań, można wyświetlić zmiennych lokalnych i wywołać parametrów dla tej funkcji w okienku zmiennych.

![Widok debugowania migawki w portalu](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Migawki mogą zawierać poufne informacje, i domyślnie nie są widoczne. Aby wyświetlić migawki, musisz mieć `Application Insights Snapshot Debugger` rola przypisana użytkownikowi.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Migawki z programu Visual Studio Enterprise 2017 debugowania
1. Kliknij przycisk **pobrać migawki** przycisk, aby pobrać `.diagsession` pliku, który można otworzyć w programie Visual Studio Enterprise 2017 r.

2. Aby otworzyć `.diagsession` pliku, należy najpierw [Pobierz i zainstaluj rozszerzenie migawki debugera programu Visual Studio](https://aka.ms/snapshotdebugger).

3. Po otwarciu pliku migawki, zostanie wyświetlona strona debugowania minizrzutu w programie Visual Studio. Kliknij przycisk **debugowania kodu zarządzanego** można rozpocząć debugowania migawki. Migawka zostanie otwarty wiersz kodu, w której wystąpił wyjątek, aby umożliwić debugowanie bieżący stan procesu.

    ![Wyświetl migawkę debugowania w programie Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Pobrany migawki zawiera wszystkie pliki symboli, które zostały odnalezione na serwerze aplikacji sieci web. Te pliki symboli są wymagane, aby skojarzyć dane migawki z kodu źródłowego. Dla aplikacji usługi aplikacji upewnij się, że włączyć symbol wdrożenia po opublikowaniu aplikacji sieci web.

## <a name="how-snapshots-work"></a>Jak działają migawki

Moduł zbierający migawki jest zaimplementowany jako [Application Insights Telemetrii procesora](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po uruchomieniu aplikacji migawki modułu zbierającego dane telemetryczne procesora jest dodawany do potoku dane telemetryczne aplikacji.
Po każdej aktualizacji wywołania aplikacji [TrackException](app-insights-asp-net-exceptions.md#exceptions), moduł zbierający migawki oblicza identyfikator problemu z typu zgłaszanego wyjątku i przerzucane metody.
Za każdym razem, aplikacji wywołuje TrackException, licznik jest zwiększany odpowiedni identyfikator problemu. Jeśli licznik osiągnie `ThresholdForSnapshotting` wartość, identyfikator problemu jest dodawany do planu zbierania danych.

Moduł zbierający migawki monitoruje również wyjątki, ponieważ są one generowane przez subskrybowanie [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) zdarzeń. Gdy zdarzenie jest generowane, identyfikator problemu wyjątku jest obliczana i porównywana identyfikatorów Problem w planie kolekcji.
Jeśli istnieje dopasowanie, tworzona jest migawka uruchomionego procesu. Migawka jest przypisany unikatowy identyfikator i wyjątek jest dołączana z danym identyfikatorem. Po powrocie obsługi FirstChanceException, zwrócony wyjątek normalnego przetwarzania. Po pewnym czasie wyjątek osiągnie ponownie metodę TrackException gdzie, wraz z identyfikatorem migawki jest zgłoszony do usługi Application Insights.

Proces główny kontynuuje działanie i obsługiwać ruch do użytkowników z małego przerwania. W tym samym czasie migawki jest przekazany do procesu przekazujący migawki. Przekazujący migawki tworzy minizrzutu i przekazuje ją do usługi Application Insights oraz wszystkie pliki odpowiednich symboli (.pdb).

> [!TIP]
> - Migawki procesu jest wstrzymane Sklonowanie uruchomionego procesu.
> - Tworzenie migawki trwa około 10-20 w milisekundach.
> - Wartość domyślna dla `ThresholdForSnapshotting` 1. Jest to również wartość minimalna. W związku z tym aplikacja ma ten sam wyjątek wyzwalanie **dwukrotnie** przed utworzeniem migawki.
> - Ustaw `IsEnabledInDeveloperMode` na true, jeśli chcesz wygenerować migawki podczas debugowania w programie Visual Studio.
> - Częstotliwość tworzenia migawki jest ograniczona przez `SnapshotsPerTenMinutesLimit` ustawienie. Domyślnie limit jest jedną migawkę, co 10 minut.
> - Mogą być przekazywane nie więcej niż 50 migawek na dzień.

## <a name="current-limitations"></a>Bieżące ograniczenia

### <a name="publish-symbols"></a>Publikuj symbole
Debuger migawki wymaga plików symboli na serwerze produkcyjnym w celu zdekodowania zmienne i zapewnia środowisko debugowania w programie Visual Studio. 15.2 wydanie programu Visual Studio 2017 publikuje symboli dla kompilacji wydania domyślnie po opublikowaniu go do usługi App Service. W poprzednich wersjach, należy dodać następujący wiersz do swój profil publikowania `.pubxml` plików symboli są publikowane w trybie wersji:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Dla rozwiązań usługi obliczenia Azure i innych typów, upewnij się, że pliki symboli są w tym samym folderze DLL aplikacji głównej (zazwyczaj `wwwroot/bin`) lub są dostępne w bieżącej ścieżce.

### <a name="optimized-builds"></a>Zoptymalizowane kompilacji
W niektórych przypadkach zmiennych lokalnych nie można wyświetlić w kompilacjach wydania z powodu optymalizacji, które są stosowane przy użyciu kompilatora JIT.
Jednak w usłudze Azure App Services, moduł zbierający migawki można deoptimize przerzucane metod, które są częścią jego planu zbierania danych.

> [!TIP]
> Zainstaluj rozszerzenie lokacji usługi Application Insights w usłudze App Service, aby uzyskać pomoc techniczną deoptimization.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Te wskazówki ułatwiają rozwiązywanie problemów z debugera migawki.

### <a name="use-the-snapshot-health-check"></a>Użyj sprawdzania kondycji migawki
Otwórz migawki debugowania nie pojawia się powoduje kilka typowych problemów. Przy użyciu nieaktualnych migawki modułu zbierającego, na przykład; osiągnięcia dzienny limit przekazywania; lub może migawki jest po prostu zbyt długo do przekazania. Umożliwia sprawdzanie kondycji migawki Rozwiązywanie typowych problemów.

Brak łącze w okienku wyjątek umożliwiający przejście do sprawdzenia kondycji migawki widoku śledzenia end-to-end.

![Wprowadź sprawdzenie kondycji migawki](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Interfejs interaktywnych, rozmów przypominającej szuka typowe problemy i przeprowadza Cię rozwiązywania tych problemów.

![Sprawdzanie kondycji](./media/app-insights-snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zobacz Podręcznik następujące kroki rozwiązywania problemów.

### <a name="verify-the-instrumentation-key"></a>Sprawdź klucza Instrumentacji

Upewnij się, że używasz klucza Instrumentacji poprawne w opublikowanej aplikacji. Zazwyczaj klucza Instrumentacji zostanie odczytany z pliku ApplicationInsights.config. Sprawdź, czy wartość jest taka sama jak klucza instrumentacji dla zasobu usługi Application Insights, który zostanie wyświetlony w portalu.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnij do najnowszej wersji pakietu NuGet

Aby upewnić się, że używasz najnowszej wersji Microsoft.ApplicationInsights.SnapshotCollector, użyj Menedżera pakietów NuGet programu Visual Studio. Informacje o wersji można znaleźć w folderze https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Sprawdź dzienniki przesyłania

Po utworzeniu migawki, plik minizrzutu (dmp) jest tworzony na dysku. Proces przesyłania oddzielne tworzy plik minizrzutu i przekazuje, oraz wszystkie skojarzone pliki PDB do magazynu Application Insights migawki debugera. Po pomyślnym przekazaniu minizrzut są usuwane z dysku. Pliki dziennika, aby proces przesyłania są przechowywane na dysku. W środowisku usługi aplikacji można znaleźć te dzienniki w `D:\Home\LogFiles`. Użyj witryny zarządzania Kudu dla aplikacji usługi, aby znaleźć te pliki dziennika.

1. Otwórz aplikację usługi aplikacji w portalu Azure.
2. Kliknij przycisk **zaawansowane narzędzia**, lub Wyszukaj **Kudu**.
3. Kliknij przycisk **Przejdź**.
4. W **konsoli debugowania** listy rozwijanej wybierz pozycję **CMD**.
5. Kliknij przycisk **LogFiles**.

Powinny pojawić się co najmniej jeden plik o nazwie, który rozpoczyna się od `Uploader_` lub `SnapshotUploader_` i `.log` rozszerzenia. Kliknij odpowiednią ikonę, aby pobrać wszystkie pliki dziennika lub je otworzyć w przeglądarce.
Nazwa pliku zawiera unikatowy sufiks, który identyfikuje wystąpienie usługi aplikacji. Jeśli wystąpienie usługi aplikacji znajduje się na więcej niż jednej maszynie, istnieją oddzielnych plików dziennika dla każdego komputera. Gdy przekazujący wykryje nowy plik minizrzutu, jest rejestrowany w pliku dziennika. Oto przykład pomyślnie migawki i przekaż:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> W powyższym przykładzie pochodzi z wersji 1.2.0 pakiet Microsoft.ApplicationInsights.SnapshotCollector NuGet. W starszych wersjach, proces przesyłania jest nazywany `MinidumpUploader.exe` i dziennika jest mniej szczegółowe.

W poprzednim przykładzie, klucz Instrumentacji jest `c12a605e73c44346a984e00000000000`. Ta wartość powinna być zgodna klucza instrumentacji aplikacji.
Minizrzut jest skojarzony z migawki z Identyfikatorem `139e411a23934dc0b9ea08a626db16c5`. Ten identyfikator można użyć do zlokalizowania telemetrii skojarzony wyjątek w Application Insights Analytics później.

Przekazujący skanowania pod kątem nowych baz danych PDB o co 15 minut. Oto przykład:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

W przypadku aplikacji, które są _nie_ hostowanych w usłudze App Service, dzienniki przesyłania znajdują się w tym samym folderze co minizrzutów: `%TEMP%\Dumps\<ikey>` (gdzie `<ikey>` jest klucz Instrumentacji).

### <a name="troubleshooting-cloud-services"></a>Rozwiązywanie problemów z usługami w chmurze
Dla ról usług w chmurze domyślny folder tymczasowy jest zbyt mały do przechowywania plików minizrzutu, co może prowadzić do utraty migawki.
Potrzebne miejsce zależy od całkowitej zestaw roboczy aplikacji i liczby równoczesnych migawek.
Zestaw roboczy rolę sieci web ASP.NET 32-bitowych jest zwykle od 200 MB do 500 MB.
Zezwalaj na co najmniej dwóch jednoczesnych migawek.
Na przykład jeśli aplikacja korzysta z 1 GB całkowita zestaw roboczy, należy się upewnić, że istnieje co najmniej 2 GB miejsca na dysku do przechowywania migawek.
Wykonaj poniższe kroki konfigurowania roli użytkownika usługi w chmurze z dedykowanym zasobu lokalnego migawek.

1. Dodaj nowego zasobu lokalnego do usługi w chmurze, edytując plik definicji (csdef) usługa w chmurze. W poniższym przykładzie zdefiniowano zasób o nazwie `SnapshotStore` o rozmiarze 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Zmodyfikuj kod uruchamiania roli użytkownika, aby dodać zmienną środowiskową, który wskazuje `SnapshotStore` zasobu lokalnego. Dla roli proces roboczy kod powinien zostać dodany do roli użytkownika `OnStart` metody:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Dla ról sieci Web (ASP.NET), kod należy dodać do aplikacji sieci web `Application_Start` metody:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Zaktualizuj plik ApplicationInsights.config roli użytkownika, aby zastąpić lokalizacja folderu tymczasowego używanego przez `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Użyj usługi Application Insights Wyszukaj, aby znaleźć wyjątki z migawkami

Po utworzeniu migawki przerzucane wyjątek zostanie oznaczony przy użyciu identyfikatora migawki. Ten identyfikator migawki jest uwzględniona jako właściwości niestandardowej, gdy dane telemetryczne wyjątku jest zgłoszony do usługi Application Insights. Przy użyciu **wyszukiwania** w usłudze Application Insights, można znaleźć wszystkie dane telemetryczne z `ai.snapshot.id` właściwości niestandardowej.

1. Przejdź do zasobu usługi Application Insights w portalu Azure.
2. Kliknij przycisk **wyszukiwania**.
3. Typ `ai.snapshot.id` w polu tekstowym wyszukiwania i naciśnij klawisz Enter.

![Wyszukaj dane telemetryczne z Identyfikatorem migawki w portalu](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Jeśli wyszukiwanie nie zwróciło żadnych wyników, migawek nie zostały zgłoszone w do usługi Application Insights dla aplikacji w wybranym zakresie czasu.

Aby wyszukać Identyfikatora określoną migawkę z dzienników przesyłania, należy wpisać ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć dane telemetryczne dla migawki, który został przekazany, wykonaj następujące kroki:

1. Sprawdź, czy przeglądania prawo zasobu usługi Application Insights, upewniając klucza instrumentacji.

2. Przy użyciu sygnatury czasowej w dzienniku przekazujący, Dostosuj zakres czasu filtr wyszukiwania, aby pokrywał się tego zakresu czasu.

Jeśli nadal nie widać Wystąpił wyjątek o takim identyfikatorze migawki, dane telemetryczne wyjątku nie zgłosił do usługi Application Insights. Taka sytuacja może się zdarzyć, jeśli awaria aplikacji po zajęło migawki, ale przed zgłosiła ona dane telemetryczne wyjątku. W takim wypadku zapoznaj się z dziennikami usługi aplikacji w obszarze `Diagnose and solve problems` aby zobaczyć, czy wystąpiły nieoczekiwane ponowne uruchomienia lub nieobsługiwane wyjątki.

### <a name="edit-network-proxy-or-firewall-rules"></a>Edytuj reguły serwera proxy lub zapory sieciowe

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, konieczne może być edytować reguły Zezwalaj aplikacji na komunikację z usługą debugera migawki. Oto [listę adresów IP i portów używanych przez debuger migawki](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Kolejne kroki

* [Ustaw snappoints w kodzie](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) można pobrać migawek bez oczekiwania na wyjątek.
* [Diagnozowanie wyjątków w aplikacjach sieci web](app-insights-asp-net-exceptions.md) wyjaśniono, jak wyświetlić więcej wyjątków do usługi Application Insights.
* [Inteligentne wykrywania](app-insights-proactive-diagnostics.md) automatycznie odnajduje anomalii wydajności.
