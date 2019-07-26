---
title: Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak uaktualnić klaster usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2ed58846b9e7816092f0fc0787204921071d75e9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498551"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)

W ramach cyklu życia klastra AKS często konieczne jest uaktualnienie do najnowszej wersji programu Kubernetes. Ważne jest, aby zastosować najnowsze wersje zabezpieczeń Kubernetes lub uaktualnić je w celu uzyskania najnowszych funkcji. W tym artykule opisano sposób uaktualniania składników głównych lub pojedynczej, domyślnej puli węzłów w klastrze AKS.

W przypadku klastrów AKS, które korzystają z wielu pul węzłów lub węzłów systemu Windows Server (obecnie w wersji zapoznawczej w AKS), zobacz [uaktualnianie puli węzłów w AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Przed rozpoczęciem

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

> [!WARNING]
> Uaktualnienie klastra AKS wyzwala Cordon i opróżnia węzły. W przypadku braku dostępnego limitu przydziału obliczeń uaktualnienie może zakończyć się niepowodzeniem.  Aby uzyskać więcej informacji, zobacz [zwiększenie limitów przydziału](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Sprawdź dostępność dostępnych uaktualnień klastrów AKS

Aby sprawdzić, które wersje Kubernetes są dostępne dla klastra, użyj polecenia [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Poniższy przykład sprawdza dostępność dostępnych uaktualnień do klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie moja resourceName:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> W przypadku uaktualniania klastra AKS wersje pomocnicze Kubernetes nie mogą być pomijane. Na przykład uaktualnienia między *1.11. x* -> *1.12. x* lub *1.12. x* -> *1.13. x* są dozwolone, jednak *1.11. x* -> *1.13. x* nie jest.
>
> Aby przeprowadzić uaktualnienie, w programie *1.11. x* -> *1.13. x*najpierw Uaktualnij program z wersji *1.11. x* -> *1.12. x*, a następnie Uaktualnij z wersji *1.12. x* -> *1.13. x*.

Następujące przykładowe dane wyjściowe pokazują, że klaster można uaktualnić do wersji *1.12.7* lub *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Mając listę dostępnych wersji klastra AKS, użyj polecenia [AZ AKS upgrade][az-aks-upgrade] , aby przeprowadzić uaktualnienie. W trakcie procesu uaktualniania program AKS dodaje nowy węzeł do klastra, na którym działa określona wersja Kubernetes, a następnie uważnie [Cordon i opróżnia][kubernetes-drain] jeden ze starych węzłów w celu zminimalizowania przerw w działaniu aplikacji. Gdy nowy węzeł zostanie potwierdzony jako uruchomiony program ApplicationManager, stary węzeł zostanie usunięty. Ten proces jest powtarzany do momentu uaktualnienia wszystkich węzłów w klastrze.

Poniższy przykład uaktualnia klaster do wersji *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

Uaktualnienie klastra trwa kilka minut, w zależności od liczby posiadanych węzłów.

Aby upewnić się, że uaktualnienie zakończyło się pomyślnie, użyj polecenia [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazują, że klaster działa teraz *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak uaktualnić istniejący klaster AKS. Aby dowiedzieć się więcej o wdrażaniu klastrów AKS i zarządzaniu nimi, zobacz zestaw samouczków.

> [!div class="nextstepaction"]
> [Samouczki AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
