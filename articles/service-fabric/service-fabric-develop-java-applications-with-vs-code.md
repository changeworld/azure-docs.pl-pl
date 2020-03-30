---
title: Tworzenie aplikacji Java za pomocą kodu programu Visual Studio
description: W tym artykule pokazano, jak tworzyć, wdrażać i debugować aplikacje sieci szkieletowej usługi Java przy użyciu kodu programu Visual Studio.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610050"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Tworzenie aplikacji sieci szkieletowej usług Java za pomocą kodu programu Visual Studio

[Rozszerzenie Niezawodne usługi sieci szkieletowej usług dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji java service fabric w systemach operacyjnych Windows, Linux i macOS.

W tym artykule pokazano, jak tworzyć, wdrażać i debugować aplikację sieci szkieletowej usługi Java przy użyciu kodu programu Visual Studio.

> [!IMPORTANT]
> Aplikacje Java sieci szkieletowej usług można rozwijać na komputerach z systemem Windows, ale można je wdrożyć tylko w klastrach systemu Azure Linux. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już kod usługi VS, rozszerzenie niezawodnych usług sieci szkieletowej usług dla programu VS Code i wszelkie zależności wymagane dla środowiska programistycznego. Aby dowiedzieć się więcej, zobacz [Wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule użyto aplikacji Do głosowania w [przykładowym repożorze aplikacji Java usługi Sieci Szkieletowej.](https://github.com/Azure-Samples/service-fabric-java-quickstart) 

Aby sklonować repozytorium na komputerze deweloperskim, uruchom następujące polecenie z okna terminala (okno polecenia w systemie Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w programie VS Code

Otwórz kod VS.  Kliknij ikonę Eksploratora na **pasku aktywności** i kliknij pozycję **Otwórz folder**lub kliknij pozycję Plik > **Otwórz folder**. Przejdź do katalogu *./service-fabric-java-quickstart/Voting* w folderze, w którym sklonowane jest repozytorium, a następnie kliknij przycisk **OK**. Obszar roboczy powinien zawierać te same pliki pokazane na poniższym zrzucie ekranu.

![Aplikacja do głosowania java w obszarze roboczym](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Naciśnij klawisze (Ctrl + Shift + p), aby otworzyć **paletę poleceń** w programie VS Code.
2. Wyszukaj i wybierz polecenie **Sieci szkieletowej usług: Zbuduj aplikację.** Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminala.

   ![Polecenie kompilacji aplikacji w programie VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po sbudowaniu aplikacji można ją wdrożyć w klastrze lokalnym. 

> [!IMPORTANT]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na komputerach z systemem Windows.

1. Z **palety poleceń**wybierz **polecenie Sieć szkieletowa usług: Wdrażanie aplikacji (Localhost).** Dane wyjściowe procesu instalacji są przesyłane do zintegrowanego terminala.

   ![Wdrażanie polecenia aplikacji w programie VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania uruchom przeglądarkę i otwórz `http://localhost:19080/Explorer`Eksploratora sieci szkieletowej usług: . Powinieneś zobaczyć, że aplikacja jest uruchomiona. Może to zająć trochę czasu, więc bądź cierpliwy. 

   ![Aplikacja do głosowania w Eksploratorze sieci szkieletowej usług](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po sprawdzeniu, że aplikacja jest uruchomiona, uruchom przeglądarkę `http://localhost:8080`i otwórz tę stronę: . Jest to front-end sieci Web aplikacji. Możesz dodawać przedmioty i klikać na nie, aby zagłosować.

   ![Wniosek o głosowanie w przeglądarce](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Aby usunąć aplikację z klastra, wybierz polecenie **Service Fabric: Remove Application** z **palety poleceń**. Dane wyjściowe procesu odinstalowywania są wysyłane do zintegrowanego terminala. Za pomocą Eksploratora sieci szkieletowej usług można sprawdzić, czy aplikacja została usunięta z lokalnego klastra.

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w programie VS Code aplikacja musi być uruchomiona w klastrze lokalnym. Punkty przerwania można następnie dodać do kodu.

> [!IMPORTANT]
> Debugowanie aplikacji Java nie jest obsługiwane na komputerach z systemem Windows.

Aby przygotować votingdataservice i aplikacji głosowania do debugowania, wykonaj następujące kroki:

1. Zaktualizuj plik *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh.*
Skomentuj polecenie w wierszu 6 (użyj '#') i dodaj następujące polecenie na dole pliku:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Zaktualizuj plik *Voting/VotingApplication/ApplicationManifest.xml.* Ustaw **atrybuty MinReplicaSetSize** i **TargetReplicaSetSize** na "1" w **statefulservice** elementu:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknij ikonę Debugowania na **pasku aktywności,** aby otworzyć widok debugera w programie VS Code. Kliknij ikonę koła zębatego u góry widoku debugera i wybierz opcję **Java** z menu rozwijanego środowiska. Zostanie otwarty plik launch.json. 

   ![Ikona debugowania w obszarze roboczym kodu programu VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. W pliku launch.json ustaw wartość portu w konfiguracji o nazwie **Debug (Attach)** na **8001**. Zapisz plik.

   ![Konfiguracja debugowania dla pliku launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Wdrażanie aplikacji w klastrze lokalnym przy użyciu polecenia **Sieć szkieletowa usług: Wdrażanie aplikacji (Localhost).** Sprawdź, czy aplikacja jest uruchomiona w Eksploratorze sieci szkieletowej usług. Aplikacja jest teraz gotowa do debugowania.

Aby ustawić punkt przerwania, wykonaj następujące czynności:

1. W Eksploratorze otwórz plik */Voting/VotingDataService/src/statefulservice/VotingDataService.java.* Ustaw punkt przerwania w pierwszym `try` wierszu `addItem` kodu w bloku w metodzie (wiersz 80).
   
   ![Ustawianie punktu przerwania w usłudze danych o głosowaniu](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Upewnij się, że ustawiono punkty przerwania w wierszach wykonywalnych kodu. Na przykład punkty przerwania ustawione `try` na deklaracje metody, instrukcje lub `catch` instrukcje zostaną pominięte przez debugera.
2. Aby rozpocząć debugowanie, kliknij ikonę Debugowania na **pasku aktywności**, wybierz konfigurację **debugowania (Dołącz)** z menu debugowania i kliknij przycisk Uruchom (zielona strzałka).

   ![Konfiguracja debugowania (dołączania)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. W przeglądarce internetowej wejdź na stronę `http://localhost:8080`. Wpisz nowy element w polu tekstowym i kliknij przycisk **+ Dodaj**. Twój punkt przerwania powinien zostać trafiony. Można użyć paska narzędzi debugowania w górnej części programu VS Code, aby kontynuować wykonywanie, krok za wiersze, krok do metod lub wyjść z bieżącej metody. 
   
   ![Punkt przerwania trafienia](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi Debugowania u góry programu VS Code.
   
   ![Rozłączanie się z debugerem](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po zakończeniu debugowania można użyć service **fabric: Remove Application** polecenia, aby usunąć aplikację głosowania z lokalnego klastra. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć i debugować aplikacje sieci szkieletowej usług C# za pomocą programu VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
