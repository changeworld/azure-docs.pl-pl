---
title: 'Brama sieci VPN platformy Azure: wymagania kryptograficzne'
description: W tym artykule omówiono wymagania kryptograficzne i bramy sieci VPN platformy Azure
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902823"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Wymagania kryptograficzne i bramy sieci VPN platformy Azure — informacje

W tym artykule omówiono sposób konfigurowania bram sieci VPN platformy Azure w celu spełnienia wymagań kryptograficznych zarówno dla tuneli sieci VPN s2s międzylokacyjnych, jak i połączeń sieci wirtualnej do sieci wirtualnej na platformie Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Informacje o połączeniach sieci VPN IKEv1 i IKEv2 dla platformy Azure

Tradycyjnie zezwalaliśmy na połączenia IKEv1 tylko dla podstawowych jednostek SKU i zezwalaliśmy na połączenia IKEv2 dla wszystkich jednostek SKU bramy sieci VPN innych niż podstawowe jednostki SKU. Podstawowe jednostki SKU zezwalają tylko na 1 połączenie i wraz z innymi ograniczeniami, takimi jak wydajność, klienci korzystający ze starszych urządzeń obsługujących tylko protokoły IKEv1 mieli ograniczone środowisko. Aby zwiększyć komfort korzystania z usług klientów korzystających z protokołów IKEv1, zezwalamy teraz na połączenia IKEv1 dla wszystkich jednostek SKU bramy sieci VPN, z wyjątkiem podstawowych jednostek SKU. Aby uzyskać więcej informacji, zobacz [Jednostki SKU bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku).

![Połączenia Usługi Azure VPN Gateway IKEv1 i IKEv2](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Gdy połączenia IKEv1 i IKEv2 są stosowane do tej samej bramy sieci VPN, tranzyt między tymi dwoma połączeniami jest włączony automatycznie.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Parametry zasad IPsec i IKE dla bram sieci VPN platformy Azure — informacje

Standard protokołu IPsec i IKE obsługuje szeroką gamę algorytmów kryptograficznych w różnych kombinacjach. Jeśli nie zażądasz określonej kombinacji algorytmów i parametrów kryptograficznych, bramy sieci VPN platformy Azure użyją zestawu propozycji domyślnych. Domyślne zestawy zasad zostały wybrane w celu maksymalizacji współdziałania z szeroką gamą urządzeń sieci VPN innych firm w domyślnych konfiguracjach. W rezultacie zasady i liczba propozycji nie mogą obejmować wszystkich możliwych kombinacji dostępnych algorytmów kryptograficznych i kluczowych mocnych stron.

Domyślny zestaw zasad dla bramy sieci VPN platformy Azure znajduje się w artykule: [Informacje o urządzeniach sieci VPN i parametrach IPsec/IKE dla połączeń bramy sieci VPN między lokacjami](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Wymagania kryptograficzne

W przypadku komunikacji, która wymaga określonych algorytmów kryptograficznych lub parametrów, zazwyczaj ze względu na wymagania dotyczące zgodności lub zabezpieczeń, można teraz skonfigurować ich bramy sieci VPN platformy Azure do używania niestandardowych zasad IPsec/IKE z określonymi algorytmami kryptograficznymi i mocnymi sługami kluczy, a nie domyślnymi zestawami zasad platformy Azure.

Na przykład zasady trybu głównego IKEv2 dla bram sieci VPN platformy Azure wykorzystują tylko Diffie-Hellman Group 2 (1024 bity), podczas gdy może być konieczne określenie silniejszych grup, które mają być używane w IKE, takich jak Grupa 14 (2048-bit), Grupa 24 (2048-bitowa grupa MODP) lub ECP (krzywa eliptyczna grupy) 256 lub 384 bitów (odpowiednio grupa 19 i grupa 20). Podobne wymagania dotyczą również zasad trybu szybkiego protokołu IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Niestandardowe zasady IPsec/IKE z bramami sieci VPN platformy Azure

Bramy sieci VPN platformy Azure obsługują teraz niestandardowe zasady IPsec/IKE dla połączenia. W przypadku połączenia lokacja-lokacja lub sieci wirtualnej z siecią wirtualną można wybrać określoną kombinację algorytmów kryptograficznych dla protokołu IPsec i IKE o żądanej sile klucza, jak pokazano w poniższym przykładzie:

![ipsec-ike-policy ipsec-ike-policy ipsec-ike-policy ipsec](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

Można utworzyć zasady IPsec/IKE i zastosować do nowego lub istniejącego połączenia.

### <a name="workflow"></a>Przepływ pracy

1. Tworzenie sieci wirtualnych, bram sieci VPN lub bram sieci lokalnej dla topologii łączności zgodnie z opisem w innych dokumentach insektowych
2. Tworzenie zasad protokołu IPsec/IKE
3. Zasady można zastosować podczas tworzenia połączenia S2S lub sieci wirtualnej z siecią wirtualną
4. Jeśli połączenie jest już utworzone, można zastosować lub zaktualizować zasady do istniejącego połączenia

## <a name="ipsecike-policy-faq"></a>Zasady IPsec/IKE często zadawane pytania

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania niestandardowych zasad protokołu IPsec/IKE w połączeniu, zobacz [Konfigurowanie zasad protokołu IPsec/IKE.](vpn-gateway-ipsecikepolicy-rm-powershell.md)

Zobacz też [Łączenie wielu urządzeń sieci VPN opartych na zasadach,](vpn-gateway-connect-multiple-policybased-rm-ps.md) aby dowiedzieć się więcej o opcji UsePolicyBasedTrafficSelectors.
