---
title: Jak kod programu Visual Studio współpracuje z usługą Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Dowiedz się, jak program Visual Studio Code i usługa Azure Dev Spaces pomagają debugować i szybko iterować aplikacje usługi Kubernetes
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240444"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak kod programu Visual Studio współpracuje z usługą Azure Dev Spaces

Za pomocą programu Visual Studio Code i [rozszerzenia Azure Dev Spaces][azds-extension] można przygotować, uruchomić i debugować swoje usługi za pomocą usługi Azure Dev Spaces. Za pomocą programu Visual Studio Code i rozszerzenia Azure Dev Spaces można:

* Generowanie zasobów do uruchamiania i debugowania usług w u. AKS
* Uruchamianie usług Java, Node.js i .NET Core w przestrzeni deweloperów
* Bezpośrednio debuguj usługi Java, Node.js i .NET Core działające w przestrzeni deweloperskiej

## <a name="generate-assets"></a>Generowanie zasobów

Kod programu Visual Studio i rozszerzenie Usługi Azure Dev Spaces generują następujące zasoby dla projektu:

* Pliki dockerfiles dla aplikacji Java przy użyciu aplikacji Maven, Node.js i .NET Core
* Wykresy helm dla prawie każdego języka z Dockerfile
* Plik, `azds.yaml` który jest [plikiem konfiguracyjnym usługi Azure Dev Spaces][azds-yaml] dla twojego projektu
* Folder `.vscode` z konfiguracją uruchamiania kodu programu Visual Studio dla aplikacji Java przy użyciu aplikacji Maven, Node.js i .NET Core

Dockerfile, wykres Helm `azds.yaml` i pliki są tymi samymi zasobami generowanymi podczas uruchamiania `azds prep`. Te pliki mogą być również używane poza kodem programu Visual Studio `azds up`do uruchamiania projektu w programie AKS, na przykład uruchomione. Folder `.vscode` jest używany tylko przez kod programu Visual Studio do uruchamiania projektu w programie AKS z programu Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Uruchamianie usługi w AKS

Po wygenerowaniu zasobów dla projektu można uruchomić usługi Java, Node.js i .NET Core w istniejącej przestrzeni deweloperów z programu Visual Studio Code. Na stronie *debugowania* programu Visual Studio Code można wywołać konfigurację uruchamiania z katalogu, `.vscode` aby uruchomić projekt.

Należy utworzyć klaster AKS i włączyć usługi Azure Dev Spaces w klastrze poza programem Visual Studio Code. Na przykład można użyć interfejsu wiersza polecenia platformy Azure lub witryny Azure portal, aby wykonać tę konfigurację. Można ponownie użyć istniejących plików Dockerfiles, `azds.yaml` wykresów helm i plików utworzonych poza programem Visual Studio Code, takich jak zasoby generowane przez uruchomienie `azds prep`. Jeśli ponownie użyć zasobów generowanych poza visual studio kod, `.vscode` nadal trzeba mieć katalog. Ten `.vscode` katalog może być ponownie generowany przez kod programu Visual Studio i rozszerzenie Usługi Azure Dev Spaces i nie zastąpi istniejących zasobów.

W przypadku projektów .NET Core musisz mieć [zainstalowane rozszerzenie C#,][csharp-extension] aby uruchomić usługę .NET z programu Visual Studio Code. Również w przypadku projektów języka Java przy użyciu narzędzia Maven musi być zainstalowany [debuger Java dla usługi Azure Dev Spaces,][java-extension] a także [maven zainstalowany i skonfigurowany][maven] do uruchamiania usługi Java z programu Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Debugowanie usługi w usłudze AKS

Po uruchomieniu projektu można debugować usługi Java, Node.js i .NET Core uruchomione w przestrzeni deweloperskiej bezpośrednio z programu Visual Studio Code. Konfiguracja uruchamiania `.vscode` w katalogu zawiera dodatkowe informacje debugowania do uruchamiania usługi z debugowaniem włączonym w przestrzeni deweloperskiej. Visual Studio Code dołącza również do procesu debugowania w uruchomionym kontenerze w przestrzeniach deweloperskich, co pozwala ustawić punkty przerwania, sprawdzić zmienne i wykonać inne operacje debugowania.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Używanie kodu programu Visual Studio w obszarze azure dev spaces

Kod programu Visual Studio i rozszerzenie Azure Dev Spaces można zobaczyć w następujących programach Szybki start:

* [Szybka iteracji i debugowania z Visual Studio Code i Java][quickstart-java]
* [Szybko iterować i debugować za pomocą programu Visual Studio Code i .NET][quickstart-netcore]
* [Szybka iteracji i debugowania z Visual Studio Code i Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
