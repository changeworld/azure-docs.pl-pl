---
title: Tryb failover i ponownie włączyć ochronę maszyn wirtualnych platformy Azure replikowane do regionu pomocniczego platformy Azure w celu odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
description: Dowiedz się, jak w trybie Failover i ponownie włączyć ochronę maszyn wirtualnych platformy Azure replikowane do regionu pomocniczego platformy Azure do odzyskiwania po awarii, za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 96e3c0b761a9ed4c5f84d8ece1ba504bd5aacf6f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257761"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Tryb failover i ponownie włączyć ochronę maszyn wirtualnych platformy Azure między regionami

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku opisano sposób awaryjnego przełączania maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure. Po przełączeniu w tryb failover, należy ponownie maszynę Wirtualną włączyć ochronę. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przełączanie w tryb failover maszyny wirtualnej platformy Azure
> * Ponowne włączanie ochrony pomocniczej maszyny Wirtualnej platformy Azure w celu replikowania jej do regionu podstawowego.

> [!NOTE]
> Ten samouczek zawiera to najprostsza ścieżka przy użyciu domyślnych ustawień oraz dostosowywania minimalnej. W przypadku bardziej złożonych scenariuszy należy użyć tych artykułów, w obszarze "How to" maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończenie [próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Sprawdzenie właściwości maszyny wirtualnej przed uruchomieniem testu trybu failover. Maszyna wirtualna musi być zgodna z [wymaganiami platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Uruchamianie trybu failover w regionie pomocniczym

1. W obszarze **Replikowane elementy** wybierz maszynę wirtualną, która ma być przełączona w tryb failover, a następnie wybierz pozycję **Tryb failover**

   ![Tryb failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:

   * **Najnowszy** (domyślna): Przetwarza wszystkie dane w usłudze Site Recovery i udostępnia najniższy cel punktu odzyskiwania (RPO).
   * **Najnowszy przetworzony**: Przywraca maszynę wirtualną do ostatniego punktu odzyskiwania, które zostały przetworzone przez usługę Site Recovery.
   * **Niestandardowy**: Wprowadza tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

3. Wybierz **Zamknij maszynę przed rozpoczęciem pracy awaryjnej** Jeśli chcesz, aby Usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Usługa Site Recovery nie usuwaj źródła po włączeniu trybu failover.

4. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.

5. Po przejściu w tryb failover zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przejść do innego punktu odzyskiwania dla maszyny wirtualnej, możesz użyć opcji **Zmień punkt odzyskiwania**.

6. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover.
   Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze. Obecnie nie można zmienić punktu odzyskiwania.

## <a name="reprotect-the-secondary-vm"></a>Ponowne włączanie ochrony pomocniczej maszyny wirtualnej

Po przełączeniu maszyny wirtualnej w tryb failover należy ponownie włączyć jej ochronę, aby ponownie zreplikować ją do regionu podstawowego.

1. Upewnij się, że maszyna wirtualna jest w stanie **Tryb failover zatwierdzony**, a następnie sprawdź, czy region podstawowy jest dostępny oraz czy możesz utworzyć w nim nowe zasoby i uzyskać do nich dostęp.
2. W obszarze **Magazyn** > **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, która została przełączona w tryb failover, a następnie wybierz pozycję **Ponownie włącz ochronę**.

   ![Kliknięcie prawym przyciskiem myszy w celu ponownego włączenia ochrony](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Sprawdź, że kierunek ochrony z regionu pomocniczego do regionu podstawowego, jest zaznaczona.
3. Przejrzyj informacje o **grupie zasobów, sieci, magazynie i zestawach dostępności**. Wszystkie zasoby oznaczone jako nowe są tworzone w ramach operacji ponownej ochrony.
4. Kliknij przycisk **OK**, aby wyzwolić zadanie włączania ponownej ochrony. To zadanie inicjuje lokację docelową przy użyciu najnowszych danych. Następnie replikuje zmiany do regionu podstawowego. Maszyna wirtualna jest teraz w stanie chronionym.

## <a name="next-steps"></a>Kolejne kroki
- Po ponowne włączanie ochrony [Dowiedz się, jak](azure-to-azure-tutorial-failback.md) powracać po awarii do regionu podstawowego, gdy będzie ona dostępna.
- [Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownego włączania ochrony.
