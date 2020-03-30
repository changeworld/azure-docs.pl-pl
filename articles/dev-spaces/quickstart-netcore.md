---
title: 'Debugowanie i itercja w udusianie w uduszenie: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Dev Spaces i Visual Studio Code do debugowania i szybkiego iteracji aplikacji .NET Core w usłudze Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240208"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Szybki start: debugowanie i iteracja w usłudze Kubernetes: Visual Studio Code i .NET Core — usługi Azure Dev Spaces

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteratively opracowania kodu w kontenerach przy użyciu programu Visual Studio Code.
- Debuguj kod w przestrzeni deweloperskiej z programu Visual Studio Code.

Usługa Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Kod Java i Visual Studio](quickstart-java.md)
- [Node.js i Kod programu Visual Studio](quickstart-nodejs.md)
- [Program .NET Core i visual studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany kod programu Visual Studio](https://code.visualstudio.com/download).
- Zainstalowane rozszerzenia [azure dev spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [c#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) dla kodu programu Visual Studio.
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie *MyResourceGroup* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usług Azure Dev Spaces w klastrze usługi AKS

Użyj `use-dev-spaces` tego polecenia, aby włączyć miejsca dewelopera w klastrze AKS i postępować zgodnie z instrukcjami. Poniższe polecenie włącza miejsca dewelopera w klastrze *MyAKS* w grupie *MyResourceGroup* i tworzy *domyślną* przestrzeń dewelopera.

> [!NOTE]
> Polecenie `use-dev-spaces` zainstaluje również interfejsu wiersza polecenia azure dev spaces, jeśli nie jest jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia usługi Azure Dev Spaces w usłudze Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Pobierz przykładowy kod aplikacji

W tym artykule używasz [przykładowej aplikacji Azure Dev Spaces,](https://github.com/Azure/dev-spaces) aby zademonstrować przy użyciu usługi Azure Dev Spaces.

Sklonuj aplikację z gitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Przygotowywanie przykładowej aplikacji w programie Visual Studio Code

Otwórz program Visual Studio Code, kliknij pozycję *Plik,* a następnie *otwórz...* przejdź do katalogu *dev-spaces/samples/dotnetcore/getting-started/webfrontend* i kliknij przycisk *Otwórz*.

Projekt *webfrontend* jest teraz otwarty w programie Visual Studio Code. Aby uruchomić aplikację w obszarze deweloperskim, wygeneruj zasoby wykresu Platformy Docker i Helm przy użyciu rozszerzenia Azure Dev Spaces w palecie poleceń.

Aby otworzyć paletę poleceń w programie Visual Studio Code, kliknij pozycję Wyświetl, a następnie kliknij polecenie *Wyświetl,* a następnie *pozycję Paleta poleceń*. Zacznij pisać `Azure Dev Spaces` i `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`kliknij przycisk .

![Przygotowywanie plików konfiguracyjnych dla usługi Azure Dev Spaces](./media/common/command-palette.png)

Gdy program Visual Studio Code monituje również o `Yes` skonfigurowanie publicznego punktu końcowego, wybierz opcję włączenia publicznego punktu końcowego.

![Wybieranie publicznego punktu końcowego](media/common/select-public-endpoint.png)

To polecenie przygotowuje projekt do uruchomienia w usłudze Azure Dev Spaces przez wygenerowanie wykresu Dockerfile i Helm. Generuje również katalog *vscode* z konfiguracją debugowania w katalogu głównym projektu.

> [!TIP]
> [Wykres Dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez usługę Azure Dev Spaces do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Tworzenie i uruchamianie kodu w umilaniu z programu Visual Studio Code

Kliknij ikonę *Debugowania* po lewej stronie i kliknij przycisk *.NET Core Launch (AZDS)* u góry.

![](media/get-started-netcore/debug-configuration.png)

To polecenie tworzy i uruchamia usługę w usłudze Azure Dev Spaces w trybie debugowania. Okno *Terminal* u dołu zawiera dane wyjściowe kompilacji i adresy URL dla usługi uruchomionej w usłudze Azure Dev Spaces. *Konsola debugowania* pokazuje dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń usługi Azure Dev Spaces na *palecie poleceń,* upewnij się, że zainstalowano [rozszerzenie kodu programu Visual Studio dla usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź również otwarty *katalog dev-spaces/samples/dotnetcore/getting-started/webfrontend* w programie Visual Studio Code.

Możesz zobaczyć uruchomione usługi, otwierając publiczny adres URL.

> [!Note]
> Początkowo publiczny adres URL może wyświetlać błąd *nieprawidłowej bramy.* Odczekaj kilka sekund przed odświeżeniem strony sieci Web, a usługa powinna być uruchomiona.

Kliknij *przycisk Debugowanie,* a następnie *pozycję Zatrzymaj debugowanie,* aby zatrzymać debuger.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, można zaktualizować dowolny plik w projekcie i ponownie uruchomić *program .NET Core Launch (AZDS).* Przykład:

1. Jeśli aplikacja jest nadal uruchomiona, kliknij przycisk *Debugowanie,* a następnie *zatrzymaj debugowanie,* aby ją zatrzymać.
1. Aktualizacja [wiersza 22 w: `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Zapisz zmiany.
1. Uruchom ponownie *program .NET Core Launch (AZDS).*
1. Przejdź do uruchomionej usługi i kliknij przycisk *Informacje*.
1. Obserwuj zmiany.
1. Kliknij *przycisk Debugowanie,* a następnie *zatrzymaj debugowanie,* aby zatrzymać aplikację.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom usługę w trybie debugowania przy użyciu *funkcji .NET Core Launch (AZDS).*

Przejdź wstecz do widoku *Eksploratora,* klikając pozycję *Widok,* a następnie *pozycję Explorer*. Otwórz `Controllers/HomeController.cs` i kliknij gdzieś na linii 22, aby umieścić tam kursor. Aby ustawić punkt przerwania trafienia *F9* lub kliknij przycisk *Debugowanie,* a następnie *przełącz punkt przerwania*.

Otwórz usługę w przeglądarce i nie zwróć uwagi na to, że nie zostanie wyświetlony żaden komunikat. Powrót do programu Visual Studio Code i obserwować wiersz 20 jest wyróżniony. Ustawiony punkt przerwania wstrzymał usługę w wierszu 20. Aby wznowić usługę, naciśnij *klawisz F5* lub kliknij przycisk *Debugowanie,* a następnie *kontynuuj*. Wróć do przeglądarki i zwróć uwagę, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w usłudze Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątkach.

Usuń punkt przerwania, umieszczając kursor w wierszu 22 i uderzając `Controllers/HomeController.cs` *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizowanie kodu z programu Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, `Controllers/HomeController.cs`aktualizuj wiersz 22 w pliku . Przykład:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Zapisz plik. Kliknij *przycisk Debugowanie,* a następnie *uruchom ponownie debugowanie* lub na *pasku narzędzi Debugowanie*kliknij przycisk *Uruchom ponownie debugowanie.*

![](media/common/debug-action-refresh.png)

Otwórz usługę w przeglądarce i zwróć uwagę, że zostanie wyświetlony zaktualizowany komunikat.

Zamiast przebudowywania i ponownego wprowadzania nowego obrazu kontenera za każdym razem, gdy są dokonywane zmiany kodu, usługa Azure Dev Spaces stopniowo ponownie kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
