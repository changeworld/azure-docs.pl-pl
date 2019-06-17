---
title: Wdróż aplikację podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure — interfejs wiersza polecenia
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania aplikacji podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure przy użyciu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62131014"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Wdróż aplikację podwójnego stosu protokołu IPv6 w sieci wirtualnej platformy Azure - CLI (wersja zapoznawcza)

W tym artykule pokazano, jak wdrożyć aplikację na platformie Azure, która obejmuje sieć wirtualną podwójnego stosu z podsiecią podwójnego stosu, moduł równoważenia obciążenia z konfiguracji frontonu podwójnego (IPv4 + IPv6), maszyn wirtualnych przy użyciu karty sieciowe, które ma podwójną konfiguracji adresu IP, podwójny stos (IPv4 + IPv6) reguły podwójną sieciowej grupy zabezpieczeń, a dwa publiczne adresy IP.

> [!Important]
> Podwójnego stosu protokołu IPv6 dla usługi Azure Virtual Network jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i użyć wiersza polecenia platformy Azure lokalnie, ten przewodnik Szybki Start wymaga przy użyciu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Aby dowiedzieć się, jaka wersja jest zainstalowana, uruchom polecenie `az --version`. Aby uzyskać informacje na temat instalacji i uaktualnienia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne
Aby używać protokołu IPv6 dla funkcji sieci wirtualnej platformy Azure, należy skonfigurować subskrypcji przy użyciu programu Azure PowerShell w następujący sposób:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Trwa rejestracja funkcji ukończyć do 30 minut. Za pomocą następującego polecenia wiersza polecenia platformy Azure, możesz sprawdzić swój status rejestracji:

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
Po zakończeniu rejestracji, uruchom następujące polecenie:

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej obsługującej podwójny stos, należy utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myRGDualStack* w *eastus* lokalizacji:

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Utwórz protokołów IPv4 i IPv6 publiczne adresy IP dla modułu równoważenia obciążenia
Aby uzyskać dostęp do punktów końcowych protokołów IPv4 i IPv6 w Internecie, należy protokołów IPv4 i IPv6 publiczne adresy IP dla modułu równoważenia obciążenia. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Poniższy przykład tworzy protokołów IPv4 i IPv6 publiczny adres IP o nazwie *dsPublicIP_v4* i *dsPublicIP_v6* w *myRGDualStack* grupy zasobów:

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

## <a name="create-public-ip-addresses-for-vms"></a>Tworzenie publiczne adresy IP dla maszyn wirtualnych

Zdalnie uzyskiwać dostęp do maszyn wirtualnych w sieci internet, należy publicznych adresów IPv4 dla maszyn wirtualnych. Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip).

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

W tej sekcji konfigurowania adresu IP frontonu podwójny (IPv4 i IPv6) i puli adresów zaplecza modułu równoważenia obciążenia, a następnie Tworzenie podstawowego modułu równoważenia obciążenia.

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Tworzenie podstawowego modułu równoważenia obciążenia za pomocą [tworzenie równoważenia obciążenia sieciowego az](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) o nazwie **dsLB** zawierającą pulę frontonu o nazwie **dsLbFrontEnd_v4**, pulę zaplecza o nazwie  **dsLbBackEndPool_v4** skojarzony z publicznym adresem IP protokołu IPv4 **dsPublicIP_v4** utworzonego w poprzednim kroku. 

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

### <a name="create-ipv6-frontend"></a>Tworzenie frontonu protokołu IPv6

Tworzenie adresu IP frontonu protokołu IPV6 z [tworzenie az sieci lb frontend-ip](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). W poniższym przykładzie utworzono konfigurację adresu IP frontonu o nazwie *dsLbFrontEnd_v6* i dołącza *dsPublicIP_v6* adresu:

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Skonfiguruj pulę adresów zaplecza IPv6

Tworzenie pul z adresów zaplecza IPv6 [az lb puli adresów sieciowych — tworzenie](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). Poniższy przykład obejmuje tworzenie puli adresów zaplecza o nazwie *dsLbBackEndPool_v6* obejmujący maszynami wirtualnymi z konfiguracjami IPv6 karty Sieciowej:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. 

Utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create). Poniższy przykład obejmuje tworzenie reguły modułu równoważenia obciążenia o nazwie *dsLBrule_v4* i *dsLBrule_v6* i równoważy ruch na *TCP* portu *80* do IPv4 i IPv6 konfiguracji adresów IP frontonu:

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Przed przystąpieniem do wdrażania maszyn wirtualnych, należy utworzyć pomocnicze zasoby sieci — zestaw dostępności, sieciowej grupy zabezpieczeń, sieci wirtualnej i wirtualne karty sieciowe. 
### <a name="create-an-availability-set"></a>Tworzenie zestawu dostępności
Aby zwiększyć dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). Poniższy przykład obejmuje tworzenie zestawu dostępności o nazwie *dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń dla reguły rządzące ruchu przychodzącego i wychodzącego komunikacji w sieci Wirtualnej.

#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Tworzenie reguły grupy zabezpieczeń sieci dla połączeń przychodzących i wychodzących

Tworzenie reguły grupy zabezpieczeń sieci, aby zezwolić na połączenia RDP przy użyciu portu 3389, połączenie z Internetem za pośrednictwem portu 80, a dla połączeń wychodzących z [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

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

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Poniższy przykład tworzy sieć wirtualną o nazwie *dsVNET* z podsieciami *dsSubNET_v4* i *dsSubNET_v6*:

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

Tworzenie wirtualnych kart sieciowych dla każdej maszyny Wirtualnej za pomocą [tworzenie az sieciowego](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Poniższy przykład tworzy wirtualną kartę Sieciową dla każdej maszyny Wirtualnej. Każda karta sieciowa ma dwie konfiguracje adresów IP (1 konfiguracji protokołu IPv4, 1 konfiguracji protokołu IPv6). Utwórz konfigurację protokołu IPV6 z [tworzenie az sieci nic ip-config](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
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

Tworzenie maszyn wirtualnych za pomocą [tworzenie az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją. 

Tworzenie maszyny wirtualnej *dsVM0* w następujący sposób:

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
Tworzenie maszyny wirtualnej *dsVM1* w następujący sposób:

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetl sieć wirtualna podwójnego stosu protokołu IPv6 w witrynie Azure portal
Sieć wirtualna podwójnego stosu protokołu IPv6 można wyświetlić w witrynie Azure portal w następujący sposób:
1. Na pasku wyszukiwania portalu wpisz *dsVnet*.
2. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie **Przegląd** strony podwójnego stosu sieci wirtualnej o nazwie *dsVnet*. Podwójny stos sieci wirtualnej zawiera dwie karty sieciowe IPv4 i IPv6 konfiguracji znajduje się w podsieci podwójnego stosu, o nazwie *dsSubnet*.

  ![Podwójnego stosu protokołu IPv6 sieci wirtualnej na platformie Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Protokół IPv6 dla sieci wirtualnej platformy Azure jest dostępna w witrynie Azure portal w trybie tylko do odczytu dla tej wersji zapoznawczej.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono podstawowy moduł równoważenia obciążenia za pomocą podwójnego konfigurację adresu IP frontonu (IPv4 i IPv6). Utworzono również dwie maszyny wirtualne, które karty sieciowe dołączone do dwóch konfiguracji adresu IP (IPV4 + IPv6), które zostały dodane do puli zaplecza modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat obsługi protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)