---
title: Uruchom drążenie odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak uruchomić przechodzenie awaryjne w regionie pomocniczym dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091331"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Uruchom przechodzenie do szczegółów odzyskiwania po awarii do regionu pomocniczego dla maszyn wirtualnych platformy Azure 

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych i ich dostępności podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

W tym samouczku przedstawiono sposób uruchamiania próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure z jednego regionu platformy Azure w innym, wraz z testem trybu failover. Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych
> * Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

> [!NOTE]
> Ten samouczek ułatwia przeprowadzenie testowania odzyskiwania po awarii z minimalnymi krokami. Jeśli chcesz dowiedzieć się więcej o różnych aspektach związanych z wykonywaniem testowania odzyskiwania po awarii, w tym zagadnieniach dotyczących sieci, automatyzacji lub rozwiązywania problemów, zapoznaj się z dokumentami w obszarze "jak to" dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Przed uruchomieniem testu trybu failover zalecamy, aby zweryfikować właściwości maszyny wirtualnej w celu upewnienia się, że wszystko jest zgodne z oczekiwaniami.  Uzyskaj dostęp do właściwości maszyny wirtualnej w obszarze **Elementy replikowane**. Blok **Podstawy** zawiera informacje o ustawieniach i stanie maszyny.
- **Zalecamy, aby do testowania trybu failover użyć oddzielnej sieci maszyn wirtualnych platformy Azure**, a nie domyślnej sieci skonfigurowanej podczas włączania replikacji.
- W zależności od konfiguracji sieci źródłowej dla każdej karty sieciowej można opcjonalnie określić **podsieć, adres IP, publiczny adres IP, grupę zabezpieczeń sieci lub Load Balancer wewnętrzny** do dołączenia do każdej karty sieciowej w obszarze Ustawienia testowej pracy w trybie failover w sieci obliczeniowej & sieciowej przed przeprowadzeniem testowania odzyskiwania po awarii.


## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W obszarze **Ustawienia** > **Elementy replikowane** kliknij ikonę **+Test pracy w trybie failover** maszyny wirtualnej.

2. W obszarze **Test pracy w trybie failover** wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover:

    - **Najnowsze**: przetwarza wszystkie dane w Site Recovery i zapewnia najniższy RTO (cel czasu odzyskiwania).
    - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Element Custom jest dostępny tylko po przełączeniu w tryb failover pojedynczej maszyny wirtualnej, a nie w przypadku pracy w trybie awaryjnym z planem odzyskiwania.

3. Należy wybrać docelową sieć wirtualną platformy Azure, z którą maszyny wirtualne platformy Azure w regionie pomocniczym zostaną połączone po przejściu w tryb failover.

    > [!NOTE]
    > Lista rozwijana umożliwiająca wybranie usługi Azure Virtual Network nie będzie widoczna, jeśli ustawienia testowej pracy w trybie failover są wstępnie skonfigurowane dla zreplikowanego elementu.

4. Aby uruchomić tryb failover, kliknij przycisk **OK**. Aby śledzić postępy, kliknij maszynę wirtualną w celu otwarcia jej właściwości. Możesz też kliknąć zadanie **Test pracy w trybie failover** w obszarze nazwy magazynu > **Ustawienia** > **Zadania** > **Zadania usługi Site Recovery**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszyny wirtualne utworzone podczas testowania trybu failover, kliknij pozycję **Wyczyść test pracy w trybie failover** w replikowanym elemencie w planie odzyskiwania. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie produkcyjnego trybu failover](azure-to-azure-tutorial-failover-failback.md)
