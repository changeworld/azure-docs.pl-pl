---
title: Programowanie za pomocą języka Java na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Szybkie opracowywanie usługi Kubernetes przy użyciu kontenery, mikrousługi i języka Java na platformie Azure
keywords: Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, Java, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s
manager: gwallace
ms.openlocfilehash: b3e199f38f6f57cf10991f7e03757b8b603f74ad
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706861"
---
# <a name="quickstart-develop-with-java-on-kubernetes-using-azure-dev-spaces"></a>Szybki start: Programowanie za pomocą języka Java na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania

Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Iteracyjne tworzenie kodu w kontenerów za pomocą programu Visual Studio Code.
- Debugowanie kodu w obszarze dev z programu Visual Studio Code.


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Miejsca do magazynowania Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) i [debugera języka Java dla usługi Azure Dev miejsca do magazynowania](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) rozszerzeń programu Visual Studio Code.
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Narzędzia maven, zainstalować i skonfigurować](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Utwórz klaster Azure Kubernetes Service

Musisz utworzyć klaster usługi AKS w [obsługiwany region][supported-regions]. Poniższe polecenia Utwórz grupę zasobów o nazwie *MyResourceGroup* i klaster AKS, o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie usługi Azure Dev miejsca do magazynowania w klastrze usługi AKS

Użyj `use-dev-spaces` polecenie, aby włączyć Dev miejsca do magazynowania w klastrze usługi AKS i postępuj zgodnie z monitami. Poniższe polecenie powoduje włączenie tworzenia miejsca do magazynowania na *MyAKS* klastra w systemie *MyResourceGroup* grupy i tworzy *domyślne* dev miejsca.

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

W tym artykule używasz [miejsca do magazynowania Azure Dev Przykładowa aplikacja](https://github.com/Azure/dev-spaces) celu pokazanie sposobu używania usługi Azure Dev miejsca do magazynowania.

Klonowanie aplikacji z usługi GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Przygotowanie przykładowej aplikacji w programie Visual Studio Code

Otwórz program Visual Studio Code, kliknij *pliku* następnie *Otwórz...* , przejdź do *dev-miejsca do magazynowania/przykłady/java/pobieranie pracę/webfrontend* katalogu, a następnie kliknij przycisk *Otwórz*.

Masz teraz *webfrontend* otwarty projekt w programie Visual Studio Code. Aby uruchomić aplikację w obszarze dev, generowanie platformy Docker i Helm zasoby wykresu przy użyciu rozszerzenia Azure Dev miejsca do magazynowania w Pallette polecenia.

Aby otworzyć paletę poleceń w programie Visual Studio Code kliknij pozycję *widoku* następnie *paletę poleceń*. Rozpocznij wpisywanie ciągu `Azure Dev Spaces` i kliknij pozycję `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Przygotowanie pliki konfiguracji do usługi Azure Dev miejsca do magazynowania](./media/common/command-palette.png)

Visual Studio Code monit również skonfigurować swoje obrazy podstawowe, ujawnionych portów i publicznym punktem końcowym, wybierz `Azul Zulu OpenJDK for Azure (Free LTS)` na podstawowy obraz `8080` narażonych portu i `Yes` umożliwiające publicznym punktem końcowym.

![Wybierz podstawowy obraz](media/get-started-java/select-base-image.png)

![Ujawnionych portów](media/get-started-java/select-exposed-port.png)

![Wybierz publiczny punkt końcowy](media/get-started-java/select-public-endpoint.png)

To polecenie przygotowuje projektu do uruchamiania w obszarach deweloperów platformy Azure przez wygenerowanie pliku Dockerfile i Helm wykresu. Polecenie to generuje także *.vscode* katalog debugowania konfiguracji w katalogu głównym projektu.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Kompilowanie i uruchamianie kodu w rozwiązaniu Kubernetes za pomocą programu Visual Studio

Kliknij pozycję *debugowania* ikonę po lewej stronie i kliknij przycisk *Uruchom Program Java (AZDS, Distributed File System)* u góry.

![Uruchom Program języka Java](media/get-started-java/debug-configuration.png)

To polecenie kompiluje i uruchamia usługę w Azure Dev miejsca do magazynowania. *Terminalu* u dołu okna przedstawia dane wyjściowe kompilacji i adresów URL usługi systemem Azure Dev miejsca do magazynowania. *Konsoli debugowania* zawiera dane wyjściowe dziennika.

> [!Note]
> Jeśli nie widzisz żadnych poleceń usługi Azure Dev miejsca do magazynowania w *paletę poleceń*, upewnij się, że zainstalowano [rozszerzenie programu Visual Studio Code dla usługi Azure Dev miejsca do magazynowania](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Sprawdź także, możesz otworzyć *dev-miejsca do magazynowania/przykłady/java/pobieranie pracę/webfrontend* katalogu w programie Visual Studio Code.

Możesz zobaczyć usługi uruchomione przez otwarcie publicznego adresu URL.

Kliknij przycisk *debugowania* następnie *Zatrzymaj debugowanie* zatrzymanie debugera.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, zaktualizuj dowolny plik w projekcie i ponownie uruchom *Uruchom Program Java (AZDS, Distributed File System)* . Przykład:

1. Jeśli aplikacja jest nadal uruchomione, kliknij przycisk *debugowania* następnie *Zatrzymaj debugowanie* go zatrzymać.
1. Aktualizacja [wiersz 19 w `src/main/java/com/ms/sample/webfrontend/Application.java` ](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) do:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Zapisz zmiany.
1. Uruchom ponownie *, uruchom Program Java (AZDS)* .
1. Przejdź do usługi uruchomione i obserwować zmiany.
1. Kliknij przycisk *debugowania* następnie *Zatrzymaj debugowanie* zatrzymania aplikacji.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ustawianie i używanie punktów przerwania do debugowania

Uruchom przy użyciu usługi *Uruchom Program Java (AZDS, Distributed File System)* . To rozwiązanie działa również usługi w trybie debugowania.

Przejdź z powrotem do *Explorer* widoku, klikając *widoku* następnie *Explorer*. Otwórz `src/main/java/com/ms/sample/webfrontend/Application.java` i kliknij przycisk gdzieś w wierszu 19 tam umieścić kursor. Aby ustawić punkt przerwania trafień *F9* lub kliknij przycisk *debugowania* następnie *Przełącz punkt przerwania*.

Otwórz swoją usługę w przeglądarce i zwróć uwagę, że jest wyświetlany żaden komunikat. Wróć do programu Visual Studio Code i sprawdź, czy jest wyróżniony wiersz 19. Punkt przerwania, gdy ustawiasz została wstrzymana usługi w wierszu 19. Aby wznowić działanie usługi, trafienia *F5* lub kliknij przycisk *debugowania* następnie *Kontynuuj*. Wróć do przeglądarki i zwróć uwagę, że teraz jest wyświetlany komunikat.

Podczas uruchamiania usługi w usłudze Kubernetes załączonym debuggerze, masz pełny dostęp do debugowania informacje, takie jak stos wywołań, zmienne lokalne i informacje o wyjątku.

Usuń punkt przerwania, umieszczając kursor w wierszu 19 `src/main/java/com/ms/sample/webfrontend/Application.java` TAB i naciśnięcie klawisza *F9*.

## <a name="update-code-from-visual-studio-code"></a>Aktualizowanie kodu z programu Visual Studio Code

Gdy usługa jest uruchomiona w trybie debugowania, zaktualizuj wiersz 19 w `src/main/java/com/ms/sample/webfrontend/Application.java`. Na przykład:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Zapisz plik. Kliknij przycisk *debugowania* następnie *Uruchom ponownie debugowanie* lub *narzędzi debugowania*, kliknij przycisk *Uruchom ponownie debugowanie* przycisku.

![Odśwież debugowania](media/common/debug-action-refresh.png)

Otwórz swoją usługę w przeglądarce i zwróć uwagę, że zaktualizowano wiadomość jest wyświetlana.

Zamiast ponownego kompilowania lub wdrażania nowy obraz kontenera każdorazowo edycji kodu zostaną wprowadzone, miejsca do magazynowania Azure Dev przyrostowo następuje rekompilacja kodu w ramach istniejącego kontenera, aby zapewnić szybsze pętli Edycja i debugowanie.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługi Azure Dev miejsca do magazynowania ułatwia tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie zespołowe przy pracy z różnymi wersjami lub gałęzi kodu w różnych miejsc do magazynowania.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development (Praca z wieloma kontenerami i programowanie zespołowe)](multi-service-java.md)


[supported-regions]: about.md#supported-regions-and-configurations