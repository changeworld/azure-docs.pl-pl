---
title: Debugowanie aplikacji w programie Visual Studio | Dokumentacja firmy Microsoft
description: Zwiększyć niezawodność i wydajność usługi poprzez opracowywania i debugowania je w programie Visual Studio na lokalny klaster projektowy.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4582fd16d08ae8d51460dc8cabfd282e1f2cd43e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206564"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debugowanie aplikacji sieci szkieletowej usług za pomocą programu Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Debugowanie aplikacji lokalnej sieci szkieletowej usług
Wdrażanie i debugowanie aplikacji sieci szkieletowej usług Azure w klastrze programowanie komputer lokalny, można oszczędzić czas i pieniądze. Visual Studio 2017 lub programu Visual Studio 2015 można wdrożyć aplikację na lokalny klaster i automatyczne łączenie z debugera do wszystkich wystąpień aplikacji.

1. Uruchom lokalny klaster projektowy, wykonując kroki opisane w [konfigurowania środowiska deweloperskiego sieci szkieletowej usług](service-fabric-get-started.md).
2. Naciśnij klawisz **F5** lub kliknij przycisk **debugowania** > **Rozpocznij debugowanie**.
   
    ![Rozpocznij debugowanie aplikacji][startdebugging]
3. Ustaw punkty przerwania w kodzie i kroków aplikacji przez kliknięcie polecenia w **debugowania** menu.
   
   > [!NOTE]
   > Program Visual Studio jest dołączana do wszystkich wystąpień aplikacji. Gdy jest krokowe kodu, punkty przerwania napotkać uzyskać przez wiele procesów, co powoduje równoczesnych sesji. Spróbuj wyłączyć punktów przerwania po ich jest trafienie dzięki uzależnione identyfikator wątku każdego punktu przerwania lub za pomocą zdarzeń diagnostycznych.
   > 
   > 
4. **Zdarzeń diagnostycznych** automatycznie zostanie otwarte okno, aby umożliwić przeglądanie zdarzeń diagnostycznych w czasie rzeczywistym.
   
    ![Wyświetl zdarzenia diagnostycznego w czasie rzeczywistym][diagnosticevents]
5. Można również otworzyć **zdarzeń diagnostycznych** okna w Eksploratorze chmury.  W obszarze **sieci szkieletowej usług**, kliknij prawym przyciskiem myszy dowolny węzeł lub wybrać **śladów przesyłania strumieniowego widoku**.
   
    ![Otwórz okno zdarzeń diagnostycznych][viewdiagnosticevents]
   
    Jeśli chcesz filtrować dane śledzenia do określonej usługi lub aplikacji, po prostu Włącz śledzenie przesyłania strumieniowego dla tej określonej usługi lub aplikacji.
6. Zdarzenia diagnostyczne są widoczne w automatycznie wygenerowanych **ServiceEventSource.cs** pliku i jest wywoływana przez kod aplikacji.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **Zdarzeń diagnostycznych** okna obsługuje filtrowanie, wstrzymywanie i zapoznanie się zdarzenia w czasie rzeczywistym.  Filtr jest prosty ciąg wyszukiwania wiadomości zdarzeń, w tym jego zawartość.
   
    ![Filtrowanie, wstrzymać i wznowić lub sprawdzić zdarzenia w czasie rzeczywistym][diagnosticeventsactions]
