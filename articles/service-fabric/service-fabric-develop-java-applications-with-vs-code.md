---
title: Twórz aplikacje Java usługi Azure Service Fabric za pomocą programu Visual Studio Code | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak tworzenie, wdrażanie i debugowanie aplikacji Java usługi Service Fabric przy użyciu programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 7f60371fb533526ef5bdb154d0c08dface9c0d1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393950"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Twórz aplikacje Java usługi Service Fabric za pomocą programu Visual Studio Code

[Rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji Java usługi Service Fabric w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł pokazuje, jak tworzenie, wdrażanie i debugowanie aplikacji Java usługi Service Fabric przy użyciu programu Visual Studio Code.

> [!IMPORTANT]
> Aplikacje Java usługi Service Fabric mogą być tworzone na maszynach Windows, ale mogą być rozmieszczane tylko klastry z systemem Linux platformy Azure. Debugowanie aplikacji Java nie jest obsługiwana na Windows.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że już zainstalowano program VS Code, rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code i wszelkie zależności wymagane dla swojego środowiska projektowego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule używa aplikacji do głosowania w [Szybki Start aplikacji Java usługi Service Fabric przykładowym repozytorium GitHub](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Aby sklonować repozytorium na komputerze deweloperskim, uruchom następujące polecenie w oknie terminalu (okno polecenia na Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w programie VS Code

Open VS Code.  Kliknij ikonę Eksploratora **pasku działań** i kliknij przycisk **Otwórz Folder**, lub kliknij przycisk **Plik -> Otwórz Folder**. Przejdź do *./service-fabric-java-quickstart/Voting* kliknij katalog, w folderze, do którego sklonowano repozytorium następnie **OK**. Obszar roboczy może zawierać te same pliki, które pokazano na poniższym zrzucie ekranu.

![Aplikacja do głosowania języka Java w obszarze roboczym](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Naciśnij klawisz (Ctrl + Shift + p) aby otworzyć **paletę poleceń** w programie VS Code.
2. Wyszukaj i wybierz pozycję **usługi Service Fabric: Tworzenie aplikacji** polecenia. Skompilowane dane wyjściowe są wysyłane do zintegrowany terminal programu.

   ![Tworzenie polecenia aplikacji w programie VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po skonstruowaniu aplikacji można wdrożyć w klastrze lokalnym. 

> [!IMPORTANT]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na maszynach Windows.

1. Z **paletę poleceń**, wybierz opcję **usługi Service Fabric: Wdrażanie aplikacji (Localhost) polecenia**. Dane wyjściowe z procesu instalacji są wysyłane do zintegrowany terminal programu.

   ![Wdrażanie polecenia aplikacji w programie VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz narzędzie Service Fabric Explorer: `http://localhost:19080/Explorer`. Powinieneś zobaczyć, że aplikacja jest uruchomiona. To może trochę potrwać, więc o cierpliwość. 

   ![Aplikacja do głosowania w narzędziu Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po upewnieniu się, że aplikacja jest uruchomiona, uruchom przeglądarkę i otwórz tę stronę: `http://localhost:8080`. Jest to frontonu w aplikacji sieci web. Można dodawać elementy i kliknij je, aby zagłosować.

   ![Aplikacja do głosowania w przeglądarce](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Aby usunąć aplikację z klastra, wybierz pozycję **usługi Service Fabric: Usuń aplikację** polecenia **paletę poleceń**. Proces dezinstalacji dane wyjściowe są wysyłane do zintegrowany terminal programu. Można użyć narzędzia Service Fabric Explorer, aby zweryfikować, że aplikacja została usunięta z lokalnego klastra.

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w programie VS Code, aplikacja musi działać w klastrze lokalnym. Następnie można dodać punkty przerwania w kodzie.

> [!IMPORTANT]
> Debugowanie aplikacji Java nie jest obsługiwana na maszynach Windows.

Aby przygotować VotingDataService i aplikacji do głosowania do debugowania, wykonaj następujące czynności:

1. Aktualizacja *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* pliku.
Komentarz polecenie w wierszu 6 (Użyj "#") i dodaj następujące polecenie do dolnej części pliku:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizacja *Voting/VotingApplication/ApplicationManifest.xml* pliku. Ustaw **MinReplicaSetSize** i **TargetReplicaSetSize** atrybutów "1" w **klasy StatefulService** elementu:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknij ikonę debugowania **pasku działań** otwarcie widoku debugera w programie VS Code. Kliknij ikonę koła zębatego w górnej części Widok debugera i wybierz **Java** z menu rozwijanego w środowisku. Plik launch.json zostanie otwarty. 

   ![Debugowanie ikonę w obszarze roboczym programu VS Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. W pliku launch.json, ustaw wartość portu w konfiguracji o nazwie **debugowanie (dołączanie)** do **8001**. Zapisz plik.

   ![Konfiguracja debugowania dla pliku launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Wdrażanie aplikacji w klastrze lokalnym za pomocą **usługi Service Fabric: Wdrażanie aplikacji (Localhost)** polecenia. Sprawdź, czy aplikacja jest uruchomiona w narzędziu Service Fabric Explorer. Twoja aplikacja jest teraz gotowy do debugowania.

Aby ustawić punkt przerwania, wykonaj następujące czynności:

1. Otwórz w Eksploratorze */Voting/VotingDataService/src/statefulservice/VotingDataService.java* pliku. Ustaw punkt przerwania w pierwszym wierszu kodu w `try` bloku `addItem` — metoda (wiersz: 80).
   
   ![Ustaw punkt przerwania w głosowania usługi danych](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Sprawdź, czy ustawić punkty przerwania w pliku wykonywalnego wierszach kodu. Na przykład punktów przerwania ustawiony na deklaracje metody `try` instrukcji lub `catch` instrukcji, zostaną pominięte przez debuger.
2. Aby rozpocząć debugowanie, kliknij ikonę debugowania **pasku działań**, wybierz opcję **debugowania (Dołącz)** konfiguracji z menu debugowanie, a następnie kliknij przycisk uruchomienia (zielonej strzałki).

   ![Debugowanie (dołączanie) konfiguracji](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. W przeglądarce internetowej przejdź do `http://localhost:8080`. W polu tekstowym wpisz nowy element, a następnie kliknij przycisk **+ Dodaj**. Należy trafiony punkt przerwania. Na pasku narzędzi debugowania w górnej części programu VS Code umożliwia kontynuowanie wykonywania, Przekrocz wiersze, krok po kroku do metody, lub wychodzenia z bieżącej metody. 
   
   ![Trafiony punkt przerwania](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi debugowania, w górnej części programu VS Code.
   
   ![Odłącz od debugera](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po zakończeniu debugowania, możesz użyć **usługi Service Fabric: Usuń aplikację** polecenie, aby usunąć aplikację do głosowania z lokalnego klastra. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [programowanie i debugowanie C# aplikacji usługi Service Fabric za pomocą programu VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
