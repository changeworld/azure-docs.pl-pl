---
title: Utwórz maszynę wirtualną platformy Azure za pomocą sieci przyspieszony | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia maszyny wirtualnej systemu Linux za pomocą przyspieszony sieci.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: aa74596906206ba4460e80af9015955c0b848cd4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Utwórz maszynę wirtualną systemu Linux za pomocą przyspieszony sieci

> [!IMPORTANT] 
> Maszyny wirtualne muszą być tworzone przyspieszony sieci włączone. Nie można włączyć tę funkcję w istniejących maszyn wirtualnych. Wykonaj poniższe kroki, aby włączyć przyspieszony sieci:
>   1. Usuń maszynę wirtualną.
>   2. Ponownie utwórz maszynę wirtualną za pomocą przyspieszony sieci włączone.
>

W tym samouczku Dowiedz się jak utworzyć za pomocą przyspieszony sieci maszyny wirtualnej systemu Linux (VM). Przyspieszone sieci umożliwia wirtualizację We/Wy z jednym elementem głównym (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Ta ścieżka wysokiej wydajności pomija hosta z ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU do użycia z najbardziej wymagających obciążeń sieci na obsługiwanych typów maszyny Wirtualnej. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z włączonymi i wyłączonymi przyspieszonego sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonego sieci hosta i przełącznik wirtualny musi przejść przez cały ruch sieciowy i maszyny Wirtualnej. Przełącznik wirtualny udostępnia wszystkie egzekwowanie zasad, takich jak grupy zabezpieczeń sieci, list kontroli dostępu, izolacji i innych usług z wirtualizacją sieci dla ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj [wirtualizacji sieci funkcji Hyper-V i przełącznik wirtualny](https://technet.microsoft.com/library/jj945275.aspx) artykułu.

Z przyspieszonego w sieci, ruch sieciowy dociera do maszyny Wirtualnej interfejsu sieciowego (NIC), a następnie jest przekazywany do maszyny Wirtualnej. Wszystkie zasady sieci, które ma zastosowanie przełącznika wirtualnego są teraz Odciążone i sprzętu. Stosowanie zasad w sprzęcie umożliwia kartę Sieciową do przesyłania dalej ruchu sieciowego bezpośrednio do maszyny Wirtualnej, pomijanie hosta i przełącznik wirtualny, przy zachowaniu zasady zastosowaniu na hoście.

Korzyści wynikające z przyspieszonego sieci dotyczą tylko maszynę Wirtualną, która jest włączone. Aby uzyskać najlepsze wyniki to idealne rozwiązanie w celu włączenia tej funkcji na co najmniej dwie maszyny wirtualne podłączone do tej samej sieci wirtualnej platformy Azure (VNet). Podczas komunikacji między sieciami wirtualnymi lub łączącego lokalnymi, ta funkcja ma minimalny wpływ na ogólną opóźnienia.

## <a name="benefits"></a>Korzyści
* **Zmniejszyć opóźnienia / wyższy pakietów na sekundę (pps):** usunięcie przełącznika wirtualnego z ścieżki danych usuwa czasu, jaki poświęcają pakietów na hoście dla przetwarzanie zasad i zwiększa liczbę pakietów, które mogą być przetwarzane w ramach maszyny Wirtualnej.
* **Zmniejszona zakłócenia:** przełącznik wirtualny przetwarzania zależy od ilości zasad, które musi zostać zastosowana i obciążenia procesora CPU, który wykonuje przetwarzanie. Odciążanie wymuszanie zasad na sprzęcie usuwa tego zmienności dostarczania pakietów bezpośrednio do maszyny Wirtualnej, usunięcie hosta do komunikacji maszyny Wirtualnej i wszystkich oprogramowania przerwań i przełączenia kontekstu.
* **Zmniejszyć użycie procesora CPU:** pomijanie przełącznika wirtualnego na hoście prowadzi do mniej użycie procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
* **Ubuntu 16.04**: 4.11.0-1013 lub nowszej wersji jądra
* **Z dodatkiem SP3 SLES 12**: 4.4.92-6.18 lub nowszej wersji jądra
* **RHEL 7.4**: 7.4.2017120423 lub nowszej wersji jądra
* **CentOS 7.4**: 7.4.20171206 lub nowszej wersji jądra

## <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszone sieci jest obsługiwana w najbardziej ogólnego przeznaczenia i rozmiary obliczeniowe są zoptymalizowane pod kątem wystąpienia z co najmniej 4 Vcpu. W przypadkach, takich jak D/DSv3 lub E/ESv3 obsługujące wielowątkowość przyspieszony sieci jest obsługiwany w wystąpieniach maszyny Wirtualnej z co najmniej 8 Vcpu.  Są obsługiwane serii: D/DSv2, D/DSv3 E/ESv3, F/Fs/Fsv2 i Ms/Mms. 

Aby uzyskać więcej informacji na wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiony
Dostępna we wszystkich publicznych regiony platformy Azure, a także chmur Azure dla instytucji rządowych.

## <a name="limitations"></a>Ograniczenia
Podczas przy użyciu tej możliwości istnieją następujące ograniczenia:

* **Tworzenie interfejsu sieci:** akcelerowanego sieci można włączyć tylko dla nowych kart sieciowych. Nie można włączyć dla istniejącej karty sieciowej.
* **Tworzenie maszyny Wirtualnej:** A kart interfejsu Sieciowego z włączoną obsługą przyspieszonego sieci może zostać dołączona tyko do maszyny Wirtualnej po utworzeniu maszyny Wirtualnej. Nie można dołączyć karty Sieciowej do istniejącej maszyny Wirtualnej. Jeśli dodawanie maszyny Wirtualnej do istniejących danych o dostępności, wszystkich maszyn wirtualnych w zestawie dostępności muszą również przyspieszyć sieci włączone.
* **Tylko wdrożenia za pośrednictwem usługi Azure Resource Manager:** maszyn wirtualnych (klasyczne) nie można wdrożyć za pomocą przyspieszony sieci.

Chociaż ten artykuł zawiera kroki, aby utworzyć maszynę wirtualną z przyspieszonego w sieci przy użyciu wiersza polecenia platformy Azure, możesz również [Utwórz maszynę wirtualną z przyspieszonego w sieci przy użyciu portalu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej przy użyciu obsługiwany system operacyjny i rozmiar maszyny Wirtualnej w portalu, w obszarze **ustawienia**, wybierz pozycję **włączone** w obszarze **przyspieszony sieci**. Po utworzeniu maszyny wirtualnej, należy wykonać instrukcje [Potwierdź, że jest włączona przyspieszonego sieci](#confirm-that-accelerated-networking-is-enabled).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj najnowszą [Azure CLI 2.0](/cli/azure/install-az-cli2) i zaloguj się do platformy Azure konta przy użyciu [logowania az](/cli/azure/reference-index#az_login). W poniższych przykładach Zastąp przykładowe nazwy parametrów własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNic*, i *myVm*.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *centralus* lokalizacji:

```azurecli
az group create --name myResourceGroup --location centralus
```

Musisz wybrać obsługiwanym regionie systemu Linux na liście [Linux przyspieszony sieci](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* z jedną podsiecią:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
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

## <a name="create-a-network-interface-with-accelerated-networking"></a>Utwórz interfejs sieciowy z przyspieszonego sieci

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

## <a name="create-a-vm-and-attach-the-nic"></a>Utwórz Maszynę wirtualną i Dołącz kartę Sieciową
Podczas tworzenia maszyny Wirtualnej, określ karty interfejsu Sieciowego zostały utworzone z `--nics`. Należy wybrać odpowiedni rozmiar i dystrybucji na liście [Linux przyspieszony sieci](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

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

Po utworzeniu maszyny Wirtualnej, jest zwracana dane wyjściowe podobne do następujących przykładowe dane wyjściowe. Zwróć uwagę na **publicznego adresu IP**. Ten adres jest używany do maszyny Wirtualnej w kolejnych krokach.

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Upewnij się, że przyspieszonego sieć jest włączona

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną Wirtualną. Zastąp `<your-public-ip-address>` z publicznym adresem IP przypisane do wirtualnego utworzonego komputera i zastąpić *azureuser* Jeśli używasz innej wartości `--admin-username` podczas tworzenia maszyny Wirtualnej.

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
