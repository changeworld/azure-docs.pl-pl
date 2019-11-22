---
title: Instalowanie Azure Dev Spaces na AKS i narzędzi po stronie klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Dowiedz się, jak zainstalować Azure Dev Spaces w klastrze AKS i zainstalować narzędzia po stronie klienta.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: c62fe38a12b5ec279bc51fe8bc0d340e2f439200
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280054"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Instalowanie Azure Dev Spaces na AKS i narzędzi po stronie klienta

W tym artykule przedstawiono kilka sposobów instalowania Azure Dev Spaces w klastrze AKS oraz instalowania narzędzi po stronie klienta.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Instalowanie Azure Dev Spaces przy użyciu interfejsu wiersza polecenia

Aby można było zainstalować miejsca deweloperskie za pomocą interfejsu wiersza polecenia, potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Zainstalowano interfejs wiersza polecenia platformy Azure][install-cli].
* [Klaster AKS][create-aks-cli] w [obsługiwanym regionie][supported-regions].

Użyj `use-dev-spaces` polecenia, aby włączyć miejsca deweloperskie w klastrze AKS i postępuj zgodnie z monitami.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Powyższe polecenie włącza miejsca deweloperskie w klastrze *myAKSCluster* w grupie Grupa *zasobów* i tworzy *domyślny* obszar dev.

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Polecenie `use-dev-spaces` instaluje również interfejs wiersza polecenia Azure Dev Spaces.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Instalowanie Azure Dev Spaces przy użyciu Azure Portal

Aby można było zainstalować miejsca deweloperskie przy użyciu Azure Portal, potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Klaster AKS][create-aks-portal] w [obsługiwanym regionie][supported-regions].

Aby zainstalować Azure Dev Spaces przy użyciu Azure Portal:
1. Zaloguj się w witrynie [Azure Portal][az-portal].
1. Przejdź do klastra AKS.
1. Kliknij pozycję *spacje dla deweloperów*.
1. Zmień *wartość opcji* *Włącz funkcję miejsca* do tworzenia i kliknij przycisk *Zapisz*.

![Włącz funkcję Spaces dev w Azure Portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Zainstalowanie Azure Dev Spaces przy użyciu Azure Portal nie **powoduje** zainstalowania żadnych narzędzi po stronie klienta dla programu Azure dev Spaces.

## <a name="install-the-client-side-tooling"></a>Instalowanie narzędzi po stronie klienta

Azure Dev Spaces narzędzi po stronie klienta można użyć do współdziałania z miejscami deweloperskimi w klastrze AKS z komputera lokalnego. Istnieje kilka sposobów instalacji narzędzi po stronie klienta:

* W [Visual Studio Code][vscode]zainstaluj [rozszerzenie Azure dev Spaces][vscode-extension].
* W programie [Visual Studio 2019][visual-studio]Zainstaluj obciążenie Programowanie na platformie Azure.
* W programie Visual Studio 2017 Zainstaluj obciążenie programowanie w sieci Web i [Visual Studio Tools for Kubernetes][visual-studio-k8s-tools].
* Pobierz i zainstaluj interfejs wiersza polecenia [systemu Windows][cli-win], [Mac][cli-mac]lub [Linux][cli-linux] .

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
