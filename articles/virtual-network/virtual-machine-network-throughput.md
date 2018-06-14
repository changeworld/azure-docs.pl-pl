---
title: Przepustowość sieci maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przepływność sieci maszyny wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: f22b6f361f0c5bea547721309bb0f75b62f18d92
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2018
ms.locfileid: "27778959"
---
# <a name="virtual-machine-network-bandwidth"></a>Przepustowość sieci maszyny wirtualnej

Azure oferuje różne rozmiary maszyn wirtualnych i typy, każda z różnych kombinacji możliwości wydajności. Możliwość co jest sieci przepływności (lub przepustowości), wyrażona w megabitach na sekundę (MB/s). Ponieważ maszyny wirtualne są hostowane w udostępnionych sprzętu, pojemność sieci muszą zostać udostępnione dość między maszynami wirtualnymi współdzieli ten sam sprzęt. Większe maszyny wirtualne są przydzielane stosunkowo większej przepustowości niż mniejsze maszyny wirtualnej.
 
Przepustowość sieci przydzielona do każdej maszyny wirtualnej jest naliczane na ruch wychodzący ruch (wychodzący) z maszyny wirtualnej. Cały ruch sieciowy, pozostawiając maszyny wirtualnej jest liczony limitem przydzielone, niezależnie od docelowego. Na przykład jeśli maszyna wirtualna ma limit 1000 MB/s, ten limit dotyczy zarówno ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej lub poza platformą Azure.
 
Transfer danych przychodzących jest nie naliczane lub ograniczone bezpośrednio. Istnieją inne czynniki, takie jak limity Procesora i pamięci masowej, które mogą mieć wpływ na zdolność maszynę wirtualną do przetwarzania przychodzących danych.

Przyspieszone sieci jest funkcją mające na celu poprawę wydajności sieci, w tym czas oczekiwania, przepływności i użycie procesora CPU. Podczas przyspieszonego sieci może zwiększyć przepływność maszynę wirtualną, jego to zrobić tylko przepustowość przydzielona do maszyny wirtualnej w górę. Aby dowiedzieć się więcej na temat akcelerowanego sieci, zobacz akcelerowanego sieci dla [Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) maszyn wirtualnych.
 
Maszyny wirtualne platformy Azure musi mieć jedną, ale może ma kilka, dołączyć do nich interfejsów sieciowych. Przepustowość przydzielona do maszyny wirtualnej jest sumą cały ruch wychodzący na wszystkich interfejsach sieciowych dołączonych do maszyny wirtualnej. Innymi słowy przydzielonej przepustowości odbywa się dla maszyny wirtualnej, niezależnie od tego, jak wiele interfejsów sieciowych jest podłączony do maszyny wirtualnej. Aby dowiedzieć się, interfejsy sieciowe ilu różnych Obsługa rozmiary maszyny Wirtualnej platformy Azure, zobacz Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych. 

## <a name="expected-network-throughput"></a>Przepustowość sieci oczekiwany

Oczekiwano wychodzącego przepływność i liczba interfejsów sieciowych obsługiwanych przez każdy rozmiar maszyny Wirtualnej została szczegółowo opisana na platformie Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych. Wybierz typ, takich jak ogólnego przeznaczenia, a następnie wybierz serię rozmiar na wynikowej strony, takich jak seria Dv2. Każda seria zawiera tabelę za pomocą sieci specyfikacje w ostatniej kolumnie pod tytułem **maksymalna liczba kart sieciowych / oczekiwano wydajność sieci (MB/s)**. 

Limit przepustowości stosuje się do maszyny wirtualnej. Przepływność jest niezależna od następujących czynników:
- **Liczba interfejsów sieciowych**: limit przepustowości jest zbiorcza z cały ruch wychodzący z maszyny wirtualnej.
- **Przyspieszony sieci**: Chociaż funkcji mogą być pomocne w osiągnięciu limitu opublikowane, nie powoduje zmiany limit.
- **Miejsce docelowe ruchu**: wszystkie miejsca docelowe są wliczane do ograniczenia ruchu wychodzącego.
- **Protokół**: cały ruch wychodzący za pośrednictwem protokołów wszystkich liczy się do limitu.

## <a name="next-steps"></a>Kolejne kroki

- [Zoptymalizowania przepływności sieci dla systemu operacyjnego maszyny wirtualnej](virtual-network-optimize-network-bandwidth.md)
- [Przepustowość sieci testu](virtual-network-bandwidth-testing.md) dla maszyny wirtualnej.
