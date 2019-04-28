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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411853"
---
Sieć wirtualna-sieć wirtualna — często zadawane pytania dotyczą połączeń bramy sieci VPN. Aby dowiedzieć się, jak komunikacja równorzędna sieci wirtualnych, zobacz [komunikacja równorzędna sieci wirtualnych](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Czy w ramach platformy Azure są naliczane opłaty za ruch danych między sieciami wirtualnymi?

Ruch sieci wirtualnej między sieciami wirtualnymi w tym samym regionie jest bezpłatny w obu kierunkach, korzystając z połączenia bramy sieci VPN. Ruch wychodzący w sieci wirtualnej między sieciami VNet między regionami są naliczane opłaty za pomocą szybkości transferu danych wychodzących między sieciami wirtualnymi, na podstawie regionów źródłowych. Aby uzyskać więcej informacji, zobacz [bramy sieci VPN stronę z cennikiem](https://azure.microsoft.com/pricing/details/vpn-gateway/). Jeśli łączysz sieci wirtualne przy użyciu równorzędnej zamiast bramy sieci VPN, zobacz [cennik usługi Virtual network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Ruch sieć wirtualna-sieć wirtualna jest przenoszona w Internecie?

Nie. Sieć wirtualna-sieć wirtualna ruchu przechodzącego przez szkieletowej platformy Microsoft Azure, nie przez internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Czy można ustanowić połączenia sieć wirtualna-sieć wirtualna, dla dzierżaw usługi Azure Active Directory (AAD)?

Tak, połączenia sieć wirtualna-sieć wirtualna, które używają bramy sieci VPN platformy Azure działa w dzierżawach usługi AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Czy ruch sieciowy w ramach połączenia między sieciami wirtualnymi jest bezpieczny?

Tak, jest chroniony przez szyfrowanie protokołu IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Czy do połączenia sieci wirtualnych jest potrzebne urządzenie sieci VPN?

Nie. Łączenie wielu sieci wirtualnych platformy Azure nie wymaga urządzenia sieci VPN, chyba że jest wymagana łączność między wieloma lokalizacjami.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Czy sieci wirtualne muszą znajdować się w tym samym regionie?

Nie. Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Jeśli te sieci wirtualne nie są w tej samej subskrypcji, czy subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Active Directory?

Nie.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Czy połączenia między sieciami wirtualnymi można używać do łączenia sieci wirtualnych między osobnymi wystąpieniami platformy Azure? 

Nie. Połączenie między sieciami wirtualnymi obsługuje łączenie sieci wirtualnych w tym samym wystąpieniu platformy Azure. Na przykład nie można utworzyć połączenie między usługą globalnej platformy Azure i US-chiński/niemiecki dla instytucji rządowych Azure wystąpień. Należy rozważyć użycie połączenia sieci VPN typu lokacja-lokacja dla tych scenariuszy.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Czy połączenia między sieciami wirtualnymi można używać wraz z połączeniami obejmującymi wiele lokacji?

Tak. Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Z iloma lokacjami lokalnymi i sieciami wirtualnymi może połączyć się jedna sieć wirtualna?

Zobacz [wymagania dotyczące bramy](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tabeli.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Czy za pomocą połączenia między sieciami wirtualnymi można łączyć się z maszynami wirtualnymi lub usługami w chmurze znajdującymi się poza siecią wirtualną?

Nie. Połączenie między sieciami wirtualnymi obsługuje sieci wirtualne. Go nie obsługuje połączeń maszyn wirtualnych i usług, które nie znajdują się w sieci wirtualnej w chmurze.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Można to usługa w chmurze lub punkt końcowy z równoważeniem obciążenia obejmować sieci wirtualne?

Nie. Usługa w chmurze ani punkt końcowy z równoważeniem obciążenia nie mogą rozciągać się na sieci wirtualne, nawet wtedy, gdy są one połączone ze sobą.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Czy można użyć typu sieci PolicyBased VPN, dla połączeń sieci wirtualnej między sieciami wirtualnymi i połączenia obejmujące wiele lokacji?

Nie. Połączenia z sieciami wirtualnymi i połączenia obejmujące wiele lokacji wymagają bram Azure VPN Gateway przy użyciu typu RouteBased (nazywanymi wcześniej o routingu dynamicznym) typy sieci VPN.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Czy można połączyć sieć wirtualną VPN opartą na trasach z inną siecią wirtualną VPN opartą na zasadach?

Nie, obie sieci wirtualne, należy użyć VPN oparta na trasach (nazywanych wcześniej o routingu dynamicznym).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Czy tunele VPN współdzielą przepustowość?

Tak. Wszystkie tunele VPN sieci wirtualnej współdzielą dostępną przepustowość bramy VPN Azure i są wspólnie objęte umową SLA dotyczącą danej bramy na platformie Azure.

### <a name="are-redundant-tunnels-supported"></a>Czy nadmiarowe tunele są obsługiwane?

Nadmiarowe tunele między dwiema sieciami wirtualnymi są obsługiwane, jeśli jedna brama sieci wirtualnej jest w konfiguracji aktywne-aktywne.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Czy w konfiguracjach połączeń między sieciami wirtualnymi mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Czy wśród połączonych sieci wirtualnych i lokacji lokalnych mogą występować nakładające się przestrzenie adresowe?

Nie. Nakładające się przestrzenie adresowe nie są dopuszczalne.



