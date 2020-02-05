---
title: WAF na temat usługi Azure Application Gateway bot Protection — Omówienie
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web (WAF) na Application Gateway bot Protection
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027097"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Omówienie funkcji Zapora aplikacji sieci Web platformy Azure w usłudze Azure Application Gateway bot Protection

Około 20% całego ruchu internetowego pochodzi z nieprawidłowych botów. Mogą to być takie jak braki, skanowanie i wyszukiwanie luk w zabezpieczeniach aplikacji sieci Web. Gdy te botów są zatrzymane w zaporze aplikacji sieci Web (WAF), nie mogą się z nią zaatakować. Nie mogą oni również używać zasobów i usług, takich jak frontony i inna podstawowa infrastruktura.

Możesz włączyć zarządzaną regułę ochrony bot dla WAF, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP. Adresy IP pochodzą ze źródła analizy zagrożeń firmy Microsoft. Rozwiązanie Intelligent Security Graph obsługuje analizę zagrożeń firmy Microsoft i jest używane przez wiele usług, w tym usługę Azure Security Center.

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure .

## <a name="use-with-owasp-rulesets"></a>Używanie z zestawami reguł OWASP

Można użyć zestawu reguł ochrony bot wraz z dowolnym zestawem reguł OWASP (2.2.9, 3,0 i 3,1). W danym momencie może być użyty tylko jeden zestaw reguł OWASP. Zestaw reguł bot Protection zawiera dodatkową regułę, która pojawia się we własnym zestawie reguł. Jest ona zatytułowana **Microsoft_BotManagerRuleSet_0 dziesiąta**i można ją włączyć lub wyłączyć, podobnie jak w przypadku innych reguł OWASP.

![Zestaw reguł bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aktualizacja zestawu reguł

Zestaw reguł łagodzenia boty dla znanych nieprawidłowych adresów IP aktualizuje wiele razy dziennie z kanału informacyjnego analizy zagrożeń firmy Microsoft, aby zachować synchronizację z botów. Aplikacje sieci Web są stale chronione nawet w przypadku zmiany wektorów ataków bot.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ochrony bot dla zapory aplikacji sieci Web w usłudze Azure Application Gateway (wersja zapoznawcza)](bot-protection.md)
