---
title: Rozwiązywanie problemów z łącznością prywatnego punktu końcowego platformy Azure
description: Wskazówki krok po kroku umożliwiające zdiagnozowanie łączności z prywatnym punktem końcowym
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
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191069"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Rozwiązywanie problemów z łącznością z prywatnym punktem końcowym

Ten przewodnik zawiera wskazówki krok po kroku dotyczące sprawdzania poprawności i zdiagnozowania konfiguracji prywatnego połączenia z punktem końcowym. 

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą linku prywatnego. To rozwiązanie pomaga w zabezpieczeniu obciążeń na platformie Azure, zapewniając prywatną łączność z zasobami usługi platformy Azure z sieci wirtualnej. Dzięki temu te usługi są efektywnie umieszczane w sieci wirtualnej. 

Oto scenariusze łączności, które są dostępne z prywatnymi punktami końcowymi 
- Sieć wirtualna z tego samego regionu 
- regionalne sieci wirtualne równorzędne
- globalnie równorzędne sieci wirtualne
- Klient lokalny dla sieci VPN lub obwodów usługi Express Route

## <a name="diagnosing-connectivity-problems"></a>Diagnozowanie problemów z łącznością 
Wykonaj kroki opisane poniżej, aby upewnić się, że wszystkie typowe konfiguracje są rozpoznawane zgodnie z oczekiwaniami, aby rozwiązać problemy z łącznością z konfiguracją prywatnego punktu końcowego.

1. Przejrzyj konfigurację prywatnego punktu końcowego, przeglądając zasób 

    a) przejdź do **prywatnego centrum linków**

      ![Prywatne centrum linków](./media/private-endpoint-tsg/private-link-center.png)

    b) wybierz prywatne punkty końcowe z okienka nawigacji po lewej stronie
    
      ![Prywatne punkty końcowe](./media/private-endpoint-tsg/private-endpoints.png)

    c) filtrowanie i wybieranie prywatnego punktu końcowego, który ma zostać zdiagnozowany

    d) Przejrzyj informacje dotyczące sieci wirtualnej i usługi DNS
    
     - Zweryfikowano stan połączenia
     - Upewnij się, że maszyna wirtualna ma łączność z siecią wirtualną obsługującą prywatne punkty końcowe
     - Informacje o nazwie FQDN (kopiowanie) i przypisany prywatny adres IP
    
       ![Konfiguracja sieci wirtualnej i systemu DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Użyj [**Azure monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) , aby sprawdzić, czy dane przepływają

    a) dla zasobu prywatnego punktu końcowego wybierz pozycję **Monitoruj**
     - Wybierz pozycję dane lub dane i sprawdź, czy dane przepływają podczas próby nawiązania połączenia z prywatnym punktem końcowym. Oczekiwano opóźnienia około 10 minut.
    
       ![Weryfikowanie telemetrii prywatnego punktu końcowego](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Użyj rozwiązywania problemów z połączeniem maszyny wirtualnej z **Network Watcher**

    a) wybierz maszynę wirtualną klienta

    b) wybierz sekcję **Rozwiązywanie problemów z połączeniami** , karta **połączenie wychodzące**
    
      ![Network Watcher — Testowanie połączeń wychodzących](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) wybierz pozycję **użyj Network Watcher, aby uzyskać szczegółowe śledzenie połączeń**
    
      ![Network Watcher — Rozwiązywanie problemów z połączeniem](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) wybierz pozycję **Testuj według nazwy FQDN**
     - Wklej nazwę FQDN z prywatnego zasobu punktu końcowego
     - Podaj port (*zwykle 443 dla usługi Azure Storage lub Cosmos, 1336 dla SQL...* )

    e) kliknij pozycję **Testuj** i sprawdź poprawność wyników testu.
    
      ![Network Watcher — wyniki testu](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. Rozpoznawanie nazw DNS z wyników testów musi mieć ten sam prywatny adres IP przypisany do prywatnego punktu końcowego

    a) Jeśli ustawienia DNS są nieprawidłowe, wykonaj następujące czynności:
     - Przy użyciu strefy prywatnej: 
       - Upewnij się, że sieć wirtualna sieci wirtualnej klienta jest skojarzona z strefą prywatną
       - Sprawdź, Prywatna strefa DNS rekord strefy istnieje, Utwórz, jeśli nie jest istniejący
    
     - Używanie niestandardowej usługi DNS:
       - Sprawdź ustawienia DNS klienta i sprawdź poprawność konfiguracji DNS.
       Aby uzyskać wskazówki, zobacz [Omówienie prywatnego punktu końcowego — konfiguracja systemu DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) .

    b) Jeśli łączność kończy się niepowodzeniem z powodu sieciowej grupy zabezpieczeń/UDR
     - Przejrzyj reguły ruchu wychodzącego sieciowej grupy zabezpieczeń i Utwórz odpowiednie reguły ruchu wychodzącego, aby zezwolić na ruch
    
       ![Reguły ruchu wychodzącego sieciowej grupy zabezpieczeń](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Jeśli połączenie ma zweryfikowane wyniki, problem z łącznością może być związany z innymi aspektami, takimi jak wpisy tajne, tokeny i hasła w warstwie aplikacji.
   - W takim przypadku Przejrzyj konfigurację zasobu linku prywatnego skojarzonego z prywatnym punktem końcowym. Zapoznaj się z [przewodnikiem rozwiązywania problemów z prywatnym linkiem](troubleshoot-private-link-connectivity.md). 

6. Skontaktuj się z zespołem [pomocy technicznej platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , jeśli problem nadal nie został rozwiązany i nadal istnieje problem z połączeniem. 

## <a name="next-steps"></a>Następne kroki

 * [Utwórz prywatny punkt końcowy w zaktualizowanej podsieci (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Przewodnik rozwiązywania problemów z linkiem prywatnym](troubleshoot-private-link-connectivity.md)
