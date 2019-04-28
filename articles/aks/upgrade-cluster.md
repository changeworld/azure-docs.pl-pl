---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028444"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często konieczne uaktualnienie do najnowszej wersji platformy Kubernetes. Należy zastosować najnowszych wersji platformy Kubernetes w zabezpieczeń lub Uaktualnij do wersji uzyskać najnowsze funkcje. W tym artykule przedstawiono sposób uaktualniania istniejącego klastra usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.56 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdź, czy są dostępne Uaktualnianie klastra AKS

Aby sprawdzić, które wersje rozwiązania Kubernetes są dostępne dla klastra, użyj [az aks get uaktualnienia] [ az-aks-get-upgrades] polecenia. Poniższy przykład sprawdza dostępne aktualizacje do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Podczas uaktualniania klastra usługi AKS nie pominięto pomocniczej wersji rozwiązania Kubernetes. Na przykład uaktualnienie między *1.10.x* -> *1.11.x* lub *1.11.x* -> *1.12.x* mogą jednak *1.10.x* -> *1.12.x* nie jest.
>
> Aby przeprowadzić uaktualnienie, z *1.10.x* -> *1.12.x*najpierw uaktualnienie *1.10.x* -> *1.11.x*, następnie uaktualnić z *1.11.x* -> *1.12.x*.

Następujące przykładowe dane wyjściowe pokazuje, że klaster można uaktualnić do wersji *1.11.5* lub *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Z listy dostępnych wersji dla klastra usługi AKS, użyj [uaktualnienia az aks] [ az-aks-upgrade] polecenie, aby uaktualnić. Podczas procesu uaktualniania AKS dodaje nowy węzeł do klastra, następnie dokładnie [cordon i opróżniania] [ kubernetes-drain] jeden węzeł w czasie, aby zminimalizować zakłócenia dla działających aplikacji. Poniższy przykład uaktualniania klastra do wersji *1.11.6*:

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

Trwa kilka minut, aby uaktualnić klaster, w zależności od liczby węzłów masz.

Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, należy użyć [az aks show] [ az-aks-show] polecenia:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazuje, że teraz uruchamiane w klastrze *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano sposób uaktualniania istniejącego klastra usługi AKS. Aby dowiedzieć się więcej na temat wdrażania i zarządzania klastrami usługi AKS, zobacz zestawie samouczków.

> [!div class="nextstepaction"]
> [Samouczki usługi AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show