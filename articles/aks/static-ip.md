---
title: Używanie statycznego adresu IP z modułem równoważenia obciążenia
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć i używać statycznego adresu IP za pomocą modułu równoważenia obciążenia usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886742"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Używanie statycznego publicznego adresu IP i etykiety DNS za pomocą modułu równoważenia obciążenia usługi Azure Kubernetes (AKS)

Domyślnie publiczny adres IP przypisany do zasobu modułu równoważenia obciążenia utworzonego przez klaster AKS jest prawidłowy tylko przez cały okres użytkowania tego zasobu. Jeśli usuniesz usługę Kubernetes, skojarzony moduł równoważenia obciążenia i adres IP również zostaną usunięte. Aby przypisać określony adres IP lub zachować adres IP dla ponownie rozmieszczonych usług Kubernetes, można utworzyć i używać statycznego publicznego adresu IP.

W tym artykule pokazano, jak utworzyć statyczny publiczny adres IP i przypisać go do usługi Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

W tym artykule opisano użycie *standardowego* adresu IP jednostki SKU ze *standardowym* modułem równoważenia obciążenia jednostki SKU. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Tworzenie statycznego adresu IP

Utwórz statyczny publiczny adres IP za pomocą polecenia [tworzenia publicznego adresu IP az.][az-network-public-ip-create] Poniżej przedstawiono utworzenie statycznego zasobu IP o nazwie *myAKSPublicIP* w grupie zasobów *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Jeśli używasz *podstawowego* modułu równoważenia obciążenia SKU w klastrze AKS, użyj *basic* dla parametru *sku* podczas definiowania publicznego adresu IP. Tylko *podstawowe jednostki* SKU współpracują z *podstawowym* modułem równoważenia obciążenia jednostki SKU i tylko *standardowe* jednostki SKU współpracują ze *standardowymi* modułami równoważenia obciążenia jednostki SKU. 

Wyświetlany jest adres IP, jak pokazano na poniższym skróconym przykładzie:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Później można uzyskać publiczny adres IP za pomocą polecenia [az network public-ip list.][az-network-public-ip-list] Określ nazwę utworzonej grupy zasobów węzła i publiczny adres IP oraz zapytaj o *adres ipAddress,* jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Przed utworzeniem usługi upewnij się, że podmiot zabezpieczeń usługi używany przez klaster AKS ma delegowane uprawnienia do innej grupy zasobów. Przykład:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Alternatywnie można użyć systemu przypisanego tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

Aby utworzyć usługę *LoadBalancer* ze statycznym publicznym `loadBalancerIP` adresem IP, dodaj właściwość i wartość statycznego publicznego adresu IP do manifestu YAML. Utwórz plik `load-balancer-service.yaml` o nazwie i skopiuj w następującym pliku YAML. Podaj swój własny publiczny adres IP utworzony w poprzednim kroku. Poniższy przykład ustawia również adnotację do grupy zasobów o nazwie *myResourceGroup*. Podaj własną nazwę grupy zasobów.

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

Utwórz usługę i `kubectl apply` wdrożenie za pomocą polecenia.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Stosowanie etykiety DNS do usługi

Jeśli usługa używa dynamicznego lub statycznego publicznego adresu IP, `service.beta.kubernetes.io/azure-dns-label-name` można użyć adnotacji usługi, aby ustawić publiczną etykietę DNS. Spowoduje to opublikowanie w pełni kwalifikowanej nazwy domeny dla usługi przy użyciu publicznych serwerów DNS platformy Azure i domeny najwyższego poziomu. Wartość adnotacji musi być unikatowa w lokalizacji platformy Azure, dlatego zaleca się użycie etykiety o wystarczającej kwalifikacjach.   

Platforma Azure automatycznie dołączy domyślną podsieć, taką jak `<location>.cloudapp.azure.com` (gdzie lokalizacja jest wybrany regionem), do nazwy, którą podasz, aby utworzyć w pełni kwalifikowaną nazwę DNS. Przykład:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Aby opublikować usługę we własnej domenie, zobacz [usługa Azure DNS][azure-dns-zone] i projekt dns [zewnętrznego.][external-dns]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli statyczny adres IP zdefiniowany we właściwości *loadBalancerIP* manifestu usługi Kubernetes nie istnieje lub nie został utworzony w grupie zasobów węzła i nie skonfigurowano żadnych dodatkowych delegacji, tworzenie usługi równoważenia obciążenia kończy się niepowodzeniem. Aby rozwiązać problem, przejrzyj zdarzenia tworzenia usługi za pomocą [polecenia kubectl describe.][kubectl-describe] Podaj nazwę usługi określoną w manifeście YAML, jak pokazano w poniższym przykładzie:

```console
kubectl describe service azure-load-balancer
```

Wyświetlane są informacje o zasobie usługi Kubernetes. *Zdarzenia* na końcu poniższego przykładowego danych wyjściowych wskazują, że *nie znaleziono adresu IP podanego przez użytkownika*. W tych scenariuszach sprawdź, czy utworzono statyczny publiczny adres IP w grupie zasobów węzła i czy adres IP określony w manifeście usługi Kubernetes jest poprawny.

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

Aby uzyskać dodatkową kontrolę nad ruchem sieciowym do aplikacji, można zamiast tego [utworzyć kontroler transferu danych przychodzących][aks-ingress-basic]. Można również [utworzyć kontroler ruchu przychodzącego ze statycznym publicznym adresem IP][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
