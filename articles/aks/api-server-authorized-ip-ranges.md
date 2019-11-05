---
title: Zakresy adresów IP autoryzowanych serwerów interfejsu API w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zabezpieczyć klaster przy użyciu zakresu adresów IP na potrzeby dostępu do serwera interfejsu API w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472963"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Bezpieczny dostęp do serwera interfejsu API za pomocą zakresów autoryzowanych adresów IP w usłudze Azure Kubernetes Service (AKS)

W programie Kubernetes serwer interfejsu API odbiera żądania wykonania akcji w klastrze, takie jak tworzenie zasobów lub skalowanie liczby węzłów. Serwer interfejsu API jest centralną metodą współdziałania z klastrem i zarządzania nim. Aby zwiększyć bezpieczeństwo klastra i zminimalizować ataki, serwer interfejsu API powinien być dostępny tylko z ograniczonym zestawem zakresów adresów IP.

W tym artykule pokazano, jak używać zakresów adresów IP autoryzowanych przez serwer interfejsu API do ograniczania, które adresy IP i CIDR mogą uzyskać dostęp do płaszczyzny kontroli.

> [!IMPORTANT]
> W nowych klastrach zakresy adresów IP autoryzowanych przez serwer interfejsu API są obsługiwane tylko przez moduł równoważenia obciążenia *standardowej* jednostki SKU. Istniejące klastry z modułem równoważenia obciążenia *podstawowej* jednostki SKU oraz skonfigurowanymi zakresami autoryzowanych adresów IP serwera interfejsu API będą nadal działały zgodnie z oczekiwaniami. Te istniejące clusers można także uaktualnić i nadal będą działać.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że pracujesz z klastrami korzystającymi z [korzystającą wtyczki kubenet][kubenet].  W przypadku klastrów opartych na [interfejsie Azure Container Network Interface (CNI)][cni-networking] nie będzie potrzebna tabela tras wymagana do zabezpieczenia dostępu.  Należy ręcznie utworzyć tabelę tras.  Aby uzyskać więcej informacji, zobacz [Zarządzanie tabelami tras](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Zakres adresów IP autoryzowanych przez serwer API działa tylko w przypadku nowych klastrów AKS utworzonych przez Ciebie. W tym artykule opisano sposób tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure.

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Podczas konfigurowania zakresów autoryzowanych adresów IP serwera interfejsu API obowiązują następujące ograniczenia:

* Obecnie nie można używać Azure Dev Spaces, ponieważ komunikacja z serwerem interfejsu API jest również blokowana.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Przegląd zakresów adresów IP autoryzowanych serwerów interfejsu API

Serwer interfejsu API Kubernetes to sposób ujawniania podstawowych interfejsów API Kubernetes. Ten składnik zapewnia interakcję z narzędziami do zarządzania, takimi jak `kubectl` lub pulpit nawigacyjny Kubernetes. AKS zapewnia jednodostępny wzorzec klastra z dedykowanym serwerem interfejsu API. Domyślnie serwer interfejsu API ma przypisany publiczny adres IP i należy kontrolować dostęp przy użyciu kontroli dostępu opartej na rolach (RBAC).

Aby zabezpieczyć dostęp do dostępnego publicznie AKS kontroli/serwera interfejsu API, można włączyć i używać autoryzowanych zakresów adresów IP. Te autoryzowane zakresy adresów IP zezwalają na komunikowanie się z serwerem interfejsu API tylko zdefiniowanym zakresom IP. Żądanie wysłane do serwera interfejsu API z adresu IP, który nie jest częścią tych autoryzowanych zakresów adresów IP, jest blokowane. Należy nadal używać RBAC, aby autoryzować użytkowników i akcje, które żądają.

Aby uzyskać więcej informacji na temat serwera interfejsu API i innych składników klastra, zobacz [Kubernetes podstawowe pojęcia dotyczące AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Zakres adresów IP autoryzowanych przez serwer interfejsu API działa tylko w przypadku nowych klastrów AKS. Nie można włączyć autoryzowanych zakresów adresów IP w ramach operacji tworzenia klastra. Jeśli spróbujesz włączyć autoryzowane zakresy adresów IP w ramach procesu tworzenia klastra, węzły klastra nie będą mogły uzyskać dostępu do serwera interfejsu API podczas wdrażania, ponieważ w tym punkcie nie jest zdefiniowany adres IP ruchu wychodzącego.

Najpierw utwórz klaster przy użyciu polecenia [AZ AKS Create][az-aks-create] . Poniższy przykład tworzy klaster z jednym węzłem o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Aktualizowanie klastra z autoryzowanymi zakresami adresów IP

Domyślnie klaster używa usługi [równoważenia obciążenia standardowej jednostki SKU][standard-sku-lb], której można użyć do skonfigurowania bramy wychodzącej. Użyj [AZ Network Public-IP list][az-network-public-ip-list] i określ grupę zasobów klastra AKS, która zwykle zaczyna się od *MC_* . Spowoduje to wyświetlenie publicznego adresu IP dla klastra, którego można zezwolić. Użyj polecenia [AZ AKS Update][az-aks-update] i określ parametr *--API-Server-autoryzowane-IP-Ranges* , aby zezwolić na adres IP klastra. Na przykład:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Aby włączyć autoryzowane zakresy adresów IP serwera interfejsu API, użyj polecenia [AZ AKS Update][az-aks-update] i określ parametr *--API-Server-autoryzowane-IP-Ranges* , aby podać listę autoryzowanych zakresów adresów IP. Te zakresy adresów IP są zwykle zakresami adresów używanymi przez sieci lokalne lub publiczne adresy IP. Po określeniu zakresu CIDR, należy zacząć od pierwszego adresu IP z zakresu. Na przykład *137.117.106.90/29* jest prawidłowym zakresem, ale należy określić pierwszy adres IP z zakresu, na przykład *137.117.106.88/29*.

Poniższy przykład umożliwia włączenie zakresów adresów IP autoryzowanych serwerów interfejsu API w klastrze o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*. Zakresy adresów IP do autoryzacji to *172.0.0.0/16* (zakres adresów pod/Node) i *168.10.0.0/18* ():

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Należy dodać te zakresy do listy dozwolonych:
> - Publiczny adres IP zapory
> - CIDR usługi
> - Zakres adresów dla podsieci, z węzłami i zestawami
> - Każdy zakres reprezentujący sieci, z których będziesz administrować klastrem

## <a name="disable-authorized-ip-ranges"></a>Wyłącz autoryzowane zakresy adresów IP

Aby wyłączyć autoryzowane zakresy adresów IP, użyj [AZ AKS Update][az-aks-update] i określ pusty zakres, aby wyłączyć autoryzowane zakresy adresów IP serwera interfejsu API. Na przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Następne kroki

W tym artykule włączono zakres adresów IP autoryzowanych przez serwer interfejsu API. To podejście jest jedną z metod uruchomienia bezpiecznego klastra AKS.

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące zabezpieczeń aplikacji i klastrów w AKS][concepts-security] i [najlepszych rozwiązaniach dotyczących zabezpieczeń klastrów i uaktualnień w AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
