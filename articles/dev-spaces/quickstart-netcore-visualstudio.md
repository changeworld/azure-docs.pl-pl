---
title: Tworzenie obszaru deweloperskiego Kubernetes w chmurze | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824642"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Szybki start: tworzenie obszaru deweloperskiego Kubernetes za pomocą usługi Azure Dev Spaces (platforma .NET Core i program Visual Studio)

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Debugowanie kodu działającego w klastrze.

> [!Note]
> **Jeśli utkniesz** w dowolnym momencie, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) lub prześlij komentarz na tej stronie. Możesz też spróbować wypełnić instrukcje z bardziej szczegółowego [samouczka](get-started-netcore-visualstudio.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Klaster Kubernetes z uruchomioną platformą Kubernetes 1.9.6 w regionach EastUS, WestEurope lub CanadaEast z włączonym routingiem aplikacji protokołu HTTP.

  ![Pamiętaj, aby włączyć routing aplikacji protokołu HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Program Visual Studio 2017 z zainstalowanym obciążeniem Tworzenie aplikacji internetowych. Jeśli program nie został zainstalowany, pobierz go [tutaj](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Konfigurowanie obszarów Azure Dev Spaces

Zainstaluj [rozszerzenie programu Visual Studio dla usługi Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

Następnie utworzysz i skonfigurujesz projekt dla usługi Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

W programie Visual Studio 2017 utwórz nowy projekt. Obecnie projekt musi być **aplikacją internetową ASP.NET Core**. Nazwij projekt **webfrontend**.

Wybierz szablon **Aplikacja internetowa (Model-View-Controller)** i upewnij się, że zostały wybrane pozycje **.NET Core** i **ASP.NET Core 2.0**.

### <a name="create-a-dev-space-in-azure"></a>Tworzenie obszaru deweloperskiego na platformie Azure

Po otwarciu utworzonego przed chwilą projektu wybierz usługę **Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W wyświetlonym oknie dialogowym upewnij się, że logujesz się przy użyciu odpowiedniego konta, a następnie wybierz istniejący klaster.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Na liście rozwijanej **Miejsce** zostaw na razie ustawienie `default`. Zaznacz pole wyboru **Publicznie dostępne**, aby aplikacja internetowa była dostępna za pośrednictwem publicznego punktu końcowego.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Kliknij przycisk **OK**, aby wybrać lub utworzyć klaster.

Jeśli wybierzesz klaster, który nie został skonfigurowany do pracy z usługą Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Wybierz pozycję **OK**. 

### <a name="look-at-the-files-added-to-project"></a>Przyglądanie się plikom dodanym do projektu
Czekając na utworzenie obszaru deweloperskiego, przyjrzyj się plikom, które zostały dodane do projektu w przypadku wybrania opcji korzystania z usługi Azure Dev Spaces.

- Został dodany folder o nazwie `charts`, a w tym folderze został utworzony szablon [planu narzędzia Helm](https://docs.helm.sh) dla Twojej aplikacji. Te pliki służą do wdrażania aplikacji w obszarze deweloperskim.
- Plik `Dockerfile` zawiera informacje potrzebne do spakowania aplikacji w standardowym formacie Docker.
- Plik `azds.yaml` zawiera informacje o konfiguracji wymagane przez obszar deweloperski, na przykład informacje o tym, czy aplikacja powinna być dostępna za pośrednictwem publicznego punktu końcowego.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po pomyślnym utworzeniu obszaru deweloperskiego możesz debugować aplikację. Ustaw punkt przerwania w kodzie, na przykład w wierszu 20 pliku `HomeController.cs`, w którym jest ustawiona zmienna `Message`. Naciśnij klawisz **F5**, aby rozpocząć debugowanie. 

Program Visual Studio będzie komunikować się z obszarem deweloperskim w celu skompilowania i wdrożenia aplikacji, a następnie otworzy przeglądarkę z uruchomioną aplikacją internetową. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w obszarze deweloperskim na platformie Azure. Przyczyną utworzenia adresu hosta lokalnego jest utworzenie przez usługę Azure Dev Spaces tymczasowego tunelu SSH w kontenerze uruchomionym na platformie Azure.

Kliknij link **Informacje** w górnej części strony, aby wyzwolić punkt przerwania. Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](get-started-netcore-visualstudio.md#call-another-container)