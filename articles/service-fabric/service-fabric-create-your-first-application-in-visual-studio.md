---
title: Tworzenie niezawodnej usługi Azure Service Fabric w języku C#
description: Twórz, wdrażaj i debuguj aplikację usług Reliable Services skompilowaną w usłudze Azure Service Fabric przy użyciu programu Visual Studio.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: 7dadaadd0e6a6e6d71685356568076ad26305cc2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642227"
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Tworzenie pierwszej aplikacji stanowych usług Reliable Services w usłudze Service Fabric w języku C#

Dowiedz się, jak wdrożyć pierwszą aplikację usługi Azure Service Fabric dla platformy .NET w systemie Windows w ciągu kilku minut. Po zakończeniu będziesz mieć lokalny klaster uruchamiany z aplikacją usług Reliable Services.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz [skonfigurowane środowisko programowania](service-fabric-get-started.md). Ten proces obejmuje również zainstalowanie zestawu SDK usługi Service Fabric i programu Visual Studio 2017 lub 2015.

## <a name="create-the-application"></a>Tworzenie aplikacji

1. Uruchom program Visual Studio jako administrator.

2. Utwórz projekt, naciskając klawisze Ctrl+Shift+N.

3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Chmura** > **Aplikacja usługi Service Fabric**.

4. Nazwij aplikację **MyApplication**. Następnie wybierz przycisk **OK**.

   ![Okno dialogowe nowego projektu w programie Visual Studio][1]

5. W następnym oknie dialogowym można utworzyć aplikację usługi Service Fabric dowolnego typu. W przypadku tego przewodnika Szybki start wybierz pozycję **.Net Core 2.0** > **Usługa stanowa**.

6. Nazwij usługę **MyStatefulService**. Następnie wybierz przycisk **OK**.

    ![Okno dialogowe nowej usługi w programie Visual Studio][2]

    Program Visual Studio tworzy projekt aplikacji i projekt usługi stanowej. Następnie wyświetla je w Eksploratorze rozwiązań.

    ![Eksplorator rozwiązań po utworzeniu aplikacji z usługą stanową][3]

    Projekt aplikacji (**MyApplication**) nie zawiera żadnego kodu. Zamiast tego odwołuje się do zestawu projektów usług. Ma także trzy inne typy zawartości:

    * **Profile publikowania**  
    Profile na potrzeby wdrażania w różnych środowiskach.

    * **Skrypty**  
    Skrypty programu PowerShell przeznaczone do wdrażania i uaktualniania aplikacji.

    * **Definicja aplikacji**  
W obszarze *ApplicationPackageRoot* znajduje się plik ApplicationManifest.xml, który opisuje kompozycję aplikacji. Skojarzone pliki parametrów aplikacji znajdują się w obszarze *ApplicationParameters* i mogą służyć do określania parametrów związanych z danym środowiskiem. Program Visual Studio wybiera plik parametrów aplikacji określony w skojarzonym profilu publikowania.
    
