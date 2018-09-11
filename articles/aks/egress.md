---
title: Lista dozwolonych adresów ruchu wychodzącego ze klastra Azure Kubernetes Service (AKS)
description: Lista dozwolonych adresów ruchu wychodzącego ze klaster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347804"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Ruch wychodzący w usłudze Azure Kubernetes Service (AKS)

Domyślnie są losowo przydzielani adres ruchu wychodzącego z klastra usługi Azure Kubernetes Service (AKS). Ta konfiguracja nie jest idealnym rozwiązaniem, jeśli zachodzi potrzeba zidentyfikować adres IP do uzyskiwania dostępu do usług zewnętrznych. W tym dokumencie opisano, jak tworzyć i obsługiwać ruch wychodzący statycznie przypisanego adresu IP, w klastrze AKS.

## <a name="egress-overview"></a>Wyjście — omówienie

Ruch wychodzący z klastra usługi AKS jest zgodna z konwencjami modułu równoważenia obciążenia platformy Azure, które są udokumentowane [tutaj][outbound-connections]. Przed pierwszej usługi Kubernetes typu `LoadBalancer` utworzeniu agenta węzły nie są częścią żadnej puli usługi Azure Load Balancer. W tej konfiguracji węzłów nie na poziomie wystąpienia, publiczny adres IP. Azure tłumaczy przepływu wychodzącego do publicznych źródłowy adres IP, który nie jest konfigurowalne lub deterministyczna.

Gdy usługi Kubernetes typu `LoadBalancer` utworzeniu agenta dodawania węzłów w puli usługi Azure Load Balancer. Dla przepływu wychodzącego Azure tłumaczy to publiczny adres IP skonfigurowany w usłudze równoważenia obciążenia.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Aby uniemożliwić losowych adresów IP, Utwórz statyczny adres IP i upewnij się, że moduł równoważenia obciążenia używa tego adresu. Adres IP musi zostać utworzona w usłudze AKS **węzła** grupy zasobów.

Pobierz nazwę grupy zasobów przy użyciu [az resource show] [ az-resource-show] polecenia. Zaktualizuj nazwę grupy zasobów i nazwę klastra pod kątem danego środowiska.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Następnie użyj [tworzenie sieci az public-ip] [ public-ip-create] polecenie, aby utworzyć statyczny publiczny adres IP. Zaktualizuj nazwę grupy zasobów, aby dopasować gatherred nazwy w ostatnim kroku.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi przy użyciu statycznego adresu IP

Teraz, gdy adres IP, tworzenie usługi Kubernetes z typem `LoadBalancer` i przypisać adres IP do usługi.

Utwórz plik o nazwie `egress-service.yaml` i skopiuj do poniższego kodu YAML. Zaktualizuj adres IP, pod kątem danego środowiska.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Tworzenie usługi i wdrażanie za pomocą `kubectl apply` polecenia.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Tworzenie tej usługi Konfiguruje nowy adres IP frontonu modułu równoważenia obciążenia platformy Azure. Jeśli nie masz żadnych innych adresów IP skonfigurowane, następnie **wszystkich** ruch wychodzący teraz należy używać tego adresu. Gdy na usługi Azure Load Balancer skonfigurowano wiele adresów, ruch wychodzący używa pierwszy adres IP tego modułu równoważenia obciążenia.

## <a name="verify-egress-address"></a>Zweryfikuj adres ruchu wychodzącego

Aby sprawdzić, czy publiczny adres IP jest używany, użyj usługi takie jak `checkip.dyndns.org`.

Uruchom i dołączyć do zasobnika:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Jeśli to konieczne, zainstaluj narzędzie curl w kontenerze:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, która zwraca adres IP ruchu wychodzącego:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Należy sprawdzić, czy adres IP odpowiada statyczny adres IP dołączony do równoważenia obciążenia platformy Azure.

## <a name="ingress-controller"></a>Kontroler danych przychodzących

Aby uniknąć, obsługa wielu publicznych adresów IP dla modułu równoważenia obciążenia platformy Azure, należy rozważyć użycie kontrolera danych przychodzących. Ruch przychodzący kontrolery zapewniają korzyści, takie jak Równoważenie obciążenia, kończenie żądań SSL/TLS, obsługę dla identyfikatora URI modyfikacji oprogramowania i nadrzędne szyfrowania SSL/TLS. Aby uzyskać więcej informacji na temat kontrolerów ruch przychodzący w usłudze AKS, zobacz [Konfigurowanie serwera NGINX kontrolera danych przychodzących w klastrze AKS] [ ingress-aks-cluster] przewodnik.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o oprogramowaniu, przedstawione w tym dokumencie.

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli-install]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]
- [Połączenia wychodzące przez moduł równoważenia obciążenia platformy Azure][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
