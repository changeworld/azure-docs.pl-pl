---
title: Przyspieszoną sieć za pomocą odzyskiwania po awarii maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób włączania przyspieszonej sieci za pomocą usługi Azure Site Recovery do odzyskiwania po awarii maszyn wirtualnych platformy Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: c7edc7979636ced8697aa5ad724f9c6600d840bb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283362"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Przyspieszona sieć za pomocą odzyskiwania po awarii maszyn wirtualnych platformy Azure

Przyspieszona sieć umożliwia wirtualizację we/wy pojedynczego elementu głównego (SR-IOV) do maszyny Wirtualnej, znacznie poprawia wydajność sieci. Tej ścieżki o wysokiej wydajności pomija hosta ze ścieżki danych, zmniejszając czas oczekiwania, zakłócenia i użycie procesora CPU, do użytku z najbardziej wymagających obciążeń sieci na obsługiwane typy maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi z lub bez przyspieszonej łączności sieciowej:

![Porównanie](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Usługa Azure Site Recovery umożliwia korzystanie z zalet Accelerated Networking dla maszyn wirtualnych platformy Azure, przełączone w tryb failover do innego regionu platformy Azure. W tym artykule opisano, jak można włączyć Accelerated Networking dla maszyn wirtualnych platformy Azure replikowane za pomocą usługi Azure Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz:
-   Maszyna wirtualna platformy Azure [architektura replikacji](azure-to-azure-architecture.md)
-   [Konfigurowanie replikacji](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure
-   [Przechodzenie w tryb failover](azure-to-azure-tutorial-failover-failback.md) maszyn wirtualnych platformy Azure

## <a name="accelerated-networking-with-windows-vms"></a>Przyspieszona sieć maszyn wirtualnych Windows

Usługa Azure Site Recovery obsługuje Włączanie Accelerated Networking dla maszyn wirtualnych replikowanych tylko wtedy, gdy źródłowa maszyna wirtualna Accelerated Networking włączone. Jeśli źródłowej maszyny wirtualnej nie ma Accelerated Networking, włączone, możesz dowiedzieć się, jak włączanie maszyn wirtualnych Accelerated Networking for Windows [tutaj](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Poniższe dystrybucje obsługiwane są gotowe w galerii platformy Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszona sieć jest obsługiwana w najbardziej ogólnego przeznaczenia i oferujące zoptymalizowane możliwości obliczeniowe wystąpień o rozmiarach z co najmniej 2 procesorów wirtualnych.  Te serie obsługiwane są następujące: D/DSv2 i F/Fs

W wystąpieniach będzie używanych obsługujące wielowątkowość przyspieszonej sieci jest obsługiwany na wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych. Seria obsługiwane są następujące: D/DSv3, E/ESv3, Fsv2 i Ms/Mms

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Przyspieszona sieć maszyn wirtualnych systemu Linux

Usługa Azure Site Recovery obsługuje Włączanie Accelerated Networking dla maszyn wirtualnych replikowanych tylko wtedy, gdy źródłowa maszyna wirtualna Accelerated Networking włączone. Źródłowej maszyny wirtualnej nie ma Accelerated Networking, włączone, można dowiedzieć się jak włączyć Accelerated Networking dla maszyn wirtualnych systemu Linux [tutaj](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Poniższe dystrybucje obsługiwane są gotowe w galerii platformy Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch Database" za pośrednictwem jądra backports**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyny Wirtualnej
Przyspieszona sieć jest obsługiwana w najbardziej ogólnego przeznaczenia i oferujące zoptymalizowane możliwości obliczeniowe wystąpień o rozmiarach z co najmniej 2 procesorów wirtualnych.  Te serie obsługiwane są następujące: D/DSv2 i F/Fs

W wystąpieniach będzie używanych obsługujące wielowątkowość przyspieszonej sieci jest obsługiwany na wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych. Seria obsługiwane są następujące: D/DSv3, E/ESv3, Fsv2 i Ms/Mms.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [rozmiarów maszyn wirtualnych systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Włączanie Accelerated Networking replikowane maszyny wirtualne

Po użytkownik [włączyć replikację](azure-to-azure-tutorial-enable-replication.md) maszyn wirtualnych platformy Azure Site Recovery automatycznie wykryje, czy interfejsy sieciowe maszyny wirtualnej mają Accelerated Networking włączone. Jeśli przyspieszonej sieci jest już włączony, Usługa Site Recovery automatycznie skonfiguruje przyspieszonej sieci w interfejsach sieciowych zreplikowanej maszyny wirtualnej.

Stan przyspieszonej sieci można sprawdzić w obszarze **interfejsy sieciowe** części **obliczenia i sieć** ustawienia dla zreplikowanej maszyny wirtualnej.

![Przyspieszona sieć ustawienie](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Po włączeniu przyspieszonej sieci na źródłowej maszynie wirtualnej po włączeniu replikacji, aby umożliwić Accelerated Networking dla interfejsów sieciowych zreplikowanej maszyny wirtualnej przez następujący proces:
1. Otwórz **obliczenia i sieć** ustawienia dla zreplikowanej maszyny wirtualnej
2. Kliknij nazwę interfejsu sieciowego w ramach **interfejsy sieciowe** sekcji
3. Wybierz **włączone** z listy rozwijanej dla przyspieszonej sieci w ramach **docelowej** kolumny

![Włącz przyspieszona sieć](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Proces powyżej powinno być stosowane dla istniejących replikowanych maszyn wirtualnych, które nie miała wcześniej Accelerated Networking włączona automatycznie przez usługę Site Recovery.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [zalety Accelerated Networking](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Dowiedz się więcej na temat ograniczeń i ograniczenia Accelerated Networking for [maszyn wirtualnych Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) i [maszyn wirtualnych systemu Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Dowiedz się więcej o [planów odzyskiwania](site-recovery-create-recovery-plans.md) do automatyzowania trybu failover w aplikacji.
