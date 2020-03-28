---
title: Powiększanie wersji owe maszyn wirtualnych platformy Azure do regionu podstawowego za pomocą usługi Azure Site Recovery.
description: W tym artykule opisano, jak przywrócić maszyny wirtualne platformy Azure do regionu podstawowego za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091343"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Powrót maszyny wirtualnej platformy Azure w sieci Azure między regionami platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii, zarządzając replikacją, trybem failover i powrotem awaryjnym maszyn lokalnych i maszyn wirtualnych platformy Azure (VMs) i aranżacji ich.

W tym samouczku opisano, jak odzyskać po awarii pojedynczą maszynę wirtualną platformy Azure. Po przełączeniu awaryjnym, należy wrócić po awarii do regionu podstawowego, gdy jest dostępny. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Po awarii maszyny Wirtualnej w regionie pomocniczym.
> * Ponownie obróć podstawową maszynę wirtualną z powrotem do regionu pomocniczego.
> 
> [!NOTE]
> 
> Ten samouczek pomaga w trybie fail,kilka maszyn wirtualnych do regionu docelowego i z powrotem do regionu źródłowego z minimalnymi dostosowaniami. Aby uzyskać bardziej szczegółowe instrukcje, zapoznaj się z [instrukcjami dotyczącymi maszyn wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/)

## <a name="before-you-start"></a>Przed rozpoczęciem

* Upewnij się, że stan maszyny Wirtualnej jest **failover zatwierdzone**.
* Sprawdź, czy region podstawowy jest dostępny i czy możesz tworzyć i uzyskiwać do niego dostęp do nowych zasobów.
* Upewnij się, że reprotection jest włączona.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownejrotektywności maszyn wirtualnych można w razie potrzeby przywrócić po awarii do regionu podstawowego.

1. W przechowalni wybierz pozycję **Replikowane elementy**, a następnie wybierz maszynę wirtualną, która została ponownie przerzucona.

    ![Powrót po awarii do podstawowego](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. W **obszarze Elementy replikowane**wybierz maszynę wirtualną, a następnie wybierz pozycję **Przewiduj awaryjnie**.
3. W **obszarze Przewijanie awaryjne**wybierz punkt odzyskiwania, który ma być awaryjny:
    - **Najnowsze (domyślne)**: Przetwarza wszystkie dane w usłudze Site Recovery i zapewnia najniższy cel punktu odzyskiwania (RPO).
    - **Ostatnio przetworzone:** Przywraca maszynę wirtualną do najnowszego punktu odzyskiwania, który został przetworzony przez odzyskiwanie witryny.
    - **Niestandardowe:** Nie można przejść do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.
4. Wybierz **zamknij komputer przed rozpoczęciem pracy awaryjnej,** jeśli chcesz, aby odzysk lokacji próbował zamknąć maszyny wirtualne w regionie odzyskiwania po awarii przed wyzwoleniem pracy awaryjnej. Tryb failover jest kontynuowany, nawet jeśli zamknięcie nie powiedzie się. 
5. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.
6. Po zakończeniu pracy awaryjnej sprawdź poprawność maszyny Wirtualnej, logując się do niej. W razie potrzeby można zmienić punkt odzyskiwania.
7. Po zweryfikowaniu pracy awaryjnej wybierz pozycję **Zatwierdzanie pracy awaryjnej**. Zatwierdzenie usuwa wszystkie dostępne punkty odzyskiwania. Opcja punktu odzyskiwania zmiany nie jest już dostępna.
8. Maszyna wirtualna powinna być pokazywalna jako przejęta po awarii i po awarii.

    ![Maszyna wirtualna w regionach pierwotnych i pomocniczych](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> W przypadku komputerów z rozszerzeniem odzyskiwania lokacji w wersji 9.28.x.x, a następnie [do pakietu zbiorczego Aktualizacja 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Usługa Site Recovery czyści maszyny w pomocniczym regionie odzyskiwania po awarii, po zakończeniu powrotu po awarii i ponownym zabezpieczeniu maszyn wirtualnych. Nie ma potrzeby ręcznego usuwania maszyn wirtualnych i kart sieciowych w regionie pomocniczym. Jeśli całkowicie wyłączysz replikację po niepowodzeniu, usługa Site Recovery oczyści dyski w regionie odzyskiwania po awarii, oprócz maszyn wirtualnych i kart sieciowych.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownego ekscesu.
