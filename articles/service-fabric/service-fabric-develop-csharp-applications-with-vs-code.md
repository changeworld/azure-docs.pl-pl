---
title: Twórz aplikacje platformy .NET Core usługi Azure Service Fabric za pomocą programu Visual Studio Code | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak tworzenie, wdrażanie i debugowanie aplikacji .NET Core usługi Service Fabric przy użyciu programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 60b634b0b927804249148737ee7a99c0e86dd7d6
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537784"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Twórz C# aplikacji usługi Service Fabric za pomocą programu Visual Studio Code

[Rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji .NET Core usługi Service Fabric w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł pokazuje, jak tworzenie, wdrażanie i debugowanie aplikacji .NET Core usługi Service Fabric przy użyciu programu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że już zainstalowano program VS Code, rozszerzenie usług Reliable Services usługi Service Fabric dla programu VS Code i wszelkie zależności wymagane dla swojego środowiska projektowego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule wykorzystano CounterService aplikacji w [usługi Service Fabric platformy .NET Core wprowadzenie przykłady repozytorium GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Aby sklonować repozytorium na komputerze deweloperskim, uruchom następujące polecenie w oknie terminalu (okno polecenia na Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w programie VS Code

### <a name="windows"></a>Windows
Kliknij prawym przyciskiem myszy ikonę programu VS Code w Start Menu i wybierz polecenie **Uruchom jako administrator**. Aby dołączyć debuger do usług, musisz uruchomić program VS Code jako administrator.

### <a name="linux"></a>Linux
Za pomocą terminalu, przejdź do ścieżki /service-fabric-dotnet-core-getting-started/Services/CounterService z katalogu aplikacji sklonowanym się do lokalnie.

Uruchom następujące polecenie, aby otworzyć program VS Code jako użytkownik główny, tak, aby dołączyć debuger do usług.
```
sudo code . --user-data-dir='.'
```

Aplikacja powinna pojawiają się w obszarze roboczym programu VS Code.

![Licznik aplikacji usługi w obszarze roboczym](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji
1. Naciśnij klawisz (Ctrl + Shift + p) aby otworzyć **paletę poleceń** w programie VS Code.
2. Wyszukaj i wybierz pozycję **usługi Service Fabric: Tworzenie aplikacji** polecenia. Skompilowane dane wyjściowe są wysyłane do zintegrowany terminal programu.

   ![Tworzenie polecenia aplikacji w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po skonstruowaniu aplikacji można wdrożyć w klastrze lokalnym. 

1. Z **paletę poleceń**, wybierz opcję **usługi Service Fabric: Wdrażanie aplikacji (Localhost) polecenia**. Dane wyjściowe z procesu instalacji są wysyłane do zintegrowany terminal programu.

   ![Wdrażanie polecenia aplikacji w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz narzędzie Service Fabric Explorer: http:\//localhost:19080 / Explorer. Powinieneś zobaczyć, że aplikacja jest uruchomiona. To może trochę potrwać, więc o cierpliwość. 

   ![Licznik aplikacji usługi Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po upewnieniu się, aplikacja jest uruchomiona, uruchom przeglądarkę i otwórz tę stronę: http:\//localhost:31002. Jest to frontonu w aplikacji sieci web. Odśwież stronę Aby wyświetlić bieżącą wartość licznika, ponieważ zwiększa.

   ![Licznik aplikacji usługi w przeglądarce](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publikowanie aplikacji w klastrze usługi Azure Service Fabric
Wraz z wdrożeniem aplikacji w klastrze lokalnym, możesz również opublikować aplikację do zdalnego klastra usługi Azure Service Fabric. 

1. Upewnij się, czy został wcześniej utworzony swoją aplikację przy użyciu powyższych instrukcji. Zaktualizuj plik konfiguracji wygenerowanego `Cloud.json` ze szczegółami zdalny klaster, który chcesz opublikować.

2. Z **paletę poleceń**, wybierz opcję **usługi Service Fabric: Publikowanie aplikacji polecenie**. Dane wyjściowe z procesu instalacji są wysyłane do zintegrowany terminal programu.

   ![Publikowanie aplikacji polecenie w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Po zakończeniu wdrażania Uruchom przeglądarkę i Otwórz narzędzie Service Fabric Explorer: `https:<clusterurl>:19080/Explorer`. Powinieneś zobaczyć, że aplikacja jest uruchomiona. To może trochę potrwać, więc o cierpliwość. 

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w programie VS Code, aplikacja musi działać w klastrze lokalnym. Następnie można dodać punkty przerwania w kodzie.

Aby ustawić punkt przerwania i debugowania, wykonaj następujące czynności:
1. Otwórz w Eksploratorze */src/CounterServiceApplication/CounterService/CounterService.cs* plik i ustaw punkt przerwania w wierszu 62 wewnątrz `RunAsync` metody.
3. Kliknij ikonę debugowania **pasku działań** otwarcie widoku debugera w programie VS Code. Kliknij ikonę koła zębatego w górnej części Widok debugera i wybierz **platformy .NET Core** z menu rozwijanego w środowisku. Plik launch.json zostanie otwarty. Możesz zamknąć ten plik. Teraz powinien być widoczny opcje konfiguracji dostępne w menu konfiguracji debugowania, znajdujący się obok przycisku uruchomienia (zielonej strzałki).

   ![Debugowanie ikonę w obszarze roboczym programu VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Wybierz **Dołączanie programu .NET Core** menu konfiguracji debugowania.

   ![Debugowanie ikonę w obszarze roboczym programu VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otwórz narzędzie Service Fabric Explorer w przeglądarce: http:\//localhost:19080 / Explorer. Kliknij przycisk **aplikacje** i testowania odzyskiwania po awarii w dół, aby określić węzła podstawowego, który CounterService działa na. Na ilustracji poniżej węzła podstawowego dla CounterService jest węzłem 0.

   ![Podstawowy węzeł CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. W programie VS Code kliknij ikonę uruchomienia (zielonej strzałki) obok **Dołączanie programu .NET Core** konfiguracji debugowania. W oknie dialogowym wyboru procesu wybierz CounterService procesu, który działa w węźle podstawowym, który został zidentyfikowany w kroku 4.

   ![Podstawowy proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Punkt przerwania w *CounterService.cs* plik osiągnie się bardzo szybko. Następnie można zbadać wartości zmiennych lokalnych. Użyj narzędzi debugowania w górnej części programu VS Code kontynuowanie wykonywania, Przekrocz wiersze, krok po kroku do metody, lub wychodzenia z bieżącej metody. 

   ![Debugowanie wartości](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi debugowania, w górnej części programu VS Code...
   
   ![Odłącz od debugera](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po zakończeniu debugowania, możesz użyć **usługi Service Fabric: Usuń aplikację** polecenie, aby usunąć CounterService aplikacji w klastrze lokalnym. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [programowanie i debugowanie aplikacji Java usługi Service Fabric za pomocą programu VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



