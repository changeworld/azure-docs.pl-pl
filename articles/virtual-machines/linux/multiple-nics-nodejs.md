---
title: Utwórz Maszynę wirtualną systemu Linux na platformie Azure z wieloma kartami sieciowymi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną systemu Linux z wieloma kartami sieciowymi, dołączone do niego przy użyciu wiersza polecenia platformy Azure lub usługi Resource Manager szablonów.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 20e3a65c28e95849822d81076b6780e05a2aebbf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Utwórz maszynę wirtualną systemu Linux z wieloma kartami sieciowymi przy użyciu 1.0 interfejsu wiersza polecenia platformy Azure
Można utworzyć maszynę wirtualną (VM) na platformie Azure, który ma wiele interfejsów sieci wirtualnej (NIC) do niego dołączony. Typowy scenariusz ma różne podsieci dla łączności frontonu i zaplecza lub sieć przeznaczona do monitorowania lub kopii zapasowej rozwiązanie. W tym artykule przedstawiono szybki poleceń, aby utworzyć Maszynę wirtualną z wieloma kartami sieciowymi, do niego dołączony. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługuje różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny Wirtualnej.

> [!WARNING]
> Po utworzeniu maszyny Wirtualnej — kart sieciowych nie można dodać do istniejącej maszyny Wirtualnej z interfejsu wiersza polecenia platformy Azure w wersji 1.0 należy dołączyć wiele kart sieciowych. Możesz [Dodawanie kart sieciowych do istniejącej maszyny Wirtualnej Azure CLI 2.0](multiple-nics.md). Możesz również [Utwórz maszynę Wirtualną, oparty na oryginalnym dyskach wirtualnych](copy-vm.md) i tworzenia wielu kart sieciowych podczas wdrażania maszyny Wirtualnej.


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#create-supporting-resources) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](multiple-nics.md) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="create-supporting-resources"></a>Utwórz dodatkowe zasoby
Upewnij się, że masz [interfejsu wiersza polecenia Azure](../../cli-install-nodejs.md) zalogowany i w trybie Menedżera zasobów:

```azurecli
azure config mode arm
```

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *mojekontomagazynu*, i *myVM*.

Najpierw utwórz grupę zasobów. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
azure group create myResourceGroup --location eastus
```

Utwórz konto magazynu do przechowywania maszyn wirtualnych. Poniższy przykład tworzy konto magazynu o nazwie *mojekontomagazynu*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Tworzenie sieci wirtualnej do maszyn wirtualnych do połączenia. Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* z prefiksem adresu o *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Utwórz dwie sieci wirtualnej podsieci — po jednej dla ruchu frontonu i jedną dla ruchu zaplecza. Poniższy przykład tworzy dwie podsieci o nazwie *mySubnetFrontEnd* i *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Tworzenie i konfigurowanie wielu kart sieciowych
Więcej szczegółów zawiera temat [wdrażania wielu kart sieciowych przy użyciu interfejsu wiersza polecenia Azure](../../virtual-machines/linux/multiple-nics.md), tym skrypty proces w pętli do utworzenia w kartach sieciowych.

Poniższy przykład tworzy dwie karty sieciowe, o nazwie *myNic1* i *myNic2*, z jedną kartą Sieciową nawiązywania połączenia z każdej podsieci:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Zwykle również utworzyć [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-networks-nsg.md) lub [modułu równoważenia obciążenia](../../load-balancer/load-balancer-overview.md) ułatwia zarządzanie i rozpowszechniają ruch maszyn wirtualnych. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Powiąż z kart sieciowych do grupy zabezpieczeń sieci przy użyciu `azure network nic set`. Poniższy przykład wiąże *myNic1* i *myNic2* z *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Utwórz Maszynę wirtualną i Dołącz karty interfejsu sieciowego
Podczas tworzenia maszyny Wirtualnej, teraz należy określić wiele kart sieciowych. Zamiast przy użyciu `--nic-name` zapewnienie jednej karty Sieciowej, zamiast tego należy użyć `--nic-names` i zawierają rozdzielaną przecinkami listę kart sieciowych. Należy również zwrócić uwagę, gdy wybierz rozmiar maszyny Wirtualnej. Brak ograniczeń dla łącznej liczby kart sieciowych, które można dodać do maszyny Wirtualnej. Przeczytaj więcej na temat [rozmiarów maszyn wirtualnych systemu Linux](sizes.md). Poniższy przykład przedstawia sposób określić wiele kart sieciowych, a następnie rozmiar maszyny Wirtualnej, który obsługuje korzystanie z wielu kart sieciowych (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

Podczas dodawania wielu kart sieciowych do maszyny Wirtualnej systemu Linux, należy utworzyć reguły routingu. Te reguły zezwalają na maszynie Wirtualnej na wysyłanie i odbieranie ruchu, który należy do określonej karty sieciowej. W przeciwnym razie ruchu, który należy do eth1, na przykład nie może przetworzyć poprawnie trasy zdefiniowanej wartości domyślnej. Aby rozwiązać ten problem routingu, zobacz [Konfiguruj system operacyjny gościa dla wielu kart sieciowych](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Tworzenie wielu kart sieciowych przy użyciu szablonów usługi Resource Manager
Szablony usługi Azure Resource Manager umożliwia definiowanie środowiska deklaratywne pliki w formacie JSON. Możesz przeczytać [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Szablony Menedżera zasobów umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu kart sieciowych. Możesz użyć *kopiowania* umożliwia określenie liczby wystąpień, aby utworzyć:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Przeczytaj więcej na temat [tworzenia wielu wystąpień przy użyciu *kopiowania*](../../resource-group-create-multiple.md). 

Można również użyć `copyIndex()` następnie dołączyć numer na nazwę zasobu, co pozwala na tworzenie `myNic1`, `myNic2`itp. Poniżej przedstawiono przykład dołączanie wartość indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pełny przykład można znaleźć [tworzenia wielu kart sieciowych przy użyciu szablonów usługi Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

Podczas dodawania wielu kart sieciowych do maszyny Wirtualnej systemu Linux, należy utworzyć reguły routingu. Te reguły zezwalają na maszynie Wirtualnej na wysyłanie i odbieranie ruchu, który należy do określonej karty sieciowej. W przeciwnym razie ruchu, który należy do eth1, na przykład nie może przetworzyć poprawnie trasy zdefiniowanej wartości domyślnej. Aby rozwiązać ten problem routingu, zobacz [Konfiguruj system operacyjny gościa dla wielu kart sieciowych](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="next-steps"></a>Kolejne kroki
Upewnij się przejrzeć [rozmiarów maszyn wirtualnych systemu Linux](sizes.md) podczas tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi. Należy zwrócić uwagę na maksymalną liczbę kart sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej. 

Należy pamiętać, że nie można dodać dodatkowe karty sieciowe do istniejącej maszyny Wirtualnej, należy utworzyć wszystkich kart sieciowych podczas wdrażania maszyny Wirtualnej. Zachowaj ostrożność podczas planowania wdrożeń w taki sposób, aby upewnić się, czy istnieje połączenie sieciowe wymagane od samego początku.

