---
title: Samouczek dotyczący rozwiązania Kubernetes na platformie Azure — skalowanie aplikacji
description: Samouczek dotyczący usługi AKS — skalowanie aplikacji
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61654ae972965800909544554cc93dae511e1ff1
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480276"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Samouczek: skalowanie aplikacji w usłudze Azure Kubernetes Service (AKS)

Jeśli wykonujesz kolejno zadania z samouczków, masz już działający klaster Kubernetes w usłudze AKS z wdrożoną aplikacją do głosowania platformy Azure.

Ta część samouczka, piąta z siedmiu, obejmuje skalowanie w poziomie zasobników w tej aplikacji oraz skalowanie automatyczne. Dowiesz się również, jak przez skalowanie liczby węzłów maszyny wirtualnej platformy Azure zmieniać możliwości hostowania obciążeń w klastrze. Wykonasz następujące zadania:

> [!div class="checklist"]
> * Skalowanie węzłów platformy Azure dla rozwiązania Kubernetes
> * Ręczne skalowanie zasobników Kubernetes
> * Konfigurowanie skalowania automatycznego zasobników obsługujących fronton aplikacji

W kolejnych samouczkach aplikacja Azure Vote jest aktualizowana do nowej wersji.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazu kontenera, obraz został przekazany do usługi Azure Container Registry i utworzono klaster Kubernetes. Następnie uruchomiono aplikację w klastrze usługi Kubernetes.

Jeśli te kroki nie zostały wykonane, a chcesz skorzystać z tej części samouczka, wróć do części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

## <a name="manually-scale-pods"></a>Ręczne skalowanie zasobników

Poprzednio wdrożono fronton aplikacji do głosowania platformy Azure oraz wystąpienie pamięci podręcznej Redis — każdy z tych elementów został wdrożony z jedną repliką. Aby to zweryfikować, uruchom polecenie [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Dane wyjściowe:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ręcznie zmień liczbę zasobników we wdrożeniu aplikacji `azure-vote-front`, używając polecenia [kubectl scale][kubectl-scale]. W tym przykładzie liczba ta zostanie zwiększona do 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Uruchom polecenie [kubectl get pods][kubectl-get], aby zweryfikować utworzenie zasobników przez rozwiązanie Kubernetes. Po upływie około minuty dodatkowe zasobniki zaczną działać:

```azurecli
kubectl get pods
```

Dane wyjściowe:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatyczne skalowanie zasobników

Rozwiązanie Kubernetes obsługuje [automatyczne skalowanie zasobników w poziomie][kubernetes-hpa], umożliwiające dostosowywanie liczby zasobników we wdrożeniu do użycia procesora lub innych wybranych metryk. Program [Metrics Server][metrics-server] służy do zapewniania platformie Kubernetes informacji o wykorzystaniu zasobów. Aby zainstalować program Metrics Server, sklonuj repozytorium GitHub `metrics-server` i zainstaluj przykładowe definicje zasobów. Aby wyświetlić zawartość tych definicji YAML, zobacz [program Metrics Server dla platformy Kuberenetes w wersji 1.8 lub nowszej][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Aby móc korzystać ze skalowania automatycznego, należy zdefiniować wymagania i limity użycia procesora CPU dla zasobników. We wdrożeniu aplikacji `azure-vote-front` kontener frontonu wymaga 0,25 CPU, a limit wynosi 0,5 CPU. Ustawienia są następujące:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

W poniższym przykładzie użyto polecenia [kubectl autoscale][kubectl-autoscale] w celu automatycznego skalowania liczby zasobników we wdrożeniu aplikacji `azure-vote-front`. Jeśli użycie procesora CPU przekroczy 50%, skalowanie automatyczne spowoduje zwiększenie liczby zasobników, maksymalnie do 10.

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Aby wyświetlić stan skalowania automatycznego, uruchom następujące polecenie:

```azurecli
kubectl get hpa
```

Dane wyjściowe:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Po upływie kilku minut, przy minimalnym obciążeniu aplikacji do głosowania platformy Azure, liczba replik zasobników zostanie automatycznie zmniejszona do 3.

## <a name="manually-scale-aks-nodes"></a>Ręczne skalowanie węzłów usługi AKS

Jeśli utworzono klaster Kubernetes przy użyciu poleceń z poprzedniego samouczka, zawiera on jeden węzeł. Jeśli planujesz zwiększenie lub zmniejszenie liczby obciążeń kontenerów w klastrze, możesz ręcznie dostosować liczbę węzłów.

W poniższym przykładzie liczba węzłów w klastrze Kubernetes o nazwie *myAKSCluster* zostanie zwiększona do trzech. Wykonanie tego polecenia może zająć kilka minut.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Dane wyjściowe są podobne do następujących:

```
"agentPoolProfiles": [
  {
    "count": 3,
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
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto różnych funkcji skalowania w klastrze Kubernetes. Wykonano następujące zadania:

> [!div class="checklist"]
> * Ręczne skalowanie zasobników Kubernetes
> * Konfigurowanie skalowania automatycznego zasobników obsługujących fronton aplikacji
> * Skalowanie węzłów platformy Azure dla rozwiązania Kubernetes

Przejdź do następnego samouczka, aby dowiedzieć się, jak zaktualizować aplikację w usłudze Kubernetes.

> [!div class="nextstepaction"]
> [Aktualizowanie aplikacji w rozwiązaniu Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
