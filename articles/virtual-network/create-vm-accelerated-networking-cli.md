---
title: Tworzenie maszyny wirtualnej platformy Azure z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć maszynę wirtualną systemu Linux z włączoną obsługą przyspieszonej sieci.
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
ms.openlocfilehash: 05f8430efa31b39d49025fb8456108da229d3d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239829"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Tworzenie maszyny wirtualnej systemu Linux z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną systemu Linux (VM) z przyspieszoną siecią. Aby utworzyć maszynę wirtualną systemu Windows z [przyspieszoną siecią, zobacz Tworzenie maszyny Wirtualnej systemu Windows z przyspieszoną siecią](create-vm-accelerated-networking-powershell.md). Przyspieszona sieć umożliwia wirtualną wirtualną wirtualną wirtualizację pojedynczego katalogu głównego (SR-IOV), znacznie poprawiając jej wydajność sieciową. Ta ścieżka o wysokiej wydajności omija hosta z ścieżki danych, zmniejszając opóźnienia, jitter i wykorzystanie procesora CPU, do użytku z najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Poniższy obraz przedstawia komunikację między dwoma maszynami wirtualnymi z przyspieszoną siecią i bez niej:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej sieci cały ruch sieciowy do i z maszyny Wirtualnej musi przechodzić przez hosta i przełącznika wirtualnego. Przełącznik wirtualny zapewnia wszystkie wymuszanie zasad, takie jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacja i inne zwirtualizowane usługi sieciowe dla ruchu sieciowego. Aby dowiedzieć się więcej o przełącznikach wirtualnych, przeczytaj artykuł o [wirtualizacji sieci Hyper-V i przełączniku wirtualnym.](https://technet.microsoft.com/library/jj945275.aspx)

Dzięki przyspieszonej sieci ruch sieciowy dociera do interfejsu sieciowego maszyny wirtualnej, a następnie jest przekazywanie dalej do maszyny Wirtualnej. Wszystkie zasady sieciowe stosowane przełącznik wirtualny są teraz odciążane i stosowane w sprzęcie. Stosowanie zasad w sprzęcie umożliwia karcie sieciowej przekazywanie ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijając hosta i przełącznika wirtualnego, zachowując wszystkie zasady stosowane w hoście.

Korzyści z przyspieszonej sieci mają zastosowanie tylko do maszyny Wirtualnej, na których jest włączona. Aby uzyskać najlepsze wyniki, jest idealnym rozwiązaniem, aby włączyć tę funkcję na co najmniej dwóch maszyn wirtualnych połączonych z tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikowania się w sieciach wirtualnych lub łączenia lokalnego, ta funkcja ma minimalny wpływ na ogólne opóźnienie.

## <a name="benefits"></a>Korzyści
* **Mniejsze opóźnienie / wyższe pakiety na sekundę (pps):** Usunięcie przełącznika wirtualnego z ścieżki danych powoduje usunięcie czasu spędzanego przez pakiety na hoście w celu przetwarzania zasad i zwiększenie liczby pakietów, które mogą być przetwarzane wewnątrz maszyny Wirtualnej.
* **Zmniejszono tremę:** Przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszania zasad do sprzętu usuwa tę zmienność, dostarczając pakiety bezpośrednio do maszyny Wirtualnej, usuwając komunikację hosta z maszyną wirtualną i wszystkie przerwania oprogramowania i przełączniki kontekstu.
* **Zmniejszone wykorzystanie procesora:** Pomijanie przełącznika wirtualnego na hoście prowadzi do mniejszego wykorzystania procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane po wyjęciu z pudełka z galerii Azure: 
* **Ubuntu 14.04 z jądrem linux-azure**
* **Ubuntu 16.04 lub nowsze** 
* **SLES12 SP3 lub nowsze** 
* **RHEL 7.4 lub nowsze**
* **CentOS 7,4 lub więcej**
* **CoreOS Linux**
* **Debian "Stretch" z jądrem backports**
* **Oracle Linux 7.4 i nowsze z jądrem zgodnym z red hat (RHCK)**
* **Oracle Linux 7.5 i nowsze wersje UEK w wersji 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości rozmiarów wystąpień ogólnego przeznaczenia i zoptymalizowanych pod kątem obliczeń z 2 lub więcej procesorami wirtualnymi.  Obsługiwane serie to: D/DSv2 i F/Fs

