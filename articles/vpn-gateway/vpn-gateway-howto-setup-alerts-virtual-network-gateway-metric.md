---
title: Ustawianie alertów dotyczących metryk usługi Azure VPN Gateway
description: Kroki konfigurowania alertów dotyczących metryk bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509908"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Ustawianie alertów dotyczących metryk bramy sieci VPN

Ten artykuł ułatwia konfigurowanie alertów dotyczących metryk usługi Azure VPN Gateway. Usługa Azure Monitor zapewnia możliwość konfigurowania alertów dla zasobów platformy Azure. Można skonfigurować alerty dla bramy sieci wirtualnej typu "VPN".


|**Metryka**   | **Unit** | **Poziom szczegółowości** | **Opis** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bajty/s  | 5 minut| Średnie wykorzystanie przepustowości połączone wszystkich połączeń lokacja lokacja na bramę.     |
|**P2SBandwidth**| Bajty/s  | 1 minuta  | Średnie wykorzystanie przepustowości połączone wszystkich połączeń punkt lokacja na bramę.    |
|**P2SConnectionCount**| Count  | 1 minuta  | Liczba połączeń punkt lokacja na bramę.   |
|**TunnelAverageBandwidth** | Bajty/s    | 5 minut  | Średnie wykorzystanie przepustowości w bramie należy utworzyć tuneli. |
|**TunnelEgressBytes** | Bajty | 5 minut | Ruch wychodzący w tunelach w bramie należy utworzyć.   |
|**TunnelEgressPackets** | Count | 5 minut | Liczba wychodzących pakietów w tunelach w bramie należy utworzyć.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minut | Liczba wychodzących pakietów porzucony w tunelach spowodowane niezgodnością selektor ruchu. |
|**TunnelIngressBytes** | Bajty | 5 minut | Ruch przychodzący w tunelach w bramie należy utworzyć.   |
|**TunnelIngressPackets** | Count | 5 minut | Liczba przychodzących pakietów w tunelach w bramie należy utworzyć.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minut | Liczba przychodzących pakietów porzucony w tunelach spowodowane niezgodnością selektor ruchu. |


## <a name="setup"></a>Ustawianie alertów usługi Azure Monitor na podstawie metryk za pomocą witryny Azure portal

Krokach w poniższym przykładzie spowoduje utworzenie alertu na bramę:

- **Metryka:** TunnelAverageBandwidth
- **Warunek:** Przepustowość > 10 bajtów na sekundę
- **Window:** 5 minut
- **Akcja alertu:** Poczta e-mail



1. Przejdź do zasobu bramy sieci wirtualnej i wybierz **alerty** z **monitorowanie** kartę. Następnie utwórz nową regułę alertu lub Edytuj istniejącą regułę alertu.

   ![Opcje tworzenia reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Create")

2. Wybieranie bramy sieci VPN, jako zasób.

   ![Wybierz przycisk i bramy sieci VPN na liście zasobów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "wybierz")

3. Wybierz metrykę, aby skonfigurować alertu.

   ![Wybrane metryki na liście metryki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "wybierz")
4. Konfigurowanie logiki sygnału. Istnieją trzy składniki:

    a. **Wymiary**: Jeśli Metryka wymiarów, można wybrać wartości określonego wymiaru, tak, aby alert oblicza tylko dane tego wymiaru. Te są opcjonalne.

    b. **Warunek**: Jest to operacja można ocenić wartości metryk.

    c. **Czas**: Określ poziom szczegółowości danych metryk i czas, aby ocenić ten alert.

   ![Szczegółowe informacje dotyczące konfigurowania sygnał logiki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "wybierz")

5. Zaznacz, aby wyświetlić skonfigurowanych reguł **Zarządzaj regułami alertów**.

   ![Przycisk dla zarządzania regułami alertów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "wybierz")

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować alerty dzienników diagnostycznych tunelu, zobacz [Konfigurowanie alertów dla bramy sieci VPN, dzienniki diagnostyczne](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
