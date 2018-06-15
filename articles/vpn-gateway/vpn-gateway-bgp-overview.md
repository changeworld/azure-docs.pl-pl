---
title: Omówienie protokołu BGP z bramy sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie użycia protokołu BGP z bramami sieci VPN na platformie Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
ms.openlocfilehash: 13a17eb3d78e70a09864bf218f1027d6e98486a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23883509"
---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Omówienie użycia protokołu BGP z bramami sieci VPN na platformie Azure
W tym artykule omówiono obsługę protokołu BGP (Border Gateway Protocol) w bramach sieci VPN na platformie Azure.

BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. W przypadku jego użycia w kontekście sieci wirtualnych Azure, protokół BGP umożliwia bramom sieci VPN na platformie Azure i lokalnym urządzeniom sieci VPN (nazywanym elementami równorzędnymi lub sąsiednimi BGP), przeprowadzaną za pośrednictwem bram lub routerów wymianę „tras” zawierających przeznaczone dla obu bram informacje na temat dostępności i osiągalności tych prefiksów. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP. 

## <a name="why"></a>Dlaczego warto używać protokołu BGP?
Protokół BGP stanowi funkcję opcjonalną, której można używać z bramami sieci VPN na platformie Azure opartymi na trasie. Przed włączeniem funkcji należy również upewnić się, że lokalne urządzenia sieci VPN obsługują protokół BGP. Bram sieci VPN na platformie Azure i lokalnych urządzeń sieci VPN można używać także bez protokołu BGP. Odpowiada to użyciu tras statycznych (bez stosowania protokołu BGP) *w porównaniu do* użycia routingu dynamicznego z zastosowaniem protokołu BGP między sieciami i platformą Azure.

Protokół BGP oferuje kilka zalet i nowych możliwości:

### <a name="prefix"></a>Obsługują aktualizacje automatyczne i elastyczne prefiksu
W przypadku użycia protokołu BGP wystarczy zadeklarować 	prefiks o minimalnej długości dla określonego elementu równorzędnego BGP za pośrednictwem tunelu VPN S2S wykorzystującego protokół IPsec. Może to być nawet sam prefiks hosta (/32) adresu IP elementu równorzędnego BGP dla lokalnego urządzenia sieci VPN. Można określić, które prefiksy sieci lokalnej mają być ogłaszane na platformie Azure w celu umożliwienia dostępu sieci wirtualnej Azure.

Można również anonsują większych prefiksy, które mogą zawierać części sieci wirtualnej prefiksy adresów, takich jak duże prywatnych przestrzeni adresów IP (np. 10.0.0.0/8). Należy pamiętać, że prefiksy nie może być identyczny z jednym z prefiksami Twojej sieci wirtualnej. Trasy identyczne z prefiksami w sieci wirtualnej zostaną odrzucone.

### <a name="multitunnel"></a>Obsługuje wiele tuneli między sieci wirtualnej i lokalnej lokacji z automatycznej pracy awaryjnej w oparciu o BGP
Można utworzyć wiele połączeń między swoją siecią wirtualną na platformie Azure i lokalnymi urządzeniami sieci VPN w tej samej lokalizacji. Ta funkcja pozwala korzystać z wielu tuneli (ścieżek) między dwiema sieciami w konfiguracji aktywne/aktywne. Jeśli jeden z tunele jest rozłączony, odpowiednie trasy zostaną wycofane za pośrednictwem protokołu BGP i ruch jest kierowany do pozostałych tuneli.

Na poniższym diagramie przedstawiono prosty przykład tej konfiguracji charakteryzującej się wysoką dostępnością:

![Wiele aktywnych ścieżek](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Obsługuje routing tranzytowy między sieci lokalnej i wiele sieci wirtualnych platformy Azure
Protokół BGP umożliwia uzyskiwanie i propagowanie przez wiele bram prefiksów z różnych sieci, bez względu na to, czy są połączone bezpośrednio lub pośrednio. Umożliwia to realizowany przy użyciu bram sieci VPN na platformie Azure routing tranzytowy między lokalnymi lokacjami lub w obrębie wielu sieci wirtualnych Azure.

Na poniższym diagramie przedstawiono przykład topologii z wieloma przeskokami z wielu ścieżek, która umożliwia przesyłanie ruchu między dwiema sieciami lokalnymi za pośrednictwem bram sieci VPN na platformie Azure w ramach usługi Microsoft Networks:

![Przesyłanie z wieloma przeskokami](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>CZĘSTO ZADAWANE PYTANIA DOTYCZĄCE PROTOKOŁU BGP
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Następne kroki
Opis procedury konfigurowania protokołu BGP pod kątem połączeń obejmujących wiele lokalizacji i połączeń między sieciami wirtualnymi zawiera artykuł [Getting started with BGP on Azure VPN gateways](vpn-gateway-bgp-resource-manager-ps.md) (Rozpoczęcie pracy z protokołem BGP dla bram sieci VPN na platformie Azure).

