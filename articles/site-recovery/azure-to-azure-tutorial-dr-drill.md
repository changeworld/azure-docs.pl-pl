---
title: Uruchamianie wiertła odzyskiwania po awarii maszyny Wirtualnej platformy Azure za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak uruchomić drążenie odzyskiwania po awarii w regionie pomocniczym dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76166193"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Uruchamianie ćwiczenia odzyskiwania po awarii w regionie pomocniczym dla maszyn wirtualnych platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych i ich dostępności podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

W tym samouczku przedstawiono sposób uruchamiania próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure z jednego regionu platformy Azure w innym, wraz z testem trybu failover. Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych
> * Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

> [!NOTE]
> Ten samouczek pomaga wykonać ćwiczenie odzyskiwania po awarii przy użyciu minimalnych kroków. Aby dowiedzieć się więcej o różnych funkcjach związanych z wykonywaniem wiertła odzyskiwania po awarii, zobacz dokumentację [replikacji](azure-to-azure-how-to-enable-replication.md)maszyn wirtualnych platformy Azure, [sieci,](azure-to-azure-about-networking.md) [automatyzacji](azure-to-azure-powershell.md)lub [rozwiązywania problemów.](azure-to-azure-troubleshoot-errors.md)

## <a name="prerequisites"></a>Wymagania wstępne

Sprawdź następujące elementy przed zrobieniem tego samouczka:

- Przed uruchomieniem testowego trybu failover zaleca się sprawdzenie właściwości maszyny Wirtualnej, aby upewnić się, że jest skonfigurowana do odzyskiwania po awarii. Przejdź do**właściwości** **odzyskiwania** > po awarii **operacji** > maszyny Wirtualnej, aby wyświetlić właściwości replikacji i pracy awaryjnej.
- **Zalecamy, aby do testowania trybu failover użyć oddzielnej sieci maszyn wirtualnych platformy Azure**, a nie domyślnej sieci skonfigurowanej podczas włączania replikacji.
- W zależności od konfiguracji sieci źródłowej dla każdej karty sieciowej można określić **podsieć**, **prywatny adres IP,** publiczny adres **IP,** **grupę zabezpieczeń sieci**lub moduł **równoważenia obciążenia,** aby dołączyć do każdej karty sieci owej w testach ustawień trybu failover w **obliczeniach i sieci** przed wykonaniem ćwiczenia odzyskiwania po awarii.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

W tym przykładzie pokazano, jak używać magazynu usług odzyskiwania do wykonywania testu maszyny Wirtualnej w pracy awaryjnej.

1. Wybierz przechowalnię i przejdź do pozycji Elementy > chronione Elementy **replikowane**i wybierz maszynę wirtualną.**Replicated items**
1. W **testowym przewijaniu awaryjnego**wybierz punkt odzyskiwania, który ma być używany do pracy awaryjnej:
   - **Najnowsze**: Przetwarza wszystkie dane w u administratorze witryny i zapewnia najniższy cel czasu odzyskiwania (Recovery Time Objective).
   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. W tej opcji nie spędza się czasu na przetwarzanie danych, więc zapewnia niski cel rto.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowe:** Przeładuj awaryjnie do określonego punktu odzyskiwania. Niestandardowe jest dostępna tylko wtedy, gdy w trybie failover jednej maszyny Wirtualnej, a nie do pracy awaryjnej z planem odzyskiwania.
1. Wybierz docelową sieć wirtualną platformy Azure, z którą będą łączyć się maszyny wirtualne platformy Azure w regionie pomocniczym po przełączeniu awaryjnym.

   > [!NOTE]
   > Jeśli ustawienia trybu failover testu są wstępnie skonfigurowane dla elementu replikowanego, menu rozwijane, aby wybrać sieć wirtualną platformy Azure, nie jest widoczne.

1. Aby rozpocząć pracę awaryjną, wybierz przycisk **OK**. Aby śledzić postęp z magazynu, przejdź do **monitorowania** > **zadań odzyskiwania witryny** i wybierz zadanie **testowania trybu failover.**
1. Po zakończeniu pracy awaryjnej replika maszyny wirtualnej platformy Azure pojawi się w **maszynach wirtualnych**portalu Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
1. Aby usunąć maszyny wirtualne, które zostały utworzone podczas pracy awaryjnej testu, wybierz **cleanup test pracy awaryjnej** na zreplikowany element lub planu odzyskiwania. W **uwagach**należy rejestrować i zapisywać wszystkie obserwacje związane z testem w wersji failover.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie produkcyjnego trybu failover](azure-to-azure-tutorial-failover-failback.md)
