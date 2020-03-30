---
title: Debugowanie aplikacji w programie Visual Studio
description: Zwiększ niezawodność i wydajność usług, opracowując i debugując je w programie Visual Studio w lokalnym klastrze deweloperów.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624136"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Zaćmienie/Jawa](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Debugowanie lokalnej aplikacji sieci szkieletowej usług
Możesz zaoszczędzić czas i pieniądze, wdrażając i debugując aplikację usługi Azure Service Fabric w lokalnym klastrze deweloperskim komputera. Visual Studio 2019 lub 2015 można wdrożyć aplikację do klastra lokalnego i automatycznie połączyć debugera do wszystkich wystąpień aplikacji. Visual Studio musi być uruchomiony jako administrator, aby połączyć debugera.

1. Uruchom klaster lokalnego rozwoju, wykonując kroki opisane w [ustawieniach konfigurowania środowiska programistycznego sieci szkieletowej usług](service-fabric-get-started.md).
2. Naciśnij **klawisz F5** lub kliknij przycisk **Debugowanie** > **rozpocznij debugowanie**.
   
    ![Uruchamianie debugowania aplikacji][startdebugging]
3. Ustaw punkty przerwania w kodzie i krok po aplikacji, klikając polecenia w menu **debugowania.**
   
   > [!NOTE]
   > Visual Studio dołącza do wszystkich wystąpień aplikacji. Podczas przechodzenia przez kod, punkty przerwania może zostać trafiony przez wiele procesów, co powoduje równoczesnych sesji. Spróbuj wyłączyć punkty przerwania po ich trafieniu, uzależniając każdy punkt przerwania od identyfikatora wątku lub przy użyciu zdarzeń diagnostycznych.
   > 
   > 
4. Okno **Zdarzenia diagnostyczne** zostanie automatycznie otwarte, dzięki czemu można wyświetlać zdarzenia diagnostyczne w czasie rzeczywistym.
   
    ![Wyświetlanie zdarzeń diagnostycznych w czasie rzeczywistym][diagnosticevents]
5. Okno **Zdarzenia diagnostyczne** można również otworzyć w Eksploratorze chmury.  W obszarze **Sieci szkieletowej usług**kliknij prawym przyciskiem myszy dowolny węzeł i wybierz polecenie **Wyświetl ślady przesyłania strumieniowego**.
   
    ![Otwieranie okna zdarzeń diagnostycznych][viewdiagnosticevents]
   
    Jeśli chcesz filtrować ślady do określonej usługi lub aplikacji, włącz śledzenie przesyłania strumieniowego w tej określonej usłudze lub aplikacji.
6. Zdarzenia diagnostyczne można zobaczyć w automatycznie generowanym pliku **ServiceEventSource.cs** i są wywoływane z kodu aplikacji.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Okno **Zdarzenia diagnostyczne** obsługuje filtrowanie, wstrzymywanie i sprawdzanie zdarzeń w czasie rzeczywistym.  Filtr jest prostym wyszukiwaniem ciągu komunikatu zdarzenia, w tym jego zawartości.
   
    ![Filtrowanie, wstrzymywanie i wznawianie zdarzeń lub sprawdzanie zdarzeń w czasie rzeczywistym][diagnosticeventsactions]
