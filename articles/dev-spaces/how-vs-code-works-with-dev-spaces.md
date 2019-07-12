---
title: Działania programu Visual Studio Code za pomocą usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Działania programu Visual Studio Code za pomocą usługi Azure Dev miejsca do magazynowania
keywords: Azure Dev spacje, Dev miejsca do magazynowania, Docker, Kubernetes, Azure, AKS, usłudze Azure Kubernetes Service, kontenerów
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712152"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Działania programu Visual Studio Code za pomocą usługi Azure Dev miejsca do magazynowania

Można użyć programu Visual Studio Code i [rozszerzenia usługi Azure Dev miejsca do magazynowania][azds-extension] w celu przygotowania, uruchamianie i debugowanie usługi Azure Dev spacji. Za pomocą programu Visual Studio Code i rozszerzenia usługi Azure Dev miejsca do magazynowania możesz wykonywać następujące czynności:

* Generuj elementy zawartości umożliwiająca uruchamianie i debugowanie usług w usłudze AKS
* Uruchamianie usługi w języka Java, Node.js i platformy .NET Core w miejscu do deweloperów
* Bezpośrednio debugowanie usługi Java, Node.js i platformy .NET Core, w miejsce dev

## <a name="generate-assets"></a>Generuj elementy zawartości

Visual Studio Code i rozszerzenia usługi Azure Dev miejsca do magazynowania generuje następujące zasoby do projektu:

* Pliki Dockerfile dla aplikacji w języku Java przy użyciu narzędzia Maven, aplikacji w technologii Node.js i aplikacji platformy .NET Core
* Wykresów rozwiązania Helm w prawie każdym języku za pomocą pliku Dockerfile
* `azds.yaml` Pliku, który jest [pliku konfiguracji usługi Azure Dev miejsca do magazynowania][azds-yaml] dla projektu
* A `.vscode` folderu za pomocą konfiguracji uruchamiania programu Visual Studio Code projektu dla aplikacji w języku Java przy użyciu narzędzia Maven, aplikacji w technologii Node.js i aplikacji platformy .NET Core

Wykresu Helm w pliku Dockerfile i `azds.yaml` pliki są tego samego zasoby generowane podczas uruchamiania `azds prep`. Te pliki mogą również poza programem Visual Studio code uruchamianie projektu w usłudze AKS, takie jak uruchamianie `azds up`. `.vscode` Folder jest używany tylko przez program Visual Studio code do uruchamiania projektu w usłudze AKS z programu Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Uruchamianie usługi w usłudze AKS

Po wygenerowaniu zasobów dla Twojego projektu, można uruchomić usługi w języka Java, Node.js i platformy .NET Core w istniejącej przestrzeni dev, z programu Visual Studio Code. W *debugowania* stronie programu Visual Studio Code, możesz wywołać konfiguracji uruchamiania z `.vscode` katalogu, aby uruchomić projekt.

Należy utworzyć klaster usługi AKS i włączanie usługi Azure Dev miejsca do magazynowania w klastrze poza programem Visual Studio Code. Na przykład można użyć wiersza polecenia platformy Azure lub w portalu Azure w tej konfiguracji. Można wykorzystać istniejące pliki Dockerfile, wykresów rozwiązania Helm i `azds.yaml` plików utworzonych poza programem Visual Studio Code, takich jak zasoby wygenerowane przez uruchomienie `azds prep`. Jeśli ponownie użyć zasobów wygenerowany poza programem Visual Studio Code, nadal musisz mieć `.vscode` katalogu. To `.vscode` katalogu mogą zostać wygenerowana ponownie przez program Visual Studio code i rozszerzenia usługi Azure Dev miejsca do magazynowania i nie spowoduje zastąpienie istniejących zasobów.

Dla projektów .NET Core, konieczne jest posiadanie [ C# rozszerzenia][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] zainstalowanych także [Maven zainstalowaną i skonfigurowaną][maven] do uruchamiania usługi Java usługi Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Debugowanie usługi w usłudze AKS

Po uruchomieniu projektu, można debugować swoje Java, Node.js i platformy .NET Core usługi działające w miejsce dev bezpośrednio z poziomu programu Visual Studio Code. Konfiguracja uruchamiania w `.vscode` katalogu zawiera dodatkowe informacje debugowania dotyczące uruchamiania usługi z włączonym przestrzeni dev debugowaniem. Visual Studio Code również dołącza do procesu debugowania w kontenerze uruchomiona w swojej dev miejsca do magazynowania, co pozwala ustawić punkty przerwania, sprawdzanie zmiennych i wykonywać inne operacje debugowania.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Program Visual Studio Code za pomocą usługi Azure Dev miejsca do magazynowania

Możesz zobaczyć programu Visual Studio Code i rozszerzenia usługi Azure Dev miejsca do magazynowania, Praca z usługi Azure Dev miejsca do magazynowania w przewodnikach:

* [Programowanie za pomocą języka Java][quickstart-java]
* [Programowanie przy użyciu platformy .NET][quickstart-netcore]
* [Programowanie za pomocą środowiska Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md