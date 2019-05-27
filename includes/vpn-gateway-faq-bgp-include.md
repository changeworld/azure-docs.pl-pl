---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 192a6f4841e9dc3a478da5e4b53594362955ca71
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119513"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Czy protokół BGP jest obsługiwany na wszystkich jednostkach SKU bramy sieci VPN platformy Azure?
Nie. Protokół BGP jest obsługiwany na bramach sieci VPN **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** i **HighPerformance** na platformie Azure. **Podstawowa** jednostka SKU nie jest obsługiwana.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Czy można użyć protokołu BGP z bramą sieci VPN platformy Azure opartej na zasadach?
Nie, protokół BGP jest obsługiwany tylko przez bramy sieci VPN opartych na trasach.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Czy można użyć prywatnych numerów ASN (numery systemu autonomicznego)?
Tak, można użyć własnych publicznych lub prywatnych numerów ASN dla sieci lokalnych i sieci wirtualnych platformy Azure.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>Czy można użyć 32-bitowych numerów ASN (numerów systemu autonomicznego)?
Nie, obecnie usługi Azure VPN Gateway obsługują 16-bitowe numery ASN.

### <a name="are-there-asns-reserved-by-azure"></a>Czy istnieją numery ASN zarezerwowane przez platformę Azure?
Tak, następujące numery ASN są zarezerwowane przez platformę Azure zarówno dla wewnętrznej, jak i zewnętrznej komunikacji równorzędnej:

* Public ASNs: 8074, 8075, 12076
* Prywatne numery ASN: 65515, 65517, 65518, 65519, 65520

Tych numerów ASN nie można określać dla lokalnych urządzeń sieci VPN podczas nawiązywania połączenia z bramami sieci VPN platformy Azure.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Czy istnieją inne numery ASN, których nie mogę używać?
Tak, następujące numery ASN zostały [zarezerwowane przez organizację IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) i nie można ich skonfigurować w usłudze Azure VPN Gateway:

23456, 64496-64511, 65535–65551 i 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Czy można użyć tego samego numeru ASN zarówno dla lokalnych sieci VPN, jak sieci wirtualnych platformy Azure?
Nie, należy przypisać różne numery ASN sieciom lokalnym i sieciom wirtualnym platformy Azure, jeśli są łączone za pomocą protokołu BGP. Bramy sieci VPN platformy Azure mają przypisany domyślny numer ASN 65515, niezależnie od tego, czy protokół BGP jest włączony dla łączności między różnymi lokalizacjami firmy. Można zastąpić to ustawienie domyślne, przypisując inny numer ASN podczas tworzenia bramy sieci VPN, lub zmienić numer ASN po utworzeniu bramy. Konieczne będzie przypisanie lokalnych numerów ASN do odpowiednich bram sieci lokalnej platformy Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Jakie prefiksy adresów będą anonsowane do użytkownika przez bramy sieci VPN platformy Azure?
Brama sieci VPN będzie anonsować następujące trasy na urządzeniach lokalnych działających według protokołu BGP:

* Prefiksy adresów sieci wirtualnej użytkownika
* Prefiksy adresów dla każdej bramy sieci lokalnej podłączonej do bramy sieci VPN platformy Azure
* Trasy zostały uzyskane na podstawie innych sesji równorzędnych protokołu BGP podłączonych do bramy sieci VPN platformy Azure **z wyjątkiem trasy domyślnej lub tras nakładających się dla dowolnego prefiksu sieci wirtualnej**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Jak wiele prefiksy można anonsować do bramy sieci VPN platformy Azure?
Firma Microsoft obsługuje do 4000 prefiksów. Sesja protokołu BGP zostanie przerwana, jeśli liczba prefiksów przekroczy limit.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Czy można anonsować trasę domyślną (0.0.0.0/0) do bram sieci VPN platformy Azure?
Tak.

Pamiętaj, że spowoduje to wymuszenie całego ruchu wychodzącego do lokacji lokalnej i uniemożliwi maszynom wirtualnym sieci wirtualnej akceptowanie publicznej komunikacji bezpośrednio z Internetu, na przykład za pomocą protokołu RDP lub SSH z Internetu do maszyn wirtualnych.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Czy można anonsować takie same prefiksy jak prefiksy mojej sieci wirtualnej?

Nie, anonsowanie takich samych prefiksów jak prefiksy adresów Twojej sieci wirtualnej zostanie zablokowane lub odfiltrowane przez platformę Azure. Można jednak anonsować prefiks, który jest podzbiorem tego, co znajduje się wewnątrz sieci wirtualnej. 

