---
title: Używanie statycznego adresu IP dla ruchu wychodzącego
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć statyczny publiczny adres IP dla ruchu wychodzącego w klastrze usługi Kubernetes (AKS) usługi Azure
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 08a9682434605fffde73c835e7a9e9d6971d7ff0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803386"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Użyj statycznego publicznego adresu IP dla ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)

Domyślnie adres IP wychodzącego z klastra usługi Azure Kubernetes (AKS) jest przypisywany losowo. Ta konfiguracja nie jest idealna, gdy trzeba zidentyfikować adres IP dostępu do usług zewnętrznych, na przykład. Zamiast tego może być konieczne przypisanie statycznego adresu IP, który może być umieszczony na białej liście w celu uzyskania dostępu do usługi.

W tym artykule pokazano, jak utworzyć i używać statycznego publicznego adresu IP do użytku z ruchem wychodzącym w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Potrzebne są również zainstalowane i skonfigurowane i skonfigurowane narzędzia Azure CLI w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Przegląd ruchu wychodzącego

Ruch wychodzący z klastra AKS jest zgodny z [konwencjami równoważenia obciążenia platformy Azure.][outbound-connections] Przed utworzeniem pierwszej usługi Kubernetes typu `LoadBalancer` węzły agenta w klastrze AKS nie są częścią żadnej puli modułu równoważenia obciążenia platformy Azure. W tej konfiguracji węzły nie mają publicznego adresu IP na poziomie wystąpienia. Platforma Azure tłumaczy przepływ wychodzący na publiczny źródłowy adres IP, który nie jest konfigurowalny ani deterministyczny.

Po utworzeniu usługi Kubernetes typu `LoadBalancer` węzły agenta są dodawane do puli modułu równoważenia obciążenia platformy Azure. W przypadku przepływu wychodzącego platforma Azure tłumaczy go na pierwszy publiczny adres IP skonfigurowany na modułu równoważenia obciążenia. Ten publiczny adres IP jest prawidłowy tylko przez cały okres użytkowania tego zasobu. Jeśli usuniesz usługę Kubernetes LoadBalancer, skojarzony moduł równoważenia obciążenia i adres IP również zostaną usunięte. Aby przypisać określony adres IP lub zachować adres IP dla ponownie rozmieszczonych usług Kubernetes, można utworzyć i używać statycznego publicznego adresu IP.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj parametr kwerendy. `--query nodeResourceGroup` W poniższym przykładzie pobiera grupę zasobów węzła dla nazwy klastra AKS *myAKSCluster* w nazwie grupy zasobów *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz utwórz statyczny publiczny adres IP za pomocą polecenia [tworzenia publicznego adresu IP az.][az-network-public-ip-create] Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu adresu IP, takiego jak *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Adres IP jest pokazany, jak pokazano na poniższym skróconym przykładzie:

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
```

Później można uzyskać publiczny adres IP za pomocą polecenia [az network public-ip list.][az-network-public-ip-list] Określ nazwę grupy zasobów węzła, a następnie zapytaj o *adres ipAddress,* jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi ze statycznym adresem IP

Aby utworzyć usługę o statycznym publicznym adresie IP, dodaj `loadBalancerIP` właściwość i wartość statycznego publicznego adresu IP do manifestu YAML. Utwórz plik `egress-service.yaml` o nazwie i skopiuj w następującym pliku YAML. Podaj swój własny publiczny adres IP utworzony w poprzednim kroku.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Utwórz usługę i `kubectl apply` wdrożenie za pomocą polecenia.

```console
kubectl apply -f egress-service.yaml
```

Ta usługa konfiguruje nowy adres IP frontendu w modułu równoważenia obciążenia platformy Azure. Jeśli nie masz żadnych innych adresów IP skonfigurowane, a następnie **cały** ruch wychodzący powinien teraz używać tego adresu. Gdy wiele adresów są skonfigurowane na modułu Azure Load Balancer, wychodzący używa pierwszego adresu IP na tym moduł równoważenia obciążenia.

## <a name="verify-egress-address"></a>Weryfikowanie adresu wychodzącego

Aby sprawdzić, czy statyczny publiczny adres IP jest używany, można `checkip.dyndns.org`użyć usługi wyszukiwania DNS, takiej jak .

Rozpocznij i dołącz do podstawowej kapsuły *Debiana:*

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Aby uzyskać dostęp do witryny `apt-get` sieci `curl` web z poziomu kontenera, należy użyć do zainstalowania w kontenerze.

```console
apt-get update && apt-get install curl -y
```

Teraz użyj curl, aby uzyskać dostęp do *checkip.dyndns.org* witryny. Adres IP wyjścia jest wyświetlany, jak pokazano w poniższym przykładzie danych wyjściowych. Ten adres IP jest zgodny ze statycznym publicznym adresem IP utworzonym i zdefiniowanym dla usługi loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Następne kroki

Aby uniknąć utrzymywania wielu publicznych adresów IP na modułu Równoważenia obciążenia platformy Azure, zamiast tego można użyć kontrolera transferu danych przychodzących. Kontrolery transferu danych przychodzących zapewniają dodatkowe korzyści, takie jak zakończenie protokołu SSL/TLS, obsługa ponownego zapisywania identyfikatorów URI i szyfrowanie SSL/TLS. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowego kontrolera transferu danych przychodzących w u.][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
