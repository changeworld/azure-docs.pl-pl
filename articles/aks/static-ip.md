---
title: Używanie statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć i używać statycznego adresu IP z modułem równoważenia obciążenia Azure Kubernetes Service (AKS).
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031652"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statyczny publiczny adres IP za pomocą modułu równoważenia obciążenia Azure Kubernetes Service (AKS)

Domyślnie publiczny adres IP przypisany do zasobu modułu równoważenia obciążenia, utworzone przez klaster AKS jest tylko prawidłowy dla cyklem życia tego zasobu. Jeśli usuniesz usługi Kubernetes, moduł równoważenia obciążenia skojarzone i adres IP również zostaną usunięte. Jeśli chcesz przypisać określony adres IP lub zachować adres IP w ponownie wdrożonym usług Kubernetes, można tworzyć i używać statyczny publiczny adres IP.

W tym artykule dowiesz się, jak utworzyć statyczny publiczny adres IP i przypisać ją do usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Obecnie tylko *podstawowej jednostki SKU IP*jest obsługiwana. Pracy jest w toku do obsługi *IP w warstwie standardowa* zasobów jednostki SKU. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

Po utworzeniu statyczny publiczny adres IP do użycia za pomocą usługi AKS zasób adresu IP, powinny zostać utworzone w **węzła** grupy zasobów. Jeśli chcesz oddzielne zasoby, zobacz następującą sekcję, aby [Użyj statycznego adresu IP spoza grupy zasobów węzła](#use-a-static-ip-address-outside-of-the-node-resource-group).

Najpierw Uzyskaj nazwę grupy zasobów węzła przy użyciu [az aks show] [ az-aks-show] polecenie i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera węzeł grupy zasobów dla nazwy klastra AKS *myAKSCluster* w polu Nazwa grupy zasobów *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

Teraz Utwórz statyczny publiczny adres IP z [tworzenie publicznego adresu ip sieci az] [ az-network-public-ip-create] polecenia. Określ nazwę grupy zasobów węzła uzyskanego w poprzednim poleceniu, a następnie nazwę dla adresu IP adresów zasobów, takich jak *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

Adres IP jest wyświetlany, jak pokazano w następujących danych wyjściowych skróconego przykładu:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Możesz później uzyskać publiczny adres IP, korzystając [az sieci public-ip list] [ az-network-public-ip-list] polecenia. Określ nazwę grupy zasobów węzła publiczny adres IP został utworzony i zapytanie o *ipAddress* jak pokazano w poniższym przykładzie:

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę za pomocą statyczny publiczny adres IP, należy dodać `loadBalancerIP` właściwości i wartość statycznego publicznego adresu IP adresów w manifeście YAML. Utwórz plik o nazwie `load-balancer-service.yaml` i skopiuj do poniższego kodu YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku.

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

Tworzenie usługi i wdrażanie za pomocą `kubectl apply` polecenia.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Użyj statycznego adresu IP poza węzeł grupy zasobów

Za pomocą platformy Kubernetes 1.10 lub nowszej można użyć statycznego adresu IP, który jest tworzony poza węzeł grupy zasobów. Jednostki usługi używany przez klaster AKS musi mieć delegowane uprawnienia do innej grupy zasobów, jak pokazano w poniższym przykładzie:

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Aby korzystać z adresu IP poza węzeł grupy zasobów, dodawanie adnotacji do definicji usługi. Poniższy przykład ustawia adnotację do grupy zasobów o nazwie *myResourceGroup*. Podaj własną nazwę grupy zasobów:

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

Jeśli statyczny adres IP jest zdefiniowany w *loadBalancerIP* właściwości manifestu usługi Kubernetes, nie istnieje lub nie został utworzony w węźle grupy zasobów oraz brak dodatkowych delegacji skonfigurowane, usługi równoważenia obciążenia Tworzenie zakończy się niepowodzeniem. Aby rozwiązać problemy, przejrzyj zdarzenia tworzenia usługi przy użyciu [opisują kubectl] [ kubectl-describe] polecenia. Podaj nazwę usługi, jak określono w manifeście YAML, jak pokazano w poniższym przykładzie:

```console
kubectl describe service azure-load-balancer
```

Zostaną wyświetlone informacje o zasobie usługi Kubernetes. *Zdarzenia* na końcu następujące przykładowe dane wyjściowe wskazują, że *użytkownika podany adres IP nie został znaleziony*. W tych scenariuszach Sprawdź, czy utworzono statyczny publiczny adres IP, w węźle grupy zasobów i czy adres IP podany w manifeście usługi Kubernetes jest poprawna.

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

## <a name="next-steps"></a>Kolejne kroki

Dla dodatkowej kontroli nad ruchem sieciowym aplikacji, warto zamiast tego [utworzyć kontroler danych przychodzących][aks-ingress-basic]. Możesz również [utworzyć kontroler danych przychodzących z statyczny publiczny adres IP][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku