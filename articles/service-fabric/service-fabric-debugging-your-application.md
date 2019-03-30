---
title: Debugowanie aplikacji w programie Visual Studio | Dokumentacja firmy Microsoft
description: Zwiększ niezawodność i wydajność usługi, opracowywania i debugowania ich w programie Visual Studio na lokalny klaster projektowy.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9fbd9b8e298713dad022196989027f9e43ce806d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667739"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Debugowanie lokalne aplikacji usługi Service Fabric
Przez wdrożenie i profilowanie aplikacji usługi Azure Service Fabric w klastrze programowania komputera lokalnego, można zaoszczędzić czas i pieniądze. Visual Studio 2017 lub Visual Studio 2015, można wdrożyć aplikację w klastrze lokalnym i automatycznie nawiążą połączenie debugera do wszystkich wystąpień aplikacji; Visual Studio, należy uruchomić jako Administrator, aby połączyć z debugera.

1. Uruchom lokalny klaster projektowy, wykonując kroki opisane w [Konfigurowanie środowiska deweloperskiego usługi Service Fabric](service-fabric-get-started.md).
2. Naciśnij klawisz **F5** lub kliknij przycisk **debugowania** > **Rozpocznij debugowanie**.
   
    ![Rozpocznij debugowanie aplikacji][startdebugging]
3. Ustawianie punktów przerwania w kodzie i kroku za pośrednictwem aplikacji, klikając przycisk polecenia w **debugowania** menu.
   
   > [!NOTE]
   > Program Visual Studio dołącza do wszystkich wystąpień aplikacji. Gdy jesteś wykonywanie kodu, punkty przerwania napotkać pobieranie przez wiele procesów skutkuje równoczesnych sesji. Spróbuj wyłączyć punkty przerwania, po ich one trafień, dokonując uwarunkowany identyfikator wątku każdego punktu przerwania lub za pomocą zdarzeń diagnostycznych.
   > 
   > 
4. **Zdarzenia diagnostyczne** automatycznie zostanie otwarte okno, aby można było wyświetlić zdarzeń diagnostycznych w czasie rzeczywistym.
   
    ![Wyświetlanie zdarzeń diagnostycznych w czasie rzeczywistym][diagnosticevents]
5. Możesz również otworzyć **zdarzenia diagnostyczne** okna w programie Cloud Explorer.  W obszarze **usługi Service Fabric**, kliknij prawym przyciskiem myszy dowolny węzeł i wybierz polecenie **ślady przesyłania strumieniowego widoku**.
   
    ![Otwórz okno zdarzeń diagnostycznych][viewdiagnosticevents]
   
    Jeśli chcesz filtrować swoje informacje śledzenia do określonej usługi lub aplikacji, po prostu włącz przesyłania strumieniowego danych śledzenia dla tej określonej usługi lub aplikacji.
6. Zdarzenia diagnostyczne są widoczne w automatycznie wygenerowany **ServiceEventSource.cs** pliku i jest wywoływana przez kod aplikacji.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **Zdarzenia diagnostyczne** okna obsługuje filtrowanie, wstrzymywania i inspekcja zdarzeń w czasie rzeczywistym.  Filtr jest prosty ciąg wyszukiwania wiadomości zdarzeń, w tym jego zawartość.
   
    ![Filtrowanie, wstrzymać i wznowić lub sprawdzić zdarzenia w czasie rzeczywistym][diagnosticeventsactions]
