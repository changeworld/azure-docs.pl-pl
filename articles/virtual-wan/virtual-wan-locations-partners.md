---
title: Partnerzy i lokalizacje wirtualnej sieci WAN platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera listę partnerów wirtualnej sieci WAN platformy Azure i lokalizacji centrum.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123291"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Wirtualni partnerzy sieci WAN i wirtualne lokalizacje koncentratorów

Ten artykuł zawiera informacje na temat regionów obsługiwanych przez wirtualną sieć WAN i partnerów w zakresie łączności z centrum wirtualnym.

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie lub za pomocą urządzeń dostawcy za pośrednictwem wirtualnego partnera sieci WAN. Korzystanie z urządzeń partnerskich umożliwia łatwość użycia, uproszczenie łączności i zarządzanie konfiguracją.

Łączność z urządzenia lokalnego jest ustanawiana w sposób zautomatyzowany do Centrum wirtualnego. Centrum wirtualne to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Możesz mieć tylko jedno centrum na region.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automatyzacja od partnerów łączności

Urządzenia łączące się z wirtualną siecią WAN platformy Azure mają wbudowaną automatyzację do łączenia się. Jest to zazwyczaj skonfigurowane w interfejsie użytkownika zarządzania urządzeniami (lub równoważnym), który konfiguruje zarządzanie łącznością i konfiguracją między urządzeniem oddziału sieci VPN a punktem końcowym sieci VPN usługi Azure Virtual Hub (bramą sieci VPN).

W konsoli urządzenia/centrum zarządzania skonfigurowane są następujące automatyzacje wysokiego poziomu:

* Odpowiednie uprawnienia urządzenia do uzyskiwania dostępu do wirtualnej grupy zasobów sieci WAN platformy Azure
* Przekazywanie urządzenia w oddziale do wirtualnej sieci WAN platformy Azure
* Automatyczne pobieranie informacji o łączności platformy Azure
* Konfiguracja lokalnego urządzenia oddziału 

Niektórzy partnerzy łączności może rozszerzyć automatyzacji w celu włączenia tworzenia usługi Azure Virtual Hub VNet i bramy sieci VPN. Jeśli chcesz dowiedzieć się więcej o automatyzacji, zobacz [Wskazówki dotyczące automatyzacji dla partnerów wirtualnej sieci WAN.](virtual-wan-configure-automation-providers.md)

## <a name="connectivity-through-partners"></a><a name="partners"></a>Łączność za pośrednictwem partnerów

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Na naszej mapie drogowej na najbliższą przyszłość zostaną następujący partnerzy: 128 Technologies, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink i VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Lokalizacje

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [wirtualne często zadawane pytania](virtual-wan-faq.md)dotyczące sieci WAN .

* Aby uzyskać więcej informacji na temat automatyzacji łączności z wirtualną siecią WAN platformy Azure, zobacz [wskazówki dotyczące automatyzacji dla partnerów wirtualnej sieci WAN.](virtual-wan-configure-automation-providers.md)
