---
title: Przepływność sieci maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o przepływności sieci maszyny wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 182b3b7dad828e67d006391e00986406729c959d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689250"
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

## <a name="next-steps"></a>Kolejne kroki

- [Optymalizowanie przepływności sieci dla systemu operacyjnego maszyny wirtualnej](virtual-network-optimize-network-bandwidth.md)
- [Przepływność sieci testu](virtual-network-bandwidth-testing.md) dla maszyny wirtualnej.
