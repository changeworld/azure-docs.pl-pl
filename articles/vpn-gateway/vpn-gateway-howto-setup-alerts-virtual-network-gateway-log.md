---
title: Ustawianie alertów dotyczących zdarzeń dziennika diagnostycznego z bramy Azure VPN Gateway
description: Kroki, aby skonfigurować alerty dotyczące zdarzeń dzienniki diagnostyczne bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 3880c847c54136dfd3ba1ecfe0178565091e229f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510206"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Ustawianie alertów dotyczących zdarzeń dziennika diagnostycznego z bramy sieci VPN

Ten artykuł pomoże Ci skonfigurować alerty na podstawie zdarzeń dziennika diagnostycznego z bramy Azure VPN Gateway.

## <a name="setup"></a>Konfigurowanie alertów

Krokach w poniższym przykładzie spowoduje utworzenie alert w przypadku zdarzenie rozłączenia, która obejmuje tunelu sieci VPN typu lokacja lokacja:


1. W witrynie Azure portal Wyszukaj **usługi Log Analytics** w obszarze **wszystkich usług** i wybierz **obszarów roboczych usługi Log Analytics**.

   ![Opcje do przechodzenia do obszarów roboczych usługi Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Wybierz **Utwórz** na **usługi Log Analytics** strony.

   ![Strona analizy dziennika za pomocą przycisku Utwórz](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "wybierz")

3. Wybierz **Utwórz nowy** i wprowadź szczegóły.

   ![Szczegóły dotyczące tworzenia obszaru roboczego usługi Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "wybierz")

4. Znajdź bramy sieci VPN na **Monitor** > **ustawień diagnostycznych** bloku.

   ![Opcje służące do znajdowania bramy sieci VPN w konfiguracji ustawień diagnostyki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "wybierz")

5. Aby włączyć diagnostykę, kliknij dwukrotnie ikonę bramy, a następnie wybierz pozycję **Włącz diagnostykę**.

   ![Opcje włączania diagnostyki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "wybierz")

6. Wypełnij szczegóły i upewnij się, że **wysyłanie do usługi Log Analytics** i **TunnelDiagnosticLog** są zaznaczone. Wybierz obszar roboczy analizy dziennika, który został utworzony w kroku 3.

   ![Wybrane pola wyboru](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "wybierz")

7. Przejdź do omówienia dla zasobu bramy sieci wirtualnej, a następnie wybierz pozycję **alerty** z **monitorowanie** kartę. Następnie utwórz nową regułę alertu lub Edytuj istniejącą regułę alertu.

   ![Tworzenie nowej reguły alertu wybrane](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "wybierz")

   ![punkt lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "wybierz")
8. Wybierz obszar roboczy usługi Log Analytics i zasobu.

   ![Wybrane dla obszaru roboczego i zasobów](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "wybierz")

9. Wybierz **przeszukiwania dzienników niestandardowych** jako logiki sygnału w obszarze **Dodaj warunek**.

   ![Opcje wyszukiwania dzienników niestandardowych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "wybierz")

10. W polu tekstowym **Zapytanie wyszukiwania** wpisz poniższe zapytanie. Zastąp wartości w <> zgodnie z potrzebami.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Ustaw wartość progu na 0, a następnie wybierz pozycję **gotowe**.

    ![Wprowadzając zapytania i wybierając próg](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "wybierz")

11. Na **Utwórz regułę** wybierz opcję **Utwórz nowy** w obszarze **grup akcji** sekcji. Wypełnij szczegóły i wybierz pozycję **OK**.

    ![Szczegóły dotyczące nowej grupy akcji](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "wybierz")

12. Na **Utwórz regułę** strony, wypełnij szczegóły **dostosować akcje** i upewnij się, że poprawna nazwa pojawia się w **Nazwa grupy akcji** sekcji. Wybierz **Utwórz regułę alertu** Aby utworzyć regułę.

    ![Opcje tworzenia reguły](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "wybierz")

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować alerty dotyczące metryk tunelu, zobacz [Konfigurowanie alertów dotyczących metryk bramy sieci VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
