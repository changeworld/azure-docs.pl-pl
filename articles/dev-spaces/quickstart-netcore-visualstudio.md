---
title: 'Debugowanie i itercja w udusianie w uduszenie: Visual Studio & .NET Core'
services: azure-dev-spaces
ms.date: 11/13/2019
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Dev Spaces i visual studio do debugowania i szybkiego iteracji aplikacji .NET Core w usłudze Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
manager: gwallace
ms.custom: vs-azure
ms.workload: azure-vs
ms.openlocfilehash: 5d0d1541600f4c10b021d5d7f7f435f1aa5ae589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239720"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio--net-core---azure-dev-spaces"></a>Szybki start: debugowanie i iteracja w usłudze Kubernetes: Visual Studio & .NET Core — Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerach przy użyciu programu Visual Studio.
- Kod debugowania uruchomiony w klastrze przy użyciu programu Visual Studio.

Usługa Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Kod Java i Visual Studio](quickstart-java.md)
- [Node.js i Kod programu Visual Studio](quickstart-nodejs.md)
- [Kod programu .NET Core i Visual Studio](quickstart-netcore.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- Visual Studio 2019 w systemie Windows z zainstalowanym obciążeniem programu Azure Development. Programu Visual Studio 2017 można również używać w systemie Windows z zainstalowanym obciążeniem web development i [narzędziami programu Visual Studio dla aplikacji Kubernetes.](https://aka.ms/get-vsk8stools) Jeśli nie masz zainstalowanego programu Visual Studio, pobierz go [tutaj](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Aby utworzyć klaster:

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Wybierz *+ Utwórz zasób > usługę Kubernetes*. 
1. Wprowadź _prefiks subskrypcji,_ _grupa zasobów_, _nazwa klastra Kubernetes_, _Region_, _wersja Kubernetes_i _prefiks nazwy DNS_.

    ![Tworzenie usługi AKS w witrynie Azure portal](media/get-started-netcore-visualstudio/create-aks-portal.png)

1. Kliknij pozycję *Przegląd + utwórz*.
1. Kliknij przycisk *Utwórz*.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usług Azure Dev Spaces w klastrze usługi AKS

Przejdź do klastra AKS w portalu Azure i kliknij pozycję *Miejsca deweloperów*. Zmień *użyj spacji deweloperskich* na *Tak* i kliknij przycisk *Zapisz*.

![Włączanie przestrzeni deweloperskich w witrynie Azure portal](media/get-started-netcore-visualstudio/enable-dev-spaces-portal.png)

## <a name="create-a-new-aspnet-web-app"></a>Tworzenie nowej ASP.NET aplikacji sieci Web

1. Otwórz program Visual Studio.
1. Tworzenie nowego projektu.
1. Wybierz *ASP.NET Podstawową aplikację sieci Web* i kliknij przycisk *Dalej*.
1. Nadaj nazwę *witrynie webfront projektu* i kliknij przycisk *Utwórz*.
1. Po wyświetleniu monitu wybierz pozycję *Aplikacja sieci Web (Model-View-Controller)* dla szablonu.
1. Wybierz *.NET Core* i *ASP.NET Core 2.1* u góry.
1. Kliknij przycisk *Utwórz*.

## <a name="connect-your-project-to-your-dev-space"></a>Łączenie projektu z przestrzenią deweloperów

W projekcie wybierz **pozycję Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania, jak pokazano poniżej.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

W oknie dialogowym Azure Dev Spaces wybierz *klaster subskrypcji* i *usługi Azure Kubernetes*. Pozostaw *miejsce* ustawione *jako domyślne* i włącz pole wyboru *Publicznie dostępne.* Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

Ten proces wdraża usługę do *domyślnego* miejsca deweloperskiego z publicznie dostępnym adresem URL. Jeśli wybierzesz klaster, który nie został skonfigurowany do pracy z usługą Azure Dev Spaces, zobaczysz komunikat z pytaniem, czy chcesz go skonfigurować. Kliknij przycisk *OK*.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Publiczny adres URL usługi uruchomionej w *domyślnym* obszarze deweloperskim jest wyświetlany w oknie *Dane wyjściowe:*

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

Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:62266
Completed warmup for project 'webfrontend' in 125 seconds.
```

W powyższym przykładzie publiczny http://default.webfrontend.1234567890abcdef1234.eus.azds.io/adres URL to . 

Wybierz **debugowanie,** a następnie **rozpocznij debugowanie**. Po kilku sekundach zostanie uruchomiony usługa i Visual Studio otworzy przeglądarkę z publicznym adresem URL usługi. Jeśli przeglądarka nie otwiera się automatycznie, przejdź do publicznego adresu URL usługi w przeglądarce i wejdź w interakcję z usługą działającą w przestrzeni deweloperów.

Ten proces może mieć wyłączony publiczny dostęp do usługi. Aby włączyć dostęp publiczny, można zaktualizować [wartość transferuwstawnika ruchu przychodzącego w *pliku values.yaml*][ingress-update].

## <a name="update-code"></a>Aktualizowanie kodu

Jeśli program Visual Studio jest nadal połączony z miejscem deweloperskim, kliknij przycisk stop. Zmień linię 20 na: `Controllers/HomeController.cs`
    
```csharp
ViewData["Message"] = "Your application description page in Azure.";
```

Zapisz zmiany i wybierz **debugowanie,** a następnie **rozpocznij debugowanie**. Po kilku sekundach zostanie uruchomiony usługa i Visual Studio otworzy przeglądarkę z publicznym adresem URL usługi. Jeśli przeglądarka nie otwiera się automatycznie, nawiguj po publicznym adresie URL usługi w przeglądarce i kliknij przycisk *Informacje*. Należy zauważyć, że zostanie wyświetlony zaktualizowany komunikat.

Zamiast przebudowywania i ponownego wprowadzania nowego obrazu kontenera za każdym razem, gdy są dokonywane zmiany kodu, usługa Azure Dev Spaces stopniowo ponownie kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Jeśli program Visual Studio jest nadal połączony z miejscem deweloperskim, kliknij przycisk stop. Otwórz `Controllers/HomeController.cs` i kliknij gdzieś na linii 20, aby umieścić tam kursor. Aby ustawić punkt przerwania trafienia *F9* lub kliknij przycisk *Debugowanie,* a następnie *przełącz punkt przerwania*. Aby uruchomić usługę w trybie debugowania w przestrzeni deweloperskiej, naciśnij *klawisz F5* lub kliknij przycisk *Debugowanie,* a następnie *rozpocznij debugowanie*.

Otwórz usługę w przeglądarce i nie zwróć uwagi na to, że nie zostanie wyświetlony żaden komunikat. Powrót do programu Visual Studio i obserwować wiersz 20 jest wyróżniony. Ustawiony punkt przerwania wstrzymał usługę w wierszu 20. Aby wznowić usługę, naciśnij *klawisz F5* lub kliknij przycisk *Debugowanie,* a następnie *kontynuuj*. Wróć do przeglądarki i zwróć uwagę, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w usłudze Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątkach.

Usuń punkt przerwania, umieszczając kursor w wierszu 20 i uderzając `Controllers/HomeController.cs` *F9*.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

Przejdź do grupy zasobów w witrynie Azure portal i kliknij pozycję *Usuń grupę zasobów*. Alternatywnie można użyć polecenia [az aks delete:](/cli/azure/aks#az-aks-delete)

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-netcore-visualstudio.md)

[ingress-update]: how-dev-spaces-works-up.md#how-running-your-code-is-configured
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