W przypadku wystąpień obsługujących hiperwątkową przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorami wirtualnymi. Obsługiwane serie to: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms i Ms/Mmsv2.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Obrazy niestandardowe
Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszoną sieć, upewnij się, że mają wymagane sterowniki do pracy z kartami sieciowymi Mellanox ConnectX-3 i ConnectX-4 Lx na platformie Azure.

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach platformy Azure, a także w usługach Azure Government Clouds.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszonej sieci na uruchomionej maszynie wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez włączonej przyspieszonej sieci może mieć włączoną funkcję tylko wtedy, gdy zostanie zatrzymana i cofnięta.  
### <a name="deployment-through-azure-resource-manager"></a>Wdrażanie za pośrednictwem usługi Azure Resource Manager
Maszyny wirtualne (klasyczne) nie można wdrożyć z przyspieszoną siecią.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny Wirtualnej systemu Linux za pomocą przyspieszonej sieci platformy Azure
## <a name="portal-creation"></a>Tworzenie portalu
Chociaż w tym artykule przedstawiono kroki tworzenia maszyny wirtualnej z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure, można również [utworzyć maszynę wirtualną z przyspieszoną siecią za pomocą witryny Azure portal.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Podczas tworzenia maszyny wirtualnej w portalu w bloku **Tworzenie maszyny wirtualnej** wybierz kartę **Sieć.**  Na tej karcie dostępna jest opcja **przyspieszonej sieci**.  Jeśli wybrano [obsługiwany system operacyjny](#supported-operating-systems) i rozmiar maszyny [Wirtualnej,](#supported-vm-instances)ta opcja zostanie automatycznie zapełniona jako "On".  Jeśli nie, wypełni opcję "Off" dla przyspieszonej sieci i da użytkownikowi powód, dla którego nie jest włączona.   

* *Uwaga:* Tylko obsługiwane systemy operacyjne można włączyć za pośrednictwem portalu.  Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszoną sieć, utwórz maszynę wirtualną przy użyciu interfejsu wiersza polecenia lub programu PowerShell. 

Po utworzeniu maszyny wirtualnej można potwierdzić, że przyspieszona sieć jest włączona, postępując zgodnie z instrukcjami w [funkcji Potwierdź, że przyspieszona sieć jest włączona.](#confirm-that-accelerated-networking-is-enabled)

## <a name="cli-creation"></a>Tworzenie interfejsu wiersza polecenia
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj najnowszą [platformę Cli platformy Azure](/cli/azure/install-azure-cli) i zaloguj się do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index) W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów obejmowały *myResourceGroup*, *myNic*i *myVm*.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji centralus:*

```azurecli
az group create --name myResourceGroup --location centralus
```

Wybierz obsługiwany region Linuksa wymieniony w [sieci akcelerowanej linuksowej](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

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
Utwórz grupę zabezpieczeń sieci [z nsg sieci az create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Sieciowa grupa zabezpieczeń zawiera kilka reguł domyślnych, z których jedna wyłącza cały dostęp przychodzący z Internetu. Otwórz port, aby umożliwić SSH dostęp do maszyny wirtualnej z [az network nsg reguły tworzenia:](/cli/azure/network/nsg/rule)

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Tworzenie interfejsu sieciowego z przyspieszoną siecią

Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Publiczny adres IP nie jest wymagany, jeśli nie planujesz dostępu do maszyny wirtualnej z Internetu, ale aby wykonać kroki opisane w tym artykule, jest to wymagane.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Utwórz interfejs sieciowy z [tworzeniem sieciowej az](/cli/azure/network/nic) z włączoną obsługą przyspieszonej sieci. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w podsieci *mySubnet* sieci wirtualnej *myVnet* i kojarzy grupę zabezpieczeń sieci *myNetworkSecurityGroup* z interfejsem sieciowym:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Tworzenie maszyny wirtualnej i dołączanie karty sieciowej
Podczas tworzenia maszyny Wirtualnej określ kartę `--nics`sieciową utworzoną za pomocą pliku . Wybierz rozmiar i dystrybucję wymienioną w [sieciach przyspieszanych przez linuks .](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z obrazem UbuntuLTS i rozmiarem obsługującym przyspieszoną sieć *(Standard_DS4_v2):*

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

Aby uzyskać listę wszystkich rozmiarów i cech maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po utworzeniu maszyny Wirtualnej zwracane są dane wyjściowe podobne do następujących przykładowych danych wyjściowych. Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany do uzyskiwania dostępu do maszyny Wirtualnej w kolejnych krokach.

```output
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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Upewnij się, że przyspieszona sieć jest włączona

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zamień `<your-public-ip-address>` publiczny adres IP przypisany do utworzonej maszyny wirtualnej i zamień *użytkownika platformy Azure,* jeśli `--admin-username` podczas tworzenia maszyny wirtualnej została użyta inna wartość.

```bash
ssh azureuser@<your-public-ip-address>
```

Z powłoki Bash `uname -r` wprowadź i potwierdź, że wersja jądra jest jedną z następujących wersji lub większa:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Upewnij się, że urządzenie VF Mellanox `lspci` jest narażony na maszynę wirtualną za pomocą polecenia. Zwrócone dane wyjściowe są podobne do następującego wyjścia:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sprawdź aktywność na VF (funkcja `ethtool -S eth0 | grep vf_` wirtualna) z polecenia. Jeśli otrzymasz dane wyjściowe podobne do następującego przykładowego wyjścia, przyspieszone tworzenie sieci jest włączone i działa.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Przyspieszona sieć jest teraz włączona dla maszyny Wirtualnej.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Obsługa dynamicznego wiązania i odwoływania funkcji wirtualnej 
Aplikacje muszą działać za przez syntetyczną kartę sieciową, która jest widoczna na maszynie Wirtualnej. Jeśli aplikacja jest uruchamiana bezpośrednio za przez kartę sieciową VF, nie odbiera **wszystkich** pakietów, które są przeznaczone do maszyny Wirtualnej, ponieważ niektóre pakiety są wyświetlane za ich dostępem do interfejsu syntetycznego.
Jeśli uruchomisz aplikację za 100000000000 000 000 **000** 000 000 000 000 000 000 000 000 000 000 000 000 0 Upewnia się również, że aplikacja jest nadal uruchomiona, nawet jeśli vf jest odwołany, gdy host jest obsługiwany. Aplikacje wiążące się z syntetyczną kartą sieciową są **obowiązkowym** wymogiem dla wszystkich aplikacji korzystających z **przyspieszonej sieci.**

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz przyspieszoną sieć na istniejących maszynach wirtualnych
Jeśli utworzono maszynę wirtualną bez przyspieszonej sieci, można włączyć tę funkcję na istniejącej maszynie wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszoną sieć, spełniając następujące wymagania wstępne, które są również opisane powyżej:

* Maszyna wirtualna musi mieć obsługiwany rozmiar dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwany obraz Galerii Platformy Azure (i wersja jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS muszą zostać zatrzymane/cofnięto alokacji przed włączeniem przyspieszonej sieci na dowolnej karcie sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Poszczególne maszyny wirtualne & maszyn wirtualnych w zestawie dostępności
Najpierw zatrzymać/zdezawukuj przydzielenie maszyny wirtualnej lub, jeśli zestaw dostępności, wszystkie maszyny wirtualne w zestawie:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Ważne, należy pamiętać, że jeśli maszyna wirtualna została utworzona indywidualnie, bez zestawu dostępności, wystarczy zatrzymać/zdelocytować poszczególne maszyny Wirtualnej, aby włączyć przyspieszoną sieć.  Jeśli maszyna wirtualna została utworzona z zestawem dostępności, wszystkie maszyny wirtualne zawarte w zestawie dostępności będą musiały zostać zatrzymane/cofnięte przed włączeniem przyspieszonej sieci na dowolnej z kart sieciowych. 

Po zatrzymaniu włącz przyspieszoną sieć na karcie sieciowej maszyny Wirtualnej:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Uruchom ponownie maszynę wirtualną lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że przyspieszona sieć jest włączona: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
VMSS jest nieco inna, ale następuje ten sam przepływ pracy.  Najpierw zatrzymaj maszyny wirtualne:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Po zatrzymaniu maszyn wirtualnych zaktualizuj właściwość Przyspieszona sieć w interfejsie sieciowym:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Należy pamiętać, że VMSS ma uaktualnienia maszyn wirtualnych, które stosują aktualizacje przy użyciu trzech różnych ustawień, automatyczne, toczenia i ręczne.  W tych instrukcjach zasady są ustawione na automatyczne, dzięki czemu VMSS będzie odebrać zmiany natychmiast po ponownym uruchomieniu.  Aby ustawić go na automatyczny, tak aby zmiany były natychmiast pobierane: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Na koniec uruchom ponownie usługę VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Po ponownym uruchomieniu poczekaj na zakończenie uaktualnień, ale po zakończeniu, VF pojawi się wewnątrz maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwanego systemu operacyjnego i rozmiaru maszyny Wirtualnej).

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych za pomocą przyspieszonej sieci

Maszyny wirtualne z włączoną obsługą przyspieszonej sieci można zwymiarować tylko na maszyny wirtualne obsługujące przyspieszoną sieć.  

Nie można zmienić rozmiaru maszyny Wirtualnej z włączoną obsługą sieci maszyny Wirtualnej, które nie obsługuje przyspieszonej sieci przy użyciu operacji zmieniania rozmiaru.  Zamiast tego, aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/zdajlokuj maszynę wirtualną lub jeśli w zestawie dostępności/VMSS zatrzymaj/zdeokuj wszystkie maszyny wirtualne w zestawie/VMSS.
* Przyspieszona sieć musi być wyłączona na karcie sieciowej maszyny Wirtualnej lub jeśli w zestawie dostępności/VMSS wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu przyspieszonej sieci zestaw VM/availability/VMSS można przenieść do nowego rozmiaru, który nie obsługuje przyspieszonej sieci i ponownego uruchomienia.  

