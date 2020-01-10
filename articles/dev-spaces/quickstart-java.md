---
title: 'Debugowanie i iteracja na Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: W tym przewodniku szybki start pokazano, jak używać Azure Dev Spaces i Visual Studio Code do debugowania i szybkiej iteracji aplikacji Java w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Java, Helm, Service siatk, routing sieci usługi, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: c1f346aceb6e710001911d7575024288c9c2ba6b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771074"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Szybki Start: debugowanie i iteracja na Kubernetes z Visual Studio Code i Java — Azure Dev Spaces

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjnie opracowuj kod w kontenerach przy użyciu Visual Studio Code.
- Debuguj kod w obszarze deweloperskim z Visual Studio Code.

Azure Dev Spaces umożliwia również debugowanie i iterację przy użyciu:
- [Node. js i Visual Studio Code](quickstart-nodejs.md)
- [.NET Core i Visual Studio Code](quickstart-netcore.md)
- [.NET Core i Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Visual Studio Code zainstalowane](https://code.visualstudio.com/download).
- Debuger [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [Java dla Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) zainstalowanych rozszerzeń programu Visual Studio Code.
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Maven zainstalowany i skonfigurowany](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

> [!NOTE]
> `use-dev-spaces` polecenie zainstaluje również interfejs wiersza polecenia Azure Dev Spaces, jeśli nie został jeszcze zainstalowany. Nie można zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

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

W tym artykule użyto [Azure dev Spaces przykładowej aplikacji](https://github.com/Azure/dev-spaces) do zademonstrowania przy użyciu Azure dev Spaces.

Sklonuj aplikację z usługi GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Przygotuj przykładową aplikację w Visual Studio Code

Otwórz Visual Studio Code, kliknij *plik* , a następnie *Otwórz...* , przejdź do katalogu *dev-Spaces/Samples/Java/Start/webfrontonu* , a następnie kliknij przycisk *Otwórz*.

Masz teraz otwarty projekt *webfrontonu* w Visual Studio Code. Aby uruchomić aplikację w obszarze dev, wygeneruj zasoby wykresu Docker i Helm przy użyciu rozszerzenia Azure Dev Spaces w palecie poleceń.

Aby otworzyć paletę poleceń w Visual Studio Code, kliknij przycisk *Widok* i *paleta poleceń*. Zacznij pisać `Azure Dev Spaces` a następnie kliknij pozycję `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Przygotuj pliki konfiguracyjne dla Azure Dev Spaces](./media/common/command-palette.png)

Gdy Visual Studio Code również zostanie wyświetlony komunikat z prośbą o skonfigurowanie obrazów podstawowych, uwidoczniony port i publiczny punkt końcowy, wybierz `Azul Zulu OpenJDK for Azure (Free LTS)` dla obrazu podstawowego, `8080` dla uwidocznionego portu i `Yes`, aby włączyć publiczny punkt końcowy.

![Wybierz obraz podstawowy](media/get-started-java/select-base-image.png)

![Wybierz uwidoczniony port](media/get-started-java/select-exposed-port.png)

![Wybierz publiczny punkt końcowy](media/get-started-java/select-public-endpoint.png)

To polecenie przygotowuje projekt do uruchomienia w Azure Dev Spaces przez wygenerowanie wykresu pliku dockerfile i Helm. Generuje również katalog *. programu vscode* z konfiguracją debugowania w katalogu głównym projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Kompiluj i uruchamiaj kod w programie Kubernetes z programu Visual Studio

Kliknij ikonę *debugowania* po lewej stronie, a następnie kliknij pozycję *Uruchom program Java (AZDS)* w górnej części ekranu.

![Uruchom program Java](media/get-started-java/debug-configuration.png)

To polecenie kompiluje i uruchamia usługę w Azure Dev Spaces. W oknie *terminalu* u dołu są wyświetlane dane wyjściowe kompilacji i adresy URL dla usługi uruchomionej Azure dev Spaces. W *konsoli debugowania* są wyświetlane dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń Azure Dev Spaces w *palecie poleceń*, upewnij się, że zainstalowano [rozszerzenie Visual Studio Code dla Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź również, czy otwarto w Visual Studio Code folder *dev Spaces/Samples/Java/Start-Started/webfrontonie* .

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL.

Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać debuger.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, możesz zaktualizować dowolny plik w projekcie i ponownie uruchomić *program Java (AZDS)* . Przykład:

1. Jeśli aplikacja jest nadal uruchomiona, kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby ją zatrzymać.
1. Zaktualizuj [wiersz 19 w `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) do:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Zapisz zmiany.
1. Ponownie uruchom *program Java (AZDS)* .
1. Przejdź do działającej usługi i obserwuj wprowadzone zmiany.
1. Kliknij pozycję *Debuguj* , a następnie *Zatrzymaj debugowanie* , aby zatrzymać aplikację.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom usługę przy użyciu *programu uruchomieniowego języka Java (AZDS)* . Powoduje to również uruchomienie usługi w trybie debugowania.

Przejdź z powrotem do widoku *Eksploratora* , klikając przycisk *Wyświetl* , a następnie *Eksploratora*. Otwórz `src/main/java/com/ms/sample/webfrontend/Application.java` i kliknij gdziekolwiek w wierszu 19, aby umieścić w nim kursor. Aby ustawić punkt przerwania, naciśnij klawisz *F9* lub kliknij pozycję *Debuguj* , a następnie *Przełącz punkt przerwania*.

Otwórz usługę w przeglądarce i zwróć uwagę, że komunikat nie jest wyświetlany. Wróć do Visual Studio Code i obserwuj wiersz 19. Ustawiony punkt przerwania został wstrzymany usługi w wierszu 19. Aby wznowić działanie usługi, naciśnij klawisz *F5* lub kliknij pozycję *Debuguj* i *Kontynuuj*. Wróć do przeglądarki i Zauważ, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 19 w `src/main/java/com/ms/sample/webfrontend/Application.java` i naciskając klawisz *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizuj kod z Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, zaktualizuj wiersz 19 w `src/main/java/com/ms/sample/webfrontend/Application.java`. Przykład:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Zapisz plik. Kliknij pozycję *Debuguj* , a następnie *ponownie uruchom debugowanie* lub na *pasku narzędzi debugowania*kliknij przycisk *Uruchom ponownie debugowanie* .

![Odśwież debugowanie](media/common/debug-action-refresh.png)

Otwórz usługę w przeglądarce i sprawdź, czy zostanie wyświetlony zaktualizowany komunikat.

Zamiast ponownie kompilować i wdrażać nowy obraz kontenera przy każdej modyfikacji kodu, Azure Dev Spaces przyrostowo kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
