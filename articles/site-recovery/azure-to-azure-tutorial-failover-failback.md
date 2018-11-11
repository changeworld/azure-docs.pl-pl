---
title: Przełączanie w tryb failover i powrót po awarii maszyn wirtualnych IaaS platformy Azure replikowanych do dodatkowego regionu świadczenia usługi Azure na potrzeby odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
description: Dowiedz się, jak przełączać w tryb failover i przywracać po awarii maszyny wirtualne platformy Azure replikowane do dodatkowego regionu świadczenia usługi Azure na potrzeby odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9917656a3bc8fd8cfe7d71786dc39d296a2449c1
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239291"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Przełączanie maszyn wirtualnych platformy Azure w tryb failover i przywracanie po awarii między regionami świadczenia usługi Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku opisano sposób przełączania w tryb failover jednej maszyny wirtualnej platformy Azure do pomocniczego regionu świadczenia usługi Azure. Po przełączeniu w tryb failover możesz przywrócić ją po awarii do regionu podstawowego, kiedy stanie się dostępny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przełączanie w tryb failover maszyny wirtualnej platformy Azure
> * Ponowne włączanie ochrony pomocniczej maszyny wirtualnej platformy Azure w celu replikowania jej do regionu podstawowego
> * Powrót po awarii pomocniczej maszyny wirtualnej
> * Ponowne włączanie ochrony podstawowej maszyny wirtualnej w regionie pomocniczym

> [!NOTE]
> Ten samouczek prowadzi użytkownika przez kroki procedury przełączenia w tryb failover do regionu docelowego i z powrotem przy minimalnym dostosowaniu. Jeśli chcesz dowiedzieć się więcej o różnych aspektach związanych z trybem failover, w tym o zagadnieniach dotyczących sieci, automatyzacji lub rozwiązywania problemów, zapoznaj się z dokumentami w sekcji „Instrukcje” dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończenie [próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Sprawdzenie właściwości maszyny wirtualnej przed uruchomieniem testu trybu failover. Maszyna wirtualna musi być zgodna z [wymaganiami platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Uruchamianie trybu failover w regionie pomocniczym

1. W obszarze **Replikowane elementy** wybierz maszynę wirtualną, która ma być przełączona w tryb failover, a następnie wybierz pozycję **Tryb failover**

   ![Tryb failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:

   * **Najnowszy** (domyślna): ta opcja przetwarza wszystkie dane w usłudze Site Recovery i udostępnia najniższy cel punktu odzyskiwania.
   * **Najnowszy przetworzony**: ta opcja przywraca maszynę wirtualną do ostatniego punktu odzyskiwania, który został przetworzony przez usługę Site Recovery.
   * **Niestandardowy**: użyj tej opcji, aby przełączyć w tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**, jeśli usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się.

4. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.

5. Po przejściu w tryb failover zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przejść do innego punktu odzyskiwania dla maszyny wirtualnej, możesz użyć opcji **Zmień punkt odzyskiwania**.

6. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover.
   Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze. Opcja **Zmień punkt odzyskiwania** nie jest już dostępna.

## <a name="reprotect-the-secondary-vm"></a>Ponowne włączanie ochrony pomocniczej maszyny wirtualnej

Po przełączeniu maszyny wirtualnej w tryb failover należy ponownie włączyć jej ochronę, aby ponownie zreplikować ją do regionu podstawowego.

1. Upewnij się, że maszyna wirtualna jest w stanie **Tryb failover zatwierdzony**, a następnie sprawdź, czy region podstawowy jest dostępny oraz czy możesz utworzyć w nim nowe zasoby i uzyskać do nich dostęp.
2. W obszarze **Magazyn** > **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, która została przełączona w tryb failover, a następnie wybierz pozycję **Ponownie włącz ochronę**.

   ![Kliknięcie prawym przyciskiem myszy w celu ponownego włączenia ochrony](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Zwróć uwagę, że kierunek ochrony z regionu pomocniczego do regionu podstawowego, jest już wybrany.
3. Przejrzyj informacje o **grupie zasobów, sieci, magazynie i zestawach dostępności**. Wszystkie zasoby oznaczone jako (nowy) są tworzone w ramach operacji ponownej ochrony.
4. Kliknij przycisk **OK**, aby wyzwolić zadanie włączania ponownej ochrony. To zadanie inicjuje lokację docelową przy użyciu najnowszych danych. Następnie replikuje zmiany do regionu podstawowego. Maszyna wirtualna jest teraz w stanie chronionym.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownym włączeniu ochrony maszyn wirtualnych możesz wykonać powrót po awarii do regionu podstawowego. W tym celu skonfiguruj przełączanie w tryb failover z regionu pomocniczego do regionu podstawowego, według opisu w tym artykule.
