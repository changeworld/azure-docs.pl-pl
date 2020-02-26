---
title: Skalowanie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skalować liczbę węzłów w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2071ba93e826fabc4778426e2b4404b7475d1bd2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594401"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie liczby węzłów w klastrze usługi Azure Kubernetes Service (AKS)

W przypadku zmiany zasobów aplikacji można ręcznie skalować klaster AKS, aby uruchamiał inną liczbę węzłów. Po skalowaniu w dół węzły są dokładnie [odizolowywane i opróżniane][kubernetes-drain] , aby zminimalizować zakłócenia w działaniu aplikacji. Podczas skalowania w górę AKS czeka, aż węzły zostaną oznaczone jako `Ready` przez klaster Kubernetes przed zaplanowaniem zasobników.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Najpierw pobierz *nazwę* puli węzłów za pomocą polecenia [AZ AKS show][az-aks-show] . Poniższy przykład pobiera nazwę puli węzłów dla klastra o nazwie *myAKSCluster* w *grupie zasobów zasobu zasobu:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Następujące przykładowe dane wyjściowe pokazują, że *Nazwa* jest *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

Aby skalować węzły klastra, użyj polecenia [AZ AKS Scale][az-aks-scale] . Poniższy przykład skaluje klaster o nazwie *myAKSCluster* do jednego węzła. Podaj własną *nazwę nodepool* z poprzedniego polecenia, na przykład *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Następujące przykładowe dane wyjściowe pokazują, że klaster został pomyślnie przeskalowany do jednego węzła, jak pokazano w sekcji *agentPoolProfiles* :

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

W tym artykule opisano ręczne skalowanie klastra AKS w celu zwiększenia lub zmniejszenia liczby węzłów. Możesz również użyć automatycznego [skalowania klastra][cluster-autoscaler] , aby automatycznie skalować klaster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
