---
title: O Azure punkt-lokacja routingu | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga zrozumieć, jak działa routing sieci VPN typu punkt-lokacja.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: a0576e00d22b731f7ee9de3a9b021c0f52fc8ef9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702180"
---
# <a name="about-point-to-site-vpn-routing"></a>Routing sieci VPN typu punkt-lokacja — informacje

Ten artykuł pomaga w zrozumieniu zachowania routingu sieci VPN Azure punkt-lokacja. Zachowanie routingu P2S sieci VPN jest zależna od klienta systemu operacyjnego, protokół używany do połączenia sieci VPN i jak sieci wirtualnych (sieci wirtualne) są połączone ze sobą.

Azure obecnie obsługuje dwa protokoły dla dostępu zdalnego, IKEv2 i SSTP. Protokół IKEv2 jest obsługiwany w wielu systemach operacyjnych klienta, włącznie z systemu Windows, Linux MacOS, Android i iOS. Protokół SSTP jest obsługiwana tylko w systemie Windows. Jeśli zmiany topologii sieci i klientów sieci VPN systemu Windows, pakietu klienta VPN dla klientów systemu Windows należy pobrany i zainstalowany ponownie, aby zmiany zostały zastosowane do klienta.

> [!NOTE]
> Ten artykuł dotyczy tylko protokołu IKEv2.
>

## <a name="diagrams"></a>Diagramy

Istnieje wiele różnych diagramów w tym artykule. Każda sekcja zawiera innej topologii lub konfiguracji. Do celów tego artykułu lokacja-lokacja (S2S) i połączeń między wirtualnymi do działać tak samo, są tuneli IPsec. Wszystkie bramy sieci VPN w tym artykule są oparte na trasach.

## <a name="isolatedvnet"></a>Jeden izolowane sieci wirtualnej

Połączenie bramy sieci VPN typu punkt-lokacja, w tym przykładzie jest sieci wirtualnej, który nie jest podłączony lub połączyć za pomocą z innych sieci wirtualnych (VNet1). W tym przykładzie VNet1 można uzyskać dostępu klientów korzystających z protokołu SSTP lub IKEv2.

![izolowane sieci wirtualnej routingu](./media/vpn-gateway-about-point-to-site-routing/1.jpg "izolowane routingu w sieci wirtualnej")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do VNet1

* Klienci z systemem innym niż Windows mogą uzyskiwać dostęp do VNet1

## <a name="multipeered"></a>Wiele połączyć za pomocą sieci wirtualnych

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 jest połączyć za pomocą z VNet2. Sieci wirtualnej 2 jest połączyć za pomocą z VNet3. VNet1 jest połączyć za pomocą z VNet4. Nie ma żadnych bezpośrednich komunikacji równorzędnej między VNet1 i VNet3. VNet1 ma "Zezwalaj przesyłania bramy" i ma VNet2 "Użycie bram zdalnego" włączone.

Przy użyciu systemu Windows mogą uzyskać dostęp klienci bezpośrednio połączyć za pomocą sieci wirtualnych, ale klient VPN musi zostać ponownie pobrana, jeśli zmian do topologii sieci lub sieci wirtualnej komunikacji równorzędnej. Klienci z systemem innym niż Windows dostępne bezpośrednio połączyć za pomocą sieci wirtualnych. Dostęp nie jest przechodnie i może zawierać maksymalnie tylko bezpośrednio połączyć za pomocą sieci wirtualnych.

![wiele sieci wirtualnych połączyć za pomocą](./media/vpn-gateway-about-point-to-site-routing/2.jpg "wielu połączyć za pomocą sieci wirtualnych")

### <a name="address-space"></a>Przestrzeń adresowa:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows, mogą uzyskiwać dostęp VNet1, VNet2 i VNet4, ale klient VPN musi zostać ponownie pobrana topologii zmiany zaczęły obowiązywać.

* Klienci systemu Windows nie mogą uzyskiwać dostęp VNet1, VNet2 i VNet4

## <a name="multis2s"></a>Wiele sieci wirtualnych nawiązano połączenie przy użyciu sieci VPN S2S

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie jest równorzędna bezpośredniego ani połączenie sieci VPN typu lokacja-lokacja między VNet1 i VNet3. Wszystkie połączenia lokacja-lokacja nie działa dla routingu BGP.

Klientów przy użyciu systemu Windows lub inny obsługiwany system operacyjny, ma dostęp tylko do VNet1. Aby uzyskać dostęp do dodatkowych sieci wirtualnych, należy użyć protokołu BGP.

![wiele sieci wirtualnych i S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "wiele sieci wirtualnych i S2S")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp tylko do VNet1

* Klienci z systemem innym niż Windows mogą uzyskiwać dostęp do VNet1 tylko

## <a name="multis2sbgp"></a>Wiele sieci wirtualnych nawiązano połączenie przy użyciu sieci VPN S2S (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie jest równorzędna bezpośredniego ani połączenie sieci VPN typu lokacja-lokacja między VNet1 i VNet3. Wszystkie połączenia lokacja-lokacja działają BGP dla routingu.

