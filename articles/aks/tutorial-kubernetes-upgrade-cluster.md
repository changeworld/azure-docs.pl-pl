---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — uaktualnianie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) dowiesz się, jak uaktualnić istniejący klaster usługi AKS do najnowszej dostępnej wersji rozwiązania Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "41919951"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Samouczek: uaktualnianie rozwiązania Kubernetes w usłudze Azure Kubernetes Service (AKS)

W ramach cyklu życia aplikacji i klastra możesz chcieć wykonać uaktualnienie do najnowszej dostępnej wersji rozwiązania Kubernetes i skorzystać z nowych funkcji. Klaster usługi Azure Kubernetes Service (AKS) można uaktualnić za pomocą interfejsu wiersza polecenia platformy Azure. Aby zminimalizować przestój uruchomionych aplikacji, węzły Kubernetes są dokładnie [odizolowywane i opróżniane][kubernetes-drain] w ramach procesu uaktualniania.

W tym samouczku (część siódma z siedmiu) jest uaktualniany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes. Jeśli te kroki nie zostały wykonane, a chcesz skorzystać z tej części samouczka, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.44 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Pobieranie dostępnych wersji klastrów

Przed uaktualnieniem klastra użyj polecenia [az aks get-upgrades][], aby sprawdzić, która wersja rozwiązania Kubernetes jest dostępna na potrzeby uaktualnienia:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

W poniższym przykładzie bieżąca wersja to *1.9.6*, a dostępne wersje są wyświetlone w kolumnie *Uaktualnienia*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Uaktualnianie klastra

Użyj polecenia [az aks upgrade][], aby uaktualnić klaster usługi AKS. W poniższym przykładzie klaster jest uaktualniany do rozwiązania Kubernetes w wersji *1.10.6*.

> [!NOTE]
> Jednocześnie można uaktualnić tylko jedną wersję pomocniczą. Na przykład można wykonać uaktualnienie z wersji *1.9.6* do *1.10.3*, ale wykonanie uaktualnienia bezpośrednio z wersji *1.9.6* do wersji *1.11.x* nie jest możliwe. Aby wykonać uaktualnienie z wersji *1.9.6* do wersji *1.11.x*, najpierw wykonaj uaktualnienie z wersji *1.9.6* do wersji *1.10.3*, a następnie wykonaj kolejne uaktualnienie z wersji *1.10.3* do wersji *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

Poniższe skrócone przykładowe dane wyjściowe pokazują, że parametr *kubernetesVersion* ma teraz wartość *1.10.6*:

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
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Weryfikowanie uaktualnienia

Potwierdź, że uaktualnienie powiodło się, używając polecenia [az aks show][] w następujący sposób:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Następujące przykładowe dane wyjściowe pokazują, że w klastrze usługi AKS uruchomiona jest *wersja 1.10.6 rozwiązania Kubernetes*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku uaktualniane jest rozwiązanie Kubernetes w klastrze AKS. W tym samouczku omówiono:

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
[azure-cli-install]: /cli/azure/install-azure-cli