---
title: Użyj statycznego adresu IP usługi równoważenia obciążenia Azure Kubernetes usługi (AKS)
description: Za pomocą statycznego adresu IP usługi równoważenia obciążenia Azure Kubernetes usługi (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 9f6c34bd09d022b2453869c048f5f3cda7580b91
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596665"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Użyj statycznego adresu IP usługi równoważenia obciążenia Azure Kubernetes usługi (AKS)

W niektórych przypadkach, takich jak podczas ładowania usługi Kubernetes Azure (AKS) zostaje odtworzone równoważenia lub Kubernetes usługi z typem usługi równoważenia obciążenia są odtwarzane publicznego adresu IP usługi Kubernetes mogą ulec zmianie. Konfigurowanie statycznego adresu IP dla usług Kubernetes szczegóły tego dokumentu.

## <a name="create-static-ip-address"></a>Tworzenie statycznego adresu IP

Utwórz statycznego publicznego adresu IP dla usługi Kubernetes. Adres IP musi zostać utworzona w AKS **węzła** grupy zasobów. Pobierz nazwę grupy zasobów z [Pokaż zasobów az] [ az-resource-show] polecenia.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Użyj [tworzenie publicznego adresu ip sieci az] [ az-network-public-ip-create] polecenie, aby utworzyć adres IP.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Zanotuj adres IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 W razie potrzeby można pobrać adresu przy użyciu [az sieci ip publicznego listy] [ az-network-public-ip-list] polecenia.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Tworzenie usługi z adresu IP

Po zainicjowano statyczny adres IP, usługi Kubernetes można tworzyć za pomocą `loadBalancerIP` właściwości i wartości statycznego adresu IP.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP nie został utworzony lub został utworzony w grupie zasobów niewłaściwy, tworzenia usługi nie powiedzie się. Aby rozwiązać, zwróć zdarzeń tworzenia usługi z [opisano kubectl] [ kubectl-describe] polecenia.

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-resource-show]: /cli/azure/resource#az-resource-show