Na przykład jeśli sieć wirtualna używała przestrzeni adresów 10.0.0.0/16, można anonsować przestrzeń 10.0.0.0/8. Nie można jednak anonsować przestrzeni 10.0.0.0/16 ani 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Czy można użyć protokołu BGP do połączeń między sieciami wirtualnymi użytkownika?
Tak, protokołu BGP można użyć do połączeń zarówno między różnymi lokalizacjami, jak i połączeń między sieciami wirtualnymi.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Czy można mieszać połączenia BGP z połączeniami protokołów innych niż BGP dla bram sieci VPN użytkownika platformy Azure?
Tak, dla tej samej bramy sieci VPN platformy Azure można łączyć zarówno połączenia protokołu BGP, jak i połączenia innych protokołów.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Czy brama sieci VPN platformy Azure obsługuje routing tranzytowy protokołu BGP?
Tak, routing tranzytowy protokołu BGP jest obsługiwany, ale bramy sieci VPN platformy Azure **nie** anonsują tras domyślnych do innych elementów równorzędnych protokołu BGP. Aby włączyć routing tranzytowy dla wielu bram sieci VPN platformy Azure, należy włączyć protokół BGP dla wszystkich pośrednich połączeń między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [o BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Czy można mieć więcej niż jeden tunel między bramą sieci VPN platformy Azure i lokalną siecią użytkownika?
Tak, można utworzyć więcej niż jeden tunel VPN S2S między bramą sieci VPN platformy Azure i siecią lokalną. Należy pamiętać, że wszystkie te tunele będą uwzględniane w łącznej liczbie tuneli dla bram sieci VPN platformy Azure oraz że należy włączyć protokół w obydwu tunelach.

Na przykład dwa nadmiarowe tunele między bramą sieci VPN platformy Azure i jedną z sieci lokalnych będą wymagały 2 tuneli z łącznego limitu przydziału dla bramy sieci VPN użytkownika platformy Azure (10 dla standardowej i 30 dla wysokowydajnej).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Czy można mieć wiele tuneli między dwiema sieciami wirtualnymi platformy Azure z protokołem BGP?
Tak, ale co najmniej jedna z bram sieci wirtualnej musi mieć konfigurację aktywne-aktywne.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Czy można użyć protokołu BGP dla sieci VPN S2S w konfiguracji współistnienia sieci ExpressRoute i sieci VPN S2S?
Tak. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Jakiego adresu używa brama sieci VPN platformy Azure dla adresu IP elementu równorzędnego protokołu BGP?
Brama sieci VPN platformy Azure przydziela pojedynczy adres IP z zakresu podsieci bramy zdefiniowanego dla sieci wirtualnej. Domyślnie jest to przedostatni adres zakresu. Na przykład jeśli podsieć bramy to 10.12.255.0/27, w zakresie od 10.12.255.0 do 10.12.255.31, adresem IP elementu równorzędnego protokołu BGP dla bramy sieci VPN platformy Azure jest 10.12.255.30. Te informacje można znaleźć wśród informacji dotyczących bramy sieci VPN platformy Azure.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Jakie są wymagania dotyczące adresów IP elementów równorzędnych protokołu BGP na urządzeniu sieci VPN użytkownika?
Adres lokalnego elementu równorzędnego protokołu BGP **NIE MOŻE** być taki sam jak publiczny adres IP urządzenia sieci VPN użytkownika. Na urządzeniu sieci VPN należy użyć innego adresu IP dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu, ale należy pamiętać, że nie może być to adres APIPA (169.254.x.x). Ten adres należy określić w odpowiedniej bramie sieci lokalnej reprezentującej daną lokalizację.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Co należy określić jako prefiksy adresów użytkownika dla bramy sieci lokalnej w przypadku korzystania z protokołu BGP?
Brama sieci lokalnej platformy Azure określa początkowe prefiksy adresów dla sieci lokalnej. W przypadku protokołu BGP należy przydzielić prefiks hosta (prefiks /32) adresu IP elementu równorzędnego protokołu BGP jako przestrzeń adresów dla danej sieci lokalnej. Jeśli adresem IP elementu równorzędnego protokołu BGP jest 10.52.255.254, należy określić adres „10.52.255.254/32” jako przestrzeń adresów sieci lokalnej dla bramy sieci lokalnej reprezentującej tę sieć lokalną. Należy tak postąpić, aby brama sieci VPN platformy Azure ustanawiała sesję protokołu BGP przez tunel sieci VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Co należy dodać do lokalnego urządzenia sieci VPN dla sesji połączenia równorzędnego protokołu BGP?
Na urządzeniu sieci VPN należy dodać trasę hosta dla adresu IP elementu równorzędnego protokołu BGP platformy Azure wskazującego tunel sieci VPN S2S protokołu IPsec. Na przykład jeśli adresem IP elementu równorzędnego sieci VPN platformy Azure jest „10.12.255.30”, należy dodać trasę hosta dla adresu „10.12.255.30” z interfejsem następnego skoku pasującym do interfejsu tunelu protokołu IPsec urządzenia sieci VPN użytkownika.