Aby zapoznać się z omówieniem zawartości projektu usługi, zobacz [Pierwsze kroki z usługami Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Wdrażanie i debugowanie aplikacji

Teraz, gdy już masz aplikację, przeprowadź jej uruchamianie, wdrażanie i debugowanie, wykonując poniższe kroki.

1. W programie Visual Studio, wybierz **F5** wdrożyć aplikację do debugowania.  Kliknij przycisk **tak** Jeśli wyświetlony komunikat z pytaniem udzielić "ServiceFabricAllowedUsers" grupy odczyt i wykonywanie uprawnień do katalogu projektu programu Visual Studio.

    >[!NOTE]
    >Przy pierwszym uruchamianiu i wdrażaniu aplikacji w środowisku lokalnym program Visual Studio tworzy lokalny klaster na potrzeby debugowania. Może to potrwać jakiś czas. Stan tworzenia klastra jest wyświetlany w oknie danych wyjściowych programu Visual Studio.
    
     Gdy klaster jest gotowy, z aplikacji znajdującej się w zestawie SDK otrzymasz powiadomienie menedżera paska zadań klastra lokalnego.
     
    >[!NOTE]
    >Wymaga to ćwiczenie 5 węzła (vs. Węzeł 1) klastra. Można to sprawdzić w następujący sposób: uruchomienie narzędzia Service Fabric Explorer, klikając prawym przyciskiem myszy **usługi sieć szkieletowa Local Cluster Manager** aplikacji na pasku zadań systemu, a następnie kliknij przycisk **Przełącz tryb klastra**. Kliknij przycisk **5 węzła** Jeśli wybrano 1 węzła.
    
    ![Powiadomienie klastra lokalnego na pasku zadań][4]

    Po uruchomieniu aplikacji program Visual Studio automatycznie wyświetla okno podglądu zdarzeń diagnostycznych, w którym można zobaczyć wyniki śledzenia usług.
    
    ![Podgląd zdarzeń diagnostycznych][5]

    >[!NOTE]
    >Zdarzenia powinny automatycznie uruchamiać śledzenie w Podglądzie zdarzeń diagnostycznych. Jeśli Podgląd zdarzeń diagnostycznych wymaga ręcznego skonfigurowania, najpierw otwórz plik `ServiceEventSource.cs` znajdujący się w projekcie **MyStatefulService**. Skopiuj wartość atrybutu `EventSource` u góry klasy `ServiceEventSource`. W poniższym przykładzie źródło zdarzenia ma nazwę `"MyCompany-MyApplication-MyStatefulService"` (u Ciebie może ona być inna).
>
    >![Lokalizowanie nazwy źródła zdarzeń usługi][service-event-source-name]


2. W dalszej kolejności otwórz okno dialogowe **Dostawcy ETW**. Następnie wybierz ikonę koła zębatego, która znajduje się na karcie **Zdarzenia diagnostyczne**. Wklej nazwę skopiowanego źródła zdarzeń do pola wejściowego **Dostawcy ETW**. Następnie naciśnij przycisk **Zastosuj**. To spowoduje automatyczne rozpoczęcie śledzenia zdarzeń.

    ![Ustawianie nazwy źródła zdarzeń diagnostycznych][setting-event-source-name]

    W oknie Zdarzenia diagnostyczne powinny się teraz zacząć pojawiać zdarzenia.

    Szablon usługi stanowej przedstawia wartość licznika, która zwiększa się w metodzie `RunAsync` w pliku **MyStatefulService.cs**.

3. Rozwiń jedno ze zdarzeń, aby zobaczyć więcej szczegółów, w tym węzeł, w którym wykonywany jest kod. W tym przypadku jest to węzeł **\_Node\_0**, ale może się to różnić w zależności od maszyny.
   
    ![Szczegóły w podglądzie zdarzeń diagnostycznych][6]

4. Klaster lokalny zawiera pięć węzłów, które są hostowane na jednej maszynie. W środowisku produkcyjnym każdy węzeł jest hostowany na odrębnej maszynie fizycznej lub wirtualnej. Wyłączmy jeden z węzłów w klastrze lokalnym, aby zasymulować utratę maszyny w czasie działania debugera programu Visual Studio.

5. W oknie **Eksplorator rozwiązań** otwórz plik **MyStatefulService.cs**. 

6. Znajdź metodę `RunAsync`, a następnie ustaw punkt przerwania w pierwszym wierszu metody.

    ![Punkt przerwania w metodzie RunAsync usługi stanowej][7]

7. Uruchom narzędzie Service Fabric Explorer, klikając prawym przyciskiem myszy **usługi sieć szkieletowa Local Cluster Manager** aplikacji na pasku zadań systemu, a następnie wybierając **Zarządzaj klastrem lokalnym**.

    ![Uruchamianie narzędzia Service Fabric Explorer z menedżera klastra lokalnego][systray-launch-sfx]

    Narzędzie [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) oferuje wizualną reprezentację klastra. Zawiera ono zestaw aplikacji wdrożonych w klastrze oraz zestaw składających się na niego węzłów fizycznych.

8. W lewym okienku rozwiń pozycje **Klaster** > **Węzły** i znajdź węzeł, w którym wykonywany jest kod. Następnie, aby zasymulować ponowne uruchomienie maszyny, wybierz pozycję **Akcje** > **Dezaktywuj (ponowne uruchomienie)**.

    ![Zatrzymanie węzła w narzędziu Service Fabric Explorer][sfx-stop-node]

    Chwilowo powinien być widoczny trafiony punkt przerwania w programie Visual Studio, gdy obliczenia wykonywane w jednym węźle zostaną awaryjnie przełączone na inny węzeł. Naciśnij klawisz **F5** aby kontynuować.

9. Następnie wróć do podglądu zdarzeń diagnostycznych i sprawdź komunikaty. Wartość licznika nadal wzrastała, mimo że zdarzenia w rzeczywistości pochodzą z innego węzła.

    ![Szczegóły w podglądzie zdarzeń diagnostycznych po przełączeniu do trybu failover][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Czyszczenie klastra lokalnego (opcjonalnie)

Pamiętaj, że ten klaster lokalny to klaster rzeczywisty. Zatrzymanie debugera powoduje usunięcie wystąpienia aplikacji i wyrejestrowanie typu aplikacji. Klaster będzie jednak nadal uruchomiony w tle. Jeśli chcesz zatrzymać klaster lokalny, możesz to zrobić na kilka sposobów.

### <a name="keep-application-and-trace-data"></a>Przechowywanie danych aplikacji i śledzenia

Zamknij klaster, klikając prawym przyciskiem myszy aplikację **Menedżer klastra lokalnego** na pasku zadań, a następnie wybierając polecenie **Zatrzymaj klaster lokalny**.

### <a name="delete-the-cluster-and-all-data"></a>Usuwanie klastra i wszystkich danych

Usuń klaster, klikając prawym przyciskiem myszy aplikację **Menedżer klastra lokalnego** na pasku zadań. Następnie wybierz pozycję **Usuń klaster lokalny**. 

Jeśli wybierzesz tę opcję, program Visual Studio ponownie wdroży klaster po następnym uruchomieniu aplikacji. Wybierz tę opcję tylko wtedy, gdy nie będziesz używać klastra lokalnego przez pewien czas lub gdy chcesz odzyskać zasoby.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usługach [Reliable Services](service-fabric-reliable-services-introduction.md).
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
