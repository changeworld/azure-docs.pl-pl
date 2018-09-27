---
title: Używanie statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć i używać statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: a7e592e9911c596f2cf74724e73c469ed616e5f0
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391349"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statyczny publiczny adres IP za pomocą modułu równoważenia obciążenia Azure Kubernetes Service (AKS)

Domyślnie publiczny adres IP przypisany do zasobu modułu równoważenia obciążenia, utworzone przez klaster AKS jest tylko prawidłowy dla cyklem życia tego zasobu. Jeśli usuniesz usługi Kubernetes, moduł równoważenia obciążenia skojarzone i adres IP również zostaną usunięte. Jeśli chcesz przypisać określony adres IP lub zachować adres IP w ponownie wdrożonym usług Kubernetes, można tworzyć i używać statyczny publiczny adres IP.

W tym artykule dowiesz się, jak utworzyć statyczny publiczny adres IP i przypisać ją do usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.46 lub później zainstalowane i skonfigurowane. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz zainstalować lub uaktualnić, zobacz [instalacji wiersza polecenia platformy Azure] [azure-install-cli].

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

Kiedy tworzysz statyczny publiczny adres IP do użycia za pomocą usługi AKS, należy utworzyć zasób adresu IP w **węzła** grupy zasobów. Pobierz nazwę grupy zasobów przy użyciu [az aks show] [ az-aks-show] polecenie i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera węzeł grupy zasobów dla nazwy klastra AKS *myAKSCluster* w polu Nazwa grupy zasobów *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz statyczny publiczny adres IP z [tworzenie publicznego adresu ip sieci az] [ az-network-public-ip-create] polecenia. Określ nazwę grupy zasobów węzła uzyskanego w poprzednim poleceniu, a następnie nazwę dla adresu IP adresów zasobów, takich jak *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Adres IP jest wyświetlany, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
````

Możesz później uzyskać publiczny adres IP, korzystając [az sieci public-ip list] [ az-network-public-ip-list] polecenia. Określ nazwę grupy zasobów, węzeł, a następnie wyszukać *ipAddress* jak pokazano w poniższym przykładzie:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę ze statycznym adresem IP, należy dodać `loadBalancerIP` właściwości i wartość statyczny adres IP adresów w manifeście YAML, jak pokazano w poniższym przykładzie:

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

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP jest zdefiniowany w *loadBalancerIP* właściwości manifestu usługi Kubernetes, nie istnieje lub nie został utworzony w węźle grupy zasobów, tworzenia usługi równoważenia obciążenia nie powiedzie się. Aby rozwiązać problemy, przejrzyj zdarzenia tworzenia usługi przy użyciu [opisują kubectl] [ kubectl-describe] polecenia. Podaj nazwę usługi, jak określono w manifeście YAML, jak pokazano w poniższym przykładzie:

```console
kubectl describe service azure-vote-front
```

Zostaną wyświetlone informacje o zasobie usługi Kubernetes. *Zdarzenia* na końcu następujące przykładowe dane wyjściowe wskazują, że *użytkownika podany adres IP nie został znaleziony*. W tych scenariuszach Sprawdź, czy utworzono statyczny publiczny adres IP, w węźle grupy zasobów i czy adres IP podany w manifeście usługi Kubernetes jest poprawna.

```
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

## <a name="next-steps"></a>Kolejne kroki

Dla dodatkowej kontroli nad ruchem sieciowym aplikacji, warto zamiast tego [utworzyć kontroler danych przychodzących][aks-ingress-basic]. Możesz również [utworzyć kontroler danych przychodzących z statyczny publiczny adres IP][aks-static-ingress].

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
