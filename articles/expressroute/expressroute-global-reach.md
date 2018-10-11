---
title: O zasięgu globalnym usługi ExpressRoute | Dokumentacja firmy Microsoft
description: W tym artykule opisano nowości zasięgu globalnym usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: c96841274f73be766b0157f036746599cc836dd3
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068047"
---
# <a name="expressroute-global-reach-preview"></a>Globalny zasięg usługi ExpressRoute (wersja zapoznawcza)
Usługa ExpressRoute jest prywatny i odporne na błędy sposób łączenia sieci lokalnej Microsoft Cloud. Wiele usług w chmurze firmy Microsoft, takich jak Azure, Office 365 i Dynamics 365 dostęp z Twojego Centrum danych prywatnych lub sieci firmowej. Na przykład masz oddziału w San Francisco z obwodem usługi ExpressRoute w Dolinie Krzemowej i innego oddziału firmy w Londynie z obwodem usługi ExpressRoute, w tym samym mieście. Obu biur oddziałów mogą mieć łączność o dużej szybkości do zasobów platformy Azure w zachodnich stanach USA i Południowe Zjednoczone Królestwo. Jednak oddziałów nie wymiany danych bezpośrednio ze sobą. Innymi słowy 10.0.1.0/24 może wysyłać dane do 10.0.3.0/24 i 10.0.4.0/24, ale nie do 10.0.2.0/24.

![Bez][1]

Za pomocą **zasięgu globalnym ExpressRoute**, możesz połączyć obwodów usługi ExpressRoute razem w celu zapewnienia sieci prywatnej od sieci w środowisku lokalnym. W powyższym przykładzie, dodając ExpressRoute zasięgu globalnym San Francisco pakietu office (10.0.1.0/24) mogą bezpośrednio wymiany danych z biura Londyn (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i za pośrednictwem globalnej sieci firmy Microsoft. 

![za pomocą][2]

## <a name="use-case"></a>Przypadek użycia
ExpressRoute zasięgu globalnym zaprojektowano w celu uzupełnienia WAN implementacji dostawcy usług i połączyć z biur oddziałów na całym świecie. Na przykład, jeśli usługodawca głównie działa w Stanach Zjednoczonych i połączył wszystkie gałęzie w Stanach Zjednoczonych, ale dostawca usług nie działają w Japonii i Hongkong SAR, o zasięgu globalnym usługi ExpressRoute można pracować dostawcę usług lokalnych i Microsoft będzie łączyć się z gałęziami do tych w Stanach Zjednoczonych, przy użyciu usługi ExpressRoute i Nasza globalna sieć.

![Przypadek użycia][3]

## <a name="availability"></a>Dostępność 
Zasięgu globalnym usługi ExpressRoute jest obecnie obsługiwane w następujących miejscach.

* Stany Zjednoczone
* Wielka Brytania 
* Irlandia
* Holandia
* Australia
* Hongkong 
* Japonia

Utworzono obwodów usługi ExpressRoute [lokalizacje komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md) powyżej krajów lub regionów. Aby włączyć usługi ExpressRoute zasięgu globalnym między [różnych regionów geopolitycznych](expressroute-locations.md), obwodów usługi musi być jednostki SKU Premium.

## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o zasięgu globalnym usługi ExpressRoute](expressroute-faqs.md)
2. [Jak włączyć zasięgu globalnym usługi ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Połącz obwód usługi ExpressRoute z sieci wirtualnej platformy Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagram bez zasięgu globalnym"
[2]: ./media/expressroute-global-reach/2.png "Diagram o zasięgu globalnym"
[3]: ./media/expressroute-global-reach/3.png "Użyj przypadków globalny zasięg"
