---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc49b33fd3e6d582b31af5fe0507884e60205757
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791562"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Platforma Azure zwiększa się wraz z klienta podstawowej i dodaje obsługę nowych regionów się dotrzymać wzrost zapotrzebowania. Nowe funkcje są również dodawane co miesiąc między usługami. Możesz przenosić maszyny wirtualne (VM) w innym regionie lub w strefach dostępności w celu zwiększenia dostępności.

W tym samouczku opisano różne scenariusze, w których chcesz przenosić maszyny wirtualne. Również zawiera opis sposobu konfigurowania architektury w regionie docelowym w celu osiągnięcia wyższej dostępności. 

W tym samouczku nauczysz:

> [!div class="checklist"]
> 
> * Powody, aby przenieść maszyny wirtualne
> * Typowe architektury
> * Przenoszenie maszyn wirtualnych jest region docelowy
> * Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności

## <a name="reasons-to-move-azure-vms"></a>Przyczyny przenoszenia maszyn wirtualnych platformy Azure

Możesz przenieść maszyny wirtualne z następujących powodów:

- Została już wdrożona w jednym regionie, a nowa funkcja obsługi region został dodany, który jest bliżej użytkowników końcowych w aplikacji lub usługi. W tym scenariuszu należy przenosić maszyny wirtualne jako nowego regionu w celu zmniejszenia opóźnień. Użyj tej samej metody, jeśli chcesz skonsolidować subskrypcje lub jeśli istnieją reguły ładu lub organizacji, które wymagają przenoszenia.
- Maszyna wirtualna została wdrożona jako pojedynczego wystąpienia maszyny Wirtualnej lub w ramach zestawu dostępności. Jeśli chcesz zwiększyć dostępność umowy SLA, można przenieść maszyny wirtualne w strefie dostępności.

## <a name="steps-to-move-azure-vms"></a>Kroki umożliwiające przeniesienie maszyn wirtualnych platformy Azure

Aby przenieść maszyny wirtualne, należy wykonać następujące kroki:

1. Sprawdź wymagania wstępne.
2. Przygotuj źródłowe maszyny wirtualne.
3. Przygotuj region docelowy.
4. Kopiuj dane do regionu docelowego. Kopiowanie danych ze źródłowej maszyny Wirtualnej w regionie docelowym za pomocą technologii replikacji usługi Azure Site Recovery.
5. Testowanie konfiguracji. Po zakończeniu replikacji należy przetestować konfigurację, wykonując test trybu failover z siecią nieprodukcyjnych.
6. Przenoszenie.
7. Odrzuć zasobów w regionie źródłowym.

> [!NOTE]
> W poniższych sekcjach znajdują się szczegółowe informacje na temat tych kroków.
> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do innego, ale nie obsługuje przenoszenia w obrębie regionu.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typowe architektury wdrożenia wielowarstwowego

W tej sekcji opisano typowe architektur wdrożeń dla aplikacji wielowarstwowych na platformie Azure. Przykładem jest trójwarstwową aplikacją z publicznym adresem IP. Każda z warstw (sieci web, aplikacji i bazy danych) ma dwie maszyny wirtualne każdego i są one połączone przez moduł równoważenia obciążenia platformy Azure do innej warstwy. Warstwa bazy danych ma replikacji programu SQL Server Always On między maszynami wirtualnymi w celu zapewnienia wysokiej dostępności.

* **Jednego wystąpienia maszyn wirtualnych wdrożonych w różnych warstwach**: Każda maszyna wirtualna w warstwie jest skonfigurowany jako pojedynczego wystąpienia maszyny Wirtualnej i jest połączona za pomocą usługi równoważenia obciążenia do innej warstwy. Ta konfiguracja jest najprostszym do przyjęcia.

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w warstwach](media/move-vm-overview/regular-deployment.png)

* **Maszyny wirtualne w każdej warstwie i wdrożone w zestawach dostępności**: Każda maszyna wirtualna w warstwie jest skonfigurowany w zestawie dostępności. [Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi węzłami sprzętowymi w klastrze. Dzięki temu w przypadku awarii sprzętu lub oprogramowania, w obrębie platformy Azure ma wpływ tylko na część maszyn wirtualnych, a całe rozwiązanie nadal będzie dostępne i funkcjonalne.

     ![Wdrażanie maszyn wirtualnych w zestawach dostępności](media/move-vm-overview/avset.png)

* **Maszyny wirtualne w poszczególnych warstwach wdrożone w różnych strefach dostępności**: Każda maszyna wirtualna w warstwie jest skonfigurowane w ramach [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

     ![Wdrożenie strefy dostępności](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Przenoszenie maszyn wirtualnych bez zmian do regionu docelowego

Na podstawie [architektury](#typical-architectures-for-a-multi-tier-deployment) tutaj wspomniano wcześniej, to wygląd wdrożenia po przenoszenie jest region docelowy.

* **Jednego wystąpienia maszyn wirtualnych wdrożonych w różnych warstwach.**

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w warstwach](media/move-vm-overview/single-zone.png)

* **Maszyny wirtualne w każdej warstwie i wdrożone w zestawach dostępności**

     ![Obejmujące wiele regionów zestawów dostępności](media/move-vm-overview/crossregionaset.png)

* **Maszyny wirtualne w poszczególnych warstwach wdrożone w różnych strefach dostępności**

     ![Wdrażanie maszyn wirtualnych w różnych strefach dostępności](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności

* **Jednego wystąpienia maszyn wirtualnych wdrożonych w różnych warstwach.**

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w warstwach](media/move-vm-overview/single-zone.png)

* **Maszyny wirtualne w każdej warstwie i wdrożone w zestawach dostępności**: Można skonfigurować maszyny wirtualne w zestawie w oddzielnych strefach dostępności, po włączeniu replikacji dla maszyny Wirtualnej przy użyciu usługi Azure Site Recovery dostępności. Umowa SLA dla dostępności będzie 99,9%, po zakończeniu operacji przenoszenia.

     ![Wdrażanie maszyn wirtualnych w różnych zestawach dostępności i strefy dostępności](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-vms-avset-azone.md)

