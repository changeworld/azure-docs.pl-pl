---
title: Usługi Azure Application Insights Snapshot Debugger dla aplikacji platformy .NET | Dokumentacja firmy Microsoft
description: Debugowanie migawek są automatycznie zbierane, gdy wyjątki zostaną zgłoszone w aplikacjach .NET w środowisku produkcyjnym
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: d4c27c8297fb5a2ad13a245279a206d00fc4f8b1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144129"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugowanie migawek wyjątków w aplikacjach .NET

Gdy wystąpi wyjątek, może automatycznie zbierać migawek debugowania z działającej aplikacji internetowej. Migawka przedstawia stan zmiennych i kod źródłowy w momencie utworzenia zgłoszenia wyjątku. Rozszerzenie Snapshot Debugger (wersja zapoznawcza) w [usługi Azure Application Insights](app-insights-overview.md) monitoruje dane telemetryczne dotyczące wyjątków z aplikacji sieci web. Zbiera migawki na listy wyjątków zgłaszanie górnej dzięki temu uzyskasz informacje potrzebne do diagnozowania problemów w środowisku produkcyjnym. Obejmują [pakiet NuGet modułu zbierającego migawki](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) w aplikacji i opcjonalnie skonfigurować kolekcję parametrów w [plik ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Migawki są wyświetlane na [wyjątki](app-insights-asp-net-exceptions.md) w portalu Application Insights.

Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Aby uzyskać bardziej zaawansowane środowisko debugowania z kodem źródłowym, należy otworzyć migawki za pomocą programu Visual Studio 2017 Enterprise, [pobierając rozszerzenie Snapshot Debugger dla programu Visual Studio](https://aka.ms/snapshotdebugger). W programie Visual Studio, możesz również [Ustaw punkty przyciągania do interaktywnego robienia migawek](https://aka.ms/snappoint) bez oczekiwania na wyjątek.

Zbieranie migawek jest dostępna dla:
* .NET framework i ASP.NET działających .NET Framework 4.5 lub nowszej.
* Aplikacje platformy .NET core 2.0 i ASP.NET Core 2.0, systemem Windows.

Są obsługiwane w następujących środowiskach:
* Usługa Azure App Service.
* Usługa w chmurze Azure programu rodziny systemów operacyjnych, 4 lub nowszego.
* Usługi platformy Azure Service Fabric uruchomiony w systemie Windows Server 2012 R2 lub nowszym.
* Usługa Azure maszyn wirtualnych z systemem Windows Server 2012 R2 lub nowszym.
* Lokalnych maszyn wirtualnych lub fizycznych z systemem Windows Server 2012 R2 lub nowszym.

> [!NOTE]
> Aplikacje klienckie (na przykład, WPF, formularze Windows lub platformy uniwersalnej systemu Windows) nie są obsługiwane.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET

1. [Włącz usługę Application Insights w aplikacji sieci web](app-insights-asp-net.md), jeśli nie została ona jeszcze zrobione.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Przejrzyj opcje domyślne, które pakiet dodane do [plik ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

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
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
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

4. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. W niektórych przypadkach (na przykład starsze wersje platformy .NET), konieczne może być [Konfigurowanie zbierania wyjątków](app-insights-asp-net-exceptions.md#exceptions) aby zobaczyć wyjątki, przy użyciu migawek w portalu.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Konfigurowanie zbierania migawek dla aplikacji ASP.NET Core 2.0

1. [Włącz usługę Application Insights w aplikacji sieci web platformy ASP.NET Core](app-insights-asp-net-core.md), jeśli nie została ona jeszcze zrobione.

    > [!NOTE]
    > Należy się upewnić, że aplikacja odwołuje się do wersji 2.1.1 lub nowszego, pakietu Microsoft.ApplicationInsights.AspNetCore.

2. Obejmują [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Modyfikowanie aplikacji `Startup` klasy można dodawać i konfigurować modułu zbierającego migawki danych telemetrycznych procesora.

    Dodaj następujące instrukcje using `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Dodaj następujący kod `SnapshotCollectorTelemetryProcessorFactory` klasy `Startup` klasy.

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
    Dodaj `SnapshotCollectorConfiguration` i `SnapshotCollectorTelemetryProcessorFactory` usług do uruchomienia potoku:

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

4. Konfigurowanie modułu zbierającego migawki, dodając sekcji SnapshotCollectorConfiguration do pliku appsettings.json. Na przykład:

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
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Konfigurowanie zbierania migawek dla innych aplikacji .NET

1. Jeśli aplikacja nie jest już za pomocą usługi Application Insights, zacznij od [włączenie usługi Application Insights i ustawić dla klucza Instrumentacji](app-insights-windows-desktop.md).

2. Dodaj [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pakietu NuGet w swojej aplikacji.

3. Migawki są zbierane tylko na wyjątki, które są zgłaszane do usługi Application Insights. Może być konieczne zmodyfikowanie kodu na zgłoszenie ich. Kodu obsługi wyjątków zależy od struktury aplikacji, ale przykład znajduje się poniżej:
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

## <a name="grant-permissions"></a>Udzielenie uprawnień

Właściciele subskrypcji platformy Azure można sprawdzić migawki. Inni użytkownicy muszą mieć uprawnienie przez właściciela.

Aby przyznać uprawnienia, Przypisz `Application Insights Snapshot Debugger` ról do użytkowników, którzy będą inspekcji migawki. Tę rolę można przypisać do poszczególnych użytkowników lub grupy według właścicieli subskrypcji dla elementu docelowego zasobu usługi Application Insights lub grupy zasobów lub subskrypcji.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
1. Kliknij przycisk **kontrola dostępu (IAM)**.
1. Kliknij przycisk **+ Dodaj** przycisku.
1. Wybierz **Application Insights Snapshot Debugger** z **role** listy rozwijanej.
1. Wyszukaj, a następnie wprowadź nazwę użytkownika do dodania.
1. Kliknij przycisk **Zapisz** przycisk, aby dodać użytkownika do roli.


> [!IMPORTANT]
> Migawki mogą potencjalnie zawierać osobistych, jak i innych informacji poufnych w zmiennej i wartości parametrów.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Debugowanie migawki w portalu Application Insights

Jeśli migawka jest niedostępna dla danego wyjątku lub identyfikator problemu **Otwórz migawkę debugowania** przycisk pojawia się na [wyjątek](app-insights-asp-net-exceptions.md) w portalu Application Insights.

![Przycisk Otwórz migawkę debugowania przy wyjątku](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

W widoku debugowania migawki zobaczysz stos wywołań i okienko zmiennych. Po wybraniu ramki stosu wywołań w panelu stosu wywołań, mogą wyświetlać zmienne lokalne i parametry dla tej funkcji wywołanie w okienku zmiennych.

![Wyświetl migawkę debugowania w portalu](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Migawki mogą zawierać poufne informacje, a domyślnie nie są widoczne. Aby wyświetlić migawki, konieczne jest posiadanie `Application Insights Snapshot Debugger` rola przypisana użytkownikowi.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Debugowanie migawki za pomocą programu Visual Studio 2017 Enterprise
1. Kliknij przycisk **Pobierz migawkę** przycisk, aby pobrać `.diagsession` plików, które mogą być otwierane w programie Visual Studio 2017 Enterprise.

2. Aby otworzyć `.diagsession` pliku, należy najpierw [Pobierz i zainstaluj rozszerzenie Snapshot Debugger dla programu Visual Studio](https://aka.ms/snapshotdebugger).

3. Po otwarciu pliku migawki zostanie wyświetlona strona minizrzutu w programie Visual Studio. Kliknij przycisk **debugowanie kodu zarządzanego** można rozpocząć debugowania migawki. Migawka zostanie otwarty do wiersza kodu, w którym wystąpił wyjątek, aby można było debugować bieżący stan procesu.

    ![Wyświetl migawkę debugowania w programie Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

Pobrany migawki zawiera wszystkie pliki symboli, które zostały odnalezione na serwerze aplikacji sieci web. Te pliki symboli są wymagane, aby skojarzyć dane migawki z kodem źródłowym. W przypadku aplikacji usługi App Service upewnij się, że umożliwiają wdrażanie symboli, podczas publikowania aplikacji sieci web.

## <a name="how-snapshots-work"></a>Jak działają migawek

Modułu zbierającego migawki jest implementowany jako [Application Insights Telemetrii procesora](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po uruchomieniu aplikacji, procesor danych Telemetrycznych modułu zbierającego migawki jest dodawany do potoku telemetrii Twojej aplikacji.
Każdym wywołaniami aplikacji [metoda TrackException](app-insights-asp-net-exceptions.md#exceptions), modułu zbierającego migawki oblicza identyfikator problemu z rodzaj zgłaszanego wyjątku i zgłaszanie metody.
Za każdym razem, Twoja aplikacja wywołuje metoda TrackException, licznik jest zwiększany odpowiedni identyfikator problemu. Jeśli licznik osiągnie `ThresholdForSnapshotting` wartości, identyfikator problemu jest dodawany do planu zbierania.

Jak są one generowane przez Subskrybowanie modułu zbierającego migawki również monitoruje wyjątki [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) zdarzeń. Gdy zostanie wyzwolony tego zdarzenia, identyfikator problemu wyjątku jest obliczana i porównywana z identyfikatorami Problem w planie kolekcji.
Jeśli istnieje dopasowanie, tworzona jest migawka uruchomionego procesu. Migawka jest przypisany unikatowy identyfikator, a wyjątek jest oznaczony za pomocą tego identyfikatora. Po powrocie obsługi FirstChanceException zgłoszony wyjątek są przetwarzane w zwykły sposób. Po pewnym czasie wyjątek osiągnie ponownie metodą metoda TrackException gdzie, oraz identyfikator migawki, jest zgłaszany do usługi Application Insights.

Główny proces jest kontynuowany do uruchamiania i obsługiwać ruch do użytkowników z niewielkim przerwy. W tym samym czasie migawki jest przekazywane do procesu Przekazywarka migawki. Przekazujesz migawki tworzy minizrzutu i przekazuje je do usługi Application Insights oraz wszystkie pliki odpowiednich symboli (.pdb).

> [!TIP]
> - Migawki procesu jest wstrzymane klonu uruchomionego procesu.
> - Tworzenie migawki trwa około 10 do 20 MS.
> - Wartością domyślną dla `ThresholdForSnapshotting` 1. Jest to również wartość minimalna. W związku z tym, Twoja aplikacja ma wyzwolić ten sam wyjątek **dwukrotnie** przed utworzeniem migawki.
> - Ustaw `IsEnabledInDeveloperMode` wartość true, jeśli chcesz wygenerować migawki podczas debugowania w programie Visual Studio.
> - Częstotliwość tworzenia migawki jest ograniczona przez `SnapshotsPerTenMinutesLimit` ustawienie. Domyślnie limit jest jedną migawkę, co 10 minut.
> - Mogą być przekazywane nie więcej niż 50 migawek na dzień.

## <a name="current-limitations"></a>Bieżące ograniczenia

### <a name="publish-symbols"></a>Publikuj symbole
Rozszerzenie Snapshot Debugger wymaga plików symboli na serwerze produkcyjnym do zdekodowania zmienne i aby zapewnić środowisko debugowania w programie Visual Studio.
Wersja 15.2 (lub nowszy) programu Visual Studio 2017 publikuje symbole dla wersji kompilacji domyślnie, publikuje w usłudze App Service. W poprzednich wersjach, należy dodać następujący wiersz do Twojego profilu publikowania `.pubxml` plik symboli są publikowane w trybie wydania:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

W przypadku usługi Azure Compute a innymi typami danych, upewnij się, że pliki symbol znajdują się w tym samym folderze plik .dll aplikacji głównej (zazwyczaj `wwwroot/bin`) lub są dostępne w bieżącej ścieżce.

### <a name="optimized-builds"></a>Zoptymalizowane kompilacjami
W niektórych przypadkach nie można wyświetlić zmiennych lokalnych w wydawanych wersjach ze względu na optymalizacje, które są stosowane przy użyciu kompilatora JIT.
Jednak w usłudze Azure App Services, modułu zbierającego migawki można deoptimize zgłaszanie metod, które są częścią planu zbierania.

> [!TIP]
> W usłudze App Service, aby uzyskać pomoc techniczną deoptimization, należy zainstalować rozszerzenie witryny usługi Application Insights.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe wskazówki ułatwiają rozwiązywanie problemów z rozszerzenia Snapshot Debugger.

### <a name="use-the-snapshot-health-check"></a>Użyj sprawdzania kondycji migawki
Kilka typowych problemów z wynikiem Otwórz migawkę debugowania nie jest wyświetlane. Przy użyciu nieaktualnych modułu zbierającego migawki, na przykład; osiągnięcia dziennego limitu przekazywanych; lub może być migawki jest po prostu zbyt długo do przekazania. Umożliwia sprawdzanie kondycji migawki Rozwiązywanie typowych problemów.

W okienku wyjątek widoku śledzenia end-to-end, który umożliwia przejście do sprawdzania kondycji migawki znajduje się link.

![Wprowadź sprawdzenie kondycji migawki](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

Interfejs interaktywny, jak rozmowa szuka typowe problemy i przeprowadzi Cię rozwiązywania tych problemów.

![Kontrola kondycji](./media/app-insights-snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zobacz Podręcznik następujące kroki rozwiązywania problemów.

### <a name="verify-the-instrumentation-key"></a>Sprawdź klucz Instrumentacji

Upewnij się, że używasz klucza Instrumentacji poprawne w opublikowanej aplikacji. Zazwyczaj klucz Instrumentacji jest do odczytu z pliku ApplicationInsights.config. Sprawdź, czy wartość jest taka sama jak klucz Instrumentacji zasobu usługi Application Insights, które pojawi się w portalu.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnij do najnowszej wersji pakietu NuGet

Aby upewnić się, że używasz najnowszej wersji Microsoft.ApplicationInsights.SnapshotCollector, użyj Menedżera pakietów NuGet dla programu Visual Studio. Informacje o wersji znajduje się w temacie https://github.com/Microsoft/ApplicationInsights-Home/issues/167

### <a name="check-the-uploader-logs"></a>Sprawdź dzienniki obiektu przekazującego

Po utworzeniu migawki, tworzony jest plik minizrzutu (.dmp) na dysku. Proces obiektu przekazującego osobne tworzy ten plik minizrzutu i przekazuje go wraz z wszelkich skojarzonych plików PDB do magazynu Application Insights Snapshot Debugger. Po pomyślnym przekazaniu minizrzut jest usuwany z dysku. Pliki dziennika dla procesu Przekazywarka są przechowywane na dysku. W środowisku usługi App Service można znaleźć te dzienniki w `D:\Home\LogFiles`. Użyj witryny Kudu zarządzania dla usługi App Service, aby znaleźć te pliki dziennika.

1. Otwórz aplikację usługi App Service w witrynie Azure portal.
2. Kliknij przycisk **Narzędzia zaawansowane**, lub Wyszukaj **Kudu**.
3. Kliknij przycisk **Przejdź**.
4. W **konsoli debugowania** listy rozwijanej wybierz pozycję **CMD**.
5. Kliknij przycisk **LogFiles**.

Powinien zostać wyświetlony co najmniej jeden plik z nazwą rozpoczynającą się od `Uploader_` lub `SnapshotUploader_` i `.log` rozszerzenia. Kliknij na odpowiednią ikonę, aby pobrać wszystkie pliki dziennika lub otworzyć je w przeglądarce.
Nazwa pliku zawiera unikatowy sufiks, który identyfikuje wystąpienia usługi App Service. Wystąpienie usługi App Service jest przechowywana na więcej niż jednym komputerze, czy osobne pliki dziennika dla poszczególnych maszyn. Gdy przekazujesz wykryje nowy plik minizrzutu, zostało zapisane w pliku dziennika. Oto przykład pomyślne migawki i przekazywania:

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
> W powyższym przykładzie pochodzi z pakietu Microsoft.ApplicationInsights.SnapshotCollector NuGet wersji 1.2.0 lub nowszej. We wcześniejszych wersjach, proces Przekazywarka jest nazywany `MinidumpUploader.exe` i dziennika jest mniej szczegółowa.

W poprzednim przykładzie klucz Instrumentacji jest `c12a605e73c44346a984e00000000000`. Ta wartość powinna odpowiadać klucz instrumentacji aplikacji.
Minizrzut jest skojarzony z migawki z Identyfikatorem `139e411a23934dc0b9ea08a626db16c5`. Tego Identyfikatora można użyć później do zlokalizowania telemetrii skojarzony wyjątek w analizy usługi Application Insights.

Przekazujesz skanowania pod kątem nowych baz danych PDB, o co 15 minut. Oto przykład:

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

W przypadku aplikacji, _nie są_ hostowana w usłudze App Service, dzienniki Przekazywarka znajdują się w tym samym folderze co minizrzutów: `%TEMP%\Dumps\<ikey>` (gdzie `<ikey>` jest klucz Instrumentacji).

### <a name="troubleshooting-cloud-services"></a>Rozwiązywanie problemów z usługami w chmurze
Dla ról w usługach w chmurze domyślny folder tymczasowy może być zbyt mała do przechowywania plików minizrzutu, co prowadzi do utraty migawki.
Potrzebne miejsce zależy od całkowitej zestaw roboczy aplikacji i liczbę współbieżnych migawek.
Zestaw roboczy role sieci web ASP.NET 32-bitowych jest zazwyczaj od 200 MB do 500 MB.
Zezwala na co najmniej dwa współbieżne migawki.
Na przykład jeśli aplikacja korzysta z 1 GB pamięci całkowita zestawu roboczego, należy się upewnić, że istnieje co najmniej 2 GB miejsca na dysku do przechowywania migawek.
Wykonaj następujące kroki, aby skonfigurować usługi roli usługi w chmurze z dedykowanych zasobów lokalnych na potrzeby migawek.

1. Dodaj nowy zasób lokalnego do usługi w chmurze, edytując plik definicji (csdef) usługi w chmurze. W poniższym przykładzie zdefiniowano zasób o nazwie `SnapshotStore` o rozmiarze 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Zmodyfikuj kod startowy roli użytkownika, aby dodać zmienną środowiskową, który wskazuje na `SnapshotStore` zasobu lokalnego. Dla ról procesów roboczych kod powinny zostać dodane do Twojej roli `OnStart` metody:
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

3. Aktualizowanie pliku ApplicationInsights.config Twojej roli w celu zastąpienia lokalizacja folderu tymczasowego używana przez `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="overriding-the-shadow-copy-folder"></a>Zastępowanie folder kopii w tle

Podczas uruchamiania modułu zbierającego migawki, próbuje odnaleźć folderu na dysku, który jest odpowiedni do uruchamiania procesu Przekazywarka migawki. Wybrany folder jest określany jako folder kopii w tle.

Modułu zbierającego migawki sprawdza kilka lokalizacji dobrze znanego, upewniając się, że ma uprawnienia do skopiowania plików binarnych Przekazywarka migawki. Używane są następujące zmienne środowiskowe:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- FOLDER DANE APLIKACJI
- TEMP

Jeśli nie można odnaleźć odpowiedniego folderu, modułu zbierającego migawki zgłasza błąd powiedzenie _"Nie można odnaleźć folderu kopii w tle odpowiednie"._

Jeśli kopia zakończy się niepowodzeniem, raporty modułu zbierającego migawki `ShadowCopyFailed` błędu.

Jeśli przekazujesz nie można uruchomić, raporty modułu zbierającego migawki `UploaderCannotStartFromShadowCopy` błędu. Treść komunikatu często zawierają `System.UnauthorizedAccessException`. Ten błąd zazwyczaj występuje, ponieważ aplikacja jest uruchomiona przy użyciu konta z ograniczonymi uprawnieniami. To konto ma uprawnienia do zapisu do folderu kopii w tle, ale go nie ma uprawnień do wykonania kodu.

Ponieważ te błędy zwykle i tak się stanie podczas uruchamiania ich będzie zazwyczaj może występować `ExceptionDuringConnect` informacją o tym błędzie _"Przekazywarka nie powiodło się."_

Aby uniknąć tych błędów, można określić folder kopii w tle ręcznie za pośrednictwem `ShadowCopyFolder` opcji konfiguracji. Na przykład za pomocą pliku ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Lub, jeśli używasz pliku appsettings.json za pomocą aplikacji .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Aby znaleźć wyjątki z migawkami Użyj funkcji wyszukiwania usługi Application Insights

Po utworzeniu migawki Zgłaszanie wyjątku jest oznaczony przy użyciu identyfikatora migawki Ten identyfikator migawki jest dołączony jako właściwości niestandardowych, gdy dane telemetryczne dotyczące wyjątków jest zgłaszany do usługi Application Insights. Za pomocą **wyszukiwania** w usłudze Application Insights można znaleźć wszystkie dane telemetryczne z `ai.snapshot.id` właściwości niestandardowej.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
2. Kliknij przycisk **wyszukiwania**.
3. Typ `ai.snapshot.id` w polu tekstowym wyszukiwania i naciśnij klawisz Enter.

![Wyszukiwanie danych telemetrycznych przy użyciu Identyfikatora migawki w portalu](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Jeśli to wyszukiwanie nie zwróciło żadnych wyników, nie znaleziono migawek zostały zgłoszone w do usługi Application Insights dla aplikacji w wybranym zakresie czasu.

Aby wyszukać identyfikator określoną migawkę z dzienników obiektu przekazującego, wpisz ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć dane telemetryczne dla migawki, który został przekazany, wykonaj następujące kroki:

1. Należy się upewnić, że szukasz na właściwy zasób usługi Application Insights, sprawdzając klucz instrumentacji.

2. Przy użyciu sygnaturę czasową od dziennika obiektu przekazującego, Dostosuj zakres czasu filtr wyszukiwania, aby uwzględnić ten zakres czasu.

Jeśli nadal nie widzisz wyjątków o takim identyfikatorze migawki, dane telemetryczne dotyczące wyjątków nie zostało zgłoszone do usługi Application Insights. Ta sytuacja może wystąpić, jeśli aplikacja uległa awarii po zajęło migawki, ale przed zgłosił dane telemetryczne dotyczące wyjątków. W takim przypadku sprawdź dzienniki usługi App Service w ramach `Diagnose and solve problems` aby zobaczyć, czy wystąpiły nieoczekiwane ponowne uruchomienie lub nieobsługiwane wyjątki.

### <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie reguł serwera proxy lub zapory sieciowej

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapora, może być konieczne edytowanie reguły, aby umożliwić aplikacji do komunikowania się z usługą rozszerzenia Snapshot Debugger. Oto [listę adresów IP i porty używane przez rozszerzenie Snapshot Debugger](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Kolejne kroki

* [Ustaw punkty przyciągania w kodzie](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) można pobrać migawek bez oczekiwania na wyjątek.
* [Diagnozowanie wyjątków w aplikacjach sieci web](app-insights-asp-net-exceptions.md) wyjaśnia, jak ustawienie widoczności więcej wyjątków usługi Application Insights.
* [Wykrywanie inteligentne](app-insights-proactive-diagnostics.md) automatycznie wykrywa anomalie wydajność.
