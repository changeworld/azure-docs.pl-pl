---
title: 'Rozwiązywanie problemów z połączeniem sieci VPN między lokacjami platformy Azure, które nie może połączyć titleSux: Brama sieci VPN platformy Azure'
description: Dowiedz się, jak rozwiązać problem połączenia sieci VPN między lokacjami, które nagle przestaje działać i nie można ich ponownie połączyć.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862581"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Rozwiązywanie problemów: połączenie sieci VPN między lokacjami platformy Azure nie może się połączyć i przestaje działać

Po skonfigurowaniu połączenia sieci VPN lokacja lokacja między siecią lokalną a siecią wirtualną platformy Azure połączenie sieci VPN nagle przestaje działać i nie można go ponownie połączyć. Ten artykuł zawiera kroki rozwiązywania problemów ułatwiające rozwiązanie tego problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Aby rozwiązać ten problem, najpierw spróbuj [zresetować bramę sieci VPN platformy Azure](vpn-gateway-resetgw-classic.md) i zresetować tunel z lokalnego urządzenia sieci VPN. Jeśli problem będzie się powtarzał, wykonaj następujące kroki, aby zidentyfikować przyczynę problemu.

### <a name="prerequisite-step"></a>Krok wstępny

Sprawdź typ bramy sieci VPN platformy Azure.

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

2. Sprawdź **na** stronie Przegląd bramy sieci VPN informacje o typie.
    
    ![Omówienie bramy](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Sprawdzanie, czy lokalne urządzenie sieci VPN jest sprawdzane

1. Sprawdź, czy używasz [sprawdzonego urządzenia sieci VPN i wersji systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie nie jest zweryfikowanym urządzeniem sieci VPN, może być wymagane skontaktowanie się z producentem urządzenia, aby sprawdzić, czy występuje problem ze zgodnością.

2. Upewnij się, że urządzenie sieci VPN jest poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [Edytowanie przykładów konfiguracji urządzenia](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Weryfikowanie klucza udostępnionego

Porównaj klucz udostępniony lokalnego urządzenia sieci VPN z siecią VPN usługi Azure Virtual Network, aby upewnić się, że klucze są zgodne. 

Aby wyświetlić klucz udostępniony połączenia sieci VPN platformy Azure, użyj jednej z następujących metod:

**Portal Azure**

1. Przejdź do utworzonego połączenia lokacja-lokacja bramy sieci VPN.

2. W sekcji **Ustawienia** kliknij pozycję **Klucz udostępniony**.
    
    ![Klucz współużytkowany](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dla modelu wdrażania usługi Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Dla klasycznego modelu wdrażania:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Weryfikowanie równorzędnych serwerów IP sieci VPN

-   Definicja adresu IP w obiekcie **Bramy sieci lokalnej** na platformie Azure powinna być zgodna z lokalnym adresem IP urządzenia.
-   Definicja ip bramy platformy Azure, która jest ustawiona na urządzeniu lokalnym, powinna być zgodna z adresem IP bramy platformy Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Sprawdź UDR i NSG w podsieci bramy

Sprawdź i usuń routing zdefiniowany przez użytkownika (UDR) lub sieciowe grupy zabezpieczeń (NSG) w podsieci bramy, a następnie przetestuj wynik. Jeśli problem zostanie rozwiązany, sprawdź poprawność ustawień, które zostały zastosowane UDR lub NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Sprawdzanie lokalnego adresu interfejsu zewnętrznego urządzenia sieci VPN

- Jeśli adres IP urządzenia sieci VPN związany z Internetem znajduje się w definicji **sieci lokalnej** na platformie Azure, mogą wystąpić sporadyczne rozłączenia.
- Zewnętrzny interfejs urządzenia musi znajdować się bezpośrednio w Internecie. Między Internetem a urządzeniem nie powinno być translacji adresu sieciowego ani zapory.
- Aby skonfigurować klastrowanie zapory tak, aby miało wirtualny adres IP, należy przerwać klaster i udostępnić urządzenie sieci VPN bezpośrednio do interfejsu publicznego, z którego brama może się połączyć.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Sprawdź, czy podsieci są dokładnie zgodne (bramy oparte na zasadach platformy Azure)

-   Sprawdź, czy przestrzeń adresowa sieci wirtualnej jest dokładnie zgodna z definicjami sieci wirtualnej platformy Azure i lokalnymi.
-   Sprawdź, czy podsieci są dokładnie zgodne z definicjami **bramy sieci lokalnej** i lokalnych dla sieci lokalnej.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Weryfikowanie sondy kondycji bramy platformy Azure

1. Otwórz sondę kondycji, przeglądając następujący adres URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kliknij ostrzeżenie certyfikatu.
3. Jeśli otrzymasz odpowiedź, brama sieci VPN jest uważana za w dobrej kondycji. Jeśli nie otrzymasz odpowiedzi, brama może nie być w dobrej kondycji lub sieciowej sieciowej w podsieci bramy jest przyczyną problemu. Poniższy tekst jest przykładową odpowiedzią:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Sprawdź, czy lokalne urządzenie sieci VPN ma włączoną funkcję doskonałego zachowania tajemnicy do przodu

Doskonała funkcja zachowania tajemnicy do przodu może powodować problemy z rozłączeniem. Jeśli urządzenie sieci VPN ma włączoną doskonałą tajemnicę przekazywania, wyłącz tę funkcję. Następnie zaktualizuj zasady IPsec bramy sieci VPN.

## <a name="next-steps"></a>Następne kroki

-   [Konfigurowanie połączenia lokacja-lokacja z siecią wirtualną](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń sieci VPN między lokacjami](vpn-gateway-ipsecikepolicy-rm-powershell.md)
