---
title: Niepowodzenie ponownie maszyny wirtualne platformy Azure replikowane do regionu pomocniczego platformy Azure w celu odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
description: Dowiedz się, jak zakończyć się niepowodzeniem ponownie Azure maszyn wirtualnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: tutorial
origin.date: 03/18/2019
ms.date: 04/22/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: c8ce05e644ad556542314b17151b808586734824
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118183"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Niepowodzenie ponownie Azure maszyn wirtualnych między regionami platformy Azure

[Usługi Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez organizowanie replikacji, pracy awaryjnej i kończyć się niepowodzeniem, ponownie z maszyn lokalnych i maszyn wirtualnych (VM) i zarządzanie nimi.

W tym samouczku opisano, jak ich powrotu po awarii jednej maszyny Wirtualnej platformy Azure. Po przełączeniu w tryb failover możesz przywrócić ją po awarii do regionu podstawowego, kiedy stanie się dostępny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Powrót po awarii maszyny Wirtualnej platformy Azure w regionie pomocniczym.
> * Ponownego włączenia ochrony podstawowej maszyny Wirtualnej platformy Azure do regionu pomocniczego.
> 
> [!NOTE]
> 
> Ten samouczek ułatwia do trybu failover kilka maszyn wirtualnych do regionu docelowego i powrotem do regionu źródłowego z minimalną dostosowań. Aby uzyskać więcej szczegółowych instrukcji zapoznaj się z artykułów w obszarze "How to" dla maszyn wirtualnych platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

> * Upewnij się, że maszyna wirtualna znajduje się w **tryb Failover zatwierdzony** stanu.
> * Sprawdź, czy region podstawowy jest dostępny, i możesz utworzyć i zapisać w nim nowe zasoby.
> * Upewnij się, że włączono ponownej ochrony.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownego włączenia jego ochrony maszyn wirtualnych można powrotu po awarii do regionu podstawowego zgodnie z potrzebami.

1. W magazynie kliknij **zreplikowane elementy** i wybierz maszynę Wirtualną, która była ponownego włączenia jego ochrony.

    ![Powrót po awarii do podstawowego](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Kliknij przycisk **testu pracy awaryjnej** do wykonywania testu trybu failover z powrotem do regionu podstawowego.
4. Wybierz punkt odzyskiwania i sieci wirtualnej do testowania trybu failover, a następnie kliknij przycisk **OK**. Możesz przejrzeć testów, które maszyna wirtualna utworzona w regionie podstawowym.
5. Po testowy tryb failover zakończy się pomyślnie, kliknij przycisk **wyczyść test pracy awaryjnej** aby wyczyścić zasoby utworzone w regionie źródłowym do testowania trybu failover.
6. W **zreplikowane elementy**, a następnie wybierz maszynę Wirtualną i kliknij przycisk **trybu Failover**.
7. W **trybu Failover**, wybierz punkt odzyskiwania w tryb failover.
    - **Najnowsze (ustawienie domyślne)**: Przetwarza wszystkie dane w usłudze Site Recovery i udostępnia najniższy cel punktu odzyskiwania (RPO).
    - **Najnowszy przetworzony**: Przywraca maszynę Wirtualną do ostatniego punktu odzyskiwania, które zostały przetworzone przez usługę Site Recovery.
    - **Niestandardowy**: Wprowadza tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

8. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby Usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Należy pamiętać, że usługa Site Recovery nie usuwaj źródła po włączeniu trybu failover.
9. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.
10. Po przejściu w tryb failover Zweryfikuj maszynę Wirtualną, logując się do niego. Można zmienić punktu odzyskiwania, zgodnie z potrzebami.
11. Po zweryfikowaniu przełączenie w tryb failover, kliknij przycisk **zatwierdzić tryb failover**. Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania. Zmiana opcji punktu odzyskiwania nie jest już dostępna.
12. Powinny być widoczne jako przełączone w tryb failover i powrót po awarii maszyny Wirtualnej.

    ![Maszyn wirtualnych na region podstawowy i pomocniczy](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Odzyskiwanie po awarii maszyn wirtualnych będą nadal w zamknięcia stanu z cofniętą alokacją. To jest celowe, ponieważ usługa Site Recovery zapisuje informacje maszyn wirtualnych, które mogą być przydatne dla trybu failover z serwera podstawowego do regionu pomocniczego później. Cofnięto alokację maszyny wirtualne, nie są opłata, więc powinny być przechowywane, są one.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownego włączania ochrony.

<!--Update_Description: new articles on azure to azure tutorial failback -->
<!--ms.date: 04/22/2019-->