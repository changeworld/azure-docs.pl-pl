---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66692779"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często konieczne uaktualnienie do najnowszej wersji platformy Kubernetes. Należy zastosować najnowszych wersji platformy Kubernetes w zabezpieczeń lub Uaktualnij do wersji uzyskać najnowsze funkcje. W tym artykule przedstawiono sposób uaktualniania wzorca składników lub pojedynczej domyślnej puli węzeł w klastrze AKS.

Dla usługi AKS klastrów korzystających z wielu pule węzłów lub węzłów systemu Windows Server, (zarówno jak obecnie w wersji zapoznawczej w usłudze AKS), zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdź, czy są dostępne Uaktualnianie klastra AKS

Aby sprawdzić, które wersje rozwiązania Kubernetes są dostępne dla klastra, użyj [az aks get uaktualnienia] [ az-aks-get-upgrades] polecenia. Poniższy przykład sprawdza dostępne aktualizacje do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Podczas uaktualniania klastra usługi AKS nie pominięto pomocniczej wersji rozwiązania Kubernetes. Na przykład uaktualnienie między *1.11.x* -> *1.12.x* lub *1.12.x* -> *1.13.x* mogą jednak *1.11.x* -> *1.13.x* nie jest.
>
> Aby przeprowadzić uaktualnienie, z *1.11.x* -> *1.13.x*najpierw uaktualnienie *1.11.x* -> *1.12.x*, następnie uaktualnić z *1.12.x* -> *1.13.x*.

Następujące przykładowe dane wyjściowe pokazuje, że klaster można uaktualnić do wersji *1.12.7* lub *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Z listy dostępnych wersji dla klastra usługi AKS, użyj [uaktualnienia az aks] [ az-aks-upgrade] polecenie, aby uaktualnić. Podczas procesu uaktualniania AKS dodaje nowy węzeł do klastra który uruchamia określonej wersji platformy Kubernetes, następnie dokładnie [cordon i opróżniania] [ kubernetes-drain] jednego z węzłów stary, aby zminimalizować zakłócenia dla uruchamiania aplikacje. Gdy nowy węzeł został potwierdzony jako uruchomiona zasobniki aplikacji, węzła starego zostaną usunięte. Ten proces jest powtarzany, aż wszystkie węzły w klastrze zostały uaktualnione.

Poniższy przykład uaktualniania klastra do wersji *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Trwa kilka minut, aby uaktualnić klaster, w zależności od liczby węzłów masz.

Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, należy użyć [az aks show] [ az-aks-show] polecenia:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazuje, że teraz uruchamiane w klastrze *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano sposób uaktualniania istniejącego klastra usługi AKS. Aby dowiedzieć się więcej na temat wdrażania i zarządzania klastrami usługi AKS, zobacz zestawie samouczków.

> [!div class="nextstepaction"]
> [Samouczki usługi AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
