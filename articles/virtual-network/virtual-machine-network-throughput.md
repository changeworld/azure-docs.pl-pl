---
title: Przepustowość sieci maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o przepływności sieci maszyny wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245436"
---
# <a name="virtual-machine-network-bandwidth"></a>Przepustowość sieci maszyn wirtualnych

Platforma Azure oferuje różne rozmiary i typy maszyn wirtualnych, z których każdy ma inną kombinację możliwości wydajności. Jedną z możliwości jest przepustowość sieci (lub przepustowość), mierzona w megabitach na sekundę (Mb/s). Ponieważ maszyny wirtualne są hostowane na współużytkowanym sprzęcie, pojemność sieci musi być współużytkowana sprawiedliwie między maszynami wirtualnymi współużytkumiasu tego samego sprzętu. Większe maszyny wirtualne są przydzielane stosunkowo większą przepustowość niż mniejsze maszyny wirtualne.
 
Przepustowość sieci przydzielona do każdej maszyny wirtualnej jest mierzona na ruchu wychodzącym (wychodzącym) z maszyny wirtualnej. Cały ruch sieciowy opuszczający maszynę wirtualną jest wliczany do przydzielonego limitu, niezależnie od miejsca docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 Mbps, ten limit ma zastosowanie, czy ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub poza platformą Azure.
 
Ruch przychodzący nie jest mierzony ani ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i magazynu, które mogą mieć wpływ na zdolność maszyny wirtualnej do przetwarzania przychodzących danych.

Przyspieszona sieć to funkcja mająca na celu zwiększenie wydajności sieci, w tym opóźnienia, przepływności i wykorzystania procesora. Podczas gdy przyspieszona sieć może poprawić przepływność maszyny wirtualnej, może to zrobić tylko do przydzielonej przepustowości maszyny wirtualnej. Aby dowiedzieć się więcej o przyspieszonej sieci, zobacz Przyspieszone tworzenie sieci dla maszyn wirtualnych [z systemem Windows](create-vm-accelerated-networking-powershell.md) lub [Linux.](create-vm-accelerated-networking-cli.md)
 
Maszyny wirtualne platformy Azure muszą mieć jeden, ale może mieć kilka interfejsów sieciowych dołączonych do nich. Przepustowość przydzielona do maszyny wirtualnej jest sumą całego ruchu wychodzącego we wszystkich interfejsach sieciowych dołączonych do maszyny wirtualnej. Innymi słowy przydzielona przepustowość jest na maszynie wirtualnej, niezależnie od tego, ile interfejsów sieciowych są dołączone do maszyny wirtualnej. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje różne rozmiary maszyn wirtualnych platformy Azure, zobacz Rozmiary maszyn wirtualnych systemu [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure. 

## <a name="expected-network-throughput"></a>Oczekiwana przepustowość sieci

Oczekiwana przepływność wychodząca i liczba interfejsów sieciowych obsługiwanych przez każdy rozmiar maszyny Wirtualnej jest szczegółowo opisana w rozmiarach maszyn wirtualnych systemu Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Wybierz typ, taki jak Ogólnego przeznaczenia, a następnie wybierz serię rozmiarów na stronie wynikowej, taką jak seria Dv2. Każda seria ma tabelę ze specyfikacjami sieci w ostatniej kolumnie zatytułowanej **Max NIC / Oczekiwana wydajność sieci (Mb/s).** 

Limit przepływności ma zastosowanie do maszyny wirtualnej. Na przepływność nie mają wpływu następujące czynniki:
- **Liczba interfejsów sieciowych:** Limit przepustowości jest skumulowany dla całego ruchu wychodzącego z maszyny wirtualnej.
- **Przyspieszona sieć**: Chociaż funkcja może być pomocna w osiągnięciu opublikowanego limitu, nie zmienia limitu.
- **Miejsce docelowe ruchu:** Wszystkie miejsca docelowe są wliczane do limitu ruchu wychodzącego.
- **Protokół:** Cały ruch wychodzący na wszystkich protokołach jest wliczany do limitu.

## <a name="network-flow-limits"></a>Limity przepływu sieci

Oprócz przepustowości liczba połączeń sieciowych obecnych na maszynie wirtualnej w danym momencie może mieć wpływ na jej wydajność sieci. Stos sieci platformy Azure zachowuje stan dla każdego kierunku połączenia TCP/UDP w strukturach danych o nazwie "przepływy". Typowe połączenie TCP/UDP będzie miało 2 utworzone przepływy, jeden dla ruchu przychodzącego i drugi dla kierunku wychodzącego. 

Transfer danych między punktami końcowymi wymaga utworzenia kilku przepływów oprócz tych, które wykonują transfer danych. Niektóre przykłady są przepływy utworzone dla rozpoznawania DNS i przepływów utworzonych dla sond kondycji modułu równoważenia obciążenia. Należy również zauważyć, że urządzenia wirtualne sieciowe , takie jak bramy, serwery proxy, zapory, zobaczą przepływy tworzone dla połączeń zakończonych na urządzeniu i pochodzących z urządzenia. 

![Liczba przepływów dla konwersacji TCP za pośrednictwem urządzenia do przekazywania](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limity przepływu i zalecenia

Obecnie stos sieci platformy Azure obsługuje przepływy sieci 250K z dobrą wydajnością dla maszyn wirtualnych z większymi niż 8 rdzeniami procesora i przepływami całkowitymi 100k z dobrą wydajnością dla maszyn wirtualnych z mniej niż 8 rdzeniami procesora. Po przekroczenie tego limitu wydajność sieci ulega łagodnej degradacji dla dodatkowych przepływów do twardego limitu przepływów całkowitych 500K, 250K przychodzących i 250K wychodzących, po czym dodatkowe przepływy są odrzucane.

||Maszyny wirtualne z <8 rdzeni procesora|Maszyny wirtualne z ponad 8 rdzeniami procesora|
|---|---|---|
|<b>Dobra wydajność</b>|Przepływy 100K |Przepływy 250K|
|<b>Obniżona wydajność</b>|Powyżej przepływów 100k|Powyżej przepływów 250K|
|<b>Limit przepływu</b>|Przepływy 500K|Przepływy 500K|

Metryki są dostępne w [usłudze Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) do śledzenia liczby przepływów sieciowych i szybkości tworzenia przepływu w wystąpieniach maszyny Wirtualnej lub VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Stawki za zakładanie i zakończenia połączeń mogą również wpływać na wydajność sieci, ponieważ ustanawianie i kończynie połączeń współużytkuje procesor CPU z procedurami przetwarzania pakietów. Zaleca się, aby porównywać obciążenia względem wzorców ruchu oczekiwanego i skalować obciążenia w poziomie odpowiednio do potrzeb wydajności. 

## <a name="next-steps"></a>Następne kroki

- [Optymalizowanie przepływności sieci dla systemu operacyjnego maszyny wirtualnej](virtual-network-optimize-network-bandwidth.md)
- [Przetestuj przepustowość sieci](virtual-network-bandwidth-testing.md) dla maszyny wirtualnej.
