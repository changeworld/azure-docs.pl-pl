---
title: Wdrażanie aplikacji dwusłonowej IPv6 — podstawowy moduł równoważenia obciążenia — interfejs wiersza polecenia
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu IPv6 w sieci wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 396c37d4c8de6a890102e435c5ec6cc70b598638
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421021"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Wdrażanie aplikacji dwusłonowej IPv6 przy użyciu podstawowego modułu równoważenia obciążenia — cli

W tym artykule pokazano, jak wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) z podstawowym modułem równoważenia obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure, która zawiera sieć wirtualną z dwoma stosami z podsiecią z podwójnym stosem, podstawowy moduł równoważenia obciążenia z dwoma (IPv4 + IPv6), maszyny wirtualne z kartami sieciowymi, które mają podwójną konfigurację IP, dwie reguły grupy zabezpieczeń sieci i podwójne publiczne adresy IP.

Aby wdrożyć aplikację z dwoma stosami (IPV4 + IPv6) przy użyciu standardowego modułu równoważenia obciążenia, zobacz [Wdrażanie aplikacji podwójnego stosu IPv6 ze standardowym modułem równoważenia obciążenia przy użyciu narzędzia Azure CLI.](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)


Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie zamiast tego, ten szybki start wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej z dwoma stosami należy utworzyć grupę zasobów z [utworzeniem grupy AZ](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *DsResourceGroup01* w lokalizacji *eastus:*

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Tworzenie publicznych adresów IP IPv4 i IPv6 dla modułu równoważenia obciążenia
Aby uzyskać dostęp do punktów końcowych IPv4 i IPv6 w Internecie, potrzebujesz publicznych adresów IP IPv4 i IPv6 dla modułu równoważenia obciążenia. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Poniższy przykład tworzy publiczny adres IP IPv4 i IPv6 o nazwie *dsPublicIP_v4* i *dsPublicIP_v6* w grupie zasobów *DsResourceGroup01:*

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Tworzenie publicznych adresów IP dla maszyn wirtualnych

Aby zdalnie uzyskiwać dostęp do maszyn wirtualnych w Internecie, potrzebujesz publicznych adresów IP protokołu IPv4 dla maszyn wirtualnych. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji można skonfigurować adres IP z dwiema frontendami (IPv4 i IPv6) oraz pulę adresów zaplecza dla modułu równoważenia obciążenia, a następnie utworzyć podstawowy moduł równoważenia obciążenia.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Utwórz podstawowy moduł równoważenia obciążenia z [siecią az lb utwórz](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie **dsLB,** która zawiera pulę frontonu o nazwie **dsLbFrontEnd_v4**, pulę wewnętrznej bazy danych o nazwie **dsLbBackEndPool_v4** skojarzoną z publicznym adresem IP IPv4 **dsPublicIP_v4** utworzonym w poprzednim kroku. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Tworzenie frontendu IPv6

Utwórz ipv6 frontend IP z [az sieci lb frontend-ip utworzyć](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). Poniższy przykład tworzy konfigurację IP wewnętrznej bazy o nazwie *dsLbFrontEnd_v6* i dołącza *adres dsPublicIP_v6:*

```azurecli
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurowanie puli adresów zaplecza IPv6

Utwórz pule adresów zaplecza IPv6 z [utworzeniem puli adresów az lb](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). W poniższym przykładzie utworzono pulę adresów zaplecza o nazwie *dsLbBackEndPool_v6* w celu uwzględnienia maszyn wirtualnych z konfiguracjami kart sieciowych IPv6:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Utwórz sondę kondycji za pomocą polecenia [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) w celu monitorowania kondycji maszyn wirtualnych. 

```azurecli
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). W poniższym przykładzie utworzono reguły równoważenia obciążenia o nazwach *dsLBrule_v4* i *dsLBrule_v6* i równoważą ruch na porcie *TCP* *80* do konfiguracji IP frontendu IPv4 i IPv6:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed wdrożeniem niektórych maszyn wirtualnych należy utworzyć pomocnicze zasoby sieciowe — zestaw dostępności, grupę zabezpieczeń sieci, sieć wirtualną i wirtualne karty sieciowe. 
### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby zwiększyć dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Utwórz zestaw dostępności z [az vm dostępność zestaw create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Poniższy przykład tworzy zestaw dostępności o nazwie *dsAVset:*

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz grupę zabezpieczeń sieci dla reguł, które będą regulować komunikację przychodzącą i wychodzącą w sieci wirtualnej.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Tworzenie sieciowej grupy zabezpieczeń z [utworzeniem sieci AZ network nsg](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla połączeń przychodzących i wychodzących

Utwórz regułę sieciowej grupy zabezpieczeń, aby umożliwić połączenia RDP za pośrednictwem portu 3389, połączenie internetowe za pośrednictwem portu 80 oraz połączenia wychodzące z [regułą nsg sieci AZ .](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVNET* z podsieciami *dsSubNET_v4* i *dsSubNET_v6:*

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Tworzenie kart sieciowych

Tworzenie wirtualnych kart sieciowych dla każdej maszyny Wirtualnej za pomocą [sieciowej nic az .](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) Poniższy przykład tworzy wirtualną kartę sieciową dla każdej maszyny Wirtualnej. Każda karta sieciowa ma dwie konfiguracje IP (1 konfiguracja IPv4, 1 konfiguracja IPv6). Tworzenie konfiguracji IPV6 z [az sieci nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).

```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Tworzenie maszyn wirtualnych za pomocą [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją. 

Utwórz maszynę wirtualną *dsVM0* w następujący sposób:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Utwórz maszynę wirtualną *dsVM1* w następujący sposób:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej z dwoma stosami IPv6 w witrynie Azure portal
Sieć wirtualną z dwoma stosami IPv6 można wyświetlić w witrynie Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *dsVnet*.
2. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej z dwoma stosami o nazwie *dsVnet*. Sieć wirtualna z dwoma stosami pokazuje dwie karty sieciowe z konfiguracjami IPv4 i IPv6 znajdującymi się w podsieci podwójnego stosu o nazwie *dsSubnet*.

  ![Sieć wirtualna z dwoma stosami IPv6 na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group#az-group-delete) aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```azurecli
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono podstawowy moduł równoważenia obciążenia z konfiguracją IP z podwójnym frontendem (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które zawierały karty sieciowe z dwiema konfiguracjami IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)
