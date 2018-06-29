---
title: Tworzenie usługi Kubernetes Azure (AKS) wewnętrznego modułu równoważenia obciążenia
description: Użyj wewnętrznego modułu równoważenia obciążenia z usługi Kubernetes Azure (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097924"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Użyj wewnętrznego modułu równoważenia obciążenia z usługi Kubernetes Azure (AKS)

Równoważenie obciążenia wewnętrznego sprawia, że usługa Kubernetes dostępne do aplikacji działających w tej samej sieci wirtualnej co klaster Kubernetes. Szczegóły tego dokumentu, tworzenia wewnętrznego modułu równoważenia obciążenia z usługi Kubernetes Azure (AKS). Moduł równoważenia obciążenia Azure są dostępne w dwóch jednostki SKU: Basic i Standard. AKS używa podstawowy SKU.

## <a name="create-internal-load-balancer"></a>Utworzyć wewnętrznego modułu równoważenia obciążenia

Aby utworzyć wewnętrznego modułu równoważenia obciążenia, tworzenie manifestu usługi z typem usługi `LoadBalancer` i `azure-load-balancer-internal` adnotacji, jak pokazano w poniższym przykładzie.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Po wdrożeniu usługi równoważenia obciążenia Azure jest tworzony i udostępniane na tej samej sieci wirtualnej co klaster AKS.

![Obraz AKS wewnętrznego modułu równoważenia obciążenia](media/internal-lb/internal-lb.png)

Podczas pobierania usługi szczegółów, adres IP w `EXTERNAL-IP` kolumny jest adres IP wewnętrznego modułu równoważenia obciążenia.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Określ adres IP

Jeśli chcesz użyć określonego adresu IP z wewnętrznego modułu równoważenia obciążenia, Dodaj `loadBalancerIP` właściwości specyfikacji modułu równoważenia obciążenia. Określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i już nie można przypisać do zasobu.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Podczas pobierania szczegółów usługi, adres IP na `EXTERNAL-IP` powinien odzwierciedlać określony adres IP.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Usunąć modułu równoważenia obciążenia

Po usunięciu wszystkich usług przy użyciu usługi równoważenia obciążenia wewnętrznego modułu równoważenia obciążenia, się również zostanie usunięta.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach Kubernetes w [dokumentacji usług Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
