---
title: Rozwiązywanie problemów z debugerem migawek usługi Azure Application Insights
description: W tym artykule przedstawiono kroki rozwiązywania problemów i informacje, aby pomóc deweloperom, którzy mają problemy z włączaniem lub używaniem debugera migawek usługi Application Insights.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671413"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Rozwiązywanie problemów z włączaniem debugera migawek usługi Application Insights lub wyświetlaniem migawek
Jeśli włączono debuger migawek usługi Application Insights dla aplikacji, ale nie są one widząc migawki dla wyjątków, można użyć tych instrukcji do rozwiązywania problemów. Może istnieć wiele różnych powodów, dla których migawki nie są generowane. Można uruchomić sprawdzanie kondycji migawki, aby zidentyfikować niektóre z możliwych typowych przyczyn.

## <a name="use-the-snapshot-health-check"></a>Korzystanie z sprawdzania kondycji migawki
Kilka typowych problemów powoduje, że migawka otwartego debugowania nie jest wyświetlana. Na przykład przy użyciu przestarzałego modułu zbierającego migawkę; osiągnięcie dziennego limitu przesyłania; a może migawka po prostu zajmuje dużo czasu, aby przesłać. Sprawdzanie kondycji migawki służy do rozwiązywania typowych problemów.

Istnieje łącze w okienku wyjątków widoku śledzenia end-to-end, który zabierze Cię do sprawdzania kondycji migawki.

