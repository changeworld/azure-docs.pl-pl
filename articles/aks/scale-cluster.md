---
title: Skalowanie klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skalować liczbę węzłów w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 9cc06df5d2a66ede18af52c13201c731c12e2049
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614506"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie liczby węzłów w klastrze usługi Azure Kubernetes Service (AKS)

Jeśli zasób sprostania wymaganiom Twojej aplikacji, zmienić można ręcznie skalować klastra usługi AKS, aby uruchomić innej liczby węzłów. Skalowanie w węzły są dokładnie [odizolowywane i opróżniane][kubernetes-drain] aby zminimalizować zakłócenia dla działających aplikacji. Skalowanie w górę AKS czeka, aż węzły zostaną oznaczone jako `Ready` przez klaster Kubernetes, zanim zasobników zaplanowano na nich.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Najpierw pobierz *nazwa* użycia puli węzeł [az aks show][az-aks-show] polecenia. Poniższy przykład pobiera nazwę puli węzeł klastra o nazwie *myAKSCluster* w *myResourceGroup* grupy zasobów:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Następujące przykładowe dane wyjściowe pokazuje, że *nazwa* jest *nodepool1*:

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

Użyj [az aks skalowania][az-aks-scale] polecenia do skalowania węzłów klastra. W poniższym przykładzie skala jest klaster o nazwie *myAKSCluster* do jednego węzła. Podaj własny *nodepool — nazwa* z poprzedniego polecenia, takie jak *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Następujące przykładowe dane wyjściowe pokazuje klastra ma została pomyślnie przeskalowana do jednego węzła, jak pokazano na *agentPoolProfiles* sekcji:

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

W tym artykule można ręcznie skalować klastra usługi AKS w taki sposób, aby zwiększyć lub zmniejszyć liczbę węzłów. Można również użyć [klastra skalowania automatycznego][cluster-autoscaler] (obecnie dostępna w wersji zapoznawczej w usłudze AKS) do automatycznego dowolnego skalowania klastra.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
