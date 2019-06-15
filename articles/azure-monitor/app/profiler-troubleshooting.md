---
title: Rozwiązywanie problemów z usługi Azure Application Insights Profiler | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono informacje pomagające deweloperom, którzy mają problemy włączania lub za pomocą Application Insights Profiler i instrukcje rozwiązywania problemów.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6ab13e1ecd5f4825270f4b3a28251b959ad98f02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731064"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Rozwiązywanie problemów, włączanie i wyświetlanie Application Insights Profiler

## <a id="troubleshooting"></a>Rozwiązywanie ogólnych

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profile są przekazywane tylko wtedy, gdy istnieją żądania do aplikacji, gdy Profiler jest uruchomiona

Usługa Azure Application Insights Profiler zbiera dane profilowania na dwie minuty, co godzinę. Gromadzone są również danych po wybraniu **profilu teraz** znajdujący się w **skonfigurować Application Insights Profiler** okienka. Jednak dane profilowania są przekazywane tylko wtedy, gdy może być dołączane do żądania, które wystąpiły w uruchomionej Profiler. 

Profiler zapisuje komunikaty śledzenia i zdarzenia niestandardowe do zasobu usługi Application Insights. Można użyć tych zdarzeń, aby zobaczyć, jak działa Profiler. Jeśli Twoim zdaniem Profiler powinien być uruchomiony i przechwytywanie śladów, ale nie są one wyświetlane w **wydajności** okienku można sprawdzić, jak Profiler została uruchomiona:

1. Wyszukiwanie komunikatów śledzenia i zdarzenia niestandardowe, wysyłane przez Profiler do zasobu usługi Application Insights. Ten ciąg wyszukiwania umożliwia wyszukiwanie istotnych danych:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Poniższa ilustracja przedstawia dwa przykłady wyszukiwania z dwóch sztucznej Inteligencji zasobów: 
    
   * Po lewej stronie aplikacja nie jest odbierania żądań, gdy Profiler jest uruchomiona. Komunikat wyjaśniono, że przekazywanie zostało anulowane ze względu na Brak działania. 

   * Po prawej stronie Profiler pracę i wysyłane zdarzenia niestandardowe, po jego wykryciu żądań, które wystąpiły w uruchomionej Profiler. Jeśli zdarzenie niestandardowe ServiceProfilerSample jest wyświetlany, oznacza, że Profiler jest dołączony śledzenia na żądanie i możesz wyświetlić ślad **Application Insights Performance** okienka.

     Jeśli zostanie wyświetlone nie dane telemetryczne, Profiler nie jest uruchomiona. Aby rozwiązać problemy, zobacz sekcje dotyczące rozwiązywania problemów dla danego typu specyficzne dla aplikacji w dalszej części tego artykułu.  

     ![Wyszukiwanie danych telemetrycznych Profiler][profiler-search-telemetry]

1. Jeśli wystąpiły żądania, gdy uruchomiono Profiler, upewnij się, że żądania są obsługiwane przez część aplikacji, która ma Profiler włączone. Chociaż czasami aplikacje składają się z wielu składników, Profiler jest włączone dla niektórych składników. **Skonfigurować Application Insights Profiler** okienko Wyświetla składniki, które zostały przekazane dane śledzenia.

### <a name="other-things-to-check"></a>Inne czynności do wykonania
* Upewnij się, że Twoja aplikacja działa na platformie .NET Framework 4.6.
* Jeśli aplikacja sieci web znajduje się aplikacja ASP.NET Core, musi być uruchomiony co najmniej ASP.NET Core 2.0.
* Jeśli dane, które próbujesz wyświetlić jest starszy niż kilka tygodni, spróbuj ograniczyć czas filtru i spróbuj ponownie. Ślady są usuwane po upływie siedmiu dni.
* Upewnij się, że serwery proxy lub zapora nie zablokowane dostęp do https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Zliczanie w równoległych wątków podwójnej precyzji

W niektórych przypadkach metryki całkowity czas, w podglądzie stosu jest większa niż czas trwania żądania.

