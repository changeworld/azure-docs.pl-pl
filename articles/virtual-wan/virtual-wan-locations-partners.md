---
title: Azure wirtualne sieci WAN w ramach której partnerzy lokalizacje | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę partnerów Azure wirtualnego WAN i lokalizacji Centrum.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60459844"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Wirtualne sieci WAN partnerzy i lokalizacje koncentrator wirtualny

Ten artykuł zawiera informacji na temat wirtualnych sieci WAN obsługiwanych regionach oraz partnerów łączności do koncentratora wirtualnego.

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie albo za pomocą dostawcy urządzeń za pośrednictwem wirtualnej sieci WAN partnera. Za pomocą urządzeń partnerów pozwala ułatwić użytkowania uproszczenia łączności i zarządzanie konfiguracją.

Połączenia z poziomu lokalnego urządzenia jest określana w zautomatyzowany sposób do koncentratora wirtualnego. Koncentrator wirtualny jest zarządzany przez firmę Microsoft sieci wirtualnej. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Może mieć tylko jedno Centrum na region.

## <a name="automation"></a>Automatyzacja partnerów łączności

Urządzenia łączące się Azure wirtualnego WAN ma wbudowane automatyzacji do łączenia z. Zazwyczaj jest ono ustawione w górę w zarządzania urządzeniami interfejsu użytkownika (lub odpowiednik), która konfiguruje łączności i konfiguracji zarządzania między urządzeniem sieci VPN gałęzi do punktu końcowego sieci VPN platformy Azure wirtualnego Centrum (Brama sieci VPN).

Następujące wysokiego poziomu automatyzacji jest skonfigurowana w Centrum konsoli/zarządzania urządzeniami:

* Odpowiednie uprawnienia dla tego urządzenia do dostępu do wirtualnej sieci WAN grupy zasobów platformy Azure
* Przekazywanie urządzenia gałęzi do platformy Azure, wirtualna sieć WAN
* Automatyczne pobieranie informacji łączność platformy Azure
* Konfiguracja lokalnej gałęzi urządzenia 

Niektórzy partnerzy łączności mogą rozszerzyć automatyzację, obejmują tworzenie sieci wirtualnej koncentratora wirtualnego platformy Azure i bramy sieci VPN. Jeśli chcesz dowiedzieć się więcej na temat automatyzacji, zobacz [Konfigurowanie automatyzacji — partnerzy WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Łączność za pośrednictwem partnerów

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>lokalizacje

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat wirtualnych sieci WAN, zobacz [wirtualne sieci WAN — często zadawane pytania](virtual-wan-faq.md).

* Aby uzyskać więcej informacji o tym, jak zautomatyzować połączenie WAN wirtualnych platformy Azure, zobacz [wirtualne sieci WAN partnerów — jak zautomatyzować](virtual-wan-configure-automation-providers.md).
