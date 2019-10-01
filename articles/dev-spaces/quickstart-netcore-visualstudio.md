---
title: Debugowanie i iteracja przy użyciu programów Visual Studio i .NET Core w systemie AKS z Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Szybkie tworzenie Kubernetes przy użyciu kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 41dea16f7938d391f8cdf03e1a731e8082f74b26
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695486"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-and-net-core-on-kubernetes-with-azure-dev-spaces"></a>Szybki Start: debugowanie i iteracja przy użyciu programów Visual Studio i .NET Core w systemie Kubernetes z Azure Dev Spaces

W tym przewodniku dowiesz się, jak:

- Skonfiguruj Azure Dev Spaces z zarządzanym klastrem Kubernetes na platformie Azure.
- Iteracyjnie opracowuj kod w kontenerach za pomocą programu Visual Studio.
- Debuguj kod uruchomiony w klastrze przy użyciu programu Visual Studio.

Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Java i Visual Studio Code](quickstart-java.md)
- [Node. js i Visual Studio Code](quickstart-nodejs.md)
- [.NET Core i Visual Studio Code](quickstart-netcore.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- Program Visual Studio 2019 w systemie Windows z zainstalowanym obciążeniem programowania na platformie Azure. Możesz również użyć programu Visual Studio 2017 w systemie Windows z zainstalowanym obciążeniem programowaniem w sieci Web i [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools) . Jeśli nie masz zainstalowanego programu Visual Studio, Pobierz go [tutaj](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Aby utworzyć klaster:

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Wybierz pozycję *+ Utwórz zasób > usługę Kubernetes*. 
1. Wprowadź _subskrypcję_, _grupę zasobów_, _nazwę klastra Kubernetes_, _region_, _wersję Kubernetes_i _prefiks nazwy DNS_.

    ![Utwórz AKS w Azure Portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kliknij przycisk *Przegląd + Utwórz*.
1. Kliknij przycisk *Utwórz*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Przejdź do klastra AKS w Azure Portal a następnie kliknij pozycję *Spaces*(Tworzenie). Zmień *wartość opcji* *Włącz funkcję miejsca* do tworzenia i kliknij przycisk *Zapisz*.

![Włącz funkcję Spaces dev w Azure Portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Tworzenie nowej aplikacji sieci Web ASP.NET

1. Otwórz program Visual Studio.
1. Utwórz nowy projekt.
1. Wybierz *ASP.NET Core aplikację sieci Web* i nadaj nazwę projektowi *webfrontonu*.
1. Kliknij przycisk *OK*.
1. Po wyświetleniu monitu wybierz pozycję *aplikacja sieci Web (Model-View-Controller)* dla szablonu.
1. Wybierz pozycję *.NET Core* i *ASP.NET Core 2,0* w górnej części strony.
1. Kliknij przycisk *OK*.

## <a name="connect-your-project-to-your-dev-space"></a>Połącz projekt z obszarem deweloperskim

W projekcie wybierz **Azure dev Spaces** z listy rozwijanej ustawienia uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W oknie dialogowym Azure Dev Spaces wybierz *subskrypcję* i *klaster usługi Azure Kubernetes*. Pozostaw *miejsce* ustawione na *domyślne* i Włącz pole wyboru *dostępne publicznie* . Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ten proces służy do wdrażania usługi w *domyślnym* obszarze deweloperskim z publicznie dostępnym adresem URL. Jeśli wybierzesz klaster, który nie został skonfigurowany do pracy z Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować. Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Publiczny adres URL dla usługi uruchomionej w *domyślnym* obszarze dev jest wyświetlany w oknie *danych wyjściowych* :

```cmd
Starting warmup for project 'webfrontend'.
Waiting for namespace to be provisioned.
Using dev space 'default' with target 'MyAKS'
...
Successfully built 1234567890ab
Successfully tagged webfrontend:devspaces-11122233344455566
Built container image in 39s
Waiting for container...
36s

Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

W powyższym przykładzie publiczny adres URL to http://webfrontend.1234567890abcdef1234.eus.azds.io/. Przejdź do publicznego adresu URL usługi i skontaktuj się z usługą uruchomioną w obszarze dev.

Ten proces mógł wyłączyć publiczny dostęp do usługi. Aby włączyć dostęp publiczny, można zaktualizować wartość transferu danych przychodzących [w *wartości. YAML*][ingress-update].

## <a name="update-code"></a>Aktualizuj kod

Jeśli program Visual Studio jest nadal połączony z obszarem deweloperskim, kliknij przycisk Zatrzymaj. Zmień wiersz 20 w `Controllers/HomeController.cs` na:
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Zapisz zmiany i uruchom usługę przy użyciu **Azure dev Spaces** z listy rozwijanej ustawienia uruchamiania. Otwórz publiczny adres URL usługi w przeglądarce i kliknij pozycję *informacje*. Zwróć uwagę, że zostanie wyświetlony zaktualizowany komunikat.

Zamiast ponownie kompilować i wdrażać nowy obraz kontenera przy każdej modyfikacji kodu, Azure Dev Spaces przyrostowo kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Jeśli program Visual Studio jest nadal połączony z obszarem deweloperskim, kliknij przycisk Zatrzymaj. Otwórz `Controllers/HomeController.cs` i kliknij gdziekolwiek w wierszu 20, aby umieścić w nim kursor. Aby ustawić punkt przerwania, naciśnij klawisz *F9* lub kliknij pozycję *Debuguj* , a następnie *Przełącz punkt przerwania*. Aby uruchomić usługę w trybie debugowania w obszarze deweloperskim, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* , a następnie *Rozpocznij debugowanie*.

Otwórz usługę w przeglądarce i zwróć uwagę, że komunikat nie jest wyświetlany. Wróć do programu Visual Studio i obserwuj wiersz 20 został wyróżniony. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 20. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* i *Kontynuuj*. Wróć do przeglądarki i Zauważ, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 20 w `Controllers/HomeController.cs` i naciskając klawisz *F9*.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

Przejdź do grupy zasobów w Azure Portal a następnie kliknij pozycję *Usuń grupę zasobów*. Alternatywnie możesz użyć polecenia [AZ AKS Delete](/cli/azure/aks#az-aks-delete) :

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Praca z wieloma kontenerami i programowaniem zespołu](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works.md#how-running-your-code-is-configured
[supported-regions]: about.md#supported-regions-and-configurations
