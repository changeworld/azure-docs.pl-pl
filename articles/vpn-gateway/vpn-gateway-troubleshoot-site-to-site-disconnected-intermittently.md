---
title: Sporadyczne rozwiązywanie problemów z siecią VPN między lokacjami w usłudze Azure
description: Dowiedz się, jak rozwiązać problem, w którym połączenie sieci VPN między lokacjami jest regularnie rozłączane.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862564"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Rozwiązywanie problemów: sieć VPN między lokacjami w usłudze Azure rozłącza się sporadycznie

Może wystąpić problem, że nowe lub istniejące połączenie sieci VPN lokacji platformy Microsoft Azure nie jest stabilne lub regularnie się rozłącza. Ten artykuł zawiera kroki rozwiązywania problemów ułatwiające zidentyfikowanie i rozwiązanie przyczyny problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="prerequisite-step"></a>Krok wstępny

Sprawdź typ bramy sieci wirtualnej platformy Azure:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Sprawdź **na** stronie Przegląd bramy sieci wirtualnej informacje o typie.
    
    ![Przegląd bramy](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Sprawdzanie, czy lokalne urządzenie sieci VPN jest sprawdzane

1. Sprawdź, czy używasz [sprawdzonego urządzenia sieci VPN i wersji systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie sieci VPN nie jest weryfikowane, może być wymagane skontaktowanie się z producentem urządzenia, aby sprawdzić, czy nie występuje problem ze zgodnością.
2. Upewnij się, że urządzenie sieci VPN jest poprawnie skonfigurowane. Aby uzyskać więcej informacji, zobacz [Edytowanie przykładów konfiguracji urządzenia](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Sprawdź ustawienia skojarzenia zabezpieczeń(bramy sieci wirtualnej platformy Azure oparte na zasadach)

1. Upewnij się, że sieć wirtualna, podsieci i zakresy w definicji **bramy sieci lokalnej** na platformie Microsoft Azure są takie same jak konfiguracja na lokalnym urządzeniu sieci VPN.
2. Sprawdź, czy ustawienia skojarzenia zabezpieczeń są zgodne.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 Sprawdzanie tras zdefiniowanych przez użytkownika lub sieciowych grup zabezpieczeń w podsieci bramy

Trasa zdefiniowana przez użytkownika w podsieci bramy może ograniczać część ruchu i zezwalać na inny ruch. To sprawia, że wydaje się, że połączenie VPN jest zawodne dla niektórych ruchu i dobre dla innych. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 Sprawdź ustawienie "jeden tunel sieci VPN na parę podsieci" (w przypadku bram sieci wirtualnej opartych na zasadach)

Upewnij się, że lokalne urządzenie sieci VPN ma jeden **tunel sieci VPN na parę podsieci** dla bram sieci wirtualnej opartych na zasadach.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 Sprawdź, czy nie ma ograniczeń skojarzenia zabezpieczeń (w przypadku bram sieci wirtualnej opartych na zasadach)

Brama sieci wirtualnej oparta na zasadach ma limit 200 par skojarzenia zabezpieczeń podsieci. Jeśli liczba podsieci sieci wirtualnej platformy Azure pomnożona razy przez liczbę podsieci lokalnych jest większa niż 200, zobaczysz sporadyczne odłączanie podsieci.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 Sprawdzanie lokalnego adresu interfejsu zewnętrznego urządzenia sieci VPN

- Jeśli adres IP urządzenia sieci VPN skierowany do Internetu znajduje się w definicji **bramy sieci lokalnej** na platformie Azure, mogą wystąpić sporadyczne rozłączenia.
- Zewnętrzny interfejs urządzenia musi znajdować się bezpośrednio w Internecie. Między Internetem a urządzeniem nie powinno być translacji adresów sieciowych ani zapory.
-  Jeśli skonfigurujesz klastrowanie zapory tak, aby miało wirtualny adres IP, należy go przerwać i udostępnić urządzenie sieci VPN bezpośrednio do interfejsu publicznego, z którego brama może się połączyć.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 Sprawdź, czy lokalne urządzenie sieci VPN ma włączoną funkcję Perfect Forward Secrecy

Funkcja **Doskonała utajnianie do przodu** może powodować problemy z rozłączeniem. Jeśli urządzenie sieci VPN ma **włączoną funkcję Doskonałe utajnienie przekazywania,** wyłącz tę funkcję. Następnie [zaktualizuj zasady IPsec bramy sieci wirtualnej](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie połączenia lokacja lokacja z siecią wirtualną](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń sieci VPN między lokacjami](vpn-gateway-ipsecikepolicy-rm-powershell.md)

