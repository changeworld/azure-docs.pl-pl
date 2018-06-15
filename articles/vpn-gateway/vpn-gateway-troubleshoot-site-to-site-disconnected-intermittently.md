---
title: Rozwiązywanie problemów z Azure sporadycznie zakończy połączenie sieci VPN typu lokacja-lokacja | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym połączenie sieci VPN typu lokacja-lokacja rozłączone regularnie.
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
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160588"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Rozwiązywanie problemów: Azure VPN lokacja-lokacja rozłącza sporadycznie

Może wystąpić problem, że nowe lub istniejące połączenie sieci VPN typu lokacja-lokacja Microsoft Azure nie jest stabilna lub odłącza regularnie. Ten artykuł zawiera Rozwiązywanie problemów z czynności, aby zidentyfikować i rozpoznać przyczynę problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="prerequisite-step"></a>Krok wymagań wstępnych

Sprawdź typ bramy sieci wirtualnej platformy Azure:

1. Przejdź do [portalu Azure](https://portal.azure.com).
2. Sprawdź **omówienie** strony bramy sieci wirtualnej, aby uzyskać informacje o typie.
    
    ![Omówienie bramy](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Sprawdź, czy lokalnego urządzenia sieci VPN zostanie zweryfikowany.

1. Sprawdź, czy używasz [zweryfikowane urządzenia sieci VPN i wersję systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Urządzenia sieci VPN nie jest weryfikowany, należy skontaktować się z producentem urządzenia, aby sprawdzić, czy jakikolwiek problem ze zgodnością.
2. Upewnij się, że urządzenia sieci VPN został poprawnie skonfigurowany. Aby uzyskać więcej informacji, zobacz [przykłady konfiguracji urządzenia edytowania](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Sprawdź ustawienia skojarzenia zabezpieczeń (dla bramy oparte na zasadach sieci wirtualnej platformy Azure)

1. Upewnij się, że sieci wirtualnej, podsieci i zakresy w **bramy sieci lokalnej** definicji na platformie Microsoft Azure są takie same jak konfiguracji lokalnego urządzenia sieci VPN.
2. Upewnij się, że ustawienia skojarzenia zabezpieczeń są zgodne.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 wyboru dla tras zdefiniowanych przez użytkownika lub grupy zabezpieczeń sieci w podsieci bramy

Trasy zdefiniowane przez użytkownika w podsieci bramy może ograniczanie część ruchu i stosowanie pozostałego ruchu. Dzięki temu są wyświetlane, czy połączenie sieci VPN jest zawodne dla niektórych ruchu i dobrej dla innych użytkowników. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 sprawdzenie "Jeden tunel VPN na parę podsieć" (dla bram sieci wirtualnej na podstawie zasad)

Upewnij się, że ustawiono lokalnego urządzenia sieci VPN mają **jeden tunel VPN na pary podsieci** dla bram sieci wirtualnej na podstawie zasad.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Sprawdź, czy krok 5 ograniczenia skojarzenia zabezpieczeń (dla bram sieci wirtualnej na podstawie zasad)

Brama sieci wirtualnej na podstawie zasad ma limit 200 pary skojarzenia zabezpieczeń podsieci. Jeśli mnożona liczba podsieci sieci wirtualnej platformy Azure czas liczbą podsieci lokalne jest większe niż 200, zobacz sporadyczne podsieci odłączania.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 Sprawdź lokalne adres interfejsu zewnętrznego urządzenia sieci VPN

- Jeśli internetowy adres IP urządzenia sieci VPN wchodzi w **bramy sieci lokalnej** definicji na platformie Azure, mogą występować sporadycznie rozłączeń.
- Interfejs zewnętrzne urządzenie musi być bezpośrednio połączony z Internetem. Powinien istnieć nie translatora adresów sieciowych (NAT) lub urządzenia od Internetu zaporą.
-  Jeśli konfigurujesz zapory klaster ma wirtualnego adresu IP, musi przerwać klastra i ujawnia urządzenia sieci VPN bezpośrednio na interfejs publiczny, który bramy mogą łączyć się z.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 sprawdzanie, czy doskonałego utajnienia włączone ma lokalnego urządzenia sieci VPN

**Doskonałego utajnienia** funkcji może spowodować problemy rozłączenia. Jeśli urządzenie sieci VPN ma **doskonała utajnienie** włączyć, wyłączyć tę funkcję. Następnie [aktualizacji zasad protokołu IPsec bramy sieci wirtualnej](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Kolejne kroki

- [Skonfiguruj połączenie lokacja-lokacja sieci wirtualnej](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Skonfiguruj zasady IPsec i IKE dla połączenia sieci VPN typu lokacja-lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)