Przy użyciu systemu Windows lub inny obsługiwany system operacyjny, klienci mogą uzyskiwać dostęp do wszystkich sieci wirtualnych, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, ale trasy do sieci wirtualnych połączonych trzeba dodać ręcznie do klientów systemu Windows.

![wiele sieci wirtualnych i S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "wiele sieci wirtualnych i S2S protokołu BGP")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows, mogą uzyskiwać dostęp VNet1, VNet2 i VNet3, ale trasy VNet2 i VNet3, należy dodać ręcznie.

* Klienci systemu Windows nie mogą uzyskiwać dostęp VNet1, VNet2 i VNet3

## <a name="vnetbranch"></a>Jedną sieć wirtualną i biura oddziału

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 nie jest połączony / połączyć za pomocą z innych sieci wirtualnej, ale jest podłączony do lokacji lokalnej za pomocą połączenia sieci VPN typu lokacja-lokacja nie jest uruchomiona protokołu BGP.

Klienci systemu Windows, mogą uzyskiwać dostęp VNet1 główna i oddział firmy (Site1), ale należy ręcznie dodać trasy do Site1 do klienta. Klienci z systemem innym niż Windows można uzyskać dostępu do VNet1, a także Site1 lokalnymi.

![Routing w sieci wirtualnej i biura oddziału](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routingu z sieci wirtualnej i biura oddziału")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp tylko VNet1

* Klienci z systemem innym niż Windows mogą uzyskiwać dostęp do VNet1 tylko

## <a name="vnetbranchbgp"></a>Jedną sieć wirtualną i biura oddziału (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 nie jest połączone lub połączyć za pomocą z innych sieci wirtualnej, ale jest połączona z lokacją lokalną (Site1) za pośrednictwem połączenia sieci VPN typu lokacja-lokacja z protokołu BGP.

Klientów systemu Windows można uzyskać dostępu do sieci wirtualnej i oddział firmy (Site1), ale należy ręcznie dodać trasy do Site1 do klienta. Klienci z systemem innym niż Windows, można uzyskać dostępu do sieci wirtualnej, a także w lokalnym oddziale firmy.

![jedną sieć wirtualną i biura oddziału (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jedną sieć wirtualną i biura oddziału")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Trasy dodane do klientów systemu Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows, mogą uzyskiwać dostęp VNet1 i Site1, ale trasy do Site1, należy dodać ręcznie.

* Klienci z systemem innym niż Windows można uzyskać dostępu do VNet1 i Site1.


## <a name="multivnets2sbranch"></a>Wiele sieci wirtualnych nawiązano połączenie przy użyciu S2S i biura oddziału

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie jest równorzędna bezpośredniego ani tunelu VPN lokacja-lokacja między sieciami VNet1 i VNet3. VNet3 jest podłączona do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN nie działają protokołu BGP.

Wszyscy klienci mają dostęp tylko VNet1.

![office S2S i gałęzi Multi-VNet](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S i gałęzi pakietu office")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Klienci dodać trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows mogą uzyskiwać dostęp do VNet1 tylko

* Klienci z systemem innym niż Windows mogą uzyskiwać dostęp do VNet1 tylko

## <a name="multivnets2sbranchbgp"></a>Wiele sieci wirtualnych nawiązano połączenie przy użyciu S2S i biura oddziału (BGP)

W tym przykładzie połączenie bramy sieci VPN typu punkt-lokacja zostanie VNet1. VNet1 jest podłączony do VNet2 przy użyciu połączenia sieci VPN typu lokacja-lokacja. VNet2 jest podłączony do VNet3 przy użyciu połączenia sieci VPN typu lokacja-lokacja. Nie jest równorzędna bezpośredniego ani tunelu VPN lokacja-lokacja między sieciami VNet1 i VNet3. VNet3 jest podłączona do biura oddziału (Site1) przy użyciu połączenia sieci VPN typu lokacja-lokacja. Wszystkie połączenia sieci VPN działają protokołu BGP.

Klientów przy użyciu systemu Windows można uzyskać dostępu do sieci wirtualnych i lokacje, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja, ale trasy do VNet2, VNet3 i Site1 należy dodać ręcznie do klienta. Klienci z systemem innym niż Windows można uzyskać dostępu do sieci wirtualnych i lokacji, które są połączone za pomocą połączenia sieci VPN typu lokacja-lokacja bez ręcznej interwencji. Dostęp jest przechodnie i klienci mogą uzyskiwać dostęp do zasobów wszystkich połączonych sieci wirtualnych i witryn (lokalnego).

![office S2S i gałęzi Multi-VNet](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S i gałęzi pakietu office")

### <a name="address-space"></a>Przestrzeń adresowa

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Trasy dodane

* Klienci dodać trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy dodane do klientów z systemem innym niż Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Dostęp

* Klienci systemu Windows, mogą uzyskiwać dostęp VNet1, VNet2 VNet3 i Site1, ale należy ręcznie dodać trasy VNet2, VNet3 i Site1 do klienta.

* Klienci z systemem innym niż Windows można uzyskać dostępu do VNet1, Vnet2 VNet3 i Site1.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [tworzenie P2S sieci VPN przy użyciu portalu Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) aby rozpocząć tworzenie P2S sieć VPN.
