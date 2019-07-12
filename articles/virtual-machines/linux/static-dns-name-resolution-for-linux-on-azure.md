---
title: Użycie wewnętrznego serwera DNS do rozpoznawania nazw maszyn wirtualnych przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Jak utworzyć sieć wirtualną kart interfejsu i użycie wewnętrznego serwera DNS do rozpoznawania nazw maszyn wirtualnych na platformie Azure przy użyciu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: gwallace
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
ms.openlocfilehash: c180c129e4e2c434cffe2ea2ca823904e8faae89
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708698"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Tworzenie karty interfejsu sieci wirtualnej i używanie wewnętrznego serwera DNS dla rozpoznawania nazw maszyn wirtualnych na platformie Azure

W tym artykule pokazano, jak skonfigurować statyczne wewnętrzne nazwy DNS dla maszyn wirtualnych systemu Linux przy użyciu karty interfejsu sieci wirtualnej (vNics) i nazw etykiet DNS przy użyciu wiersza polecenia platformy Azure. Statyczne nazwy DNS są używane dla usług infrastruktury stałych, takich jak serwer kompilacji usługi Jenkins, który jest używany dla tego dokumentu lub serwerze usługi Git.

Wymagania są następujące:

* [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Szybkie polecenia
Jeśli musisz szybko wykonywać zadania w poniższej sekcji przedstawiono polecenia potrzebne. Więcej szczegółowych informacji i kontekst dla każdego kroku można znaleźć w pozostałej części dokumentu, [uruchamianie tutaj](#detailed-walkthrough). Aby wykonać te kroki, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

Wymagania wstępne: Grupa zasobów, sieci wirtualnej i podsieci i sieciowej grupy zabezpieczeń przy użyciu protokołu SSH dla ruchu przychodzącego.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Utwórz wirtualną kartę sieciową z statyczne wewnętrzne nazwy DNS
Tworzenie wirtualnej karty sieciowej z [tworzenie az sieciowego](/cli/azure/network/nic). `--internal-dns-name` Flaga interfejsu wiersza polecenia jest ustawienie etykiety DNS, który zawiera nazwę DNS statycznych dla wirtualnej karty sieciowej (vNic). Poniższy przykład tworzy wirtualną kartę sieciową o nazwie `myNic`, łączy się z `myVnet` sieci wirtualnej i tworzy wewnętrzny rekord nazwy DNS o nazwie `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Wdrażanie maszyny Wirtualnej i połączenia wirtualnej karty sieciowej
Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). `--nics` Flagi nawiązanie połączenia z wirtualnej karty sieciowej maszyny Wirtualnej podczas wdrażania na platformie Azure. Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` za pomocą usługi Azure Managed Disks i dołącza wirtualnej karty sieciowej o nazwie `myNic` z poprzedniego kroku:

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

Ciągłej integracji i ciągłego wdrażania (CiCd) infrastruktury na platformie Azure wymaga określonych serwerów jako statyczne lub długotrwałe serwerów. Zalecane jest, że zasoby systemu Azure, takich jak sieci wirtualne i sieciowe grupy zabezpieczeń są statyczne i długo znajdowały się rzadko wdrożonych zasobów. Po wdrożeniu sieci wirtualnej może zostać użyte przez nowych wdrożeń bez żadnych negatywnie wpływa na infrastruktury. Możesz później dodać serwer repozytorium Git lub serwer automatyzacji Jenkins zapewnia CiCd z tą siecią wirtualną dla rozwoju lub środowisk testowych.  

Wewnętrzne nazwy DNS są tylko do rozpoznania wewnątrz sieci wirtualnej platformy Azure. Ponieważ wewnętrzne nazwy DNS, nie są one poza Internetem, zapewnienia większego poziomu zabezpieczeń infrastruktury do rozpoznania.

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają `myResourceGroup`, `myNic`, i `myVM`.

## <a name="create-the-resource-group"></a>Tworzenie grupy zasobów
Najpierw utwórz grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w lokalizacji `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Następnym krokiem jest do tworzenia sieci wirtualnej do maszyn wirtualnych do uruchomienia. Sieć wirtualna zawiera jedną podsieć w ramach tego przewodnika. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [tworzenie sieci wirtualnej](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie `myVnet` i podsieć o nazwie `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń
Sieciowe grupy zabezpieczeń platformy Azure są równoważne do zapory w warstwie sieciowej. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [tworzenie sieciowych grup zabezpieczeń w interfejsie wiersza polecenia platformy Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Dodaj regułę ruchu przychodzącego, aby zezwolić na SSH
Dodaj regułę ruchu przychodzącego dla sieciowej grupy zabezpieczeń z [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule). Poniższy przykład tworzy regułę o nazwie `myRuleAllowSSH`:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Skojarzenia podsieci z sieciową grupą zabezpieczeń
Aby skojarzyć podsieci z sieciową grupą zabezpieczeń, należy użyć [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet). Poniższy przykład kojarzy nazwę podsieci `mySubnet` z sieciową grupą zabezpieczeń o nazwie `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Utwórz wirtualną kartę sieciową i statyczne nazwy DNS
Platforma Azure jest bardzo elastyczny, ale aby użyć nazwy DNS dla rozpoznawania nazw maszyn wirtualnych, należy utworzyć sieci wirtualnej karty interfejsu (vNics), które zawierają etykietę DNS. kart sieciowych są ważne, ponieważ użytkownik może korzystać z nich, łącząc je do różnych maszyn wirtualnych za pośrednictwem cyklu życia infrastruktury. To podejście zapewnia wirtualnej karty sieciowej jako zasób statyczny, maszyny wirtualne mogą być tymczasowe. Za pomocą DNS etykietowania w wirtualnej karty sieciowej, możemy włączyć rozpoznawanie nazw prostego z innymi maszynami wirtualnymi w sieci wirtualnej. Przy użyciu możliwej do rozpoznania nazwy temu pozostałe maszyny wirtualne na dostęp do serwera usługi automation o nazwie DNS `Jenkins` lub na serwerze usługi Git `gitrepo`.  

Tworzenie wirtualnej karty sieciowej z [tworzenie az sieciowego](/cli/azure/network/nic). Poniższy przykład tworzy wirtualną kartę sieciową o nazwie `myNic`, łączy się z `myVnet` sieć wirtualną o nazwie `myVnet`i tworzy wewnętrzny rekord nazwy DNS o nazwie `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Wdróż maszynę Wirtualną do infrastruktury sieci wirtualnej
W efekcie powstał sieci wirtualnej i podsieci i sieciowej grupy zabezpieczeń działają jako zapora chronić nasze podsieci, blokuje cały ruch przychodzący z wyjątkiem port 22 dla protokołu SSH i wirtualną kartę sieciową. Teraz można wdrożyć Maszynę wirtualną wewnątrz tego istniejącej infrastrukturze sieci.

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie `myVM` za pomocą usługi Azure Managed Disks i dołącza wirtualnej karty sieciowej o nazwie `myNic` z poprzedniego kroku:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Za pomocą flagi interfejsu wiersza polecenia do wyróżnienia istniejące zasoby, możemy poinstruować platformy Azure, aby wdrożyć maszynę Wirtualną w istniejącej sieci. Aby dobrze w przypadku, gdy wdrożono sieć wirtualną i podsieć, ich może pozostać jako statyczne lub trwałe zasobów w obrębie regionu platformy Azure.  

## <a name="next-steps"></a>Następne kroki
* [Tworzenie niestandardowego środowiska dla maszyny wirtualnej z systemem Linux poprzez bezpośrednie użycie poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu szablonów](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
