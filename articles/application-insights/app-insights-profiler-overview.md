---
title: Profil aplikacji produkcyjnych na platformie Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: bba15ee901e7ccecc513f526339bde2fcc6277fa
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142574"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil aplikacji produkcyjnych na platformie Azure za pomocą usługi Application Insights
## <a name="enable-profiler-for-your-application"></a>Włącz Profiler do aplikacji

Application Insights Profiler zawiera ślady wydajności dla aplikacji działających w środowisku produkcyjnym na platformie Azure. Przechwytuje dane automatycznie w odpowiedniej skali bez wywierania negatywnego wpływu na użytkowników końcowych. Profiler pomoże zidentyfikować ścieżka kodu "gorącymi", która zajmuje najwięcej czasu podczas obsługi namierzenie internetowego żądania. 

Program profilujący w programach .net wdrożonych na następujących usług platformy Azure. Szczegółowe instrukcje dotyczące włączania profiler dla każdego typu usług są poniższe linki.

* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne i Scalesets maszyny Wirtualnej](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Jeśli włączono Profiler, ale nie widać ślady, Sprawdź nasze [przewodnik rozwiązywania problemów.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

Uruchamianie Profiler w środowisku lokalnym nie jest oficjalnie obsługiwane, ale mamy coś [instrukcje, możesz wypróbować.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Wyświetlanie danych profilera

Aby program profilujący do przekazania danych śledzenia żądań aplikacji aktywnie obsługi. Jeśli wykonujesz eksperymentu można wygenerować żądania do aplikacji sieci web przy użyciu [test wydajności, Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Jeśli Profiler nowo zostało włączone, możesz uruchomić test obciążenia krótki. Po uruchomieniu testu obciążenia, należy nacisnąć klawisz **profilu teraz** znajdujący się w [ **strony ustawień Profiler**](). Gdy profiler jest uruchomiona, jej będzie profilu losowo o co godzinę i czas trwania wynosi dwie minuty. Jeśli aplikacja obsługuje ciągłego strumienia żądań, Profiler przekaże ślady co godzinę.

Po aplikacja odbiera część ruchu i profiler miał czas przekazywania trances, powinny być śladów, aby wyświetlić. To może zająć 5 – 10 minut. Aby wyświetlić ślady, przejdź do **wydajności** okienku wybierz **podjąć akcje** wyświetlić ślady narzędzia profiler, a następnie wybierz pozycję **ślady Profiler** przycisku.

![Śledzi Profiler Podgląd okienko Insights wydajności aplikacji][performance-blade]

Wybierz próbkę, aby wyświetlić podział na poziomie kodu czas wykonywania żądania.

![Eksplorator śledzenia usługi Application Insights][trace-explorer]

W Eksploratorze śledzenia zawiera następujące informacje:

* **Pokaż ścieżkę aktywną**: Otwiera największą liść węzła lub co najmniej coś zamknąć. W większości przypadków ten węzeł zbliża się wąskim gardłem wydajności.
* **Etykieta**: Nazwa funkcji lub zdarzeń. W drzewie wyświetlany kombinacji kodu i zdarzenia, które wystąpiły (takich jak SQL i HTTP zdarzenia). Najważniejsze zdarzenia reprezentuje całkowity czas trwania żądania.
* **Upłynęło**: interwał czasu między rozpoczęciem operacji i koniec operacji.
* **Gdy**: czas po funkcji lub zdarzeń była uruchomiona w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak odczytać dane dotyczące wydajności

Narzędzie profiler usługi firmy Microsoft używa kombinacji metody próbkowania i instrumentacji do analizowania wydajności aplikacji. Szczegółowe kolekcji jest w toku, narzędzie profiler usługi przykłady wskaźnik instrukcji dla każdego Procesora maszyny każdego milisekund. Każda próbka przechwytuje pełny stos wywołań wątku, który jest w trakcie wykonywania. Daje ona szczegółowe informacje na temat ten wątek został działania, zarówno na wysokim poziomie, jak i na niskim poziomie abstrakcji. Narzędzie profiler usługi zbiera również inne zdarzenia do śledzenia działania korelacji i przyczynowości, łącznie z kontekstem przełączanie zdarzeń, zdarzenia Biblioteka zadań równoległych (TPL) i zdarzenia puli wątków.

Stos wywołań, który jest wyświetlany w widoku osi czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje pełny stos wywołań wątku, zawiera kod z programu Microsoft .NET Framework i z innych platform, które odwołują się.

### <a id="jitnewobj"></a>Alokacja obiektu wspólnego (clr! JIT\_nowy lub clr! JIT\_Newarr1)

**CLR! JIT\_New** i **clr! JIT\_Newarr1** funkcji pomocnika w .NET Framework, które alokować pamięci z zarządzanego stosu. **CLR! JIT\_New** jest wywoływane, gdy obiekt jest przydzielany. **CLR! JIT\_Newarr1** jest wywoływane, gdy tablica obiektów została przydzielona. Te dwie funkcje są zazwyczaj szybkie i stosunkowo małe ilości czasu. Jeśli widzisz **clr! JIT\_New** lub **clr! JIT\_Newarr1** zająć znaczną ilość czasu na Twojej osi czasu, oznacza to, że kod może być przydzielanie wielu obiektów i wykorzystywanie znacznej ilości pamięci.

### <a id="theprestub"></a>Trwa ładowanie kodu języka wspólnego (clr! ThePreStub)

**CLR! ThePreStub** jest funkcji pomocnika, która w .NET Framework, który przygotowuje kodu wykonywanego po raz pierwszy. To zwykle obejmują, ale nie ogranicza się do kompilacji just-in-time (JIT). Dla każdego metodę języka C# **clr! ThePreStub** powinna ona zostać wywołana najwyżej jeden raz w okresie istnienia procesu.

Jeśli **clr! ThePreStub** zajmuje dużo czasu na żądanie, oznacza to, żądanie jest to pierwsza, która wykonuje tę metodę. Czas dla .NET Framework środowiska uruchomieniowego należy załadować pierwszej metody jest znaczący. Należy rozważyć, za pomocą procesu rozgrzewania, który jest wykonywany przed użytkownikom uzyskać do niego dostęp, lub Rozważ uruchomienie Native Image Generator (ngen.exe) część kodu na swoje zestawy.

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

## <a name="next-steps"></a>Następne kroki
Włącz Application Insights Profiler dla aplikacji platformy Azure
* [App Services](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne i Scalesets maszyny Wirtualnej](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
