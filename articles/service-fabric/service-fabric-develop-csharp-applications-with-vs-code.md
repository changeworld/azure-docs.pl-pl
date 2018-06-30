---
title: Tworzenie aplikacji platformy .NET Core Azure Service Fabric z kodem Visual Studio | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób tworzenia, wdrażania i debugowania aplikacji .NET Core usługi Service Fabric przy użyciu programu Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 27c7c62125f3f559fb1764292729cbbfdc1c4e5f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116124"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Tworzenie aplikacji sieci szkieletowej usług C# za pomocą programu Visual Studio Code

[Rozszerzenia usługi sieci szkieletowej niezawodnych usług VS kodu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) ułatwia tworzenie aplikacji platformy .NET Core Service Fabric w systemach operacyjnych Windows, Linux lub macOS.

W tym artykule przedstawiono sposób tworzenia, wdrażania i debugowanie aplikacji .NET Core usługi Service Fabric przy użyciu programu Visual Studio Code.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zainstalowano już kodzie VS, rozszerzenie usługi sieci szkieletowej usług niezawodnej kodzie VS i wszelkie zależności wymagane dla środowiska deweloperskiego. Aby dowiedzieć się więcej, zobacz [wprowadzenie](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Pobierz przykład
W tym artykule wykorzystano CounterService aplikacji w [usługi sieć szkieletowa .NET Core wprowadzenie przykłady repozytorium GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Klonowanie repozytorium na komputerze deweloperskim, uruchom następujące polecenie z poziomu okna terminali (okno polecenia w systemie Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Otwórz aplikację w kodzie VS

### <a name="windows"></a>Windows
Kliknij prawym przyciskiem myszy ikonę programu VS kod w Start Menu i wybierz polecenie **Uruchom jako administrator**. Aby dołączyć debuger do usługi, należy uruchomić kodzie VS jako administrator.

### <a name="linux"></a>Linux
Przy użyciu terminala, przejdź do ścieżki /service-fabric-dotnet-core-getting-started/Services/CounterService z katalogu aplikacji został sklonowany w lokalnie.

Uruchom następujące polecenie, aby otworzyć kodzie VS jako użytkownik główny, tak aby debuger może połączyć się z usługami.
```
sudo code . --user-data-dir='.'
```

Aplikacja powinien zostać wyświetlony w obszarze roboczym kodzie VS.

![Aplikacja usługi licznika w obszarze roboczym](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Kompilowanie aplikacji
1. Naciśnij klawisz (Ctrl + Shift + p) można otworzyć **palety polecenie** w kodzie VS.
2. Wyszukaj i wybierz **usługi Service Fabric: tworzenie aplikacji** polecenia. Dane wyjściowe kompilacji są wysyłane do zintegrowanego terminala.

   ![Tworzenie aplikacji polecenia w kodzie VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Wdrażanie aplikacji do klastra lokalnego
Po skonstruowaniu aplikacji można wdrożyć w klastrze lokalnym. 

1. Z **palety polecenia**, wybierz pozycję **usługi Service Fabric: polecenie wdrożenia aplikacji (Localhost)**. Dane wyjściowe procesu instalacji są wysyłane do zintegrowanego terminala.

   ![Wdrażanie aplikacji polecenia w kodzie VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Po zakończeniu wdrażania przeglądarkę i Otwórz Eksploratora usługi sieć szkieletowa: http://localhost:19080/Explorer. Powinna zostać wyświetlona aplikacja działa. Może to zająć trochę czasu, dlatego cierpliwość. 

   ![Aplikacja usługi licznika w Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Po upewnieniu się, aplikacja jest uruchomiona, uruchom przeglądarkę i otwórz tę stronę: http://localhost:31002. Jest to frontonu aplikacji sieci web. Odśwież stronę, aby wyświetlić bieżącą wartość licznika, ponieważ powoduje zwiększenie.

   ![Aplikacja usługi licznika w przeglądarce](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Debugowanie aplikacji
Podczas debugowania aplikacji w kodzie VS, aplikacja musi działać w klastrze lokalnym. Następnie można dodać punkty przerwania w kodzie.

Aby ustawić punkt przerwania i debugowania, wykonaj następujące kroki:
1. Otwórz w Eksploratorze */src/CounterServiceApplication/CounterService/CounterService.cs* pliku i ustaw punkt przerwania w wierszu 62 wewnątrz `RunAsync` metody.
3. Kliknij ikonę debugowania w **działania pasek** aby otworzyć widok debugera w kodzie VS. Kliknij ikonę Koło zębate w górnej części Widok debugera i wybierz **.NET Core** z menu rozwijanego środowiska. Plik launch.json można otworzyć. Możesz zamknąć ten plik. Teraz powinno zostać wyświetlone opcje konfiguracji w menu konfiguracji debugowania, znajdujący się obok przycisku uruchamiania (zieloną strzałkę).

   ![Debugowanie ikonę w obszarze roboczym kodu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Wybierz **.NET Core dołączyć** z menu Debugowanie konfiguracji.

   ![Debugowanie ikonę w obszarze roboczym kodu VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Otwórz Eksploratora usługi sieć szkieletowa w przeglądarce: http://localhost:19080/Explorer. Kliknij przycisk **aplikacji** , a następnie przejść dowwn określić podstawowy węzła, który CounterService działa na. Na ilustracji poniżej węzła podstawowego dla CounterService jest węzłem 0.

   ![Węzeł podstawowy dla CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. W kodzie VS, kliknij ikonę uruchamiania (zieloną strzałkę) obok **.NET Core dołączyć** konfiguracji debugowania. W oknie dialogowym Wybieranie procesu wybierz CounterService proces, który działa w węźle podstawowym, który został zidentyfikowany w kroku 4.

   ![Podstawowy proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Punkt przerwania w *CounterService.cs* plik zostanie uruchomiona bardzo szybko. Następnie można sprawdzić wartości zmiennych lokalnych. Użyj narzędzi debugowania w górnej części kodu w PORÓWNANIU do kontynuowania wykonywania kroku przy użyciu linii, Wkrocz do metody, lub wychodzenia z bieżącej metody. 

   ![Debugowanie wartości](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Aby zakończyć sesję debugowania, kliknij ikonę plug na pasku narzędzi debugowania w górnej części kodu programu VS.
   
   ![Odłącz od debugera](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Po zakończeniu debugowania, można użyć **usługi Service Fabric: usuwanie aplikacji** polecenie, aby usunąć CounterService aplikacji w klastrze lokalnym. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [opracowywania i debugowania aplikacji Java sieci szkieletowej usług za pomocą kodu VS](./service-fabric-develop-java-applications-with-vs-code.md).