![Wprowadź sprawdzanie kondycji migawki](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktywny, podobny do czatu interfejs szuka typowych problemów i poprowadzi Cię, aby je naprawić.

![Kontrola kondycji](./media/snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zapoznaj się z następującymi ręcznymi krokami rozwiązywania problemów.

## <a name="verify-the-instrumentation-key"></a>Sprawdź klucz oprzyrządowania

Upewnij się, że używasz odpowiedniego klucza instrumentacji w opublikowanej aplikacji. Zazwyczaj klucz instrumentacji jest odczytywany z pliku ApplicationInsights.config. Sprawdź, czy wartość jest taka sama jak klucz instrumentacji dla zasobu usługi Application Insights, który jest widoczny w portalu.

## <a name="preview-versions-of-net-core"></a>Wersja zapoznawcza programu .NET Core
Jeśli aplikacja używa wersji zapoznawczej .NET Core, a debuger migawek został włączony za pośrednictwem [okienka Usługi Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) w portalu, debuger migawek może nie zostać uruchomiony. Postępuj zgodnie z instrukcjami w [Enable Migawka Debuger dla innych środowisk,](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) aby najpierw dołączyć [microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet pakiet z aplikacją ***oprócz*** włączania za pośrednictwem [okienka usługi Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnienie do najnowszej wersji pakietu NuGet

Jeśli debuger migawek został włączony za pośrednictwem [okienka usługi Application Insights w portalu,](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)aplikacja powinna już być uruchomiona najnowszy pakiet NuGet. Jeśli debuger migawek została włączona przez dołączenie [pakietu Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet, użyj Menedżera pakietów NuGet programu Visual Studio, aby upewnić się, że używasz najnowszej wersji programu Microsoft.ApplicationInsights.SnapshotCollector. Informacje o wersji można znaleźć na stroniehttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Sprawdzanie dzienników przesyłania

Po utworzeniu migawki na dysku tworzony jest plik minidump (.dmp). Oddzielny proces przesyłania tworzy ten plik minidump i przekazuje go, wraz z wszelkimi skojarzonymi plikami PDB, do magazynu debugera migawek usługi Application Insights. Po pomyślnym przesłaniu minidumpu jest on usuwany z dysku. Pliki dziennika dla procesu przesyłania są przechowywane na dysku. W środowisku usługi app service można `D:\Home\LogFiles`znaleźć te dzienniki w . Użyj witryny zarządzania Kudu dla usługi App Service, aby znaleźć te pliki dziennika.

1. Otwórz aplikację usługi App Service w witrynie Azure portal.
2. Kliknij pozycję **Narzędzia zaawansowane**lub wyszukaj **kudu**.
3. Kliknij **przycisk Przejdź**.
4. Z listy rozwijanej **konsoli debugowania** wybierz pozycję **CMD**.
5. Kliknij **pozycję LogFiles**.

Powinien zostać wyświetlony co najmniej jeden plik `Uploader_` `SnapshotUploader_` o `.log` nazwie, która zaczyna się od lub i rozszerzenie. Kliknij odpowiednią ikonę, aby pobrać wszystkie pliki dziennika lub otworzyć je w przeglądarce.
Nazwa pliku zawiera unikatowy sufiks identyfikujący wystąpienie usługi App Service. Jeśli wystąpienie usługi App Service jest hostowane na więcej niż jednym komputerze, istnieją oddzielne pliki dziennika dla każdego komputera. Gdy przesyłający wykryje nowy plik minidump, jest rejestrowany w pliku dziennika. Oto przykład pomyślnej migawki i przesłania:

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
> Powyższy przykład pochodzi z wersji 1.2.0 pakietu Microsoft.ApplicationInsights.SnapshotCollector NuGet. We wcześniejszych wersjach proces przesyłania `MinidumpUploader.exe` jest wywoływany, a dziennik jest mniej szczegółowy.

W poprzednim przykładzie kluczem `c12a605e73c44346a984e00000000000`oprzyrządowania jest . Ta wartość powinna być zgodna z kluczem instrumentacji dla aplikacji.
Minidump jest skojarzony z migawką `139e411a23934dc0b9ea08a626db16c5`z identyfikatorem . Ten identyfikator można użyć później, aby zlokalizować skojarzone dane telemetryczne wyjątków w usłudze Application Insights Analytics.

Przesyłający skanuje w poszukiwaniu nowych pdb około raz na 15 minut. Oto przykład:

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

W przypadku aplikacji, które _nie są_ hostowane w usłudze App Service, dzienniki `%TEMP%\Dumps\<ikey>` przesyłania `<ikey>` znajdują się w tym samym folderze co minidumps: (gdzie znajduje się klucz instrumentacji).

## <a name="troubleshooting-cloud-services"></a>Rozwiązywanie problemów z usługami w chmurze
W przypadku ról w usługach w chmurze domyślny folder tymczasowy może być zbyt mały, aby pomieścić pliki minidump, co prowadzi do utraty migawek.
Miejsce potrzebne zależy od całkowitego zestawu roboczego aplikacji i liczby równoczesnych migawek.
Zestaw roboczy 32-bitowej ASP.NET roli sieci web wynosi zazwyczaj od 200 MB do 500 MB.
Zezwalaj na co najmniej dwie równoczesne migawki.
Na przykład jeśli aplikacja używa 1 GB całkowitego zestawu roboczego, należy upewnić się, że istnieje co najmniej 2 GB miejsca na dysku do przechowywania migawek.
Wykonaj następujące kroki, aby skonfigurować rolę usługi w chmurze za pomocą dedykowanego zasobu lokalnego dla migawek.

1. Dodaj nowy zasób lokalny do usługi w chmurze, edytując plik definicji usługi w chmurze (csdef). Poniższy przykład definiuje zasób wywoływany `SnapshotStore` o rozmiarze 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Zmodyfikuj kod startowy roli, aby `SnapshotStore` dodać zmienną środowiskową, która wskazuje na zasób lokalny. W przypadku ról procesu roboczego kod powinien `OnStart` zostać dodany do metody roli:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   W przypadku ról sieci Web (ASP.NET) kod należy dodać `Application_Start` do metody aplikacji sieci web:
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

3. Zaktualizuj plik ApplicationInsights.config roli roli, aby zastąpić tymczasową lokalizację folderu używanego przez`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Zastępowanie folderu Kopiowanie w tle

Po uruchomieniu modułu zbierającego migawki próbuje znaleźć folder na dysku, który jest odpowiedni do uruchamiania procesu przekazywania migawek. Wybrany folder jest nazywany folderem Kopia w tle.

Moduł zbierający migawki sprawdza kilka dobrze znanych lokalizacji, upewniając się, że ma uprawnienia do kopiowania plików binarnych przesyłania migawek. Używane są następujące zmienne środowiskowe:
- Fabric_Folder_App_Temp
- Localappdata
- Appdata
- Najwyższa temp

Jeśli nie można odnaleźć odpowiedniego folderu, aplikacja Snapshot Collector zgłasza komunikat _"Nie można znaleźć odpowiedniego folderu kopii w tle"._

Jeśli kopia nie powiedzie `ShadowCopyFailed` się, moduł zbierający migawki zgłasza błąd.

Jeśli nie można uruchomić modułu przekazu, aplikacja Snapshot Collector zgłasza błąd. `UploaderCannotStartFromShadowCopy` Treść wiadomości często zawiera `System.UnauthorizedAccessException`. Ten błąd zwykle występuje, ponieważ aplikacja jest uruchomiona przy koncie o zmniejszonych uprawnieniach. Konto ma uprawnienia do zapisu w folderze kopiowania w tle, ale nie ma uprawnień do wykonywania kodu.

Ponieważ te błędy zwykle zdarzają się podczas uruchamiania, zwykle następuje błąd z informacją `ExceptionDuringConnect` _"Nie można uruchomić narzędzia Uploader"._

Aby obejść te błędy, można ręcznie określić folder `ShadowCopyFolder` kopiowania w tle za pomocą opcji konfiguracji. Na przykład za pomocą ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Lub, jeśli używasz appsettings.json z aplikacją .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Wyszukiwanie usługi Application Insights umożliwia znajdowanie wyjątków z migawkami

Po utworzeniu migawki wyjątek zgłaszania jest oznaczony identyfikatorem migawki. Ten identyfikator migawki jest uwzględniony jako właściwość niestandardowa, gdy dane telemetryczne wyjątku są zgłaszane do usługi Application Insights. Za pomocą **wyszukiwania** w usłudze Application Insights, można znaleźć wszystkie dane telemetryczne z właściwością niestandardową. `ai.snapshot.id`

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
2. Kliknij przycisk **Wyszukaj**.
3. Wpisz `ai.snapshot.id` pole tekstowe Wyszukaj i naciśnij klawisz Enter.

![Wyszukiwanie danych telemetrycznych z identyfikatorem migawki w portalu](./media/snapshot-debugger/search-snapshot-portal.png)

Jeśli to wyszukiwanie zwraca żadnych wyników, a następnie żadne migawki zostały zgłoszone do usługi Application Insights dla aplikacji w wybranym zakresie czasu.

Aby wyszukać określony identyfikator migawki z dzienników przesyłania, wpisz ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć danych telemetrycznych migawki, o której wiadomo, że została przekazana, wykonaj następujące kroki:

1. Sprawdź, czy szukasz odpowiedniego zasobu usługi Application Insights, weryfikując klucz instrumentacji.

2. Korzystając z sygnatury czasowej z dziennika przesyłarka, dostosuj filtr Zakres czasu wyszukiwania, aby uwzględnić ten zakres czasu.

Jeśli nadal nie widzisz wyjątku o tym identyfikatorze migawki, dane telemetryczne wyjątku nie zostały zgłoszone do usługi Application Insights. Taka sytuacja może się zdarzyć, jeśli aplikacja uległa awarii po jej uruchomieniu migawki, ale przed zgłoszeniem danych telemetrycznych wyjątku. W takim przypadku sprawdź dzienniki `Diagnose and solve problems` usługi app service w obszarze, aby sprawdzić, czy wystąpiły nieoczekiwane ponowne uruchomienie lub nieobsługiwał wyjątków.

## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie reguł serwera proxy lub zapory sieciowej

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapory, może być konieczne edytowanie reguł, aby umożliwić aplikacji komunikowanie się z usługą debugera migawek. Wiadomości IP używane przez debuger migawki są zawarte w tagu usługi Usługi Azure Monitor.
