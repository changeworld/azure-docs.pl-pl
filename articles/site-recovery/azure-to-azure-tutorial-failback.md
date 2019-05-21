---
title: Niepowodzenie ponownie maszyny wirtualne platformy Azure replikowane do regionu pomocniczego platformy Azure w celu odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
description: Dowiedz się, jak zakończyć się niepowodzeniem ponownie Azure maszyn wirtualnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 339cbf18d79053dfb0704d928b8c9251c73b3a6e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951791"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Powrót po awarii maszyny Wirtualnej platformy Azure między regionami platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych oraz maszyn wirtualnych platformy Azure, a także orkiestrację tych procesów.

W tym samouczku opisano, jak ich powrotu po awarii jednej maszyny Wirtualnej platformy Azure. Po przełączeniu w tryb failover, należy powrotu po awarii do regionu podstawowego, gdy jest ona dostępna. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Powrót po awarii maszyn wirtualnych w regionie pomocniczym.
> * Ponowne włączanie ochrony podstawowej maszyny Wirtualnej do regionu pomocniczego.
> 
> [!NOTE]
> 
> Ten samouczek ułatwia do trybu failover kilka maszyn wirtualnych do regionu docelowego i powrotem do regionu źródłowego z minimalną dostosowań. Aby uzyskać więcej szczegółowych instrukcji, przejrzyj [instrukcje przeprowadzi na maszynach wirtualnych Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Zanim rozpoczniesz

* Upewnij się, że stan maszyny Wirtualnej to **tryb Failover zatwierdzony**.
* Sprawdź, czy region podstawowy jest dostępny i że jesteś w stanie utworzyć i zapisać w nim nowe zasoby.
* Upewnij się, że włączono tego ponownego włączania ochrony.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownym włączeniu ochrony maszyn wirtualnych, można powrotu po awarii do regionu podstawowego zgodnie z potrzebami.

1. W magazynie, wybierz **zreplikowane elementy**, a następnie wybierz maszynę Wirtualną, która została przełączona do trybu.

    ![Powrót po awarii do podstawowego](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Wybierz **testowanie trybu failover** do wykonywania testu trybu failover z powrotem do regionu podstawowego.
4. Wybierz punkt odzyskiwania i sieci wirtualnej do testowania trybu failover, a następnie wybierz pozycję **OK**. Możesz przejrzeć testów, które maszyna wirtualna utworzona w regionie podstawowym.
5. Po testowy tryb failover zakończy się pomyślnie, wybierz **wyczyść test pracy awaryjnej** aby wyczyścić zasoby utworzone w regionie źródłowym do testowania trybu failover.
6. W **zreplikowane elementy**, wybierz maszynę Wirtualną, a następnie wybierz **trybu Failover**.
7. W **trybu Failover**, wybierz punkt odzyskiwania do trybu failover:
    - **Najnowsze (ustawienie domyślne)**: Przetwarza wszystkie dane w usłudze Site Recovery i udostępnia najniższy cel punktu odzyskiwania (RPO).
    - **Najnowszy przetworzony**: Przywraca maszynę Wirtualną do ostatniego punktu odzyskiwania, które zostały przetworzone przez usługę Site Recovery.
    - **Niestandardowy**: Wprowadza tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

8. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** chcącym Site Recovery ma spróbować zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączenie w tryb failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Należy pamiętać, że usługa Site Recovery nie usuwaj źródła po włączeniu trybu failover.
9. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.
10. Po zakończeniu pracy w trybie failover Zweryfikuj maszynę Wirtualną, logując się do niego. Można zmienić punktu odzyskiwania, zgodnie z potrzebami.
11. Po zweryfikowaniu przełączenie w tryb failover, wybierz **zatwierdzić tryb failover**. Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania. Zmiana opcji punktu odzyskiwania nie jest już dostępna.
12. Powinny być widoczne jako przełączone w tryb failover i powrót po awarii maszyny Wirtualnej.

    ![Maszyn wirtualnych na region podstawowy i pomocniczy](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Odzyskiwanie po awarii maszyn wirtualnych będą nadal w stanie zamknięcie/z cofniętą alokacją. To jest celowe, ponieważ usługa Site Recovery zapisuje informacje maszyn wirtualnych, które mogą być przydatne dla trybu failover z serwera podstawowego do regionu pomocniczego później. Cofnięto alokację maszyny wirtualne, nie są opłata, więc powinny być przechowywane, są one.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownego włączania ochrony.
