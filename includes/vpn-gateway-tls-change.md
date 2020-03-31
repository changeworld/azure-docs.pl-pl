---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183122"
---
Od 1 lipca 2018 r. z usługi Azure VPN Gateway zostanie usunięta obsługa techniczna protokołów TLS 1.0 i 1.1. Usługa VPN Gateway będzie obsługiwać tylko protokół TLS 1.2. Dotyczy to tylko połączeń typu punkt-lokacja; nie będzie to miało wpływu na połączenia między lokacjami. Jeśli używasz protokołu TLS dla sieci VPN typu lokacja na klientach systemu Windows 10, nie musisz podejmować żadnych działań. Jeśli używasz protokołu TLS do połączeń typu punkt-lokacja na klientach systemu Windows 7 i Windows 8, zapoznaj się z [często zadawanymi](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) pytaniami dotyczącymi bramy sieci VPN, aby uzyskać instrukcje aktualizacji.