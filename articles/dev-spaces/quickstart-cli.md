---
title: Tworzenie aplikacji w u kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Ten przewodnik Szybki start pokazuje, jak używać usługi Azure Dev Spaces i wiersza polecenia do tworzenia aplikacji w usłudze Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8ee5cba06d9a526640d9057ee88a681d46392f4f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239706"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Szybki start: tworzenie aplikacji w usłudze Kubernetes — azure dev spaces
Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Tworzenie i uruchamianie kodu w kontenerach przy użyciu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
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

Sklonuj aplikację z gitHub i przejdź do katalogu *dev-spaces/samples/nodejs/getting-started/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Przygotowanie aplikacji

Aby uruchomić aplikację w usłudze Azure Dev Spaces, potrzebujesz wykresu Dockerfile i Helm. W przypadku niektórych języków, takich jak [Java][java-quickstart], [.NET core][netcore-quickstart]i [Node.js][nodejs-quickstart], narzędzia klienta usługi Azure Dev Spaces mogą generować wszystkie potrzebne zasoby. W wielu innych językach, takich jak Go, PHP i Python, narzędzie klienta może generować wykres Helm, o ile można podać prawidłowy plik Dockerfile.

Generowanie zasobów wykresu Platformy Docker i Helm do uruchamiania aplikacji w umięśnych za pomocą `azds prep` polecenia:

```cmd
azds prep --enable-ingress
```

Aby poprawnie `prep` wygenerować zasoby wykresu Platformy Docker i Helm, należy uruchomić polecenie z katalogu *dev-spaces/samples/nodejs/getting-started/webfrontend.*

> [!TIP]
> Polecenie `prep` próbuje wygenerować [wykres Dockerfile i Helm](how-dev-spaces-works-prep.md#prepare-your-code) dla projektu. Usługa Azure Dev Spaces używa tych plików do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

Skompiluj i uruchom kod `azds up` w aks za pomocą polecenia:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Możesz zobaczyć uruchomione usługi, otwierając publiczny adres URL, który `azds up` jest wyświetlany w danych wyjściowych z polecenia. W tym przykładzie publiczny *http://webfrontend.1234567890abcdef1234.eus.azds.io/* adres URL to .

> [!NOTE]
> Po przejściu do usługi `azds up`podczas uruchamiania, ślady żądań HTTP `azds up` są również wyświetlane w danych wyjściowych polecenia. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Można wyłączyć te ślady przy użyciu `--disable-http-traces` podczas uruchamiania `azds up`.

Jeśli `azds up` zatrzymasz polecenie przy użyciu *klawiszy Ctrl+c,* usługa będzie nadal działać w usłudze AKS, a publiczny adres URL pozostanie dostępny.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, można zaktualizować dowolny plik `azds up` w projekcie i ponownie uruchomić polecenie. Przykład:

1. Jeśli `azds up` nadal działa, naciśnij *klawisze Ctrl+c*.
1. Aktualizacja [wiersza 13 w: `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Zapisz zmiany.
1. Uruchom ponownie `azds up` polecenie:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Przejdź do uruchomionej usługi i obserwuj zmiany.
1. Naciśnij *klawisze Ctrl+c,* aby zatrzymać `azds up` polecenie.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service