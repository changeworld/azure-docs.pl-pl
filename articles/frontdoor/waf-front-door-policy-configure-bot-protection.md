---
title: Konfigurowanie ochrony bot dla zapory aplikacji sieci Web za pomocą platformy Azure — drzwi (wersja zapoznawcza)
description: Poznaj zaporę aplikacji sieci Web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846355"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurowanie ochrony bot dla zapory aplikacji sieci Web (wersja zapoznawcza)
W tym artykule opisano sposób konfigurowania reguły ochrony bot w zaporze aplikacji sieci Web platformy Azure (WAF) dla drzwi zewnętrznych przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell lub szablonu Azure Resource Manager.

Zarządzany zestaw reguł ochrony bot można włączyć dla WAF, aby wykonywał niestandardowe akcje na żądaniach ze znanych złośliwych adresów IP. Adresy IP pochodzą ze źródła analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz podstawowe zasady WAF dla drzwi przednich, postępując zgodnie z instrukcjami opisanymi w artykule [Tworzenie zasad WAF dla drzwi platformy Azure z użyciem Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Włącz zestaw reguł ochrony bot

1. Na stronie zasady podstawowe, która została utworzona w poprzedniej sekcji, w obszarze **Ustawienia**kliknij pozycję **reguły**.
2. Na stronie Szczegóły w sekcji **Zarządzanie regułami** z menu rozwijanego zaznacz pole wyboru przed regułą **BotProtection-Preview-0,1**, a następnie wybierz pozycję **Zapisz** powyżej.
    
   ![Reguła ochrony bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować WAF](waf-front-door-monitor.md).
