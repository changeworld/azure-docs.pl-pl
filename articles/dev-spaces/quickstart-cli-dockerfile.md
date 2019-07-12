---
title: Wdrażanie aplikacji za pomocą pliku Dockerfile na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Wdrażanie mikrousług za pomocą pliku Dockerfile w usłudze AKS za pomocą usługi Azure Dev miejsca do magazynowania
keywords: Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s
manager: gwallace
ms.openlocfilehash: a272047955564ba745d4cf521e560af9d24cfb9e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706837"
---
# <a name="quickstart-develop-an-application-with-a-dockerfile-on-kubernetes-using-azure-dev-spaces"></a>Szybki start: Tworzenie aplikacji z pliku Dockerfile na platformie Kubernetes przy użyciu usługi Azure Dev miejsca do magazynowania
Ten przewodnik zawiera informacje na temat wykonywania następujących czynności:

- Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
- Twórz i uruchamiaj kod w kontenerach przy użyciu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
- [Zainstalowany interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

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

W tym artykule używasz [miejsca do magazynowania Azure Dev Przykładowa aplikacja](https://github.com/Azure/dev-spaces) celu pokazanie sposobu używania usługi Azure Dev miejsca do magazynowania. Przykładowa aplikacja została napisana z rzeczywistym użyciem, ale można używać niemal dowolnego języka, tak długo, jak Podaj prawidłowy plik Dockerfile konteneryzowanie aplikacji i uruchom je w usłudze AKS.

Klonowanie aplikacji z usługi GitHub i przejdź do *dev-miejsca do magazynowania/samples/golang/wprowadzenie — pracę/webfrontend* katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/golang/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Przygotowywanie aplikacji

Aby uruchomić aplikację na Azure Dev miejsca do magazynowania, należy wykres pliku Dockerfile i Helm. W przypadku niektórych języków takich jak [Java][java-quickstart], [.NET core][netcore-quickstart], i [Node.js][nodejs — Szybki Start], klient usługi Azure Dev miejsca do magazynowania narzędzi można wygenerować wszystkie zasoby, które są potrzebne. W przypadku wielu innych języków takich jak z rzeczywistym użyciem, PHP i Python klienta narzędzi można wygenerować wykresu Helm, tak długo, jak możesz podać prawidłowy plik Dockerfile.

Przykładowa aplikacja zawiera prawidłowym pliku Dockerfile. Aby wygenerować zasoby wykresu Helm do uruchamiania aplikacji w usłudze Kubernetes, należy użyć `azds prep` polecenia:

```cmd
azds prep --public
```

Należy uruchomić `prep` polecenia *dev-miejsca do magazynowania/samples/golang/wprowadzenie — pracę/webfrontend* katalogu można prawidłowo wygenerować zasoby wykresu Helm.

# <a name="build-and-run-code-in-kubernetes"></a>Kompilowanie i uruchamianie kodu na platformie Kubernetes

Kompilowanie i uruchamianie kodu w usłudze AKS za pomocą `azds up` polecenia:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...3s
Installing Helm chart...2s
Waiting for container image build...39s
Building container image...
Step 1/7 : FROM golang:1.10
Step 2/7 : EXPOSE 80
Step 3/7 : WORKDIR /go/src
Step 4/7 : COPY src .
Step 5/7 : WORKDIR /go/src/app
Step 6/7 : RUN go install app
Step 7/7 : ENTRYPOINT /go/bin/app
Built container image in 41s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://default.webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:50199
...
```

Możesz zobaczyć usługi uruchomione przez otwarcie publicznego adresu URL i przechodząc do `/api` ścieżki. Publiczny adres URL jest wyświetlany w danych wyjściowych `azds up` polecenia. W tym przykładzie jest publiczny adres URL `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/` i spowoduje przejście do `http://default.webfrontend.1234567890abcdef1234.eus.azds.io/api`.

Jeśli użytkownik zaprzestanie `azds up` polecenie, używając *Ctrl + c*, usługa będzie w dalszym ciągu uruchamiać w usłudze AKS i publiczny adres URL, pozostaną dostępne.

## <a name="update-code"></a>Aktualizowanie kodu

Aby wdrożyć zaktualizowaną wersję usługi, zaktualizuj dowolny plik w projekcie i ponownie uruchom `azds up` polecenia. Na przykład:

1. Jeśli `azds up` jest nadal uruchomione, naciśnij klawisz *Ctrl + c*.
1. Aktualizacja [wiersz 29 w `src/app/main.go` ](https://github.com/Azure/dev-spaces/blob/master/samples/golang/getting-started/webfrontend/src/app/main.go#L29) do:
    
    ```golang
        fmt.Fprintf(w, "Hello from webfrontend in Azure")
    ```

1. Zapisz zmiany.
1. Uruchom ponownie `azds up` polecenia:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Przejdź do usługi uruchomione i obserwować zmiany.
1. Naciśnij klawisz *Ctrl + c* przestanie `azds up` polecenia.

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługi Azure Dev miejsca do magazynowania ułatwia tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie zespołowe przy pracy z różnymi wersjami lub gałęzi kodu w różnych miejsc do magazynowania.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w obszarach deweloperów platformy Azure][team-quickstart]


[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations