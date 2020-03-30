---
title: 'Debugowanie i itercja w udusianie: Kod programu Visual Studio & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Dev Spaces i Visual Studio Code do debugowania i szybkiego iteracji aplikacji Java w usłudze Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Java, Helm, usługa mesh, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ac7a1b37b565f3589b7c049a3c1ed2a84972ded0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239730"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Szybki start: debugowanie i iteracja w usłudze Kubernetes za pomocą kodu programu Visual Studio i języka Java — usługi Azure Dev Spaces

W tym przewodniku Szybki start można skonfigurować usługi Azure Dev Spaces z zarządzanym klastrem Kubernetes i użyć aplikacji Java w programie Visual Studio Code do iteracyjnego tworzenia i debugowania kodu w kontenerach. Usługa Azure Dev Spaces umożliwia debugowanie i testowanie wszystkich składników aplikacji w usłudze Azure Kubernetes Service (AKS) przy minimalnej konfiguracji komputera deweloperskiego. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Zestaw java development kit (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Program Visual Studio Code](https://code.visualstudio.com/download)
- [Usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [debuger java dla usług Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) rozszerzenia dla programu Visual Studio Code.
- [Narzędzie CLI platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Kubernetes platformy Azure

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie *MyResourceGroup* i klaster AKS o nazwie *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usług Azure Dev Spaces w klastrze usługi AKS

Użyj `use-dev-spaces` tego polecenia, aby włączyć miejsca dewelopera w klastrze AKS i postępować zgodnie z instrukcjami. Następujące polecenie włącza miejsca dewelopera w klastrze *MyAKS* w grupie *MyResourceGroup* i tworzy *domyślną* przestrzeń dewelopera.

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

Otwórz program Visual Studio Code, wybierz **pozycję Plik,** a następnie **otwórz**, przejdź do katalogu *dev-spaces/samples/java/getting-started/webfrontend* i wybierz pozycję **Otwórz**.

Projekt *webfrontend* jest teraz otwarty w programie Visual Studio Code. Aby uruchomić aplikację w obszarze deweloperskim, wygeneruj zasoby wykresu Platformy Docker i Helm przy użyciu rozszerzenia Azure Dev Spaces w palecie poleceń.

Aby otworzyć paletę poleceń w programie Visual Studio Code, wybierz pozycję **Wyświetl,** a następnie **pozycję Paleta poleceń**. Rozpocznij `Azure Dev Spaces` wpisywanie i wybierz **pozycję Azure Dev Spaces: Przygotuj pliki konfiguracyjne dla usługi Azure Dev Spaces**.

![Przygotowywanie plików konfiguracyjnych dla usługi Azure Dev Spaces](./media/common/command-palette.png)

Gdy program Visual Studio Code monituje również o skonfigurowanie obrazów podstawowych, narażony port i publiczny punkt końcowy, wybierz `Azul Zulu OpenJDK for Azure (Free LTS)` dla obrazu podstawowego, `8080` dla narażonego portu i `Yes` włączyć publiczny punkt końcowy.

![Wybierz obraz bazowy](media/get-started-java/select-base-image.png)

![Wybieranie narażonego portu](media/get-started-java/select-exposed-port.png)

![Wybieranie publicznego punktu końcowego](media/get-started-java/select-public-endpoint.png)

To polecenie przygotowuje projekt do uruchomienia w usłudze Azure Dev Spaces przez wygenerowanie wykresu Dockerfile i Helm. Generuje również katalog *vscode* z konfiguracją debugowania w katalogu głównym projektu.

> [!TIP]
> [Wykres Dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu jest używany przez usługę Azure Dev Spaces do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Tworzenie i uruchamianie kodu w umilaniu z programu Visual Studio Code

Wybierz ikonę **debugowania** po lewej stronie i wybierz **pozycję Uruchom program Java (AZDS)** u góry.

![Uruchom program Java](media/get-started-java/debug-configuration.png)

To polecenie tworzy i uruchamia usługę w usłudze Azure Dev Spaces. Okno **Terminal** u dołu zawiera dane wyjściowe kompilacji i adresy URL usługi z uruchomioną usługą Azure Dev Spaces. **Konsola debugowania** pokazuje dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń usługi Azure Dev Spaces na **palecie poleceń,** upewnij się, że zainstalowano [rozszerzenie kodu programu Visual Studio dla usługi Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź również otwarty *katalog dev-spaces/samples/java/getting-started/webfrontend* w programie Visual Studio Code.

Możesz zobaczyć uruchomione usługi, otwierając publiczny adres URL.

Wybierz **debugowanie,** a następnie **zatrzymaj debugowanie,** aby zatrzymać debuger.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, można zaktualizować dowolny plik w projekcie i ponownie uruchomić **program Uruchom Java (AZDS).** Przykład:

1. Jeśli aplikacja jest nadal uruchomiona, wybierz **debugowanie,** a następnie **zatrzymaj debugowanie,** aby ją zatrzymać.
1. Aktualizacja [linii 19 `src/main/java/com/ms/sample/webfrontend/Application.java` w:](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19)
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Zapisz zmiany.
1. Uruchom ponownie **program Java (AZDS)**.
1. Przejdź do uruchomionej usługi i obserwuj zmiany.
1. Wybierz **debugowanie,** a następnie **zatrzymaj debugowanie,** aby zatrzymać aplikację.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom usługę za pomocą **programu Uruchom program Java (AZDS).** Spowoduje to również działanie usługi w trybie debugowania.

Przejdź wstecz do widoku **Eksploratora,** wybierając **pozycję Widok,** a następnie **Eksplorator**. Otwórz *src / main / java / com / ms / sample / webfrontend / Application.java* i kliknij gdzieś na linii 19, aby umieścić kursor tam. Aby ustawić punkt przerwania, naciśnij **klawisz F9** lub wybierz opcję **Debugowanie,** a następnie **przełącz punkt przerwania**.

Otwórz usługę w przeglądarce i nie zwróć uwagi na to, że nie zostanie wyświetlony żaden komunikat. Powrót do kodu programu Visual Studio i obserwować wiersz 19 jest wyróżniony. Ustawiony punkt przerwania wstrzymał usługę w wierszu 19. Aby wznowić usługę, naciśnij **klawisz F5** lub wybierz **pozycję Debugowanie,** a następnie **kontynuuj**. Wróć do przeglądarki i zwróć uwagę, że komunikat jest teraz wyświetlany.

Podczas uruchamiania usługi w usłudze Kubernetes z dołączonym debugerem masz pełny dostęp do informacji debugowania, takich jak stos wywołań, zmienne lokalne i informacje o wyjątkach.

Usuń punkt przerwania, umieszczając kursor w wierszu 19 w *src/main/java/com/ms/sample/webfrontend/Application.java* i naciskając **klawisz F9**.

## <a name="update-code-from-visual-studio-code"></a>Aktualizowanie kodu z programu Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, aktualizuj linię 19 w *src/main/java/com/ms/sample/webfrontend/Application.java*. Przykład:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Zapisz plik. Wybierz **opcję Debugowanie,** a następnie **uruchom ponownie debugowanie** lub na **pasku narzędzi Debugowanie**wybierz przycisk **Uruchom ponownie debugowanie.**

![Odśwież debugowanie](media/common/debug-action-refresh.png)

Otwórz usługę w przeglądarce i zwróć uwagę, że zostanie wyświetlony zaktualizowany komunikat.

Zamiast przebudowywania i ponownego wprowadzania nowego obrazu kontenera za każdym razem, gdy są dokonywane zmiany kodu, usługa Azure Dev Spaces stopniowo ponownie kompiluje kod w istniejącym kontenerze, aby zapewnić szybszą pętlę edycji/debugowania.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
