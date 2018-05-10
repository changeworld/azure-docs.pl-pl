---
title: Utwórz maszynę wirtualną platformy Azure za pomocą sieci przyspieszony | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia maszyny wirtualnej systemu Linux za pomocą przyspieszony sieci.
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
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 0f7f389df96f38bea3634bf712af3f9bf4bdde09
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną systemu Linux za pomocą przyspieszony sieci

W tym samouczku Dowiedz się jak utworzyć za pomocą przyspieszony sieci maszyny wirtualnej systemu Linux (VM). Aby utworzyć Maszynę wirtualną systemu Windows za pomocą przyspieszony sieci, zobacz [utworzyć Maszynę wirtualną systemu Windows za pomocą przyspieszony sieci](create-vm-accelerated-networking-powershell.md). Przyspieszone sieci umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Ta ścieżka wysokiej wydajności pomija hosta z ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU do użycia z najbardziej wymagających obciążeń sieci na obsługiwanych typów maszyny Wirtualnej. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z włączonymi i wyłączonymi przyspieszonego sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonego sieci hosta i przełącznik wirtualny musi przejść przez cały ruch sieciowy i maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak grupy zabezpieczeń sieci, list kontroli dostępu, izolacji i innych usług z wirtualizacją sieci dla ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszonego w sieci, ruch sieciowy dociera do maszyny Wirtualnej interfejsu sieciowego (NIC), a następnie jest przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które ma zastosowanie przełącznika wirtualnego są teraz Odciążone i sprzętu. Stosowanie zasad w sprzęcie umożliwia kartę Sieciową do przesyłania dalej ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijanie hosta i przełącznik wirtualny, przy zachowaniu zasady zastosowaniu na hoście.

Korzyści wynikające z przyspieszonego sieci dotyczą tylko maszynę Wirtualną, która jest włączone. Aby uzyskać najlepsze wyniki to idealne rozwiązanie w celu włączenia tej funkcji na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi lub łączącego lokalnymi, ta funkcja ma minimalny wpływ na ogólną opóźnienia.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć opóźnienia / wyższy pakietów na sekundę (pps):** usunięcie przełącznika wirtualnego z ścieżki danych usuwa czasu, jaki poświęcają pakietów na hoście dla przetwarzanie zasad i zwiększa liczbę pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Zmniejszona zakłócenia:** przełącznik wirtualny przetwarzania zależy od ilości zasad, które musi zostać zastosowana i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszanie zasad na sprzęcie usuwa tego zmienności dostarczania pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji maszyny Wirtualnej i wszystkich oprogramowania przerwań i przełączenia kontekstu.
* **Zmniejszyć użycie procesora CPU:** pomijanie przełącznika wirtualnego na hoście prowadzi do mniej użycie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Fabrycznej z galerii Azure obsługiwane są następujące dystrybucji: 
* **Ubuntu 16.04** 
* **SLES 12 Z DODATKIEM SP3** 
* **RHEL 7.4**
* **7.4 centOS**
* **CoreOS systemu Linux**
* **Debian "Stretch", z backports jądra**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszone sieci jest obsługiwana w najbardziej ogólnego przeznaczenia i rozmiary obliczeniowe są zoptymalizowane pod kątem wystąpienia z Vcpu 2 lub większą.  Te serie obsługiwane są: D/DSv2 i F/Fs

Przyspieszony sieci jest obsługiwany w wystąpieniach, które obsługują wielowątkowość na wystąpień maszyn wirtualnych z co najmniej 4 Vcpu. Są obsługiwane serii: D/DSv3, E/ESv3 Fsv2 i Ms/Mms.

Aby uzyskać więcej informacji na wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regiony
Dostępna we wszystkich publicznych regiony platformy Azure, a także chmur Azure dla instytucji rządowych.

