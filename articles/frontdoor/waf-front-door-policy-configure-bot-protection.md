---
title: Skonfiguruj ochronę bot zapory aplikacji sieci web za pomocą usługi Azure drzwiami frontowymi (wersja zapoznawcza)
description: Dowiedz się, zapory aplikacji sieci web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481627"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Skonfiguruj ochronę bot zapory aplikacji sieci web (wersja zapoznawcza)
W tym artykule pokazano, jak skonfigurować reguły ochrony bota w zapory aplikacji sieci web platformy Azure (WAF) dla drzwiami frontowymi przy użyciu szablonu z wiersza polecenia platformy Azure, programu Azure PowerShell lub usługi Azure Resource Manager.

Zestaw reguł ochrony zarządzanych Bot można włączyć dla zapory aplikacji internetowych do wykonania akcji niestandardowych dla żądań ze znanymi złośliwymi adresami IP. Adresy IP pochodzą z analizy zagrożeń firmy Microsoft, źródła danych. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) uprawnienia analizy zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym usługi Azure Security Center.

> [!IMPORTANT]
> Zestaw reguł ochrony BOT jest obecnie dostępna w publicznej wersji zapoznawczej i jest dostarczana z wersji zapoznawczej Umowa dotycząca poziomu usług. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Tworzenie podstawowych zasad zapory aplikacji sieci Web dla drzwiami frontowymi, wykonując instrukcje opisane w [utworzyć zasady zapory aplikacji sieci Web Azure drzwiami frontowymi przy użyciu witryny Azure portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Włącz zestaw reguł ochrony bot

1. Na stronie podstawowych zasad, który został utworzony w poprzedniej sekcji, w obszarze **ustawienia**, kliknij przycisk **reguły**.
2. Na stronie szczegółów w obszarze **zarządzanie regułami** sekcji z menu rozwijanego, zaznacz pole wyboru przed reguły **BotProtection — wersja zapoznawcza — 0,1**, a następnie wybierz pozycję **Zapisz**powyżej.
    
   ![Reguły ochrony BOT](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Monitorowanie zapory aplikacji sieci Web](waf-front-door-monitor.md).
