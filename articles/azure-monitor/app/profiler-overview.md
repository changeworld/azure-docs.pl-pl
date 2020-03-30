---
title: Aplikacje produkcyjne profilu na platformie Azure za pomocą usługi Application Insights Profiler
description: Zidentyfikuj gorącą ścieżkę w kodzie serwera sieci web za pomocą profilera o niskim rozmiarze.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671651"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Aplikacje produkcyjne profilu na platformie Azure za pomocą usługi Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Włącz profiler usługi Application Insights dla aplikacji

Usługa Azure Application Insights Profiler zapewnia ślady wydajności dla aplikacji, które są uruchomione w produkcji na platformie Azure. Profiler przechwytuje dane automatycznie na dużą skalę bez negatywnego wpływu na użytkowników. Profiler pomaga zidentyfikować "gorącą" ścieżkę kodu, która zajmuje najdłużej, gdy obsługuje określone żądanie sieci web. 

Profiler współpracuje z aplikacjami platformy .NET, które są wdrażane w następujących usługach platformy Azure. Szczegółowe instrukcje dotyczące włączania profilera dla każdego typu usługi znajdują się w poniższych łączach.

* [Usługa aplikacji platformy Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**PODGLĄD** ASP.NET podstawowych aplikacji sieci Web systemu Azure dla systemu Windows](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Jeśli masz włączoną funkcję Profiler, ale nie widzisz śladów, zapoznaj się z naszym [przewodnikiem rozwiązywania problemów.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

## <a name="view-profiler-data"></a>Wyświetlanie danych profilera

Aby profiler do przekazywania śladów, aplikacja musi być aktywnie obsługi żądań. Jeśli przeprowadzasz eksperyment, możesz generować żądania do aplikacji sieci web przy użyciu [testów wydajności usługi Application Insights.](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test) Jeśli masz nowo włączone Profiler, można uruchomić test obciążenia krótkiego. Gdy test obciążenia jest uruchomiony, wybierz przycisk **Profil teraz** w [okienku Ustawienia **profilera** ](profiler-settings.md). Gdy profiler jest uruchomiony, profile losowo około raz na godzinę i przez okres dwóch minut. Jeśli aplikacja obsługuje stały strumień żądań, profiler przekazuje ślady co godzinę.

Po aplikacji odbiera ruch i Profiler miał czas, aby przekazać ślady, powinny mieć ślady do wyświetlenia. Ten proces może potrwać od 5 do 10 minut. Aby wyświetlić ślady, w okienku **Wydajność** wybierz pozycję **Podejmij akcje**, a następnie wybierz przycisk **Ślady profilera.**

![Śledzenie profilera w okienku wydajności usługi Application Insights][performance-blade]

Wybierz przykład, aby wyświetlić podział na poziomie kodu czasu spędzonego na wykonywaniu żądania.

![Eksplorator śledzenia usługi Application Insights][trace-explorer]

Eksplorator śledzenia wyświetla następujące informacje:

* **Pokaż gorącą ścieżkę:** Otwiera największy węzeł liścia lub przynajmniej coś blisko. W większości przypadków ten węzeł znajduje się w pobliżu wąskiego gardła wydajności.
* **Etykieta**: Nazwa funkcji lub zdarzenia. Drzewo wyświetla kombinację kodu i zdarzeń, które wystąpiły, takich jak SQL i HTTP zdarzeń. Najważniejsze zdarzenie reprezentuje ogólny czas trwania żądania.
* **Upłynął:** Przedział czasu między rozpoczęciem operacji a zakończeniem operacji.
* **Kiedy**: Czas, kiedy funkcja lub zdarzenie było uruchomione w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak odczytać dane dotyczące wydajności

Profiler usługi firmy Microsoft używa kombinacji metod próbkowania i instrumentacji do analizy wydajności aplikacji. Gdy szczegółowa kolekcja jest w toku, profiler usługi próbki wskaźnik instrukcji każdego procesora cpu komputera co milisekundę. Każdy przykład przechwytuje kompletny stos wywołań wątku, który jest aktualnie wykonywany. Zawiera szczegółowe informacje o tym, co ten wątek robił, zarówno na wysokim poziomie, jak i na niskim poziomie abstrakcji. Profiler usługi zbiera również inne zdarzenia do śledzenia korelacji aktywności i przyczynowości, w tym zdarzeń przełączania kontekstu, zdarzeń biblioteki równoległej zadań (TPL) i zdarzeń puli wątków.

Stos wywołań, który jest wyświetlany w widoku osi czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje kompletny stos wywołań wątku, zawiera kod z programu Microsoft .NET Framework i innych struktur, do których odwołujesz.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Alokacja obiektów (clr! JIT\_Nowy lub clr! JIT\_Newarr1)

**Clr! JIT\_Nowy** i **clr! JIT\_Newarr1** są funkcje pomocnicze w .NET Framework, które przydzielają pamięć z zarządzanego stosu. **clr! JIT\_Nowy** jest wywoływany, gdy obiekt jest przydzielany. **clr! JIT\_Newarr1** jest wywoływany podczas przydzielania tablicy obiektów. Te dwie funkcje są zwykle szybkie i zajmują stosunkowo niewiele czasu. Jeśli **clr! JIT\_Nowy** lub **clr! JIT\_Newarr1** zajmuje dużo czasu na osi czasu, kod może być przydzielanie wielu obiektów i zużywa znaczne ilości pamięci.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Kod ładowania (clr! ThePreStub)

**clr! ThePreStub** jest funkcją pomocnika w .NET Framework, która przygotowuje kod do wykonania po raz pierwszy. To wykonanie zwykle zawiera, ale nie ogranicza się do kompilacji just-in-time (JIT). Dla każdej metody C#, **clr! ThePreStub** powinny być wywoływane co najwyżej raz podczas procesu.

Jeśli **clr! ThePreStub** zajmuje dużo czasu dla żądania, żądanie jest pierwszym, aby wykonać tę metodę. Czas dla środowiska uruchomieniowego programu .NET Framework, aby załadować pierwszą metodę jest znacząca. Można rozważyć użycie procesu rozgrzewania, który wykonuje tę część kodu, zanim użytkownicy uzyskują do niego dostęp, lub należy rozważyć uruchomienie generatora obrazów natywnych (ngen.exe) w zestawach.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Rywalizacja o blokadę (clr! JITutil\_MonContention lub clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** lub **clr! JITutil\_MonEnterWorker** wskazuje, że bieżący wątek oczekuje na blokadę do wydania. Ten tekst jest często wyświetlany podczas wykonywania instrukcji **C# LOCK,** wywoływania **metody Monitor.Enter** lub wywoływania metody z atrybutem **MethodImplOptions.Synchronized.** Rywalizacja o blokadę zwykle występuje, gdy wątek _A_ uzyskuje blokadę, a wątek _B_ próbuje uzyskać tę samą blokadę, zanim wątek _A_ ją zwolni.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Kod ładowania ([COLD])

Jeśli nazwa metody zawiera **[COLD]**, takie jak **mscorlib.ni![ COLD]System.Reflection.CustomAttribute.IsDefined**, .NET Framework runtime wykonuje kod po raz pierwszy, który nie jest zoptymalizowany przez [optymalizację z przewodnikiem profilu](/cpp/build/profile-guided-optimizations). Dla każdej metody powinna być wyświetlana co najwyżej raz podczas procesu.

Jeśli ładowanie kodu zajmuje znaczną ilość czasu dla żądania, żądanie jest pierwszym, aby wykonać nieoptymizowany część metody. Należy rozważyć użycie procesu rozgrzewania, który wykonuje tę część kodu, zanim użytkownicy uzyskują do niego dostęp.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Wyślij żądanie HTTP

Metody takie jak **HttpClient.Send** wskazują, że kod oczekuje na zakończenie żądania HTTP.

### <a name="database-operation"></a><a id="sqlcommand"></a>Operacja bazy danych

Metody takie jak **SqlCommand.Execute** wskazują, że kod oczekuje na zakończenie operacji bazy danych.

### <a name="waiting-await_time"></a><a id="await"></a>Oczekiwanie (OCZEKIWANIE\_NA CZAS)

**AWAIT\_TIME** wskazuje, że kod oczekuje na zakończenie innego zadania. To opóźnienie zwykle dzieje się z C# **AWAIT** instrukcji. Gdy kod nie C# **AWAIT**, wątek odwija i zwraca kontrolę do puli wątków i **AWAIT** nie ma wątku, który jest zablokowany oczekiwania na zakończenie await. Jednak logicznie wątku, który nie **AWAIT** jest "zablokowany" i oczekuje na zakończenie operacji. **Instrukcja\_AWAIT TIME** wskazuje zablokowany czas oczekiwania na zakończenie zadania.

### <a name="blocked-time"></a><a id="block"></a>Zablokowany czas

**BLOCKED_TIME** wskazuje, że kod oczekuje na inny zasób, który ma być dostępny. Na przykład może czekać na obiekt synchronizacji, dla wątku, które mają być dostępne lub żądanie, aby zakończyć.

### <a name="unmanaged-async"></a>Niezarządzane Async

.NET framework emituje zdarzenia ETW i przekazuje identyfikatory działań między wątkami, dzięki czemu wywołania asynchowe mogą być śledzone przez wątki. Niezarządzanego kodu (kod macierzysty) i niektóre starsze style kodu asynchroniowego brakuje tych zdarzeń i identyfikatorów działania, więc profiler nie może powiedzieć, jaki wątek i jakie funkcje są uruchomione w wątku. Jest to oznaczone jako "Niezarządzane Async" w stosie wywołań. Jeśli pobierzesz plik ETW, możesz użyć [PerfView,](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) aby uzyskać lepszy wgląd w to, co się dzieje.

### <a name="cpu-time"></a><a id="cpu"></a>Czas procesora

Procesor jest zajęty wykonywaniem instrukcji.

### <a name="disk-time"></a><a id="disk"></a>Czas dysku

Aplikacja wykonuje operacje dysku.

### <a name="network-time"></a><a id="network"></a>Czas sieciowy

Aplikacja wykonuje operacje sieciowe.

### <a name="when-column"></a><a id="when"></a>Kiedy kolumna

**Kolumna When** jest wizualizacją sposobu, w jaki przykłady INCLUSIVE zebrane dla węzła różnią się w czasie. Całkowity zakres żądania jest podzielony na przedziały czasu 32. Próbki włącznie dla tego węzła są gromadzone w tych 32 zasobników. Każde zasobnik jest reprezentowany jako pasek. Wysokość paska reprezentuje skalowane wartości. W przypadku węzłów oznaczonych **jako CPU_TIME** lub **BLOCKED_TIME**lub w przypadku, gdy istnieje oczywista relacja do zużywania zasobu (na przykład procesora CPU, dysku lub wątku), pasek reprezentuje zużycie jednego z zasobów podczas zasobu. W przypadku tych metryk można uzyskać wartość większą niż 100 procent, zużywając wiele zasobów. Na przykład jeśli używasz średnio dwóch procesorów w interwale, otrzymasz 200 procent.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi pięć dni. Maksymalna liczba danych, które są pojmowane dziennie, wynosi 10 GB.

Nie ma żadnych opłat za korzystanie z usługi Profiler. Aby można było go używać, aplikacja sieci web musi być hostowana w co najmniej podstawowej warstwie funkcji aplikacji sieci Web usługi Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algorytm narzutów i próbkowania

Profiler losowo uruchamia dwie minuty co godzinę na każdej maszynie wirtualnej, która obsługuje aplikację, która ma profiler włączone do przechwytywania śladów. Gdy profiler jest uruchomiony, dodaje od 5 do 15 procent obciążenie procesora CPU do serwera.

## <a name="next-steps"></a>Następne kroki
Włącz profiler usługi Application Insights dla aplikacji platformy Azure. Zobacz również:
* [Usługi aplikacji](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
