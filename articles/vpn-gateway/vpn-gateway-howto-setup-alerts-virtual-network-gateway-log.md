---
title: Jak skonfigurować alerty dotyczące zdarzeń dzienników diagnostycznych usługi Azure VPN Gateway
description: Kroki, aby skonfigurować alerty dotyczące zdarzeń dzienniki diagnostyczne bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769739"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Konfigurowanie alertów dotyczących zdarzeń dzienniki diagnostyczne bramy sieci VPN

Ten artykuł ułatwia konfigurowanie alertów na podstawie zdarzeń dzienniki diagnostyczne bramy sieci VPN.


## <a name="setup"></a>Konfigurowanie alertów monitora platformy Azure na podstawie zdarzeń dziennik diagnostyczny funkcji przy użyciu portalu

Przykład czynności zostanie utworzony alert dla zdarzenia rozłączenia tunelu sieci VPN typu lokacja lokacja



1. Wyszukaj "Log Analytics" w ramach wszystkich usług, a następnie wybierz "Obszarów roboczych usługi Log Analytics" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Kliknij przycisk "Utwórz" na stronie usługi Log Analytics.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "wybierz")

3. Sprawdź obszar roboczy "Utwórz nowy" i wprowadź szczegóły.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "wybierz")

4. Znajdź bramy sieci VPN w taki sposób, w obszarze "Monitor" > bloku "Ustawienia diagnostyki" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "wybierz")

5. Aby włączyć diagnostykę, kliknij dwukrotnie w ramach bramy, a następnie kliknij polecenie "Włącz diagnostykę" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "wybierz")

6. Wypełnij szczegóły i upewnij się, są sprawdzane "Wysyłanie do usługi Log Analytics" i "TunnelDiagnosticLog". Wybierz obszar roboczy analizy dziennika, który został utworzony w kroku 3.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "wybierz")

7. Przejdź do omówienia zasobu bramy sieci wirtualnej i wybierz "Alerty" na karcie monitorowanie, a następnie utwórz nową regułę alertu lub Edytuj istniejącą regułę alertu.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "wybierz")

8. Wybierz obszar roboczy usługi Log Analytics i zasobu.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "wybierz")

9. Wybierz opcję "niestandardowa przeszukiwania dzienników" jako logiki sygnału w obszarze "Dodaj warunek" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "wybierz")

10. Wprowadź następujące zapytanie w polu tekstowym "Zapytania wyszukiwania", zastępując wartości w <> zgodnie z potrzebami.

    AzureDiagnostics | gdzie kategoria == "TunnelDiagnosticLog" i ResourceId == toupper ("<RESOURCEID OF GATEWAY>") i TimeGenerated > ago(5m) i remoteIP_s == "<REMOTE IP OF TUNNEL>" i status_s == "Disconnected"

    Wartość progowa równa 0 i kliknij przycisk "Gotowe"

    ![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "wybierz")

11. Na stronie "Utwórz regułę" kliknij przycisk "Utwórz nowy" w sekcji grup akcji. Wypełnij szczegóły, a następnie kliknij przycisk OK

![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "wybierz")

12. Na stronie "Utwórz reguły" Wypełnij szczegóły "Dostosowywanie akcji" i upewnij się, że poprawna nazwa grupy akcji pojawia się w sekcji "Nazwa grupy akcji". Kliknij przycisk "Utwórz regułę alertu" Aby utworzyć regułę.
![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "wybierz")

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować alerty dotyczące metryk tunelu, zobacz [jak konfigurować alerty dotyczące metryk bramy sieci VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
