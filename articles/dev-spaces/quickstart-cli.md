---
title: Wdróż aplikację w usłudze Kubernetes przy użyciu Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Wdrażanie mikrousługi na AKS z Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
manager: gwallace
ms.openlocfilehash: b845a176497891f55990c560e719832c6eaebb73
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842527"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Szybki start: Opracowywanie aplikacji na Kubernetes przy użyciu Azure Dev Spaces
Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Opracowywanie i uruchamianie kodu w kontenerach przy użyciu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

Należy utworzyć klaster AKS w [obsługiwanym regionie][supported-regions]. Poniższe polecenia tworzą grupę zasobów o nazwie Moja *zasobów* i klaster AKS o nazwie *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Włączanie Azure Dev Spaces w klastrze AKS

`use-dev-spaces` Użyj polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami. Poniższe polecenie włącza miejsca deweloperskie w klastrze *MyAKS* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

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

Sklonuj aplikację z witryny GitHub i przejdź do katalogu *dev Spaces/Samples/NodeJS/Start-Started/webfrontonu* :

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Przygotowywanie aplikacji

Aby można było uruchomić aplikację na Azure Dev Spaces, wymagany jest wykres pliku dockerfile i Helm. W przypadku niektórych języków, takich jak [Java][java-quickstart], [.NET Core][netcore-quickstart]i [Node. js][nodejs-quickstart], narzędzia klienta Azure dev Spaces mogą generować wszystkie potrzebne zasoby. W przypadku wielu innych języków, takich jak go, PHP i Python, narzędzia klienckie mogą generować wykres Helm, o ile można podać prawidłowy pliku dockerfile.

Wygeneruj elementy zawartości wykresu Docker i Helm na potrzeby uruchamiania aplikacji w programie Kubernetes `azds prep` przy użyciu polecenia:

```cmd
azds prep --public
```

Należy uruchomić `prep` polecenie z katalogu *dev-Spaces/Samples/NodeJS/Data-Start/webfronton* , aby poprawnie wygenerować zasoby wykresu Docker i Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

Kompiluj i uruchamiaj kod w AKS przy użyciu `azds up` polecenia:

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

Możesz zobaczyć, że usługa jest uruchomiona, otwierając publiczny adres URL, który jest wyświetlany w danych wyjściowych `azds up` polecenia. W tym przykładzie publiczny adres URL to *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

> [!NOTE]
> Gdy przejdziesz do usługi w trakcie `azds up`działania, w danych wyjściowych `azds up` polecenia są również wyświetlane dane śledzenia żądań HTTP. Te ślady mogą pomóc w rozwiązywaniu problemów i debugowaniu usługi. Te ślady można wyłączyć przy `--disable-http-traces` użyciu programu `azds up`podczas uruchamiania.

Jeśli zatrzymasz `azds up` polecenie za pomocą *klawiszy CTRL + c*, usługa będzie nadal działać w AKS, a publiczny adres URL pozostanie dostępny.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, możesz zaktualizować dowolny plik w projekcie i ponownie uruchomić `azds up` polecenie. Na przykład:

1. Jeśli `azds up` nadal działa, naciśnij *klawisze CTRL + c*.
1. Aktualizuj [wiersz 13 w `server.js` programie](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) do:
    
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

1. Przejdź do działającej usługi i obserwuj wprowadzone zmiany.
1. Naciśnij *klawisze CTRL + c* , aby `azds up` zatrzymać polecenie.

## <a name="clean-up-your-azure-resources"></a>Czyszczenie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations