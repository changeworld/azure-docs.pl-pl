---
title: Tworzenie obszaru deweloperskiego Kubernetes w chmurze | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: quickstart
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 224b8fac3634e5ee9e5ffadc7664bd046467d2ce
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660854"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>Szybki start: Tworzenie obszaru deweloperskiego Kubernetes za pomocą usługi Azure Dev Spaces (platforma .NET Core i program Visual Studio)

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Debugowanie kodu działającego w klastrze.

> [!Note]
> **Jeśli utkniesz** w dowolnym momencie, zobacz sekcję [Rozwiązywanie problemów](troubleshooting.md) lub prześlij komentarz na tej stronie. Możesz również spróbować wykonać instrukcje z bardziej szczegółowego [samouczka](get-started-netcore-visualstudio.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Klaster Kubernetes z uruchomioną platformą Kubernetes 1.9.6 lub nowszą w regionie EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral lub CanadaEast z włączonym routingiem aplikacji protokołu HTTP.

  ![Pamiętaj, aby włączyć routing aplikacji protokołu HTTP.](media/common/Kubernetes-Create-Cluster-3.PNG)

- Program Visual Studio 2017 z zainstalowanym obciążeniem Tworzenie aplikacji internetowych. Jeśli program nie został zainstalowany, pobierz go [tutaj](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="set-up-azure-dev-spaces"></a>Konfigurowanie usługi Azure Dev Spaces

Zainstaluj program [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="connect-to-a-cluster"></a>Łączenie z klastrem

Następnie utworzysz i skonfigurujesz projekt dla usługi Azure Dev Spaces.

### <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

W programie Visual Studio 2017 utwórz nowy projekt. Obecnie projekt musi być **aplikacją internetową ASP.NET Core**. Nazwij projekt **webfrontend**.

Wybierz szablon **Aplikacja internetowa (Model-View-Controller)** i upewnij się, że zostały wybrane pozycje **.NET Core** i **ASP.NET Core 2.0**.

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>Włączanie usługi Dev Spaces dla klastra usługi AKS

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
- Plik `azds.yaml` zawiera konfigurację w czasie programowania, która jest wymagana przez obszar deweloperski.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Debugowanie kontenera w środowisku Kubernetes
Po pomyślnym utworzeniu obszaru deweloperskiego możesz debugować aplikację. Ustaw punkt przerwania w kodzie, na przykład w wierszu 20 pliku `HomeController.cs`, w którym jest ustawiona zmienna `Message`. Naciśnij klawisz **F5**, aby rozpocząć debugowanie. 

Program Visual Studio będzie komunikować się z obszarem deweloperskim w celu skompilowania i wdrożenia aplikacji, a następnie otworzy przeglądarkę z uruchomioną aplikacją internetową. Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w obszarze deweloperskim na platformie Azure. Przyczyną utworzenia adresu hosta lokalnego jest utworzenie przez usługę Azure Dev Spaces tymczasowego tunelu SSH w kontenerze uruchomionym w usłudze AKS.

Kliknij link **Informacje** w górnej części strony, aby wyzwolić punkt przerwania. Masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne, informacje o wyjątkach itd., zupełnie jakby kod był wykonywany lokalnie.


## <a name="iteratively-develop-code"></a>Iteracyjne programowanie kodu

Usługa Azure Dev Spaces umożliwia nie tylko uruchamianie kodu w środowisku Kubernetes — pozwala też szybko i wielokrotnie wyświetlać efekt zmian wprowadzonych w kodzie w środowisku Kubernetes w chmurze.

### <a name="update-a-content-file"></a>Aktualizowanie pliku zawartości
1. Znajdź plik `./Views/Home/Index.cshtml` i zmień kod HTML. Na przykład zmień wiersz 70 z `<h2>Application uses</h2>` na `<h2>Hello k8s in Azure!</h2>`
1. Zapisz plik.
1. Otwórz przeglądarkę i odśwież stronę. Na stronie powinien być widoczny zaktualizowany kod HTML.

Co się stało? Modyfikacje plików zawartości, takich jak HTML i CSS, nie wymagają ponownej kompilacji w aplikacji internetowej .NET Core. Z tego względu aktywna sesja F5 automatycznie synchronizuje wszystkie zmodyfikowane pliki zawartości z uruchomionym kontenerem w usłudze AKS, co umożliwia błyskawiczny podgląd zmian zawartości.

### <a name="update-a-code-file"></a>Aktualizowanie pliku kodu
Aktualizacja plików kodu wymaga nieco więcej pracy, ponieważ trzeba ponownie skompilować aplikację .NET Core i utworzyć zaktualizowane pliki binarne aplikacji.

1. Zatrzymaj debuger w programie Visual Studio.
1. Otwórz plik kodu o nazwie `Controllers/HomeController.cs` i zmień komunikat wyświetlany na stronie Informacje: `ViewData["Message"] = "Your application description page.";`
1. Zapisz plik.
1. Naciśnij klawisz **F5**, aby ponownie uruchomić debugowanie. 

Zamiast ponownego kompilowania i wdrażania nowego obrazu kontenera przy każdej zmianie kodu, co często zajmuje dużo czasu, usługa Azure Dev Spaces przyrostowo ponownie kompiluje kod w istniejącym kontenerze, co przyspiesza działanie pętli edytowania/debugowania.

Odśwież aplikację internetową w przeglądarce, a następnie otwórz stronę Informacje. Twój komunikat powinien zostać wyświetlony w interfejsie użytkownika.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-netcore-visualstudio.md)
