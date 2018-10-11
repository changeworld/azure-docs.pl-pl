---
title: Architektura replikacji Azure – Azure w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie składników i architektury używanych podczas replikowania maszyn wirtualnych platformy Azure między regionami platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 20387c325497934ccb2e02188b24a0125f937e00
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078261"
---
# <a name="azure-to-azure-replication-architecture"></a>Architektura replikacji Azure – Azure


W tym artykule opisano architekturę używany, gdy replikacja, tryb failover i odzyskiwanie maszyn wirtualnych (VM) między regionami platformy Azure przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md) usługi.




## <a name="architectural-components"></a>Składniki architektury

Na rysunku poniżej przedstawiono ogólny widok środowisku maszyny Wirtualnej platformy Azure, w określonym regionie (w tym przykładzie lokalizacji wschodnie stany USA). W środowisku maszyny Wirtualnej platformy Azure:
- Aplikacje mogą działać na maszynach wirtualnych z dyskami zarządzanymi lub dysków niezarządzanych rozkładają się na kontach magazynu.
- Maszyny wirtualne mogły zostać uwzględnione w co najmniej jednej podsieci w sieci wirtualnej.


**Replikacji Azure – Azure**

![środowisku klienta](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Proces replikacji

### <a name="step-1"></a>Krok 1

Po włączeniu replikacji maszyny Wirtualnej platformy Azure, następujące zasoby są tworzone automatycznie w regionie docelowym, na podstawie ustawień regionu źródłowego. Można dostosować ustawienia zasobów obiektu docelowego zgodnie z potrzebami.

![Włączanie replikacji procesu, krok 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Zasób** | **Szczegóły**
--- | ---
**Docelowa grupa zasobów** | Grupa zasobów, do której należą replikowane maszyny wirtualne, po włączeniu trybu failover. Lokalizacja ta grupa zasobów może być w dowolnym regionie systemu Azure, z wyjątkiem regionu platformy Azure, w którym znajdują się źródłowych maszyn wirtualnych.
**Docelowa sieć wirtualna** | Sieć wirtualna, w którym replikowane maszyny wirtualne znajdują się po włączeniu trybu failover. Mapowanie sieci jest tworzona między sieciami wirtualnymi na źródłowym i docelowym i na odwrót.
**Konta magazynu pamięci podręcznej** | Zanim zmiany maszyny Wirtualnej źródłowego są replikowane do docelowego konta magazynu, są śledzone i wysyłane do konta magazynu pamięci podręcznej w lokalizacji źródłowej. Ten krok zapewnia minimalny wpływ na aplikacji produkcyjnych uruchamianych na maszynie Wirtualnej.
**Docelowe konta magazynu (Jeśli źródło maszyny Wirtualnej nie korzysta z dysków zarządzanych)**  | Konta magazynu w lokalizacji docelowej, do którego dane są replikowane.
** Dyski zarządzane repliki (w przypadku dysków zarządzanych źródłowej maszyny wirtualnej) **  | Usługa Managed disks w lokalizacji docelowej, do którego dane są replikowane.
**Docelowe zestawy dostępności**  | Zestawy dostępności, której są replikowane maszyny wirtualne znajdują się po włączeniu trybu failover.

### <a name="step-2"></a>Krok 2

Ponieważ replikacja jest włączona, rozszerzenie usługę mobilności usługi Site Recovery jest automatycznie instalowany na maszynie Wirtualnej:

1. Maszyna wirtualna jest zarejestrowana przy użyciu usługi Site Recovery.

2. Ciągła replikacja jest skonfigurowana dla maszyny Wirtualnej. Opłata za zapisywanie danych na dyskach maszyn wirtualnych ciągle są przenoszone do konta magazynu pamięci podręcznej w lokalizacji źródłowej.

   ![Włączanie replikacji procesu, krok 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Usługa Site Recovery nigdy nie wymaga łączności dla ruchu przychodzącego do maszyny Wirtualnej. Tylko łączność wychodząca jest potrzebny do wykonania poniższych czynności.

 - Adresy IP/adresami URL usługi odzyskiwania lokacji
 - Adresy URL/IP uwierzytelnianie usługi Office 365
 - Adresy IP konta magazynu pamięci podręcznej

Jeśli włączono spójność między wieloma maszynami wirtualnymi, maszyny z grupy replikacji komunikują się między sobą przez port 20004. Upewnij się, że żadne urządzenie zapory nie blokuje wewnętrznej komunikacji między maszynami wirtualnymi za pośrednictwem portu 20004.

> [!IMPORTANT]
Jeśli do grupy replikacji chcesz dodać maszyny wirtualne z systemem Linux, musisz ręcznie otworzyć port 20004 dla ruchu wychodzącego zgodnie ze wskazówkami dla konkretnej wersji systemu Linux.

### <a name="step-3"></a>Krok 3

Gdy ciągłej replikacji jest w toku, zapisy na dysku natychmiast są przenoszone do konta magazynu pamięci podręcznej. Usługa Site Recovery przetwarza dane i wysyła je do obiektu docelowego konta magazynu lub replika usługi managed disks. Po przetworzeniu danych punkty odzyskiwania są generowane w docelowym koncie magazynu, co kilka minut.

## <a name="failover-process"></a>Procesu pracy awaryjnej

Po zainicjowaniu przejścia w tryb failover maszyny wirtualne są utworzone w docelowej grupie zasobów, docelowa sieć wirtualna, podsieć docelowa, a następnie w zestawie dostępności docelowego. Podczas pracy w trybie failover można używać dowolnego punktu odzyskiwania.

![Procesu pracy awaryjnej](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybkie replikowanie](azure-to-azure-quickstart.md) Maszynie wirtualnej platformy Azure do regionu pomocniczego.
