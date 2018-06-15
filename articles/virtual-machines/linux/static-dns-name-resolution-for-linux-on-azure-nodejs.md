---
title: Przy użyciu wewnętrznego serwera DNS do rozpoznawania nazw maszyny Wirtualnej na platformie Azure | Dokumentacja firmy Microsoft
description: Przy użyciu wewnętrznego serwera DNS do rozpoznawania nazw maszyny Wirtualnej na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 73ecbb5bfcc99eb4507cac24b0539a04c3d8f521
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917282"
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Przy użyciu wewnętrznego serwera DNS do rozpoznawania nazw maszyny Wirtualnej na platformie Azure

W tym artykule pokazano, jak ustawić statyczny wewnętrznej nazwy DNS dla maszyn wirtualnych systemu Linux przy użyciu karty wirtualnej karty Sieciowej (VNic) i nazwy etykiety DNS. Statyczne nazwy DNS są używane dla usług trwałych infrastruktury, takich jak serwer kompilacji Wpięć, który służy do tego dokumentu lub serwer Git.

Wymagania są następujące:

* [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Wersje interfejsu wiersza polecenia umożliwiające wykonanie zadania
Zadanie można wykonać przy użyciu jednej z następujących wersji interfejsu wiersza polecenia:

- [Azure CLI 1.0](#quick-commands) — nasze interfejsu wiersza polecenia dla klasycznego i zasobów zarządzania wdrażania modeli (w tym artykule)
- [Interfejs wiersza polecenia platformy Azure w wersji 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — nasz interfejs wiersza polecenia nowej generacji dla modelu wdrażania na potrzeby zarządzania zasobami


## <a name="quick-commands"></a>Szybkie polecenia

Jeśli chcesz szybko wykonywać zadania poniższej sekcji Szczegóły poleceń potrzebne. Bardziej szczegółowe informacje i kontekst dla każdego kroku można znaleźć pozostałej części dokumentu, [uruchamiania tutaj](#detailed-walkthrough).  

Wymagania Wstępne: NSG grupy zasobów, sieciami wirtualnymi, przy użyciu protokołu SSH ruchu przychodzącego, podsieci.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Utworzyć VNic statyczne wewnętrzne nazwy DNS

`-r` Flaga interfejsu wiersza polecenia jest do ustawiania etykiety DNS, który zawiera nazwę DNS statycznych VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Wdróż maszynę Wirtualną do sieci wirtualnej, NSG, a następnie połącz VNic

`-N` VNic łączy się z nowej maszyny Wirtualnej podczas wdrażania na platformie Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Szczegółowy przewodnik

Pełne ciągłej integracji i ciągłe wdrażanie (CiCd) infrastruktury na platformie Azure wymaga niektórych serwerów serwery statyczne lub długotrwałe.  Zaleca się, że zasoby platformy Azure, takich jak sieci wirtualnych (sieci wirtualne) i grupy zabezpieczeń sieci (NSG), musi być statyczny i tam długo zasoby, które rzadko są wdrożone.  Po wdrożeniu sieci wirtualnej można użyć ponownie przez nowych wdrożeń bez żadnych niekorzystny wpływ infrastruktury.  Dodawanie do tej sieci statycznych serwera repozytorium Git i serwer automatyzacji Wpięć zapewnia CiCd Twojego środowisk deweloperskich lub testowania.  

Wewnętrzny nazw DNS są tylko rozpoznawany w sieci wirtualnej platformy Azure.  Ponieważ nazwy DNS są wewnętrzne, nie są one rozpoznać poza internet, zapewniając dodatkowe zabezpieczenia infrastruktury.

_Przykładami Zamień własne nazwy._

## <a name="create-the-resource-group"></a>Utwórz grupę zasobów

Grupa zasobów jest potrzebny do organizowania wszystko, co mamy utworzyć w tym przewodniku.  Aby uzyskać więcej informacji na temat grup zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Tworzenie sieci wirtualnej

Pierwszym krokiem jest kompilacji do uruchamiania maszyn wirtualnych do sieci wirtualnej.  Sieć wirtualna zawiera jedną podsieć w ramach tego przewodnika.  Aby uzyskać więcej informacji o sieci wirtualnych platformy Azure, zobacz [tworzenie sieci wirtualnej](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Tworzenie grupy NSG

Podsieć jest oparty za istniejącą sieciową grupę zabezpieczeń, dlatego budujemy NSG przed podsieci.  Azure grup NSG są równoważne zapory w warstwie sieci.  Aby uzyskać więcej informacji dotyczących grup NSG Azure, zobacz [sposób tworzenia grup NSG w wiersza polecenia platformy Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Dodaj regułę Zezwalaj SSH dla ruchu przychodzącego

Maszyny Wirtualnej systemu Linux wymaga dostępu z Internetu, więc regułę zezwalającą na ruch przychodzący port 22 mają być przekazywane za pośrednictwem sieci do portu 22 na Maszynie wirtualnej systemu Linux nie jest wymagane.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Dodaj podsieć do sieci wirtualnej

Maszyny wirtualne w ramach sieci wirtualnej muszą znajdować się w podsieci.  Każda sieć wirtualna może mieć wiele podsieci.  Utwórz podsieć i skojarzyć podsieci z grupy NSG można dodać zapory do podsieci.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Podsieć jest teraz dodany w sieci wirtualnej i skojarzonych z grupy NSG i reguły NSG.

## <a name="creating-static-dns-names"></a>Tworzenie statycznej nazwy DNS

Platforma Azure jest bardzo elastyczne, ale aby użyć nazwy DNS dla rozpoznawania nazw maszyn wirtualnych, należy utworzyć je jako wirtualne karty sieciowe (VNics) za pomocą etykiety DNS.  VNics są ważne, ponieważ użytkownik może korzystać z nich łącząc je do różnych maszyn wirtualnych, które VNic jako zasób statycznych maszyn wirtualnych mogą być tymczasowe.  Za pomocą DNS etykietowania na karcie VNic, możemy włączyć rozpoznawanie nazw prostego z innych maszyn wirtualnych w sieci wirtualnej.  Przy użyciu nazwy rozpoznawalną umożliwia innych maszyn wirtualnych na dostęp do serwera automatyzacji za pomocą nazwy DNS `Jenkins` lub na serwerze Git `gitrepo`.  Utwórz VNic i skojarzyć go z podsiecią utworzony w poprzednim kroku.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Wdróż maszynę Wirtualną do sieci wirtualnej i grupy NSG

Mamy teraz sieci wirtualnej, podsieci w tej sieci wirtualnej i grupy NSG działający jako zapora chronić nasze podsieci blokuje cały ruch przychodzący z wyjątkiem port 22 dla protokołu SSH.  Teraz można wdrożyć maszyny Wirtualnej w tym istniejącej infrastruktury sieci.

Przy użyciu interfejsu wiersza polecenia Azure i `azure vm create` polecenia do istniejącej grupy zasobów platformy Azure, sieci wirtualnej, podsieci i VNic wdrożeniu maszyny Wirtualnej systemu Linux.  Aby uzyskać więcej informacji na instalację pełną maszyny Wirtualnej za pomocą interfejsu wiersza polecenia, zobacz [tworzyć kompletne środowisko systemu Linux przy użyciu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Za pomocą flag interfejsu wiersza polecenia do wyróżnienia istniejących zasobów, poinstruuj firma Microsoft Azure, aby wdrożyć maszynę Wirtualną w istniejącej sieci.  Aby przywołują, po wdrożeniu sieci wirtualnej i podsieci, możesz je zostawić jako statyczne ani stałe zasoby w Twoim regionie Azure.  

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie niestandardowego środowiska dla maszyny wirtualnej z systemem Linux poprzez bezpośrednie użycie poleceń interfejsu wiersza polecenia platformy Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz Maszynę wirtualną systemu Linux na platformie Azure za pomocą szablonów](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
