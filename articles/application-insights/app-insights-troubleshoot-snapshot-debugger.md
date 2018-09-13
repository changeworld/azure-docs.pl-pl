---
title: Usługi Azure Application Insights Snapshot Debugger, w przewodniku rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure Application Insights Snapshot Debugger.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 285f42a6b52819077b92abce78c1f51756780604
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648871"
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Rozszerzenia Snapshot Debugger: Przewodnik rozwiązywania problemów

Application Insights Snapshot Debugger umożliwia automatycznego zbierania migawek debugowania z poziomu aplikacji sieci web. Migawka przedstawia stan zmiennych i kod źródłowy w momencie utworzenia Wystąpił wyjątek. W tym artykule wyjaśniono, jak debuger działa i zapewnia rozwiązania typowych problemów.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Jak działa rozszerzenie Application Insights Snapshot Debugger

Application Insights Snapshot Debugger jest częścią potoku danych telemetrycznych usługi Application Insights (wystąpienia ITelemetryProcessor). Modułu zbierającego migawki monitoruje zarówno wyjątki zgłaszane w kodzie (AppDomain.FirstChanceException), jak i dane telemetryczne dotyczące wyjątków zgłoszonych w usłudze Application Insights za pośrednictwem `TelemetryClient.TrackException`. Gdy pakiet modułu zbierającego migawki zostało pomyślnie dodane do projektu i wykrył jedno zdarzenie w potoku telemetrii zdarzenia niestandardowego o nazwie "AppInsightsSnapshotCollectorLogs" i "SnapshotCollectorEnabled" w danych niestandardowych będą wysyłane. W tym samym czasie, zostanie uruchomiony w procesie zwanym "SnapshotUploader.exe" (wersji 1.2.0 lub nowszej) lub "MinidumpUploader.exe" (przed wersji 1.2.0 lub nowszej), można przekazać zebrane migawki plików danych do usługi Application Insights.  Podczas uruchamiania procesu obiektu przekazującego, wysyła zdarzenie niestandardowe o nazwie "UploaderStart". Po utworzeniu tego modułu zbierającego migawki staną się normalne działanie monitorowania.

Podczas monitorowania modułu zbierającego migawki telemetria wyjątków usługi Application Insights używa parametrów (na przykład ThresholdForSnapshotting MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) zdefiniowanych w Konfiguracja do określenia, kiedy zbiera dane migawki. Po spełnieniu wszystkich reguł, moduł zbierający będzie żądać migawki, dla wyjątku dalej w tym samym miejscu. Jednocześnie zdarzenie niestandardowe usługi Application Insights o nazwie "AppInsightsSnapshotCollectorLogs" i "RequestSnapshots" będą wysyłane. Ponieważ kompilator będzie optymalizacji kodu "Release", zmienne lokalne może nie być widoczne w zebrane migawki. Modułu zbierającego migawki zostanie podjęta próba deoptimize metodę, która zgłosiła wyjątek podczas żądania migawki. W tym czasie będą wysyłane zdarzenia usługi Application Insights niestandardowej o nazwie "AppInsightsSnapshotCollectorLogs" i "ProductionBreakpointsDeOptimizeMethod" w danych niestandardowych.  Po zebraniu migawki dalej wyjątku, zmienne lokalne będą dostępne. Po zebraniu migawki, będzie on wklejeniu kodu. 

> [!NOTE]
> Deoptimization wymaga rozszerzenia witryny usługi Application Insights do zainstalowania.

