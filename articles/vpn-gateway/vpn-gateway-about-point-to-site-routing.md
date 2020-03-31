---
title: 'Usługa Azure VPN Gateway: informacje o routingu P2S'
description: Ten artykuł pomaga zrozumieć, jak działa routing sieci VPN typu punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239748"
---
# <a name="about-point-to-site-vpn-routing"></a>Routing sieci VPN typu punkt-lokacja — informacje

Ten artykuł pomaga zrozumieć, jak działa routing sieci VPN w usłudze Azure Point-to-Site. Zachowanie routingu sieci VPN P2S zależy od systemu operacyjnego klienta, protokołu używanego dla połączenia sieci VPN i sposobu, w jaki sieci wirtualne (sieci wirtualne) są ze sobą połączone.

Platforma Azure obsługuje obecnie dwa protokoły dostępu zdalnego, IKEv2 i SSTP. IKEv2 jest obsługiwany w wielu klienckich systemach operacyjnych, w tym Windows, Linux, MacOS, Android i iOS. Protokół SSTP jest obsługiwany tylko w systemie Windows. Jeśli zmienisz topologię sieci i masz klientów sieci VPN systemu Windows, pakiet klienta sieci VPN dla klientów systemu Windows musi zostać pobrany i zainstalowany ponownie, aby zmiany zostały zastosowane do klienta.

> [!NOTE]
> Ten artykuł dotyczy tylko IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Informacje o diagramach

Istnieje wiele różnych diagramów w tym artykule. Każda sekcja zawiera inną topologię lub konfigurację. Na potrzeby tego artykułu połączenia lokacja lokacja (S2S) i sieć wirtualna do sieci wirtualnej działają w ten sam sposób, ponieważ oba są tunelami IPsec. Wszystkie bramy sieci VPN w tym artykule są oparte na trasach.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Jeden izolowany VNet

Połączenie bramy sieci VPN typu punkt-lokacja w tym przykładzie jest dla sieci wirtualnej, która nie jest połączona ani równorzędna z żadną inną siecią wirtualną (VNet1). W tym przykładzie klienci mogą uzyskiwać dostęp do sieci wirtualnej1.

![izolowana routing sieci wirtualnej](./media/vpn-gateway-about-point-to-site-routing/1.jpg "izolowana routing sieci wirtualnej")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1

* Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Wiele równorzędnych sieci wirtualnych

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć wirtualna1 jest równorzędna z siecią wirtualną2. Sieć wirtualna 2 jest równorzędna z siecią wirtualną3. Sieć wirtualna1 jest równorzędna z siecią wirtualną4. Nie ma bezpośredniego komunikacji równorzędnej między siecią wirtualną1 i siecią wirtualną3. VNet1 ma "Zezwalaj na tranzyt bramy" i sieci wirtualnej2 i sieci wirtualnej4 mają "Użyj bramy zdalne" włączone.

Klienci korzystający z systemu Windows mogą uzyskiwać dostęp bezpośrednio do równorzędnych sieci wirtualnych, ale klient sieci VPN musi zostać pobrany ponownie, jeśli zostaną wprowadzone jakiekolwiek zmiany w komunikacji równorzędnej sieci wirtualnej lub topologii sieci. Klienci spoza systemu Windows mogą uzyskiwać dostęp bezpośrednio do równorzędnych sieci wirtualnych. Dostęp nie jest przechodnie i jest ograniczona tylko do bezpośrednio równorzędnych sieci wirtualnych.

![wiele równorzędnych sieci wirtualnych](./media/vpn-gateway-about-point-to-site-routing/2.jpg "wiele równorzędnych sieci wirtualnych")

### <a name="address-space"></a>Przestrzeń adresowa:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2 i sieci wirtualnej4, ale klient sieci VPN musi zostać pobrany ponownie, aby zmiany topologii zostały wprowadzone.

* Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2 i sieci wirtualnej4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Wiele sieci wirtualnych połączonych za pomocą sieci VPN S2S

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 jest połączona z siecią wirtualną2 przy użyciu połączenia sieci VPN lokacja lokacja. Sieć Wirtualna 2 jest połączona z siecią wirtualną3 przy użyciu połączenia sieci VPN lokacja lokacja. Nie ma bezpośredniego komunikacji równorzędnej lub połączenia sieci VPN lokacja lokacja między siecią wirtualną1 a siecią wirtualną3. Wszystkie połączenia lokacja-lokacja nie są uruchomione BGP dla routingu.

Klienci korzystający z systemu Windows lub innego obsługiwanego systemu operacyjnego mogą uzyskiwać dostęp tylko do sieci wirtualnej1. Aby uzyskać dostęp do dodatkowych sieci wirtualnych, należy użyć protokołu BGP.

![wiele sieci wirtualnych i S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "wiele sieci wirtualnych i S2S")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

