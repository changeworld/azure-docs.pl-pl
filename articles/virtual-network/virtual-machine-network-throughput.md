---
title: Przepływność sieci maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o przepływności sieci maszyny wirtualnej platformy Azure.
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
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153734"
---
# <a name="virtual-machine-network-bandwidth"></a>Przepustowość sieci maszyny wirtualnej

Platforma Azure oferuje szereg rozmiarów maszyn wirtualnych i typy, każdy z różne kombinacje wydajności. Możliwość jeden jest sieci przepływności (lub przepustowości), wyrażona w megabitach na sekundę (MB/s). Ponieważ maszyny wirtualne są hostowane na sprzęcie współużytkowanym, pojemność sieci musi być udostępniona dość między maszynami wirtualnymi, udostępnianie tego samego sprzętu. Większe maszyny wirtualne są przydzielane stosunkowo większej przepustowości niż mniejsze maszyny wirtualne.
 
Przepustowość sieci, przydzielone do każdej maszyny wirtualnej jest mierzone w (ruch wychodzący) ruch wychodzący z maszyny wirtualnej. Cały ruch sieciowy, pozostawiając maszyny wirtualnej jest liczony limitem przydzielone, niezależnie od docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 MB/s, ten limit dotyczy zarówno ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub poza platformą Azure.
 
Ruch przychodzący jest mierzone lub nie ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i pamięci masowej, które mogą wpłynąć na możliwość przetwarzania przychodzących danych maszyny wirtualnej.

Przyspieszona sieć jest funkcją pozwalające zwiększyć wydajność sieci, w tym czas oczekiwania, przepływność i użycie procesora CPU. Gdy przyspieszonej łączności sieciowej, można zwiększyć przepływność maszynę wirtualną, go to zrobić tylko przepustowość przydzielona do maszyny wirtualnej w górę. Aby dowiedzieć się więcej na temat Accelerated networking, zobacz temat dotyczący usługi Accelerated networking for [Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) maszyn wirtualnych.
 
Maszyny wirtualne platformy Azure musi mieć jedną, ale może mieć kilka, dołączonych do nich interfejsy sieciowe. Przepustowość przydzielona do maszyny wirtualnej jest sumą cały ruch wychodzący we wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej. Innymi słowy przydzielonej przepustowości odbywa się dla maszyny wirtualnej, niezależnie od tego, jak wiele interfejsów sieciowych dołączonych do maszyny wirtualnej. Aby dowiedzieć się, ile interfejsy różnych Obsługa rozmiarów maszyn wirtualnych platformy Azure, zobacz platformę Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych. 

## <a name="expected-network-throughput"></a>Przepływność sieci oczekiwane

Oczekiwanej przepływności wychodzące i liczbę interfejsów sieciowych obsługiwanych przez każdego rozmiaru maszyny Wirtualnej została szczegółowo opisana na platformie Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych. Wybierz typ, takich jak ogólnego przeznaczenia, a następnie wybierz serię rozmiar na stronie wynikowe, np. zalecamy używanie serii Dv2. Każda seria ma jedną tabelę z sieci specyfikacji w ostatniej kolumnie pod tytułem **maksymalna liczba kart sieciowych / oczekiwana wydajność sieci (MB/s)**. 

Limit przepływności stosuje się do maszyny wirtualnej. Przepływność jest zależny od następujących czynników:
- **Liczba interfejsów sieciowych**: Limit przepustowości jest zbiorcze z całego ruchu wychodzącego z maszyny wirtualnej.
- **Przyspieszona sieć**: Chociaż ta funkcja może być pomocny w osiągnięcia limitu opublikowane, nie powoduje zmiany limitu.
- **Miejsce docelowe ruchu**: Wszystkie miejsca docelowe są wliczane do limitu ruchu wychodzącego.
- **Protokół**: Cały ruch wychodzący za pośrednictwem wszystkich protokołów, liczy się do limitu.

## <a name="network-flow-limits"></a>Limity usługi Flow w sieci

Oprócz przepustowość liczba połączeń sieciowych, które są obecne na maszynie Wirtualnej w danym momencie może wpłynąć na wydajność sieci. Usługa Azure stack sieci zachowuje stan dla każdego kierunku połączenia TCP/UDP w strukturach danych o nazwie "przepływy". Typowe połączenie TCP/UDP będzie miał 2 przepływy utworzone, jeden dla ruchu przychodzącego i inny wpis dla kierunku wychodzącego. 

Transfer danych między punktami końcowymi wymaga tworzenia kilku przepływów, oprócz tych, które transferu danych. Niektóre przykłady są przepływy utworzone dla rozpoznawania nazw DNS i przepływów utworzonych dla sondy kondycji modułu równoważenia obciążenia. Należy pamiętać, że sieciowych urządzeń wirtualnych (urządzeń WUS), takich jak bram, serwery proxy i zapory, zobaczą również przepływy tworzone dla połączeń została przerwana na urządzenie i zainicjowany przez urządzenie. 

![Liczba przepływów dla konwersacji TCP za pośrednictwem urządzenia przekazywania](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limity przepływu i zalecenia

Dziś Azure stosu sieciowego obsługuje przepływów sieci Całkowita 250 tys dobrą wydajność w przypadku maszyn wirtualnych o rozmiarze większym niż 8 rdzeni procesora CPU i 100 KB łączna liczba przepływów z dobrą wydajność w przypadku maszyn wirtualnych z mniej niż 8 rdzeni procesora CPU. Ostatnie tej sieci limit spadku wydajności bez problemu zmieniała dla przydzielenie dodatkowych przepływów maksymalnie stały limit 1 mln łączna liczba przepływów, K 500 dla ruchu przychodzącego i 500 KB ruchu wychodzącego, po których przydzielenie dodatkowych przepływów są porzucane.

||Maszyny wirtualne za pomocą < 8 rdzeni procesora CPU|Maszyny wirtualne z 8 + liczba rdzeni procesora CPU|
|---|---|---|
|<b>Dobra wydajność</b>|Przepływy 100 KB |Przepływy 250 tys.|
|<b>Pogorszenie wydajności</b>|Powyżej 100 tysięcy przepływów|Ponad 250 KB przepływów|
|<b>Limit przepływu</b>|1 mln przepływów|1 mln przepływów|

Metryki są dostępne w [usługi Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) śledzić liczbę przepływów sieci i szybkość Tworzenie przepływu na wystąpień maszyny Wirtualnej lub zestawu skalowania maszyn wirtualnych.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Stawki za ustanawianie połączenia może również wpływać na wydajność sieci jako połączenie ustanawianie udziałów procesora CPU za pomocą procedury przetwarzania pakietów. Firma Microsoft zaleca benchmark obciążeń pod kątem wzorców oczekiwanego natężenia ruchu i skalowania obciążeń odpowiednio do potrzeb w zakresie wydajności. 

## <a name="next-steps"></a>Kolejne kroki

- [Optymalizowanie przepływności sieci dla systemu operacyjnego maszyny wirtualnej](virtual-network-optimize-network-bandwidth.md)
- [Przepływność sieci testu](virtual-network-bandwidth-testing.md) dla maszyny wirtualnej.
