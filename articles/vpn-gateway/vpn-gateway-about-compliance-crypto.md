---
title: O wymaganiach kryptograficznych oraz bram Azure VPN Gateway | Dokumentacja firmy Microsoft
description: W tym artykule omówiono wymagania kryptograficzne i bram Azure VPN Gateway
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 05/22/2017
ms.date: 10/01/2018
ms.author: v-jay
ms.openlocfilehash: 060e647badcc3bad7b44d7cef3530c36b8ecdf57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648693"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>O wymaganiach kryptograficznych oraz bram Azure VPN Gateway

W tym artykule omówiono sposób konfigurowania bramy sieci VPN platformy Azure do spełnienia wymagań kryptograficznych dla tuneli sieci VPN S2S między środowiskami lokalnymi i połączeń między sieciami na platformie Azure. 

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Parametry zasad IPsec i IKE bram Azure VPN Gateway — informacje
Standardowego protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Jeśli klienci nie poprosić o konkretnej kombinacji algorytmów kryptograficznych i parametrów, bram sieci VPN platformy Azure Użyj zestaw propozycji domyślne. Domyślne zestawy zasad dobrano w celu zmaksymalizowania współdziałanie z szerokiej gamy urządzeń sieci VPN innych firm w konfiguracji domyślnej. W rezultacie zasady i liczba propozycji nie obejmują wszystkich możliwych kombinacji dostępne algorytmy kryptograficzne i siły klucza.

Domyślne zasady dla bramy sieci VPN platformy Azure znajduje się w dokumencie: [O urządzeniach sieci VPN i parametrów protokołu IPsec/IKE dla połączeń bramy VPN typu lokacja-lokacja](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Wymagania kryptograficzne
Komunikacji, który wymaga parametrów lub algorytmy kryptograficzne zwykle ze względu na wymagania zabezpieczeń lub zgodności klientów można teraz skonfigurować ich bram Azure VPN Gateway niestandardowe zasady protokołu IPsec/IKE za pomocą określonych usług kryptograficznych algorytmów i sił klucza, a nie z domyślnych zestawów zasad.

Na przykład zasady trybu głównego protokołu IKEv2 dla bram Azure VPN Gateway wykorzystywać tylko 2 (1024 bity), grupy Diffie'ego-Hellmana, natomiast klientów może być konieczne określenie silniejsze grup do użycia w IKE, takich jak grupy 14 (2048-bitowy), 24 grupy (grupa MODP 2048-bitowy) lub ECP (elliptic Krzywa grup) bit 256 lub 384 (grupy 19 i 20 grupy odpowiednio). Podobne wymagania dotyczą również zasad trybu szybkiego protokołu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Niestandardowe zasady protokołu IPsec/IKE z bramami sieci VPN platformy Azure
Bramy sieci VPN platformy Azure są teraz obsługiwane dla każdego połączenia, niestandardowe zasady protokołu IPsec/IKE. Lokacja-lokacja lub połączenia sieć wirtualna-sieć wirtualna możesz wybrać konkretnej kombinacji algorytmów kryptograficznych dla protokołu IPsec i IKE za pomocą żądanego siłę klucza, jak pokazano w poniższym przykładzie:

![ipsec-ike-policy](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Można utworzyć zasady protokołu IPsec/IKE i zastosować do nowego lub istniejącego połączenia. 

### <a name="workflow"></a>Przepływ pracy

1. Tworzenie sieci wirtualnych, bram sieci VPN lub bramy sieci lokalnej na potrzeby topologii połączenia, zgodnie z opisem w innych dokumentach porad
2. Tworzenie zasad protokołu IPsec/IKE
3. Zasady można zastosować podczas tworzenia połączenia S2S lub sieć wirtualna-sieć wirtualna
4. Jeśli połączenie jest już utworzony, można zastosować lub zaktualizować zasady do istniejącego połączenia


## <a name="ipsecike-policy-faq"></a>Zasady protokołu IPsec/IKE — często zadawane pytania

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="next-steps"></a>Kolejne kroki
Zobacz [zasad IPsec/IKE skonfigurować](vpn-gateway-ipsecikepolicy-rm-powershell.md) instrukcje krok po kroku dotyczące konfigurowania niestandardowe zasady protokołu IPsec/IKE dla połączenia.

Zobacz też [podłączenie wielu urządzeń sieci VPN oparte na zasadach](vpn-gateway-connect-multiple-policybased-rm-ps.md) Aby dowiedzieć się więcej na temat opcji UsePolicyBasedTrafficSelectors.