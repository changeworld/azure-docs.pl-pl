---
title: Przenoszenie maszyn wirtualnych platformy Azure do innego regionu za pomocą usługi Azure Site Recovery
description: Korzystanie z usługi Azure Site Recovery w celu przenoszenia maszyn wirtualnych platformy Azure z jednego regionu platformy Azure do innego.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498049"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure

Ten artykuł zawiera omówienie przyczyn i kroków związanych z przenoszeniem maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md) 


## <a name="reasons-to-move-azure-vms"></a>Powody przenoszenia maszyn wirtualnych platformy Azure

Maszyny wirtualne mogą być przesuwne z następujących powodów:

- Został już wdrożony w jednym regionie i dodano nową obsługę regionu, która jest bliższa użytkownikom końcowym aplikacji lub usługi. W tym scenariuszu chcesz przenieść maszyny wirtualne, jak jest do nowego regionu w celu zmniejszenia opóźnienia. Użyj tego samego podejścia, jeśli chcesz skonsolidować subskrypcje lub jeśli istnieją reguły ładu lub organizacji, które wymagają przeniesienia.
- Maszyna wirtualna została wdrożona jako maszyna wirtualna z pojedynczym wystąpieniem lub jako część zestawu dostępności. Jeśli chcesz zwiększyć ujeżdności, możesz przenieść maszyny wirtualne do strefy dostępności.

## <a name="steps-to-move-azure-vms"></a>Kroki przenoszenia maszyn wirtualnych platformy Azure

Aby przenieść maszyny wirtualne, należy wykonać następujące kroki:

1. Weryfikowanie wymagań wstępnych
2. Przygotuj źródłowe maszyny wirtualne.
3. Przygotuj region docelowy.
4. Kopiowanie danych do regionu docelowego. Użyj technologii replikacji usługi Azure Site Recovery, aby skopiować dane ze źródłowej maszyny Wirtualnej do regionu docelowego.
5. Przetestuj konfigurację. Po zakończeniu replikacji przetestuj konfigurację, wykonując test pracy awaryjnej w sieci nieprodukcyjnej.
6. Wykonaj ruch.
7. Odrzuć zasoby w regionie źródłowym.

> [!NOTE]
> Szczegółowe informacje na temat tych kroków znajdują się w poniższych sekcjach.
> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do drugiego, ale nie obsługuje przenoszenia w obrębie regionu.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typowe architektury wdrożenia wielowarstwowego

W tej sekcji opisano najbardziej typowe architektury wdrażania aplikacji wielowarstwowej na platformie Azure. Przykładem jest aplikacja trójwarstwowa z publicznym adresem IP. Każda z warstw (sieci web, aplikacji i bazy danych) ma dwie maszyny wirtualne, każda i są one połączone przez moduł równoważenia obciążenia platformy Azure z innymi warstwami. Warstwa bazy danych ma replikację programu SQL Server Always On między maszynami wirtualnymi w celu uzyskania wysokiej dostępności.

* **Maszyny wirtualne z jednym wystąpieniem wdrożone w różnych warstwach:** każda maszyna wirtualna w warstwie jest skonfigurowana jako maszyna wirtualna z pojedynczym wystąpieniem i jest połączona przez moduły równoważenia obciążenia z innymi warstwami. Ta konfiguracja jest najprostsza do przyjęcia.

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w różnych warstwach](media/move-vm-overview/regular-deployment.png)

* **Maszyny wirtualne w każdej warstwie wdrożone w zestawach dostępności:** każda maszyna wirtualna w warstwie jest skonfigurowana w zestawie dostępności. [Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi węzłami sprzętowymi w klastrze. Gwarantuje to, że jeśli awaria sprzętu lub oprogramowania na platformie Azure nastąpi, dotyczy to tylko podzbioru maszyn wirtualnych, a ogólne rozwiązanie pozostanie dostępne i działa.

     ![Wdrażanie maszyn wirtualnych w różnych zestawach dostępności](media/move-vm-overview/avset.png)

* **Maszyny wirtualne w każdej warstwie wdrożonej w strefach dostępności:** każda maszyna wirtualna w warstwie jest skonfigurowana w [strefach dostępności.](https://docs.microsoft.com/azure/availability-zones/az-overview) Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

     ![Wdrożenie strefy dostępności](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Przenoszenie maszyn wirtualnych bez zmian do regionu docelowego

Na podstawie [architektury](#typical-architectures-for-a-multi-tier-deployment) wymienionych wcześniej, oto jak będą wyglądały wdrożenia po wykonaniu przeniesienia, tak jak jest w regionie docelowym.

* **Maszyny wirtualne z pojedynczym wystąpieniem wdrożone w różnych warstwach**

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w różnych warstwach](media/move-vm-overview/single-zone.png)

* **Maszyny wirtualne w każdej warstwie wdrożone w zestawach dostępności**

     ![Zestawy dostępności między regionami](media/move-vm-overview/crossregionaset.png)

* **Maszyny wirtualne w każdej warstwie wdrożone w strefach dostępności**

     ![Wdrażanie maszyn wirtualnych w strefach dostępności](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności

* **Maszyny wirtualne z pojedynczym wystąpieniem wdrożone w różnych warstwach**

     ![Wdrażanie maszyn wirtualnych w jednym wystąpieniu w różnych warstwach](media/move-vm-overview/single-zone.png)

* **Maszyny wirtualne w każdej warstwie wdrożone w zestawach dostępności:** Maszyny wirtualne można skonfigurować w dostępności ustawionej na oddzielne strefy dostępności po włączeniu replikacji maszyny Wirtualnej przy użyciu usługi Azure Site Recovery. Umowy SLA dostępności będzie 99,99% po zakończeniu operacji przenoszenia.

     ![Wdrażanie maszyn wirtualnych w zestawach dostępności i strefach dostępności](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-vms-avset-azone.md)

