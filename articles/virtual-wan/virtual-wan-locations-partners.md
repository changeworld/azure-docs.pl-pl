---
title: Lokalizacje partnerów sieci wirtualnej platformy Azure | Microsoft Docs
description: Ten artykuł zawiera listę partnerów i lokalizacji centrów wirtualnych sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: cefd0653985e4340e62e0ea56f73ff2cabf5741b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312123"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Wirtualne partnerzy sieci WAN i wirtualne lokalizacje koncentratora

Ten artykuł zawiera informacje na temat regionów i partnerów obsługiwanych przez wirtualną sieć WAN na potrzeby łączności z koncentratorem wirtualnym.

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie lub za pomocą urządzeń dostawcy za pośrednictwem wirtualnego partnera sieci WAN. Korzystanie z urządzeń partnerskich umożliwia łatwe korzystanie z programu, uproszczenie łączności i zarządzanie konfiguracją.

Połączenie z urządzeniem lokalnym jest nawiązywane w zautomatyzowany sposób z koncentratorem wirtualnym. Koncentratorem wirtualnym jest sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Możesz mieć tylko jeden koncentrator na region.

## <a name="automation"></a>Automatyzacja z partnerów łączności

Urządzenia łączące się z wirtualną siecią WAN platformy Azure mają wbudowaną automatyzację, aby nawiązać połączenie. Jest to zazwyczaj skonfigurowane w interfejsie użytkownika zarządzania urządzeniami (lub równoważne), które konfiguruje zarządzanie łącznością i konfiguracją między urządzeniem oddziału sieci VPN a punktem końcowym sieci VPN usługi Azure Virtual Hub (Brama sieci VPN).

Następująca Automatyzacja wysokiego poziomu jest skonfigurowana w konsoli urządzenia/w centrum zarządzania:

* Odpowiednie uprawnienia dla urządzenia umożliwiającego dostęp do grupy zasobów wirtualnej sieci WAN platformy Azure
* Przekazywanie urządzenia oddziału do wirtualnej sieci WAN platformy Azure
* Automatyczne pobieranie informacji o łączności z platformą Azure
* Konfiguracja lokalnego urządzenia rozgałęzienia 

Niektórzy partnerzy łączności mogą zwiększyć automatyzację, aby uwzględnić tworzenie sieci wirtualnej i VPN Gateway usługi Azure Virtual Hub. Jeśli chcesz dowiedzieć się więcej o automatyzacji, zobacz [Configure Automation-WAN Partners](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Łączność za pośrednictwem partnerów

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Następujący partnerzy są styczeń na naszym planie w najbliższej przyszłości: Arista, F5 Networks, Fortinet, Silver-szczyt, Velocloud, Nuage Nokia.

## <a name="locations"></a>Miejsce

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz temat [często zadawane pytania dotyczące wirtualnej sieci WAN](virtual-wan-faq.md).

* Aby uzyskać więcej informacji na temat automatyzowania łączności z usługą Azure Virtual WAN, zobacz [wirtualne partnerzy sieci WAN — jak zautomatyzować](virtual-wan-configure-automation-providers.md).
