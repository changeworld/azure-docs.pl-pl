---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak tworzenie i używanie wewnętrznego modułu równoważenia obciążenia, aby udostępnić swoje usługi z usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: 1b5d18a3dfd1181fd06b58fd58f496457e24b58e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956375"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Użyj wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Kubernetes Service (AKS)

Aby ograniczyć dostęp do aplikacji w usłudze Azure Kubernetes Service (AKS), można tworzyć i używać wewnętrznego modułu równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępny tylko dla aplikacji działających w tej samej sieci wirtualnej jako klaster Kubernetes. Ten artykuł pokazuje, jak tworzenie i używanie wewnętrznego modułu równoważenia obciążenia za pomocą usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Równoważenie obciążenia Azure jest dostępna w dwóch jednostkach SKU - *podstawowe* i *standardowa*. Obecnie obsługuje AKS *podstawowe* jednostki SKU. Jeśli chcesz użyć *standardowa* jednostki SKU, możesz użyć nadrzędnego [aparatu aks][aks-engine]. Aby uzyskać więcej informacji, zobacz [porównania jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Jednostki usługi Klaster AKS wymaga zgody na zarządzanie zasobami sieciowymi, jeśli używasz istniejącej podsieci lub grupy zasobów. Ogólnie rzecz biorąc, Przypisz *Współautor sieci* roli do jednostki usługi dla delegowanego zasobów. Aby uzyskać więcej informacji na temat uprawnień, zobacz [AKS delegowanego dostępu do innych zasobów platformy Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Do tworzenia wewnętrznego modułu równoważenia obciążenia, należy utworzyć manifest usługi o nazwie `internal-lb.yaml` z typem usługi *modułu równoważenia obciążenia* i *azure obciążenia równoważenia — wewnętrzny* adnotacji, jak pokazano w następującym przykład:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wdrażanie przy użyciu modułu równoważenia obciążenia wewnętrznego [kubectl zastosować] kubectl-Zastosuj] i określ nazwę manifeście YAML:

```console
kubectl apply -f internal-lb.yaml
```

Usługa Azure load balancer jest tworzony w węźle grupy zasobów i podłączone do tej samej sieci wirtualnej jako klaster AKS.

Po wyświetleniu szczegóły usługi na adres IP wewnętrznego modułu równoważenia obciążenia jest wyświetlana w *EXTERNAL-IP* kolumny. W tym kontekście *zewnętrznych* w odniesieniu do zewnętrznego interfejsu modułu równoważenia obciążenia nie jest odbiera publiczny, zewnętrzny adres IP. Może potrwać minutę lub dwie dla adresu IP do zmiany z *\<oczekujące\>* do rzeczywistego wewnętrznego adresu IP, jak pokazano w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Określ adres IP

Jeśli chcesz użyć określonego adresu IP przy użyciu wewnętrznego modułu równoważenia obciążenia, dodać *loadBalancerIP* właściwości manifestu YAML modułu równoważenia obciążenia. Określony adres IP musi znajdować się w tej samej podsieci co klaster AKS i już nie można przypisać do zasobu.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Po wdrożeniu i wyświetlić szczegóły usługi, adres IP w *EXTERNAL-IP* kolumny odzwierciedla określonego adresu IP:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Użyj sieci prywatnej

Podczas tworzenia klastra usługi AKS, można określić ustawienia zaawansowane sieci. Takie podejście umożliwia wdrażanie klastra w istniejącej sieci wirtualnej platformy Azure i podsieci. Jest jeden scenariusz wdrażania klastra usługi AKS w taki sposób, w sieci prywatnej, która jest podłączony do sieci w środowisku lokalnym i uruchamiania usługi jest dostępna tylko wewnętrznie. Aby uzyskać więcej informacji, zobacz Konfigurowanie własnych podsieciach sieci wirtualnej za pomocą [wtyczki Kubenet] [ use-kubenet] lub [wtyczki Azure CNI][advanced-networking].

Brak zmian do poprzednie kroki są wymagane do wdrożenia wewnętrznego modułu równoważenia obciążenia w klastrze usługi AKS, który korzysta z sieci prywatnej. Moduł równoważenia obciążenia jest tworzony w tej samej grupie zasobów klastra usługi AKS, ale nawiązanie połączenia z prywatną sieć wirtualną i podsieć, jak pokazano w poniższym przykładzie:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Może być konieczne przyznanie jednostka usługi dla klastra usługi AKS *Współautor sieci* roli do grupy zasobów, w których są wdrażane zasoby sieci wirtualnej platformy Azure. Wyświetl jednostki usługi przy użyciu [az aks show][az-aks-show], takich jak `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Aby utworzyć przypisanie roli, należy użyć [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia.

## <a name="specify-a-different-subnet"></a>Wybierz inną podsieć

Aby określić podsieć dla modułu równoważenia obciążenia, dodać *azure obciążenia równoważenia podsieci wewnętrznej.-* adnotacji do usługi. Określonej podsieci musi być w tej samej sieci wirtualnej jako klastra usługi AKS. Po wdrożeniu usługi równoważenia obciążenia *EXTERNAL-IP* adres jest częścią określonej podsieci.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Usuń moduł równoważenia obciążenia

Po usunięciu wszystkich usług korzystających z wewnętrznego modułu równoważenia obciążenia, load balancer są także usuwane.

Możesz także bezpośrednio usunąć usługa z dowolnym zasobem platformy Kubernetes, takich jak `kubectl delete service internal-app`, które również usuwa podstawowego modułu równoważenia obciążenia platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi Kubernetes na [Kubernetes usługi dokumentacji][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
