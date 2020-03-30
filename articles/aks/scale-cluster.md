---
title: Skalowanie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skalować liczbę węzłów w klastrze usługi Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368421"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie liczby węzłów w klastrze usługi Azure Kubernetes Service 

Jeśli zmienią się potrzeby zasobów aplikacji, można ręcznie skalować klaster usługi AKS w celu uruchomienia innej liczby węzłów. Podczas skalowania w dół węzły są starannie [kordonowane i opróżniane,][kubernetes-drain] aby zminimalizować zakłócenia w uruchamianiu aplikacji. Podczas skalowania w górę usługa AKS `Ready` czeka, aż węzły zostaną oznaczone przez klaster Kubernetes, zanim zostaną zaplanowane na nich zasobniki.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Najpierw uzyskaj *nazwę* puli węzłów za pomocą polecenia [az aks show.][az-aks-show] W poniższym przykładzie zostanie podana nazwa puli węzłów klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Poniższy przykładowy wynik pokazuje, że *nazwa* jest *nodepool1:*

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Użyj polecenia [skali az aks,][az-aks-scale] aby skalować węzły klastra. Poniższy przykład skaluje klaster o nazwie *myAKSCluster* do pojedynczego węzła. Podaj własną *nazwę --nodepool z* poprzedniego polecenia, na przykład *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Poniższe przykładowe dane wyjściowe pokazują, że klaster pomyślnie przeskalował się do jednego węzła, jak pokazano w sekcji *agentPoolProfiles:*

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule ręcznie skalowane klastra usługi AKS, aby zwiększyć lub zmniejszyć liczbę węzłów. Można również użyć [skalowania automatycznego klastra,][cluster-autoscaler] aby automatycznie skalować klaster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
