---
title: Tworzenie usługi Azure Private Link przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć usługę Azure Private Link przy użyciu interfejsu wiersza polecenia platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350237"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Tworzenie usługi łącza prywatnego przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule pokazano, jak utworzyć usługę łącza prywatnego na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie zamiast tego, ten szybki start wymaga użycia najnowszej wersji interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego
### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów, która będzie hostowała tę sieć wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji westcentralus:*

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). W tym przykładzie tworzy domyślną sieć wirtualną o nazwie *myVirtualNetwork* z jedną podsiecią o nazwie *mySubnet:*

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Tworzenie podsieci
Utwórz podsieć dla sieci wirtualnej za pomocą [podsieci sieci AZ .](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) W tym przykładzie tworzy podsieć o nazwie *mySubnet* w sieci wirtualnej *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia 
Utwórz wewnętrzny moduł równoważenia obciążenia z [az network lb create](/cli/azure/network/lb#az-network-lb-create). W tym przykładzie utworzy się wewnętrzny moduł równoważenia obciążenia o nazwie *myILB* w grupie zasobów o nazwie *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Tworzenie sondy kondycji modułu równoważenia obciążenia

Sonda kondycji sprawdza wszystkie wystąpienia maszyny wirtualnej, aby upewnić się, że mogą one odbierać ruch sieciowy. Wystąpienie maszyny wirtualnej, w przypadku którego sprawdzanie kondycji za pomocą sondy nie powiodło się, jest usuwane z modułu równoważenia obciążenia do momentu ponownego przejścia do trybu online i pomyślnego sprawdzenia kondycji. Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia *myHTTPRule* za pomocą polecenia [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) w celu nasłuchiwania na porcie 80 w puli frontonu *myFrontEnd* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool*, również przy użyciu portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie nie obejmujemy tworzenia maszyn wirtualnych. Można wykonać kroki w [Tworzenie modułu wewnętrznego modułu równoważenia obciążenia, aby załadować maszyny wirtualne równoważenia przy użyciu narzędzia Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) do tworzenia dwóch maszyn wirtualnych, które mają być używane jako serwery wewnętrznej bazy danych dla modułu równoważenia obciążenia. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Wyłączanie zasad sieci usługi Private Link w podsieci 
Usługa Private Link wymaga adresu IP z dowolnej podsieci w sieci wirtualnej. Obecnie nie obsługujemy zasad sieciowych dotyczących tych adresów IP.  W związku z tym musimy wyłączyć zasady sieci w podsieci. Zaktualizuj podsieć, aby wyłączyć zasady sieci usługi Łącza prywatne z [aktualizacją podsieci sieci AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego  
 
Utwórz usługę Łącze prywatne przy użyciu konfiguracji IP frontendu standardowego modułu równoważenia obciążenia z [utworzeniem usługi prywatnej az.](/cli/azure/network/private-link-service#az-network-private-link-service-create) W tym przykładzie utworzy się usługę Łącze prywatne o nazwie *myPLS* przy użyciu standardowego modułu równoważenia obciążenia o nazwie *myLoadBalancer* w grupie zasobów o nazwie *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Po utworzeniu należy wziąć pod uwagę identyfikator usługi łącza prywatnego. Będzie to potrzebne później do żądania połączenia z tą usługą.  
 
Na tym etapie usługa Private Link została pomyślnie utworzona i jest gotowa do odbioru ruchu. Należy zauważyć, że powyższy przykład jest tylko zademonstrować tworzenie usługi private link przy użyciu interfejsu wiersza polecenia platformy Azure.  Firma We nie skonfigurowano pul zaplecza modułu równoważenia obciążenia ani dowolnej aplikacji w pulach wewnętrznej bazy danych, aby nasłuchiwać ruchu. Jeśli chcesz zobaczyć przepływy ruchu end-to-end, zdecydowanie zaleca się skonfigurować aplikację za standardowy moduł równoważenia obciążenia.  
 
Następnie zademonstrujemy sposób mapowania tej usługi do prywatnego punktu końcowego w innej sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Ponownie przykład jest ograniczony do tworzenia prywatnego punktu końcowego i łączenia się z usługą Private Link utworzonej powyżej przy użyciu interfejsu wiersza polecenia platformy Azure. Ponadto można utworzyć maszyny wirtualne w sieci wirtualnej, aby wysyłać/odbierać ruch do prywatnego punktu końcowego.        
 
## <a name="private-endpoints"></a>Prywatne punkty końcowe

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej 
Utwórz sieć wirtualną za pomocą [sieci wirtualnej AZ .](/cli/azure/network/vnet#az-network-vnet-create) W tym przykładzie tworzy sieć wirtualną o nazwie *myPEVNet* w grupie zasobów o nazwie *myResourcegroup:* 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Tworzenie podsieci 
Utwórz podsieć w sieci wirtualnej za pomocą [podsieci sieci AZ .](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) W tym przykładzie tworzy podsieć o nazwie *mySubnet* w sieci wirtualnej o nazwie *myPEVnet* w grupie zasobów o nazwie *myResourcegroup:* 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Wyłączanie zasad sieci prywatnych punktów końcowych w podsieci 
Prywatny punkt końcowy można utworzyć w dowolnej podsieci w sieci wirtualnej. Obecnie nie obsługujemy zasad sieciowych w prywatnych punktach końcowych.  W związku z tym musimy wyłączyć zasady sieci w podsieci. Zaktualizuj podsieć, aby wyłączyć zasady sieci prywatnego punktu końcowego za pomocą [aktualizacji podsieci sieci az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Tworzenie prywatnego punktu końcowego i łączenie się z usługą łączy prywatnych 
Utwórz prywatny punkt końcowy do korzystania z usługi Private Link utworzonej powyżej w sieci wirtualnej:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Identyfikator prywatnego *połączenia-zasobu* można uzyskać `az network private-link-service show` w usłudze Private Link. Identyfikator będzie wyglądał następująco:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Pokaż połączenia usługi Private Link 
 
Zobacz żądania połączenia w usłudze Private Link za pomocą [programu az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Private Link](private-link-service-overview.md)
 
