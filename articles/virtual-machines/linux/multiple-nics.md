---
title: Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu wielu kart sieciowych
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux z wieloma kartami sieciowymi dołączonymi do niej przy użyciu szablonów interfejsu wiersza polecenia platformy Azure lub menedżera zasobów.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267185"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Jak utworzyć maszynę wirtualną systemu Linux na platformie Azure z wieloma kartami interfejsu sieciowego


W tym artykule opisano sposób tworzenia maszyny Wirtualnej z wieloma kartami sieciowymi za pomocą interfejsu wiersza polecenia platformy Azure.

## <a name="create-supporting-resources"></a>Tworzenie zasobów pomocniczych
Zainstaluj najnowszą [platformę Cli platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmowały *myResourceGroup*, *mystorageaccount*i *myVM*.

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Utwórz sieć wirtualną za pomocą [sieci wirtualnej AZ create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* i podsieci o nazwie *mySubnetFrontEnd:*

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Utwórz podsieć dla ruchu zaplecza za pomocą [sieciowej az vnet utworzyć](/cli/azure/network/vnet/subnet). Poniższy przykład tworzy podsieć o nazwie *mySubnetBackEnd:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Tworzenie i konfigurowanie wielu kart sieciowych
Utwórz dwie karty sieciowe z [tworzeniem sieciowej az.](/cli/azure/network/nic) Poniższy przykład tworzy dwie karty sieciowe o nazwie *myNic1* i *myNic2*, połączone z sieciową grupą zabezpieczeń, z jedną kartą sieciową łączącą się z każdą podsiecią:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Tworzenie maszyny wirtualnej i dołączanie kart sieciowych
Podczas tworzenia maszyny Wirtualnej określ karty `--nics`sieciowe utworzone za pomocą pliku . Należy również zwrócić należy zwrócić szczególną ostrożność podczas wybierania rozmiaru maszyny Wirtualnej. Istnieją limity dla całkowitej liczby kart sieciowych, które można dodać do maszyny Wirtualnej. Dowiedz się więcej o [rozmiarach maszyn wirtualnych z systemem Linux](sizes.md).

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM:*

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

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych.](#configure-guest-os-for-multiple-nics)

## <a name="add-a-nic-to-a-vm"></a>Dodawanie karty sieciowej do maszyny Wirtualnej
W poprzednich krokach utworzono maszynę wirtualną z wieloma kartami sieciowym. Można również dodać karty sieciowe do istniejącej maszyny Wirtualnej za pomocą interfejsu wiersza polecenia platformy Azure. Różne [rozmiary maszyn wirtualnych](sizes.md) obsługują różną liczbę kart sieciowych, więc odpowiednio rozmiar maszyny wirtualnej. W razie potrzeby można [zmienić rozmiar maszyny Wirtualnej](change-vm-size.md).

Utwórz kolejną kartę sieciową z [tworzeniem sieci AZ .](/cli/azure/network/nic) Poniższy przykład tworzy kartę sieciową o nazwie *myNic3* połączoną z podsiecią zaplecza i sieciową grupą zabezpieczeń utworzoną w poprzednich krokach:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Aby dodać kartę sieciową do istniejącej maszyny Wirtualnej, najpierw zdeokuj [maszynę wirtualną za pomocą az vm deallocate](/cli/azure/vm). W poniższym przykładzie przydziela maszynę wirtualną o nazwie *myVM:*


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Dodaj kartę sieciową z [dodatkiem az vm nic .](/cli/azure/vm/nic) W poniższym przykładzie dodaje *myNic3* do *myVM:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę [wirtualną z az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych.](#configure-guest-os-for-multiple-nics)

## <a name="remove-a-nic-from-a-vm"></a>Usuwanie karty sieciowej z maszyny Wirtualnej
Aby usunąć kartę sieciową z istniejącej maszyny Wirtualnej, najpierw zdewastuj alokację maszyny wirtualnej za pomocą [az vm deallocate](/cli/azure/vm). W poniższym przykładzie przydziela maszynę wirtualną o nazwie *myVM:*

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Usuń kartę sieciową z [az vm nic usunąć](/cli/azure/vm/nic). Poniższy przykład usuwa *myNic3* z *myVM:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Uruchom maszynę [wirtualną z az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Tworzenie wielu kart sieciowych przy użyciu szablonów Menedżera zasobów
Szablony usługi Azure Resource Manager używają deklaratywnych plików JSON do definiowania środowiska. Możesz przeczytać [omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). Szablony Menedżera zasobów umożliwiają tworzenie wielu wystąpień zasobu podczas wdrażania, takich jak tworzenie wielu kart sieciowych. Użyj *kopii,* aby określić liczbę wystąpień do utworzenia:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Dowiedz się więcej o [tworzeniu wielu wystąpień przy użyciu *programu Copy*](../../resource-group-create-multiple.md). 

Można również użyć `copyIndex()` a, aby następnie dołączyć liczbę do nazwy `myNic1`zasobu, co pozwala na tworzenie , `myNic2`itp. Poniżej przedstawiono przykład dołączania wartości indeksu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Można przeczytać pełny przykład [tworzenia wielu kart sieciowych przy użyciu szablonów Menedżera zasobów](../../virtual-network/template-samples.md).

Dodaj tabele routingu do systemu operacyjnego gościa, wykonując kroki opisane w [temacie Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych.](#configure-guest-os-for-multiple-nics)

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurowanie systemu operacyjnego gościa dla wielu kart sieciowych

W poprzednich krokach utworzono sieć wirtualną i podsieć, dołączone karty sieciowe, a następnie utworzono maszynę wirtualną. Nie utworzono publicznego adresu IP i sieciowej grupy zabezpieczeń zezwalają na ruch SSH. Aby skonfigurować system operacyjny gościa dla wielu kart sieciowych, należy zezwolić na połączenia zdalne i uruchamiać polecenia lokalnie na maszynie wirtualnej.

Aby zezwolić na ruch SSH, utwórz regułę sieciowej grupy zabezpieczeń z [regułą nsg sieci az, należy utworzyć](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) w następujący sposób:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Tworzenie publicznego adresu IP z [siecią az public-ip utworzyć](/cli/azure/network/public-ip#az-network-public-ip-create) i przypisać go do pierwszej karty sieciowej z [az sieci nic ip-config aktualizacji:](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Aby wyświetlić publiczny adres IP maszyny Wirtualnej, użyj [az vm show](/cli/azure/vm#az-vm-show) w następujący sposób::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Teraz SSH do publicznego adresu IP maszyny Wirtualnej. Domyślną nazwą użytkownika podana w poprzednim kroku był *azureuser*. Podaj własną nazwę użytkownika i publiczny adres IP:

```bash
ssh azureuser@137.117.58.232
```

Aby wysłać do lub z pomocniczego interfejsu sieciowego, należy ręcznie dodać trwałe trasy do systemu operacyjnego dla każdego pomocniczego interfejsu sieciowego. W tym artykule *eth1* jest interfejsem pomocniczym. Instrukcje dotyczące dodawania trwałych tras do systemu operacyjnego różnią się w zależności od dystrybucji. Instrukcje dotyczące dystrybucji można znaleźć w dokumentacji dystrybucji.

Podczas dodawania trasy do systemu operacyjnego adres bramy wynosi *0,1* dla podsieci, w której znajduje się interfejs sieciowy. Na przykład, jeśli interfejsowi sieciowym jest przypisany adres *10.0.2.4,* brama określona dla trasy to *10.0.2.1*. Można zdefiniować określoną sieć dla miejsca docelowego trasy lub określić miejsce docelowe *0.0.0.0*, jeśli chcesz, aby cały ruch dla interfejsu przechodził przez określoną bramę. Brama dla każdej podsieci jest zarządzana przez sieć wirtualną.

Po dodaniu trasy dla interfejsu pomocniczego sprawdź, czy trasa znajduje `route -n`się w tabeli tras za pomocą programu . Poniższe przykładowe dane wyjściowe jest dla tabeli trasy, która ma dwa interfejsy sieciowe dodane do maszyny Wirtualnej w tym artykule:

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

Upewnij się, że dodana trasa będzie się powtarzać ponownie, sprawdzając tabelę tras ponownie po ponownym uruchomieniu komputera. Aby przetestować łączność, można wprowadzić następujące polecenie, na przykład gdzie *eth1* jest nazwą pomocniczego interfejsu sieciowego:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Następne kroki
Przejrzyj [rozmiary maszyn wirtualnych z systemem Linux](sizes.md) podczas próby utworzenia maszyny Wirtualnej z wieloma kartami sieciowymi. Należy zwrócić uwagę na maksymalną liczbę kart sieciowych, które obsługuje każdy rozmiar maszyny Wirtualnej.

Aby jeszcze bardziej zabezpieczyć maszyny wirtualne, użyj dostępu do maszyn wirtualnych w samą porę. Ta funkcja otwiera reguły sieciowej grupy zabezpieczeń dla ruchu SSH w razie potrzeby i przez określony czas. Aby uzyskać więcej informacji, zobacz [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (Zarządzanie dostępem maszyny wirtualnej przy użyciu funkcji „dokładnie na czas”).
