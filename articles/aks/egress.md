---
title: Statyczny adres IP dla ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć i używać statyczny publiczny adres IP dla ruchu wychodzącego w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 094a696a12025dcfd575ce3f035b12b4a04aba10
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615565"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Użyj statyczny publiczny adres IP dla ruchu wychodzącego w usłudze Azure Kubernetes Service (AKS)

Domyślnie są losowo przydzielani adres IP ruchu wychodzącego z klastra usługi Azure Kubernetes Service (AKS). Ta konfiguracja nie jest idealnym rozwiązaniem w przypadku, gdy należy określić adres IP, aby uzyskać dostęp do usług zewnętrznych, na przykład. Zamiast tego należy przypisać statyczny adres IP, który może być umieszczona na białej liście, aby uzyskać dostęp do usługi.

W tym artykule przedstawiono sposób tworzenia i używania statyczny publiczny adres IP do użycia z usługą ruch wychodzący w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli potrzebujesz instalacja lub uaktualnienie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj][install-azure-cli].

## <a name="egress-traffic-overview"></a>Omówienie ruchu wychodzącego

Ruch wychodzący z klastra usługi AKS następuje [konwencje usługi Azure Load Balancer][outbound-connections]. Przed pierwszej usługi Kubernetes typu `LoadBalancer` zostanie utworzony, agent węzłów w klastrze AKS nie są częścią żadnej puli usługi Azure Load Balancer. W tej konfiguracji węzły mają żaden poziom wystąpienia, publiczny adres IP. Azure tłumaczy przepływu wychodzącego do publicznych źródłowy adres IP, który nie jest konfigurowalne lub deterministyczna.

Gdy usługi Kubernetes typu `LoadBalancer` utworzeniu agenta dodawania węzłów w puli usługi Azure Load Balancer. Dla przepływu wychodzącego Azure tłumaczy to publiczny adres IP skonfigurowany w usłudze równoważenia obciążenia. Ten publiczny adres IP jest prawidłowy tylko dla cyklem życia tego zasobu. Jeśli usuniesz usługi Kubernetes usługi równoważenia obciążenia, moduł równoważenia obciążenia skojarzone i adres IP również zostaną usunięte. Jeśli chcesz przypisać określony adres IP lub zachować adres IP w ponownie wdrożonym usług Kubernetes, można tworzyć i używać statyczny publiczny adres IP.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Kiedy tworzysz statyczny publiczny adres IP do użycia za pomocą usługi AKS, należy utworzyć zasób adresu IP w **węzła** grupy zasobów. Pobierz nazwę grupy zasobów przy użyciu [az aks show][az-aks-show] polecenie i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera węzeł grupy zasobów dla nazwy klastra AKS *myAKSCluster* w polu Nazwa grupy zasobów *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz Utwórz statyczny publiczny adres IP z [tworzenie publicznego adresu ip sieci az][az-network-public-ip-create] polecenia. Określ nazwę grupy zasobów węzła uzyskanego w poprzednim poleceniu, a następnie nazwę dla adresu IP adresów zasobów, takich jak *myAKSPublicIP*:

```azurecli-interactive
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
```

Możesz później uzyskać publiczny adres IP, korzystając [az sieci public-ip list][az-network-public-ip-list] polecenia. Określ nazwę grupy zasobów, węzeł, a następnie wyszukać *ipAddress* jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Aby utworzyć usługę za pomocą statyczny publiczny adres IP, należy dodać `loadBalancerIP` właściwości i wartość statycznego publicznego adresu IP adresów w manifeście YAML. Utwórz plik o nazwie `egress-service.yaml` i skopiuj do poniższego kodu YAML. Podaj własny publiczny adres IP utworzony w poprzednim kroku.

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

Tworzenie usługi i wdrażanie za pomocą `kubectl apply` polecenia.

```console
kubectl apply -f egress-service.yaml
```

Ta usługa umożliwia skonfigurowanie nowego adresu IP frontonu modułu równoważenia obciążenia platformy Azure. Jeśli nie masz żadnych innych adresów IP skonfigurowane, następnie **wszystkich** ruch wychodzący teraz należy używać tego adresu. Gdy na usługi Azure Load Balancer skonfigurowano wiele adresów, ruch wychodzący używa pierwszy adres IP tego modułu równoważenia obciążenia.

## <a name="verify-egress-address"></a>Zweryfikuj adres ruchu wychodzącego

Aby sprawdzić, czy statyczny publiczny adres IP jest używany, można użyć Usługa wyszukiwania DNS, takich jak `checkip.dyndns.org`.

Uruchom i dołączyć do podstawowego *Debian* zasobnika:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Aby uzyskać dostęp do witryny sieci web w kontenerze, należy użyć `apt-get` zainstalował `curl` do kontenera.

```console
apt-get update && apt-get install curl -y
```

Teraz dostęp za pomocą programu curl *checkip.dyndns.org* lokacji. Adres IP ruchu wychodzącego jest wyświetlany, wyświetlaną w następujących przykładowych danych wyjściowych. Ten adres IP odpowiada statyczny publiczny adres IP utworzone i zdefiniowane przez usługę modułu równoważenia obciążenia:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uniknąć, obsługa wielu publicznych adresów IP dla modułu równoważenia obciążenia platformy Azure, możesz zamiast tego użyć kontrolera danych przychodzących. Ruch przychodzący kontrolery zapewniają dodatkowe korzyści, takich jak kończenia żądań SSL/TLS, pomoc techniczną dla identyfikatora URI modyfikacji oprogramowania i nadrzędne szyfrowania SSL/TLS. Aby uzyskać więcej informacji, zobacz [utworzyć kontroler podstawowy ruch przychodzący w usłudze AKS][ingress-aks-cluster].

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