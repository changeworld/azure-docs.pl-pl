---
title: Profil aplikacji internetowych na żywo na platformie Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Określ ścieżki aktywnej w kodzie serwera sieci web za pomocą profilera niskiego poziomu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b11883ae592999a49db363486fd47a566bdfd360
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498712"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil działających aplikacji sieci web platformy Azure za pomocą usługi Application Insights

Ta funkcja usługi Azure Application Insights jest ogólnie dostępna dla funkcji Web Apps w usłudze Azure App Service i zasoby obliczeniowe platformy Azure. Aby uzyskać informacje dotyczące [przy użyciu lokalnego profiler](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

W tym artykule omówiono ilość czasu spędzonego w każdej metodzie działającej aplikacji internetowej, gdy używasz [usługi Application Insights](app-insights-overview.md). Narzędzie Application Insights Profiler Wyświetla szczegółowe profile bieżących żądań, które zostały obsłużone przez aplikację. Profiler wyróżnia *ścieżka aktywna* , który używa najwięcej czasu. Żądania z różnych czasów odpowiedzi są profilowane, na podstawie próbkowania. Za pomocą różnych technik, można zminimalizować obciążenie, który jest skojarzony z aplikacją.

Profiler działa aktualnie w przypadku aplikacji sieci web ASP.NET i ASP.NET Core uruchomionych w aplikacji sieci Web. Podstawowa warstwę usługi lub nowszy jest wymagany, aby użyć Profiler.

## <a id="installation"></a> Włącz Profiler dla aplikacji sieci Web

Po wdrożeniu aplikacji sieci Web, niezależnie od tego zestawu SDK aplikacji usługi Insights są zawarte w kodzie źródłowym, wykonaj następujące czynności:

1. Przejdź do **App Services** okienko w witrynie Azure portal.
1. Przejdź do **Ustawienia > Monitorowanie** okienka.

   ![Włączanie usługi App Insights w portalu usługi App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Albo postępuj zgodnie z instrukcjami w okienku, aby utworzyć nowy zasób lub wybierz istniejący zasób usługi App Insights w celu monitorowania aplikacji sieci web. Zaakceptuj wszystkie domyślne opcje. **Diagnostyka na poziomie kodu** jest domyślnie włączona i umożliwia Profiler.

   ![Dodaj rozszerzenie witryny usługi App Insights][Enablement UI]

1. Profiler jest teraz zainstalowany z rozszerzeniem lokacji usługi App Insights i jest włączane przy użyciu aplikacji ustawienia aplikacji usługi.

    ![Ustawienia aplikacji dla Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Włącz Profiler dla zasobów obliczeniowych platformy Azure (wersja zapoznawcza)

Aby uzyskać informacje, zobacz [wersję zapoznawczą Profiler za zasoby obliczeniowe platformy Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Wyświetlanie danych profilera

Upewnij się, że aplikacja odbiera ruch. Jeśli przeprowadzasz eksperymentu można wygenerować żądania do aplikacji sieci web przy użyciu [test wydajności, Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Po włączeniu nowo Profiler, po uruchomieniu testu obciążenia krótki około 15 minut, które będzie generował ślady narzędzia profiler. Gdyby Profiler już włączone przez jakiś czas Zachowaj należy pamiętać, że Profiler uruchamia losowo dwa razy na godzinę i czas trwania wynosi dwie minuty, każdym razem uruchamia. Zalecamy najpierw uruchamiania testu obciążeniowego za jedną godzinę upewnić się, że możesz uzyskać przykładowe ślady narzędzia profiler.

Gdy Twoja aplikacja otrzyma część ruchu, przejdź do **wydajności** okienku wybierz **podjąć akcje** wyświetlić ślady narzędzia profiler, a następnie wybierz pozycję **ślady Profiler** przycisku.

![Śledzi Profiler Podgląd okienko Insights wydajności aplikacji][performance-blade-v2-examples]

Wybierz próbkę, aby wyświetlić podział na poziomie kodu czas wykonywania żądania.

![Eksplorator śledzenia usługi Application Insights][trace-explorer]

W Eksploratorze śledzenia zawiera następujące informacje:

* **Pokaż ścieżkę aktywną**: Otwiera największą liść węzła lub co najmniej coś zamknąć. W większości przypadków ten węzeł jest sąsiadujące wąskim gardłem wydajności.
* **Etykieta**: Nazwa funkcji lub zdarzeń. W drzewie wyświetlany kombinacji kodu i zdarzenia, które wystąpiły (takich jak SQL i HTTP zdarzenia). Najważniejsze zdarzenia reprezentuje całkowity czas trwania żądania.
* **Upłynęło**: interwał czasu między rozpoczęciem operacji i koniec operacji.
* **Gdy**: czas po funkcji lub zdarzeń była uruchomiona w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak odczytać dane dotyczące wydajności

Narzędzie profiler usługi firmy Microsoft używa kombinacji metody próbkowania i instrumentacji do analizowania wydajności aplikacji. Szczegółowe kolekcji jest w toku, narzędzie profiler usługi przykłady wskaźnik instrukcji dla każdego Procesora maszyny każdego milisekund. Każda próbka przechwytuje pełny stos wywołań wątku, który jest w trakcie wykonywania. Daje ona szczegółowe informacje na temat ten wątek został działania, zarówno na wysokim poziomie, jak i na niskim poziomie abstrakcji. Narzędzie profiler usługi zbiera również inne zdarzenia do śledzenia działania korelacji i przyczynowości, łącznie z kontekstem przełączanie zdarzeń, zdarzenia Biblioteka zadań równoległych (TPL) i zdarzenia puli wątków.

Stos wywołań, który jest wyświetlany w widoku osi czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje pełny stos wywołań wątku, zawiera kod z programu Microsoft .NET Framework i z innych platform, które odwołują się.

### <a id="jitnewobj"></a>Alokacja obiektu wspólnego (clr! JIT\_nowy lub clr! JIT\_Newarr1)

**CLR! JIT\_New** i **clr! JIT\_Newarr1** funkcji pomocnika w .NET Framework, które alokować pamięci z zarządzanego stosu. **CLR! JIT\_New** jest wywoływane, gdy obiekt jest przydzielany. **CLR! JIT\_Newarr1** jest wywoływane, gdy tablica obiektów została przydzielona. Te dwie funkcje są zazwyczaj szybkie i stosunkowo małe ilości czasu. Jeśli widzisz **clr! JIT\_New** lub **clr! JIT\_Newarr1** zająć znaczną ilość czasu na Twojej osi czasu, oznacza to, że kod może być przydzielanie wielu obiektów i wykorzystywanie znacznej ilości pamięci.

### <a id="theprestub"></a>Trwa ładowanie kodu języka wspólnego (clr! ThePreStub)

**CLR! ThePreStub** jest funkcji pomocnika, która w .NET Framework, który przygotowuje kodu wykonywanego po raz pierwszy. To zwykle obejmują, ale nie jest ograniczona do kompilacji just-in-time (JIT). Dla każdego metodę języka C# **clr! ThePreStub** powinna ona zostać wywołana najwyżej jeden raz w okresie istnienia procesu.

Jeśli **clr! ThePreStub** zajmuje znaczną ilość czasu dla żądania, oznacza to, czy żądanie jest pierwszą, która wykonuje tę metodę. Czas dla .NET Framework środowiska uruchomieniowego należy załadować pierwszej metody jest znaczący. Należy rozważyć, za pomocą procesu rozgrzewania, który jest wykonywany przed użytkownikom uzyskać do niego dostęp, lub Rozważ uruchomienie Native Image Generator (ngen.exe) część kodu na swoje zestawy.

### <a id="lockcontention"></a>Rywalizacja o blokady (clr! JITutil\_MonContention lub clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** lub **clr! JITutil\_MonEnterWorker** wskazuje, że bieżący wątek czeka na zwolnienie blokady. Ten tekst jest wyświetlany często, podczas wykonywania języka C# **blokady** instrukcji, podczas wywoływania **Monitor.Enter** metodę, lub podczas wywoływania metody z **MethodImplOptions.Synchronized** atrybutu. Zwykle występuje Rywalizacja o blokady po wątku _A_ uzyskuje blokadę, a wątku _B_ próbuje uzyskać ten sam blokady przed wątku _A_ zwalnia go.

### <a id="ngencold"></a>Trwa ładowanie kodu ([ZIMNEGO])

Jeśli nazwa metody zawiera **[ZIMNYCH]**, takich jak **mscorlib.ni! [COLD]system.Reflection.CustomAttribute.IsDefined**, środowiska uruchomieniowego .NET Framework jest wykonywanie kodu po raz pierwszy, który nie jest zoptymalizowana przez <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">Optymalizacja sterowana profilem</a>. Dla każdej metody powinien zostać wyświetlony co najwyżej jeden raz w okresie istnienia procesu.

Jeśli podczas ładowania kodu zajmuje znaczną ilość czasu dla żądania, oznacza to, czy żądanie jest pierwszy z nich można wykonać niezoptymalizowane część metody. Należy rozważyć użycie procesu rozgrzewania, który wykonuje część kodu, zanim do niego dostęp przez użytkowników.

### <a id="httpclientsend"></a>Wyślij żądanie HTTP

Metody, takie jak **HttpClient.Send** wskazują, że kod czeka na ukończenie żądania HTTP.

### <a id="sqlcommand"></a>Operacja bazy danych

Metody, takie jak **SqlCommand.Execute** wskazują, że kod czeka na zakończenie operacji bazy danych.

### <a id="await"></a>Oczekiwanie (AWAIT\_czas)

**AWAIT\_czasu** wskazuje, że kod oczekuje na zakończenie innego zadania. Zazwyczaj jest to określane za pomocą języka C# **AWAIT** instrukcji. Jeśli dany kod realizuje C# **AWAIT**wątek rozwija i zwraca kontrolę do puli wątków i istnieje żaden wątek, który jest zablokowany, trwa oczekiwanie na **AWAIT** na zakończenie. Jednak logicznie wątku, czy **AWAIT** "blocked" i czeka na zakończenie operacji. **AWAIT\_czasu** deklaracja wskazuje czas blokowania oczekiwanie na zakończenie zadania.

### <a id="block"></a>Czas blokowania

**BLOCKED_TIME** wskazuje, że kod oczekuje na inny zasób, które mają być dostępne. Na przykład może być oczekiwanie obiektu synchronizacji, wątek ma być dostępny lub do zakończenia wniosku.

### <a id="cpu"></a>Czas procesora CPU

Procesor był zajęty wykonywaniem instrukcji.

### <a id="disk"></a>Czas dysku

Aplikacja wykonuje operacje dyskowe.

### <a id="network"></a>Czas sieci

Aplikacja jest przeprowadzanie operacji sieciowych.

### <a id="when"></a>Gdy kolumny

**Podczas** kolumna jest wizualizację jak WŁĄCZNYCH próbek zebranych dla węzła różnią się wraz z upływem czasu. Łączny zakres żądania jest podzielony na 32 przedziałów czasu. Włącznych próbek dla tego węzła są zbierane w tych zasobników 32. Każdego przedziału jest reprezentowany jako pasek. Wysokość paska reprezentuje skalowaną wartość. Dla węzłów, które są oznaczone **CPU_TIME** lub **BLOCKED_TIME**, lub w przypadku, gdy istnieje relacja widocznych na korzystanie z zasobów (na przykład procesora CPU, dysku lub wątek), pasek reprezentuje użycie jednego z zasoby w okresie tego zasobnika. Dla tych metryk jest można uzyskać wartość większą niż 100% za korzystanie z wielu zasobów. Na przykład jeśli używasz, średnio dwa procesory przedział czasu, Uzyskaj 200 procent.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych to pięć dni. Maksymalna dane są pozyskiwane na dzień wynosi 10 GB.

Nie istnieją żadne opłaty za korzystanie z usługi Profiler. Możesz użyć usługi Profiler, aplikacji sieci web musi być hostowany w warstwy podstawowa, aplikacji sieci Web.

## <a name="overhead-and-sampling-algorithm"></a>Obciążenie i algorytm próbkowania

Profiler uruchamia losowo dwie minuty, co godzinę na każdej maszynie wirtualnej, który hostuje aplikację, która ma Profiler włączone do przechwytywania śladów. Gdy Profiler jest uruchomiona, dodaje od 5 do 15 procent obciążenie Procesora na serwerze.

Więcej serwerów, które są dostępne do hostowania aplikacji, ma Profiler mniejszym wpływem na wydajność aplikacji ogólnej. Jest to spowodowane algorytm próbkowania powoduje Profiler systemem tylko 5 procent serwerów, w dowolnym momencie. Do obsługi żądań sieci web, aby zrównoważyć obciążenie serwera spowodowane przez uruchomione Profiler, dostępnych jest więcej serwerów.

## <a name="disable-profiler"></a>Wyłącz narzędzie Profiler

Aby zatrzymać lub ponownie uruchom program Profiler aplikację internetową poszczególne wystąpienia, w obszarze **zadania Web Job**, przejdź do zasobu aplikacji sieci Web. Aby usunąć Profiler, przejdź do **rozszerzenia**.

![Wyłącz Profiler dla zadania w sieci web][disable-profiler-webjob]

Zaleca się, że masz Profiler włączone na wszystkich Twoich aplikacji sieci web do wykrywania problemów z wydajnością tak szybko, jak to możliwe.

Jeśli używasz WebDeploy, aby wdrożyć zmiany dla aplikacji sieci web, upewnij się, wykluczyć App_Data folder przed usunięciem podczas wdrażania. W przeciwnym razie rozszerzenia Profiler pliki są usuwane przy następnym wdrożysz aplikację sieci web na platformie Azure.


## <a id="troubleshooting"></a>Rozwiązywanie problemów

### <a name="too-many-active-profiling-sessions"></a>Zbyt wiele aktywnych sesji profilowania

Obecnie można włączyć Profiler na maksymalnie cztery Azure web apps i miejsc wdrożenia, które działają w ramach jednego planu usług. Jeśli zadanie sieci web Profiler zgłoszenie zbyt wiele aktywnych sesji profilowania, przenieść niektórych aplikacji sieci web do planu w innej usługi.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak ustalić, czy Application Insights Profiler działa?

Profiler jest uruchamiany jako zadanie web ciągły w aplikacji sieci web. Możesz otworzyć zasób aplikacji sieci web w [witryny Azure portal](https://portal.azure.com). W **WebJobs** okienko, sprawdź stan **ApplicationInsightsProfiler**. Jeśli nie jest uruchomiony, otwórz **dzienniki** Aby uzyskać więcej informacji.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Dlaczego nie można znaleźć przykłady stosu, mimo że Profiler działa?

Oto kilka rzeczy, które można sprawdzić:

* Upewnij się, że Twój plan usługi sieci web app service w warstwie podstawowa lub wyższej.
* Upewnij się, że Twoja aplikacja sieci web ma Application Insights SDK 2.2 w wersji Beta lub później włączone.
* Upewnij się, że Twoja aplikacja sieci web ma **APPINSIGHTS_INSTRUMENTATIONKEY** ustawienia skonfigurowane za pomocą tego samego klucz instrumentacji, który jest używany przez zestaw SDK usługi Application Insights.
* Upewnij się, że Twoja aplikacja sieci web działa na platformie .NET Framework 4.6.
* Jeśli aplikacja sieci web znajduje się aplikacja ASP.NET Core, musi być uruchomiony co najmniej ASP.NET Core 2.0.

Po rozpoczęciu Profiler istnieje okres rozgrzewania krótki, podczas którego Profiler zbiera aktywnie kilka ślady wydajności. Po tym Profiler gromadzi ślady wydajności na dwie minuty, co godzinę.

> [!NOTE]
> Istnieje błąd w agencie programu profiler, który uniemożliwia przekazywanie ślady z aplikacji uruchomionych na platformy ASP.NET Core 2.1. Firma Microsoft pracuje nad poprawką, zostanie on gotowy wkrótce.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Został przy użyciu narzędzia profiler usługi platformy Azure. Co się stało z jej?

Po włączeniu Application Insights Profiler, agent narzędzia profiler usługi platformy Azure jest wyłączona.

### <a id="double-counting"></a>Zliczanie w równoległych wątków podwójnej precyzji

W niektórych przypadkach metryki całkowity czas, w podglądzie stosu jest większa niż czas trwania żądania.

Taka sytuacja może wystąpić, gdy dwa lub więcej wątków są skojarzone z żądaniem i działają równolegle. W takim przypadku czas łączna liczba wątków jest większa niż czas, jaki upłynął. Jeden wątek może być oczekiwanie na inne, należy wykonać. Podgląd próbuje wykryć tę działalność i pomija postrzegać czas oczekiwania, ale jej błędami boku wyświetlania zbyt dużej ilości informacji, a nie pominięto, co może być krytyczne informacje.

Wyświetlenie równoległych wątków w Twoje ślady określają, które wątki oczekują, dzięki czemu można ustalić ścieżkę krytyczną dla żądania. W większości przypadków wątek, który szybko przechodzi w stan oczekiwania, po prostu oczekuje na inne wątki. Koncentrować się na inne wątki i Ignoruj czasu wątków oczekujących.

### <a id="issue-loading-trace-in-viewer"></a>Nie danych profilowania

Oto kilka rzeczy, które można sprawdzić:

* Jeśli dane, które chcesz wyświetlić jest starsza niż kilka tygodni, spróbuj ograniczyć czas filtru i spróbuj ponownie.
* Upewnij się, nie ma serwerów proxy lub Zapora zablokowała dostęp do https://gateway.azureserviceprofiler.net.
* Upewnij się, że klucz Instrumentacji usługi Application Insights, którego używasz w swojej aplikacji jest taki sam jak zasób usługi Application Insights, użytego w celu Włącz profilowanie. Klucz jest zwykle w pliku ApplicationInsights.config, ale można ją również w pliku web.config lub app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Raport o błędach w podglądzie profilowania

Prześlij zgłoszenie do pomocy technicznej w portalu. Pamiętaj uwzględniać identyfikator korelacji z komunikatu o błędzie.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Błąd wdrożenia: katalog nie jest pusty "D:\\macierzystego\\witryny\\wwwroot\\App_Data\\zadania

W przypadku ponownego wdrażania aplikacji sieci web do zasobu aplikacji sieci Web za pomocą Profiler włączone, może pojawić się komunikat, jak pokazano poniżej:

*Katalog nie jest pusty "D:\\macierzystego\\witryny\\wwwroot\\App_Data\\zadania*

Ten błąd występuje, jeśli należy uruchomić narzędzie Web Deploy ze skryptów lub w potoku wdrożenia metodyki DevOps platformy Azure. Rozwiązanie to można dodać następujące parametry dodatkowe wdrożenia do zadań narzędzia Web Deploy:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Te parametry, usuń folder, który jest używany przez program Application Insights Profiler i odblokować proces ponownego wdrażania. Nie wpływają one na wystąpienie Profiler, które jest aktualnie uruchomione.

## <a name="manual-installation"></a>Instalacja ręczna

Po skonfigurowaniu Profiler, aktualizacje są wprowadzane do ustawień aplikacji sieci web. Możesz ręcznie zastosować aktualizacje, jeśli wymaga środowiska. Przykładem może być, że aplikacja jest uruchomiona w środowisku aplikacji sieci Web w usłudze PowerApps.

1. W **kontroli aplikacji sieci Web** otwartym okienkiem **ustawienia**.
1. Ustaw **.Net Framework w wersji** do **wersje 4.6**.
1. Ustaw **zawsze włączone** do **na**.
1. Dodaj **APPINSIGHTS_INSTRUMENTATIONKEY** aplikacji ustawienia, a następnie ustaw wartość na ten sam klucz instrumentacji, który jest używany przez zestaw SDK.
1. Otwórz **zaawansowane narzędzia**.
1. Wybierz **Przejdź** otworzyć Kudu witryny sieci Web.
1. W witrynie sieci Web programu Kudu, wybierz **rozszerzeń witryny**.
1. Zainstaluj **usługi Application Insights** w galerii aplikacji sieci Web platformy Azure.
1. Uruchom ponownie aplikację internetową.

## <a id="profileondemand"></a> Ręcznie wyzwalać Profiler

Profiler może być uruchamiane ręcznie, za pomocą kliknięcia jednego przycisku. Załóżmy, że uruchomieniu testu wydajności sieci web. Konieczne będzie śladów, które pomagają zrozumieć, jak działa aplikacja sieci web pod obciążeniem. Masz kontrolę nad kiedy są przechwytywane dane śledzenia ma podstawowe znaczenie, ponieważ wiadomo, gdy zostanie uruchomiony test obciążeniowy, ale interwał próbkowania losowe może jej przeoczyć.
Poniższe kroki pokazują, jak działa w tym scenariuszu:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Opcjonalnie) Krok 1: Generowanie ruch do aplikacji sieci web przez uruchomienie testu wydajności sieci web

Jeśli Twoja aplikacja sieci web ma już ruchu przychodzącego lub po prostu chcesz ręcznie wygenerować ruchu, Pomiń tę sekcję i przejdź do kroku 2.

Przejdź do portalu usługi Application Insights **Konfiguruj > testowania Wydajnościowego**. Kliknij nowy przycisk, aby rozpocząć nowy test wydajności.
![Utwórz nowy test wydajności][create-performance-test]

W **nowego testu wydajności** okienko, skonfiguruj docelowy adres URL testu. Zaakceptuj wszystkie ustawienia domyślne i uruchom test obciążenia.

![Konfigurowanie testu obciążeniowego][configure-performance-test]

Zobaczysz, że nowy test znajduje się w kolejce najpierw następuje stan w toku.

![test obciążeniowy jest przesłane i umieszczonych w kolejce][load-test-queued]

![test obciążenia jest uruchomiony w toku][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Krok 2. uruchamianie profilera na żądanie

Gdy uruchomiony jest test obciążeniowy, firma Microsoft można uruchomić program profilujący do przechwytywania śladów, w usłudze web app, gdy odbiera obciążenia.
Przejdź do okienka Profiler skonfigurować:

![Konfigurowanie wpis w okienku profilera][configure-profiler-entry]

Na **okienko skonfigurować Profiler**, Brak **profilu teraz** przycisk, aby wyzwalać profiler we wszystkich wystąpieniach aplikacji internetowych połączonych. Ponadto zostały podane widoczności na, gdy program profilujący został uruchomiony w przeszłości.

![Profiler na żądanie][profiler-on-demand]

Zobaczysz powiadomienie i zmianę profilera stan uruchomienia stanu.

### <a name="step-3-view-traces"></a>Krok 3: Wyświetlanie śladów

Po zakończeniu program profilujący, wykonaj instrukcje dotyczące powiadomień, aby przejść do bloku i wyświetlić ślady wydajności.

### <a name="troubleshooting-on-demand-profiler"></a>Rozwiązywanie problemów z profilera na żądanie

Czasami może zostać wyświetlony komunikat o błędzie limitu czasu Profiler po sesji na żądanie:

![Błąd upływu limitu czasu Profiler][profiler-timeout]

Istnieją dwie możliwe przyczyny Dlaczego zostanie wyświetlony ten błąd:

1. Sesji profilera na żądanie zakończyło się pomyślnie, ale usługi Application Insights trwała dłużej przetwarzania zebranych danych. Jeśli dane nie zostało ukończone, przetwarzanych w ciągu 15 minut, w portalu zostanie wyświetlony komunikat limitu czasu. Jednak po krótkiej chwili Profiler ślady pojawią się. W takim przypadku po prostu zignoruj komunikat o błędzie teraz. Obecnie pracujemy nad poprawką

1. Aplikacja sieci web ma starszą wersję agenta Profiler, który nie ma funkcji na żądanie. Jeśli profil usługi Application Insights został włączony wcześniej, jest szansa, że musisz zaktualizować swoje agenta Profiler, aby rozpocząć korzystanie z funkcji na żądanie.
  
Wykonaj następujące kroki, aby zaznaczyć i zainstalować najnowszą Profiler:

1. Przejdź do ustawień aplikacji usługi App i sprawdzić, jeśli są ustawione następujące ustawienia:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Zastąp klucz Instrumentacji właściwe dla usługi Application Insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0, jeśli dowolne z tych ustawień nie są ustawione, przejdź do okienka włączenie usługi Application Insights, aby zainstalować najnowsze rozszerzenie witryny.

1. Przejdź do okienka usługi Application Insights w portalu usługi App Services.

    ![Włącz usługę Application Insights w portalu usługi App Services][enable-app-insights]

1. Jeśli zostanie wyświetlony przycisk "Aktualizuj" na następującej stronie, kliknij go, aby zaktualizować rozszerzenie witryny usługi Application Insights, co spowoduje zainstalowanie najnowszego agenta Profiler.
![Aktualizuj rozszerzenie witryny][update-site-extension]

1. Następnie kliknij przycisk **zmienić** zapewnić Profiler jest włączona i wybierz **OK** Aby zapisać zmiany.

    ![Zmiany i Zapisz usługi app insights][change-and-save-appinsights]

1. Wróć do **ustawienia aplikacji** kartę dla usługi App Service można dokładnie sprawdzić następujące elementy ustawienia aplikacji są ustawione:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Zastąp klucz Instrumentacji właściwe dla usługi application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Ustawienia aplikacji dla programu profilującego][app-settings-for-profiler]

1. Opcjonalnie sprawdź wersję rozszerzenia i upewnienie się, że żadna aktualizacja jest dostępna.

    ![Sprawdzanie aktualizacji rozszerzenia][check-for-extension-update]

## <a name="next-steps"></a>Kolejne kroki

* [Praca z usługą Application Insights w programie Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
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
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
