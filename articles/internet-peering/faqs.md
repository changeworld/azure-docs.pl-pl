---
title: Komunikacja między internetem — często zadawane pytania
titleSuffix: Azure
description: Komunikacja między internetem — często zadawane pytania
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775500"
---
# <a name="internet-peering---faqs"></a>Komunikacja między internetem — często zadawane pytania

Możesz przejrzeć poniższe informacje w przypadku pytań ogólnych.

**Jaka jest różnica między komunikacją równorzędną w Internecie a usługą komunikacji równorzędnej?**

Usługa komunikacji równorzędnej to usługa, która ma na celu zapewnienie łączności publiczną ip klasy korporacyjnej dla klientów korporacyjnych. Internet klasy korporacyjnej obejmuje łączność za pośrednictwem dostawców usług internetowych, którzy mają wysoką przepustowość łączności z firmą Microsoft i nadmiarowości dla łączności wysokiej dostępności. Ponadto ruch użytkownika jest zoptymalizowany pod kątem opóźnienia do najbliższej przeglądarki Microsoft Edge. Usługa komunikacji równorzędnej opiera się na komunikacji równorzędnej z operatorem partnerskim. Łączność komunikacji równorzędnej z partnerem musi być direct komunikacji równorzędnej, w przeciwieństwie do komunikacji równorzędnej programu Exchange. Bezpośrednia komunikacja równorzędna musi mieć nadmiarowość lokalną i geograficzną.

**Co to jest starsza komunikacja równorzędna?**

Połączenie komunikacji równorzędnej skonfigurowane przy użyciu programu Azure PowerShell jest zarządzane jako zasób platformy Azure. Połączenia komunikacji równorzędnej skonfigurowane w przeszłości są przechowywane w naszym systemie jako starsze komunikacji równorzędnej, które można przekonwertować do zarządzania jako zasób platformy Azure.

**Kiedy wywoływany jest New-AzPeeringDirectConnectionObject, jakie adresy IP są podawane do urządzeń Microsoft i Peer?**

Podczas wywoływania polecenia cmdlet New-AzPeeringDirectConnectionObject wprowadzany jest adres /31 (a.b.c.d/31) lub /30 (a.b.c.d/30). Pierwszy adres IP (a.b.c.d+0) jest przekazytrym urządzenia Peer, a drugi adres IP (a.b.c.d+1) jest podawany urządzeniu firmy Microsoft.

**Co to są parametry MaxPrefixesAdvertisedIPv4 i MaxPrefixesAdvertisedIPv6 w połączeniu cmdlet New-AzPeeringDirectConnectionObject?**

MaxPrefixesAdvertisedIPv4 i MaxPrefixesAdvertisedIPv6 parametry reprezentują maksymalną liczbę prefiksów IPv4 i IPv6 peer chce Microsoft zaakceptować. Parametry te mogą być modyfikowane w dowolnym momencie.