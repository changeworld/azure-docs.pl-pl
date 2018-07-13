---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — aktualizowanie klastra
description: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — aktualizowanie klastra
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131647"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Samouczek: uaktualnianie rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

Klaster usługi Azure Kubernetes Service (AKS) można uaktualnić za pomocą interfejsu wiersza polecenia platformy Azure. Aby zminimalizować przestój uruchomionych aplikacji, węzły Kubernetes są dokładnie [odizolowywane i opróżniane][kubernetes-drain] w ramach procesu uaktualniania.

W tym samouczku (część siódma z siedmiu) jest uaktualniany klaster Kubernetes. Zadania do wykonania to na przykład:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes.

Jeśli te kroki nie zostały wykonane, a chcesz skorzystać z tej części samouczka, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Uzyskiwanie wersji klastrów

Przed uaktualnieniem klastra użyj polecenia [az aks get-upgrades][], aby sprawdzić, która wersja rozwiązania Kubernetes jest dostępna na potrzeby uaktualnienia.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

W poniższym przykładzie bieżąca wersja węzła to *1.9.6*, a dostępne wersje są wyświetlone w kolumnie *Uaktualnienia*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Uaktualnianie klastra

Użyj polecenia [az aks upgrade][], aby uaktualnić węzły klastra. W poniższym przykładzie klaster jest aktualizowany do wersji *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

Poniższe skrócone przykładowe dane wyjściowe pokazują, że parametr *kubernetesVersion* ma teraz wartość *1.10.3*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Weryfikowanie uaktualnienia

Potwierdź, że uaktualnienie powiodło się, używając polecenia [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Dane wyjściowe:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku uaktualniane jest rozwiązanie Kubernetes w klastrze AKS. Wykonano następujące zadania:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

Skorzystaj z tego linku, aby dowiedzieć się więcej o usłudze AKS.

> [!div class="nextstepaction"]
> [Omówienie usługi AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade