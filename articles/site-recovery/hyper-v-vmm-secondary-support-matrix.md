---
title: Macierz obsługi odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V, w programie VMM z chmury do lokacji dodatkowej za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługi replikacji maszyny Wirtualnej funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: e8b8f9856fe7e0fa591ceb42aab97e92642b6098
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399362"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Macierz obsługi dla odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej

Ten artykuł zawiera podsumowanie, co jest obsługiwane w przypadku używania [usługi Azure Site Recovery](site-recovery-overview.md) usługi w celu replikowania maszyn wirtualnych funkcji Hyper-V zarządzane w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej. Jeśli chcesz replikować maszyny wirtualne funkcji Hyper-V do platformy Azure, zapoznaj się z [tej macierzy obsługi](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Tylko można replikować do lokacji dodatkowej w przypadku hostów funkcji Hyper-V są zarządzane w chmurach programu VMM.

  

## <a name="host-servers"></a>Serwery hosta

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2012 R2 | Serwery muszą być uruchomione najnowsze aktualizacje.
Windows Server 2016 |  Chmury programu VMM 2016 z systemu Windows Server 2016 i 2012 R2 hostów nie są obecnie obsługiwane.<br/><br/> Wdrożenia uaktualnionych z programu System Center 2012 R2 VMM 2012 R2 do programu System Center 2016 nie są obecnie obsługiwane.


## <a name="replicated-vm-support"></a>Replikowane maszyn wirtualnych z osłoną

Poniższa tabela zawiera podsumowanie obsługi systemów operacyjnych w przypadku maszyn replikowanych z usługą Site Recovery. Każde obciążenie może być uruchomiony w obsługiwanym systemie operacyjnym.

**Wersja Windows** | **Funkcji Hyper-V (z programem VMM)**
--- | ---
Windows Server 2016 | Dowolny system operacyjny gościa z [obsługiwane przez funkcję Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) w systemie Windows Server 2016 
Windows Server 2012 R2 | Dowolny system operacyjny gościa z [obsługiwane przez funkcję Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) w systemie Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Magazyn maszyny systemu Linux

Można replikować tylko maszyny z systemem Linux przy użyciu następujących magazynu:

- System (EXT3, ETX4, ReiserFS, XFS) plików.
- Mapowanie urządzeń wielościeżkowych oprogramowania.
- Menedżer woluminów (LVM2).
- Serwery fizyczne z HP CCISS kontroler magazynu nie są obsługiwane.
- System plików ReiserFS jest obsługiwany tylko w systemie SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfiguracja sieci — Host/gościa maszyny Wirtualnej

**Konfiguracja** | **Obsługiwane**  
--- | --- 
Host - zespołu kart interfejsu Sieciowego | Tak 
Host - sieci VLAN | Tak 
Host — protokołu IPv4 | Yes 
Host - IPv6 | Nie 
Maszyna wirtualna - gościa zespołu kart interfejsu Sieciowego | Nie
Gość maszyny Wirtualnej — protokołu IPv4 | Tak
Gość maszyny Wirtualnej — IPv6 | Nie
Gość maszyny Wirtualnej — Windows/Linux — statyczny adres IP | Yes
Gość maszyny Wirtualnej — Multi-NIC | Tak


## <a name="storage"></a>Magazyn

### <a name="host-storage"></a>Magazyn hosta

**Magazyn (host)** | **Obsługiwane**
--- | --- 
NFS | ND
SMB 3.0 |  Tak
SAN (ISCSI) | Tak
Wiele ścieżek (MPIO) | Tak

### <a name="guest-or-physical-server-storage"></a>Maszyna gościa lub serwer fizyczny magazynu

**Konfiguracja** | **Obsługiwane**
--- | --- | 
VMDK |  ND
VHD/VHDX | Tak (maksymalnie 16 dysków)
Zbieranie generacji 2 maszyn wirtualnych | Tak
Udostępniony dysk klastra | Nie
Zaszyfrowanego dysku | Nie
UEFI| ND
NFS | Nie
SMB 3.0 | Nie
RDM | ND
> 1 TB danych na dysku | Tak
Wolumin dysku rozłożonego > 1 TB<br/><br/> LVM | Tak
Miejsca do magazynowania | Tak
Dodaj lub usuń gorąco dysku | Nie
Wykluczanie dysku | Yes
Wiele ścieżek (MPIO) | Tak

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane**
--- | --- 
Przeniesienie magazynów między grupami zasobów (w ramach lub subskrypcji) |  Nie
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach lub subskrypcji) | Nie

## <a name="azure-site-recovery-provider"></a>Dostawca usługi Azure Site Recovery

Dostawca służy do koordynowania komunikacji między serwerami programu VMM. 

**najnowsze** | **Aktualizacje**
--- | --- 
5.1.19 ([dostępna z portalu](https://aka.ms/downloaddra) | [Najnowsze funkcje i poprawki](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Kolejne kroki

[Replikacja maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM do lokacji dodatkowej](tutorial-vmm-to-vmm.md)

