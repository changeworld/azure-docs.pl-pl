---
title: Używany wewnętrzny serwer DNS do rozpoznawania nazw maszyny Wirtualnej Azure CLI 2.0 | Dokumentacja firmy Microsoft
description: Tworzenie sieci wirtualnej kart i korzystania z wewnętrznego serwera DNS do rozpoznawania nazw maszyny Wirtualnej na platformie Azure, Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 8df9035cf4a5e62102353c701625526e211b7282
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915331"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Tworzenie karty interfejsu sieci wirtualnej i korzystania z wewnętrznego serwera DNS do rozpoznawania nazw maszyny Wirtualnej na platformie Azure
W tym artykule przedstawiono sposób ustawić statyczny wewnętrznej nazwy DNS dla maszyn wirtualnych systemu Linux przy użyciu nazwy etykiety DNS i karty interfejsu sieci wirtualnej (vNics) 2.0 interfejsu wiersza polecenia platformy Azure. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Statyczne nazwy DNS są używane dla usług trwałych infrastruktury, takich jak serwer kompilacji Wpięć, który służy do tego dokumentu lub serwer Git.

Wymagania są następujące:

* [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli chcesz szybko wykonywać zadania poniższej sekcji Szczegóły poleceń potrzebne. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, [uruchamiania tutaj](#detailed-walkthrough). Aby wykonać te kroki, należy najnowszej [Azure CLI 2.0](/cli/azure/install-az-cli2) zainstalowane i zalogowany do konta platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login).

Wymagania Wstępne: Grupy zasobów, sieć wirtualną i podsieć, grupy zabezpieczeń sieci przy użyciu protokołu SSH ruchu przychodzącego.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Utwórz karty interfejsu sieci wirtualnej o nazwie DNS wewnętrzny statycznej
Utwórz vNic z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create). `--internal-dns-name` Flaga interfejsu wiersza polecenia jest do ustawiania etykiety DNS, który zawiera nazwę DNS statycznych dla wirtualnej karty sieciowej (vNic). Poniższy przykład tworzy vNic, o nazwie `myNic`, łączy się `myVnet` sieci wirtualnej i tworzy wewnętrzny rekordu nazwy DNS nazywanego `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Wdróż Maszynę wirtualną i połączyć vNic
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). `--nics` Flagi vNic łączy się z maszyny Wirtualnej podczas wdrażania na platformie Azure. Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` z dyskami zarządzane Azure i dołącza o nazwie vNic `myNic` w poprzednim kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Pełne ciągłej integracji i ciągłe wdrażanie (CiCd) infrastruktury na platformie Azure wymaga niektórych serwerów serwery statyczne lub długotrwałe. Zaleca się, że Azure zasoby, takie jak sieci wirtualnych i grup zabezpieczeń sieci są statyczne i tam długo zasoby, które rzadko są wdrożone. Po wdrożeniu sieci wirtualnej można użyć ponownie przez nowych wdrożeń bez żadnych niekorzystny wpływ infrastruktury. Później można dodać serwer repozytorium Git lub serwer automatyzacji Wpięć dostarcza CiCd do tej sieci wirtualnej dla rozwoju lub środowisk testowych.  

Wewnętrzny nazw DNS są tylko rozpoznawany w sieci wirtualnej platformy Azure. Ponieważ nazwy DNS są wewnętrzne, nie są one rozpoznać poza internet, zapewniając dodatkowe zabezpieczenia infrastruktury.

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametru zawierają `myResourceGroup`, `myNic`, i `myVM`.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów
Najpierw należy utworzyć grupy zasobów z [Tworzenie grupy az](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie `myResourceGroup` w `westus` lokalizacji:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Utwórz sieć wirtualną

Następnym krokiem jest tworzenie sieci wirtualnej można uruchomić maszyny wirtualne do. Sieć wirtualna zawiera jedną podsieć w ramach tego przewodnika. Aby uzyskać więcej informacji o sieci wirtualnych platformy Azure, zobacz [utworzyć sieć wirtualną](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Utwórz sieć wirtualną z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` i podsieć o nazwie `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Utwórz grupę zabezpieczeń sieci
Grupy zabezpieczeń sieci platformy Azure są równoważne zapory w warstwie sieci. Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [sposób tworzenia grup NSG w interfejsu wiersza polecenia Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Utwórz grupę zabezpieczeń sieci z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Dodaj regułę ruchu przychodzącego zezwalająca na SSH
Dodaj regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń z [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Poniższy przykład tworzy reguły o nazwie `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Podsieć jest skojarzona z sieciową grupą zabezpieczeń
Aby skojarzyć podsieci z sieciową grupą zabezpieczeń, użyj [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Poniższy przykład powoduje skojarzenie nazwy podsieci `mySubnet` z sieciową grupą zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Tworzenie wirtualnej karty sieciowej i statyczne nazwy DNS
Platforma Azure jest bardzo elastyczne, ale aby użyć nazwy DNS dla rozpoznawania nazw maszyny Wirtualnej, należy utworzyć sieci wirtualnej karty interfejsu (vNics), które obejmują etykietę DNS. vNics są ważne, ponieważ użytkownik może korzystać z nich łącząc je do różnych maszyn wirtualnych z cyklem infrastruktury. Takie podejście zapewnia vNic jako zasób statycznych maszyn wirtualnych mogą być tymczasowe. Za pomocą DNS etykietowania na karcie vNic, możemy włączyć rozpoznawanie nazw prostego z innych maszyn wirtualnych w sieci wirtualnej. Przy użyciu nazwy rozpoznawalną umożliwia innych maszyn wirtualnych na dostęp do serwera automatyzacji za pomocą nazwy DNS `Jenkins` lub na serwerze Git `gitrepo`.  

Utwórz vNic z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy vNic, o nazwie `myNic`, łączy się `myVnet` sieci wirtualnej o nazwie `myVnet`i tworzy wewnętrzny rekordu nazwy DNS nazywanego `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Wdróż maszynę Wirtualną do infrastruktury sieci wirtualnej
Mamy teraz sieć wirtualna i podsieć, grupa zabezpieczeń sieci działający jako zapory do ochrony przez blokuje cały ruch przychodzący z wyjątkiem port 22 protokołu SSH i vNic naszych podsieci. Teraz można wdrożyć maszyny Wirtualnej w tym istniejącej infrastruktury sieci.

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` z dyskami zarządzane Azure i dołącza o nazwie vNic `myNic` w poprzednim kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Za pomocą flag interfejsu wiersza polecenia do wyróżnienia istniejących zasobów, poinstruuj firma Microsoft Azure, aby wdrożyć maszynę Wirtualną w istniejącej sieci. Aby przywołują, po wdrożeniu sieci wirtualnej i podsieci, możesz je zostawić jako statyczne ani stałe zasoby w Twoim regionie Azure.  

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie niestandardowego środowiska dla maszyny wirtualnej z systemem Linux poprzez bezpośrednie użycie poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz Maszynę wirtualną systemu Linux na platformie Azure za pomocą szablonów](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
