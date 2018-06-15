---
title: Skalowanie klastra usługi Kubernetes Azure (AKS)
description: Skalowanie klastra usługi Kubernetes Azure (AKS).
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 577fff2e659759647ffc7e96158ebcbe5a88ab25
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934698"
---
# <a name="scale-an-azure-kubernetes-service-aks-cluster"></a>Skalowanie klastra usługi Kubernetes Azure (AKS)

Skalowanie klastra usługi AKS do innej liczby węzłów jest łatwe.  Wybierz żądaną liczbę węzłów i uruchom polecenie `az aks scale`.  Podczas skalowania, węzły będą dokładnie [cordoned i opróżnione] [ kubernetes-drain] aby zminimalizować zakłócenia dla aplikacji.  Podczas skalowania w górę polecenie `az` czeka, aż węzły zostaną oznaczone jako `Ready` przez klaster Kubernetes.

## <a name="scale-the-cluster-nodes"></a>Skalowanie węzłów klastra

Użyj polecenia `az aks scale` w celu skalowania węzłów klastra. Poniższy przykład skaluje klastra o nazwie *myAKSCluster* do jednego węzła.

```azurecli-interactive
az aks scale --name myAKSCluster --resource-group myResourceGroup --node-count 1
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z samouczków usługi AKS.

> [!div class="nextstepaction"]
> [Samouczek usługi AKS][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md