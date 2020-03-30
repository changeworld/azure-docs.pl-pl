---
title: Wirtualna sieć WAN platformy Azure i praca zdalna
description: Na tej stronie opisano, jak można korzystać z wirtualnej sieci WAN platformy Azure, aby umożliwić pracę zdalną ze względu na pandemię COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337126"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Wirtualna sieć WAN platformy Azure i obsługa pracy zdalnej

>[!NOTE]
>W tym artykule opisano, jak można korzystać z wirtualnej sieci WAN platformy Azure, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do pracy zdalnej i łagodzenia problemów sieciowych, które występują z powodu kryzysu COVID-19.
>

Czy rozszyfrujesz, aby zapewnić łączność dla użytkowników zdalnych?
Czy nagle widzisz potrzebę wspierania przypływu użytkowników poza to, co zaplanowałeś?
Czy potrzebujesz użytkownika, aby połączyć się z domu i nie tylko uzyskać dostęp do chmury, ale także mieć możliwość dotarcia do lokalnego?
Czy potrzebujesz zdalnych użytkowników, aby móc dotrzeć do zasobów za prywatną siecią WAN?
Czy użytkownicy muszą uzyskiwać dostęp do zasobów wewnątrz chmury bez konieczności konfigurowania łączności między regionami?
Ponieważ ta globalna pandemia powoduje bezprecedensowe zmiany wokół nas, zespół wirtualnej sieci WAN platformy Azure jest tutaj, aby zaspokoić potrzeby związane z łącznością.

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która łączy wiele funkcji sieci, zabezpieczeń i routingu w celu zapewnienia jednego interfejsu operacyjnego. Funkcje te obejmują łączność oddziałową (poprzez automatyzację łączności z urządzeń wirtualnego partnera WAN, takich jak SD-WAN lub VPN CPE), łączność sieci VPN między lokacjami, łączność sieci VPN z użytkownikami zdalnymi (point-to-site), łączność prywatna (ExpressRoute), Łączność wewnątrz chmury (łączność przechodnia dla sieci wirtualnych), interconnectivity VPN ExpressRoute, routing, zapora platformy Azure, szyfrowanie dla łączności prywatnej itp. Nie musisz mieć wszystkie te przypadki użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz rozpocząć pracę tylko z jednym przypadkiem użycia i dostosować sieć w miarę jej rozwoju.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Teraz mówiąc o zdalnych użytkowników, pozwala spojrzeć na to, co trzeba, aby sieć i działa:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Konfigurowanie łączności użytkownika zdalnego

Możesz połączyć się z zasobami na platformie Azure za pomocą połączenia IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN dla użytkownika zdalnego. Ten klient może być [klientem sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554) lub klientem OpenVPN lub dowolnym klientem obsługującym protokół IKEv2. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Łączność z użytkownikiem zdalnym do lokalnego

Masz tu dwie opcje:

* Skonfiguruj łączność lokacja z dowolnym istniejącym urządzeniem sieci VPN. Po podłączeniu urządzenia sieci VPN IPsec do centrum wirtualnej sieci WAN platformy Azure wzajemne połączenia między siecią VPN użytkownika typu "punkt-lokacja" (użytkownik zdalny) a siecią VPN między lokacjami jest automatyczne. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN między lokacjami z lokalnego urządzenia sieci VPN na wirtualną sieć WAN platformy Azure, zobacz [Tworzenie połączenia lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

* Podłącz obwód usługi ExpressRoute do koncentratora wirtualnej sieci WAN. Podłączanie obwodu usługi ExpressRoute wymaga wdrożenia bramy usługi ExpressRoute w wirtualnej sieci WAN. Po wdrożeniu jednego z nich wzajemne połączenia między siecią VPN użytkownika typu "punkt-lokacja" a użytkownikiem usługi ExpressRoute są automatyczne. Aby utworzyć połączenie usługi ExpressRoute, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md). Do nawiązania połączenia z wirtualną siecią WAN platformy Azure można użyć istniejącego obwodu usługi ExpressRoute.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Istniejący podstawowy klient wirtualnej sieci WAN

Podstawowa wirtualna sieć WAN zapewnia tylko sieć VPN typu lokacja lokacja. Aby użytkownicy zdalni mogli się połączyć, należy uaktualnić wirtualną sieć WAN do standardowej wirtualnej sieci WAN. Aby uzyskać instrukcje uaktualnienia wirtualnej sieci WAN, zobacz [Uaktualnianie wirtualnej sieci WAN z podstawowej do standardowej](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Dodatkowe informacje

Wirtualna sieć WAN obsługuje jeden koncentrator na region/lokalizację. Aby uzyskać informacje o lokalizacji, zobacz [wirtualnych partnerów sieci WAN i lokalizacje](virtual-wan-locations-partners.md) artykułu. Każdy koncentrator obsługuje do 10 000 połączeń zdalnych użytkowników, 1000 połączeń w odgałęzieniach, cztery obwody usługi ExpressRoute i do 500 połączeń z siecią wirtualną. Podczas skalowania użytkowników zdalnych, jeśli masz jakieś pytania, nie wahaj się azurevirtualwan@microsoft.comszukać pomocy, wysyłając wiadomość e-mail na adres . Jeśli potrzebujesz pomocy technicznej, pamiętaj, aby otworzyć bilet pomocy technicznej z witryny Azure portal i pomoc będzie w drodze.

## <a name="faq"></a><a name="faq"></a>Najczęściej zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [omówienie wirtualnej sieci WAN](virtual-wan-about.md)