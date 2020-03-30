---
title: 'Azure ExpressRoute: Łączenie się z usługą Microsoft Cloud przy użyciu globalnego zasięgu'
description: W tym artykule wyjaśniono globalny zasięg usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538508"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Usługa ExpressRoute to prywatny i odporny sposób łączenia sieci lokalnych z usługą Microsoft Cloud. Dostęp do wielu usług w chmurze firmy Microsoft, takich jak Azure i Office 365, można uzyskać z prywatnego centrum danych lub sieci firmowej. Na przykład w San Francisco może znajdować się oddział z obwodem usługi ExpressRoute w Dolinie Krzemowej i innym oddziałem w Londynie z obwodem usługi ExpressRoute w tym samym mieście. Oba oddziały mogą mieć szybką łączność z zasobami platformy Azure w stanach Zachodnie i Południowe Wielkiej Brytanii. Jednak oddziały nie mogą wymieniać danych bezpośrednio ze sobą. Innymi słowy, 10.0.1.0/24 może wysyłać dane do 10.0.3.0/24 i 10.0.4.0/24, ale NIE do 10.0.2.0/24.

![Bez][1]

Dzięki **układzie Globalny zasięg usługi ExpressRoute**można łączyć obwody usługi ExpressRoute, aby utworzyć sieć prywatną między sieciami lokalnymi. W powyższym przykładzie wraz z dodatkiem usługi ExpressRoute Global Reach biuro w San Francisco (10.0.1.0/24) może bezpośrednio wymieniać dane z biurem w Londynie (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i za pośrednictwem globalnej sieci firmy Microsoft. 

![Z][2]

## <a name="use-case"></a>Przypadek użycia
Usługa ExpressRoute Global Reach została zaprojektowana w celu uzupełnienia implementacji sieci WAN dostawcy usług i połączenia oddziałów na całym świecie. Na przykład, jeśli usługodawca działa głównie w Stanach Zjednoczonych i połączył wszystkie twoje oddziały w USA, ale dostawca usług nie działa w Japonii i Hong Kongu, z usługą ExpressRoute Global Reach możesz współpracować z lokalnym dostawcą usług i Firma Microsoft połączy tam twoje oddziały z oddziałami w Stanach Zjednoczonych za pomocą usługi ExpressRoute i naszej globalnej sieci.

![przypadek użycia][3]

## <a name="availability"></a>Dostępność 
Usługa ExpressRoute Global Reach jest obecnie obsługiwana w następujących miejscach.

* Australia
* Kanada
* Francja
* Niemcy
* SRA Hongkong
* Irlandia
* Japonia
* Korea
* Holandia
* Nowa Zelandia
* Singapur
* Szwajcaria
* Wielka Brytania
* Stany Zjednoczone

Obwody usługi ExpressRoute muszą być tworzone w [lokalizacjach komunikacji równorzędnej usługi ExpressRoute](expressroute-locations.md) w powyższych krajach lub regionach. Aby włączyć globalny zasięg usługi ExpressRoute między [różnymi regionami geopolitycznymi,](expressroute-locations.md)obwody muszą być jednostkami SKU premium.

## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o globalnym zasięgu usługi ExpressRoute](expressroute-faqs.md)
2. [Jak włączyć globalny zasięg usługi ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Łącze obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram bez globalnego zasięgu"
[2]: ./media/expressroute-global-reach/2.png "diagram z globalnym zasięgiem"
[3]: ./media/expressroute-global-reach/3.png "przypadek użycia globalnego zasięgu"
