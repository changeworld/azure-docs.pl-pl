---
title: Konfigurowanie ochrony botów dla zapory aplikacji sieci Web platformy Azure (WAF)
description: Dowiedz się, jak skonfigurować ochronę botów dla Zapory aplikacji sieci Web (WAF) w usłudze Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516866"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Konfigurowanie ochrony botów dla Zapory aplikacji sieci Web w usłudze Azure Application Gateway (wersja zapoznawcza)

W tym artykule pokazano, jak skonfigurować regułę ochrony bota w zaporze aplikacji sieci Web (WAF) platformy Azure dla bramy aplikacji przy użyciu portalu Azure. 

Można włączyć zarządzany zestaw reguł ochrony botów dla waf, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP. Adresy IP pochodzą z źródła danych firmy Microsoft Analizy zagrożeń. Inteligentny wykres zabezpieczeń zasila analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Usługę Azure Security Center.

> [!NOTE]
> Zestaw reguł ochrony bota jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Szczegółowe informacje można znaleźć w [dodatkowych warunkach użytkowania w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) platformy Microsoft Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz podstawowe zasady WAF dla bramy aplikacji, postępując zgodnie z instrukcjami opisanymi w [temat Tworzenie zasad zapory aplikacji sieci Web dla bramy aplikacji](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Włączanie zestawu reguł ochrony botów

1. Na **utworzonej** wcześniej stronie Zasad podstawowych w obszarze **Ustawienia**wybierz pozycję **Reguły**.  

2. Na stronie szczegółów w sekcji **Zarządzanie regułami** z menu rozwijanego zaznacz pole wyboru reguły ochrony bota, a następnie wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Ochrona przed botami](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat reguł niestandardowych, zobacz [Reguły niestandardowe zapory aplikacji sieci Web w wersji 2 w usłudze Azure Application Gateway](custom-waf-rules-overview.md).