Zleconą migawki dla określonego wyjątku modułu zbierającego migawki rozpocznie się monitorowanie wyjątków aplikacji obsługi potoku (AppDomain.FirstChanceException). Jeśli wyjątek wystąpi ponownie, moduł zbierający zostanie uruchomiony, migawki (niestandardowego zdarzenia usługi Application Insights o nazwie "AppInsightsSnapshotCollectorLogs" i "SnapshotStart" w danych niestandardowych). Wykonano kopię w tle uruchomionego procesu, a następnie (będzie można zduplikować tabeli stron). To zwykle potrwa 10 do 20 MS. Po tym niestandardowego zdarzenia usługi Application Insights o nazwie "AppInsightsSnapshotCollectorLogs" i "SnapshotStop" w danych niestandardowych będą wysyłane. Po utworzeniu procesu rozwidlonego całkowita pamięć stronicowana zostanie zwiększona o jednakową kwotę jako stronicowanej pamięci w procentach uruchomionej aplikacji (zestaw roboczy będzie znacznie mniejszy). Po uruchomieniu proces aplikacji zazwyczaj kopie w tle pamięć procesu zostanie zrzucany na dysku i przekazany do usługi Application Insights. Po przekazaniu migawki powoduje wysłanie niestandardowego zdarzenia usługi Application Insights o nazwie "UploadSnapshotFinish".

## <a name="is-the-snapshot-collector-working-properly"></a>Modułu zbierającego migawki działa poprawnie?

### <a name="how-to-find-snapshot-collector-logs"></a>Jak znaleźć dzienników modułu zbierającego migawki
Migawka modułu zbierającego dzienniki są wysyłane do konta usługi Application Insights, jeśli [pakietu NuGet modułu zbierającego migawki](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) jest wersji 1.1.0 lub nowszej. Upewnij się, że *ProvideAnonymousTelemetry* nie jest ustawiony na wartość false (ma wartość true domyślnie).

* Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
* Kliknij przycisk *wyszukiwania* w sekcji Przegląd.
* Wprowadź następujący ciąg w polu wyszukiwania:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Uwaga: zmiana *zakres czasu* w razie potrzeby.

