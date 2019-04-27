---
title: O Azure Point-to-Site routingu | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak działa routing sieci VPN typu punkt-lokacja.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653616"
---
# <a name="about-point-to-site-vpn-routing"></a>Routing sieci VPN typu punkt-lokacja — informacje

Ten artykuł pomoże Ci zrozumieć, jak działa routing Azure Point-to-Site VPN. Sieci VPN P2S zachowania routingu jest zależna od klienta, system operacyjny, protokół używany do połączenia sieci VPN i jak sieci wirtualne (Vnet) są połączone ze sobą.

Platforma Azure obsługuje obecnie dwa protokoły dla dostępu zdalnego, IKEv2 i SSTP. Protokół IKEv2 jest obsługiwany w wielu systemach operacyjnych klientów, w tym Windows, Linux, MacOS, Android i iOS. Protokół SSTP jest obsługiwana tylko na Windows. Jeśli zmiany topologii sieci i klientów sieci VPN Windows pakietu klienta VPN dla klientów Windows należy pobrane i zainstalowane ponownie, aby zmiany zostały zastosowane do klienta.

> [!NOTE]
> Ten artykuł dotyczy tylko protokołu IKEv2.
>

## <a name="diagrams"></a>Diagramy

Istnieje szereg różnych diagramach w tym artykule. Każda sekcja pokazuje różnych topologii lub konfiguracji. Na potrzeby tego artykułu połączenia sieć wirtualna-sieć wirtualna i lokacja-lokacja (S2S) działają tak samo jak jedne i drugie są tuneli IPsec. Wszystkie bramy sieci VPN w tym artykule są oparte na trasach.

## <a name="isolatedvnet"></a>Jednej izolowanej sieci wirtualnej

Połączenie bramy sieci VPN typu punkt-lokacja, w tym przykładzie jest dla sieci wirtualnej, która jest podłączona lub nie połączona z innych sieci wirtualnych (sieci VNet1). W tym przykładzie klienci korzystający z protokołu SSTP lub IKEv2 można uzyskać dostęp do sieci VNet1.

![izolowany routingu w sieci wirtualnej](./media/vpn-gateway-about-point-to-site-routing/1.jpg "izolowane, routing sieci wirtualnej")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16, 192.168.0.0/24

* Dodano dla klientów innych niż Windows trasy: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskać dostępu do sieci VNet1

* Inne niż Windows, klienci mogą uzyskiwać dostęp do sieci VNet1

## <a name="multipeered"></a>Wiele wirtualnych sieciach równorzędnych

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest połączona z sieci VNet2. 2 w sieci wirtualnej jest połączona z sieci VNet3. Sieć VNet1 jest połączona z sieci VNet4. Nie ma żadnych bezpośrednich równorzędne sieci VNet1 i sieci VNet3. Sieć VNet1 ma "Zezwalaj na tranzyt bramy" i sieć VNet2 ma "Użyj bram zdalnych" włączone.

Klienci korzystający z Windows mogą uzyskiwać dostęp do wirtualne sieci równorzędne bezpośrednio, ale klient sieci VPN musi zostać ponownie pobrana, jeśli wszystkie zmiany zostaną wprowadzone do komunikacji równorzędnej sieci wirtualnych lub topologii sieci. Klienci non-Windows mogą uzyskać bezpośrednio w sieciach równorzędnych sieci wirtualnych. Dostęp nie jest przechodnia i jest ograniczona tylko bezpośrednio w równorzędnych sieciach wirtualnych.

![wiele nawiązać komunikacji równorzędnej między sieciami wirtualnymi](./media/vpn-gateway-about-point-to-site-routing/2.jpg "wielu nawiązać komunikacji równorzędnej między sieciami wirtualnymi")

### <a name="address-space"></a>Przestrzeń adresowa:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Dodano dla klientów innych niż Windows trasy: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows dostęp do sieci VNet1, VNet2 i sieci VNet4, ale klient sieci VPN musi zostać ponownie pobrana topologia zmiany zaczęły obowiązywać.

* Klienci non-Windows dostęp do sieci VNet1, VNet2 i sieci VNet4

## <a name="multis2s"></a>Wiele sieci wirtualnych połączonych za pomocą sieci VPN S2S

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest podłączony do sieci VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Sieć VNet2 jest podłączony do sieci VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej lub połączenie sieci VPN typu lokacja-lokacja między VNet1 i sieci VNet3. Wszystkie połączenia lokacja-lokacja nie działa dla routingu BGP.

Klienci korzystający z Windows lub innego obsługiwanego systemu operacyjnego dostęp tylko do sieci VNet1. Aby uzyskać dostęp do dodatkowych sieci wirtualnych, można użyć protokołu BGP.

![wiele sieci wirtualnych i S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "wielu sieci wirtualnych i S2S")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskać dostęp tylko do sieci VNet1

* Klienci non-Windows mogą uzyskiwać dostęp do sieci VNet1 tylko