Taka sytuacja może wystąpić, gdy dwa lub więcej wątków są skojarzone z żądaniem i działają równolegle. W takim przypadku czas łączna liczba wątków jest większa niż czas, jaki upłynął. Jeden wątek może być oczekiwanie na inne, należy wykonać. Podgląd próbuje wykryć tę sytuację i pomija postrzegać czas oczekiwania. W ten sposób jej błędami boku wyświetlania zbyt dużej ilości informacji zamiast pominąć, co może być krytycznych informacji.

Wyświetlenie równoległych wątków w Twoje ślady określają, które wątki oczekują, dzięki czemu można ustalić ścieżkę krytyczną dla żądania. Zazwyczaj wątkiem, który szybko przechodzi w stan oczekiwania po prostu oczekuje się na inne wątki. Koncentrować się na inne wątki i Ignoruj czasu wątków oczekujących.

### <a name="error-report-in-the-profile-viewer"></a>Raport o błędach w podglądzie profilu
Prześlij zgłoszenie do pomocy technicznej w portalu. Pamiętaj uwzględniać identyfikator korelacji z komunikatu o błędzie.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Rozwiązywanie problemów z Profiler w usłudze Azure App Service
Aby uzyskać Profiler zapewnić prawidłowe działanie:
* Twój plan usługi sieci web app service musi być w warstwie podstawowa lub wyższej.
* Twoja aplikacja sieci web musi mieć włączone w usłudze Application Insights.
* Twoja aplikacja sieci web musi mieć następujące ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | klucz Instrumentacji zasobu usługi Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **ApplicationInsightsProfiler3** zadania webjob musi być uruchomiona. Aby sprawdzić zadania webjob:
   1. Przejdź do [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. W **narzędzia** menu, wybierz opcję **pulpitu nawigacyjnego zadań Webjob**.  
      **WebJobs** zostanie otwarte okienko. 
   
      ![profiler-webjob]   
   
   1. Aby wyświetlić szczegóły zadania webjob, w tym dzienniku, wybierz **ApplicationInsightsProfiler3** łącza.  
     **Ciągłe zadanie WebJob szczegóły** zostanie otwarte okienko.

      ![profiler-webjob-log]

Jeśli nie można ustalić przyczyny Profiler nielubianych przez WAS, możesz pobrać dziennik i wysłać ją do naszego zespołu w celu uzyskania pomocy. 
    
### <a name="manual-installation"></a>Instalacja ręczna

Po skonfigurowaniu Profiler, aktualizacje są wprowadzane do ustawień aplikacji sieci web. Jeśli środowisko wymaga, należy ręcznie zastosować aktualizacje. Przykładem może być, że aplikacja jest uruchomiona w środowisku aplikacji sieci Web w usłudze PowerApps. Aby ręcznie zastosować aktualizacje:

1. W **kontroli aplikacji sieci Web** otwartym okienkiem **ustawienia**.

1. Ustaw **.NET Framework w wersji** do **wersje 4.6**.

1. Ustaw **zawsze włączone** do **na**.
1. Utwórz te ustawienia aplikacji:

    |Ustawienia aplikacji    | Wartość    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | klucz Instrumentacji zasobu usługi Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Zbyt wiele aktywnych sesji profilowania

Obecnie można włączyć Profiler na maksymalnie cztery Azure web apps i miejsc wdrożenia, które działają w ramach jednego planu usług. Jeśli masz więcej niż cztery web apps — działające w planie usług w jednej aplikacji, Profiler może zgłosić *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler jest uruchamiane oddzielnie dla każdej aplikacji sieci web i próbuje uruchomić sesję śledzenie zdarzeń dla Windows (ETW) dla każdej aplikacji. Ale ograniczoną liczbę sesji ETW. może być aktywny w tym samym czasie. Jeśli zadanie webjob Profiler zgłasza zbyt wiele aktywnych sesji profilowania, należy przenieść niektórych aplikacji sieci web, do planu w innej usługi.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Błąd wdrażania: Katalog nie jest pusty "D:\\macierzystego\\witryny\\wwwroot\\App_Data\\zadania

W przypadku ponownego wdrażania aplikacji sieci web do zasobu aplikacji sieci Web, za pomocą Profiler włączone, może zostać wyświetlony następujący komunikat:

*Katalog nie jest pusty "D:\\macierzystego\\witryny\\wwwroot\\App_Data\\zadania*

Ten błąd występuje, jeśli należy uruchomić narzędzie Web Deploy ze skryptów lub w potoku wdrażania DevOps platformy Azure. Rozwiązanie to można dodać następujące parametry dodatkowe wdrożenia do zadań narzędzia Web Deploy:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Te parametry, usuń folder, który jest używany przez program Application Insights Profiler i odblokować proces ponownego wdrażania. Nie wpływają one na wystąpienie Profiler, które jest aktualnie uruchomione.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak ustalić, czy Application Insights Profiler działa?

Profiler jest uruchamiany jako ciągłe zadanie webjob w aplikacji sieci web. Możesz otworzyć zasób aplikacji sieci web w [witryny Azure portal](https://portal.azure.com). W **WebJobs** okienko, sprawdź stan **ApplicationInsightsProfiler**. Jeśli nie jest uruchomiony, otwórz **dzienniki** Aby uzyskać więcej informacji.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Rozwiązywanie problemów z Profiler i Diagnostyka Azure

>**Rozwiązane usterki w programie profiler, który jest dostarczany w WAD dla usług w chmurze.** Najnowszą wersję WAD (1.12.2.0) dla usług Cloud Services współpracuje z wszystkich najnowszych wersji zestawu SDK aplikacji usługi Insights. Hosty usług w chmurze zostanie automatycznie uaktualniona WAD, ale nie jest bezpośrednim. Aby wymusić uaktualnienia, możesz ponownie wdrożyć usługę lub ponowny rozruch węzła.

Aby sprawdzić, czy Profiler jest poprawnie skonfigurowany przez diagnostykę platformy Azure, wykonaj następujące czynności: 
1. Najpierw sprawdź, czy zawartość konfiguracji diagnostyki platformy Azure, które zostały wdrożone są, czego oczekiwać. 

1. Po drugie upewnij się, że Diagnostyka Azure przekazuje właściwego klucza iKey w wierszu polecenia Profiler. 

1. Po trzecie Sprawdź plik dziennika Profiler, aby zobaczyć, czy Profiler został uruchomiony, ale zwrócił błąd. 

Aby sprawdzić ustawienia, które były używane do konfigurowania usługi Azure Diagnostics:

1. Zaloguj się do maszyny wirtualnej (VM), a następnie otwórz plik dziennika, w tym miejscu. (Dysk może być c: lub d:, a wersja wtyczka może się różnić).

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    lub
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. W pliku, możesz wyszukać ciąg **WadCfg** można znaleźć ustawienia, które zostały przekazane do maszyny Wirtualnej, aby skonfigurować diagnostykę platformy Azure. Można sprawdzić, czy klucz instrumentacji, używane przez obiekt sink Profiler jest poprawny.

1. Sprawdź wiersz polecenia, który jest używany do uruchamiania Profiler. Argumenty, które są używane do uruchamiania Profiler znajdują się w następującym pliku. (Dysk może być c: i d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Upewnij się, że klucz Instrumentacji w wierszu polecenia Profiler jest poprawna. 

1. Przy użyciu ścieżki znalezione w poprzednim *config.json* plików, należy sprawdzić plik dziennika Profiler. Wyświetla informacje o debugowaniu, wskazującą ustawienia, które używa Profiler. Wyświetla stan i komunikaty o błędach z Profiler.  

    Jeśli Profiler jest uruchomiona, gdy aplikacja odbiera żądania, zostanie wyświetlony następujący komunikat: *Działanie wykryte z poziomu iKey*. 

    Gdy śledzenie jest przekazywany, zostanie wyświetlony następujący komunikat: *Rozpocznij przekazywanie śledzenia*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








