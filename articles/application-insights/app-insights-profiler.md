---
title: Profil aplikacji sieci web na platformie Azure za pomocą profilera Insights aplikacji | Dokumentacja firmy Microsoft
description: Określ ścieżkę aktywną w kodzie serwera sieci web z profilera niskiego poziomu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 34824401ec8d21949c5c5036a11197a09e240bd7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil aplikacji sieci web platformy Azure na żywo za pomocą usługi Application Insights

*Ta funkcja Azure Application Insights jest ogólnie dostępna dla funkcji aplikacji sieci Web w usłudze Azure App Service i w zasoby obliczeniowe systemu Azure w wersji zapoznawczej. Aby uzyskać informacje dotyczące [Użyj profilera lokalnymi](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).*

W tym artykule omówiono ilość czasu poświęcana w każdej metodzie aplikacji sieci web na żywo, gdy używasz [usługi Application Insights](app-insights-overview.md). Narzędzia Application Insights profilera Wyświetla szczegółowe profile bieżących żądań, które zostały obsłużone przez aplikację. Wyróżnia profilera *aktywnej ścieżki* używającą najwięcej czasu. Żądania z różnych czasów odpowiedzi są profilowane na podstawie próbki. Za pomocą różnych technik, można zminimalizować koszty, który został skojarzony z aplikacją.

Profiler jest obecnie obsługiwane dla aplikacji sieci web ASP.NET i ASP.NET Core uruchomionych aplikacji sieci Web. Podstawowe warstwę usługi lub nowszego jest wymagane do używania profilera.

## <a id="installation"></a> Włącz profilera do aplikacji sieci web aplikacji sieci Web
Jeśli już zostały opublikowane w aplikacji sieci web aplikacji, ale nie wykonano żadnych czynności w kodzie źródłowym, aby użyć usługi Application Insights, wykonaj następujące czynności:
1. Przejdź do **usługi aplikacji** okienku w portalu Azure.
2. W obszarze **monitorowanie**, wybierz pozycję **usługi Application Insights**, a następnie albo postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób, lub wybierz istniejący zasób usługi Application Insights do monitorowania sieci web aplikacja.

   ![Włącz Insights aplikacji w portalu usługi aplikacji][appinsights-in-appservices]

3. Jeśli masz dostęp do kodu źródłowego projektu, [instalacji usługi Application Insights](app-insights-asp-net.md).  
   Jeśli jest już zainstalowany, upewnij się, że masz najnowszą wersję. Aby wyszukać najnowszą wersję, w Eksploratorze rozwiązań kliknij projekt prawym przyciskiem myszy, a następnie wybierz **pakiety zarządzania pakietami NuGet** > **aktualizacje** > **Aktualizuj wszystkie pakiety**. Następnie można wdrożyć aplikację.

Aplikacje platformy ASP.NET Core wymagają zainstalowania 2.1.0-beta6 pakietu Microsoft.ApplicationInsights.AspNetCore NuGet lub nowszej do pracy z profilera. Począwszy od 27 czerwca 2017 wcześniejszych wersji nie są obsługiwane.

