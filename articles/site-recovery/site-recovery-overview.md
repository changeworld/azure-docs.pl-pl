---
title: Informacje o usłudze Azure Site Recovery | Microsoft Docs
description: Zawiera omówienie usługi Azure Site Recovery oraz podsumowanie scenariuszy odzyskiwania po awarii i wdrażania migracji.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 907d12464156f551930098f6bd6a6a24596307cb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479921"
---
# <a name="about-site-recovery"></a>Informacje o usłudze Site Recovery

Usługa Azure Site Recovery — Zapraszamy! Ten artykuł zawiera krótkie omówienie usługi.

W przypadku organizacji konieczne jest zastosowanie strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) zapewniającej bezpieczeństwo danych i dostępność aplikacji oraz obciążeń w przypadku wystąpienia planowanych i nieplanowanych przestojów.

W celu realizacji strategii BCDR można wykorzystać usługi Azure Recovery Services:

- **Usługa Site Recovery**: Usługa Site Recovery pomaga zapewnić ciągłość działalności biznesowej dzięki utrzymywaniu działania aplikacji biznesowych i obciążeń podczas awarii. Usługa Site Recovery replikuje obciążenia uruchomione na maszynach fizycznych i wirtualnych z lokacji podstawowej do lokacji dodatkowej. W przypadku awarii w lokacji głównej ma miejsce przełączenie w tryb failover do lokacji dodatkowej, z poziomu której możliwy jest dostęp do aplikacji. Po ponownym uruchomieniu lokacji głównej możliwy jest do niej powrót po awarii.  
- **Usługa Backup**: Usługa [Azure Backup](https://docs.microsoft.com/azure/backup/) utrzymuje bezpieczeństwo i możliwość odzyskiwania danych dzięki tworzeniu ich kopii zapasowej na platformie Azure.

Usługa Site Recovery może zarządzać replikacją dla:

- Maszyn wirtualnych platformy Azure replikowanych między regionami świadczenia usługi Azure.
- Lokalne maszyny wirtualne, maszyny wirtualne usługi Azure Stack i serwery fizyczne.


## <a name="what-does-site-recovery-provide"></a>Co umożliwia usługa Site Recovery?


**Funkcja** | **Szczegóły**
--- | ---
**Proste rozwiązanie BCDR** | Korzystając z usługi Site Recovery, z jednej lokalizacji w portalu Azure można skonfigurować replikację, pracę w trybie failover i powrót po awarii oraz nimi zarządzać.
**Replikacja maszyny wirtualnej platformy Azure** | Odzyskiwanie po awarii maszyn wirtualnych platformy Azure można skonfigurować z regionu podstawowego do regionu dodatkowego.
**Replikacja lokalnej maszyny wirtualnej** | Lokalne maszyny wirtualne i serwery fizyczne można replikować do platformy Azure lub do dodatkowego lokalnego centrum danych. Replikacja do platformy Azure pozwala wyeliminować koszty i złożoność związane z utrzymywaniem dodatkowego centrum danych.
**Replikacja obciążenia** | Możliwe jest replikowanie dowolnego obciążenia uruchomionego na obsługiwanych maszynach wirtualnych platformy Azure, lokalnych maszynach wirtualnych funkcji Hyper-V i programu VMware oraz serwerach fizycznych z systemem Windows bądź Linux.
**Odporność danych** | Usługa Site Recovery organizuje replikację, ale nie przechwytuje danych aplikacji. Podczas replikowania na platformę Azure dane są przechowywane w usłudze Azure Storage, która zapewnia odpowiednią odporność. W przypadku przejścia w tryb failover ma miejsce tworzenie maszyn wirtualnych platformy Azure na podstawie replikowanych danych.
**Cel czasu odzyskiwania i cel punktu odzyskiwania** | Cele czasu odzyskiwania (RTO, recovery time objectives) i cele punktu odzyskiwania (RPO, recovery point objectives) należy utrzymywać w ramach limitów organizacji. Usługa Site Recovery umożliwia przeprowadzanie replikacji ciągłej w przypadku maszyn wirtualnych platformy Azure oraz maszyn wirtualnych programu VMware oraz replikacji nawet co 30 sekund w przypadku funkcji Hyper-V. Cele czasu odzyskiwania można zmniejszyć jeszcze bardziej dzięki integracji z usługą [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Utrzymywanie spójności aplikacji w trybie failover** | Możliwa jest replikacja za pomocą punktów odzyskiwania z migawkami spójnymi z aplikacjami. Takie migawki przechwytują dane na dysku, wszystkie dane w pamięci oraz wszystkie trwające transakcje.
**Przeprowadzanie testów bez zakłóceń** | W łatwy sposób można przeprowadzać próbne operacje odzyskiwania po awarii bez wpływu na trwającą replikację.
**Elastyczne przechodzenie w tryb failover** | W przypadku przewidywanych przerw w działaniu można uruchomić planowane tryby failover bez utraty danych, a w przypadku nieoczekiwanych awarii — nieplanowane tryby failover, które mogą wiązać się z minimalną utratą danych (zależnie od częstotliwości replikacji). Kiedy lokacja główna będzie znowu dostępna, można łatwo do niej wrócić.
**Dostosowane plany odzyskiwania** | Korzystając z planów odzyskiwania można dostosować i uszeregować przechodzenie w tryb failover i odzyskiwanie aplikacji wielowarstwowych działających na wielu maszynach wirtualnych. Maszyny można zgrupować w ramach planu odzyskiwania, a następnie opcjonalnie dodać skrypty i działania ręczne. Plany odzyskiwania można integrować z elementami runbook usługi Azure Automation.
**Integracja BCDR** | Usługa Site Recovery integruje się z innymi technologiami BCDR. Usługi Site Recovery można na przykład używać, aby chronić zaplecze obciążeń firmowych programu SQL Server. Usługa ta oferuje natywne wsparcie funkcji AlwaysOn programu SQL Server służące do zarządzanie trybem failover dla grup dostępności.
**Integracja usługi Azure Automation** | Bogata biblioteka usługi Azure Automation zapewnia gotowe do produkcji skrypty dopasowane do danych aplikacji, które można pobrać i zintegrować z usługą Site Recovery.
**Integracja sieci** | Usługa Site Recovery integruje się z platformą Azure, upraszczając zarządzanie siecią na potrzeby aplikacji, w tym rezerwowanie adresów IP, konfigurowanie równoważenia obciążenia i integrowanie usługi Azure Traffic Manager w celu zapewnienia efektywnych przełączeń sieci.


## <a name="what-can-i-replicate"></a>Co mogę replikować?

**Obsługiwane** | **Szczegóły**
--- | ---
**Scenariusze replikacji** | Replikowanie maszyn wirtualnych platformy Azure między regionami platformy Azure.<br/><br/>  Replikowanie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V, serwerów fizycznych (z systemami Windows i Linux) oraz maszyn wirtualnych usługi Azure Stack do platformy Azure.<br/><br/> <br/><br/> Replikacja wystąpień usługi AWS Windows na platformie Azure.<br/><br/> Replikowanie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V zarządzanych przez program System Center VMM oraz serwerów fizycznych do lokacji dodatkowej.
**Regiony** | Przejrzyj [regiony obsługiwane](https://azure.microsoft.com/regions/services/) na potrzeby usługi Site Recovery. |
**Zreplikowane maszyny** | Przejrzyj wymagania dotyczące replikacji [maszyn wirtualnych platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych](vmware-physical-azure-support-matrix.md#replicated-machines) oraz [lokalnych maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms).
**Obciążenia** | Możesz replikować dowolne obciążenia uruchomione na maszynie, która jest obsługiwana na potrzeby replikacji. Dodatkowo zespół usługi Site Recovery wykonał specyficzne dla aplikacji testowanie na potrzeby [wielu aplikacji](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [obsłudze obciążeń](site-recovery-workload.md).
* Zapoznaj się z wprowadzeniem do [replikacji maszyn wirtualnych platformy Azure między regionami](azure-to-azure-quickstart.md). 
