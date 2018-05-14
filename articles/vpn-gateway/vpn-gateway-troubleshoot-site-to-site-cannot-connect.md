---
title: Rozwiązywanie problemów z Azure połączenia sieci VPN lokacja lokacja nie może połączyć | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z połączeniem sieci VPN lokacja lokacja, nagle przestanie działać i nie można ponownie.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: dfd29e0956793cf776b9c0ea5ddbd4689ebcb015
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Rozwiązywanie problemów: Nie można połączyć z połączenia sieci VPN typu lokacja lokacja Azure oraz przestaje działać

Po skonfigurowaniu połączenia VPN lokacja lokacja między siecią lokalną i sieci wirtualnej platformy Azure, połączenie sieci VPN nagle przestanie działać i nie można ponownie. Ten artykuł zawiera kroki rozwiązywania problemów, aby rozwiązać ten problem. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, najpierw spróbuj [zresetowanie bramy sieci VPN platformy Azure](vpn-gateway-resetgw-classic.md) i zresetuj tunelu z lokalnego urządzenia sieci VPN. Jeśli problem będzie nadal występował, wykonaj następujące kroki, aby zidentyfikować przyczynę problemu.

### <a name="prerequisite-step"></a>Krok wymagań wstępnych

Sprawdź typ bramy sieci VPN platformy Azure.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

2. Sprawdź **omówienie** strony bramy sieci VPN, aby uzyskać informacje o typie.
    
    ![Omówienie bramy](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Sprawdź, czy jest zweryfikowany lokalnego urządzenia sieci VPN

1. Sprawdź, czy używasz [zweryfikowane urządzenia sieci VPN i wersję systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie nie jest zweryfikowany urządzenia sieci VPN, może być konieczne skontaktuj się z producentem urządzenia, aby sprawdzić, czy problem ze zgodnością.

2. Upewnij się, że urządzenia sieci VPN został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [Edytuj przykłady konfiguracji urządzenia](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Sprawdź klucza wstępnego

Porównaj udostępniony klucz do lokalnego urządzenia sieci VPN do wirtualnej Azure sieci VPN do upewnij się, że klucze są zgodne. 

Aby wyświetlić klucza wspólnego dla połączenia sieci VPN platformy Azure, użyj jednej z następujących metod:

**Azure Portal**

1. Przejdź do utworzonego połączenia lokacja lokacja bramy sieci VPN.

2. W **ustawienia** kliknij **klucz udostępniony**.
    
    ![Klucz współużytkowany](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Dla modelu wdrażania usługi Azure Resource Manager:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Dla klasycznym modelu wdrażania:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Sprawdź adresy IP elementu równorzędnego sieci VPN

-   Definicja IP w **bramy sieci lokalnej** obiektu w usłudze Azure powinna być zgodna adres IP urządzenia lokalnego.
-   Definicji IP bramy Azure, która jest ustawiona na urządzeniu lokalnym powinna odpowiadać IP bramy platformy Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Sprawdź przez i grup NSG podsieci bramy

Sprawdź, czy i Usuń użytkownika routingu (przez) lub grupy zabezpieczeń sieci (NSG) w podsieci bramy, a następnie wyniku testu. Jeśli problem nie zostanie rozwiązany, sprawdź poprawność ustawień, które stosowane przez lub NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Sprawdź adres interfejsu zewnętrznego urządzenia sieci VPN lokalnej

- Jeśli adres internetowy urządzenia sieci VPN jest uwzględniona w **sieci lokalnej** definicji na platformie Azure, mogą występować sporadycznie rozłączeń.
- Interfejs zewnętrzne urządzenie musi być bezpośrednio połączony z Internetem. Powinien istnieć nie translatora adresów sieciowych lub urządzenia od Internetu zaporą.
- Aby skonfigurować zaporę klaster ma wirtualnego adresu IP, musi przerwać klastra i ujawnia urządzenia sieci VPN bezpośrednio na interfejs publiczny, który bramy mogą łączyć się z.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Sprawdź, czy podsieci są takie same (Azure bramy oparte na zasadach)

-   Sprawdź, czy space(s) adresów sieci wirtualnej pasują dokładnie między sieci wirtualnej platformy Azure i lokalnymi definicje.
-   Sprawdź, czy podsieci pasują dokładnie między **bramy sieci lokalnej** lokalnych i w definicji sieci lokalnej.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Sprawdź sondy kondycji Azure bramy

1. Sondy kondycji otwarte, przechodząc pod następujący adres URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kliknij przycisk za pomocą ostrzeżenie o certyfikacie.
3. Jeśli otrzymasz odpowiedź bramy sieci VPN jest uznawany za dobrej kondycji. Jeśli nie otrzymasz odpowiedź bramy może nie być w dobrej kondycji lub grupy NSG podsieci bramy jest przyczyną problemu. Przykładowa odpowiedź brzmi:

    &lt;? wersji xml = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">podstawowego: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / ciąg&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Sprawdź, czy lokalnego urządzenia sieci VPN jest włączona funkcja doskonałego utajnienia przekazywania

Funkcja doskonałego utajnienia może spowodować problemy rozłączenia. Jeśli urządzenie sieci VPN, ma doskonałego utajnienia, należy wyłączyć tę funkcję. Następnie zaktualizuj zasady IPsec bramy sieci VPN.

## <a name="next-steps"></a>Kolejne kroki

-   [Skonfiguruj połączenie lokacja lokacja sieci wirtualnej](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurowanie zasad IPsec i IKE dla połączeń VPN lokacja lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)
