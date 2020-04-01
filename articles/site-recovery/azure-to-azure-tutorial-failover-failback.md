---
title: Maszyny wirtualne platformy Azure w wersji fail over i ponowne przetwarzanie danych z usługi Azure są replikowane do pomocniczego regionu platformy Azure w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
description: Dowiedz się, jak w pracy awaryjnej i ponownego powielania maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure w celu odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68782587"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Przejmują maszyn wirtualne platformy Azure w wersji Fail over i ponownie się nad nimi między regionami

W tym samouczku opisano, jak w trybie fail over maszyny wirtualnej platformy Azure (VM) do pomocniczego regionu platformy Azure z usługą [Azure Site Recovery.](site-recovery-overview.md) Po przepełnienie po awarii, należy ponownierotect maszyny Wirtualnej. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przełączanie w tryb failover maszyny wirtualnej platformy Azure
> * Ponowne przetwarzanie pomocniczej maszyny Wirtualnej platformy Azure, tak aby replikuje się do regionu podstawowego.

> [!NOTE]
> Ten samouczek zawiera najprostszą ścieżkę z ustawieniami domyślnymi i minimalnym dostosowaniem. Aby uzyskać bardziej złożone scenariusze, użyj artykułów w obszarze "Jak to zrobić" dla maszyn wirtualnych platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem zapoznaj się z [często zadawanymi pytaniami](site-recovery-faq.md#failover) dotyczącymi trybu failover.
- Ukończenie [próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Sprawdzenie właściwości maszyny wirtualnej przed uruchomieniem testu trybu failover. Maszyna wirtualna musi być zgodna z [wymaganiami platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Uruchamianie trybu failover w regionie pomocniczym

1. W obszarze **Replikowane elementy** wybierz maszynę wirtualną, która ma być przełączona w tryb failover, a następnie wybierz pozycję **Tryb failover**

   ![Tryb failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:

   * **Najnowsze** (domyślnie): przetwarza wszystkie dane w usłudze Odzyskiwania witryny i zapewnia najniższy cel punktu odzyskiwania (RPO).
   * **Najnowsze przetworzone:** Przywraca maszynę wirtualną do najnowszego punktu odzyskiwania, który został przetworzony przez usługę site recovery.
   * **Niestandardowe:** Nie można przejść do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

3. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** jeśli chcesz, aby usługa Site Recovery próbowała wykonać zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem pracy awaryjnej. Zamknięcie pomaga zapewnić brak utraty danych. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Usługa Site Recovery nie czyści źródła po przemijeniu w stan failover.

4. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.

5. Po przejściu w tryb failover zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przejść do innego punktu odzyskiwania dla maszyny wirtualnej, możesz użyć opcji **Zmień punkt odzyskiwania**.

6. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover.
   Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze. Nie będzie teraz można zmienić punktu odzyskiwania.

> [!NOTE]
> Po przełączeniu maszyny wirtualnej w stan fail/, do której dodajesz dysk po włączeniu replikacji maszyny Wirtualnej, punkty replikacji pokażą dyski, które są dostępne do odzyskania. Na przykład jeśli maszyna wirtualna ma jeden dysk i dodać nowy, punkty replikacji, które zostały utworzone przed dodaniem dysku pokaże, że punkt replikacji składa się z "1 z 2 dysków".

![Przepełnienie awaryjne z dodanym dyskiem](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Ponowne włączanie ochrony pomocniczej maszyny wirtualnej

Po przełączeniu maszyny wirtualnej w tryb failover należy ponownie włączyć jej ochronę, aby ponownie zreplikować ją do regionu podstawowego.

1. Upewnij się, że maszyna wirtualna jest w stanie **Tryb failover zatwierdzony**, a następnie sprawdź, czy region podstawowy jest dostępny oraz czy możesz utworzyć w nim nowe zasoby i uzyskać do nich dostęp.
2. W obszarze**Replikowane elementy** **programu Vault** > kliknij prawym przyciskiem myszy maszynę wirtualną, która została przejęta awaryjnie, a następnie wybierz pozycję **Ponownie chroń**.

   ![Kliknięcie prawym przyciskiem myszy w celu ponownego włączenia ochrony](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Sprawdź, czy kierunek ochrony, pomocniczy do regionu podstawowego, jest już zaznaczony.
3. Przejrzyj informacje o **grupie zasobów, sieci, magazynie i zestawach dostępności**. Wszystkie zasoby oznaczone jako nowe są tworzone jako część operacji ponownego twórca.
4. Kliknij przycisk **OK**, aby wyzwolić zadanie włączania ponownej ochrony. To zadanie inicjuje lokację docelową przy użyciu najnowszych danych. Następnie replikuje zmiany do regionu podstawowego. Maszyna wirtualna jest teraz w stanie chronionym.

## <a name="next-steps"></a>Następne kroki
- Po ponownej przecenieniu [dowiedz się, jak](azure-to-azure-tutorial-failback.md) wrócić do regionu podstawowego, gdy jest dostępny.
- [Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownego ekscesu.
