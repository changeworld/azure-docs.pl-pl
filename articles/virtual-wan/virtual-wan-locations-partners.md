---
title: Azure wirtualne sieci WAN w ramach której partnerzy lokalizacje | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę partnerów Azure wirtualnego WAN i lokalizacji Centrum
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984997"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Wirtualne sieci WAN partnerzy i lokalizacje koncentrator wirtualny

Ten artykuł zawiera informacji na temat wirtualnych sieci WAN obsługiwanych regionach oraz preferowany partnerów łączności do koncentratora wirtualnego.

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie albo za pomocą preferowanego dostawcy urządzeń za pośrednictwem wirtualnej sieci WAN preferowanego partnera. Za pomocą preferowanego partnera urządzeń pozwala ułatwić użytkowania uproszczenia łączności i zarządzanie konfiguracją. Połączenia z poziomu lokalnego urządzenia jest określana w zautomatyzowany sposób do koncentratora wirtualnego. Koncentrator wirtualny jest zarządzany przez firmę Microsoft sieci wirtualnej. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Może mieć tylko jedno Centrum na region.

## <a name="regions"></a>Regiony

Lista regionów obsługiwane i dostępne są następujące:

|Region geopolityczny | Regiony świadczenia usługi Azure|
|---|---|
|Ameryka Północna | Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |
|Ameryka Południowa |Brazylia Południowa |
| Europa | Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |
| Azja | Azja Wschodnia, Azja Południowo-Wschodnia |
| Japonia  | Japońska Zachodnia, Japonia Wschodnia |
| Australia | Australia Południowo-Wschodnia, Australia Wschodnia | 
| Dla instytucji rządowych w Australii | Australia Środkowa, Australia Środkowa 2 |
| Indie | Indie Zachodnie, Indie Środkowe, Indie Południowe |
| Korea Południowa | Korea Środkowa, Korea Południowa | 

## <a name="automation-from-connectivity-partners"></a>Automatyzacja partnerów łączności

W tej sekcji opisano szczegółowe informacje wysokiego poziomu automatyzacji od dostawcy połączenia.

Urządzenia łączące się Azure wirtualnego WAN ma wbudowane automatyzacji do łączenia z. Zazwyczaj jest ono ustawione w górę w zarządzania urządzeniami interfejsu użytkownika (lub odpowiednik), która konfiguruje łączności i konfiguracji zarządzania między urządzeniem sieci VPN gałęzi do punktu końcowego sieci VPN platformy Azure wirtualnego Centrum (Brama sieci VPN).

Następujące wysokiego poziomu automatyzacji jest skonfigurowana w Centrum konsoli/zarządzania urządzeniami:

* Odpowiednie uprawnienia dla tego urządzenia do dostępu do wirtualnej sieci WAN grupy zasobów platformy Azure
* Przekazywanie urządzenia gałęzi do platformy Azure, wirtualna sieć WAN
* Automatyczne pobieranie informacji łączność platformy Azure 
* Konfiguracja lokalnej gałęzi urządzenia 

Niektórzy partnerzy łączności mogą rozszerzyć automatyzację, obejmują tworzenie sieci wirtualnej koncentratora wirtualnego platformy Azure i bramy sieci VPN. Jeśli chcesz dowiedzieć się więcej na temat automatyzacji, zobacz [Konfigurowanie automatyzacji — partnerzy WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Łączność za pośrednictwem partnerów preferowane

Jeśli partner urządzenia gałęzi nie znajduje się w dalszej części tego artykułu, skontaktuj się z dostawcą urządzenia gałęzi i je skontaktuj się z nami, wysyłając wiadomość e-mail na adres azurevirtualwan@microsoft.com.

Można sprawdzić, poniższe łącza, aby uzyskać więcej informacji o usługach oferowanych przez partnerów preferowany. 

|Preferowany partnerów|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Technologia riverbed](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[Technologia 128](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wirtualnych sieci WAN, zobacz [wirtualne sieci WAN — często zadawane pytania](virtual-wan-faq.md).

Aby uzyskać więcej informacji o tym, jak zautomatyzować połączenie WAN wirtualnych platformy Azure, zobacz [wirtualne sieci WAN partnerów — jak zautomatyzować](virtual-wan-configure-automation-providers.md).