![Zrzut ekranu z wyszukiwania migawki modułu zbierającego dzienniki](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Przeanalizuj dzienniki modułu zbierającego migawki
Podczas wyszukiwania dzienników modułu zbierającego migawki, należy "UploadSnapshotFinish" zdarzeń w zakresie docelowego czasu. Jeśli nadal nie widzisz przycisk "Otwórz migawkę debugowania", aby otworzyć migawki, Wyślij wiadomość e-mail do snapshothelp@microsoft.com za pomocą usługi klucz Instrumentacji usługi Application Insights.

![Zrzut ekranu Sprawdź dzienniki modułu zbierającego migawki](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Nie można odnaleźć migawki do otwarcia
Jeśli poniższe kroki nie pomocne w rozwiązaniu problemu, Wyślij wiadomość e-mail do snapshothelp@microsoft.com o następującym kluczu Instrumentacji usługi Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Krok 1: Upewnij się, że Twoja aplikacja wysyła dane telemetryczne i dane o wyjątkach do usługi Application Insights
Przejdź do zasobu usługi Application Insights, sprawdź, czy dane wysyłane z aplikacji.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Krok 2: Upewnij się, że poprawnie dodany modułu zbierającego migawki do potoku Telemetria usługi Application Insights w swojej aplikacji
Jeśli dzienniki można znaleźć w kroku "Jak znaleźć dzienników modułu zbierającego migawki", modułu zbierającego migawki jest poprawnie dodane do projektu i możesz zignorować ten krok.

Jeśli nie ma żadnych dzienników modułu zbierającego migawki, sprawdź następujące informacje:
* Dla klasycznych aplikacji programu ASP.NET, Wyszukaj ten wiersz *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* w *plik ApplicationInsights.config* pliku.

* W przypadku aplikacji platformy ASP.NET Core, upewnij się, *ITelemetryProcessorFactory* z *SnapshotCollectorTelemetryProcessor* jest dodawany do *IServiceCollection* usług .

* Również Sprawdź, czy używasz klucza Instrumentacji poprawne w opublikowanej aplikacji.

* Modułu zbierającego migawki nie obsługuje wielu kluczy Instrumentacji w ramach jednej aplikacji, wyśle migawek na klucz Instrumentacji pierwszy wyjątek, który obserwuje go.

* Jeśli ustawisz *InstrmentationKey* ręcznie w kodzie, należy zaktualizować *InstrumentationKey* elementu z *plik ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Krok 3: Upewnij się, że przekazujesz minizrzutu jest uruchomiona.
W dziennikach modułu zbierającego migawki, wyszukaj *UploaderStart* (w polu tekstowym wyszukiwania wpisz UploaderStart). Powinna to być zdarzenia modułu zbierającego migawki monitorowane pierwszy wyjątek. Jeśli to zdarzenie nie istnieje, sprawdź inne dzienniki, aby uzyskać szczegółowe informacje. Jednym ze sposobów z możliwych do rozwiązywania tego problemu jest uruchamiany ponownie aplikację.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Krok 4: Upewnij się, że modułu zbierającego migawki wyrażone jego przeznaczenie, aby zebrać migawki
W dziennikach modułu zbierającego migawki, wyszukaj *RequestSnapshots* (typ ```RequestSnapshots``` w polu tekstowym wyszukiwania).  Jeśli nie ma żadnego, sprawdź konfigurację. Na przykład *ThresholdForSnapshotting*, który wskazuje liczbę określony wyjątek, który może wystąpić przed rozpoczęciem zbierania migawek.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Krok 5: Upewnij się, czy migawki nie jest wyłączona z powodu ochrona pamięci
Aby chronić wydajność aplikacji, migawki będzie tylko można przechwycić, gdy jest bufor pamięci dobre. W dziennikach modułu zbierającego migawki Wyszukaj "CannotSnapshotDueToMemoryUsage". W danych niestandardowych zdarzeń będzie miał szczegółowe przyczyny. Jeśli aplikacja jest uruchomiona w usłudze Azure Web App, ograniczenie może być strict. Ponieważ aplikacja internetowa platformy Azure spowoduje ponowne uruchomienie aplikacji, gdy są spełnione pewne zasady pamięci. Możesz skalować w górę planu usługi do maszyny z większej ilości pamięci, aby rozwiązać ten problem.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Krok 6: Upewnij się, że zostały przechwycone migawek
W dziennikach modułu zbierającego migawki, wyszukaj ```RequestSnapshots```.  Jeśli żaden nie jest obecny, sprawdź konfigurację. Na przykład *ThresholdForSnapshotting*, który wskazuje liczbę określonego wyjątku przed zebraniem migawki.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Krok 7: Upewnij się, że migawki są przekazywane poprawnie
W dziennikach modułu zbierającego migawki, wyszukaj ```UploadSnapshotFinish```.  Jeśli to nie jest obecny, Wyślij wiadomość e-mail do snapshothelp@microsoft.com o następującym kluczu Instrumentacji usługi Application Insights. Jeśli takie zdarzenie istnieje, Otwórz, jednym z dzienników, a następnie skopiuj wartość "SnapshotId" w danych niestandardowych. Następnie wyszukaj wartość. Spowoduje to wyszukanie wyjątków odpowiadający migawki. Następnie kliknij przycisk wyjątków i Otwórz migawkę debugowania. (Jeśli nie ma odpowiedniego wyjątku, dane telemetryczne dotyczące wyjątków próbkować można z powodu dużych ilościach. Wypróbuj inną snapshotId).

![Zrzut ekranu przedstawiający SnapshotId Znajdź](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Zrzut ekranu przedstawiający otwarte wyjątku](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Zrzut ekranu Otwórz migawkę debugowania](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Zmienne lokalne widoku migawki nie zostały zakończone

Brakuje niektórych zmiennych lokalnych. Jeśli aplikacja jest uruchomiona wersja kodu, kompilator zoptymalizuje niektóre zmienne natychmiast. Na przykład:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Jeśli Twoje przypadek jest inny, może wskazywać usterkę. Wyślij wiadomość e-mail do snapshothelp@microsoft.com o następującym kluczu Instrumentacji usługi Application Insights wraz z fragmentu kodu.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Wyświetl migawkę: Nie można uzyskać wartości lokalnej zmiennej lub argumentu
Upewnij się, że [rozszerzenie witryny usługi Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) jest zainstalowany. Jeśli problem będzie się powtarzać, Wyślij wiadomość e-mail do snapshothelp@microsoft.com o następującym kluczu Instrumentacji usługi Application Insights.
