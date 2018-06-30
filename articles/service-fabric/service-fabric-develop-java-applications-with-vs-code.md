---
title: Tworzenie aplikacji Java sieć szkieletowa usług Azure z kodem Visual Studio | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób tworzenia, wdrażania i debugowania aplikacji Java sieci szkieletowej usług za pomocą programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 54c94c50f6292694e947d97a10fd6976c14e19df
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116051"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Tworzenie aplikacji Java sieci szkieletowej usług za pomocą programu Visual Studio Code

[Rozszerzenia usługi sieci szkieletowej niezawodnych usług VS kodu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji Java sieci szkieletowej usług w systemach operacyjnych Windows, Linux lub macOS.

W tym artykule przedstawiono sposób tworzenia, wdrażania i debugowanie aplikacji sieci szkieletowej usług języka Java przy użyciu programu Visual Studio Code.

> [!IMPORTANT]
> Usługa sieci szkieletowej Java aplikacje mogą być opracowane na komputerach z systemem Windows, ale mogą być rozmieszczane tylko klastry z systemem Linux Azure. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już kodzie VS, rozszerzenie usługi sieci szkieletowej usług niezawodnej kodzie VS i wszelkie zależności wymagane dla środowiska deweloperskiego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule wykorzystano aplikację głosowania w [repozytorium GitHub próbki Szybki Start aplikacji Java sieci szkieletowej usług](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Klonowanie repozytorium na komputerze deweloperskim, uruchom następujące polecenie z poziomu okna terminali (okno polecenia w systemie Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w kodzie VS

Otwórz VS kodu.  Kliknij ikonę Explorer **działania pasek** i kliknij przycisk **Otwórz Folder**, lub kliknij przycisk **Plik -> Otwórz Folder**. Przejdź do *./service-fabric-java-quickstart/Voting* kliknij katalog w folderze, w którym sklonować repozytorium następnie **OK**. Obszaru roboczego powinna zawierać tych samych plików, jak pokazano na poniższym zrzucie ekranu.

![Głosowania aplikacji w języku Java w obszarze roboczym](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji

1. Naciśnij klawisz (Ctrl + Shift + p) można otworzyć **palety polecenie** w kodzie VS.
2. Wyszukaj i wybierz **usługi Service Fabric: tworzenie aplikacji** polecenia. Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminala.

   ![Tworzenie aplikacji polecenia w kodzie VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji do klastra lokalnego
Po skonstruowaniu aplikacji można wdrożyć w klastrze lokalnym. 

> [!IMPORTANT]
> Wdrożenie aplikacji Java na lokalny klaster nie jest obsługiwana na komputerach z systemem Windows.

1. Z **palety polecenia**, wybierz pozycję **usługi Service Fabric: polecenie wdrożenia aplikacji (Localhost)**. Dane wyjściowe procesu instalacji są wysyłane do zintegrowanego terminala.

   ![Wdrażanie aplikacji polecenia w kodzie VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania przeglądarkę i Otwórz Eksploratora usługi sieć szkieletowa: http://localhost:19080/Explorer. Powinna zostać wyświetlona aplikacja działa. Może to zająć trochę czasu, dlatego cierpliwość. 

   ![Aplikację do głosowania w narzędziu Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Po upewnieniu się, że aplikacja jest uruchomiona, uruchom przeglądarkę i otwórz tę stronę: http://localhost:8080. Jest to frontonu aplikacji sieci web. Można dodawać elementy i kliknij je do głosowania.

   ![Aplikację do głosowania w przeglądarce](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Aby usunąć aplikację z klastra, wybierz **sieci szkieletowej usług: usuwanie aplikacji** polecenie **palety polecenia**. Dane wyjściowe odinstalowywania są wysyłane do zintegrowanego terminala. Aby zweryfikować, że aplikacja została usunięta z lokalnego klastra, można użyć Eksploratora usługi sieć szkieletowa.

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w kodzie VS, aplikacja musi działać w klastrze lokalnym. Następnie można dodać punkty przerwania w kodzie.

> [!IMPORTANT]
> Debugowanie aplikacji Java nie jest obsługiwane na komputerach z systemem Windows.

Aby przygotować VotingDataService i głosowania aplikację do debugowania, wykonaj następujące kroki:

1. Aktualizacja *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* pliku.
Komentarz wiersz polecenia w wierszu 6 (Użyj "#") i na końcu pliku należy dodać następujące polecenie:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Aktualizacja *Voting/VotingApplication/ApplicationManifest.xml* pliku. Ustaw **MinReplicaSetSize** i **TargetReplicaSetSize** atrybuty "1" w **klasy StatefulService** elementu:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kliknij ikonę debugowania w **działania pasek** aby otworzyć widok debugera w kodzie VS. Kliknij ikonę Koło zębate w górnej części Widok debugera i wybierz **Java** z menu rozwijanego środowiska. Plik launch.json można otworzyć. 

   ![Debugowanie ikonę w obszarze roboczym kodu VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. W pliku launch.json, ustaw wartość portu w konfiguracji o nazwie **debugowania (Dołącz)** do **8001**. Zapisz plik.

   ![Konfiguracji debugowania dla launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Wdrażanie aplikacji do klastra lokalnego przy użyciu **usługi Service Fabric: Wdrażanie aplikacji (Localhost)** polecenia. Sprawdź, czy aplikacja działa w narzędziu Service Fabric Explorer. Aplikacja jest teraz gotowy do debugowania.

Aby ustawić punkt przerwania, wykonaj następujące kroki:

1. Otwórz w Eksploratorze */Voting/VotingDataService/src/statefulservice/VotingDataService.java* pliku. Ustaw punkt przerwania w pierwszym wierszu kodu w `try` blok w `addItem` — metoda (wiersz: 80).
   
   ![Ustaw punkt przerwania w głosowania usługi danych](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Upewnij się, że można ustawić punktów przerwania na wierszy kodu wykonywalnego. Na przykład punktów przerwania ustawionych w deklaracjach metody `try` instrukcji lub `catch` instrukcje zostaną pominięte przez debuger.
2. Aby rozpocząć debugowanie, kliknij ikonę debugowania w **działania pasek**, wybierz pozycję **debugowania (Dołącz)** konfiguracji z menu Debugowanie i kliknij przycisk Uruchom (zieloną strzałkę).

   ![Debugowanie (Dołącz) konfiguracji](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. W przeglądarce sieci web, przejdź do http://localhost:8080. Wpisz nowy element w polu tekstowym i kliknij przycisk **+ Dodaj**. Należy trafiony punkt przerwania. Narzędzi debugowania w górnej części kodu VS służy do kontynuowania wykonywania, Przekrocz wierszy, Wkrocz do metody, lub wychodzenia z bieżącej metody. 
   
   ![Trafienia punktu przerwania](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Aby zakończyć sesję debugowania, kliknij ikonę plug na pasku narzędzi debugowania w górnej części kodu programu VS.
   
   ![Odłącz od debugera](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Po zakończeniu debugowania, można użyć **usługi Service Fabric: usuwanie aplikacji** polecenie, aby usunąć aplikację głosowania z lokalnym klastrem. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [tworzenie i debugowanie aplikacji sieci szkieletowej usług C# za pomocą kodu VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
