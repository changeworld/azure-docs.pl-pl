---
title: Konfigurowanie alertów dotyczących zdarzeń dzienników diagnostycznych z usługi Azure VPN Gateway
description: Kroki konfigurowania alertów dotyczących zdarzeń dziennika diagnostyki VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: c84d457c51f71bdf315bbbcec674ff1186dd905f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249014"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Konfigurowanie alertów dotyczących zdarzeń dzienników diagnostycznych z VPN Gateway

Ten artykuł ułatwia Konfigurowanie alertów opartych na zdarzeniach dzienników diagnostycznych z usługi Azure VPN Gateway przy użyciu usługi Azure Log Analytics. 

Następujące dzienniki są dostępne na platformie Azure:

|***Nazwa*** | ***Opis*** |
|---        | ---               |
|GatewayDiagnosticLog | Zawiera dzienniki diagnostyczne dla zdarzeń konfiguracji bramy, podstawowe zmiany i zdarzenia konserwacji |
|TunnelDiagnosticLog | Zawiera zdarzenia zmiany stanu tunelu. Zdarzenia łączenia/rozłączania tunelu zawierają podsumowanie przyczyny zmiany stanu, jeśli ma to zastosowanie |
|RouteDiagnosticLog | Rejestruje zmiany tras statycznych i zdarzeń BGP występujących w bramie |
|IKEDiagnosticLog | Rejestruje komunikaty i zdarzenia kontroli IKE na bramie |
|P2SDiagnosticLog | Rejestruje komunikaty i zdarzenia kontroli punkt-lokacja w bramie |

## <a name="setup"></a>Konfigurowanie alertów

Poniższe przykładowe kroki spowodują utworzenie alertu dotyczącego zdarzenia rozłączenia obejmującego tunel VPN typu lokacja-lokacja:


1. W Azure Portal Wyszukaj **log Analytics** w obszarze **wszystkie usługi** i wybierz pozycję **log Analytics obszary robocze**.

   ![Wybory do przechodzenia do log Analytics obszarów roboczych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Utwórz")

2. Wybierz pozycję **Utwórz** na stronie **log Analytics** .

   ![Strona log Analyticsa z przyciskiem tworzenia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Wybierz pozycję")

3. Wybierz pozycję **Utwórz nową** i wypełnij szczegóły.

   ![Szczegóły dotyczące tworzenia obszaru roboczego log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Wybierz pozycję")

4. Znajdź bramę sieci VPN w bloku **monitorowanie** > **ustawień diagnostycznych** .

   ![Opcje znajdowania bramy sieci VPN w ustawieniach diagnostycznych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Wybierz pozycję")

5. Aby włączyć diagnostykę, kliknij dwukrotnie bramę, a następnie wybierz pozycję **Włącz diagnostykę**.

   ![Opcje włączania diagnostyki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Wybierz pozycję")

6. Wypełnij szczegóły i upewnij się, że wybrano opcję **Wyślij do log Analytics** i **TunnelDiagnosticLog** . Wybierz obszar roboczy Log Analytics, który został utworzony w kroku 3.

   ![Zaznaczone pola wyboru](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Wybierz pozycję")

7. Przejdź do omówienia zasobu bramy sieci wirtualnej i wybierz pozycję **alerty** na karcie **monitorowanie** . Następnie utwórz nową regułę alertu lub edytuj istniejącą regułę alertu.

   ![Wybory dotyczące tworzenia nowej reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")

   ![punkt-lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")
8. Wybierz obszar roboczy Log Analytics i zasób.

   ![Wybory dla obszaru roboczego i zasobu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Wybierz pozycję")

9. Wybierz **niestandardowe przeszukiwanie dzienników** jako logikę sygnału w obszarze **Dodawanie warunku**.

   ![Wybory dla niestandardowego przeszukiwania dzienników](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Wybierz pozycję")

10. W polu tekstowym **Zapytanie wyszukiwania** wpisz poniższe zapytanie. W razie potrzeby Zastąp wartości w < >.

    ```
    AzureDiagnostics |
      where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
      remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"
    ```

    Ustaw wartość progową na 0 i wybierz pozycję **gotowe**.

    ![Wprowadzanie zapytania i wybieranie progu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Wybierz pozycję")

11. Na stronie **Tworzenie reguły** wybierz pozycję **Utwórz nowy** w sekcji **grupy akcji** . Wypełnij szczegóły i wybierz **przycisk OK**.

    ![Szczegóły nowej grupy akcji](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Wybierz pozycję")

12. Na stronie **Tworzenie reguły** Wypełnij pola szczegóły **akcji dostosowywania** i upewnij się, że poprawna nazwa jest wyświetlana w sekcji **Nazwa grupy akcji** . Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć regułę.

    ![Wybory dla tworzenia reguły](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Wybierz pozycję")

## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować alerty dotyczące metryk tuneli, zobacz [Konfigurowanie alertów na VPN Gateway metryki](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
