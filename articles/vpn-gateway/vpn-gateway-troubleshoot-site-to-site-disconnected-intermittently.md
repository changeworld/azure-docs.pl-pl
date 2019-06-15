---
title: Rozwiązywanie problemów z usługi Azure VPN lokacja-lokacja jest sporadycznie rozłączane | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązać problem, w którym połączenie sieci VPN typu lokacja-lokacja rozłączona regularnie.
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
ms.openlocfilehash: 2fdd82c2f0c96b3bd20231911bb88cf54c172931
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457770"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Rozwiązywanie problemów: Usługa Azure VPN lokacja-lokacja jest sporadycznie rozłączane

Może wystąpić problem, że nowe lub istniejące połączenie VPN lokacja-lokacja Microsoft Azure nie jest stabilna lub odłącza się regularnie. Ten artykuł zawiera Rozwiązywanie problemów z kroki, aby ułatwić identyfikację i rozwiązywanie przyczyny problemu. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

### <a name="prerequisite-step"></a>Kroku wymagań wstępnych

Sprawdź typ bramy sieci wirtualnej platformy Azure:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Sprawdź **Przegląd** stronę bramy sieci wirtualnej, aby uzyskać informacje o typie.
    
    ![Omówienie bramy](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Sprawdź, czy lokalne urządzenie sieci VPN jest weryfikowana.

1. Sprawdź, czy używasz [zweryfikowane urządzenia sieci VPN i wersję systemu operacyjnego](vpn-gateway-about-vpn-devices.md#devicetable). Jeśli urządzenie sieci VPN nie zostanie zweryfikowana, może być konieczne skontaktuj się z producentem urządzenia, aby sprawdzić, czy jest dowolnym problem ze zgodnością.
2. Upewnij się, że urządzenie sieci VPN została poprawnie skonfigurowana. Aby uzyskać więcej informacji, zobacz [edytowanie przykładów konfiguracji urządzenia](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2, sprawdź ustawienia oferty skojarzeń zabezpieczeń (w przypadku bramy oparte na zasadach sieci wirtualnej platformy Azure)

1. Upewnij się, że sieci wirtualnej, podsieci i zakresy w **bramy sieci lokalnej** definicji w systemie Microsoft Azure są takie same jak konfiguracji lokalnego urządzenia sieci VPN.
2. Upewnij się, że ustawienia skojarzenia zabezpieczeń są takie same.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 Sprawdź trasy zdefiniowane przez użytkownika lub grupy zabezpieczeń sieci w ramach bramy podsieci

Trasa zdefiniowana przez użytkownika, w ramach bramy podsieci może być ograniczenie część ruchu i umożliwiając pozostałe rodzaje ruchu. Dzięki temu pojawiają się, że połączenie sieci VPN jest zawodne dla niektórych ruchu i dobre dla innych użytkowników. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 Sprawdź "Jeden tunel VPN za parę podsieci" (dla bramy sieci wirtualnej na podstawie zasad)

Upewnij się, że lokalne urządzenie sieci VPN jest ustawiona na mają **jeden tunel VPN za parę podsieci** dla bram sieci wirtualnej na podstawie zasad.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 Sprawdzanie ograniczenia skojarzenia zabezpieczeń (w przypadku bram sieci wirtualnej na podstawie zasad)

Brama sieci wirtualnej na podstawie zasad ma limit równy 200 pary oferty skojarzeń zabezpieczeń w podsieci. Jeśli liczba podsieci sieci wirtualnej platformy Azure oraz razy liczba lokalne podsieci jest większy niż 200, zobacz sporadyczne podsieci odłączanie.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 wyboru w środowisku lokalnym adres interfejsu zewnętrznego urządzenia sieci VPN

- Jeśli adres IP urządzenia sieci VPN z Internetem znajduje się w **bramy sieci lokalnej** definicji na platformie Azure, możesz napotkać sporadyczne odłączenia.
- Interfejs zewnętrzny urządzenia musi być bezpośrednio w Internecie. Powinna istnieć nie translacji adresów sieciowych (NAT) lub zapory między Internetem a urządzenia.
-  Jeśli skonfigurujesz zapory klastra mają wirtualnego adresu IP, należy przerwać klastra i narazić urządzenie sieci VPN bezpośrednio na interfejs publiczny, który bramy może współpracować z usługą.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 sprawdzanie, czy lokalne urządzenie sieci VPN ma Perfect Forward Secrecy włączone

**Perfect Forward Secrecy** funkcji może spowodować problemy rozłączenia. Jeśli na urządzeniu sieci VPN jest **doskonała utajnienie** włączyć, wyłączyć funkcję. Następnie [aktualizacji zasad protokołu IPsec bramy sieci wirtualnej](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie połączenia lokacja-lokacja z siecią wirtualną](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurowanie zasad protokołu IPsec/IKE dla połączeń sieci VPN typu lokacja-lokacja](vpn-gateway-ipsecikepolicy-rm-powershell.md)