## <a name="multis2sbgp"></a>Wiele sieci wirtualnych połączonych za pomocą sieci VPN S2S (BGP)

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest podłączony do sieci VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Sieć VNet2 jest podłączony do sieci VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej lub połączenie sieci VPN typu lokacja-lokacja między VNet1 i sieci VNet3. Wszystkie połączenia lokacja-lokacja są uruchomione protokołu BGP dla routingu.

Klienci korzystający z Windows lub innego obsługiwanego systemu operacyjnego, mogą uzyskiwać dostęp do wszystkich sieci wirtualnych, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, ale trasy połączone sieci wirtualne muszą być ręcznie dodane do klientów Windows.

![wiele sieci wirtualnych i S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "wielu sieci wirtualnych i S2S protokołu BGP")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows dostęp do sieci VNet1, VNet2 i sieci VNet3, ale będzie musiał ręcznie należy dodać trasy do sieci VNet2 i sieci VNet3.

* Klienci non-Windows dostęp do sieci VNet1, VNet2 i sieci VNet3

## <a name="vnetbranch"></a>Jednej sieci wirtualnej i oddziału

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. VNet1 nie jest połączony / połączona z innej sieci wirtualnej, ale jest połączona z lokacją lokalną za pomocą połączenia sieci VPN typu lokacja-lokacja protokołu BGP nie jest uruchomiona.

Klientów z systemami Windows i innych niż Windows mogą uzyskać dostęp tylko do sieci VNet1.

![Routing za pomocą sieci wirtualnej i oddziału](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routing z sieci wirtualnej i oddziału")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskać dostęp tylko do sieci VNet1

* Klienci non-Windows mogą uzyskiwać dostęp do sieci VNet1 tylko

## <a name="vnetbranchbgp"></a>Jednej sieci wirtualnej i oddziału (BGP)

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest podłączona lub nie połączona z dowolnej innej sieci wirtualnej, ale jest połączona z lokacją lokalną (Site1) za pośrednictwem połączenia sieci VPN typu lokacja-lokacja, uruchamianie protokołu BGP.

Klienci Windows mogą uzyskiwać dostęp do sieci wirtualnej oddziału (Site1), ale należy ręcznie dodać trasy do Site1 do klienta. Klienci non-Windows mogą uzyskać dostęp do sieci wirtualnej, a także w środowisku lokalnym oddziale firmy.

![jednej sieci wirtualnej i oddziału (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jednej sieci wirtualnej i oddziału")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskiwać dostęp do sieci VNet1 Site1, ale będzie musiał ręcznie należy dodać trasy, aby Site1.

* Klienci non-Windows mogą uzyskiwać dostęp do sieci VNet1 Site1.


## <a name="multivnets2sbranch"></a>Wiele sieci wirtualnych połączonych za pomocą S2S i oddziału

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest podłączony do sieci VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Sieć VNet2 jest podłączony do sieci VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej lub tunelu sieci VPN typu lokacja-lokacja między sieciami VNet1 i sieci VNet3. Sieci VNet3 jest podłączony do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN nie są uruchomione protokołu BGP.

Wszyscy klienci mogą uzyskać dostęp do sieci VNet1 tylko.

![Multi-sieć wirtualna S2S i gałęzi pakietu office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-sieć wirtualna S2S i gałęzi pakietu office")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Klienci dodane trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskiwać dostęp do sieci VNet1 tylko

* Klienci non-Windows mogą uzyskiwać dostęp do sieci VNet1 tylko

## <a name="multivnets2sbranchbgp"></a>Wiele sieci wirtualnych połączonych za pomocą S2S i oddziale firmy (BGP)

W tym przykładzie połączenia bramy sieci VPN typu punkt-lokacja jest vnet1. Sieć VNet1 jest podłączony do sieci VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Sieć VNet2 jest podłączony do sieci VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie ma bezpośredniej komunikacji równorzędnej lub tunelu sieci VPN typu lokacja-lokacja między sieciami VNet1 i sieci VNet3. Sieci VNet3 jest podłączony do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN działają protokołu BGP.

Klienci korzystający z Windows mogą uzyskiwać dostęp do sieci wirtualnych i witryn, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, ale trasy do sieci VNet2, sieci VNet3 i Site1 należy dodać ręcznie do klienta. Klienci non-Windows mogą uzyskać dostęp do sieci wirtualnych i witryn, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, bez żadnej interwencji ręcznej. Dostęp jest przechodnia, a klienci mogą uzyskiwać dostęp zasobów na wszystkich połączonych sieci wirtualnych i witryn (lokalnie).

![Multi-sieć wirtualna S2S i gałęzi pakietu office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-sieć wirtualna S2S i gałęzi pakietu office")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Klienci dodane trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci Windows mogą uzyskać dostęp, VNet1, VNet2, sieci VNet3 i Site1, ale należy ręcznie dodać trasy do sieci VNet2, sieci VNet3 i Site1 do klienta.

* Klienci non-Windows mogą uzyskać dostęp, VNet1, Vnet2, sieci VNet3 i Site1.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [tworzenie sieci VPN typu P2S za pomocą witryny Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) aby rozpocząć tworzenie sieci VPN typu P2S.
