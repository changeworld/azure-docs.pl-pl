---
title: Rozwiązywanie problemów z platformy Azure połączenia sieci VPN lokacja lokacja nie można nawiązać połączenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z połączeniem sieci VPN typu lokacja lokacja, nagle przestaje działać i nie może zostać zakończone.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3919243569035be41293ddc97c76a9f964cda7cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688501"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Rozwiązywanie problemów: Połączenia sieci VPN typu lokacja lokacja platformy Azure nie można nawiązać połączenia i przestaje działać

Po skonfigurowaniu połączenia sieci VPN typu lokacja lokacja między siecią lokalną a siecią wirtualną platformy Azure, połączenie sieci VPN nagle przestaje działać i nie może zostać zakończone. Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, najpierw spróbują [Resetowanie bramy Azure VPN gateway](vpn-gateway-resetgw-classic.md) i zresetuj tunelu z lokalnego urządzenia sieci VPN. Jeśli problem nie zniknie, wykonaj następujące kroki, aby zidentyfikować przyczynę problemu.

### <a name="prerequisite-step"></a>Kroku wymagań wstępnych

Sprawdź typ bramy sieci VPN platformy Azure.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Sprawdź **Przegląd** stronę bramy sieci VPN, aby uzyskać informacje o typie.
    
    ![Omówienie bramy](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Sprawdź, czy lokalne urządzenie sieci VPN jest zweryfikowany.

1. Sprawdź, czy używasz [zweryfikowane urządzenia sieci VPN i wersję systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie nie zostało zweryfikowane urządzenia sieci VPN, Niewykluczone, że do kontaktowania się z producentem urządzenia, aby sprawdzić, czy jest problem ze zgodnością.

2. Upewnij się, że urządzenie sieci VPN została poprawnie skonfigurowana. Aby uzyskać więcej informacji, zobacz [edytowanie przykładów konfiguracji urządzenia](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Sprawdź klucz współużytkowany

Porównaj klucza wstępnego dla lokalnego urządzenia sieci VPN na platformie Azure sieci wirtualnej VPN aby upewnić się, że klucze są takie same. 

Aby wyświetlić klucz współużytkowany dla połączenia sieci VPN platformy Azure, użyj jednej z następujących metod:

**Azure Portal**

1. Przejdź do połączenia lokacja lokacja bramy sieci VPN, który został utworzony.

2. W **ustawienia** kliknij **klucz współużytkowany**.
    
    ![Klucz współużytkowany](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W przypadku modelu wdrażania usługi Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Dla klasycznego modelu wdrażania:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Sprawdź element równorzędny adresów IP sieci VPN

-   Definicja adresów IP w **bramy sieci lokalnej** obiekt na platformie Azure powinien być zgodny adres IP urządzenia w środowisku lokalnym.
-   Definicja IP brama platformy Azure, która jest ustawiona na urządzenie lokalne powinien być zgodny adres IP bramy usługi Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Sprawdź Routing zdefiniowany przez użytkownika i sieciowymi grupami zabezpieczeń w podsieci bramy

Wyszukaj i Usuń routing zdefiniowany przez użytkownika (UDR) lub grupy zabezpieczeń sieci (NSG) w podsieci bramy, a następnie przetestować wynik. Jeśli problem nie zostanie rozwiązany, sprawdź poprawność ustawień, które trasy zdefiniowanej przez użytkownika lub sieciowej grupy zabezpieczeń stosowane.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Sprawdź adres interfejsu zewnętrznego urządzenia sieci VPN w środowisku lokalnym

- Jeśli adres IP dostępnym z Internetu, urządzenia sieci VPN jest uwzględniona w **sieci lokalnej** definicji na platformie Azure mogą zacząć występować sporadycznie odłączenia.
- Interfejs zewnętrzny urządzenia musi być bezpośrednio w Internecie. Powinna istnieć żadne translatora adresów sieciowych ani zapory między Internetem a urządzenia.
- Aby skonfigurować zaporę klastra mają wirtualnego adresu IP, możesz przerwać klastra i narazić urządzenie sieci VPN bezpośrednio na interfejs publiczny, który bramy może współpracować z usługą.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Sprawdź, czy podsieci są dokładnie zgodne (bramy platformy Azure na podstawie zasad)

-   Upewnij się, że przestrzenie adresów sieci wirtualnej są takie same dokładnie między siecią wirtualną platformy Azure i definicje w środowisku lokalnym.
-   Sprawdź, czy podsieci pasują dokładnie między **bramy sieci lokalnej** lokalnych i w definicji dla sieci lokalnej.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Sprawdź sondy kondycji brama platformy Azure

1. Sonda kondycji otwarte, przechodząc do następującego adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kliknij ostrzeżenie o certyfikacie.
3. Jeśli pojawi się odpowiedź bramy sieci VPN jest uważany za dobrej kondycji. Jeśli nie otrzymasz odpowiedzi, brama może nie być w dobrej kondycji lub sieciowej grupy zabezpieczeń w ramach bramy podsieci jest przyczyną problemu. Poniższy tekst to przykładowej odpowiedzi:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Sprawdź, czy lokalne urządzenie sieci VPN ma włączoną funkcję doskonałe utajnienie przekazywania

Funkcja doskonałe utajnienie przekazywania może powodować problemy rozłączenia. Jeśli urządzenie sieci VPN zawiera doskonałe utajnienie przekazywania włączone, należy wyłączyć tę funkcję. Następnie zaktualizuj zasad protokołu IPsec bramy sieci VPN.

## <a name="next-steps"></a>Kolejne kroki

-   [Konfigurowanie połączenia lokacja lokacja z siecią wirtualną](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń VPN typu lokacja lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)
