---
title: Omówienie ochrony botów bramy aplikacji platformy Azure
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web (WAF) w ochronie botów bramy aplikacji
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027097"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Omówienie ochrony botów aplikacji sieci Web platformy Azure na portalu aplikacji Azure

Około 20% całego ruchu internetowego pochodzi ze złych botów. Robią takie czynności, jak skrobanie, skanowanie i szukanie luk w zabezpieczeniach aplikacji internetowej. Gdy te boty są zatrzymane w Zaporze aplikacji sieci Web (WAF), nie mogą cię zaatakować. Nie można również wykorzystać zasobów i usług, takich jak zaplecze i innej podstawowej infrastruktury.

Można włączyć zarządzany zestaw reguł ochrony botów dla waf, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP. Adresy IP pochodzą z źródła danych firmy Microsoft Analizy zagrożeń. Inteligentny wykres zabezpieczeń zasila analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Usługę Azure Security Center.

> [!IMPORTANT]
> Zestaw reguł ochrony bota jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Szczegółowe informacje można znaleźć w [dodatkowych warunkach użytkowania w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) platformy Microsoft Azure.

## <a name="use-with-owasp-rulesets"></a>Używanie z zestawy reguł OWASP

Można użyć zestawy reguł ochrony botów obok dowolnego z zestawy reguł OWASP (2.2.9, 3.0 i 3.1). W danym momencie można użyć tylko jednego pliku reguł OWASP. Bot protection ruleset zawiera dodatkową regułę, która pojawia się w jego własnym zbiorze reguł. Jest zatytułowany **Microsoft_BotManagerRuleSet_0.1**i można go włączyć lub wyłączyć, podobnie jak inne reguły OWASP.

![Bot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aktualizacja programu Ruleset

Lista reguł ograniczających zagrożenie dla botów znanych złych adresów IP jest aktualizowana wiele razy dziennie z kanału danych microsoft Threat Intelligence, aby pozostać zsynchronizowanym z botami. Twoje aplikacje internetowe są stale chronione, nawet w miarę zmiany wektorów ataku bota.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ochrony botów dla Zapory aplikacji sieci Web w usłudze Azure Application Gateway (wersja zapoznawcza)](bot-protection.md)