1. W [portalu Azure](https://portal.azure.com), otwórz zasobu usługi Application Insights dla aplikacji sieci web. 
2. Wybierz **wydajności** > **włączyć Application Insights profilera**.

   ![Zaznacz opcję Włącz transparent profilera][enable-profiler-banner]

3. Alternatywnie można wybrać **profilera** konfigurację, aby wyświetlić stan i włączanie lub wyłączanie profilera.

   ![Wybierz konfigurację profilera][performance-blade]

   Aplikacje sieci Web, które są skonfigurowane przy użyciu usługi Application Insights są wymienione w **profilera** okienko konfiguracji. Po wykonaniu powyższych kroków powinien być zainstalowany agent profilera. 

4. W **profilera** okienko konfiguracji, wybierz opcję **włączyć profilera**.

5. W razie potrzeby postępuj zgodnie z instrukcjami, aby zainstalować agenta programu Profiler. Jeśli żadne aplikacje sieci web zostały skonfigurowane przy użyciu usługi Application Insights, wybierz **Dodaj aplikacje połączone**.

   ![Skonfiguruj opcje okienka][linked app services]

W przeciwieństwie do aplikacji sieci web, które znajdują się za pomocą planów aplikacji sieci Web aplikacje, które znajdują się w zasoby obliczeniowe systemu Azure (na przykład, maszynach wirtualnych platformy Azure, zestawy skalowania maszyny wirtualnej, sieć szkieletowa usług Azure lub usługi w chmurze Azure) nie są bezpośrednio zarządzane przez usługę Azure. W takim przypadku jest żadnej aplikacji sieci web, aby połączyć. Zamiast łącze do aplikacji, wybierz **włączyć profilera** przycisku.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Włącz profilera dla zasobów obliczeniowych Azure (wersja zapoznawcza)

Aby uzyskać informacje, zobacz [wersji zapoznawczej profilera zasoby obliczeniowe systemu Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Wyświetlanie danych profilera

Upewnij się, że aplikacja odbiera ruch. Jeśli przeprowadzasz eksperyment, można wygenerować żądania do używania aplikacji sieci web [test wydajności, Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Jeśli włączono nowo profilera, można uruchomić testu obciążenia krótkich przez 15 minut, które powinien wygenerować ślady profilera. Gdyby profilera już włączone przez pewien czas uruchamia keep pamiętać, że Profiler losowo uruchamia dwa razy co godzinę i czas trwania dwie minuty każdej aktualizacji. Zalecamy najpierw uruchamiania testu obciążenia przez jedną godzinę do upewnij się, że możesz uzyskać przykładowe ślady profilera.

Gdy aplikacja otrzyma część ruchu, przejdź do **wydajności** okienku wybierz **wykonać akcje** wyświetlić ślady profilera, a następnie wybierz **ślady profilera** przycisku.

![Śledzi profilera aplikacji wydajności wgląd w okienku podglądu][performance-blade-v2-examples]

Wybierz próbki, aby wyświetlić podział poziomie kodu czas wykonania żądania.

![Eksplorator śledzenia usługi Application Insights][trace-explorer]

W Eksploratorze śledzenia zawiera następujące informacje:

* **Pokaż ścieżkę aktywną**: Otwiera pośrednictwem powiązanych punktów sprzedaży liść węzła lub co najmniej coś zamknąć. W większości przypadków ten węzeł jest sąsiadujące wąskie gardło.
* **Etykieta**: nazwę funkcji lub zdarzeń. W drzewie wyświetlany zarówno kod i zdarzenia (takie jak zdarzenia SQL i HTTP). Górny zdarzeń reprezentuje całkowity czas trwania żądania.
* **Upłynął**: interwał czasu rozpoczęcia operacji i koniec operacji.
* **Gdy**: czas po funkcji lub zdarzeń była uruchomiona w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak można odczytać danych wydajności

Profilera usługi firmy Microsoft używa kombinacji metod próbkowania i instrumentacji do analizowania wydajności aplikacji. Szczegółowe kolekcji jest w toku, profilera usługi przykłady wskaźnik instrukcji procesora każdej maszynie co milisekund. Każda próbka przechwytuje stosu wywołań zakończenie wątku, który jest aktualnie wykonywany. Udostępnia szczegółowe informacje na temat tego wątku został czynności, zarówno na wysokim poziomie, jak i na niskim poziomie abstrakcji. Usługa profiler zbiera również inne zdarzenia, aby śledzić korelacji działania i przyczynowości, łącznie z kontekstem przełączania zdarzenia, zdarzenia zadania biblioteki równoległych (TPL) oraz zdarzenia puli wątków.

Stos wywołań, który jest wyświetlany w widoku osi czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje stosu wywołań zakończenie wątku, zawiera kod z programu Microsoft .NET Framework i innych platform, które możesz odwoływać się do.

### <a id="jitnewobj"></a>Obiekt alokacji (clr! JIT\_nowy lub clr! JIT\_Newarr1)
**CLR! JIT\_nowy** i **clr! JIT\_Newarr1** są funkcje pomocnicze w programie .NET Framework, które przydzielić pamięci ze sterty zarządzanej. **CLR! JIT\_nowy** jest wywoływane, gdy obiekt jest przydzielony. **CLR! JIT\_Newarr1** jest wywoływane, gdy przydzielone jest Tablica obiektów. Te dwie funkcje są zazwyczaj szybkiego i względnie niewielkich ilości czasu. Jeśli widzisz **clr! JIT\_nowy** lub **clr! JIT\_Newarr1** trwać dość czasu na osi czasu, go wskazuje, że kod może być Alokacja wiele obiektów i wykorzystywanie znacznych ilości pamięci.

### <a id="theprestub"></a>Kod ładujący (clr! ThePreStub)
**CLR! ThePreStub** jest funkcją pomocnika w programie .NET Framework, który przygotowuje kod do wykonania po raz pierwszy. To zwykle obejmuje, ale nie jest ograniczona do kompilacji just-in-time (JIT). W przypadku każdego C# metody **clr! ThePreStub** powinna być wywoływana co najwyżej raz przez cały okres istnienia procesu.

Jeśli **clr! ThePreStub** trwa dość czasu dla żądania, oznacza to, że żądanie jest pierwszą, która wykonuje tej metody. Czas dla środowiska uruchomieniowego .NET Framework załadować pierwsza metoda jest znacząca. Należy rozważyć przy użyciu procesu rozgrzewania, który wykonuje część kod przed użytkowników do niego dostęp, lub rozważ użycie polecenia Generator obrazu natywnego (ngen.exe) z zestawów.

### <a id="lockcontention"></a>Zablokuj rywalizacji (clr! JITutil\_MonContention lub clr! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** lub **clr! JITutil\_MonEnterWorker** wskazuje, że bieżący wątek oczekuje na zwolnienie blokady. Ten tekst jest zwykle wyświetlany podczas wykonywania C# **blokady** instrukcji, podczas wywoływania **Monitor.Enter** metody, lub podczas wywoływania metody z **MethodImplOptions.Synchronized** atrybutu. Rywalizacji blokad zwykle występuje, gdy wątek _A_ uzyskuje blokady i wątku _B_ próbuje uzyskać blokady tego samego przed wątku _A_ zwolnieniem.

### <a id="ngencold"></a>Kod ładujący ([chłodni])
Jeśli nazwa metody zawiera **[ZIMNYCH]**, takich jak **mscorlib.ni! [COLD]system.Reflection.CustomAttribute.IsDefined**, środowiska uruchomieniowego .NET Framework jest wykonywanie kodu po raz pierwszy, który nie jest zoptymalizowana przez <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">Optymalizacja sterowana profilem</a>. Dla każdej metody powinien zostać wyświetlony co najwyżej raz przez cały okres istnienia procesu.

Jeśli kod ładowania przyjmuje rozległe czas na żądanie, oznacza to, że żądanie jest pierwsza z nich można wykonać zoptymalizowanego część metody. Należy rozważyć użycie procesem rozgrzewania część kodu jest wykonywana przed użytkowników do niego dostęp.

### <a id="httpclientsend"></a>Wysyłanie żądania HTTP
Metod, takich jak **HttpClient.Send** wskazują, że kod czeka na żądania HTTP.

### <a id="sqlcommand"></a>Operacja bazy danych
Metod, takich jak **SqlCommand.Execute** wskazują, że kod czeka na zakończenie operacji bazy danych.

### <a id="await"></a>Oczekiwanie (AWAIT\_czasu)
**AWAIT\_czasu** wskazuje, że kod czeka na zakończenie innego zadania. Zazwyczaj dzieje się to z języka C# **AWAIT** instrukcji. Jeśli kod jest C# **AWAIT**, wątek cofa i zwraca sterowania do puli wątków i nie nie wątku, który jest zablokowana i czeka na **AWAIT** aby zakończyć. Jednak logicznie, wątek który zostało **AWAIT** "zablokowaniu" i oczekuje na zakończenie operacji. **AWAIT\_czasu** instrukcji wskazuje zablokowanych czasu oczekiwania na zakończenie zadania.

### <a id="block"></a>Czas blokowania
**BLOCKED_TIME** wskazuje, czy kod jest oczekiwanie na inne zasoby, które mają być dostępne. Na przykład może być oczekiwanie obiektu synchronizacji, wątek był dostępny lub do zakończenia żądania.

### <a id="cpu"></a>Czas procesora CPU
Procesor był zajęty wykonywaniem instrukcji.

### <a id="disk"></a>Czas dysku
Aplikacja wykonuje operacje dysku.

### <a id="network"></a>Czas sieci
Aplikacja jest przeprowadzanie operacji sieciowych.

### <a id="when"></a>Gdy kolumny
**Podczas** kolumna jest wizualizację jak włącznie przykłady zbieranych dla węzła w zależności od wraz z upływem czasu. Łączny zakres żądania jest podzielona na 32 przedziałów czasu. Wraz z wartościami granicznymi przykłady dla tego węzła jest zebranych w tych zasobników 32. Zasobnik każdy jest reprezentowany jako pasek. Wysokość paska reprezentuje skalowaną wartość. Dla węzłów, które są oznaczone jako **CPU_TIME** lub **BLOCKED_TIME**, lub w przypadku relacji widocznych na korzystanie z zasobów (na przykład procesora CPU, dysku lub wątek), pasek reprezentuje użycie jednego z zasoby w okresie tego zasobnika. Dla tych metryk jest można uzyskać wartość większa niż 100 procent za korzystanie z wielu zasobów. Na przykład jeśli używasz, średnio dwa procesory interwale get 200 procent.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi pięć dni. Maksymalna dane, które jest pozyskanych na dzień wynosi 10 GB.

Nie ma żadnych opłat za korzystanie z usługi profilera. Można użyć usługi profilera, aplikacji sieci web musi być obsługiwana w warstwie podstawowej aplikacji sieci Web.

## <a name="overhead-and-sampling-algorithm"></a>Koszty i algorytm pobierania próbek

Profiler losowo uruchamia dwie minuty co godzinę na każdej maszynie wirtualnej hostującego aplikację, która ma włączone do przechwytywania danych śledzenia profilera. Kiedy Profiler jest uruchomiona, dodaje od 5 do 15 procent obciążenie Procesora na serwerze.

Więcej serwerów, które są dostępne do hostowania aplikacji, mniej wpływ profilera ma na ogólną wydajnością. Jest to spowodowane powoduje algorytm próbkowania profilera systemem tylko 5 procent serwerów, w dowolnym momencie. Więcej serwerów dostępnych do obsługi żądań sieci web do przesunięcia spowodowane przez uruchomione profilera obciążenie serwera.

## <a name="disable-profiler"></a>Wyłącz narzędzie Profiler
Zatrzymaj lub ponownego uruchomienia profilera dla poszczególnych sieci web wystąpienia aplikacji, w obszarze **zadania Web Job**, przejdź do zasobów aplikacji sieci Web. Aby usunąć profilera, przejdź do **rozszerzenia**.

![Wyłączanie profilera dla zadanie sieci web][disable-profiler-webjob]

Zaleca się, że Profiler włączona na wszystkich aplikacji sieci web, aby odnaleźć wszystkie problemy z wydajnością możliwie jak najszybciej.

Pakiet WebDeploy można używać do wdrażania zmian do aplikacji sieci web, upewnij się, Wyklucz w folderze App_Data przed usunięciem podczas wdrażania. W przeciwnym razie rozszerzenia profilera pliki zostaną usunięte przy następnym możesz wdrożyć aplikację sieci web na platformie Azure.


## <a id="troubleshooting"></a>Rozwiązywanie problemów

### <a name="too-many-active-profiling-sessions"></a>Zbyt wiele aktywnych sesji profilowania

Obecnie można włączyć profilera na maksymalnie cztery aplikacji sieci web platformy Azure i miejsc wdrożenia, które działają w tym samym planie usługi. Jeśli zadanie sieci web profilera zgłoszenie zbyt wiele aktywnych sesji profilowania, Przenieś niektórych aplikacji sieci web do planu innej usługi.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak ustalić, czy Application Insights Profiler jest uruchomiona?

Profiler jest uruchamiany jako zadanie web ciągły w aplikacji sieci web. Można otworzyć zasobu aplikacji sieci web w [portalu Azure](https://portal.azure.com). W **Webjob** okienka, sprawdź stan **ApplicationInsightsProfiler**. Jeśli nie jest uruchomiony, otwórz **dzienniki** Aby uzyskać więcej informacji.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Dlaczego nie można odnaleźć przykładami stosu, mimo że Profiler jest uruchomiona?

Oto kilka rzeczy, które można sprawdzić:

* Upewnij się, że planu usługi aplikacji sieci web jest warstwy podstawowa lub nowszej.
* Upewnij się, że aplikacja sieci web ma Application Insights SDK 2.2 Beta lub później włączone.
* Upewnij się, że aplikacja sieci web ma **APPINSIGHTS_INSTRUMENTATIONKEY** ustawieniem skonfigurowanym mających taki sam klucz instrumentacji, która jest używana przez zestaw SDK usługi Application Insights.
* Upewnij się, że aplikacja sieci web jest uruchomiona na .NET Framework 4.6.
* Sprawdź, czy aplikacja platformy ASP.NET Core aplikacji sieci web, [wymaganych zależności](#aspnetcore).

Po rozpoczęciu Profiler jest okres rozgrzewania krótkie, podczas którego Profiler zbiera aktywnie kilka śledzenia wydajności. Po wykonaniu tej Profiler zbiera dane śledzenia wydajności przez dwie minuty co godzinę.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Został przy użyciu profilera usługi platformy Azure. Co się stało go?

Po włączeniu Application Insights profilera agentów profiler usługę Azure jest wyłączona.

### <a id="double-counting"></a>Zliczanie w równoległych wątków o podwójnej precyzji

W niektórych przypadkach w podglądzie stosu Metryka całkowity czas jest większy niż czas trwania żądania.

Taka sytuacja może wystąpić, gdy dwa lub więcej wątków są skojarzone z żądaniem i działają równolegle. W takim przypadku wątku całkowity czas jest większy niż czas, który upłynął. Jeden wątek może być oczekiwanie na drugi, należy wykonać. Podgląd komunikatów o próbuje wykryć i pomija postrzegać czas oczekiwania, ale jego errs boku Wyświetlanie zbyt dużej ilości informacji zamiast pominąć, co może być ważnych informacji.

Po wyświetleniu równoległych wątków w dane śledzenia należy ustalić, które wątków oczekujących, można ustalić ścieżkę krytyczną dla żądania. W większości przypadków szybko przechodzi w stan oczekiwania wątku jest po prostu oczekiwanie na inne wątki. Skoncentrować się na inne wątki i Ignoruj, czas w wątków oczekujących.

### <a id="issue-loading-trace-in-viewer"></a>Nie danych profilowania

Oto kilka rzeczy, które można sprawdzić:

* Jeśli dane, które próbujesz wyświetlić, jest starsza niż kilka tygodni, spróbuj ograniczyć czas filtru i spróbuj ponownie.
* Upewnij się, nie ma serwerów proxy lub zapory zablokował dostęp do https://gateway.azureserviceprofiler.net.
* Upewnij się, że klucz Instrumentacji usługi Application Insights, używanym w aplikacji jest taka sama jak zasobu usługi Application Insights, który został użyty do profilowania włączone. Klucz zazwyczaj znajduje się w pliku ApplicationInsights.config, ale można ją również w pliku web.config lub app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Raport o błędach w podglądzie profilowania

Przedstawia biletu pomocy technicznej w portalu. Należy uwzględnić identyfikator korelacji z komunikat o błędzie.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Błąd wdrażania: katalog nie jest pusty "D:\\macierzystego\\lokacji\\wwwroot\\App_Data\\zadań

Jeśli są ponownego wdrażania aplikacji sieci web do zasobów aplikacji sieci Web z profilera włączone, mogą pojawić następujący komunikat:

*Katalog nie jest pusty "D:\\macierzystego\\lokacji\\wwwroot\\App_Data\\zadań*

Ten błąd występuje podczas uruchamiania narzędzia Web Deploy ze skryptów lub z potoku wdrożenia usług Team w usłudze Visual Studio. Rozwiązanie to dodaj następujące parametry dodatkowe wdrożenia do zadań narzędzia Web Deploy:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Te parametry Usuń folder, który jest używany przez Profiler Insights aplikacji i odblokować proces ponownego wdrażania. Nie wpływają one na wystąpienie profilera, które jest aktualnie uruchomione.


## <a name="manual-installation"></a>Instalacja ręczna

Po skonfigurowaniu profilera uaktualnienia odnoszą się do ustawień aplikacji sieci web. Należy ręcznie zastosować aktualizacje, jeśli dane środowisko wymaga go. Przykładem może być, że aplikacja działa w środowisku aplikacje sieci Web dla rozwiązania PowerApps.

1. W **kontroli aplikacji sieci Web** okienku Otwórz **ustawienia**.
2. Ustaw **.Net Framework w wersji** do **4.6**.
3. Ustaw **zawsze włączone** do **na**.
4. Dodaj **APPINSIGHTS_INSTRUMENTATIONKEY** aplikacji, ustawienia i ustaw wartość na taki sam klucz instrumentacji, która jest używana przez zestaw SDK.
5. Otwórz **zaawansowane narzędzia**.
6. Wybierz **Przejdź** otworzyć Kudu witryny sieci Web.
7. W witrynie sieci Web Kudu, wybierz **lokacji rozszerzenia**.
8. Zainstaluj **usługi Application Insights** z galerii aplikacji sieci Web platformy Azure.
9. Uruchom ponownie aplikację sieci web.

## <a id="profileondemand"></a> Ręcznie uruchomić profilera
Podczas opracowywania Profiler, dodane interfejsu wiersza polecenia, aby firma Microsoft można testować profilera na usługi aplikacji. Przy użyciu tego samego interfejsu, użytkowników można dostosować sposób uruchamiania profilera. Na wysokim poziomie profilera wykorzystuje System Kudu aplikacji sieci Web do zarządzania, profilowania w tle. Po zainstalowaniu rozszerzenie usługi Application Insights, utworzymy zadanie sieci web ciągłego obsługującego profilera. Używamy tej samej technologii, aby utworzyć nowe zadanie sieci web, którą można dostosować do własnych potrzeb.

W tej sekcji opisano sposób:

* Utwórz zadanie sieci web, które można uruchomić profilera dwie minuty o naciśnięcie przycisku.
* Utwórz zadanie sieci web, które można zaplanować Profiler do uruchomienia.
* Ustaw argumentów profilera.


### <a name="set-up"></a>Konfigurowanie
Po pierwsze zapoznaj się z pulpitu nawigacyjnego zadanie sieci web. W obszarze **ustawienia**, wybierz pozycję **Webjob** kartę.

![Blok zadań webjob](./media/app-insights-profiler/webjobs-blade.png)

Jak widać, ten pulpit nawigacyjny Wyświetla wszystkie zadania sieci web, które są aktualnie zainstalowane w lokacji. Widać ApplicationInsightsProfiler2 zadanie sieci web, który ma uruchomione zadanie profilera. Jest to, gdzie utworzymy nowe zadania sieci web ręczne i zaplanowane profilowania.

Aby uzyskać dane binarne, które są potrzebne, wykonaj następujące czynności:

1.  Na stronie Kudu na **narzędzi programistycznych** wybierz opcję **zaawansowane narzędzia** Kudu logo, a następnie wybierz **Przejdź**.  
   Otwiera nową lokację, a użytkownik jest zalogowany automatycznie.
2.  Aby pobrać pliki binarne profilera, przejdź do Eksploratora plików za pomocą **konsoli debugowania** > **CMD**, który znajduje się w górnej części strony.
3.  Wybierz **lokacji** > **wwwroot** > **App_Data** > **zadania**  >   **Ciągłe**.  
   Powinny pojawić się folder o nazwie *ApplicationInsightsProfiler2*. 
4. Po lewej stronie folderu, wybierz **Pobierz** ikony.  
   Ta akcja pobiera *ApplicationInsightsProfiler2.zip* pliku. Firma Microsoft zaleca utworzenie wyczyścić katalogu można przenieść tego archiwum zip do.

### <a name="setting-up-the-web-job-archive"></a>Konfigurowanie zadania archiwum sieci web
Po dodaniu nowego zadania sieci web do witryny sieci Web Azure zasadniczo utworzyć archiwum zip z *run.cmd* pliku wewnątrz. *Run.cmd* pliku informuje system zadanie sieci web, co należy zrobić, gdy zostanie uruchomione zadanie sieci web.

1.  Utwórz nowy folder (na przykład *RunProfiler2Minutes*).
2.  Skopiuj pliki z wyodrębnionego *ApplicationInsightProfiler2* folderu do tego nowego folderu.
3.  Utwórz nową *run.cmd* pliku.  
    Dla wygody można otworzyć folderu roboczego w programie Visual Studio Code przed rozpoczęciem.
4.  W pliku, należy dodać polecenie `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. Polecenia zostały opisane w następujący sposób:

    * `start`: Określa, że Profiler do uruchomienia.  
    * `--engine-mode immediate`: Określa, że Profiler można rozpocząć profilowania natychmiast.  
    * `--single`: Określa, że Profiler do uruchomienia, a następnie zatrzymać automatycznie.  
    * `--immediate-profiling-duration 120`: Określa, że Profiler do uruchomienia przez 120 sekund lub 2 minuty.

5.  Zapisz zmiany.
6.  Archiwum folder prawym przyciskiem myszy, a następnie wybierając **przesyłają** > **skompresowanego folderu (zip)**.  
   Ta akcja tworzy plik zip, który używa nazwy folderu.

![Uruchom polecenia profilera](./media/app-insights-profiler/start-profiler-command.png)

Został utworzony plik zip zadanie sieci web, który służy do konfigurowania zadania sieci web w witrynie.

### <a name="add-a-new-web-job"></a>Dodaj nowe zadanie sieci web
W tej sekcji możesz dodać nowe zadanie sieci web w witrynie. Poniższy przykład pokazuje, jak dodać zadanie ręcznie wyzwalanych sieci web. Po dodaniu zadanie ręcznie wyzwalanych sieci web, proces jest prawie takie same dla zaplanowane zadanie sieci web.

1.  Przejdź do **sieci Web zadania** pulpitu nawigacyjnego.
2.  Na pasku narzędzi wybierz **Dodaj**.
3.  Nadaj nazwę zadania.  
    Dla uzyskania przejrzystości, może pomóc zgodna z nazwą archiwum i otwórz go dla różnych wersji *run.cmd* pliku.
4.  W **przekazywania pliku** obszaru formularza, wybierz opcję **Otwórz plik** ikonę, a następnie wyszukaj plik zip, który został utworzony w poprzedniej sekcji.

    a.  W **typu** wybierz opcję **Triggered**.  
    b.  W **wyzwalaczy** wybierz opcję **ręcznego**.

5.  Kliknij przycisk **OK**.

![Uruchom polecenia profilera](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Uruchom profilera

Teraz, gdy masz nowe zadanie sieci web, które mogą być uruchamiane ręcznie, możesz uruchomić go zgodnie z instrukcjami w tej sekcji.

Zgodnie z projektem, może mieć tylko jeden *ApplicationInsightsProfiler.exe* procesu uruchomionego na komputerze w danym momencie. Dlatego przed rozpoczęciem należy wyłączyć *ciągłe* zadanie sieci web z tego pulpitu nawigacyjnego. 
1. Zaznacz wiersz nowe zadanie sieci web, a następnie wybierz **zatrzymać**. 
2. Na pasku narzędzi wybierz **Odśwież**i upewnij się, czy stan wskazuje, że zadanie zostało zatrzymane.
3. Zaznacz wiersz nowe zadanie sieci web, a następnie wybierz **Uruchom**.
4. Z zaznaczonym wierszu, na pasku narzędzi wybierz **dzienniki** polecenia.  
    Ta akcja powoduje otwarcie pulpitu nawigacyjnego zadań sieci web dla nowego zadania sieci web i przedstawia on najnowszych uruchamia i ich wyników.
5. Wybierz wystąpienie uruchomienia, po prostu uruchomienia.  
    Jeśli zostało pomyślnie wyzwolone nowe zadanie sieci web, można wyświetlić niektórych dzienników diagnostycznych pochodzące z profilera które upewnij się, że profilowania został uruchomiony.

### <a name="things-to-consider"></a>Co należy wziąć pod uwagę

Jednak ta metoda jest stosunkowo prosta, Uwzględnij następujące kwestie:

* Ponieważ zadanie sieci web nie jest zarządzana przez naszych usług, firma Microsoft nie ma możliwości można zaktualizować pliki binarne agenta dla zadania sieci web. Firma Microsoft aktualnie nie masz strony pobierania stabilna dla naszych danych binarnych, dlatego jedynym sposobem pobrania najnowszych plików binarnych aktualizacji rozszerzenia i dane z *ciągłe* folderu podczas jak w poprzednich krokach.

* Ponieważ ten proces wykorzystuje argumenty wiersza polecenia, które pierwotnie zostały zaprojektowane dla deweloperów, a nie użytkownicy końcowi, argumenty mogą ulec zmianie w przyszłości. Należy pamiętać o ewentualnych zmian podczas uaktualniania. Go nie powinien być taki problem, ponieważ można dodać zadanie sieci web, uruchom go i testy w celu zapewnienia, że działa. Po pewnym czasie zostaną budujemy interfejsu użytkownika do obsługi to bez ręczny proces.

* Funkcja zadania Web Job aplikacji sieci Web jest unikatowa. Po uruchomieniu zadania sieci web gwarantuje to, że proces ma tego samego zmienne środowiskowe i ustawienia aplikacji, które będą miały witryny sieci Web. Oznacza to, że nie należy do przekazania klucza Instrumentacji z wiersza polecenia profilera. Profiler powinien wybierz klucza instrumentacji ze środowiska. Jednak jeśli chcesz uruchomić profilera z pola deweloperów lub na komputerze poza aplikacje sieci Web, należy podać klucz instrumentacji. Możesz to zrobić, przekazując argument `--ikey <instrumentation-key>`. Ta wartość musi być zgodna klucza Instrumentacji używanej aplikacji. Dane wyjściowe dziennika profilera informuje, które ikey wprowadzenie profilera i czy wykryliśmy działania z tego klucza Instrumentacji podczas możemy zostały profilowania.

* Zadania ręczne wyzwalanych sieci web mogą być wyzwalane za pośrednictwem sieci Web punktu zaczepienia. Ten adres URL można uzyskać przez kliknięcie prawym przyciskiem myszy zadanie sieci web na pulpicie nawigacyjnym, a następnie wyświetlić właściwości. Lub na pasku narzędzi wybierz **właściwości** po wybraniu zadanie sieci web w tabeli. Takie podejście otwiera nieskończone możliwości, takie jak wyzwalania profilera z planowaną CI/CD (np. programu VSTS) lub przypominać Flow firmy Microsoft (https://flow.microsoft.com/en-us/). Ostatecznie, wybór zależy od sposobu złożonych, która ma być Twojej *run.cmd* pliku (może to również *run.ps1* pliku), ale istnieje już elastyczność.

## <a name="next-steps"></a>Kolejne kroki

* [Praca z usługi Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
