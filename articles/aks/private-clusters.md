---
title: Tworzenie prywatnego klastra usługi Azure Kubernetes Service
description: Dowiedz się, jak utworzyć prywatny klaster usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205170"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Tworzenie prywatnego klastra usługi Azure Kubernetes Service

W klastrze prywatnym płaszczyzna kontroli lub serwer interfejsu API ma wewnętrzne adresy IP, które są zdefiniowane w [alokacji RFC1918-Address dla prywatnych Internetu](https://tools.ietf.org/html/rfc1918) dokumentów. Za pomocą klastra prywatnego można zapewnić, że ruch sieciowy między serwerem interfejsu API i pulami węzłów pozostanie tylko w sieci prywatnej.

Płaszczyzna kontroli lub serwer interfejsu API znajduje się w subskrypcji platformy Azure zarządzanej przez usługę Azure Kubernetes Service (AKS). Klaster klienta lub Pula węzłów znajduje się w subskrypcji klienta. Serwer i Pula węzłów mogą komunikować się ze sobą za pomocą [usługi Azure Private link][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest udostępniany w podsieci klastra AKS klienta.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra AKS

### <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz grupę zasobów lub Użyj istniejącej grupy zasobów dla klastra AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--enable-Private-Cluster* jest obowiązkową flagą dla klastra prywatnego. 

### <a name="advanced-networking"></a>Zaawansowane sieci  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Where *--enable-Private-Cluster* jest obowiązkową flagą dla klastra prywatnego. 

> [!NOTE]
> Jeśli adres CIDR (172.17.0.1/16) mostka platformy Docker koliduje z maską CIDR podsieci, należy odpowiednio zmienić adres mostka platformy Docker.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opcje łączenia się z klastrem prywatnym

Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. Aby zarządzać serwerem interfejsu API, należy użyć maszyny wirtualnej, która ma dostęp do Virtual Network platformy Azure klastra AKS. Istnieje kilka opcji ustanawiania łączności sieciowej z klastrem prywatnym.

* Utwórz maszynę wirtualną w tej samej usłudze Azure Virtual Network (VNet) jako klaster AKS.
* Użyj maszyny wirtualnej w oddzielnym sieci i skonfiguruj [komunikację równorzędną sieci wirtualnej][virtual-network-peering].  Zapoznaj się z sekcją poniżej, aby uzyskać więcej informacji na temat tej opcji.
* Użyj usługi [Express Route lub połączenia sieci VPN][express-route-or-VPN] .

Najłatwiej jest utworzyć maszynę wirtualną w tej samej sieci wirtualnej, co klaster AKS.  Funkcja Express Route i sieci VPN zwiększa koszty i wymaga dodatkowej złożoności sieci.  Komunikacja równorzędna sieci wirtualnych wymaga zaplanowania zakresów CIDR sieci, aby upewnić się, że nie ma nakładających się zakresów.

## <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Jak wspomniano, Komunikacja równorzędna sieci wirtualnej jest jednym ze sposobów uzyskiwania dostępu do klastra prywatnego. Aby można było użyć komunikacji równorzędnej sieci wirtualnej, należy skonfigurować łącze między siecią wirtualną i prywatną strefą DNS.
    
1. Przejdź do grupy zasobów MC_ * w Azure Portal.  
2. Wybierz prywatną strefę DNS.   
3. W lewym okienku wybierz łącze **Sieć wirtualna** .  
4. Utwórz nowy link, aby dodać sieć wirtualną maszyny wirtualnej do prywatnej strefy DNS. Udostępnienie linku strefy DNS może potrwać kilka minut.  
5. Wróć do grupy zasobów MC_ * w Azure Portal.  
6. W prawym okienku wybierz sieć wirtualną. Nazwa sieci wirtualnej ma postać *AKS-VNET-\** .  
7. W lewym okienku wybierz pozycję **Komunikacja równorzędna**.  
8. Wybierz pozycję **Dodaj**, Dodaj sieć wirtualną maszyny wirtualnej, a następnie utwórz komunikację równorzędną.  
9. Przejdź do sieci wirtualnej, w której znajduje się maszyna wirtualna, wybierz pozycję **Komunikacja równorzędna**, wybierz sieć wirtualną AKS, a następnie utwórz komunikację równorzędną. Jeśli zakresy adresów w sieci wirtualnej AKS i konflikty sieci wirtualnej maszyn wirtualnych są niepowodzeniem, Komunikacja równorzędna nie powiedzie się. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne][virtual-network-peering].

## <a name="dependencies"></a>Zależności  

* Usługa link prywatny jest obsługiwana tylko w przypadku standardowej Azure Load Balancer. Podstawowa Azure Load Balancer nie jest obsługiwana.  
* Aby użyć niestandardowego serwera DNS, wdróż serwer usługi AD przy użyciu systemu DNS do przesyłania dalej do tego 168.63.129.16 IP

## <a name="limitations"></a>Ograniczenia 
* Nie można zastosować dozwolonych zakresów adresów IP do punktu końcowego serwera prywatnego interfejsu API, są one stosowane tylko do publicznego serwera interfejsu API
* Strefy dostępności są obecnie obsługiwane w niektórych regionach, zobacz początek tego dokumentu. 
* [Ograniczenia usługi Azure Private link][private-link-service] są stosowane do prywatnych klastrów, prywatnych punktów końcowych platformy Azure i punktów końcowych usługi sieci wirtualnej, które nie są obecnie obsługiwane w tej samej sieci wirtualnej.
* Brak obsługi węzłów wirtualnych w klastrze prywatnym do Azure Container Instances prywatnego (ACI) w prywatnej sieci wirtualnej platformy Azure
* Brak wsparcia dla integracji usługi Azure DevOps z użyciem klastrów prywatnych
* W przypadku klientów, którzy muszą umożliwić Azure Container Registry pracy z prywatnym AKS, Container Registry sieci wirtualnej musi być połączona z siecią wirtualną klastra agentów.
* Brak bieżącej obsługi Azure Dev Spaces
* Brak obsługi konwertowania istniejących klastrów AKS do klastrów prywatnych
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać. 
* Azure Monitor kontenerów danych na żywo nie są obecnie obsługiwane.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

