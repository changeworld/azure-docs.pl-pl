---
title: Jak działa Visual Studio Code z Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Jak działa Visual Studio Code z Azure Dev Spaces
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297867"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak działa Visual Studio Code z Azure Dev Spaces

Możesz użyć Visual Studio Code i [rozszerzenia Azure dev Spaces][azds-extension] do przygotowywania, uruchamiania i debugowania usług przy użyciu Azure dev Spaces. Za pomocą Visual Studio Code i rozszerzenia Azure Dev Spaces można:

* Generowanie zasobów do uruchamiania i debugowania usług w AKS
* Uruchamianie usług Java, Node. js i .NET Core w przestrzeni deweloperów
* Bezpośrednie debugowanie usług Java, Node. js i .NET Core działających w obszarze dev

## <a name="generate-assets"></a>Generuj elementy zawartości

Visual Studio Code i rozszerzenie Azure Dev Spaces generują następujące zasoby dla projektu:

* Wieloetapowe dockerfile for Java — aplikacje Maven, Node. js i aplikacje platformy .NET Core
* Helm wykresy dla niemal dowolnego języka z pliku dockerfile
* Plik, który jest [Azure dev Spaces plikiem konfiguracji][azds-yaml] dla projektu `azds.yaml`
* `.vscode` Folder z konfiguracją Visual Studio Code uruchamiania projektu dla aplikacji Java korzystających z Maven, aplikacji node. js i aplikacji platformy .NET Core

Pliku dockerfile, wykres Helm i `azds.yaml` pliki są tymi samymi zasobami, które są generowane podczas uruchamiania. `azds prep` Te pliki mogą być również używane poza programem Visual Studio Code do uruchamiania projektu w programie AKS, na przykład uruchomione `azds up`. Ten `.vscode` folder jest używany tylko przez program Visual Studio Code do uruchamiania projektu w programie AKS z Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Uruchamianie usługi w AKS

Po wygenerowaniu zasobów dla projektu można uruchomić usługi Java, Node. js i .NET Core w istniejącym miejscu deweloperskim z Visual Studio Code. Na stronie *debugowanie* Visual Studio Code można wywołać konfigurację uruchamiania z `.vscode` katalogu, aby uruchomić projekt.

Należy utworzyć klaster AKS i włączyć Azure Dev Spaces w klastrze poza Visual Studio Code. Można na przykład użyć interfejsu wiersza polecenia platformy Azure lub Azure Portal, aby wykonać tę konfigurację. Można ponownie użyć istniejących wieloetapowe dockerfile, wykresów Helm i `azds.yaml` plików utworzonych poza Visual Studio Code, takich jak zasoby wygenerowane `azds prep`przez program. W przypadku ponownego użycia zasobów wygenerowanych poza programem Visual Studio Code nadal trzeba mieć `.vscode` katalog. Ten `.vscode` katalog może być ponownie generowany przez program Visual Studio Code i rozszerzenie Azure dev Spaces i nie spowoduje zastąpienia istniejących zasobów.

W przypadku projektów .NET Core musisz mieć zainstalowane [ C# rozszerzenie][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] [oraz Maven zainstalowane i skonfigurowane][Maven] do uruchamiania usługi Java z Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Debugowanie usługi w AKS

Po uruchomieniu projektu można debugować usługi Java, Node. js i .NET Core działające w obszarze dev, bezpośrednio z Visual Studio Code. Konfiguracja uruchamiania w `.vscode` katalogu zawiera dodatkowe informacje debugowania dotyczące uruchamiania usługi z włączoną funkcją debugowania w obszarze dev. Visual Studio Code również dołącza do procesu debugowania w uruchomionym kontenerze w miejscach programistycznych, co pozwala na ustawianie punktów przerwania, inspekcję zmiennych i wykonywanie innych operacji debugowania.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Użyj Visual Studio Code z Azure Dev Spaces

W następujących przewodnikach szybki start można zobaczyć Visual Studio Code i rozszerzenie Azure Dev Spaces pracujące z Azure Dev Spacesami:

* [Programowanie przy użyciu języka Java][quickstart-java]
* [Programowanie przy użyciu platformy .NET][quickstart-netcore]
* [Programowanie przy użyciu środowiska Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