8. Debugowanie usług przypomina profilowanie innej aplikacji. Łatwe debugowanie zwykle ustawi punktów przerwania za pomocą programu Visual Studio. Mimo że kolekcje niezawodnej replikowanie w wielu węzłach, nadal implementować interfejs IEnumerable. Oznacza to, że służy widok wyników w programie Visual Studio podczas debugowania aby zobaczyć, co to jest zapisany, wewnątrz. Wystarczy ustawić punkt przerwania w kodzie w dowolnym miejscu.
   
    ![Rozpocznij debugowanie aplikacji][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Debugowania zdalnego aplikacji sieci szkieletowej usług
Jeśli aplikacji sieci szkieletowej usług są uruchomione w klastrze usługi sieć szkieletowa na platformie Azure, masz możliwość je bezpośrednio z programu Visual Studio zdalne debugowanie.

> [!NOTE]
> Ta funkcja wymaga [usługi sieć szkieletowa SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla programu .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Zdalne debugowanie jest przeznaczona dla scenariusze tworzenia/testowania i nie powinna być używana w środowisku produkcyjnym ze względu na wpływ na uruchamianie aplikacji.
> 
> 

1. Przejdź do klastra w **Eksplorator chmury**, kliknij prawym przyciskiem myszy i wybierz opcję **włączenia debugowania**
   
    ![Włącz debugowanie zdalne][enableremotedebugging]
   
    Spowoduje to zaczną działać poza proces włączania rozszerzenia zdalnego debugowania na węzłach klastra oraz konfiguracji sieci wymagane.
2. Kliknij prawym przyciskiem myszy węzeł klastra w **Eksplorator chmury**i wybierz polecenie **dołączanie debugera**
   
    ![Dołączanie debugera][attachdebugger]
3. W **dołączyć do procesu** okno dialogowe, wybierz proces, który chcesz debugować, a następnie kliknij przycisk **Dołącz**
   
    ![Wybierz proces][chooseprocess]
   
    Nazwa procesu, który chcesz dołączyć, równa się nazwie nazwy zestawu projektu usługi.
   
    Debuger będzie dołączać do wszystkich węzłów uruchomiony proces.
   
   * W przypadku, gdy debugowany usługi bezstanowej wszystkie wystąpienia usługi na wszystkich węzłach są częścią sesji debugowania.
   * Jeśli debugujesz usługi stanowej repliki podstawowej dowolnej partycji będzie aktywny i w związku z tym zgłoszony przez debuger. Jeśli replika podstawowa przenosi podczas sesji debugowania, przetwarzanie ta replika nadal będzie częścią sesji debugowania.
   * Aby catch tylko odpowiednie partycji lub wystąpienia danej usługi, umożliwia warunkowych punktów przerwania stosować podział określonej partycji lub wystąpienia.
     
     ![Warunkowych punktów przerwania][conditionalbreakpoint]
     
     > [!NOTE]
     > Obecnie nie obsługujemy debugowanie klastra sieci szkieletowej usług z wielu wystąpień tej samej nazwy pliku wykonywalnego usługi.
     > 
     > 
4. Po zakończeniu debugowania aplikacji, można wyłączyć zdalnego debugowania rozszerzenia, klikając prawym przyciskiem myszy klaster w **Eksplorator chmury** i wybierz polecenie **Wyłącz debugowanie**
   
    ![Wyłącz debugowanie zdalne][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Przesyłanie strumieniowe danych śledzenia przy użyciu węzła klastra zdalnego
Jest również możliwe do strumienia ślady bezpośrednio w węźle klastra zdalnego dla programu Visual Studio. Ta funkcja umożliwia strumienia zdarzeń śledzenia funkcji ETW, utworzony na węźle klastra sieci szkieletowej usług.

> [!NOTE]
> Ta funkcja wymaga [usługi sieć szkieletowa SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla programu .NET 2.9](https://azure.microsoft.com/downloads/).
> Ta funkcja obsługuje tylko klastry działające na platformie Azure.
> 
> 

<!-- -->
> [!WARNING]
> Przesyłanie strumieniowe ślady oznacza scenariusze tworzenia/testowania i nie powinna być używana w środowisku produkcyjnym ze względu na wpływ na uruchamianie aplikacji.
> W scenariuszu produkcyjnym polegać na przekazywanie zdarzeń za pomocą diagnostyki Azure.
> 
> 

1. Przejdź do klastra w **Eksplorator chmury**, kliknij prawym przyciskiem myszy i wybierz opcję **włączyć śledzenie przesyłania strumieniowego**
   
    ![Włącz zdalne śladów przesyłania strumieniowego][enablestreamingtraces]
   
    Będzie to zaczną działać poza proces włączania przesyłania strumieniowego rozszerzenia ślady na węzłach klastra, jak również wymaganych konfiguracjach.
2. Rozwiń węzeł **węzłów** element **Eksplorator chmury**, kliknij prawym przyciskiem myszy węzeł chcesz przesyłać strumieniowo dane śledzenia z, a następnie wybierz pozycję **widoku śladów przesyłania strumieniowego**
   
    ![Widok zdalnego przesyłania strumieniowego śledzenia][viewremotestreamingtraces]
   
    Powtórz krok 2 dla dowolną liczbę węzłów wyświetlić dane śledzenia z. Każdego strumienia węzłów zostaną wyświetlone w oknie dedykowanych.
   
    Jesteś teraz widoczne ślady emitowane przez usługi Service Fabric i usług. Jeśli chcesz filtrować zdarzenia, aby wyświetlić tylko określonej aplikacji, po prostu wpisz nazwę aplikacji w filtrze.
   
    ![Wyświetlanie przesyłania strumieniowego śledzenia][viewingstreamingtraces]
3. Po zakończeniu przesyłania strumieniowego śladów z klastra, można wyłączyć zdalnego ślady przesyłania strumieniowego, klikając prawym przyciskiem myszy klaster w **Eksplorator chmury** i wybierz polecenie **Wyłącz śledzenie przesyłania strumieniowego**
   
    ![Wyłącz zdalne śladów przesyłania strumieniowego][disablestreamingtraces]

## <a name="next-steps"></a>Kolejne kroki
* [Testowanie usługi sieć szkieletowa usług](service-fabric-testability-overview.md).
* [Zarządzaj aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
