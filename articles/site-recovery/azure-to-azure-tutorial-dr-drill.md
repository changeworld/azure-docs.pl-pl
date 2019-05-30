---
title: Uruchamianie próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak uruchomić próbne odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure dla maszyn wirtualnych IaaS platformy Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d058282a9e903b37505f2a762d4264efbbec3a5a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399516"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>Uruchamianie próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w regionie pomocniczym platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych i ich dostępności podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii komputerów lokalnych i maszyn wirtualnych platformy Azure (maszyn wirtualnych) i organizuje to odzyskiwanie. Obejmuje to replikację, tryb failover i odzyskiwanie.

W tym samouczku przedstawiono sposób uruchamiania próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure z jednego regionu platformy Azure w innym, wraz z testem trybu failover. Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych
> * Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

> [!NOTE]
> Ten samouczek przeprowadza użytkownika przez kroki procedury testowania odzyskiwania po awarii przy użyciu minimalnej liczby kroków. Jeśli chcesz dowiedzieć się więcej o różnych aspektach związanych z wykonywaniem testowania odzyskiwania po awarii, w tym o zagadnieniach dotyczących sieci, automatyzacji lub rozwiązywania problemów, zapoznaj się z dokumentami w obszarze „Instrukcje” dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed uruchomieniem testu trybu failover zalecamy, aby zweryfikować właściwości maszyny wirtualnej w celu upewnienia się, że wszystko jest zgodne z oczekiwaniami.  Uzyskaj dostęp do właściwości maszyny wirtualnej w obszarze **Elementy replikowane**. Blok **Podstawy** zawiera informacje o ustawieniach i stanie maszyny.
- **Zalecamy, aby do testowania trybu failover użyć oddzielnej sieci maszyn wirtualnych platformy Azure**, a nie domyślnej sieci skonfigurowanej podczas włączania replikacji.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W obszarze **Ustawienia** > **Elementy replikowane** kliknij ikonę **+Test pracy w trybie failover** maszyny wirtualnej.

2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Należy wybrać docelową sieć wirtualną platformy Azure, z którą maszyny wirtualne platformy Azure w regionie pomocniczym zostaną połączone po przejściu w tryb failover.

4. Aby uruchomić tryb failover, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszyny wirtualne utworzone podczas testowania trybu failover, kliknij pozycję **Wyczyść test pracy w trybie failover** w replikowanym elemencie w planie odzyskiwania. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uruchamianie produkcyjnego trybu failover](azure-to-azure-tutorial-failover-failback.md)
