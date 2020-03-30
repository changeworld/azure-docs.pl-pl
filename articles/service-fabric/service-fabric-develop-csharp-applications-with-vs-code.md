---
title: Tworzenie aplikacji .NET Core za pomocą kodu programu Visual Studio
description: W tym artykule pokazano, jak tworzyć, wdrażać i debugować aplikacje sieci szkieletowej usług .NET Core przy użyciu kodu programu Visual Studio.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614527"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Tworzenie aplikacji sieci szkieletowej usług w języku C# za pomocą kodu programu Visual Studio

[Rozszerzenie niezawodne usługi sieci szkieletowej usług dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji sieci szkieletowej usług .NET Core w systemach operacyjnych Windows, Linux i macOS.

W tym artykule pokazano, jak tworzyć, wdrażać i debugować aplikację sieci szkieletowej usługi .NET Core przy użyciu kodu programu Visual Studio.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już kod usługi VS, rozszerzenie niezawodnych usług sieci szkieletowej usług dla programu VS Code i wszelkie zależności wymagane dla środowiska programistycznego. Aby dowiedzieć się więcej, zobacz [Wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule użyto aplikacji CounterService w [sieci szkieletowej .NET Core wprowadzenie przykładów repozytorium GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Aby sklonować repozytorium na komputerze deweloperskim, uruchom następujące polecenie z okna terminala (okno polecenia w systemie Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w programie VS Code

### <a name="windows"></a>Windows
Kliknij prawym przyciskiem myszy ikonę VS Code w menu Start i wybierz polecenie **Uruchom jako administrator**. Aby dołączyć debuger do usług, należy uruchomić program VS Code jako administrator.

### <a name="linux"></a>Linux
Za pomocą terminala przejdź do ścieżki /service-fabric-dotnet-core-getting-started/Services/CounterService z katalogu, do których aplikacja została sklonowana lokalnie.

Uruchom następujące polecenie, aby otworzyć program VS Code jako użytkownik root, aby debuger mógł dołączyć do usług.
```
sudo code . --user-data-dir='.'
```

Aplikacja powinna teraz pojawić się w obszarze roboczym programu VS Code.

![Aplikacja usługi licznika w obszarze roboczym](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji
1. Naciśnij klawisze (Ctrl + Shift + p), aby otworzyć **paletę poleceń** w programie VS Code.
2. Wyszukaj i wybierz polecenie **Sieci szkieletowej usług: Zbuduj aplikację.** Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminala.

   ![Polecenie Kompilacja aplikacji w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym
Po sbudowaniu aplikacji można ją wdrożyć w klastrze lokalnym. 

1. Z **palety poleceń**wybierz **polecenie Sieć szkieletowa usług: Wdrażanie aplikacji (Localhost).** Dane wyjściowe procesu instalacji są przesyłane do zintegrowanego terminala.

   ![Polecenie Wdrażanie aplikacji w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania uruchom przeglądarkę i otwórz Eksploratora sieci szkieletowej usług: http:\//localhost:19080/Explorer. Powinieneś zobaczyć, że aplikacja jest uruchomiona. Może to zająć trochę czasu, więc bądź cierpliwy. 

   ![Aplikacja usługi przeciwstawowej w Eksploratorze sieci szkieletowej usług](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po zweryfikowaniu, że aplikacja jest uruchomiona, uruchom przeglądarkę i\/otwórz tę stronę: http: /localhost:31002. Jest to front-end sieci Web aplikacji. Odśwież stronę, aby zobaczyć bieżącą wartość licznika w miarę jego zwiększania.

   ![Aplikacja counter service w przeglądarce](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publikowanie aplikacji w klastrze sieci szkieletowej usług Azure
Wraz z wdrażaniem aplikacji w klastrze lokalnym można również opublikować aplikację w zdalnym klastrze usługi Azure Service Fabric. 

1. Upewnij się, że aplikacja została sbudytując przy użyciu powyższych instrukcji. Zaktualizuj `Cloud.json` wygenerowany plik konfiguracyjny o szczegóły klastra zdalnego, w którego chcesz opublikować.

2. Z **palety poleceń**wybierz **polecenie Sieci szkieletowej usług: Publikuj aplikację**. Dane wyjściowe procesu instalacji są przesyłane do zintegrowanego terminala.

   ![Polecenie Publikuj aplikację w programie VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Po zakończeniu wdrażania uruchom przeglądarkę i otwórz `https:<clusterurl>:19080/Explorer`Eksploratora sieci szkieletowej usług: . Powinieneś zobaczyć, że aplikacja jest uruchomiona. Może to zająć trochę czasu, więc bądź cierpliwy. 

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w programie VS Code aplikacja musi być uruchomiona w klastrze lokalnym. Punkty przerwania można następnie dodać do kodu.

Aby ustawić punkt przerwania i debugowanie, wykonaj następujące kroki:
1. W Eksploratorze otwórz plik */src/CounterServiceApplication/CounterService/CounterService.cs* i ustaw punkt `RunAsync` przerwania w wierszu 62 wewnątrz metody.
3. Kliknij ikonę Debugowania na **pasku aktywności,** aby otworzyć widok debugera w programie VS Code. Kliknij ikonę koła zębatego u góry widoku debugera i wybierz pozycję **.NET Core** z menu rozwijanego środowiska. Zostanie otwarty plik launch.json. Możesz zamknąć ten plik. Teraz powinieneś zobaczyć opcje konfiguracji w menu konfiguracji debugowania znajdującym się obok przycisku uruchom (zielona strzałka).

   ![Ikona debugowania w obszarze roboczym kodu programu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Z menu konfiguracji debugowania wybierz polecenie **.NET Core Attach.**

   ![Ikona debugowania w obszarze roboczym kodu programu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otwórz Eksploratora sieci szkieletowej usług w przeglądarce: http:\//localhost:19080/Explorer. Kliknij **przycisk Aplikacje** i przejdź do szczegółów, aby określić węzeł podstawowy, na który działa usługa CounterService. Na obrazku poniżej węzła podstawowego dla CounterService jest węzeł 0.

   ![Węzeł podstawowy dla counterservice](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. W programie VS Code kliknij ikonę uruchamiania (zielona strzałka) obok konfiguracji **debugowania .NET Core Attach.** W oknie dialogowym wyboru procesu wybierz proces CounterService, który jest uruchomiony w węźle podstawowym, który został zidentyfikowany w kroku 4.

   ![Proces podstawowy](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Punkt przerwania w pliku *CounterService.cs* zostanie trafiony bardzo szybko. Następnie można eksplorować wartości zmiennych lokalnych. Użyj paska narzędzi Debug w górnej części programu VS Code, aby kontynuować wykonywanie, krok za wiersze, krok do metod lub wyjść z bieżącej metody. 

   ![Wartości debugowania](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Aby zakończyć sesję debugowania, kliknij ikonę wtyczki na pasku narzędzi Debugowania u góry programu VS Code..
   
   ![Rozłączanie się z debugerem](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po zakończeniu debugowania można użyć **service fabric: Remove Application** polecenia, aby usunąć aplikację CounterService z lokalnego klastra. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć i debugować aplikacje sieci szkieletowej usług Java za pomocą programu VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