8. Debugowanie usług jest podobne do debugowania innych aplikacji. Łatwe debugowanie, będzie zwykle ustawić punkty przerwania, za pomocą programu Visual Studio. Mimo że elementów Reliable Collections replikacja w wielu węzłach, nadal implementować interfejs IEnumerable. Oznacza to, że służy widok wyników w programie Visual Studio podczas debugowania aby zobaczyć, co to jest zapisany, wewnątrz. Po prostu ustaw punkt przerwania w kodzie w dowolnym miejscu.
   
    ![Rozpocznij debugowanie aplikacji][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Debugowanie zdalnej aplikacji usługi Service Fabric
Jeśli aplikacje usługi Service Fabric są uruchomione w klastrze usługi Service Fabric na platformie Azure, jest możliwe zdalne debugowanie je bezpośrednio z programu Visual Studio.

> [!NOTE]
> Ta funkcja wymaga [usługi Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla platformy .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Zdalne debugowanie jest przeznaczona dla scenariuszy deweloperskich lub testowych i nie może być używane w środowisku produkcyjnym ze względu na wpływ na uruchamianie aplikacji.
> 
> 

1. Przejdź do klastra w **programu Cloud Explorer**, kliknij prawym przyciskiem myszy i wybierz polecenie **włączenia debugowania**
   
    ![Włączanie debugowania zdalnego][enableremotedebugging]
   
    Spowoduje to rozpoczęcie procesu włączania zdalnego debugowania rozszerzenia na węzłach klastra oraz konfiguracji sieci wymagane.
2. Kliknij prawym przyciskiem myszy węzeł klastra w **programu Cloud Explorer**i wybierz polecenie **dołączanie debugera**
   
    ![Dołącz debuger][attachdebugger]
3. W **dołączyć do procesu** okno dialogowe, wybierz proces, którego chcesz debugować, a następnie kliknij przycisk **dołączania**
   
    ![Wybierz proces][chooseprocess]
   
    Nazwa procesu, który chcesz dołączyć, jest równa nazwa Twojej nazwy zestawu projektu usługi.
   
    Debuger ma być dołączone do wszystkich węzłów, w którym jest uruchomiony proces.
   
   * W przypadku, gdy debugujesz usługę bezstanową wszystkie wystąpienia usługi we wszystkich węzłach są częścią sesji debugowania.
   * Jeśli debugujesz usługi stanowej repliki podstawowej dowolnej partycji zostanie uaktywniony i w związku z tym przechwycony przez debuger. Jeśli replika podstawowa przesuwa się podczas sesji debugowania, przetwarzanie tej repliki bazy danych nadal będzie częścią sesji debugowania.
   * Aby można było przechwytywać tylko odpowiednie partycje lub wystąpieniami danej usługi, warunkowe punkty przerwania można użyć tylko przerwanie określonej partycji lub wystąpienia.
     
     ![Warunkowego punktu przerwania][conditionalbreakpoint]
     
     > [!NOTE]
     > Obecnie nie obsługujemy debugowania klaster usługi Service Fabric z wieloma wystąpieniami tej samej nazwy pliku wykonywalnego usługi.
     > 
     > 
4. Po zakończeniu wprowadzania zmian do debugowania aplikacji, klikając prawym przyciskiem myszy klaster, można wyłączyć zdalnego debugowania rozszerzenia **programu Cloud Explorer** i wybierz polecenie **Wyłącz debugowanie**
   
    ![Wyłącz debugowanie zdalne][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Przesyłanie strumieniowe danych śledzenia z węzła klastra zdalnego
Jest również możliwe do strumienia danych śledzenia bezpośrednio z poziomu węzła klastra zdalnego programu Visual Studio. Ta funkcja umożliwia strumienia zdarzeń śledzenia funkcji ETW, utworzone w węźle klastra usługi Service Fabric.

> [!NOTE]
> Ta funkcja wymaga [usługi Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawu Azure SDK dla platformy .NET 2.9](https://azure.microsoft.com/downloads/).
> Ta funkcja obsługuje tylko klastrów działających na platformie Azure.
> 
> 

<!-- -->
> [!WARNING]
> Przesyłanie strumieniowe danych śledzenia jest przeznaczona dla scenariuszy deweloperskich lub testowych i nie może być używane w środowisku produkcyjnym ze względu na wpływ na uruchamianie aplikacji.
> W scenariuszu produkcji będą miały funkcji przekazywania zdarzeń za pomocą usługi Azure Diagnostics.
> 
> 

1. Przejdź do klastra w **programu Cloud Explorer**, kliknij prawym przyciskiem myszy i wybierz polecenie **Włączanie przesyłania strumieniowego danych śledzenia**
   
    ![Włącz zdalne ślady przesyłania strumieniowego][enablestreamingtraces]
   
    Spowoduje to rozpoczęcie procesu Włączanie przesyłania strumieniowego rozszerzenia ślady na węzłach klastra oraz konfiguracji sieci wymagane.
2. Rozwiń **węzłów** element **Eksploratora chmury**, kliknij prawym przyciskiem myszy węzeł, aby śladów ze strumienia, a następnie wybierz **wyświetlanie śladów przesyłania strumieniowego**
   
    ![Wyświetl zdalnego, przesyłanie strumieniowe danych śledzenia][viewremotestreamingtraces]
   
    Powtórz krok 2 dla dowolną liczbę węzłów wyświetlić ślady z. Każdego strumienia węzłów pojawią się w dedykowanym oknie.
   
    Teraz jesteś w stanie wyświetlić ślady wyemitowane przez usługi Service Fabric i usługi. Jeśli chcesz filtrować zdarzenia, aby wyświetlić tylko określonej aplikacji, po prostu wpisz nazwę aplikacji w filtrze.
   
    ![Wyświetlanie, przesyłanie strumieniowe danych śledzenia][viewingstreamingtraces]
3. Po zakończeniu przesyłania strumieniowego ślady z klastrem, możesz wyłączyć zdalnego ślady przesyłania strumieniowego, klikając prawym przyciskiem myszy klaster **programu Cloud Explorer** i wybierz polecenie **wyłączyć ślady przesyłania strumieniowego**
   
    ![Wyłącz zdalne ślady przesyłania strumieniowego][disablestreamingtraces]

## <a name="next-steps"></a>Kolejne kroki
* [Testowanie usługi Service Fabric](service-fabric-testability-overview.md).
* [Zarządzaj aplikacjami usługi Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
