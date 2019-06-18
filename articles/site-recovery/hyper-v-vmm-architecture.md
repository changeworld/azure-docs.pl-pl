---
title: Architektura funkcji Hyper-V odzyskiwania po awarii do dodatkowej lokacji przy użyciu usługi Azure Site Recovery lokalnej | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie architektury do odzyskiwania po awarii lokalnych maszyn wirtualnych z funkcją Hyper-V do lokacji dodatkowej programu System Center VMM za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 22f21f11b0c374724bc6924f30ea20a21de6ab90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66398173"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura — replikacja funkcji Hyper-V do lokacji dodatkowej

Ten artykuł zawiera opis składników i procesów związanych z replikacją lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej z programem VMM za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) w witrynie Azure Portal.


## <a name="architectural-components"></a>Składniki architektury

Poniższej tabeli i grafika przedstawia ogólny widok składniki używane na potrzeby replikacji funkcji Hyper-V do lokacji dodatkowej.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure | Magazyn usługi Recovery Services tworzy się w subskrypcji platformy Azure, aby organizować replikację między lokalizacjami z programem VMM i nią zarządzać.
**Serwer VMM** | Potrzebujesz podstawowej i dodatkowej lokalizacji z programem VMM. | Zalecamy, aby jeden serwer VMM znajdował się w lokacji głównej, a jeden w lokacji dodatkowej.
**Serwer funkcji Hyper-V** |  Co najmniej jeden serwer hosta funkcji Hyper-V w głównych i dodatkowych chmurach programu VMM. | Dane są replikowane między głównymi i dodatkowymi serwerami hosta funkcji Hyper-V za pośrednictwem sieci LAN albo sieci VPN korzystającej z protokołu Kerberos lub uwierzytelniania certyfikatu.  
**Maszyny wirtualne funkcji Hyper-V** | Na serwerze hosta funkcji Hyper-V. | Źródłowy serwer hosta powinien mieć co najmniej jedną maszynę wirtualną, która ma być replikowana.

**W środowisku lokalnym do architektury w środowisku lokalnym**

![Ze środowiska lokalnego do środowiska lokalnego](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikacji

1. Po wyzwoleniu replikacji początkowej [migawki maszyny Wirtualnej funkcji Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) migawki.
2. Wirtualne dyski twarde na maszynie Wirtualnej są replikowane pojedynczo, do lokalizacji dodatkowej.
3. Jeśli zmiany dysku podczas replikacji początkowej jest w toku, funkcji Hyper-V Replica Replication Tracker śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl wysyłanym do dodatkowej lokalizacji. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny Wirtualnej zostanie usunięta, a następnie rozpoczyna się replikacja różnicowa.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

- W trybie Failover pojedynczą maszynę lub tworzyć plany odzyskiwania, aby organizować tryb failover wiele maszyn.
- Możesz uruchomić planowanego lub nieplanowanego trybu failover między lokacjami lokalnymi. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
    - Jeśli nieplanowany tryb failover do lokacji dodatkowej, należy wykonać po trybu failover maszyny znajdujące się w lokalizacji dodatkowej nie są chronione.
    - Jeśli dokonano planowanego przejścia w tryb failover, po przejściu w tryb failover maszyny znajdujące się w lokalizacji dodatkowej są chronione.
- Po przeprowadzeniu początkowego przełączenie w tryb failover Zatwierdź je, aby można było rozpocząć uzyskiwanie dostępu do obciążenia z poziomu repliki maszyny Wirtualnej.
- Jeśli lokalizacji głównej będzie znowu dostępna, należy można wykonać powrotu po awarii.
    - Należy zainicjować replikację odwrotną, można uruchomić replikacji z lokacji dodatkowej do podstawowej. Replikacja odwrotna powoduje przełączenie maszyn wirtualnych do stanu chronionego, ale aktywną lokalizacją jest nadal dodatkowe centrum danych.
    - Aby lokacja główna stała się z powrotem lokalizacją aktywną, należy zainicjować planowane przejście w tryb failover z lokacji dodatkowej do głównej, a następnie przeprowadzić kolejną replikację odwrotną.



## <a name="next-steps"></a>Kolejne kroki


Postępuj zgodnie z [w tym samouczku](hyper-v-vmm-disaster-recovery.md) Aby włączyć replikację funkcji Hyper-V między chmurami programu VMM.
