---
title: 'Azure ExpressRoute: łączenie się z Microsoft Cloud przy użyciu Global Reach'
description: W tym artykule wyjaśniono, zasięgu globalnym usługi ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: cherylmc
ms.openlocfilehash: f574576044b7e4ddd34289b5cc45fe5ca353f180
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538508"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Usługa ExpressRoute jest prywatny i odporne na błędy sposób łączenia sieci lokalnej Microsoft Cloud. Możesz uzyskać dostęp do wielu usług w chmurze firmy Microsoft, takich jak Azure, i pakietu Office 365 z prywatnego centrum danych lub sieci firmowej. Na przykład masz oddziału w San Francisco z obwodem usługi ExpressRoute w Dolinie Krzemowej i innego oddziału firmy w Londynie z obwodem usługi ExpressRoute, w tym samym mieście. Obu biur oddziałów mogą mieć łączność o dużej szybkości do zasobów platformy Azure w zachodnich stanach USA i Południowe Zjednoczone Królestwo. Jednak oddziałów nie wymiany danych bezpośrednio ze sobą. Innymi słowy 10.0.1.0/24 może wysyłać dane do 10.0.3.0/24 i 10.0.4.0/24, ale nie do 10.0.2.0/24.

![konieczności][1]

Program **ExpressRoute Global REACH**umożliwia łączenie obwodów ExpressRoute ze sobą w celu naprowadzenia prywatnej sieci między sieciami lokalnymi. W powyższym przykładzie, dodając ExpressRoute zasięgu globalnym San Francisco pakietu office (10.0.1.0/24) mogą bezpośrednio wymiany danych z biura Londyn (10.0.2.0/24) za pośrednictwem istniejących obwodów usługi ExpressRoute i za pośrednictwem globalnej sieci firmy Microsoft. 

![się][2]

## <a name="use-case"></a>Przypadek użycia
ExpressRoute zasięgu globalnym zaprojektowano w celu uzupełnienia WAN implementacji dostawcy usług i połączyć z biur oddziałów na całym świecie. Na przykład, jeśli usługodawca głównie działa w Stanach Zjednoczonych i połączył wszystkie gałęzie w Stanach Zjednoczonych, ale dostawca usług nie działają w Japonii i Hongkong SAR, o zasięgu globalnym usługi ExpressRoute można pracować dostawcę usług lokalnych i Microsoft będzie łączyć się z gałęziami do tych w Stanach Zjednoczonych, przy użyciu usługi ExpressRoute i Nasza globalna sieć.

![przypadek użycia][3]

## <a name="availability"></a>Dostępność 
Zasięgu globalnym usługi ExpressRoute jest obecnie obsługiwane w następujących miejscach.

* Australia
* Kanada
* Francja
* Niemcy
* Hongkong
* Irlandia
* Japonia
* Korea
* Holandia
* Nowa Zelandia
* Singapur
* Szwajcaria
* Wielka Brytania
* Stany Zjednoczone

Obwody usługi ExpressRoute muszą zostać utworzone w [lokalizacjach komunikacji równorzędnej ExpressRoute](expressroute-locations.md) w powyższych krajach lub regionach. Aby umożliwić ExpressRoute Global Reach między [różnymi regionami geopolitycznych](expressroute-locations.md), obwody muszą być jednostkami SKU w warstwie Premium.

## <a name="next-steps"></a>Następne kroki
1. [Dowiedz się więcej o ExpressRoute Global Reach](expressroute-faqs.md)
2. [Jak włączyć Global Reach ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Łączenie obwodu usługi ExpressRoute z usługą Azure Virtual Network](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagram bez globalnego zasięgu"
[2]: ./media/expressroute-global-reach/2.png "Diagram z zasięgiem globalnym"
[3]: ./media/expressroute-global-reach/3.png "przypadek użycia globalnego zasięgu"
