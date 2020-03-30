---
title: Odzyskiwanie po awarii funkcji Architektura-Hyper-V w lokacji dodatkowej za pomocą usługi Azure Site Recovery
description: Ten artykuł zawiera omówienie architektury odzyskiwania po awarii lokalnych maszyn wirtualnych z programem Hyper-V do dodatkowej witryny programu VMM centrum systemu z usługą Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133009"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura — replikacja funkcji Hyper-V do lokacji dodatkowej

Ten artykuł zawiera opis składników i procesów związanych z replikacją lokalnych maszyn wirtualnych funkcji Hyper-V w chmurach programu System Center Virtual Machine Manager (VMM) do lokacji dodatkowej z programem VMM za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) w witrynie Azure Portal.
a

## <a name="architectural-components"></a>Składniki architektury

Poniższa tabela i grafika zapewniają widok wysokiego poziomu składników używanych do replikacji funkcji Hyper-V do lokacji dodatkowej.

**Składnik** | **Wymaganie** | **Szczegóły**
--- | --- | ---
**Azure** | Subskrypcja platformy Azure | Magazyn usługi Recovery Services tworzy się w subskrypcji platformy Azure, aby organizować replikację między lokalizacjami z programem VMM i nią zarządzać.
**Serwer VMM** | Potrzebujesz podstawowej i dodatkowej lokalizacji z programem VMM. | Zalecamy, aby jeden serwer VMM znajdował się w lokacji głównej, a jeden w lokacji dodatkowej.
**Serwer funkcji Hyper-V** |  Co najmniej jeden serwer hosta funkcji Hyper-V w głównych i dodatkowych chmurach programu VMM. | Dane są replikowane między głównymi i dodatkowymi serwerami hosta funkcji Hyper-V za pośrednictwem sieci LAN albo sieci VPN korzystającej z protokołu Kerberos lub uwierzytelniania certyfikatu.  
**Maszyny wirtualne funkcji Hyper-V** | Na serwerze hosta funkcji Hyper-V. | Źródłowy serwer hosta powinien mieć co najmniej jedną maszynę wirtualną, która ma być replikowana.

**Lokalna architektura lokalna**

![Ze środowiska lokalnego do środowiska lokalnego](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikacji

1. Po wyzwoleniu replikacji początkowej jest pobierana migawka [migawki maszyny Wirtualnej funkcji Funkcji Hyper V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Wirtualne dyski twarde na maszynie wirtualnej są replikowane jeden po drugim do lokalizacji pomocniczej.
3. Jeśli zmiany dysku wystąpią podczas replikacji początkowej, moduł śledzenia replik repliki funkcji Hyper-V śledzi zmiany jako dzienniki replikacji funkcji Hyper-V (hrl). Te pliki dziennika znajdują się w tym samym folderze co dyski. Każdy dysk ma skojarzony plik hrl, który jest wysyłany do lokalizacji pomocniczej. Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny Wirtualnej jest usuwana i rozpoczyna się replikacja delta.
5. Różnicowe zmiany dysku w dzienniku są synchronizowane i scalane z dyskiem nadrzędnym.


## <a name="failover-and-failback-process"></a>Proces pracy w trybie failover i podczas powrotu po awarii

- W tryb failover można przełączyć pojedynczą maszynę lub można utworzyć plany odzyskiwania, aby zaaranżować tryb failover na wielu maszynach.
- Planowane lub nieplanowane przejście w tryb failover można uruchomić między lokacjami lokalnymi. Jeśli zostanie uruchomione planowane przejście w tryb failover, źródłowe maszyny wirtualne zostaną wyłączone w celu zapewnienia, że nie będzie miała miejsca utrata danych.
    - Jeśli nieplanowana funkcja failover zostanie wykonynana lokacji dodatkowej, po tym, jak maszyny trybu failover w lokalizacji dodatkowej nie są chronione.
    - Jeśli dokonano planowanego przejścia w tryb failover, po przejściu w tryb failover maszyny znajdujące się w lokalizacji dodatkowej są chronione.
- Po uruchomieniu początkowej pracy awaryjnej, należy zatwierdzić go, aby rozpocząć dostęp do obciążenia z repliki maszyny Wirtualnej.
- Gdy lokalizacja podstawowa jest ponownie dostępna, można przywrócić po awarii.
    - Zainicjować replikację odwrotną, aby rozpocząć replikację z lokacji dodatkowej do podstawowej. Replikacja odwrotna powoduje przełączenie maszyn wirtualnych do stanu chronionego, ale aktywną lokalizacją jest nadal dodatkowe centrum danych.
    - Aby lokacja główna stała się z powrotem lokalizacją aktywną, należy zainicjować planowane przejście w tryb failover z lokacji dodatkowej do głównej, a następnie przeprowadzić kolejną replikację odwrotną.



## <a name="next-steps"></a>Następne kroki


Wykonaj [ten samouczek,](hyper-v-vmm-disaster-recovery.md) aby włączyć replikację funkcji Hyper-V między chmurami programu VMM.
