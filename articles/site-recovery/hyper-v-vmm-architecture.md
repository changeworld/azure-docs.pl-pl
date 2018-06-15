---
title: Replikacji funkcji Hyper-V do lokacji dodatkowej architektury w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie architektury używanej podczas replikowania lokalnych maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej z programem System Center VMM za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 39a397edd17327a91882535fbd00222a4ae4dddc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894300"
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Replikacji funkcji Hyper-V do lokacji dodatkowej

Ten artykuł zawiera opis składników i procesów związanych z replikacją lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej z programem VMM za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) w witrynie Azure Portal.


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafiki zapewnia widok ogólny składników używanych w przypadku replikacji funkcji Hyper-V z lokacją dodatkową.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure | Magazyn usługi Recovery Services tworzy się w subskrypcji platformy Azure, aby organizować replikację między lokalizacjami z programem VMM i nią zarządzać.
**Serwer VMM** | Potrzebujesz podstawowej i dodatkowej lokalizacji z programem VMM. | Zalecamy, aby jeden serwer VMM znajdował się w lokacji głównej, a jeden w lokacji dodatkowej.
**Serwer funkcji Hyper-V** |  Co najmniej jeden serwer hosta funkcji Hyper-V w głównych i dodatkowych chmurach programu VMM. | Dane są replikowane między głównymi i dodatkowymi serwerami hosta funkcji Hyper-V za pośrednictwem sieci LAN albo sieci VPN korzystającej z protokołu Kerberos lub uwierzytelniania certyfikatu.  
**Maszyny wirtualne funkcji Hyper-V** | Na serwerze hosta funkcji Hyper-V. | Źródłowy serwer hosta powinien mieć co najmniej jedną maszynę wirtualną, która ma być replikowana.

**Dane lokalne na lokalnej architektury**

![Ze środowiska lokalnego do środowiska lokalnego](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikacji

1. Po wyzwoleniu replikacji początkowej [migawki maszyny Wirtualnej funkcji Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) migawki.
2. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane pojedynczo, do lokalizacji dodatkowej.
3. Jeśli zmiany dysku są wykonywane, gdy Replikacja początkowa jest w toku. 
4. Po zakończeniu replikacji początkowej, rozpoczyna się replikacja różnicowa. Śledzenie replikacji repliki funkcji Hyper-V śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl wysyłanym do lokalizacji dodatkowej. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

- W tryb failover jednego komputera lub utworzyć plany odzyskiwania, aby organizować tryb failover wiele maszyn.
- Można uruchomić planowanego lub nieplanowanego trybu failover między lokacjami lokalnymi. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
    - W przypadku nieplanowanego trybu failover do lokacji dodatkowej po maszyny pracy awaryjnej w dodatkowej lokalizacji nie są chronione.
    - Jeśli dokonano planowanego przejścia w tryb failover, po przejściu w tryb failover maszyny znajdujące się w lokalizacji dodatkowej są chronione.
- Po początkowej przejście w tryb failover działa, Zatwierdź go uruchomić, uzyskiwanie dostępu do obciążenia z repliki maszyny Wirtualnej.
- W przypadku lokalizacji głównej jest ponownie dostępny, możesz się niepowodzeniem ponownie.
    - Inicjuje replikację odwrotną do rozpoczęcia replikacji z lokacji dodatkowej do podstawowych. Replikacja odwrotna powoduje przełączenie maszyn wirtualnych do stanu chronionego, ale aktywną lokalizacją jest nadal dodatkowe centrum danych.
    - Aby lokacja główna stała się z powrotem lokalizacją aktywną, należy zainicjować planowane przejście w tryb failover z lokacji dodatkowej do głównej, a następnie przeprowadzić kolejną replikację odwrotną.



## <a name="next-steps"></a>Kolejne kroki


Postępuj zgodnie z [w tym samouczku](hyper-v-vmm-disaster-recovery.md) Aby włączyć replikację funkcji Hyper-V między chmurami VMM.
