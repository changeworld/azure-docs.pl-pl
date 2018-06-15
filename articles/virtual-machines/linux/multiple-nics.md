---
title: Utwórz Maszynę wirtualną systemu Linux na platformie Azure z wieloma kartami sieciowymi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną systemu Linux z wieloma kartami sieciowymi, dołączone do niego przy użyciu szablonów usługi Azure CLI w wersji 2.0 lub Menedżera zasobów.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 1968222940888c1e5399e257a9694d47adce2e45
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525186"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Tworzenie maszyny wirtualnej systemu Linux na platformie Azure z sieci wielu kart interfejsu
Można utworzyć maszynę wirtualną (VM) na platformie Azure, który ma wiele interfejsów sieci wirtualnej (NIC) do niego dołączony. Typowy scenariusz ma różne podsieci dla łączności frontonu i zaplecza lub sieć przeznaczona do monitorowania lub kopii zapasowej rozwiązanie. Ten artykuł zawiera szczegóły dotyczące sposobu tworzenia maszyn wirtualnych z wieloma kartami sieciowymi, do niego dołączony oraz dodawanie i usuwanie kart sieciowych z istniejącej maszyny Wirtualnej. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługuje różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny Wirtualnej.

Ten artykuł zawiera szczegóły dotyczące sposobu tworzenia maszyn wirtualnych z wieloma kartami sieciowymi 2.0 interfejsu wiersza polecenia platformy Azure. Czynności te można również wykonać przy użyciu [interfejsu wiersza polecenia platformy Azure w wersji 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Utwórz dodatkowe zasoby
Zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login).

W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *mojekontomagazynu*, i *myVM*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną z [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Utwórz podsieć dla ruchu zaplecza z [Utwórz podsieć sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Poniższy przykład tworzy podsieć o nazwie *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Utwórz grupę zabezpieczeń sieci z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Tworzenie i konfigurowanie wielu kart sieciowych
Utwórz dwie karty sieciowe z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy dwie karty sieciowe, o nazwie *myNic1* i *myNic2*, podłączone grupy zabezpieczeń sieci, z jedną kartą Sieciową nawiązywania połączenia z każdej podsieci:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Utwórz Maszynę wirtualną i Dołącz karty interfejsu sieciowego
Podczas tworzenia maszyny Wirtualnej, określ karty interfejsu sieciowego zostały utworzone z `--nics`. Należy również zwrócić uwagę, gdy wybierz rozmiar maszyny Wirtualnej. Brak ograniczeń dla łącznej liczby kart sieciowych, które można dodać do maszyny Wirtualnej. Przeczytaj więcej na temat [rozmiarów maszyn wirtualnych systemu Linux](sizes.md). 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Dodawanie tabel routingu z systemem operacyjnym gościa, wykonując kroki opisane w [skonfigurować system operacyjny gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Dodać kartę Sieciową do maszyny Wirtualnej
Poprzednie kroki utworzyć Maszynę wirtualną z wieloma kartami sieciowymi. Karty sieciowe mogą również dodać do istniejącej maszyny Wirtualnej z 2.0 interfejsu wiersza polecenia platformy Azure. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługuje różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny Wirtualnej. W razie potrzeby można [Zmień rozmiar maszyny Wirtualnej](change-vm-size.md).

Utwórz inną kartą Sieciową o [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy karty Sieciowej o nazwie *myNic3* podłączone do zaplecza podsieci i sieci grupą zabezpieczeń utworzoną w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę Sieciową do istniejącej maszyny Wirtualnej, najpierw cofnąć maszyny Wirtualnej z [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodaj kartę Sieciową z [Dodaj kartę sieciową maszyny wirtualnej az](/cli/azure/vm/nic#az_vm_nic_add). W poniższym przykładzie dodano *myNic3* do *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną z [uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodawanie tabel routingu z systemem operacyjnym gościa, wykonując kroki opisane w [skonfigurować system operacyjny gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Usuń kartę Sieciową z maszyny Wirtualnej
Aby usunąć karty Sieciowej z istniejącej maszyny Wirtualnej, najpierw cofnąć maszyny Wirtualnej z [deallocate wirtualna az](/cli/azure/vm#az_vm_deallocate). Poniższy przykład cofa alokację maszyny Wirtualnej o nazwie *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń z kartą Sieciową o [Usuń kartę sieciową maszyny wirtualnej az](/cli/azure/vm/nic#az_vm_nic_remove). Poniższy przykład umożliwia usunięcie *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną z [uruchomienia maszyny wirtualnej az](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


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

Pełny przykład można znaleźć [tworzenia wielu kart sieciowych przy użyciu szablonów usługi Resource Manager](../../virtual-network/template-samples.md).

Dodawanie tabel routingu z systemem operacyjnym gościa, wykonując kroki opisane w [skonfigurować system operacyjny gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Skonfiguruj system operacyjny gościa dla wielu kart sieciowych
Podczas dodawania wielu kart sieciowych do maszyny Wirtualnej systemu Linux, należy utworzyć reguły routingu. Te reguły zezwalają na maszynie Wirtualnej na wysyłanie i odbieranie ruchu, który należy do określonej karty sieciowej. W przeciwnym razie ruchu, który należy do *eth1*, na przykład nie może przetworzyć poprawnie trasy zdefiniowanej wartości domyślnej.

Aby rozwiązać ten problem routingu, należy najpierw dodać dwie tabele routingu, aby */etc/iproute2/rt_tables* w następujący sposób:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Aby wprowadzić zmiany, stałe i stosowane podczas aktywacji stosu sieciowego, należy edytować */etc/sysconfig/network-scripts/ifcfg-eth0* i */etc/sysconfig/network-scripts/ifcfg-eth1*. ALTER wiersza *"NM_CONTROLLED = yes"* do *"NM_CONTROLLED = nie"*. Bez tego kroku dodatkowe reguły/routingu nie są automatycznie stosowane.
 
Następnie należy rozszerzyć tabele routingu. Załóżmy, że mamy następujące ustawienia w miejscu:

*Routing*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Interfejsy*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Czy następnie utwórz następujące pliki i dodaj odpowiednie zasady i trasy do każdego:

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/Network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/Network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Aby zastosować zmiany, uruchom ponownie *sieci* usługi w następujący sposób:

```bash
systemctl restart network
```

Reguły routingu są teraz prawidłowo w miejscu i można się połączyć z interfejsem albo zgodnie z potrzebami.


## <a name="next-steps"></a>Kolejne kroki
Przegląd [rozmiarów maszyn wirtualnych systemu Linux](sizes.md) podczas tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi. Należy zwrócić uwagę na maksymalną liczbę kart sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej. 