* Klienci spoza systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Wiele sieci wirtualnych połączonych za pomocą sieci VPN S2S (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 jest połączona z siecią wirtualną2 przy użyciu połączenia sieci VPN lokacja lokacja. Sieć Wirtualna 2 jest połączona z siecią wirtualną3 przy użyciu połączenia sieci VPN lokacja lokacja. Nie ma bezpośredniego komunikacji równorzędnej lub połączenia sieci VPN lokacja lokacja między siecią wirtualną1 a siecią wirtualną3. Wszystkie połączenia lokacja z lokacją są uruchomione BGP dla routingu.

Klienci korzystający z systemu Windows lub innego obsługiwanego systemu operacyjnego mogą uzyskać dostęp do wszystkich sieci wirtualnych połączonych za pomocą połączenia sieci VPN lokacja lokacja, ale trasy do połączonych sieci wirtualnych muszą być ręcznie dodawane do klientów systemu Windows.

![wiele sieci wirtualnych i S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "wiele sieci wirtualnych i S2S BGP")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2 i sieci wirtualnej3, ale trasy do sieci wirtualnej 2 i sieci wirtualnej3 będą musiały zostać dodane ręcznie.

* Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2 i sieci wirtualnej3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Jedna sieć wirtualna i oddział

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 nie jest połączona/ równorzędna z żadną inną siecią wirtualną, ale jest połączona z lokacją lokalną za pośrednictwem połączenia sieci VPN lokacja lokacja lokacja, które nie jest uruchomione jako BGP.

Klienci systemu Windows i innych firm mogą uzyskiwać dostęp tylko do sieci wirtualnej1.

![routing z siecią wirtualną i oddziałem firmy](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routing z siecią wirtualną i oddziałem firmy")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Strona1: 10.101.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

* Klienci spoza systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Jedna sieć wirtualna i oddział (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 nie jest połączona ani równorzędna z żadną inną siecią wirtualną, ale jest połączona z lokacją lokalną (Site1) za pośrednictwem połączenia sieci VPN lokacja lokacja lokacja z uruchomioną usługą BGP.

Klienci systemu Windows mogą uzyskać dostęp do sieci wirtualnej i oddziału (Lokacja1), ale trasy do lokacji1 muszą być ręcznie dodane do klienta. Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej oraz lokalnego oddziału firmy.

![jedna sieć wirtualna i oddział (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jedna sieć wirtualna i oddział")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Strona1: 10.101.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1 i lokacji1, ale trasy do witryny 1 będą musiały być dodawane ręcznie.

* Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1 i lokacji1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Wiele sieci wirtualnych połączonych za pomocą S2S i oddziału

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 jest połączona z siecią wirtualną2 przy użyciu połączenia sieci VPN lokacja lokacja. Sieć Wirtualna 2 jest połączona z siecią wirtualną3 przy użyciu połączenia sieci VPN lokacja lokacja. Nie ma bezpośredniego komunikacji równorzędnej lub tunelu sieci VPN lokacja lokacja między sieciami VNet1 i VNet3. Sieć Wirtualna3 jest połączona z oddziałem firmy (Site1) przy użyciu połączenia sieci VPN lokacja lokacja. Wszystkie połączenia sieci VPN nie są uruchomione BGP.

Wszyscy klienci mogą uzyskiwać dostęp tylko do sieci wirtualnej1.

![multi-VNet S2S i oddział](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S i oddział")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Strona1: 10.101.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

* Klienci spoza systemu Windows mogą uzyskiwać dostęp tylko do sieci wirtualnej1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Wiele sieci wirtualnych połączonych za pomocą S2S i oddziału (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja jest przeznaczone dla sieci wirtualnej1. Sieć Wirtualna1 jest połączona z siecią wirtualną2 przy użyciu połączenia sieci VPN lokacja lokacja. Sieć Wirtualna 2 jest połączona z siecią wirtualną3 przy użyciu połączenia sieci VPN lokacja lokacja. Nie ma bezpośredniego komunikacji równorzędnej lub tunelu sieci VPN lokacja lokacja między sieciami VNet1 i VNet3. Sieć Wirtualna3 jest połączona z oddziałem firmy (Site1) przy użyciu połączenia sieci VPN lokacja lokacja. Wszystkie połączenia sieci VPN są uruchomione BGP.

Klienci korzystający z systemu Windows mogą uzyskiwać dostęp do sieci wirtualnych i lokacji połączonych za pomocą połączenia sieci VPN lokacja lokacja, ale trasy do sieci wirtualnej2, sieci wirtualnej3 i lokacji1 muszą być ręcznie dodane do klienta. Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnych i witryn połączonych za pomocą połączenia sieci VPN lokacja lokacja bez żadnej ręcznej interwencji. Dostęp jest przechodni, a klienci mogą uzyskiwać dostęp do zasobów we wszystkich połączonych sieciach wirtualnych i lokacjach (lokalnie).

![multi-VNet S2S i oddział](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S i oddział")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Strona1: 10.101.0.0/16

### <a name="routes-added"></a>Dodano trasy

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów innych niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2, sieci wirtualnej3 i lokacji1, ale trasy do sieci Wirtualnej2, sieci Wirtualnej3 i lokacji1 muszą być ręcznie dodawane do klienta.

* Klienci spoza systemu Windows mogą uzyskiwać dostęp do sieci wirtualnej1, sieci wirtualnej2, sieci wirtualnej3 i witryny 1.

## <a name="next-steps"></a>Następne kroki

Zobacz [Tworzenie sieci VPN P2S za pomocą portalu Azure,](vpn-gateway-howto-point-to-site-resource-manager-portal.md) aby rozpocząć tworzenie sieci VPN P2S.
