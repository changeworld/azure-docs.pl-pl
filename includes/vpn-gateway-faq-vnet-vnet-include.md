---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183052"
---
Często zadawane pytania dotyczące sieci wirtualnej do sieci wirtualnej dotyczą połączeń bramy sieci VPN. Aby uzyskać informacje na temat komunikacji równorzędnej sieci wirtualnej, zobacz [Komunikacja równorzędna sieci wirtualnej](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Czy w ramach platformy Azure są naliczane opłaty za ruch danych między sieciami wirtualnymi?

Ruch sieci wirtualnej do sieci wirtualnej w tym samym regionie jest bezpłatny dla obu kierunków podczas korzystania z połączenia bramy sieci VPN. Międzyregionowy ruch wychodzący sieci wirtualnej do sieci wirtualnej jest obciążony wychodzącymi szybkościami transferu danych między siecią wirtualną na podstawie regionów źródłowych. Aby uzyskać więcej informacji, zobacz [stronę cennika bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Jeśli łączysz sieci wirtualne przy użyciu komunikacji równorzędnej sieci wirtualnej zamiast bramy sieci VPN, zobacz [Cennik sieci wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Czy ruch sieci wirtualnej do sieci wirtualnej przemieszcza się przez Internet?

Nie. Ruch sieci wirtualnej do sieci wirtualnej przemieszcza się przez szkielet platformy Microsoft Azure, a nie przez Internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Czy można ustanowić połączenie sieci wirtualnej z siecią wirtualną w dzierżawach usługi Azure Active Directory (AAD)?

Tak, połączenia sieci wirtualnej z siecią wirtualną korzystające z bram sieci VPN platformy Azure działają między dzierżawami usługi AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Czy ruch sieciowy w ramach połączenia między sieciami wirtualnymi jest bezpieczny?

Tak, jest chroniony przez szyfrowanie IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Czy do połączenia sieci wirtualnych jest potrzebne urządzenie sieci VPN?

Nie. Łączenie wielu sieci wirtualnych platformy Azure nie wymaga urządzenia sieci VPN, chyba że jest wymagana łączność między wieloma lokalizacjami.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Czy sieci wirtualne muszą znajdować się w tym samym regionie?

Nie. Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Jeśli sieci wirtualne nie są w tej samej subskrypcji, czy subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Active Directory?

Nie.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Czy połączenia między sieciami wirtualnymi można używać do łączenia sieci wirtualnych między osobnymi wystąpieniami platformy Azure? 

Nie. Połączenie między sieciami wirtualnymi obsługuje łączenie sieci wirtualnych w tym samym wystąpieniu platformy Azure. Na przykład nie można utworzyć połączenia między globalną platformą Azure a wystąpieniami platformy Azure dla instytucji rządowych w języku chińskim/niemieckim/amerykańskim. Należy rozważyć użycie połączenia sieci VPN lokacja lokacja dla tych scenariuszy.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Czy połączenia między sieciami wirtualnymi można używać wraz z połączeniami obejmującymi wiele lokacji?

Tak. Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Z iloma lokacjami lokalnymi i sieciami wirtualnymi może połączyć się jedna sieć wirtualna?

Zobacz tabelę [Wymagania bramy.](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Czy za pomocą połączenia między sieciami wirtualnymi można łączyć się z maszynami wirtualnymi lub usługami w chmurze znajdującymi się poza siecią wirtualną?

Nie. Połączenie między sieciami wirtualnymi obsługuje sieci wirtualne. Nie obsługuje łączenia maszyn wirtualnych lub usług w chmurze, które nie znajdują się w sieci wirtualnej.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Czy usługa w chmurze lub równoważenie obciążenia punktu końcowego obejmują sieci wirtualne?

Nie. Usługa w chmurze lub punkt końcowy równoważenia obciążenia nie może obejmować sieci wirtualnych, nawet jeśli są połączone ze sobą.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Czy mogę używać typu sieci VPN opartej na zasadach dla połączeń sieci wirtualnej z siecią wirtualną lub wielu lokacji?

Nie. Połączenia sieci wirtualnej z siecią wirtualną i wieloustawowe wymagają bram sieci VPN platformy Azure z typami sieci VPN routebased (wcześniej nazywanym routingiem dynamicznym).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Czy można połączyć sieć wirtualną VPN opartą na trasach z inną siecią wirtualną VPN opartą na zasadach?

Nie, obie sieci wirtualne MUSZĄ używać sieci VPN opartych na trasach (wcześniej nazywanych routingiem dynamicznym).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Czy tunele VPN współdzielą przepustowość?

Tak. Wszystkie tunele VPN sieci wirtualnej współdzielą dostępną przepustowość bramy VPN Azure i są wspólnie objęte umową SLA dotyczącą danej bramy na platformie Azure.

### <a name="are-redundant-tunnels-supported"></a>Czy nadmiarowe tunele są obsługiwane?

Nadmiarowe tunele między dwiema sieciami wirtualnymi są obsługiwane, jeśli jedna brama sieci wirtualnej jest w konfiguracji aktywne-aktywne.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Czy w konfiguracjach połączeń między sieciami wirtualnymi mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Czy wśród połączonych sieci wirtualnych i lokacji lokalnych mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.



