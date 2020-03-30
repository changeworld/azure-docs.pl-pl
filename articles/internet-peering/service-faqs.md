---
title: Usługa komunikacji równorzędnej — często zadawane pytania
titleSuffix: Azure
description: Usługa komunikacji równorzędnej — często zadawane pytania
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775474"
---
# <a name="peering-service---faqs"></a>Usługa komunikacji równorzędnej — często zadawane pytania

Możesz przejrzeć poniższe informacje w przypadku pytań ogólnych.

**Czy operator korzystający z istniejącej komunikacji bezpośredniej w komunikacji równorzędnej z firmą Microsoft może obsługiwać usługę komunikacji równorzędnej?**

Tak, przewoźnik może wykorzystać istniejące pni do obsługi usługi komunikacji równorzędnej. PNI usługi komunikacji równorzędnej wymaga różnorodności do obsługi ha. Jeśli istniejąca PNI ma już różnorodność, nie jest wymagana żadna nowa infrastruktura. Jeśli istniejąca PNI potrzebuje różnorodności, można ją rozszerzyć.

**Czy operator może używać nowej komunikacji bezpośredniej komunikacji równorzędnej z firmą Microsoft do obsługi usługi komunikacji równorzędnej?**

Tak, jest to również możliwe. Firma Microsoft będzie współpracować z usługą Carrier w celu utworzenia nowej komunikacji równorzędnej bezpośredniej w celu obsługi usługi komunikacji równorzędnej.  

**Dlaczego bezpośrednie równorzędne wymaga obsługi usługi komunikacji równorzędnej?**

Jednym z podstawowych sterowników usługi komunikacji równorzędnej jest zapewnienie łączności z usługami online firmy Microsoft za pośrednictwem dobrze połączonej usługi SP. PNI są zawsze w zakresie Gb/s, a tym samym podstawowym elementem konstrukcyjnym dla łączności o wysokiej przepustowości między operatorem a firmą Microsoft.

**Jakie są wymagania dotyczące różnorodności w bezpośrednim komunikacji równorzędnej do obsługi usługi komunikacji równorzędnej?**

PNI musi obsługiwać nadmiarowość lokalną i nadmiarowość geograficzną. Nadmiarowość lokalna jest zdefiniowana jako dwa zróżnicowane ścieżki w określonej lokacji komunikacji równorzędnej. Nadmiarowość geograficzna wymaga, aby firma Carrier ma dodatkową łączność w innej lokacji brzegowej firmy Microsoft w przypadku awarii lokacji głównej. W przypadku krótkiego czasu trwania awarii przewoźnik może kierować ruch przez witrynę kopii zapasowej.

**Przewoźnik oferuje już SLA i Internet klasy korporacyjnej, jak różni się ta oferta?**

Niektórzy przewoźnicy oferują umowy SLA i internet klasy korporacyjnej ze strony sieci. W usłudze komunikacji równorzędnej firma Microsoft zaoferuje usługę SLA oferującą ruch w części sieci Firmy Microsoft. Wybierając peering service klient otrzyma end-to-end umowy SLA. Umowy SLA z ich witryny do firmy Microsoft edge w sieci usługodawcy mogą być objęte przez usługodawcę. La w microsoft global network od microsoft krawędzi do użytkowników końcowych aplikacji jest teraz objęte przez firmę Microsoft.

**Jeśli dostawca usług już równorzędne z microsoft przy użyciu PNI to jakie zmiany są wymagane do obsługi usługi komunikacji równorzędnej?**

* Zmiany w oprogramowaniu w celu zidentyfikowania użytkownika usługi komunikacji równorzędnej i jego ruchu. Może wymagać zmian zasad routingu w celu wymiany ruchu użytkownika w najbliższej krawędzi firmy Microsoft za pośrednictwem połączenia usługi komunikacji równorzędnej.
* Upewnij się, że łączność ma nadmiarowość lokalną i nadmiarowość geograficzną.
