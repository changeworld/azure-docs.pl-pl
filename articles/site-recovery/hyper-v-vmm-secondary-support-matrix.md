---
title: Obsługa odzyskiwania po awarii macierzy hiper-V w dodatkowej lokacji programu VMM za pomocą usługi Azure Site Recovery
description: Podsumowuje obsługę replikacji maszyny wirtualnej funkcji Hyper-V w chmurach programu VMM do lokacji dodatkowej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132962"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matryca obsługi odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej

W tym artykule podsumowano, co jest obsługiwane podczas korzystania z usługi [Azure Site Recovery](site-recovery-overview.md) do replikowania maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach Menedżera maszyn wirtualnych (VMM) w centrum systemu do lokacji dodatkowej. Jeśli chcesz replikować maszyny wirtualne z programem Hyper-V na platformie Azure, przejrzyj [tę macierz pomocy technicznej](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Można replikować do lokacji dodatkowej tylko wtedy, gdy hosty funkcji Hyper-V są zarządzane w chmurach programu VMM.


## <a name="host-servers"></a>Serwery hostów

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server 2012 R2 | Na serwerach muszą być uruchomione najnowsze aktualizacje.
Windows Server 2016 |  Chmury programu VMM 2016 z wieloma hostami systemu Windows Server 2016 i 2012 R2 nie są obecnie obsługiwane.<br/><br/> Wdrożenia uaktualnione z programu System Center 2012 R2 VMM 2012 R2 do programu System Center 2016 nie są obecnie obsługiwane.


## <a name="replicated-vm-support"></a>Obsługa replikowanej maszyny Wirtualnej

W poniższej tabeli podsumowano obsługę systemu operacyjnego dla komputerów replikowanych za pomocą funkcji Odzysk lokacji. Każde obciążenie może być uruchomione w obsługiwanym systemie operacyjnym.

**Wersja systemu Windows** | **Hyper-V (z programem VMM)**
--- | ---
Windows Server 2016 | Dowolny system operacyjny [gościa obsługiwany przez program Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) w systemie Windows Server 2016 
Windows Server 2012 R2 | Dowolny system operacyjny [gościa obsługiwany przez program Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) w systemie Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Pamięć masowa maszynowego Linux

Można replikować tylko komputery z systemem Linux z następującym magazynem:

- System plików (EXT3, ETX4, ReiserFS, XFS).
- Multipath oprogramowanie-urządzenie Mapper.
- Menedżer woluminów (LVM2).
- Serwery fizyczne z pamięcią masową kontrolera HP CCISS nie są obsługiwane.
- System plików ReiserFS jest obsługiwany tylko na SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfiguracja sieci — maszyna wirtualna hosta/gościa

**Konfiguracja** | **Obsługiwane**  
--- | --- 
Host - tworzenie zespołu sieciowej | Tak 
Host - VLAN | Tak 
Host - IPv4 | Tak 
Host - IPv6 | Nie 
Maszyna wirtualna gościa — tworzenie zespołów kart sieciowych | Nie
Maszyna wirtualna gościa - IPv4 | Tak
Maszyna wirtualna gościa - IPv6 | Nie
Maszyna wirtualna gościa — Windows/Linux — statyczny adres IP | Tak
Maszyna wirtualna dla gości — wiele kart sieciowych | Tak


## <a name="storage"></a>Magazyn

### <a name="host-storage"></a>Przechowywanie hostów

**Magazyn (host)** | **Obsługiwane**
--- | --- 
NFS | Nie dotyczy
SMB 3.0 |  Tak
SAN (ISCSI) | Tak
Wielobiegowa (MPIO) | Tak

### <a name="guest-or-physical-server-storage"></a>Magazyn serwera gościa lub serwera fizycznego

**Konfiguracja** | **Obsługiwane**
--- | --- | 
Vmdk |  Nie dotyczy
VHD/VHDX | Tak (do 16 dysków)
Maszyna wirtualna gen 2 | Tak
Udostępniony dysk klastrowy | Nie
Zaszyfrowany dysk | Nie
Uefi| Nie dotyczy
NFS | Nie
SMB 3.0 | Nie
Rdm | Nie dotyczy
Dysk > 1 TB | Tak
Wolumin z rozłożonym dyskiem > 1 TB<br/><br/> Lvm | Tak
Miejsca do magazynowania | Tak
Gorący dodawanie/usuwanie dysku | Nie
Wykluczanie dysku | Tak
Wielobiegowa (MPIO) | Tak

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane**
--- | --- 
Przenoszenie magazynów między grupami zasobów (w ramach subskrypcji lub między nimi) |  Nie
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach subskrypcji lub między nimi) | Nie

## <a name="azure-site-recovery-provider"></a>Dostawca odzyskiwania witryny platformy Azure

Dostawca koordynuje komunikację między serwerami programu VMM. 

**Najnowsza** | **Aktualizacje**
--- | --- 
5.1.19 ([dostępne na portalu](https://aka.ms/downloaddra) | [Najnowsze funkcje i poprawki](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych funkcji Hyper V w chmurach programu VMM do lokacji dodatkowej](tutorial-vmm-to-vmm.md)

