---
title: Tworzenie modułu równoważenia obciążenia wewnętrznego Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzenie i używanie wewnętrznego modułu równoważenia obciążenia, aby udostępnić swoje usługi z usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001399"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Użyj wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Kubernetes Service (AKS)

Wewnętrzne Równoważenie obciążenia sprawia, że usługa Kubernetes dostępne dla aplikacji działających w tej samej sieci wirtualnej jako klaster Kubernetes. Ten artykuł pokazuje, jak tworzenie i używanie wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Kubernetes Service (AKS). Równoważenie obciążenia Azure jest dostępna w dwóch jednostkach SKU: podstawowa i standardowa. AKS korzysta z podstawowej jednostki SKU.

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Wewnętrzny moduł równoważenia obciążenia, utworzyć manifestu usługi z typem usługi *modułu równoważenia obciążenia* i *azure obciążenia równoważenia — wewnętrzny* adnotacji, jak pokazano w poniższym przykładzie:

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

Po wdrożeniu przy użyciu `kubetctl apply`, moduł równoważenia obciążenia platformy Azure zostanie utworzony i udostępniane na tej samej sieci wirtualnej jako klaster AKS.

![Obraz przedstawiający AKS wewnętrznego modułu równoważenia obciążenia](media/internal-lb/internal-lb.png)

Wyświetlając szczegółowe informacje o usłudze adres IP w *EXTERNAL-IP* kolumna jest adres IP wewnętrznego modułu równoważenia obciążenia, jak pokazano w poniższym przykładzie:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Określ adres IP

Jeśli chcesz użyć określonego adresu IP przy użyciu wewnętrznego modułu równoważenia obciążenia, dodać *loadBalancerIP* właściwość Specyfikacja modułu równoważenia obciążenia. Określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i już nie można przypisać do zasobu.

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

Po wyświetleniu szczegóły usługi na adres IP *EXTERNAL-IP* odzwierciedla określony adres IP:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Użyj sieci prywatnej

Podczas tworzenia klastra usługi AKS, można określić ustawienia zaawansowane sieci. Takie podejście umożliwia wdrażanie klastra w istniejącej sieci wirtualnej platformy Azure i podsieci. Jest jeden scenariusz wdrażania klastra usługi AKS w taki sposób, w sieci prywatnej, która jest podłączony do sieci w środowisku lokalnym i uruchamiania usługi jest dostępna tylko wewnętrznie. Aby uzyskać więcej informacji, zobacz [konfiguracji zaawansowanej sieci w usłudze AKS][advanced-networking].

Brak zmian do poprzednie kroki są wymagane do wdrożenia wewnętrznego modułu równoważenia obciążenia w klastrze usługi AKS, który korzysta z sieci prywatnej. Moduł równoważenia obciążenia jest tworzony w tej samej grupie zasobów klastra usługi AKS, ale nawiązanie połączenia z prywatną sieć wirtualną i podsieć, jak pokazano w poniższym przykładzie:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie jednostka usługi dla klastra usługi AKS *Współautor sieci* roli do grupy zasobów, w których są wdrażane zasoby sieci wirtualnej platformy Azure. Wyświetl jednostki usługi przy użyciu [az aks show][az-aks-show], takich jak `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Aby utworzyć przypisanie roli, należy użyć [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia.

## <a name="specify-a-different-subnet"></a>Wybierz inną podsieć

Aby określić podsieć dla modułu równoważenia obciążenia, dodać *azure obciążenia równoważenia podsieci wewnętrznej.-* adnotacji do usługi. Określonej podsieci musi być w tej samej sieci wirtualnej jako klastra usługi AKS. Po wdrożeniu usługi równoważenia obciążenia *EXTERNAL-IP* adres jest częścią określonej podsieci.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>Usuń moduł równoważenia obciążenia

Po usunięciu wszystkich usług korzystających z wewnętrznego modułu równoważenia obciążenia, load balancer są także usuwane.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Kubernetes na [Kubernetes usługi dokumentacji][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create