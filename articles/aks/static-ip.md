---
title: Używanie statycznego adresu IP w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć statyczny adres IP i używać go w usłudze równoważenia obciążenia usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614467"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Używanie statycznego publicznego adresu IP z usługą równoważenia obciążenia usługi Azure Kubernetes Service (AKS)

Domyślnie publiczny adres IP przypisany do zasobu modułu równoważenia obciążenia utworzonego przez klaster AKS jest prawidłowy tylko dla cykl życia tego zasobu. Usunięcie usługi Kubernetes spowoduje również usunięcie powiązanego modułu równoważenia obciążenia i adresu IP. Jeśli chcesz przypisać określony adres IP lub zachować adres IP dla ponownie wdrożonych usług Kubernetes, możesz utworzyć statyczny publiczny adres IP i używać go.

W tym artykule opisano sposób tworzenia statycznego publicznego adresu IP i przypisywania go do usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Obecnie obsługiwana jest tylko *podstawowa jednostka SKU IP*. Pracuj w toku, aby zapewnić obsługę standardowej jednostki SKU zasobu *adresu IP* . Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

W przypadku tworzenia statycznego publicznego adresu IP do użycia z AKS należy utworzyć zasób adresu IP w grupie zasobów **węzła** . Jeśli chcesz rozdzielić zasoby, zapoznaj się z sekcją w poniższej sekcji, aby [użyć statycznego adresu IP poza grupą zasobów węzła](#use-a-static-ip-address-outside-of-the-node-resource-group).

Najpierw Pobierz nazwę grupy zasobów węzła za pomocą polecenia [AZ AKS show][az-aks-show] i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla nazwy klastra AKS *myAKSCluster* w grupie zasobów nazwa zasobu:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz statyczny publiczny adres IP za pomocą polecenia [AZ Network Public IP Create][az-network-public-ip-create] . Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu adresu IP, na przykład *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Wyświetlany jest adres IP, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Możesz później uzyskać publiczny adres IP za pomocą polecenia [AZ Network Public-IP list][az-network-public-ip-list] . Określ nazwę grupy zasobów węzła i utworzonego publicznego adresu IP, a następnie zapytaj o *adres* IP, jak pokazano w następującym przykładzie:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę ze statycznym publicznym adresem IP, Dodaj `loadBalancerIP` Właściwość i wartość statycznego publicznego adresu IP do manifestu YAML. Utwórz plik o nazwie `load-balancer-service.yaml` i skopiuj w następującym YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Utwórz usługę i wdrożenie za pomocą `kubectl apply` polecenia.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Użyj statycznego adresu IP poza grupą zasobów węzła

W przypadku Kubernetes 1,10 lub nowszego można użyć statycznego adresu IP, który jest tworzony poza grupą zasobów węzła. Nazwa główna usługi używana przez klaster AKS musi mieć delegowane uprawnienia do innej grupy zasobów, jak pokazano w następującym przykładzie:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Aby użyć adresu IP poza grupą zasobów węzła, należy dodać adnotację do definicji usługi. Poniższy przykład ustawia adnotację z grupą zasobów o nazwiemoja ResourceName. Podaj własną nazwę grupy zasobów:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP zdefiniowany we właściwości *loadBalancerIP* manifestu usługi Kubernetes nie istnieje lub nie został utworzony w grupie zasobów węzła i nie skonfigurowano żadnych dodatkowych delegowania, tworzenie usługi równoważenia obciążenia zakończy się niepowodzeniem. Aby rozwiązać problem, Przejrzyj zdarzenia tworzenia usługi za pomocą polecenia [polecenia kubectl opisywanie][kubectl-describe] . Podaj nazwę usługi określoną w manifeście YAML, jak pokazano w następującym przykładzie:

```console
kubectl describe service azure-load-balancer
```

Zostanie wyświetlone informacje o zasobie usługi Kubernetes. *Zdarzenia* na końcu następujących przykładowych danych wyjściowych wskazują, że *nie znaleziono adresu IP dostarczonego przez użytkownika*. W tych scenariuszach upewnij się, że w grupie zasobów węzła został utworzony statyczny publiczny adres IP i że adres IP określony w manifeście usługi Kubernetes jest poprawny.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Następne kroki

W celu zapewnienia dodatkowej kontroli nad ruchem sieciowym w aplikacjach można [utworzyć kontroler][aks-ingress-basic]transferu danych przychodzących. Można również utworzyć kontroler transferu danych przychodzących [ze statycznym publicznym adresem IP][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
