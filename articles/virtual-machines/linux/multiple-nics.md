---
title: Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure z wieloma kartami sieciowymi | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Maszynę wirtualną systemu Linux z wieloma kartami sieciowymi, dołączono do niego przy użyciu szablonów interfejsu wiersza polecenia platformy Azure lub usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: 04aaa1da304657ac3cc305b8939ac4fcce126145
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671181"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak utworzyć maszynę wirtualną systemu Linux na platformie Azure z sieci wielu kart interfejsu


Ten artykuł szczegółowo opisuje sposób tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi przy użyciu wiersza polecenia platformy Azure.

## <a name="create-supporting-resources"></a>Utwórz zasoby pomocnicze
Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index).

W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione *myResourceGroup*, *mystorageaccount*, i *myVM*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną przy użyciu [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieć o nazwie *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Utwórz podsieć dla ruchu zaplecza przy użyciu [az podsieci sieci wirtualnej Utwórz](/cli/azure/network/vnet/subnet). Poniższy przykład tworzy podsieć o nazwie *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Utworzenie i skonfigurowanie wielu kart sieciowych
Utwórz dwie karty sieciowe z [tworzenie az sieciowego](/cli/azure/network/nic). Poniższy przykład tworzy dwie karty sieciowe o nazwie *myNic1* i *myNic2*, podłączone grupy zabezpieczeń sieci z jedną kartą Sieciową, nawiązywania połączenia z każdej podsieci:

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

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*:

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

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Dodaj kartę Sieciową do maszyny Wirtualnej
W poprzednich krokach utworzono maszynę Wirtualną z wieloma kartami sieciowymi. Karty sieciowe można również dodać do istniejącej maszyny Wirtualnej przy użyciu wiersza polecenia platformy Azure. Różne [rozmiarów maszyn wirtualnych](sizes.md) obsługi różną liczbę kart sieciowych, więc odpowiednio rozmiaru maszyny Wirtualnej. Jeśli to konieczne, możesz [zmienić rozmiar maszyny Wirtualnej](change-vm-size.md).

Utwórz inną kartą Sieciową z [tworzenie az sieciowego](/cli/azure/network/nic). Poniższy przykład tworzy kartę Sieciową o nazwie *myNic3* podłączony do wewnętrznej podsieci i sieciowej grupy zabezpieczeń utworzone w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę Sieciową do istniejącej maszyny Wirtualnej, najpierw Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodawanie karty Sieciowej z [az vm nic dodawać](/cli/azure/vm/nic). W poniższym przykładzie dodano *myNic3* do *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Usuń kartę Sieciową z maszyny Wirtualnej
Aby usunąć kartę Sieciową z istniejącej maszyny Wirtualnej, najpierw Cofnij Przydział maszyny Wirtualnej przy użyciu [az vm deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny Wirtualnej o nazwie *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń z kartą Sieciową o [Usuń az vm nic](/cli/azure/vm/nic). Poniższy przykład usuwa *myNic3* z *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę Wirtualną za pomocą [az vm start](/cli/azure/vm):

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

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

Poprzednie kroki tworzenia sieci wirtualnej i podsieci, kart sieciowych dołączonych, a następnie utworzono maszynę Wirtualną. Publicznego adresu IP adres i sieci reguły grupy zabezpieczeń, które zezwalają na ruch SSH nie zostały utworzone. Aby skonfigurować system operacyjny gościa do wielu kart sieciowych, musisz zezwolić na połączenia zdalne, a następnie uruchom polecenia lokalnie na maszynie Wirtualnej.

Aby zezwolić na ruch SSH, należy utworzyć reguły sieciowej grupy zabezpieczeń z [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) w następujący sposób:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Tworzenie publicznego adresu IP za pomocą [tworzenie sieci az public-ip](/cli/azure/network/public-ip#az-network-public-ip-create) i przypisz je do pierwszej karty Sieciowej z [az sieci nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Aby wyświetlić publiczny adres IP maszyny wirtualnej, użyj [az vm show](/cli/azure/vm#az-vm-show) w następujący sposób:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teraz SSH z publicznego adresu IP maszyny Wirtualnej. Domyślna nazwa użytkownika podana w poprzednim kroku zostało *azureuser*. Podaj swoją nazwę użytkownika i publiczny adres IP:

```bash
ssh azureuser@137.117.58.232
```

Aby wysłać do lub z pomocniczego interfejsu sieciowego, należy ręcznie dodać trasy stałe do systemu operacyjnego w każdym dodatkowy interfejs sieciowy. W tym artykule *eth1* pomocniczy interfejs. Instrukcje dotyczące dodawania trasy stałe systemowi operacyjnemu zależą od dystrybucji. W tym temacie dokumentacji dla Twojej dystrybucji, aby uzyskać instrukcje.

Podczas dodawania trasy do systemu operacyjnego, adres bramy jest *.1* dla jednego z tych podsieci jest interfejs sieciowy. Na przykład, jeśli interfejs sieciowy zostanie przypisany adres *10.0.2.4*, bramy należy określić trasa jest *10.0.2.1*. Można zdefiniować sieci określonego dla trasy, lub określ lokalizację docelową z *0.0.0.0*, jeśli chcesz, aby cały ruch dla interfejsu przechodzić przez określony bramy. Brama dla każdej podsieci odbywa się przy użyciu sieci wirtualnej.

Po dodaniu trasę dla interfejsu dodatkowej, sprawdź, czy trasy w tabeli tras z `route -n`. Następujące przykładowe dane wyjściowe jest tabelę tras, która ma interfejsów sieciowych dwóch, dodane do maszyny Wirtualnej, w tym artykule:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Upewnij się, że trasy, którą dodałeś będzie nadal występować między ponownymi uruchomieniami, sprawdzając tabeli tras ponownie po ponownym uruchomieniu. Aby przetestować łączność, można wpisać następujące polecenie, na przykład, gdzie *eth1* nazywa się dodatkowy interfejs sieciowy:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Kolejne kroki
Przegląd [rozmiarów maszyn wirtualnych systemu Linux](sizes.md) podczas próby tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi. Należy zwrócić uwagę maksymalna liczba kart sieciowych obsługuje każdego rozmiaru maszyny Wirtualnej.

Aby dodatkowo zabezpieczyć maszyn wirtualnych, należy użyć dostęp dokładnie na czas maszyny Wirtualnej. Ta funkcja spowoduje otwarcie reguły sieciowej grupy zabezpieczeń dla ruchu protokołu SSH w razie i zdefiniowany okres. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).
