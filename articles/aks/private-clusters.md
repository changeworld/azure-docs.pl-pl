---
title: Tworzenie prywatnego klastra usługi Kubernetes platformy Azure
description: Dowiedz się, jak utworzyć prywatny klaster usługi Kubernetes platformy Azure (AKS)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499305"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Tworzenie prywatnego klastra usługi Kubernetes platformy Azure

W klastrze prywatnym płaszczyzna kontrolna lub serwer interfejsu API ma wewnętrzne adresy IP zdefiniowane w dokumencie [RFC1918 — Address Allocation for Private Internets.](https://tools.ietf.org/html/rfc1918) Za pomocą klastra prywatnego można zapewnić, że ruch sieciowy między serwerem interfejsu API a pulami węzłów pozostaje tylko w sieci prywatnej.

Płaszczyzna sterowania lub serwer interfejsu API znajduje się w subskrypcji platformy Azure (AKS) zarządzanej przez usługę Azure. Klaster klienta lub pula węzłów znajduje się w subskrypcji klienta. Serwer i puli klastra lub węzła mogą komunikować się ze sobą za pośrednictwem [usługi Azure Private Link][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest udostępniany w podsieci klastra AKS klienta.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej

## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra AKS

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów lub użyj istniejącej grupy zasobów dla klastra AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Gdzie *--enable-private-cluster* jest obowiązkową flagą dla klastra prywatnego. 

### <a name="advanced-networking"></a>Zaawansowana sieć  

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
Gdzie *--enable-private-cluster* jest obowiązkową flagą dla klastra prywatnego. 

> [!NOTE]
> Jeśli adres mostka platformy Docker CIDR (172.17.0.1/16) starć z podsiecią CIDR, należy odpowiednio zmienić adres mostka platformy Docker.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opcje łączenia się z klastrem prywatnym

Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. Aby zarządzać serwerem interfejsu API, należy użyć maszyny Wirtualnej, która ma dostęp do sieci wirtualnej azure (VNet) klastra AKS. Istnieje kilka opcji ustanawiania łączności sieciowej z klastrem prywatnym.

* Utwórz maszynę wirtualną w tej samej sieci wirtualnej platformy Azure (VNet) co klaster AKS.
* Użyj maszyny Wirtualnej w oddzielnej sieci i [skonfiguruj komunikację równorzędną sieci wirtualnej][virtual-network-peering].  Zobacz sekcję poniżej, aby uzyskać więcej informacji na temat tej opcji.
* Użyj połączenia [Trasy ekspresowej lub sieci VPN.][express-route-or-VPN]

Tworzenie maszyny Wirtualnej w tej samej sieci wirtualnej jako klaster AKS jest najprostszą opcją.  Express Route i VPN zwiększają koszty i wymagają dodatkowej złożoności sieci.  Komunikacja równorzędna sieci wirtualnej wymaga zaplanowania zakresów CIDR sieci, aby upewnić się, że nie ma nakładających się zakresów.

## <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Jak wspomniano, komunikacja równorzędna sieci wirtualnej jest jednym ze sposobów dostępu do klastra prywatnego. Aby korzystać z komunikacji równorzędnej sieci wirtualnej, należy skonfigurować łącze między siecią wirtualną a prywatną strefą DNS.
    
1. Przejdź do grupy zasobów MC_* w witrynie Azure portal.  
2. Wybierz prywatną strefę DNS.   
3. W lewym okienku wybierz łącze **Sieć wirtualna.**  
4. Utwórz nowe łącze, aby dodać sieć wirtualną maszyny Wirtualnej do prywatnej strefy DNS. Udostępnienie łącza strefa DNS zajmuje kilka minut.  
5. Wróć do grupy zasobów MC_* w witrynie Azure portal.  
6. W prawym okienku wybierz sieć wirtualną. Nazwa sieci wirtualnej jest w formie *aks-vnet-\**.  
7. W lewym okienku wybierz pozycję **Peerings**.  
8. Wybierz **pozycję Dodaj**, dodaj sieć wirtualną maszyny Wirtualnej, a następnie utwórz komunikację równorzędnej.  
9. Przejdź do sieci wirtualnej, w której masz maszynę wirtualną, wybierz **pozycję Peerings**, wybierz sieć wirtualną AKS, a następnie utwórz komunikację równorzędną. Jeśli zakresy adresów w sieci wirtualnej usługi AKS i kolizji sieci wirtualnej maszyny Wirtualnej, komunikacja równorzędna kończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej][virtual-network-peering].

## <a name="dependencies"></a>Zależności  

* Usługa Private Link jest obsługiwana tylko w standardowym modułie równoważenia obciążenia platformy Azure. Podstawowy moduł równoważenia obciążenia platformy Azure nie jest obsługiwany.  
* Aby użyć niestandardowego serwera DNS, dodaj adres Azure DNS IP 168.63.129.16 jako nadrzędny serwer DNS na niestandardowym serwerze DNS.

## <a name="limitations"></a>Ograniczenia 
* Nieautoryzowane zakresy IP nie mogą być stosowane do punktu końcowego serwera prywatnego interfejsu api, mają one zastosowanie tylko do publicznego serwera interfejsu API
* Strefy dostępności są obecnie obsługiwane w niektórych regionach, zobacz początek tego dokumentu 
* [Ograniczenia usługi Azure Private Link][private-link-service] mają zastosowanie do klastrów prywatnych, prywatnych punktów końcowych platformy Azure i punktów końcowych usługi sieci wirtualnej, które nie są obecnie obsługiwane w tej samej sieci wirtualnej.
* Brak obsługi węzłów wirtualnych w klastrze prywatnym do obracania prywatnych wystąpień kontenerów platformy Azure (ACI) w prywatnej sieci wirtualnej platformy Azure
* Brak obsługi integracji usługi Azure DevOps po wyjęciu z pudełkiem z klastrami prywatnymi
* Dla klientów, którzy muszą włączyć usługę Azure Container Registry do pracy z prywatnym usługą AKS, sieć wirtualna rejestru kontenerów musi być równorzędna z siecią wirtualną klastra agentów.
* Brak bieżącej obsługi usługi Azure Dev Spaces
* Brak obsługi konwersji istniejących klastrów AKS na klastry prywatne
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać. 
* Usługa Azure Monitor dla kontenerów Dane na żywo nie jest obecnie obsługiwana.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

