---
title: Otwieranie portów dla maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy do maszyny Wirtualnej systemu Linux przy użyciu modelu wdrażania usługi Azure resource manager i interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a12952c73863d10c4fffd013ab594a83ab1b6433
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771536"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Otwórz porty i punkty końcowe do maszyny Wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure

Otwieranie portu lub utworzenie punktu końcowego, na maszynę wirtualną (VM) na platformie Azure przez utworzenie filtru sieciowego w podsieci lub interfejsu sieciowego maszyny Wirtualnej. Te filtry, które kontroli ruchu przychodzącego i wychodzącego, możesz umieścić na sieciowej grupy zabezpieczeń, który jest dołączony do zasobu, który odbiera ruch. Użyjmy typowym przykładem ruchu w sieci web na porcie 80. W tym artykule pokazano, jak otworzyć port do maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure. 


Aby utworzyć sieciową grupę zabezpieczeń i reguł, potrzebujesz najnowszej [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowane i zalogować się do konta platformy Azure przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru zawierają *myResourceGroup*, *myNetworkSecurityGroup*, i *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Szybko otwarcie portu dla maszyny Wirtualnej
Jeśli potrzebujesz szybkiego otwierania portu dla maszyny Wirtualnej w przypadku tworzenia i testowania, możesz użyć [az vm open-port](/cli/azure/vm) polecenia. To polecenie tworzy sieciową grupę zabezpieczeń, dodaje regułę i stosuje je do maszyny Wirtualnej lub podsieci. W poniższym przykładzie otwierany portu *80* na maszynie Wirtualnej o nazwie *myVM* w grupie zasobów o nazwie *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Aby uzyskać większą kontrolę nad reguł, takich jak definiowanie źródłowy zakres adresów IP Kontynuuj dodatkowe kroki w tym artykule.


## <a name="create-a-network-security-group-and-rules"></a>Utwórz sieciową grupę zabezpieczeń i reguł
Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup* w *eastus* lokalizacji:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Dodaj regułę o [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule) zezwalają na ruch HTTP, aby na serwerze sieci Web (lub dostosować do własnego scenariusza, takiego jak łącznością SSH dostępu lub bazy danych). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule* zezwalająca na ruch TCP na porcie 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Zastosować sieciową grupę zabezpieczeń z maszyną Wirtualną
Kojarzenie sieciowej grupy zabezpieczeń z interfejsem sieciowym maszyny Wirtualnej (NIC) przy użyciu [aktualizacji karty sieciowej sieci az](/cli/azure/network/nic). Poniższy przykład kojarzy istniejącej karty NIC o nazwie *myNic* z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternatywnie można skojarzyć sieciową grupę zabezpieczeń z podsiecią sieci wirtualnej za pomocą [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet) , a nie tylko do interfejsu sieciowego na jednej maszynie Wirtualnej. Poniższy przykład kojarzy istniejącą podsieć o nazwie *mySubnet* w *myVnet* sieci wirtualnej z sieciową grupą zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat sieciowych grup zabezpieczeń
Szybkie polecenia w tym miejscu umożliwiają rozpoczęcie pracy z ruchem przepływać do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele świetnych funkcji i stopień szczegółowości do kontrolowania dostępu do zasobów. Przeczytaj więcej o [tworzenie sieciowej grupy zabezpieczeń i listy kontroli dostępu reguły tutaj](tutorial-virtual-network.md#secure-network-traffic).

W przypadku aplikacji sieci web o wysokiej dostępności należy umieszczać maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która pozwala na filtrowanie ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [sposób ładowania równoważenia maszyn wirtualnych systemu Linux na platformie Azure do utworzenia aplikacji o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono prosta Reguła zezwalająca na ruch HTTP. Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)
