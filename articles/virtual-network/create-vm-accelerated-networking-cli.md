---
title: Tworzenie maszyn wirtualnych dzięki przyspieszonej sieci | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu Accelerated Networking włączone.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 8ea17e5615c0256c084b0745a392fb49f8873f99
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805367"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną systemu Linux z przyspieszonej sieci

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną systemu Linux (VM) z przyspieszonej sieci. Aby utworzyć maszyny Wirtualnej z systemem Windows przy użyciu Accelerated Networking, zobacz [tworzenie maszyny Wirtualnej z systemem Windows przy użyciu Accelerated Networking](create-vm-accelerated-networking-powershell.md). Przyspieszona sieć umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Tej ścieżki o wysokiej wydajności pomija hosta ze ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU, do użytku z najbardziej wymagających obciążeń sieci na obsługiwane typy maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z lub bez przyspieszonej łączności sieciowej:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej łączności sieciowej hosta i przełącznik wirtualny musi przechodzić przez cały ruch sieciowy do i z maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacji i innych usług z wirtualizacją sieci do ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełączniku wirtualnym](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszoną siecią ruch sieciowy dociera do maszyny wirtualnej interfejsu sieciowego (NIC) i jest następnie przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które mają zastosowanie przełącznika wirtualnego są teraz Odciążone i stosowane w sprzętu. Stosowanie zasad w sprzęcie umożliwia karty Sieciowej do przekazywania ruchu sieciowego bezpośrednio do maszyny Wirtualnej, hosta i przełącznik wirtualny, z pominięciem przy zachowaniu wszystkich zasad, które jest stosowany na hoście.

Korzyści z przyspieszoną siecią dotyczą tylko maszynę Wirtualną, która jest włączona. Aby uzyskać najlepsze wyniki jest idealnym rozwiązaniem włączyć tę funkcję na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi ani nawiązywania połączenia w środowisku lokalnym, ta funkcja ma minimalny wpływ na ogólny czas oczekiwania.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć czas oczekiwania wyższe pakietów na sekundę (pps):** Usunięcie przełącznika wirtualnego z ścieżki danych spowoduje usunięcie pakietów poświęcić czas na hoście dla przetwarzania zasad i zwiększa się liczba pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Jednoczesnym zmniejszeniu zakłóceń:** Przełącznik wirtualny przetwarzania zależy od tego, liczbę zasad, która musi zostać zastosowana i obciążenie procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad na sprzęcie usuwa tego zmienność, zapewniając pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji z maszyną Wirtualną i wszystkie przerwań oprogramowania i przełączeń kontekstu.
* **Obniżenie wykorzystanie procesora CPU:** Przełącznik wirtualny na hoście z pominięciem prowadzi do mniejsze wykorzystanie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Poniższe dystrybucje obsługiwane są gotowe w galerii platformy Azure: 
* **Ubuntu 14.04 za pośrednictwem jądra systemu linux do platformy azure**
* **Ubuntu 16.04 lub nowszy** 
* **SLES12 z dodatkiem SP3 lub nowszy** 
* **RHEL 7.4 lub nowszej**
* **CentOS 7.4 lub nowszej**
* **CoreOS Linux**
* **Debian "Stretch Database" za pośrednictwem jądra backports**
* **Oracle Linux 7.4 lub nowszy z Red Hat zgodne jądra (RHCK)**
* **Oracle Linux 7.5 i nowsze z UEK w wersji 5**
* **FreeBSD 10.4, 11.1 i 12.0**

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszona sieć jest obsługiwana w najbardziej ogólnego przeznaczenia i oferujące zoptymalizowane możliwości obliczeniowe wystąpień o rozmiarach z co najmniej 2 procesorów wirtualnych.  Te serie obsługiwane są następujące: D/DSv2 i F/Fs

W wystąpieniach będzie używanych obsługujące wielowątkowość przyspieszonej sieci jest obsługiwany na wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych. Seria obsługiwane są następujące: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms i Ms/Mmsv2.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach platformy Azure, a także chmury dla instytucji rządowych platformy Azure.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie Accelerated Networking na uruchomionej maszyny Wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez obsługą przyspieszonej sieci może mieć tylko funkcja jest włączona, gdy zostanie zatrzymana i z cofniętą alokacją.  
### <a name="deployment-through-azure-resource-manager"></a>Wdrażanie za pomocą usługi Azure Resource Manager
Nie można wdrożyć maszyny wirtualne (klasyczne) dzięki przyspieszonej sieci.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny Wirtualnej z systemem Linux przy użyciu platformy Azure przyspieszona sieć
## <a name="portal-creation"></a>Tworzenie portalu
Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszoną siecią przy użyciu wiersza polecenia platformy Azure, możesz również [Utwórz maszynę wirtualną z przyspieszoną siecią przy użyciu witryny Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu w **Utwórz maszynę wirtualną** bloku wybierz **sieć** kartę.  Na tej karcie jest opcja **funkcji przyspieszonej łączności sieciowej**.  Jeśli wybrano [obsługiwanym systemie operacyjnym](#supported-operating-systems) i [rozmiar maszyny Wirtualnej](#supported-vm-instances), automatycznie wypełni tę opcję na wartość "Włączone".  W przeciwnym razie zostanie wypełnić Accelerated Networking opcję "Wyłączone" i przyznać użytkownikowi powód, dlaczego nie jest być włączone.   

* *Uwaga:* Można ją włączyć tylko obsługiwane systemy operacyjne za pośrednictwem portalu.  Jeśli używasz niestandardowego obrazu, a obraz obsługuje Accelerated Networking, utwórz maszynę Wirtualną przy użyciu interfejsu wiersza polecenia lub programu Powershell. 

Po utworzeniu maszyny wirtualnej, możesz sprawdzić, przyspieszonej sieci jest włączona, postępując zgodnie z instrukcjami w [upewnij się, że włączono przyspieszonej łączności sieciowej](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Tworzenie interfejsu wiersza polecenia
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj najnowszą wersję [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj się do platformy Azure konta przy użyciu [az login](/cli/azure/reference-index). W poniższych przykładach należy zastąpić własnymi wartościami przykładowe nazwy parametru. Przykładowe nazwy parametru uwzględnione *myResourceGroup*, *myNic*, i *myVm*.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

```azurecli
az group create --name myResourceGroup --location centralus
```

Wybierz obsługiwany region systemu Linux, na liście [Linux przyspieszoną sieć](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* z jedną podsiecią:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń z [tworzenie az sieciowej](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Sieciowa grupa zabezpieczeń zawiera kilka reguł domyślnych, z których jedna wyłącza wszystkie dostępu ruchu przychodzącego z Internetu. Otwórz port, aby zezwolić na dostęp SSH z maszyną wirtualną za pomocą [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Utwórz interfejs sieciowy z przyspieszoną siecią

Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Publiczny adres IP nie jest wymagane, jeśli nie ma dostępu do maszyny wirtualnej z Internetu, ale wykonanie czynności opisanych w tym artykule, jest to wymagane.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Utwórz interfejs sieciowy z [tworzenie az sieciowego](/cli/azure/network/nic) z włączoną obsługą przyspieszonej sieci. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w *mySubnet* podsieci *myVnet* sieci wirtualnej i kojarzy  *myNetworkSecurityGroup* sieciową grupę zabezpieczeń do interfejsu sieciowego:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Tworzenie maszyny Wirtualnej i dołączanie interfejsu Sieciowego
Podczas tworzenia maszyny Wirtualnej, określ interfejsu Sieciowego utworzonego za pomocą `--nics`. Wybierz rozmiar i dystrybucji na liście [Linux przyspieszoną sieć](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* przy użyciu obrazu UbuntuLTS i rozmiar, który obsługuje Accelerated Networking (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i właściwości, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po utworzeniu maszyny Wirtualnej jest zwracany, dane wyjściowe podobne do następujących przykładowych danych wyjściowych. Zanotuj wartość adresu **publicIpAddress**. Ten adres umożliwia dostęp do maszyny Wirtualnej w kolejnych krokach.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Upewnij się, że włączono przyspieszonej łączności sieciowej

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp `<your-public-ip-address>` publiczny adres IP przypisany do wirtualnej komputera został utworzony i Zastąp *azureuser* Jeśli użyto innej wartości `--admin-username` podczas tworzenia maszyny Wirtualnej.

```bash
ssh azureuser@<your-public-ip-address>
```

Z powłoki Bash, wprowadź `uname -r` i upewnij się, że wersja jądra jest jedną z następujących wersji lub nowszej:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Upewnij się, urządzenie Mellanox VF jest narażony na maszynę Wirtualną za pomocą `lspci` polecenia. Dane wyjściowe będą podobne do następujących danych wyjściowych:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sprawdź działanie funkcji Wirtualnej (funkcji wirtualnej) z `ethtool -S eth0 | grep vf_` polecenia. Jeśli pojawi się dane wyjściowe podobne do poniższego przykładu, dane wyjściowe, przyspieszona sieć jest włączona i działa.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Przyspieszona sieć jest teraz włączone dla maszyny Wirtualnej.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz Accelerated Networking na istniejących maszynach wirtualnych
Jeśli utworzono maszynę Wirtualną bez przyspieszonej sieci jest możliwe włączyć tę funkcję na istniejącej maszyny Wirtualnej.  Maszyna wirtualna musi obsługiwać Accelerated Networking, spełniając następujące wymagania wstępne opisane powyżej:

* Maszyna wirtualna musi być obsługiwany rozmiar dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwany obraz w galerii platformy Azure (oraz wersja jądra systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub zestawu skalowania maszyn wirtualnych musi być zatrzymana/z cofniętą alokacją przed włączeniem Accelerated Networking na dowolnej karcie Sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Ustaw poszczególnych maszyn wirtualnych i maszyn wirtualnych w dostępności
Najpierw Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Ważne,. Uwaga: Jeśli Twoja maszyna wirtualna została utworzona pojedynczo, bez zestawu dostępności, możesz jedynie konieczne Zatrzymaj/Cofnij Przydział poszczególnych maszyn wirtualnych, aby umożliwić przyspieszonej sieci.  Jeśli maszyna wirtualna została utworzona przy użyciu zestawu dostępności, wszystkich maszyn wirtualnych znajdujących się w zestawie dostępności muszą być zatrzymana/z cofniętą alokacją przed włączeniem Accelerated Networking na żadnym z kart sieciowych. 

Po zatrzymaniu Włącz przyspieszonej sieci na karcie interfejsu Sieciowego maszyny wirtualnej:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Ponowne uruchomienie Twojej maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że włączono przyspieszonej sieci: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
Zestawu skalowania maszyn wirtualnych jest nieco inna, ale poniżej tego samego przepływu pracy.  Po pierwsze Zatrzymaj maszyny wirtualne:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Gdy maszyny wirtualne zostaną zatrzymane, zaktualizuj właściwość adres Accelerated Networking, w ramach interfejsu sieciowego:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

. Uwaga: zestawu skalowania maszyn wirtualnych ma stosowania aktualizacji przy użyciu trzech różnych ustawień automatycznego, stopniowe i ręcznego uaktualnienia maszyny Wirtualnej.  W tych instrukcjach zasady ustawiono automatyczne tak, aby VMSS przejmą zmiany od razu po ponownym uruchomieniu komputera.  Aby ustawić go na tryb automatyczny tak, aby zmiany są wykrywane natychmiast: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Na koniec uruchom ponownie zestawu skalowania maszyn wirtualnych:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Po można ponownie uruchomić, poczekaj, aż uaktualnień zakończyć, ale po ukończeniu, VF pojawi się wewnątrz maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwany rozmiar maszyn wirtualnych i systemu operacyjnego.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych dzięki przyspieszonej sieci

Tylko można zmienić rozmiar maszyn wirtualnych dzięki przyspieszonej sieci, włączone na maszynach wirtualnych, które obsługują przyspieszonej sieci.  

Nie można zmienić rozmiaru maszyny Wirtualnej z przyspieszonej sieci włączona, do wystąpienia maszyny Wirtualnej, która nie obsługuje przyspieszonej sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub w zestawie dostępności/VMSS, Zatrzymaj/Cofnij Przydział wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszona sieć musi zostać wyłączone na karcie interfejsu Sieciowego maszyny Wirtualnej lub jeśli w dostępności zestawu/zestawu skalowania maszyn wirtualnych, wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu Accelerated Networking dostępności dla maszyny Wirtualnej/set/VMSS można przenieść na nowy rozmiar, który nie obsługuje Accelerated Networking i ponownie uruchomić.  

