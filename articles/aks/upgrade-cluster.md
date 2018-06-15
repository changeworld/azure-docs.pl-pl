---
title: Uaktualnianie klastra usługi Kubernetes Azure (AKS)
description: Uaktualnianie klastra usługi Kubernetes Azure (AKS)
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: f6b8e964f4277150e104cd6d77db092aaa8553b4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933278"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uaktualnianie klastra usługi Kubernetes Azure (AKS)

Usługi Kubernetes Azure (AKS) ułatwia wykonywanie typowych zadań zarządzania tym uaktualnianie Kubernetes klastrów.

## <a name="upgrade-an-aks-cluster"></a>Uaktualnianie klastra AKS

Przed uaktualnieniem klastra użyj polecenia `az aks get-upgrades`, aby sprawdzić, która wersja rozwiązania Kubernetes jest dostępna do uaktualnienia.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Dane wyjściowe:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Mamy trzy wersje dostępne do uaktualnienia: 1.9.1, 1.9.2 i pytanie 1.9.6. Możemy użyć polecenia `az aks upgrade` w celu przeprowadzenia uaktualnienia do najnowszej dostępnej wersji.  Podczas procesu uaktualniania węzły są dokładnie [cordoned i opróżnione] [ kubernetes-drain] aby zminimalizować zakłócenia dla aplikacji.  Przed zainicjowaniem uaktualniania klastra upewnij się, że masz na tyle dużo dodatkowej wydajności obliczeniowej, aby obsłużyć obciążenie podczas dodawania i usuwania węzłów klastra.

> [!NOTE]
> Podczas uaktualniania klastra AKS, nie pominięte Kubernetes wersje pomocnicze. Na przykład uaktualnia między 1.7.x > 1.8.x lub 1.8.x > 1.9.x są dozwolone, jednak 1.7 nie jest > 1.9.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
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
    "kubernetesVersion": "1.9.6",
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

Potwierdź, że uaktualnienie powiodło się, używając polecenia `az aks show`.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Dane wyjściowe:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z samouczków usługi AKS.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md