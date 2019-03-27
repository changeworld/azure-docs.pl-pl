---
title: Profil aplikacji produkcyjnych na platformie Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Określ ścieżki aktywnej w kodzie serwera sieci web za pomocą profilera niskiego poziomu.
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
ms.openlocfilehash: c07b325f3de6cd2cf3aaa436736786d2cdc42881
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498132"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil aplikacji produkcyjnych na platformie Azure za pomocą usługi Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Włącz Application Insights Profiler do aplikacji

Usługa Azure Application Insights Profiler zawiera ślady wydajności dla aplikacji, które są uruchomione w środowisku produkcyjnym na platformie Azure. Profiler przechwytuje dane automatycznie w odpowiedniej skali bez wywierania negatywnego wpływu na użytkowników. Profiler pomaga zidentyfikować ścieżka kodu "gorącymi", która zajmuje najwięcej czasu, gdy jest obsługi żądania namierzenie internetowego. 

Profiler działa z aplikacjami .NET, które są wdrażane na następujących usług platformy Azure. Szczegółowe instrukcje dotyczące włączania Profiler dla każdego typu usług są poniższe linki.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Zestawy skalowania maszyn wirtualnych i maszyn wirtualnych platformy Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Podgląd** aplikacji sieci Web dla systemu Linux platformy Azure dla platformy ASP.NET Core](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Jeśli włączono Profiler, ale nie widać ślady, Sprawdź nasze [przewodnik rozwiązywania problemów](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Wyświetlanie danych Profiler

Dla Profiler można przekazać dane śledzenia aplikacji musi być aktywnie obsługi żądań. Wykonujesz eksperymentu, można wygenerować żądania do aplikacji sieci web, za pomocą [testowanie wydajności usługi Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Jeśli Profiler nowo zostało włączone, możesz uruchomić test obciążenia krótki. Po uruchomieniu testu obciążenia wybierz **profilu teraz** znajdujący się na [ **ustawienia Profiler** okienko](profiler-settings.md#profiler-settings-pane). Gdy Profiler jest uruchomiona, profiluje losowo około raz na godzinę i czas trwania wynosi dwie minuty. Jeśli aplikacja obsługuje ciągłego strumienia żądań, Profiler przekazuje ślady co godzinę.

Po aplikacja odbiera część ruchu i Profiler miał czas przekazywania śladów, powinny być śladów, aby wyświetlić. To może zająć 5 – 10 minut. Aby wyświetlić ślady, w **wydajności** wybierz opcję **podjąć akcje**, a następnie wybierz pozycję **ślady Profiler** przycisku.

![Śledzi Profiler Podgląd okienko Insights wydajności aplikacji][performance-blade]

Wybierz próbkę, aby wyświetlić podział na poziomie kodu czas wykonywania żądania.

![Eksplorator śledzenia usługi Application Insights][trace-explorer]

W Eksploratorze śledzenia zawiera następujące informacje:

* **Pokaż ścieżkę aktywną**: Zostanie otwarta największą liść węzła lub co najmniej coś zamknąć. W większości przypadków ten węzeł zbliża się wąskim gardłem wydajności.
* **Etykieta**: Nazwa funkcji lub zdarzeń. W drzewie wyświetlany kombinacji kodu i zdarzenia, które wystąpiły, takimi jak SQL i HTTP. Najważniejsze zdarzenia reprezentuje całkowity czas trwania żądania.
* **Upłynęło**: Interwał czasu między rozpoczęciem operacji i koniec operacji.
* **Gdy**: Czas, po funkcji lub zdarzeń była uruchomiona w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak odczytać dane dotyczące wydajności

Narzędzie profiler usługi firmy Microsoft używa kombinacji metody próbkowania i instrumentacji do analizowania wydajności aplikacji. Szczegółowe kolekcji jest w toku, narzędzie profiler usługi przykłady wskaźnik instrukcji dla każdego Procesora maszyny każdego milisekund. Każda próbka przechwytuje pełny stos wywołań wątku, który jest w trakcie wykonywania. Daje ona szczegółowe informacje na temat ten wątek został działania, zarówno wysokiego poziomu, jak i niskim poziomie abstrakcji. Narzędzie profiler usługi zbiera również inne zdarzenia do śledzenia działania korelacji i przyczynowości, łącznie z kontekstem przełączanie zdarzeń, zdarzenia Biblioteka zadań równoległych (TPL) i zdarzenia puli wątków.

Stos wywołań, który jest wyświetlany w widoku osi czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje pełny stos wywołań wątku, zawiera kod z programu Microsoft .NET Framework i z innych platform, które odwołują się.

### <a id="jitnewobj"></a>Alokacja obiektu wspólnego (clr! JIT\_nowy lub clr! JIT\_Newarr1)

**CLR! JIT\_New** i **clr! JIT\_Newarr1** funkcji pomocnika w programie .NET Framework, które alokować pamięci z zarządzanego stosu. **CLR! JIT\_New** jest wywoływane, gdy obiekt jest przydzielany. **CLR! JIT\_Newarr1** jest wywoływane, gdy tablica obiektów została przydzielona. Te dwie funkcje są zazwyczaj szybkie i stosunkowo małe ilości czasu. Jeśli **clr! JIT\_New** lub **clr! JIT\_Newarr1** przyjmuje dużo czasu na osi czasu, kod może być przydzielanie wielu obiektów i wykorzystywanie znacznej ilości pamięci.

### <a id="theprestub"></a>Trwa ładowanie kodu języka wspólnego (clr! ThePreStub)

**CLR! ThePreStub** jest funkcji pomocnika, która w programie .NET Framework, który przygotowuje kodu wykonywanego po raz pierwszy. To wykonanie zwykle obejmują, ale nie jest ograniczona do just-in-time (JIT) kompilacja. Dla każdego C# metody **clr! ThePreStub** powinna być wywołana najwyżej jeden raz podczas procesu.

Jeśli **clr! ThePreStub** zajmuje dużo czasu na żądanie, żądanie jest pierwszy z nich do wykonywania tej metody. Czas dla środowiska uruchomieniowego .NET Framework załadować pierwszej metody jest znaczący. Należy rozważyć, za pomocą procesu rozgrzewania, który jest wykonywany przed użytkownikom uzyskać do niego dostęp, lub Rozważ uruchomienie Native Image Generator (ngen.exe) część kodu na swoje zestawy.

### <a id="lockcontention"></a>Rywalizacja o blokady (clr! JITutil\_MonContention lub clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** lub **clr! JITutil\_MonEnterWorker** wskazuje, że bieżący wątek czeka na zwolnienie blokady. Ten tekst jest wyświetlany często, podczas wykonywania C# **blokady** instrukcji, wywołaj **Monitor.Enter** metody lub wywołania metody o **MethodImplOptions.Synchronized**atrybutu. Zwykle występuje Rywalizacja o blokady po wątku _A_ uzyskuje blokadę i wątku _B_ próbuje uzyskać ten sam blokady przed wątku _A_ zwalnia go.

### <a id="ngencold"></a>Trwa ładowanie kodu ([ZIMNEGO])

Jeśli nazwa metody zawiera **[ZIMNYCH]**, takich jak **mscorlib.ni! [ COLD]system.Reflection.CustomAttribute.IsDefined**, środowisko uruchomieniowe programu .NET Framework jest wykonywanie kodu po raz pierwszy, nie jest zoptymalizowany pod kątem przez [optymalizacji sterowanej profilem](/cpp/build/profile-guided-optimizations). Dla każdej metody jej powinien zostać wyświetlony co najwyżej jeden raz w procesie.

Jeśli podczas ładowania kodu zajmuje znaczną ilość czasu dla żądania, żądanie jest pierwszy z nich można wykonać niezoptymalizowane część metody. Należy rozważyć użycie procesu rozgrzewania, który wykonuje część kodu, zanim do niego dostęp przez użytkowników.

### <a id="httpclientsend"></a>Wyślij żądanie HTTP

Metody takie jak **HttpClient.Send** wskazują, że kod czeka na ukończenie żądania HTTP.

### <a id="sqlcommand"></a>Operacja bazy danych

Metody takie jak **SqlCommand.Execute** wskazują, że kod czeka na zakończenie operacji bazy danych.

### <a id="await"></a>Oczekiwanie (AWAIT\_czas)

**AWAIT\_czasu** wskazuje, że kod oczekuje na zakończenie innego zadania. To opóźnienie zwykle dzieje się z C# **AWAIT** instrukcji. Jeśli dany kod realizuje C# **AWAIT**wątek rozwija i zwraca kontrolę do puli wątków i istnieje żaden wątek, który jest zablokowany, trwa oczekiwanie na **AWAIT** na zakończenie. Jednak logicznie wątku, czy **AWAIT** "blocked," i Trwa oczekiwanie na zakończenie operacji. **AWAIT\_czasu** deklaracja wskazuje czas blokowania oczekiwanie na zakończenie zadania.

### <a id="block"></a>Czas blokowania

**BLOCKED_TIME** wskazuje, że kod oczekuje na inny zasób, które mają być dostępne. Na przykład może być oczekiwanie obiektu synchronizacji, wątek ma być dostępny lub do zakończenia wniosku.

### <a name="unmanaged-async"></a>Niezarządzany asynchroniczny

.NET framework emituje zdarzenia ETW i przekazuje identyfikatory aktywności między wątkami, tak aby wywołań asynchronicznych można śledzić w wątkach. Niezarządzany kod (natywnych) i niektóre starsze style kodu asynchronicznego brakuje tych zdarzeń i identyfikatory działania dzięki program profilujący nie może sprawdzić, jakie wątku i jakie funkcje są uruchamiane w wątku. To jest oznaczona etykietą "Async niezarządzanego" w stosie wywołań. Jeśli pobierzesz plik ETW, można użyć [narzędzia PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) można uzyskać lepszy wgląd w działania wykonywane.

### <a id="cpu"></a>Czas procesora CPU

Procesor był zajęty wykonywaniem instrukcji.

### <a id="disk"></a>Czas dysku

Aplikacja wykonuje operacje dyskowe.

### <a id="network"></a>Czas sieci

Aplikacja jest przeprowadzanie operacji sieciowych.

### <a id="when"></a>Gdy kolumny

**Podczas** kolumna jest wizualizację jak WŁĄCZNYCH próbek zebranych dla węzła różnią się wraz z upływem czasu. Łączny zakres żądania jest podzielony na 32 przedziałów czasu. Włącznych próbek dla tego węzła są zbierane w tych zasobników 32. Każdego przedziału jest reprezentowany jako pasek. Wysokość paska reprezentuje skalowaną wartość. Dla węzłów, które są oznaczone **CPU_TIME** lub **BLOCKED_TIME**, lub w przypadku, gdy istnieje relacja widocznych na korzystanie z zasobów (na przykład procesora CPU, dysku lub wątek), pasek reprezentuje użycie jednego z zasoby podczas przedziału. Dla tych metryk jest można uzyskać wartość większą niż 100% za korzystanie z wielu zasobów. Na przykład jeśli używasz, średnio dwa procesory przedział czasu, Uzyskaj 200 procent.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych to pięć dni. Maksymalna dane są pozyskiwane na dzień wynosi 10 GB.

Nie istnieją żadne opłaty za korzystanie z usługi Profiler. Możesz z niej korzystać, aplikacji sieci web musi być hostowane w warstwie podstawowa funkcji Web Apps usługi Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Obciążenie i algorytm próbkowania

Profiler uruchamia losowo dwie minuty, co godzinę na każdej maszynie wirtualnej, który hostuje aplikację, która ma Profiler włączone do przechwytywania śladów. Gdy Profiler jest uruchomiona, dodaje z zakresu od 5 do 15 procent obciążenie Procesora na serwerze.

## <a name="next-steps"></a>Kolejne kroki
Włącz Application Insights Profiler do aplikacji platformy Azure. Zobacz też:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Zestawy skalowania maszyn wirtualnych i maszyn wirtualnych platformy Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