8. Debugowanie usługi jest jak debugowanie innych aplikacji. Zwykle można ustawić punkty przerwania za pośrednictwem programu Visual Studio dla łatwego debugowania. Mimo że niezawodne kolekcje replikują się w wielu węzłach, nadal implementują IEnumerable. Ta implementacja oznacza, że można użyć widoku wyników w programie Visual Studio podczas debugowania, aby zobaczyć, co zostało zapisane w środku. Aby to zrobić, należy ustawić punkt przerwania w dowolnym miejscu w kodzie.
   
    ![Uruchamianie debugowania aplikacji][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Uruchamianie skryptu w ramach debugowania
W niektórych scenariuszach może być konieczne uruchomienie skryptu w ramach uruchamiania sesji debugowania (np. gdy nie jest używany usług domyślnych).

W programie Visual Studio można dodać plik o nazwie **Start-Service.ps1** w folderze **Skrypty** projektu aplikacji sieci szkieletowej usług (.sfproj). Ten skrypt zostanie wywołany po utworzeniu aplikacji w klastrze lokalnym.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Debugowanie zdalnej aplikacji sieci szkieletowej usług
Jeśli aplikacje sieci szkieletowej usług są uruchomione w klastrze sieci szkieletowej usług na platformie Azure, można zdalnie debugować te aplikacje bezpośrednio z programu Visual Studio.

> [!NOTE]
> Ta funkcja wymaga [SDK 2.0 sieci szkieletowej usług](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawów SDK platformy Azure dla platformy .NET 2.9.](https://azure.microsoft.com/downloads/)    

<!-- -->
> [!WARNING]
> Zdalne debugowanie jest przeznaczony do scenariuszy deweloperskich/testowych i nie może być używane w środowiskach produkcyjnych, ze względu na wpływ na uruchomione aplikacje.

1. Przejdź do klastra w **Eksploratorze chmury**. Kliknij prawym przyciskiem myszy i wybierz polecenie **Włącz debugowanie**
   
    ![Włączanie zdalnego debugowania][enableremotedebugging]
   
    Ta akcja spowoduje rozpoczęcie procesu włączania rozszerzenia zdalnego debugowania w węzłach klastra i wymaganych konfiguracjach sieci.
2. Kliknij prawym przyciskiem myszy węzeł klastra w **Eksploratorze chmury**i wybierz polecenie **Dołącz debuger**
   
    ![Dołączanie debugera][attachdebugger]
3. W oknie dialogowym **Dołącz do procesu** wybierz proces, który chcesz debugować, a następnie kliknij przycisk **Dołącz**
   
    ![Wybieranie procesu][chooseprocess]
   
    Nazwa procesu, do którego chcesz dołączyć, jest równa nazwie zestawu projektu usługi.
   
    Debuger dołączy do wszystkich węzłów uruchomionych proces.
   
   * W przypadku, gdy debugowanie usługi bezstanowej, wszystkie wystąpienia usługi we wszystkich węzłach są częścią sesji debugowania.
   * Jeśli debugowanie usługi stanowej, tylko podstawowa replika dowolnej partycji będzie aktywna i w związku z tym przechwycone przez debugera. Jeśli replika podstawowa zostanie przesunięta podczas sesji debugowania, przetwarzanie tej repliki nadal będzie częścią sesji debugowania.
   * Aby przechwytywać tylko odpowiednie partycje lub wystąpienia danej usługi, można użyć warunkowych punktów przerwania, aby tylko przerwać określoną partycję lub wystąpienie.
     
     ![Warunkowy punkt przerwania][conditionalbreakpoint]
     
     > [!NOTE]
     > Obecnie nie obsługujemy debugowania klastra sieci szkieletowej usług z wieloma wystąpieniami o tej samej nazwie wykonywalnej usługi.
     > 
     > 
4. Po zakończeniu debugowania aplikacji można wyłączyć rozszerzenie zdalnego debugowania, klikając prawym przyciskiem myszy klaster w **Eksploratorze chmury** i wybrać **opcję Wyłącz debugowanie**
   
    ![Wyłączanie zdalnego debugowania][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Przesyłanie strumieniowe śladów z węzła klastra zdalnego
Można również przesyłać strumieniowo ślady bezpośrednio z węzła klastra zdalnego do programu Visual Studio. Ta funkcja umożliwia przesyłanie strumieniowe zdarzeń śledzenia ETW, wyprodukowanych w węźle klastra sieci szkieletowej usług.

> [!NOTE]
> Ta funkcja wymaga [SDK 2.0 sieci szkieletowej usług](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) i [zestawów SDK platformy Azure dla platformy .NET 2.9.](https://azure.microsoft.com/downloads/)
> Ta funkcja obsługuje tylko klastry uruchomione na platformie Azure.
> 
> 

<!-- -->
> [!WARNING]
> Śledzenie przesyłania strumieniowego jest przeznaczony dla scenariuszy deweloperskich/testowych i nie może być używane w środowiskach produkcyjnych, ze względu na wpływ na uruchomione aplikacje.
> W scenariuszu produkcyjnym należy polegać na przekazywanie zdarzeń przy użyciu diagnostyki platformy Azure.

1. Przejdź do klastra w **Eksploratorze chmury**. Kliknij prawym przyciskiem myszy i wybierz pozycję **Włącz śledzenie przesyłania strumieniowego**
   
    ![Włączanie zdalnych śledzenia przesyłania strumieniowego][enablestreamingtraces]
   
    Ta akcja rozpocznie proces włączania rozszerzenia śledzenia przesyłania strumieniowego w węzłach klastra, a także wymaganych konfiguracji sieci.
2. Rozwiń element **Węzły** w **Eksploratorze chmury**, kliknij prawym przyciskiem myszy węzeł, z którego chcesz przesyłać strumieniowo ślady, i wybierz polecenie **Wyświetl ślady przesyłania strumieniowego**
   
    ![Wyświetlanie śladów zdalnego przesyłania strumieniowego][viewremotestreamingtraces]
   
    Powtórz krok 2 dla tylu węzłów, z których chcesz zobaczyć ślady. Każdy strumień węzłów pojawi się w dedykowanym oknie.
   
    Teraz można zobaczyć ślady emitowane przez sieci szkieletowej usług i usług. Jeśli chcesz filtrować zdarzenia, aby wyświetlić tylko określoną aplikację, po prostu wpisz nazwę aplikacji w filtrze.
   
    ![Wyświetlanie śladów przesyłania strumieniowego][viewingstreamingtraces]
3. Po zakończeniu przesyłania strumieniowego śledzenia z klastra można wyłączyć zdalne śledzenie przesyłania strumieniowego, klikając prawym przyciskiem myszy klaster w **Eksploratorze chmury** i wybierając **pozycję Wyłącz śledzenie przesyłania strumieniowego**
   
    ![Wyłączanie zdalnych śladów przesyłania strumieniowego][disablestreamingtraces]

## <a name="next-steps"></a>Następne kroki
* [Przetestuj usługę sieci szkieletowej](service-fabric-testability-overview.md)usług .
* [Zarządzanie aplikacjami sieci szkieletowej usług w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).

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
