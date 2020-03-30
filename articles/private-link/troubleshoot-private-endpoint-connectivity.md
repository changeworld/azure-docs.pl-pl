---
title: Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure
description: Wskazówki krok po kroku dotyczące diagnozowania łączności z prywatnymi punktami końcowymi
services: private-endpoint
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
ms.openlocfilehash: fcc482e6231bbd925fd500a37989052765dede58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538538"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym platformy Azure

Ten artykuł zawiera wskazówki krok po kroku, aby sprawdzić poprawność i zdiagnozować konfigurację łączności prywatnego punktu końcowego platformy Azure.

Private Endpoint platformy Azure to interfejs sieciowy, który łączy cię prywatnie i bezpiecznie z usługą łącza prywatnego. To rozwiązanie pomaga zabezpieczyć obciążenia na platformie Azure, zapewniając prywatną łączność z zasobami usługi platformy Azure z sieci wirtualnej. To rozwiązanie skutecznie przenosi te usługi do sieci wirtualnej.

Oto scenariusze łączności, które są dostępne w private endpoint:

- Sieć wirtualna z tego samego regionu
- Regionalne sieci wirtualne
- Globalnie równorzędne sieci wirtualne
- Lokalnie klient za pośrednictwem sieci VPN lub obwodów usługi Azure ExpressRoute

## <a name="diagnose-connectivity-problems"></a>Diagnozowanie problemów z łącznością 

Przejrzyj te kroki, aby upewnić się, że wszystkie zwykłe konfiguracje są zgodnie z oczekiwaniami, aby rozwiązać problemy z łącznością z konfiguracją prywatnego punktu końcowego.

1. Przejrzyj konfigurację prywatnego punktu końcowego, przeglądając zasób.

    a. Przejdź do **centrum łączy prywatnych**.

      ![Prywatne centrum łączy](./media/private-endpoint-tsg/private-link-center.png)

    b. W lewym okienku wybierz pozycję **Prywatne punkty końcowe**.
    
      ![Prywatne punkty końcowe](./media/private-endpoint-tsg/private-endpoints.png)

    d. Filtruj i wybierz prywatny punkt końcowy, który chcesz zdiagnozować.

    d. Przejrzyj informacje o sieci wirtualnej i DNS.
     - Sprawdź, czy stan połączenia jest **zatwierdzony**.
     - Upewnij się, że maszyna wirtualna ma łączność z siecią wirtualną, która obsługuje prywatne punkty końcowe.
     - Sprawdź, czy są przypisane informacje FQDN (kopia) i prywatny adres IP.
    
       ![Konfiguracja sieci wirtualnej i dns](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Użyj [usługi Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/overview) aby sprawdzić, czy dane są przepływające.

    a. W zasób prywatnego punktu końcowego wybierz pozycję **Monitor**.
     - Wybierz **pozycję Wł.** lub **Wyjście danych**. 
     - Sprawdź, czy dane są przepływające podczas próby nawiązania połączenia z prywatnym punktem końcowym. Spodziewaj się opóźnienia wynoszącego około 10 minut.
    
       ![Weryfikowanie danych telemetrycznych prywatnego punktu końcowego](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Użyj **rozwiązywania problemów z połączeniem maszyn wirtualnych** z usługi Azure Network Watcher.

    a. Wybierz maszynę wirtualną klienta.

    b. Wybierz **pozycję Rozwiązywanie problemów z połączeniem,** a następnie wybierz kartę **Połączenia wychodzące.**
    
      ![Obserwator sieci — testowanie połączeń wychodzących](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    d. Wybierz **pozycję Użyj obserwatora sieciowego, aby uzyskać szczegółowe śledzenie połączenia**.
    
      ![Kontrola sieci — rozwiązywanie problemów z połączeniem](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Wybierz **opcję Testuj według FQDN**.
     - Wklej plik FQDN z prywatnego zasobu punktu końcowego.
     - Podaj port. Zazwyczaj należy użyć 443 dla usługi Azure Storage lub usługi Azure Cosmos DB i 1336 dla języka SQL.

    e. Wybierz **opcję Testuj**i sprawdź poprawność wyników testu.
    
      ![Network Watcher - Wyniki testów](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. Rozpoznawanie DNS z wyników testu musi mieć ten sam prywatny adres IP przypisany do prywatnego punktu końcowego.

    a. Jeśli ustawienia DNS są nieprawidłowe, wykonaj następujące czynności:
     - Jeśli używasz strefy prywatnej: 
       - Upewnij się, że sieć wirtualna maszyny Wirtualnej klienta jest skojarzona ze strefą prywatną.
       - Sprawdź, czy istnieje rekord prywatnej strefy DNS. Jeśli nie istnieje, utwórz go.
     - Jeśli używasz niestandardowego dns:
       - Przejrzyj niestandardowe ustawienia DNS i sprawdź, czy konfiguracja DNS jest poprawna.
       Aby uzyskać wskazówki, zobacz [Omówienie prywatnego punktu końcowego: konfiguracja DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b. Jeśli łączność nie powiódł się z powodu sieciowych grup zabezpieczeń (NSG) lub tras zdefiniowanych przez użytkownika:
     - Przejrzyj reguły ruchu wychodzącego sieciowej sieciowej i utwórz odpowiednie reguły ruchu wychodzącego, aby zezwolić na ruch.
    
       ![Reguły ruchu wychodzącego sieciowych sieci SSG](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. Jeśli połączenie ma zweryfikowane wyniki, problem z łącznością może być związany z innymi aspektami, takimi jak wpisy tajne, tokeny i hasła w warstwie aplikacji.
   - W takim przypadku przejrzyj konfigurację zasobu łącza prywatnego skojarzonego z prywatnym punktem końcowym. Aby uzyskać więcej informacji, zobacz [przewodnik rozwiązywania problemów z łączem prywatnym platformy Azure](troubleshoot-private-link-connectivity.md).

1. Skontaktuj się z zespołem [pomocy technicznej platformy Azure,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) jeśli problem jest nadal nierozwiązany i nadal występuje problem z łącznością.

## <a name="next-steps"></a>Następne kroki

 * [Tworzenie prywatnego punktu końcowego w zaktualizowanej podsieci (Azure portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
 * [Przewodnik dotyczący rozwiązywania problemów z łączem prywatnym usługi Azure](troubleshoot-private-link-connectivity.md)
