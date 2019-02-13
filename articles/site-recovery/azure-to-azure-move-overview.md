---
title: Przenoszenie maszyn wirtualnych IaaS platformy Azure do innego regionu świadczenia usługi Azure przy użyciu usługi Azure Site Recovery | Microsoft Docs
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5d844692b6199d93fa835da1021c9753311e17de
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824611"
---
# <a name="move-azure-vms-to-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu

Platforma Azure intensywnie się rozwija, a liczba klientów stale rośnie, dlatego dodawana jest obsługa nowych regionów w związku z rosnącym zapotrzebowaniem. Co miesiąc we wszystkich usługach dodawane są również nowsze możliwości. Z tego względu możesz chcieć przenieść maszyny wirtualne do innego regionu lub do stref dostępności w celu zwiększenia dostępności.

W tym dokumencie przedstawiono różne scenariusze, w których warto przenieść maszyny wirtualne, oraz instrukcje konfiguracji architektury w miejscu docelowym w celu osiągnięcia wyższej dostępności. 
> [!div class="checklist"]
> * [Dlaczego warto przenieść maszyny wirtualne platformy Azure](#why-would-you-move-azure-vms)
> * [Jak przenieść maszyny wirtualne platformy Azure](#how-to-move-azure-vms)
> * [Typowe architektury](#typical-architectures-for-a-multi-tier-deployment)
> * [Przenoszenie maszyn wirtualnych bez zmian do regionu docelowego](#move-azure-vms-to-another-region)
> * [Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Dlaczego warto przenieść maszyny wirtualne platformy Azure

Klienci przenoszą maszyny wirtualne z następujących powodów:

- Jeśli wykonano już wdrożenie w jednym regionie i została dodana obsługa nowego regionu, który znajduje się bliżej użytkowników końcowych aplikacji lub usługi, warto **przenieść maszyny wirtualne bez zmian do nowego regionu** w celu zmniejszenia opóźnienia. To samo podejście jest stosowane w celu konsolidacji subskrypcji lub konieczności przeniesienia ze względu na reguły ładu/organizacji. 
- Jeśli maszyna wirtualna została wdrożona jako pojedyncze wystąpienie maszyny wirtualnej lub jako część zestawu dostępności i chcesz zwiększyć dostępność w ramach umów SLA, możesz **przenieść maszyny wirtualne do zestawu dostępności**. 

## <a name="how-to-move-azure-vms"></a>Jak przenieść maszyny wirtualne platformy Azure
Aby przenieść maszyny wirtualne, należy wykonać następujące kroki:

1. Weryfikowanie wymagań wstępnych 
2. Przygotowywanie źródłowych maszyn wirtualnych 
3. Przygotowywanie regionu docelowego 
4. Kopiowanie danych do regionu docelowego — kopiowanie danych ze źródłowej maszyny wirtualnej do regionu docelowego za pomocą technologii replikacji usługi Azure Site Recovery
5. Testowanie konfiguracji: Po ukończeniu replikacji przetestuj konfigurację, wykonując test pracy w trybie failover przy użyciu sieci nieprodukcyjnej.
6. Przenoszenie 
7. Odrzucanie zasobów w regionie źródłowym 


> [!IMPORTANT]
> Obecnie usługa Azure Site Recovery obsługuje przenoszenie maszyn wirtualnych z jednego regionu do innego i nie obsługuje przenoszenia w ramach regionu. 

> [!NOTE]
> Szczegółowe wskazówki dotyczące tych kroków znajdują się w dokumentacji poszczególnych scenariuszy, zgodnie z tymi informacjami

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typowe architektury wdrożenia wielowarstwowego
W poniższej sekcji przedstawiono najczęściej spotykane architektury wdrożeń aplikacji wielowarstwowych na platformie Azure stosowane przez klientów. Przykład omówiony w tym miejscu dotyczy trójwarstwowej aplikacji z publicznym adresem IP. Każda z warstw — internetowa, aplikacji i bazy danych — zawiera 2 maszyny wirtualne, które są połączone z innymi warstwami przy użyciu modułu równoważenia obciążenia. Warstwa bazy danych ma zawsze włączoną replikację SQL między maszynami wirtualnymi w celu zapewnienia wysokiej dostępności.

1.  **Pojedyncze wystąpienia maszyn wirtualnych wdrożone w różnych warstwach** — każda maszyna wirtualna w warstwie jest skonfigurowana jako pojedyncze wystąpienie maszyny wirtualnej połączone z innymi warstwami przy użyciu modułów równoważenia obciążenia. Jest to najprostsza konfiguracja stosowana przez klientów.

       ![pojedyncze maszyny wirtualne](media/move-vm-overview/regular-deployment.PNG)

2. **Maszyny wirtualne w poszczególnych warstwach wdrożone w zestawie dostępności** — każda maszyna wirtualna w warstwie jest skonfigurowana w ramach zestawu dostępności. [Zestawy dostępności](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi węzłami sprzętowymi w klastrze. Dzięki temu ewentualne awarie sprzętowe lub błędy oprogramowania na platformie Azure będą miały wpływ tylko na część maszyn wirtualnych, a całe rozwiązanie nadal będzie dostępne i funkcjonalne. 
   
      ![zestaw dostępności](media/move-vm-overview/AVset.PNG)

3. **Maszyny wirtualne w poszczególnych warstwach wdrożone w strefie dostępności** — każda maszyna wirtualna w warstwie jest skonfigurowana w ramach [stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview). Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

      ![wdrożenie w strefie](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Przenoszenie maszyn wirtualnych bez zmian do regionu docelowego

Oto jak będą wyglądać wdrożenia po przeniesieniu bez zmian do regionu docelowego, zależnie od [architektur](#typical-architectures-for-a-multi-tier-deployment) omówionych powyżej.


1. **Pojedyncze wystąpienia maszyn wirtualnych wdrożone w różnych warstwach** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Maszyny wirtualne w poszczególnych warstwach wdrożone w zestawie dostępności**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **Maszyny wirtualne w poszczególnych warstwach wdrożone w strefie dostępności**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności

1. **Pojedyncze wystąpienia maszyn wirtualnych wdrożone w różnych warstwach** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Maszyny wirtualne w poszczególnych warstwach wdrożone w zestawie dostępności** — włączając replikację maszyny wirtualnej przy użyciu usługi Azure Site Recovery, możesz umieścić maszyny wirtualne w zestawie dostępności w oddzielnych strefach dostępności. Po ukończeniu operacji przenoszenia umowa SLA będzie obejmować dostępność na poziomie 99,9%.

      ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Następne kroki

Ten dokument zawiera ogólne wskazówki dotyczące przenoszenia maszyn wirtualnych. Aby uzyskać instrukcje krok po kroku, przeczytaj następujące dokumenty:


> [!div class="nextstepaction"]
> * [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)

> * [Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności](move-azure-VMs-AVset-Azone.md)

