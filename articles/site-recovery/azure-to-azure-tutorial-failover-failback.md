---
title: Przełączanie w tryb failover i przywracanie po awarii maszyn wirtualnych platformy Azure replikowanych do dodatkowego regionu świadczenia usługi Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak przełączyć maszyny wirtualne platformy Azure replikowane do dodatkowego regionu świadczenia usługi Azure w tryb failover i przywrócić je po awarii za pomocą usługi Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4a27142f9110fd26daa8ea0ebd151a67769e6568
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209277"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>Przełączanie maszyn wirtualnych platformy Azure w tryb failover i przywracanie po awarii między regionami świadczenia usługi Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do realizacji strategii odzyskiwania po awarii przez zarządzanie replikacją, przełączaniem do trybu failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure oraz koordynowanie tych procesów.

W tym samouczku opisano sposób przełączania w tryb failover jednej maszyny wirtualnej platformy Azure do pomocniczego regionu świadczenia usługi Azure. Po przełączeniu w tryb failover możesz przywrócić ją po awarii do regionu podstawowego, kiedy stanie się dostępny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przełączanie w tryb failover maszyny wirtualnej platformy Azure
> * Ponowne włączanie ochrony pomocniczej maszyny wirtualnej platformy Azure w celu replikowania jej do regionu podstawowego
> * Powrót po awarii pomocniczej maszyny wirtualnej
> * Ponowne włączanie ochrony podstawowej maszyny wirtualnej w regionie pomocniczym

Replikacja z platformy Azure na platformę Azure jest obecnie w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończenie [próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Sprawdzenie właściwości maszyny wirtualnej przed uruchomieniem testu trybu failover. Maszyna wirtualna musi być zgodna z [wymaganiami platformy Azure](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions).

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

Po ponownym włączeniu ochrony maszyn wirtualnych możesz wykonać powrót po awarii do regionu podstawowego. Aby to zrobić, postępuj zgodnie z instrukcjami dotyczącymi [przełączania w tryb failover](#run-a-failover).
