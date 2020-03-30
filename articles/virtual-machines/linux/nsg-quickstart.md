---
title: Otwieranie portów na maszynie Wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy na maszynie wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066630"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Otwieranie portów i punktów końcowych na maszynę wirtualną za pomocą interfejsu wiersza polecenia platformy Azure

Otwórz port lub utworzyć punkt końcowy na maszynie wirtualnej (VM) na platformie Azure, tworząc filtr sieciowy w interfejsie sieciowym podsieci lub maszyny Wirtualnej. Te filtry, które kontrolują ruch przychodzący i wychodzący, należy umieścić w sieciowej grupie zabezpieczeń dołączonej do zasobu odbieranego przez ruch. Użyjmy wspólnego przykładu ruchu internetowego na porcie 80. W tym artykule pokazano, jak otworzyć port na maszynie Wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure. 


Aby utworzyć grupę zabezpieczeń sieci i reguły, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów to *myResourceGroup*, *myNetworkSecurityGroup*i *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Szybkie otwieranie portu maszyny Wirtualnej
Jeśli trzeba szybko otworzyć port dla maszyny Wirtualnej w scenariuszu deweloper/test, można użyć [az vm open-port](/cli/azure/vm) polecenia. To polecenie tworzy grupę zabezpieczeń sieciowych, dodaje regułę i stosuje ją do maszyny Wirtualnej lub podsieci. Poniższy przykład otwiera port *80* na maszynie wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Aby uzyskać większą kontrolę nad regułami, takimi jak definiowanie źródłowego zakresu adresów IP, kontynuuj dodatkowe kroki opisane w tym artykule.


## <a name="create-a-network-security-group-and-rules"></a>Tworzenie sieciowej grupy zabezpieczeń i reguł
Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy grupę zabezpieczeń sieci o nazwie *myNetworkSecurityGroup* w lokalizacji *eastus:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Dodaj regułę z [regułą nsg sieci az,](/cli/azure/network/nsg/rule) aby zezwolić na ruch HTTP do serwera www (lub dostosować do własnego scenariusza, takiego jak dostęp SSH lub łączność bazy danych). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule,* aby zezwolić na ruch TCP na porcie 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Stosowanie sieciowej grupy zabezpieczeń do maszyny Wirtualnej
Skojarz grupę zabezpieczeń sieci z interfejsem sieciowym maszyny Wirtualnej z [aktualizacją sieciowej az .](/cli/azure/network/nic) Poniższy przykład kojarzy istniejącą kartę sieciową o nazwie *myNic* z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup:*

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternatywnie można skojarzyć grupę zabezpieczeń sieci z podsiecią sieci wirtualnej z [aktualizacją podsieci sieci az,](/cli/azure/network/vnet/subnet) a nie tylko z interfejsem sieciowym na jednej maszynie wirtualnej. Poniższy przykład kojarzy istniejącą podsieć o nazwie *mySubnet* w sieci wirtualnej *myVnet* z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup:*

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat grup zabezpieczeń sieci
Szybkie polecenia w tym miejscu pozwalają rozpocząć i działać z ruchem płynącym do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele wspaniałych funkcji i szczegółowość do kontrolowania dostępu do zasobów. Więcej informacji na temat [tworzenia sieciowej grupy zabezpieczeń i reguł listy ACL](tutorial-virtual-network.md#secure-network-traffic)można znaleźć tutaj .

W przypadku aplikacji sieci Web o wysokiej dostępności należy umieścić maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia rozdziela ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [Jak załadować równoważenie maszyn wirtualnych systemu Linux na platformie Azure, aby utworzyć aplikację o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Następne kroki
W tym przykładzie utworzono prostą regułę zezwalania na ruch HTTP. Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)
