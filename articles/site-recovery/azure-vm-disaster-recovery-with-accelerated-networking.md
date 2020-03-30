---
title: Włącz przyspieszoną sieć dla odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
description: W tym artykule opisano, jak włączyć przyspieszoną sieć za pomocą usługi Azure Site Recovery dla odzyskiwania po awarii maszyny wirtualnej platformy Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73622431"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Przyspieszona sieć z odzyskiwaniem po awarii maszyny wirtualnej platformy Azure

Przyspieszona sieć umożliwia wirtualną wirtualną wirtualną wirtualizację pojedynczego katalogu głównego (SR-IOV), znacznie poprawiając jej wydajność sieciową. Ta ścieżka o wysokiej wydajności omija hosta z ścieżki danych, zmniejszając opóźnienia, jitter i wykorzystanie procesora CPU, do użytku z najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Poniższy obraz przedstawia komunikację między dwoma maszynami wirtualnymi z przyspieszoną siecią i bez niej:

![Porównanie](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Usługa Azure Site Recovery umożliwia korzystanie z zalet przyspieszonej sieci dla maszyn wirtualnych platformy Azure, które są awaryjne w innym regionie platformy Azure. W tym artykule opisano, jak włączyć przyspieszoną sieć maszyn wirtualnych platformy Azure replikowanych za pomocą usługi Azure Site Recovery.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że rozumiesz:
-   [Architektura replikacji](azure-to-azure-architecture.md) maszyny wirtualnej platformy Azure
-   [Konfigurowanie replikacji](azure-to-azure-tutorial-enable-replication.md) dla maszyn wirtualnych platformy Azure
-   [Niepowodzenie](azure-to-azure-tutorial-failover-failback.md) Maszyny wirtualne platformy Azure

## <a name="accelerated-networking-with-windows-vms"></a>Przyspieszona sieć z maszynami wirtualnymi systemu Windows

Usługa Azure Site Recovery obsługuje włączanie przyspieszonej sieci dla replikowanych maszyn wirtualnych tylko wtedy, gdy źródłocza maszyna wirtualna ma włączoną przyspieszoną sieć. Jeśli źródłocza maszyna wirtualna nie ma włączonej przyspieszonej sieci, w [tym miejscu](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)można dowiedzieć się, jak włączyć przyspieszoną sieć dla maszyn wirtualnych systemu Windows.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane po wyjęciu z pudełka z galerii Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości rozmiarów wystąpień ogólnego przeznaczenia i zoptymalizowanych pod kątem obliczeń z 2 lub więcej procesorami wirtualnymi.  Obsługiwane serie to: D/DSv2 i F/Fs

W przypadku wystąpień obsługujących hiperwątkową przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorami wirtualnymi. Obsługiwane serie to: D/DSv3, E/ESv3, Fsv2 i Ms/Mms

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Przyspieszona sieć z maszynami wirtualnymi z systemem Linux

Usługa Azure Site Recovery obsługuje włączanie przyspieszonej sieci dla replikowanych maszyn wirtualnych tylko wtedy, gdy źródłocza maszyna wirtualna ma włączoną przyspieszoną sieć. Jeśli źródłocza maszyna wirtualna nie ma włączonej przyspieszonej sieci, tutaj można dowiedzieć się, jak włączyć przyspieszoną sieć maszyn [wirtualnych](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)systemu Linux.

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane po wyjęciu z pudełka z galerii Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" z jądrem backports**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości rozmiarów wystąpień ogólnego przeznaczenia i zoptymalizowanych pod kątem obliczeń z 2 lub więcej procesorami wirtualnymi.  Obsługiwane serie to: D/DSv2 i F/Fs

W przypadku wystąpień obsługujących hiperwątkową przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorami wirtualnymi. Obsługiwane serie to: D/DSv3, E/ESv3, Fsv2 i Ms/Mms.

Aby uzyskać więcej informacji na temat wystąpień maszyn wirtualnych, zobacz [Rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Włączanie przyspieszonej sieci dla replikowanych maszyn wirtualnych

Po [włączeniu replikacji](azure-to-azure-tutorial-enable-replication.md) dla maszyn wirtualnych platformy Azure usługa Site Recovery automatycznie wykryje, czy interfejsy sieciowe maszyny wirtualnej mają włączoną funkcję Przyspieszona sieć. Jeśli przyspieszona sieć jest już włączona, usługa Site Recovery automatycznie skonfiguruje przyspieszoną sieć w interfejsach sieciowych replikowanej maszyny wirtualnej.

Stan przyspieszonej sieci można zweryfikować w sekcji **Interfejsy sieciowe** ustawień **obliczeniowych i sieciowych** replikowanej maszyny wirtualnej.

![Przyspieszone ustawienie sieci](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Jeśli po włączeniu replikacji włączono przyspieszoną sieć na źródłowej maszynie wirtualnej, można włączyć przyspieszoną sieć dla interfejsów sieciowych replikowanej maszyny wirtualnej w następującym procesie:
1. Otwórz ustawienia **obliczeniowe i sieciowe** replikowanej maszyny wirtualnej
2. Kliknij nazwę interfejsu sieciowego w sekcji **Interfejsy sieciowe**
3. Z listy rozwijanej dla przyspieszonej sieci w kolumnie **Cel** wybierz **pozycję Włączono.**

![Włącz przyspieszoną sieć](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Powyższy proces należy również śledzić dla istniejących zreplikowanych maszyn wirtualnych, które wcześniej nie miały włączone przyspieszone sieci automatycznie przez usługę Site Recovery.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [zaletach przyspieszonej sieci.](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)
- Dowiedz się więcej o ograniczeniach i ograniczeniach przyspieszonej sieci dla [maszyn wirtualnych systemu Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) i maszyn [wirtualnych systemu Linux.](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
