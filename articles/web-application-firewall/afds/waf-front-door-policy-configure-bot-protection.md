---
title: Konfigurowanie ochrony bot dla zapory aplikacji sieci Web za pomocą platformy Azure — drzwi (wersja zapoznawcza)
description: Poznaj zaporę aplikacji sieci Web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512472"
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
    
   ![Reguła ochrony bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować WAF](waf-front-door-monitor.md).
