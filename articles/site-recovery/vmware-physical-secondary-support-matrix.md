---
title: Macierz obsługi na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware lub serwerów fizycznych do lokacji dodatkowej programu VMware przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie obsługi odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: b316d6a8293d9f23eb89e8b6fffedac316759df4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360147"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Macierz obsługi dla odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej

Ten artykuł zawiera podsumowanie, co jest obsługiwane w przypadku używania [usługi Azure Site Recovery](site-recovery-overview.md) usługi odzyskiwania po awarii maszyn wirtualnych VMware lub systemu Windows/Linux serwerów fizycznych do lokacji dodatkowej programu VMware.

- Jeśli chcesz replikować maszyny wirtualne VMware lub serwery fizyczne do platformy Azure, zapoznaj się z [tej macierzy obsługi](vmware-physical-azure-support-matrix.md).
- Jeśli chcesz replikować maszyny wirtualne funkcji Hyper-V do lokacji dodatkowej, zapoznaj się z [tej macierzy obsługi](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikacja serwerów fizycznych i maszyn wirtualnych VMware w środowisku lokalnym jest dostarczany przez InMage Scout. InMage Scout znajduje się w subskrypcji usługi Azure Site Recovery.


## <a name="host-servers"></a>Serwery hosta

**System operacyjny** | **Szczegóły**
--- | ---
vCenter Server | vCenter 5.5, 6.0 i 6.5<br/><br/> Po uruchomieniu, 6.0 lub 6.5, należy pamiętać, że tylko 5.5 funkcje są obsługiwane.


## <a name="replicated-vm-support"></a>Replikowane maszyn wirtualnych z osłoną

Poniższa tabela zawiera podsumowanie obsługi systemów operacyjnych w przypadku maszyn replikowanych z usługą Site Recovery. Każde obciążenie może być uruchomiony w obsługiwanym systemie operacyjnym.

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server | 64-bitowym systemie Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z w co najmniej z dodatkiem SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 systemem Red Hat zgodne jądra lub podzielenie Enterprise jądra wersji 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
Host - zespołu kart interfejsu Sieciowego | Yes 
Host - sieci VLAN | Yes 
Host — protokołu IPv4 | Yes 
Host - IPv6 | Nie 
Maszyna wirtualna - gościa zespołu kart interfejsu Sieciowego | Nie
Gość maszyny Wirtualnej — protokołu IPv4 | Yes
Gość maszyny Wirtualnej — IPv6 | Nie
Gość maszyny Wirtualnej — Windows/Linux — statyczny adres IP | Yes
Gość maszyny Wirtualnej — Multi-NIC | Yes


## <a name="storage"></a>Magazyn

### <a name="host-storage"></a>Magazyn hosta

**Magazyn (host)** | **Obsługiwane** 
--- | --- 
NFS | Yes 
SMB 3.0 | ND 
SAN (ISCSI) | Yes 
Wiele ścieżek (MPIO) | Yes 

### <a name="guest-or-physical-server-storage"></a>Maszyna gościa lub serwer fizyczny magazynu

**Konfiguracja** | **Obsługiwane** 
--- | --- 
VMDK | Yes 
VHD/VHDX | ND 
Zbieranie generacji 2 maszyn wirtualnych | ND 
Udostępniony dysk klastra | Yes 
Zaszyfrowanego dysku | Nie 
UEFI| Yes 
NFS | Nie 
SMB 3.0 | Nie 
RDM | Yes 
> 1 TB danych na dysku | Yes 
Wolumin dysku rozłożonego > 1 TB<br/><br/> LVM | Yes 
Miejsca do magazynowania | Nie 
Dodaj lub usuń gorąco dysku | Yes 
Wykluczanie dysku | Yes 
Wiele ścieżek (MPIO) | ND 

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane** 
--- | --- 
Przeniesienie magazynów między grupami zasobów (w ramach lub subskrypcji) | Nie 
Przenieś magazyn, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach lub subskrypcji) | Nie 

## <a name="mobility-service-and-updates"></a>Wersja usługi mobilności i aktualizacje

Usługa mobilności koordynuje replikację między lokalnymi serwerami VMware lub serwerów fizycznych i lokacji dodatkowej. Po skonfigurowaniu replikacji upewnić się, że masz najnowszą wersję usługi mobilności i innych składników.

| **Aktualizacja** | **Szczegóły** |
| --- | --- |
|Aktualizacje programu Scout | Aktualizacje programu Scout kumulują się. <br/><br/> [Dowiedz się więcej o i Pobierz](vmware-physical-secondary-disaster-recovery.md#updates) najnowsze aktualizacje programu Scout |
|Aktualizacje składników | Aktualizacje programu Scout obejmują aktualizacje dla wszystkich składników, w tym RX serwera, serwer konfiguracji, proces i główne serwery docelowe, vContinuum serwerów i serwerów źródłowych, które chcesz chronić.<br/><br/> [Dowiedz się więcej](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Kolejne kroki

Pobierz [Podręcznik użytkownika programu InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikacja maszyn wirtualnych z funkcją Hyper-V w chmurach programu VMM do lokacji dodatkowej](tutorial-vmm-to-vmm.md)
- [Replikacja maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej](tutorial-vmware-to-vmware.md)
