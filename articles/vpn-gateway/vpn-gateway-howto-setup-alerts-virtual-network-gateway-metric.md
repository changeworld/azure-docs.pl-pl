---
title: Konfigurowanie alertów w metrykach usługi Azure VPN Gateway
description: Kroki konfigurowania alertów w metrykach bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605227"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Konfigurowanie alertów dotyczących metryk bramy sieci VPN

Ten artykuł ułatwia konfigurowanie alertów w metrykach usługi Azure VPN Gateway. Usługa Azure Monitor umożliwia konfigurowanie alertów dla zasobów platformy Azure. Można skonfigurować alerty dla bram sieci wirtualnej typu "VPN".


|**Metryka**   | **Jednostki** | **Poziom szczegółowości** | **Opis** | 
|---       | ---        | ---       | ---            | ---       |
|**ŚredniaPaswidth**| Bajty/s  | 5 minut| Średnie łączne wykorzystanie przepustowości wszystkich połączeń lokacja-lokacja na bramie.     |
|**P2SBandwidth (P2SBandwidth)**| Bajty/s  | 1 min  | Średnie łączne wykorzystanie przepustowości wszystkich połączeń typu punkt-lokacja na bramie.    |
|**P2SConnectionCount**| Liczba  | 1 min  | Liczba połączeń typu punkt-lokacja w bramie.   |
|**TunelAverageBandwidth** | Bajty/s    | 5 minut  | Średnie wykorzystanie przepustowości tuneli utworzonych na bramie. |
|**TunelEgressbytes** | Bajty | 5 minut | Ruch wychodzący w tunelach utworzonych na bramie.   |
|**Pakiety pakietów tunelowych** | Liczba | 5 minut | Liczba pakietów wychodzących w tunelach utworzonych na bramie.   |
|**TunnelEgressPacketDropTSMismatch** | Liczba | 5 minut | Liczba pakietów wychodzących porzuconych w tunelach spowodowanych niezgodnością selektora ruchu. |
|**TunelowanieBytes** | Bajty | 5 minut | Ruch przychodzący w tunelach utworzonych na bramie.   |
|**Pakiety tunelowe** | Liczba | 5 minut | Liczba przychodzących pakietów w tunelach utworzonych na bramie.   |
|**TunelowaniePacketDropTSMismatch** | Liczba | 5 minut | Liczba pakietów przychodzących porzuconych w tunelach spowodowanych niezgodnością selektora ruchu. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Konfigurowanie alertów usługi Azure Monitor na podstawie metryk przy użyciu witryny Azure portal

Poniższe kroki przykład utworzy alert na bramie dla:

- **Metryka:** TunelAverageBandwidth
- **Stan:** Przepustowość > 10 bajtów na sekundę
- **Okno:** 5 minut
- **Akcja alarmowa:** Adres e-mail



1. Przejdź do zasobu bramy sieci wirtualnej i wybierz **alerty** na karcie **Monitorowanie.** Następnie utwórz nową regułę alertu lub edytuj istniejącą regułę alertu.

   ![Wybór do tworzenia reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Utwórz")

2. Wybierz bramę sieci VPN jako zasób.

   ![Przycisk Wybierz i brama sieci VPN na liście zasobów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Wybierz pozycję")

3. Wybierz metrykę, aby skonfigurować alert.

   ![Wybrana metryka na liście danych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Wybierz pozycję")
4. Skonfiguruj logikę sygnału. Istnieją trzy składniki do niego:

    a. **Wymiary:** Jeśli metryka ma wymiary, można wybrać określone wartości wymiarów, tak aby alert oceniał tylko dane tego wymiaru. Są one opcjonalne.

    b. **Warunek:** Jest to operacja do oceny wartości metryki.

    d. **Czas:** Określ szczegółowość danych metryk i okres czasu na ocenę alertu.

   ![Szczegóły konfigurowania logiki sygnału](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Wybierz pozycję")

5. Aby wyświetlić skonfigurowane reguły, wybierz pozycję **Zarządzaj regułami alertów**.

   ![Przycisk zarządzania regułami alertów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Wybierz pozycję")

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować alerty w dziennikach diagnostycznych tuneli, zobacz [Konfigurowanie alertów w dziennikach diagnostycznych bramy sieci VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
