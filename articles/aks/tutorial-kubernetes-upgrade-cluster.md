---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — aktualizowanie klastra
description: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — aktualizowanie klastra
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8340ca9d95e7600e2500755de252e7f6c6de9343
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-container-service-aks"></a>Samouczek: uaktualnianie rozwiązania Kubernetes w usłudze Azure Container Service (AKS)

Klaster usługi Azure Container Service (AKS) można uaktualnić za pomocą wiersza polecenia platformy Azure. Podczas procesu uaktualniania węzły rozwiązania Kubernetes są dokładnie [odizolowywane i opróżniane][kubernetes-drain], aby zminimalizować zakłócenia dla działających aplikacji.

W tym samouczku (część ósma z ośmiu) uaktualniany jest klaster Kubernetes. Zadania do wykonania to na przykład:

> [!div class="checklist"]
> * Identyfikowanie bieżącej i dostępnych wersji rozwiązania Kubernetes
> * Uaktualnianie węzłów rozwiązania Kubernetes
> * Weryfikowanie pomyślnego uaktualnienia

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes.

Jeśli te kroki nie zostały wykonane, a chcesz skorzystać z tej części samouczka, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Uzyskiwanie wersji klastrów

Przed uaktualnieniem klastra użyj polecenia `az aks get-upgrades`, aby sprawdzić, która wersja rozwiązania Kubernetes jest dostępna do uaktualnienia.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

W tym przykładzie bieżąca wersja węzła to `1.7.9`, a dostępne wersje uaktualnień są wyświetlane w kolumnie z uaktualnieniami.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Uaktualnianie klastra

Użyj polecenia `az aks upgrade` w celu uaktualnienia węzłów klastra. W poniższym przykładzie klaster zostaje zaktualizowany do wersji `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Dane wyjściowe:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Weryfikowanie uaktualnienia

Potwierdź, że uaktualnienie powiodło się, używając polecenia `az aks show`.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Dane wyjściowe:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
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