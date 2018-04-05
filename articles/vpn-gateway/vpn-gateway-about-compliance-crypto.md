---
title: Informacje dotyczące wymagań kryptograficznych i bram sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono wymagania dotyczące szyfrowania i bram sieci VPN platformy Azure
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2017
ms.author: yushwang
ms.openlocfilehash: d2f3da47f1d4eebe1b81964790ff6612dd78155d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Informacje dotyczące wymagań kryptograficznych i bram sieci VPN platformy Azure

W tym artykule opisano, jak można skonfigurować bramy sieci VPN platformy Azure do zaspokojenia wymagań kryptograficznych dla tuneli S2S VPN między lokalizacjami i połączeń między wirtualnymi na platformie Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Parametry zasad IPsec i IKE dla bram sieci VPN platformy Azure — informacje
Standardowego protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Jeśli klienci nie Żądaj określona kombinacja algorytmów kryptograficznych i parametrów, bram sieci VPN platformy Azure, użyj zestaw propozycji domyślne. Ustawia zasady domyślne zostały wybrane do zmaksymalizowania współdziałanie z szeroką gamę urządzenia sieci VPN innych firm w domyślnej konfiguracji. W związku z tym zasady i liczba propozycje nie obejmować wszystkich możliwych kombinacji dostępnych algorytmów kryptograficznych i kluczy sile.

Domyślne zasady ustawione dla bramy sieci VPN platformy Azure znajduje się w dokumencie: [urządzenia sieci VPN o i IPsec i IKE parametry połączenia bramy sieci VPN typu lokacja-lokacja](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Wymagania dotyczące usług kryptograficznych
Komunikacji, który wymaga parametrów lub algorytmy kryptograficzne zazwyczaj z powodu zgodności lub wymagania dotyczące zabezpieczeń klientów można teraz skonfigurować ich bramy sieci VPN platformy Azure do niestandardowych zasad IPsec i IKE za pomocą określonych algorytmów kryptograficznych i kluczy sile, a nie ustawia Azure domyślne zasady.

Na przykład zasady trybu głównego protokołu IKEv2 dla bram sieci VPN platformy Azure korzystać tylko Diffie-Hellman grupa 2 (1024 bity), natomiast klientów może być konieczne podanie silniejsze grupy do użycia w IKE, takich jak grupy 14 (2048-bitowego), 24 grupy (grupa MODP 2048-bitowego) lub ECP (krzywej eliptycznej grupy) bit 256 lub 384 (grupa 19 i 20 grupy odpowiednio). Podobne wymagania dotyczą również zasady IPsec trybu szybkiego.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Niestandardowe zasady IPsec i IKE z bram sieci VPN platformy Azure
Bramy sieci VPN platformy Azure obsługuje teraz poszczególnych połączeń, zasad IPsec i IKE niestandardowych. Lokacja-lokacja lub połączenia do wirtualnymi można wybrać określoną kombinację algorytmów kryptograficznych dla protokołu IPsec i IKE żądaną siły kluczy, jak pokazano w poniższym przykładzie:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Możesz utworzyć zasady IPsec i IKE i zastosować do nowego lub istniejącego połączenia. 

### <a name="workflow"></a>Przepływ pracy

1. Tworzenie sieci wirtualnych, bram sieci VPN lub bramy sieci lokalnej dla topologii łączności zgodnie z opisem w innych dokumenty z poradami
2. Tworzenie zasad IPsec i IKE
3. Zasady można zastosować podczas tworzenia połączenia S2S lub do wirtualnymi
4. Jeśli połączenie jest już utworzony, można zastosować lub zaktualizuj zasady do istniejącego połączenia


## <a name="ipsecike-policy-faq"></a>Zasady protokołu IPsec/IKE — często zadawane pytania

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Kolejne kroki
Zobacz [zasady IPsec/IKE skonfigurować](vpn-gateway-ipsecikepolicy-rm-powershell.md) instrukcje krok po kroku dotyczące konfigurowania niestandardowych zasad IPsec i IKE połączenia.

Zobacz też [podłączenie wielu urządzeń sieci VPN opartych na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md) Aby dowiedzieć się więcej na temat opcji UsePolicyBasedTrafficSelectors.