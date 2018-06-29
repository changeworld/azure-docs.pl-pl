---
title: Lista dozwolonych adresów ruch wychodzący ruch z klastra usługi Kubernetes Azure (AKS)
description: Lista dozwolonych adresów ruch wychodzący ruch z klastra usługi Kubernetes Azure (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100149"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure wyjście usługi Kubernetes (AKS)

Domyślnie jest losowo przypisany adres wyjście z klastra usługi Kubernetes Azure (AKS). Ta konfiguracja nie jest idealne rozwiązanie w przypadku konieczności określenie adresu IP do uzyskiwania dostępu do usług zewnętrznych. Ten dokument zawiera szczegóły dotyczące sposobu tworzenia i zarządzania adresem IP statycznie przypisane wyjście AKS klastra.

## <a name="egress-overview"></a>Transfer danych wychodzących — omówienie

Ruch wychodzący z klastra AKS jest zgodna z konwencjami modułu równoważenia obciążenia Azure, które są udokumentowane [tutaj][outbound-connections]. Przed pierwszym usługi Kubernetes typu `LoadBalancer` utworzeniu agenta węzłów nie są częścią żadnej puli usługi równoważenia obciążenia Azure. W tej konfiguracji węzłów nie na poziomie wystąpienia publicznego adresu IP. Azure tłumaczy przepływu wychodzącego do publicznego źródłowy adres IP, który nie jest można skonfigurować lub jest deterministyczna.

Raz usługi Kubernetes typu `LoadBalancer` utworzeniu agenta węzły są dodawane do puli usługi równoważenia obciążenia Azure. Dla przepływu ruchu wychodzącego Azure tłumaczy je na publiczny adres IP skonfigurowany w usłudze równoważenia obciążenia.

## <a name="create-a-static-public-ip"></a>Tworzenie statycznego publicznego adresu IP

Aby uniemożliwić losowe adresy IP, należy utworzyć statyczny adres IP i upewnij się, że moduł równoważenia obciążenia używa tego adresu. Adres IP musi zostać utworzona w AKS **węzła** grupy zasobów.

Pobierz nazwę grupy zasobów z [Pokaż zasobów az] [ az-resource-show] polecenia. Nazwa grupy zasobów i nazwa klastra do danego środowiska aktualizacji.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Następnie użyj [utworzyć az sieci publicznej ip] [ public-ip-create] polecenie, aby utworzyć statycznego publicznego adresu IP. Zaktualizuj nazwę grupy zasobów, aby dopasować gatherred nazwy w ostatnim kroku.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Tworzenie usługi za pomocą statycznego adresu IP

Teraz, gdy masz adresu IP, Utwórz usługę Kubernetes z typem `LoadBalancer` i przypisać adres IP do usługi.

Utwórz plik o nazwie `egress-service.yaml` i skopiuj następujące yaml programu. Zaktualizuj adres IP do danego środowiska.

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

Tworzenie tej usługi umożliwia skonfigurowanie nowego adresu IP frontonu modułu równoważenia obciążenia Azure. Jeśli nie masz żadnych innych adresów IP skonfigurowany, a **wszystkie** ruch wychodzący teraz użyć tego adresu. W przypadku wielu adresów są skonfigurowane w usłudze równoważenia obciążenia Azure, wyjście korzysta pierwszy adres IP w tej usługi równoważenia obciążenia.

## <a name="verify-egress-address"></a>Sprawdź poprawność adresu wyjście

Aby sprawdzić, czy publiczny adres IP jest używany, użyj usługi takie jak `checkip.dyndns.org`.

Uruchom i Dołącz do pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

W razie potrzeby zainstaluj narzędzie curl w kontenerze:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`, która zwraca wyjście adres IP:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Powinny być widoczne czy adres IP odpowiada statyczny adres IP, dołączone do usługi równoważenia obciążenia Azure.

## <a name="ingress-controller"></a>Transfer danych przychodzących kontrolera

Aby uniknąć utrzymania wiele publicznych adresów IP w usłudze równoważenia obciążenia Azure, należy rozważyć użycie kontrolera wejściowych. Transfer danych przychodzących — kontrolery zapewniają obsługę korzyści, takich jak obciążenia równoważenia zakończenia SSL/TLS dla identyfikatora URI modyfikacji oprogramowania i nadrzędne szyfrowania SSL/TLS. Aby uzyskać więcej informacji na temat kontrolerów ruch przychodzący w AKS, zobacz [NGINX Konfigurowanie kontrolera ruch przychodzący w klastrze AKS] [ ingress-aks-cluster] przewodnik.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat oprogramowania zostało to pokazane w tym dokumencie.

- [Helm interfejsu wiersza polecenia][helm-cli-install]
- [NGINX wejściowych kontrolera][nginx-ingress]
- [Połączenia wychodzące usługi równoważenia obciążenia Azure][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
