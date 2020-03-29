---
title: Włączanie usługi Azure Dev Spaces w usłudze AKS & instalowanie narzędzi po stronie klienta
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Dowiedz się, jak włączyć usługi Azure Dev Spaces w klastrze AKS i zainstalować narzędzia po stronie klienta.
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898953"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Włączanie usługi Azure Dev Spaces w klastrze usługi AKS i instalowanie narzędzi po stronie klienta

W tym artykule przedstawiono kilka sposobów włączania usługi Azure Dev Spaces w klastrze AKS oraz instalowania narzędzi po stronie klienta.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Włączanie lub usuwanie usług Azure Dev Spaces przy użyciu interfejsu wiersza polecenia

Aby można było włączyć miejsca deweloperów przy użyciu interfejsu wiersza polecenia, należy:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Zainstalowano narzędzie interfejsu wiersza polecenia platformy Azure][install-cli].
* [Klaster AKS][create-aks-cli] w [obsługiwanym regionie][supported-regions].

Użyj `use-dev-spaces` tego polecenia, aby włączyć miejsca dewelopera w klastrze AKS i postępować zgodnie z instrukcjami.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Powyższe polecenie włącza miejsca dewelopera w klastrze *myAKSCluster* w grupie *myResourceGroup* i tworzy *domyślną* przestrzeń dewelopera.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Polecenie `use-dev-spaces` instaluje również interfejsu wiersza polecenia azure dev spaces.

Aby usunąć usługi Azure Dev Spaces z `azds remove` klastra usługi AKS, użyj tego polecenia. Przykład:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Powyższe polecenie usuwa usługę Azure Dev Spaces z klastra *MyAKS* w *myresourcegroup*. Wszystkie obszary nazw utworzone za pomocą usługi Azure Dev Spaces pozostaną wraz z ich obciążeniami, ale nowe obciążenia w tych obszarach nazw nie będą instrumentowane za pomocą usługi Azure Dev Spaces. Ponadto po ponownym uruchomieniu istniejących zasobników instrumentowanych za pomocą usługi Azure Dev Spaces mogą wystąpić błędy. Te zasobniki muszą zostać ponownie rozmieszczone bez narzędzi azure dev spaces. Aby w pełni usunąć usługi Azure Dev Spaces z klastra, usuń wszystkie zasobniki we wszystkich obszarach nazw, w których włączono usługi Azure Dev Spaces.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Włączanie lub usuwanie usług Azure Dev Spaces przy użyciu witryny Azure portal

Aby można było włączyć miejsca deweloperów przy użyciu witryny Azure portal, należy:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto][az-portal-create-account].
* [Klaster AKS][create-aks-portal] w [obsługiwanym regionie][supported-regions].

Aby włączyć usługi Azure Dev Spaces przy użyciu witryny Azure portal:
1. Zaloguj się do [Portalu Azure][az-portal].
1. Przejdź do klastra usługi AKS.
1. Wybierz pozycję menu *Miejsca dewelopera.*
1. Zmień *włącz miejsca dewelopera* na *Tak* i kliknij przycisk *Zapisz*.

![Włączanie przestrzeni deweloperskich w witrynie Azure portal](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Włączenie usługi Azure Dev Spaces przy użyciu witryny Azure portal **nie powoduje** zainstalowania żadnych narzędzi po stronie klienta dla usługi Azure Dev Spaces.

Aby usunąć usługi Azure Dev Spaces z klastra usługi AKS, zmień *opcję Włącz miejsca dewelopera* na *Nie* i kliknij przycisk *Zapisz*. Wszystkie obszary nazw utworzone za pomocą usługi Azure Dev Spaces pozostaną wraz z ich obciążeniami, ale nowe obciążenia w tych obszarach nazw nie będą instrumentowane za pomocą usługi Azure Dev Spaces. Ponadto po ponownym uruchomieniu istniejących zasobników instrumentowanych za pomocą usługi Azure Dev Spaces mogą wystąpić błędy. Te zasobniki muszą zostać ponownie rozmieszczone bez narzędzi azure dev spaces. Aby w pełni usunąć usługi Azure Dev Spaces z klastra, usuń wszystkie zasobniki we wszystkich obszarach nazw, w których włączono usługi Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Instalowanie narzędzi po stronie klienta

Narzędzia po stronie klienta usługi Azure Dev Spaces umożliwiają interakcję z przestrzeniami deweloperskimi w klastrze AKS z komputera lokalnego. Istnieje kilka sposobów instalowania narzędzi po stronie klienta:

* W [programie Visual Studio Code][vscode]należy zainstalować rozszerzenie Azure Dev [Spaces][vscode-extension].
* W [programie Visual Studio 2019][visual-studio]zainstaluj obciążenie programu Azure Development.
* W programie Visual Studio 2017 zainstaluj obciążenie web development i [narzędzia programu Visual Studio dla aplikacji Kubernetes][visual-studio-k8s-tools].
* Pobierz i zainstaluj [system Windows][cli-win], [Mac][cli-mac]lub [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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
