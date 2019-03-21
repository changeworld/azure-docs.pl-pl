---
title: Niepowodzenie ponownie IaaS maszyn wirtualnych platformy Azure replikowane do regionu pomocniczego platformy Azure w celu odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
description: Dowiedz się, jak zakończyć się niepowodzeniem ponownie Azure maszyn wirtualnych przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089710"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Niepowodzenie ponownie Azure maszyn wirtualnych między regionami platformy Azure

[Usługi Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez organizowanie replikacji, pracy awaryjnej i kończyć się niepowodzeniem, ponownie z maszyn lokalnych i maszyn wirtualnych (VM) i zarządzanie nimi.

W tym samouczku opisano, jak ich powrotu po awarii jednej maszyny Wirtualnej platformy Azure. Po przełączeniu w tryb failover możesz przywrócić ją po awarii do regionu podstawowego, kiedy stanie się dostępny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Powrót po awarii pomocniczej maszyny wirtualnej
> * Ponownego włączenia ochrony podstawowej maszyny Wirtualnej do regionu pomocniczego
> 
> [!NOTE]
> 
> Ten samouczek jest przeznaczony do prowadzą użytkownika przez kroki do trybu failover do regionu docelowego i z powrotem minimalne dostosowywania; w przypadku, gdy chcesz dowiedzieć się więcej na temat różne aspekty związane z trybem failover, w tym na temat sieci, usługi automation lub rozwiązywania problemów, zapoznaj się dokumenty w obszarze "How to" maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

> * Upewnij się, że maszyna wirtualna jest w stanie tryb Failover zatwierdzony i sprawdź, czy region podstawowy jest dostępny i czy możesz utworzyć i zapisać w nim nowe zasoby.
> * Upewnij się, że włączono ponownej ochrony.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownego włączenia jego ochrony maszyn wirtualnych może zakończyć się niepowodzeniem do regionu podstawowego, jak i chcesz.

1. Przejdź do usługi Recovery Services Vault. Kliknij elementy replikowane i wybierz maszynę Wirtualną, która została ponownie włączona.

2. Powinien być widoczny poniżej. Należy pamiętać, że jest on podobny do bloku test trybu failover i trybu failover z regionu podstawowego.
![Powrót po awarii do podstawowego](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Kliknij pozycję Test trybu Failover, aby wykonać test trybu failover do regionu podstawowego. Wybierz punkt odzyskiwania i sieci wirtualnej do testowania trybu failover, a następnie wybierz przycisk OK. Możesz zobaczyć testów, które maszyna wirtualna utworzona w regionie podstawowym, którego można uzyskać dostęp i sprawdzić.

4. Gdy Test pracy awaryjnej jest zadowalający, możesz kliknąć oczyszczania testu trybu failover, aby wyczyścić zasoby utworzone w regionie źródłowym do testowania trybu failover.

5. W elementach zreplikowany wybierz maszynę Wirtualną, która ma być trybu failover > trybu Failover.

6. W tryb Failover wybierz punkt odzyskiwania w tryb failover. Możesz użyć jednej z następujących opcji:
    1. Najnowsze (ustawienie domyślne): Ta opcja przetwarza wszystkie dane w usłudze Site Recovery i udostępnia najniższy cel punktu odzyskiwania (RPO)
    2. Najnowszy przetworzony: Ta opcja przywraca maszynę wirtualną do najnowszego punktu odzyskiwania, które zostały przetworzone przez usługę Site Recovery
    3. Niestandardowe: Użyj tej opcji w tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover

7. Wybierz Zamknij maszynę przed rozpoczęciem pracy awaryjnej, jeśli chcesz, aby Usługa Site Recovery ma spróbować przeprowadzić zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Pamiętaj, że usługa Site Recovery nie obsługuje czyszczenia źródła po włączeniu trybu failover.

8. Śledzić postęp trybu failover na stronie zadań

9. Po przejściu w tryb failover zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przejść do innego punktu odzyskiwania dla maszyny wirtualnej, można użyć opcji punktu odzyskiwania zmiany.

10. Gdy jesteś zadowolony z nieudane przez maszynę wirtualną, należy zatwierdzić tryb failover. Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze. Zmiana opcji punktu odzyskiwania nie jest już dostępna.

![Maszyn wirtualnych na region podstawowy i pomocniczy](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Jeśli zostanie wyświetlony poprzedni zrzut ekranu, to maszyna wirtualna „ContosoWin2016” została przełączona w tryb failover z regionu Środkowe stany USA do regionu Wschodnie stany USA i powróciła po awarii z regionu Wschodnie stany USA do regionu Środkowe stany USA.

Należy pamiętać, że maszyny wirtualne odzyskiwania po awarii pozostanie w stanie dezalokowany zamknięcie. To zachowanie jest zaprojektowane specjalnie, ponieważ usługa Azure Site Recovery zapisuje informacje o maszynie wirtualnej, które mogą być przydatne później podczas przełączania w tryb failover z regionu podstawowego do regionu pomocniczego. Cofnięcie przydziału maszyny wirtualne nie są opłata, więc powinny być przechowywane, ponieważ jest.

> [!NOTE]
> Zobacz ["jak" sekcji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) Aby uzyskać więcej informacji na temat przepływu pracy ponownej ochrony i co się dzieje podczas ponownej ochrony.
