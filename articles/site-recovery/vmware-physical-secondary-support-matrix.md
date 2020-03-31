---
title: Obsługa odzyskiwania po awarii VMware/fizycznej w lokacji dodatkowej za pomocą usługi Azure Site Recovery
description: Podsumowuje obsługę odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych do lokacji dodatkowej za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256798"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Macierz obsługi odzyskiwania po awarii maszyn wirtualnych vmware i serwerów fizycznych do lokacji dodatkowej

W tym artykule podsumowano, co jest obsługiwane podczas korzystania z usługi [Azure Site Recovery](site-recovery-overview.md) do odzyskiwania po awarii vmware maszyn wirtualnych lub serwerów fizycznych systemu Windows/Linux do dodatkowej lokacji VMware.

- Jeśli chcesz replikować maszyny wirtualne VMware lub serwery fizyczne na platformie Azure, zapoznaj się z [tą macierzą pomocy technicznej](vmware-physical-azure-support-matrix.md).
- Jeśli chcesz replikować maszyny wirtualne z programem Hyper-V do lokacji dodatkowej, zapoznaj się z [tą macierzą pomocy technicznej](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikacja lokalnych maszyn wirtualnych VMware i serwerów fizycznych jest dostarczana przez program InMage Scout. InMage Scout jest uwzględniony w subskrypcji usługi Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Ogłoszenie końca wsparcia
Scenariusz odzyskiwania lokacji dla replikacji między lokalnymi vmware lub fizycznych centrów danych osiąga koniec pomocy technicznej.

- Od sierpnia 2018 r. scenariusza nie można skonfigurować w magazynie usług odzyskiwania, a oprogramowania InMage Scout nie można pobrać z magazynu. Obsługiwane będą istniejące wdrożenia.
- - Od 31 grudnia 2020 r. scenariusz nie będzie obsługiwany.
Obecni partnerzy mogą dołączać nowych klientów do scenariusza do momentu zakończenia pomocy technicznej.
- W 2018 i 2019 roku zostaną wydane dwie aktualizacje:

    - Aktualizacja 7: Rozwiązuje problemy z konfiguracją sieci i zgodnością i zapewnia obsługę protokołu TLS 1.2.
    - Aktualizacja 8: Dodaje obsługę systemów operacyjnych Linux RHEL/CentOS 7.3/7.4/7.5 oraz dla SUSE 12
    - Po aktualizacji 8 nie zostaną wydane żadne dalsze aktualizacje. Obsługa poprawek dla systemów operacyjnych dodanych w aktualizacji 8 będzie ograniczona, a poprawki błędów będą oparte na najlepszych działaniach.

## <a name="host-servers"></a>Serwery hostów

**System operacyjny** | **Szczegóły**
--- | ---
Serwer vCenter | vCentrer 5.5, 6.0 i 6.5<br/><br/> Jeśli uruchomisz 6.0 lub 6.5, należy pamiętać, że tylko 5.5 funkcje są obsługiwane.


## <a name="replicated-vm-support"></a>Obsługa replikowanej maszyny Wirtualnej

W poniższej tabeli podsumowano obsługę systemu operacyjnego dla komputerów replikowanych za pomocą funkcji Odzysk lokacji. Każde obciążenie może być uruchomione w obsługiwanym systemie operacyjnym.

**System operacyjny** | **Szczegóły**
--- | ---
Windows Server | 64-bitowe systemy Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 z co najmniej sp1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 z jądrem zgodnym z Red Hat lub Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Tak 
SMB 3.0 | Nie dotyczy 
SAN (ISCSI) | Tak 
Wielobiegowa (MPIO) | Tak 

### <a name="guest-or-physical-server-storage"></a>Magazyn serwera gościa lub serwera fizycznego

**Konfiguracja** | **Obsługiwane** 
--- | --- 
Vmdk | Tak 
VHD/VHDX | Nie dotyczy 
Maszyna wirtualna gen 2 | Nie dotyczy 
Udostępniony dysk klastrowy | Tak 
Zaszyfrowany dysk | Nie 
Uefi| Tak 
NFS | Nie 
SMB 3.0 | Nie 
Rdm | Tak 
Dysk > 1 TB | Tak 
Wolumin z rozłożonym dyskiem > 1 TB<br/><br/> Lvm | Tak 
Miejsca do magazynowania | Nie 
Gorący dodawanie/usuwanie dysku | Tak 
Wykluczanie dysku | Tak 
Wielobiegowa (MPIO) | Nie dotyczy 

## <a name="vaults"></a>Magazyny

**Akcja** | **Obsługiwane** 
--- | --- 
Przenoszenie magazynów między grupami zasobów (w ramach subskrypcji lub między nimi) | Nie 
Przenoszenie magazynu, sieci, maszyn wirtualnych platformy Azure między grupami zasobów (w ramach subskrypcji lub między nimi) | Nie 

## <a name="mobility-service-and-updates"></a>Usługi i aktualizacje dotyczące mobilności

Usługa mobilności koordynuje replikację między lokalnymi serwerami VMware lub serwerami fizycznymi a lokacją dodatkową. Podczas konfigurowania replikacji należy upewnić się, że masz najnowszą wersję usługi mobilności i innych składników.

| **Aktualizacja** | **Szczegóły** |
| --- | --- |
|Aktualizacje zwiadowcy | Aktualizacje zwiadowcze kumulują się. <br/><br/> [Dowiedz się więcej o](vmware-physical-secondary-disaster-recovery.md#updates) najnowszych aktualizacjach Scout i pobierz je |
|Aktualizacje składników | Aktualizacje scout obejmują aktualizacje dla wszystkich składników, w tym serwera RX, serwera konfiguracji, serwerów docelowych procesu i głównego, serwerów vContinuum i serwerów źródłowych, które chcesz chronić.<br/><br/> [Dowiedz się więcej](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Następne kroki

Pobierz [podręcznik użytkownika InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikowanie maszyn wirtualnych funkcji Hyper V w chmurach programu VMM do lokacji dodatkowej](tutorial-vmm-to-vmm.md)
- [Replikacja maszyn wirtualnych VMware i serwerów fizycznych do lokacji dodatkowej](tutorial-vmware-to-vmware.md)
