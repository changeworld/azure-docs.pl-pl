---
title: Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure z wieloma kartami sieciowymi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną systemu Linux z wieloma kartami sieciowymi, dołączono do niego przy użyciu szablonów interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 257b80c30823be41893be8659845d4fcbc922da3
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932276"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak utworzyć maszynę wirtualną systemu Linux na platformie Azure z sieci wielu kart interfejsu
Można utworzyć maszynę wirtualną (VM) na platformie Azure, która ma wiele interfejsów sieci wirtualnej (NIC) podłączone do niego. Typowym scenariuszem jest zapewnienie różnych podsieci frontonu i zaplecza łączności lub sieci z rozwiązaniem monitorowania lub tworzenia kopii zapasowych w wersji dedykowanej. Ten artykuł szczegółowo opisuje sposób tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi, dołączono do niego oraz jak dodawanie i usuwanie kart sieciowych z istniejącej maszyny Wirtualnej. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługi różną liczbę kart sieciowych, więc odpowiednio rozmiaru maszyny Wirtualnej.

Ten artykuł szczegółowo opisuje sposób tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0. 


## <a name="create-supporting-resources"></a>Utwórz zasoby pomocnicze
Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index#az_login).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione *myResourceGroup*, *mystorageaccount*, i *myVM*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Utwórz podsieć dla ruchu zaplecza przy użyciu [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Poniższy przykład tworzy podsieć o nazwie *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Utworzenie i skonfigurowanie wielu kart sieciowych
Utwórz dwie karty sieciowe z [tworzenie az sieciowego](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy dwie karty sieciowe o nazwie *myNic1* i *myNic2*, podłączone grupy zabezpieczeń sieci z jedną kartą Sieciową, nawiązywania połączenia z każdej podsieci:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Tworzenie maszyny Wirtualnej i Dołącz kart sieciowych
Podczas tworzenia maszyny Wirtualnej, określ karty interfejsu sieciowego utworzonego za pomocą `--nics`. Należy również zadbać o to jeśli wybrano rozmiar maszyny Wirtualnej. Istnieją limity całkowitą liczbę kart sieciowych, które można dodać do maszyny Wirtualnej. Przeczytaj więcej na temat [rozmiarów maszyn wirtualnych systemu Linux](sizes.md). 

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

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Dodaj kartę Sieciową do maszyny Wirtualnej
W poprzednich krokach utworzono maszynę Wirtualną z wieloma kartami sieciowymi. Karty sieciowe można również dodać do istniejącej maszyny Wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługi różną liczbę kart sieciowych, więc odpowiednio rozmiaru maszyny Wirtualnej. Jeśli to konieczne, możesz [zmienić rozmiar maszyny Wirtualnej](change-vm-size.md).

Utwórz inną kartą Sieciową z [tworzenie az sieciowego](/cli/azure/network/nic#az_network_nic_create). Poniższy przykład tworzy kartę Sieciową o nazwie *myNic3* podłączony do wewnętrznej podsieci i sieciowej grupy zabezpieczeń utworzone w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę Sieciową do istniejącej maszyny Wirtualnej, najpierw Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodawanie karty Sieciowej z [az vm nic dodawać](/cli/azure/vm/nic#az_vm_nic_add). W poniższym przykładzie dodano *myNic3* do *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Usuń kartę Sieciową z maszyny Wirtualnej
Aby usunąć kartę Sieciową z istniejącej maszyny Wirtualnej, najpierw Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń z kartą Sieciową o [Usuń az vm nic](/cli/azure/vm/nic#az_vm_nic_remove). Poniższy przykład usuwa *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Utwórz wiele kart sieciowych przy użyciu szablonów usługi Resource Manager
Szablony usługi Azure Resource Manager umożliwiają deklaratywne pliki JSON zdefiniować środowiska. Może odczytywać [przegląd możliwości usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Szablony usługi Resource Manager umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takie jak tworzenie wiele kart sieciowych. Możesz użyć *kopiowania* Aby określić liczbę wystąpień, aby utworzyć:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Przeczytaj więcej na temat [tworzenie wielu wystąpień przy użyciu *kopiowania*](../../resource-group-create-multiple.md). 

Można również użyć `copyIndex()` celu następnie dołączenia liczby do nazwy zasobu, który pozwala na tworzenie `myNic1`, `myNic2`itp. Poniżej przedstawiono przykład dodając wartość indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Pełny przykład można znaleźć [tworzenia wielu kart sieciowych przy użyciu szablonów usługi Resource Manager](../../virtual-network/template-samples.md).

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych
Po dodaniu wielu kart sieciowych do maszyny Wirtualnej z systemem Linux, musisz utworzyć reguły routingu. Te reguły zezwalają na maszynę Wirtualną do wysyłania i odbierania ruchu, który należy do określonej karty sieciowej. W przeciwnym razie ruch, który należy do *eth1*, na przykład, nie może przetworzyć poprawnie trasy zdefiniowanej wartości domyślnej.

Aby rozwiązać ten problem z routingiem, należy najpierw dodać dwie tabele routingu, aby */etc/iproute2/rt_tables* w następujący sposób:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Aby wprowadzić zmianę, trwałe i stosowane podczas aktywacji stos sieci, należy edytować */etc/sysconfig/network-scripts/ifcfg-eth0* i */etc/sysconfig/network-scripts/ifcfg-eth1*. Instrukcja ALTER wiersz *"NM_CONTROLLED = yes"* do *"NM_CONTROLLED = nie"*. Bez tego kroku dodatkowe reguły/routingu nie są automatycznie stosowane.
 
Następnie rozszerzenie tabele routingu. Załóżmy, że istnieje w miejscu następujące ustawienia:

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

Następnie będzie Utwórz następujące pliki i dodaj odpowiednie zasady i tras do poszczególnych:

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

Reguły routingu są teraz prawidłowo w miejscu, i możesz połączyć się z dowolnym interfejsem, zgodnie z potrzebami.


## <a name="next-steps"></a>Kolejne kroki
Przegląd [rozmiarów maszyn wirtualnych systemu Linux](sizes.md) podczas próby tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi. Należy zwrócić uwagę maksymalna liczba kart sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej. 
