---
title: Rozwiązywanie problemów z łącznością usługi Azure Private Link
description: Wskazówki krok po kroku dotyczące diagnozowania łączności z łączami prywatnymi
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 1e5253d617c87d5869cebc817da6d265ebfdfa7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539471"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Rozwiązywanie problemów z łącznością usługi Azure Private Link

Ten artykuł zawiera wskazówki krok po kroku, aby sprawdzić poprawność i zdiagnozować łączność dla konfiguracji łącza prywatnego platformy Azure.

Za pomocą usługi Azure Private Link można uzyskać dostęp do platformy Azure jako usługi (PaaS), takich jak Usługa Azure Storage, usługa Azure Cosmos DB i usługa Azure SQL Database oraz usługi hostowane przez klientów lub partnerów platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą przechodzi przez sieć szkieletową firmy Microsoft, co eliminuje narażenie z publicznego Internetu. Można również utworzyć własną usługę linków prywatnych w sieci wirtualnej i dostarczyć ją prywatnie do klientów.

Można włączyć usługę, która działa za standardową warstwą Azure Load Balancer dla dostępu do łączy prywatnych. Konsumenci usługi można utworzyć prywatny punkt końcowy wewnątrz ich sieci wirtualnej i mapować go do tej usługi, aby uzyskać do niego dostęp prywatnie.

Oto scenariusze łączności, które są dostępne w łączu prywatnym:

- Sieć wirtualna z tego samego regionu
- Regionalne sieci wirtualne
- Globalnie równorzędne sieci wirtualne
- Lokalnie klient za pośrednictwem sieci VPN lub obwodów usługi Azure ExpressRoute

## <a name="deployment-troubleshooting"></a>Rozwiązywanie problemów z wdrażaniem

Przejrzyj informacje na temat [Wyłączanie zasad sieciowych w usłudze łączy prywatnych](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) w celu rozwiązywania problemów z przypadkami, w których nie można wybrać źródłowego adresu IP z wybranej podsieci dla usługi łączy prywatnych.

Upewnij się, że ustawienie **privateLinkServiceNetworkPolicies** jest wyłączone dla podsieci, z której wybierasz źródłowy adres IP.

## <a name="diagnose-connectivity-problems"></a>Diagnozowanie problemów z łącznością

Jeśli występują problemy z łącznością z konfiguracją łączy prywatnych, zapoznaj się z tymi krokami, aby upewnić się, że wszystkie zwykłe konfiguracje są zgodnie z oczekiwaniami.

1. Przejrzyj konfigurację łącza prywatnego, przeglądając zasób.

    a. Przejdź do **centrum łączy prywatnych**.

      ![Prywatne centrum łączy](./media/private-link-tsg/private-link-center.png)

    b. W lewym okienku wybierz pozycję **Prywatne usługi łączy**.

      ![Prywatne usługi linkowe](./media/private-link-tsg/private-link-service.png)

    d. Filtruj i wybierz prywatną usługę łącza, którą chcesz zdiagnozować.

    d. Przejrzyj połączenia prywatnego punktu końcowego.
     - Upewnij się, że prywatny punkt końcowy, z którego szukasz łączności, jest wyświetlany ze stanem **zatwierdzonego** połączenia.
     - Jeśli stan jest **oczekujące,** wybierz go i zatwierdzić.

       ![Prywatne połączenia z punktami końcowymi](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Przejdź do prywatnego punktu końcowego, z którego nawiązujesz połączenie, wybierając nazwę. Upewnij się, że stan połączenia jest wyświetlany jako **Zatwierdzony**.

       ![Omówienie połączenia prywatnego punktu końcowego](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po zatwierdzeniu obu stron spróbuj ponownie nałączyć łączność.

    e. Przejrzyj **alias** na karcie **Przegląd** i **Identyfikator zasobu** na karcie **Właściwości.**
     - Upewnij się, że informacje o **identyfikatorze aliasu** i **identyfikatorze zasobu** są zgodne z **identyfikatorem aliasu** i **zasobu** używanym do utworzenia prywatnego punktu końcowego tej usługi.

       ![Weryfikowanie informacji o aliasach](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Weryfikowanie informacji o identyfikatorze zasobu](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Przejrzyj informacje o **widoczności** na karcie **Przegląd.**
     - Upewnij się, że subskrypcja mieści się w zakresie **widoczności.**

       ![Weryfikowanie informacji o widoczności](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. Przejrzyj informacje o **równoważce obciążenia** na karcie **Przegląd.**
     - Można przejść do modułu równoważenia obciążenia, wybierając łącze modułu równoważenia obciążenia.

       ![Weryfikowanie informacji o równoważce obciążenia](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Upewnij się, że ustawienia modułu równoważenia obciążenia są skonfigurowane zgodnie z oczekiwaniami.
       - Przejrzyj **konfigurację ip frontu**.
       - Przegląd **pul zaplecza**.
       - Przejrzyj **reguły równoważenia obciążenia**.

       ![Weryfikowanie właściwości modułu równoważenia obciążenia](./media/private-link-tsg/pls-ilb-properties.png)

     - Upewnij się, że moduł równoważenia obciążenia działa zgodnie z poprzednimi ustawieniami.
       - Wybierz maszynę wirtualną w dowolnej podsieci innej niż podsieć, w której dostępna jest pula zaplecza modułu równoważenia obciążenia.
       - Spróbuj uzyskać dostęp do frontołowa końcówka modułu równoważenia obciążenia z poprzedniej maszyny Wirtualnej.
       - Jeśli połączenie zostanie uruchomione z puli zaplecza zgodnie z regułami równoważenia obciążenia, moduł równoważenia obciążenia działa.
       - Można również przejrzeć metryki modułu równoważenia obciążenia za pośrednictwem usługi Azure Monitor, aby sprawdzić, czy dane przepływają przez moduł równoważenia obciążenia.

1. Użyj [usługi Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/overview) aby sprawdzić, czy dane są przepływające.

    a. W zasobie usługi łączy prywatnych wybierz pozycję **Metryki**.
     - Wybierz **opcję Bajty w** lub **Bajty .**
     - Sprawdź, czy dane są przepływające podczas próby nawiązania połączenia z usługą łącza prywatnego. Spodziewaj się opóźnienia wynoszącego około 10 minut.

       ![Weryfikowanie danych usługi łączy prywatnych](./media/private-link-tsg/pls-metrics.png)

1. Skontaktuj się z zespołem [pomocy technicznej platformy Azure,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) jeśli problem jest nadal nierozwiązany i nadal występuje problem z łącznością.

## <a name="next-steps"></a>Następne kroki

 * [Tworzenie prywatnej usługi łącza (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)
 * [Przewodnik dotyczący rozwiązywania problemów z prywatnym punktem końcowym platformy Azure](troubleshoot-private-endpoint-connectivity.md)
