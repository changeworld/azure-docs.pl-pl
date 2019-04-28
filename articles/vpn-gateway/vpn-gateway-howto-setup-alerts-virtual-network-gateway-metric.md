---
title: Jak skonfigurować alerty dotyczące metryk usługi Azure VPN Gateway
description: Kroki konfigurowania alertów dotyczących metryk bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769469"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Konfigurowanie alertów dotyczących metryk bramy sieci VPN

Ten artykuł ułatwia konfigurowanie alertów dotyczących metryki bramy sieci VPN. Usługa Azure monitor zapewnia możliwość konfigurowania alertów dla zasobów platformy Azure. Alerty można skonfigurować pod kątem dla bramy sieci wirtualnej typu "VPN".


|**Metryka**   | **Unit** | **Poziom szczegółowości** | **Opis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Średnie wykorzystanie przepustowości połączone wszystkich połączeń lokacja lokacja dla bramy.     |
|**P2SBandwidth**| Bajty/s  | 1 min  | Średnie wykorzystanie przepustowości połączone wszystkich połączeń punkt lokacja dla bramy.    |
|**P2SConnectionCount**| Licznik  | 1 min  | Liczba P2S połączeń w bramie.   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Średnie wykorzystanie przepustowości w bramie należy utworzyć tuneli. |
|**TunnelEgressBytes** | Bajty | 5 minut | Ruch wychodzący w tunelach w bramie należy utworzyć.   |
|**TunnelEgressPackets** | Licznik | 5 minut | Liczba wychodzących pakietów w tunelach w bramie należy utworzyć.   |
|**TunnelEgressPacketDropTSMismatch** | Licznik | 5 minut | Liczba wychodzących pakietów porzucony w tunelach spowodowane niezgodnością usług terminalowych. |
|**TunnelIngressBytes** | Bajty | 5 minut | Ruch przychodzący w tunelach w bramie należy utworzyć.   |
|**TunnelIngressPackets** | Licznik | 5 minut | Liczba przychodzących pakietów w tunelach w bramie należy utworzyć.   |
|**TunnelIngressPacketDropTSMismatch** | Licznik | 5 minut | Liczba przychodzących pakietów porzucony w tunelach spowodowane niezgodnością usług terminalowych. |


## <a name="setup"></a>Konfigurowanie alertów monitora platformy Azure na podstawie metryk, przy użyciu portalu

Przykład czynności spowoduje utworzenie alertu na bramę: <br>

**Metryka:** Średnia przepustowość tunelu <br>
**Warunek:** przepustowości > 10 bajtów na sekundę <br>
**Window:** 5 minut <br>
**Akcja alertu:** Email <br>



1. Przejdź do zasobu bramy sieci wirtualnej i wybierz "Alerty" na karcie monitorowanie, a następnie utwórz nową regułę alertu lub Edytuj istniejącą regułę alertu.

![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Wybieranie bramy sieci VPN, jako zasób.

![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "wybierz")

3. Wybierz metrykę, aby skonfigurować alert ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "wybierz")
4. Konfigurowanie logiki sygnału. Istnieją trzy składniki do logiki sygnału:

    a. Wymiary: Jeśli Metryka wymiarów, określonych wartości wymiaru można wybrać, aby alert oblicza tylko dane tego wymiaru. Te są opcjonalne.<br>
    b. Warunek: Tę operację można ocenić wartości metryk.<br>
    c. Czas: Określ poziom szczegółowości danych metryk i czas, aby ocenić ten alert na.<br>

![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "wybierz")

5. Aby wyświetlić skonfigurowanych reguł, kliknij pozycję "Zarządzaj reguły alertu" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "wybierz")

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować alerty w tunelu dzienniki diagnostyczne, zobacz [jak konfigurować alerty na dzienniki diagnostyczne bramy sieci VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
