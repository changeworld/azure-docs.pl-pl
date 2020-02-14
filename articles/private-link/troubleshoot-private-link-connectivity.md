---
title: Rozwiązywanie problemów z łącznością z linkiem prywatnym platformy Azure
description: Wskazówki krok po kroku dotyczące diagnozowania łączności z prywatnym linkiem
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
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191054"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Rozwiązywanie problemów z łącznością usługi Private Link

Ten przewodnik zawiera wskazówki krok po kroku dotyczące sprawdzania poprawności i diagnozowania łączności w ramach instalacji usługi link prywatny. 

Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS (np. Azure Storage, Azure Cosmos DB i SQL Database) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz również utworzyć własną prywatną usługę linku w sieci wirtualnej (VNet) i dostarczyć ją prywatnie do klientów. 

Usługę można włączyć za pomocą usługi Azure usługa Load Balancer w warstwie Standardowa na potrzeby prywatnego dostępu do łącza. Konsumenci usługi mogą utworzyć prywatny punkt końcowy wewnątrz sieci wirtualnej i zmapować ją na tę usługę, aby uzyskać do niej dostęp prywatnie.

Oto scenariusze łączności, które są dostępne w usłudze link prywatny
- Sieć wirtualna z tego samego regionu 
- regionalne sieci wirtualne równorzędne
- globalnie równorzędne sieci wirtualne
- Klient lokalny dla sieci VPN lub obwodów usługi Express Route

## <a name="deployment-troubleshooting"></a>Rozwiązywanie problemów z wdrażaniem

Zapoznaj się z informacjami na temat [wyłączania zasad sieciowych w usłudze linku prywatnego](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) w celu rozwiązywania problemów, gdy nie możesz wybrać źródłowego adresu IP z wybranej podsieci dla Twojej usługi linku prywatnego.

Upewnij się, że ustawienie **privateLinkServiceNetworkPolicies** jest wyłączone dla podsieci, z której jest wybierany źródłowy adres IP.

## <a name="diagnosing-connectivity-problems"></a>Diagnozowanie problemów z łącznością

Jeśli występują problemy z łącznością z konfiguracją linku prywatnego, wykonaj czynności opisane poniżej, aby upewnić się, że wszystkie typowe konfiguracje są zgodnie z oczekiwaniami.

1. Przejrzyj konfigurację usługi link prywatny, przeglądając zasób 

    a) przejdź do **prywatnego centrum linków**

      ![Prywatne centrum linków](./media/private-link-tsg/private-link-center.png)

    b) wybierz pozycję **usługi połączeń prywatnych** w okienku nawigacji po lewej stronie

      ![Usługi linków prywatnych](./media/private-link-tsg/private-link-service.png)

    c) filtrowanie i wybieranie usługi linku prywatnego, którą chcesz zdiagnozować

    d) przeglądanie połączeń prywatnych punktów końcowych
     - Upewnij się, że prywatny punkt końcowy, z którego poszukujesz łączności, znajduje się na liście ze stanem **zatwierdzone** połączenie. 
     - Jeśli jest to **oczekiwane**, wybierz ją i zatwierdź. 

       ![Połączenia prywatnego punktu końcowego](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Przejdź do prywatnego punktu końcowego, z którego nawiązujesz połączenie, klikając nazwę. Upewnij się, że stan połączenia jest wyświetlany jako **zatwierdzone**.

       ![Omówienie połączenia z prywatnym punktem końcowym](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Po zatwierdzeniu obu stron spróbuj ponownie nawiązać połączenie.

    e) Przejrzyj **alias** na karcie Przegląd i **Identyfikator zasobu** na karcie właściwości 
     - Upewnij się, że **Identyfikator aliasu/zasobu** jest zgodny z **identyfikatorem aliasu/zasobu** , którego używasz, aby utworzyć prywatny punkt końcowy dla tej usługi. 

       ![Weryfikuj alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Weryfikowanie identyfikatora zasobu](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) przegląd informacji o widoczności (sekcja Przegląd)
     - Upewnij się, że Twoja subskrypcja mieści się w zakresie **widoczności**

       ![Weryfikuj widoczność](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) przegląd informacji o Load Balancer (omówienie)
     - Aby przejść do modułu równoważenia obciążenia, kliknij link usługi równoważenia obciążenia.

       ![Sprawdź Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Upewnij się, że ustawienia Load Balancer są skonfigurowane zgodnie z oczekiwaniami
       - Przegląd konfiguracji adresu IP frontonu
       - Przeglądanie pul zaplecza
       - Przegląd reguł równoważenia obciążenia

       ![Weryfikuj Load Balancer właściwości](./media/private-link-tsg/pls-ilb-properties.png)

     - Upewnij się, że Load Balancer działa zgodnie z powyższymi ustawieniami
       - Wybierz maszynę wirtualną w dowolnej podsieci, w której jest dostępna Load Balancer Pula zaplecza
       - Spróbuj uzyskać dostęp do frontonu modułu równoważenia obciążenia z maszyny wirtualnej powyżej
       - Jeśli połączenie nadaje się do puli zaplecza zgodnie z regułami równoważenia obciążenia, moduł równoważenia obciążenia jest operacyjny
       - Możesz również przejrzeć metrykę Load Balancer za pomocą Azure Monitor, aby sprawdzić, czy dane przepływają przez moduł równoważenia obciążenia

2. Użyj [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) , aby sprawdzić, czy dane przepływają

    a) w obszarze zasób usługi link prywatny wybierz pozycję **metryki** .
     - Wybierz **bajty (w** **bajtach** )
     - Podczas próby nawiązania połączenia z usługą linku prywatnego przepływają dane. Oczekiwano opóźnienia około 10 minut.

       ![Weryfikowanie metryk usługi linku prywatnego](./media/private-link-tsg/pls-metrics.png)

3. Skontaktuj się z zespołem [pomocy technicznej platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , jeśli problem nadal nie został rozwiązany i nadal istnieje problem z połączeniem. 

## <a name="next-steps"></a>Następne kroki

 * [Tworzenie usługi linku prywatnego (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Przewodnik rozwiązywania problemów z prywatnym punktem końcowym](troubleshoot-private-endpoint-connectivity.md)
