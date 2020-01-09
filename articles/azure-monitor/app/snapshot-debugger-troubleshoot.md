---
title: Rozwiązywanie problemów z usługą Azure Application Insights Snapshot Debugger
description: W tym artykule przedstawiono kroki rozwiązywania problemów oraz informacje pomocne w przypadku deweloperów, którzy mają problemy z włączaniem lub używaniem Snapshot Debugger Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: mbullwin
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 4544f42e7c024b21c4ae050d9b11e0f9e2786d57
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432329"
---
# <a id="troubleshooting"></a>Rozwiązywanie problemów z włączaniem Application Insights Snapshot Debugger lub wyświetlania migawek
Jeśli włączono Application Insights Snapshot Debugger dla aplikacji, ale nie widzisz migawek dla wyjątków, możesz użyć tych instrukcji do rozwiązywania problemów. Może istnieć wiele różnych powodów, dla których migawki nie są generowane. Możesz uruchomić kontrolę kondycji migawek, aby zidentyfikować niektóre z możliwych częstych przyczyn.

## <a name="use-the-snapshot-health-check"></a>Użyj sprawdzania kondycji migawki
Kilka typowych problemów z wynikiem Otwórz migawkę debugowania nie jest wyświetlane. Przy użyciu nieaktualnych modułu zbierającego migawki, na przykład; osiągnięcia dziennego limitu przekazywanych; lub może być migawki jest po prostu zbyt długo do przekazania. Umożliwia sprawdzanie kondycji migawki Rozwiązywanie typowych problemów.

W okienku wyjątek widoku śledzenia end-to-end, który umożliwia przejście do sprawdzania kondycji migawki znajduje się link.

![Wprowadź sprawdzenie kondycji migawki](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interfejs interaktywny, jak rozmowa szuka typowe problemy i przeprowadzi Cię rozwiązywania tych problemów.

![Kontrola kondycji](./media/snapshot-debugger/healthcheck.png)

Jeśli to nie rozwiąże problemu, zobacz Podręcznik następujące kroki rozwiązywania problemów.

## <a name="verify-the-instrumentation-key"></a>Sprawdź klucz Instrumentacji

Upewnij się, że używasz klucza Instrumentacji poprawne w opublikowanej aplikacji. Zazwyczaj klucz Instrumentacji jest do odczytu z pliku ApplicationInsights.config. Sprawdź, czy wartość jest taka sama jak klucz Instrumentacji zasobu usługi Application Insights, które pojawi się w portalu.

## <a name="preview-versions-of-net-core"></a>Wersje zapoznawcze programu .NET Core
Jeśli aplikacja korzysta z wersji zapoznawczej programu .NET Core, a Snapshot Debugger została włączona za pośrednictwem [okienka Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) w portalu, Snapshot Debugger mogą nie zostać uruchomione. Postępuj zgodnie z instrukcjami w temacie [Enable Snapshot Debugger w innych środowiskach](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby w pierwszej kolejności uwzględnić pakiet NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) z aplikacją, która jest ***również włączona w*** [okienku Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Uaktualnij do najnowszej wersji pakietu NuGet

Jeśli Snapshot Debugger został włączony [w okienku Application Insights w portalu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), aplikacja powinna mieć już uruchomiony najnowszy pakiet NuGet. Jeśli Snapshot Debugger został włączony przez dołączenie pakietu NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , użyj Menedżera pakietów NuGet programu Visual Studio, aby upewnić się, że używasz najnowszej wersji Microsoft. ApplicationInsights. SnapshotCollector. Informacje o wersji znajduje się w temacie https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Sprawdź dzienniki obiektu przekazującego

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

## <a name="troubleshooting-cloud-services"></a>Rozwiązywanie problemów z usługami w chmurze
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

## <a name="overriding-the-shadow-copy-folder"></a>Zastępowanie folder kopii w tle

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Aby znaleźć wyjątki z migawkami Użyj funkcji wyszukiwania usługi Application Insights

Po utworzeniu migawki Zgłaszanie wyjątku jest oznaczony przy użyciu identyfikatora migawki Ten identyfikator migawki jest dołączony jako właściwości niestandardowych, gdy dane telemetryczne dotyczące wyjątków jest zgłaszany do usługi Application Insights. Za pomocą **wyszukiwania** w usłudze Application Insights można znaleźć wszystkie dane telemetryczne z `ai.snapshot.id` właściwości niestandardowej.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
2. Kliknij polecenie **Wyszukaj**.
3. Typ `ai.snapshot.id` w polu tekstowym wyszukiwania i naciśnij klawisz Enter.

![Wyszukiwanie danych telemetrycznych przy użyciu Identyfikatora migawki w portalu](./media/snapshot-debugger/search-snapshot-portal.png)

Jeśli to wyszukiwanie nie zwróciło żadnych wyników, nie znaleziono migawek zostały zgłoszone w do usługi Application Insights dla aplikacji w wybranym zakresie czasu.

Aby wyszukać identyfikator określoną migawkę z dzienników obiektu przekazującego, wpisz ten identyfikator w polu wyszukiwania. Jeśli nie możesz znaleźć dane telemetryczne dla migawki, który został przekazany, wykonaj następujące kroki:

1. Należy się upewnić, że szukasz na właściwy zasób usługi Application Insights, sprawdzając klucz instrumentacji.

2. Przy użyciu sygnaturę czasową od dziennika obiektu przekazującego, Dostosuj zakres czasu filtr wyszukiwania, aby uwzględnić ten zakres czasu.

Jeśli nadal nie widzisz wyjątków o takim identyfikatorze migawki, dane telemetryczne dotyczące wyjątków nie zostało zgłoszone do usługi Application Insights. Ta sytuacja może wystąpić, jeśli aplikacja uległa awarii po zajęło migawki, ale przed zgłosił dane telemetryczne dotyczące wyjątków. W takim przypadku sprawdź dzienniki usługi App Service w ramach `Diagnose and solve problems` aby zobaczyć, czy wystąpiły nieoczekiwane ponowne uruchomienie lub nieobsługiwane wyjątki.

## <a name="edit-network-proxy-or-firewall-rules"></a>Edytowanie reguł serwera proxy lub zapory sieciowej

Jeśli aplikacja łączy się z Internetem za pośrednictwem serwera proxy lub zapora, może być konieczne edytowanie reguły, aby umożliwić aplikacji do komunikowania się z usługą rozszerzenia Snapshot Debugger. Adresy IP używane przez Snapshot Debugger są zawarte w tagu usługi Azure Monitor.
