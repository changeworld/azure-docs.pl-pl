---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66ff1e2e02728e05cb0aeedce90de1882a8804ce
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921312"
---
Często zadawane pytania dotyczące połączenia między sieciami wirtualnymi mają zastosowanie w przypadku połączeń usługi VPN Gateway. Jeśli potrzebujesz wirtualnych sieci równorzędnych, zobacz [Wirtualne sieci równorzędne](../articles/virtual-network/virtual-network-peering-overview.md)

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Czy w ramach platformy Azure są naliczane opłaty za ruch danych między sieciami wirtualnymi?

Ruch sieciowy w ramach połączenia między sieciami wirtualnymi w tym samym regionie jest bezpłatny w obu kierunkach, jeśli połączenie jest obsługiwane przez usługę VPN Gateway. Opłaty są pobierane za ruch wychodzący w ramach połączenia sieci wirtualnych na przestrzeni różnych regionów, przy czym cenę za wychodzący transfer danych wewnątrz sieci wirtualnej określa się na podstawie regionów źródłowych. Szczegółowe informacje można znaleźć na [stronie poświęconej cenom usługi VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Jeśli połączenia sieci wirtualnych są obsługiwane za pomocą komunikacji równorzędnej sieci wirtualnych, a nie usługi VPN Gateway, zobacz [stronę poświęconą cenom usługi Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Czy ruch sieciowy w ramach połączenia między sieciami wirtualnymi odbywa się poza Internetem?

Nie. Ruch sieciowy w ramach połączenia między sieciami wirtualnymi przechodzi przez sieć szkieletową platformy Microsoft Azure, a nie przez Internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-aad-tenants"></a>Czy może nawiązać połączenie sieci wirtualnej do sieci wirtualnej, w dzierżawcy usługi AAD?

Tak, aby wirtualnymi połączeń przy użyciu bramy sieci VPN platformy Azure działa między dzierżawami usługi AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Czy ruch sieciowy w ramach połączenia między sieciami wirtualnymi jest bezpieczny?

Tak, jest zabezpieczony z użyciem szyfrowania IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Czy do połączenia sieci wirtualnych jest potrzebne urządzenie sieci VPN?

Nie. Łączenie wielu sieci wirtualnych platformy Azure nie wymaga urządzenia sieci VPN, chyba że jest wymagana łączność między wieloma lokalizacjami.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Czy sieci wirtualne muszą znajdować się w tym samym regionie?

Nie. Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>Jeśli sieci wirtualne nie należą do tej samej subskrypcji, czy subskrypcje muszą być skojarzone z tą samą dzierżawą usługi AD?

Nie.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Czy połączenia między sieciami wirtualnymi można używać do łączenia sieci wirtualnych między osobnymi wystąpieniami platformy Azure? 

Nie. Połączenie między sieciami wirtualnymi obsługuje łączenie sieci wirtualnych w tym samym wystąpieniu platformy Azure. Nie można na przykład utworzyć połączenia między publiczną platformą Azure i wystąpieniami platformy Azure dla Chin/Niemiec/instytucji rządowych USA (US Gov). W tych przypadkach należy rozważyć użycie połączenia sieci VPN typu lokacja-lokacja.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Czy połączenia między sieciami wirtualnymi można używać wraz z połączeniami obejmującymi wiele lokacji?

Tak. Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Z iloma lokacjami lokalnymi i sieciami wirtualnymi może połączyć się jedna sieć wirtualna?

Zobacz tabelę [Wymagania dotyczące bramy](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Czy za pomocą połączenia między sieciami wirtualnymi można łączyć się z maszynami wirtualnymi lub usługami w chmurze znajdującymi się poza siecią wirtualną?

Nie. Połączenie między sieciami wirtualnymi obsługuje sieci wirtualne. Nie obsługuje połączeń maszyn wirtualnych ani usług w chmurze, które nie należą do sieci wirtualnej.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Czy usługa w chmurze lub punkt końcowy z równoważeniem obciążenia może obejmować sieci wirtualne?

Nie. Usługa w chmurze ani punkt końcowy z równoważeniem obciążenia nie mogą rozciągać się na sieci wirtualne, nawet jeśli są one ze sobą połączone.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Czy można używać typu sieci VPN PolicyBased na potrzeby połączeń między sieciami wirtualnymi lub połączeń obejmujących wiele lokacji?

Nie. Połączenia między sieciami wirtualnymi i połączenia obejmujące wiele lokacji wymagają bram sieci VPN platformy Azure z sieciami typu VPN RouteBased (nazywanymi wcześniej sieciami obsługującymi routing dynamiczny).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Czy można połączyć sieć wirtualną VPN opartą na trasach z inną siecią wirtualną VPN opartą na zasadach?

Nie, obie sieci wirtualne MUSZĄ korzystać z sieci VPN opartych na trasach (nazywanych wcześniej sieciami obsługującymi routing dynamiczny).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Czy tunele VPN współdzielą przepustowość?

Tak. Wszystkie tunele VPN sieci wirtualnej współdzielą dostępną przepustowość bramy VPN Azure i są wspólnie objęte umową SLA dotyczącą danej bramy na platformie Azure.

### <a name="are-redundant-tunnels-supported"></a>Czy nadmiarowe tunele są obsługiwane?

Nadmiarowe tunele między dwiema sieciami wirtualnymi są obsługiwane, jeśli jedna brama sieci wirtualnej jest w konfiguracji aktywne-aktywne.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Czy w konfiguracjach połączeń między sieciami wirtualnymi mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Czy wśród połączonych sieci wirtualnych i lokacji lokalnych mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.