### <a name="network-interface-creation"></a>Tworzenie interfejsu sieciowego 
Przyspieszone sieci można włączyć tylko dla nowych kart sieciowych. Nie można włączyć dla istniejącej karty sieciowej.
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszony sieci na uruchomionej maszynie Wirtualnej
Obsługiwany rozmiar maszyny Wirtualnej bez przyspieszonego sieci włączone może mieć tylko funkcja włączona po zatrzymaniu i alokację.  
### <a name="deployment-through-azure-resource-manager"></a>Wdrożenia za pośrednictwem usługi Azure Resource Manager
Nie można wdrożyć maszyn wirtualnych (klasyczne) za pomocą przyspieszony sieci.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Utwórz Maszynę wirtualną systemu Linux z przyspieszonego Azure w sieci

Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszonego w sieci przy użyciu wiersza polecenia platformy Azure, możesz również [Utwórz maszynę wirtualną z przyspieszonego w sieci przy użyciu portalu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu, w obszarze **ustawienia**, wybierz pozycję **włączone**w obszarze **przyspieszony sieci**. Możliwość włączenia przyspieszonego sieci nie jest wyświetlane w portalu chyba, że wybrano [obsługiwanym systemie operacyjnym](#supported-operating-systems) i [rozmiar maszyny Wirtualnej](#supported-vm-instances). Po utworzeniu maszyny wirtualnej, należy wykonać instrukcje [Potwierdź, że jest włączona przyspieszonego sieci](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login). W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNic*, i *myVm*.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

```azurecli
az group create --name myResourceGroup --location centralus
```

Wybierz obsługiwany region systemu Linux na liście [Linux przyspieszony sieci](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* z jedną podsiecią:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz grupę zabezpieczeń sieci z [utworzyć nsg sieci az](/cli/azure/network/nsg#az_network_nsg_create). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Grupa zabezpieczeń sieci zawiera kilka reguł domyślnych, z których jedna wyłącza wszystkie dostępu przychodzące z Internetu. Otwórz port SSH dostęp do maszyny wirtualnej o [Tworzenie reguły nsg sieci az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Utwórz interfejs sieciowy z przyspieszonego sieci

Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Publiczny adres IP nie jest wymagane, jeśli nie planujesz dostęp do maszyny wirtualnej z Internetu, ale wykonać kroki opisane w tym artykule, jest to wymagane.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Tworzenie interfejsu sieciowego z [tworzenie kart interfejsu sieciowego az](/cli/azure/network/nic#az_network_nic_create) z włączoną obsługą przyspieszonego sieci. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w *mySubnet* podsieć *myVnet* sieć wirtualną i skojarzy  *myNetworkSecurityGroup* sieciową grupę zabezpieczeń do interfejsu sieciowego:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Utwórz Maszynę wirtualną i Dołącz kartę Sieciową
Podczas tworzenia maszyny Wirtualnej, określ karty interfejsu Sieciowego zostały utworzone z `--nics`. Wybierz rozmiar i dystrybucji na liście [Linux przyspieszony sieci](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* z obrazu UbuntuLTS i rozmiar, który obsługuje przyspieszony Networking (*Standard_DS4_v2*):

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

Po utworzeniu maszyny Wirtualnej, jest zwracana dane wyjściowe podobne do następujących przykładowe dane wyjściowe. Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany do maszyny Wirtualnej w kolejnych krokach.

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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Upewnij się, że przyspieszonego sieć jest włączona

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp `<your-public-ip-address>` z publicznym adresem IP przypisane do wirtualnego utworzonego komputera i zastąpić *azureuser* Jeśli używasz innej wartości `--admin-username` podczas tworzenia maszyny Wirtualnej.

```bash
ssh azureuser@<your-public-ip-address>
```

Z poziomu powłoki Bash, wprowadź `uname -r` i Potwierdź, że wersja jądra jest jednym z następujących wersji lub większa:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Potwierdź urządzenia funkcji Wirtualnej Mellanox jest widoczne dla maszyny Wirtualnej z `lspci` polecenia. Dane wyjściowe są podobne do następujących danych wyjściowych:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sprawdź działanie na funkcji Wirtualnej (funkcja wirtualnych) z `ethtool -S eth0 | grep vf_` polecenia. Jeśli pojawi się dane wyjściowe podobne do poniższego przykładu output przyspieszonego sieć jest włączona i działa.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Przyspieszone sieci jest teraz włączony dla maszyny Wirtualnej.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włącz przyspieszony sieć na istniejące maszyny wirtualne
Jeśli utworzono Maszynę wirtualną bez przyspieszony sieci jest możliwe włączyć tę funkcję na istniejącej maszyny Wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszony sieci spełniając następujące wymagania wstępne opisane powyżej:

* Maszyna wirtualna musi być obsługiwany rozmiar przyspieszony sieci
* Maszyna wirtualna musi być obsługiwany obraz w galerii Azure (i wersji jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS musi być zatrzymana alokację przed włączeniem przyspieszony sieć na dowolnej karty interfejsu Sieciowego

### <a name="individual-vms--vms-in-an-availability-set"></a>Ustaw poszczególnych maszyn wirtualnych i maszyn wirtualnych w dostępności
Najpierw Zatrzymaj/Cofnij Przydział maszyny Wirtualnej lub, jeśli zbiór dostępności, wszystkich maszyn wirtualnych w zestawie:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Ważne, proszę Uwaga: Jeśli maszyna wirtualna została utworzona oddzielnie, bez zestawu dostępności, możesz tylko konieczne stop/cofnąć poszczególnych maszyn wirtualnych, aby włączyć przyspieszony sieci.  Jeśli maszyna wirtualna została utworzona z zestawu dostępności, zawarty w zestawie dostępności wszystkich maszyn wirtualnych należy być zatrzymana alokację przed włączeniem przyspieszony sieci na żadnym z kart sieciowych. 

Gdy usługa zostanie zatrzymana, należy włączyć przyspieszony sieci na karcie Sieciowej maszyny Wirtualnej:

```azurecli
az network nic update \
    --name myVM -n myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Uruchom sieci maszyny Wirtualnej lub, jeśli w zestawie dostępności, wszystkich maszyn wirtualnych w zestawie i sprawdź, czy włączono przyspieszony sieci: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS różni się nieznacznie, ale wynika z tego samego przepływu pracy.  Przestań maszyn wirtualnych:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Po zatrzymaniu maszyny wirtualnej należy zaktualizować właściwości przyspieszony sieci w interfejsie sieci:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Sprawdź Uwaga, VMSS ma stosowania aktualizacji za pomocą trzy różne ustawienia automatycznego, stopniowego i ręcznego uaktualnienia maszyny Wirtualnej.  W tych instrukcjach zasady są ustawione na tryb automatyczny, aby VMSS przejmą zmian natychmiast po ponownym uruchomieniu.  Ustawić ją na automatyczne, dzięki czemu zmiany są natychmiast pobrana: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Na koniec uruchom ponownie VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Raz należy uruchomić ponownie, poczekaj, aż do zakończenia uaktualnienia, ale po ukończeniu, funkcji Wirtualnej pojawi się w ramach maszyny Wirtualnej.  (Upewnij się, że używasz obsługiwany rozmiar systemu operacyjnego i maszyny Wirtualnej.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmiana rozmiaru istniejących maszyn wirtualnych za pomocą przyspieszony sieci

Tylko można zmienić rozmiar maszyn wirtualnych za pomocą przyspieszony sieci włączony do maszyn wirtualnych, które obsługują przyspieszony sieci.  

Nie można zmienić rozmiaru maszyny Wirtualnej za pomocą przyspieszony sieci włączone, do wystąpienia maszyny Wirtualnej, która nie obsługuje przyspieszony sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/Deallocate maszyny Wirtualnej lub w zestawie dostępności/VMSS, Zatrzymaj/cofnąć wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszone sieci musi zostać wyłączona na karcie Sieciowej maszyny Wirtualnej lub gdy w dostępności zestawu/VMSS, wszystkich maszyn wirtualnych w zestawie/VMSS.
* Po wyłączeniu przyspieszony sieci maszyny Wirtualnej/dostępność zestawu/VMSS można przenieść do nowego rozmiaru, który nie obsługuje przyspieszony sieci i ponowne uruchomienie.  

