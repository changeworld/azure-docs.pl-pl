---
title: Architektura replikacji Azure do platformy Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używane podczas replikowania maszyn wirtualnych platformy Azure między regiony platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f6ade346305ee6f18fb41b93bc8a1dc8543fcdb7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="azure-to-azure-replication-architecture"></a>Architektura replikacji Azure do platformy Azure


W tym artykule opisano architekturę używane podczas replikacji, pracy awaryjnej i odzyskiwania maszyn wirtualnych platformy Azure (maszyny wirtualne) między regiony platformy Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.

>[!NOTE]
>Azure replikacji maszyny Wirtualnej za pomocą usługi Site Recovery jest obecnie w przeglądzie.



## <a name="architectural-components"></a>Składniki architektury

Poniższa ilustracja przedstawia ogólny widok środowiska maszyny Wirtualnej platformy Azure w określonym regionie (w tym przykładzie lokalizacji wschodnie stany USA). W środowisku maszyny Wirtualnej platformy Azure:
- Aplikacje mogą działać na maszynach wirtualnych z dyskami zarządzanych lub niezarządzanych dysków rozmieszczenie do kont magazynu.
- Maszyny wirtualne mogą zostać włączone w co najmniej jednej podsieci sieci wirtualnej.


**Replikacja usługi Azure do platformy Azure**

![środowisko klienta](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Proces replikacji

### <a name="step-1"></a>Krok 1

Po włączeniu replikacji maszyny Wirtualnej Azure następujące zasoby są tworzone automatycznie w obszarze docelowym na podstawie ustawień regionu źródła. Można dostosować ustawienia zasobów obiektu docelowego zgodnie z potrzebami.

![Włącz proces replikacji, krok 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Zasób** | **Szczegóły**
--- | ---
**Docelowa grupa zasobów** | Grupa zasobów replikowane maszyny wirtualne należą po pracy awaryjnej. Lokalizacja tej grupy zasobów można w dowolnym regionie Azure, z wyjątkiem region platformy Azure, w którym znajdują się maszyny wirtualne źródła.
**Docelowy sieci wirtualnej** | Sieć wirtualna, w którym replikowanych maszyn wirtualnych znajdują się po pracy awaryjnej. Mapowanie sieci jest tworzony między sieciami wirtualnymi źródłowym i docelowym i na odwrót.
**Konta magazynu pamięci podręcznej** | Replikacja zmian maszyny Wirtualnej źródłowego na docelowe konto magazynu, są śledzone i wysyłane do konta magazynu pamięci podręcznej w lokalizacji źródłowej. Ten krok zapewnia minimalny wpływ na aplikacje produkcyjne uruchomione na maszynie Wirtualnej.
**Docelowa kont magazynu (Jeśli źródło maszyny Wirtualnej nie korzysta z zarządzanego dysków)**  | Konta magazynu w lokalizacji docelowej, do którego dane są replikowane.
** Repliki dyskach zarządzanych (Jeśli źródło maszyny Wirtualnej znajduje się na dyskach zarządzanych) **  | Zarządzane dysków w lokalizacji docelowej, do którego dane są replikowane.
**Docelowy zestawów dostępności**  | Zestawy dostępności, której replikowanych maszyn wirtualnych znajdują się po pracy awaryjnej.

### <a name="step-2"></a>Krok 2

Ponieważ replikacja jest włączona, rozszerzenia usługi Site Recovery usługi mobilności jest automatycznie instalowany na Maszynie wirtualnej:

1. Maszyna wirtualna jest zarejestrowany z usługą Site Recovery.

2. Ciągła replikacja jest skonfigurowana dla maszyny Wirtualnej. Zapisuje dane na dyskach maszyny Wirtualnej stale są przenoszone do konta magazynu pamięci podręcznej, w lokalizacji źródłowej.

   ![Włącz proces replikacji, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Usługi Site Recovery, nigdy nie musi mieć łączność przychodzący do maszyny Wirtualnej. Tylko łączność wychodząca jest potrzebne do wykonania poniższych czynności.

 - Adresy IP/adresy URL usługi odzyskiwania lokacji
 - Adresy URL/IP uwierzytelnianie usługi Office 365
 - Adresy IP konta magazynu pamięci podręcznej

Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.

> [!IMPORTANT]
Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.

### <a name="step-3"></a>Krok 3

Po ciągłej replikacji jest w toku, dysku operacje zapisu są natychmiast przenoszone do konta magazynu pamięci podręcznej. Usługa Site Recovery przetwarza dane i wysyła je do obiektu docelowego konta magazynu lub repliki dyskach zarządzanych. Po przetworzeniu danych punkty odzyskiwania są generowane w docelowe konto magazynu, co kilka minut.

## <a name="failover-process"></a>Proces trybu failover

Po zainicjowaniu tryb failover maszyn wirtualnych są tworzone w docelowej sieci wirtualnej, docelowa grupa zasobów, docelowej podsieci i w zestawie dostępności docelowego. Podczas pracy awaryjnej można użyć dowolnego punktu odzyskiwania.

![Proces trybu failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybko replikować](azure-to-azure-quickstart.md) maszyny Wirtualnej platformy Azure w regionie pomocniczym.
