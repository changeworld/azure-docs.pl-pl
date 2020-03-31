---
title: Konfigurowanie ochrony botów dla Zapory aplikacji sieci Web za pomocą drzwi ami frontowymi platformy Azure (wersja zapoznawcza)
description: Dowiedz się Zapora aplikacji sieci Web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934647"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurowanie ochrony botów dla Zapory aplikacji sieci Web (wersja zapoznawcza)
W tym artykule pokazano, jak skonfigurować regułę ochrony botów w zaporze aplikacji sieci Web platformy Azure (WAF) dla drzwi y frontowej przy użyciu witryny Azure portal. Regułę ochrony bota można również skonfigurować przy użyciu szablonu interfejsu wiersza polecenia, programu Azure PowerShell lub usługi Azure Resource Manager.

> [!IMPORTANT]
> Zestaw reguł ochrony bota jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz podstawowe zasady WAF dla drzwi frontowych, postępując zgodnie z instrukcjami opisanymi w [obszarze Tworzenie zasad WAF dla drzwi ami frontowymi platformy Azure przy użyciu portalu Azure.](waf-front-door-create-portal.md)

## <a name="enable-bot-protection-rule-set"></a>Włączanie zestawu reguł ochrony botów

Na stronie **Reguły zarządzane** podczas tworzenia zasad zapory aplikacji sieci Web najpierw znajdź sekcję **Zestaw reguł zarządzanych,** zaznacz pole wyboru przed regułą **Microsoft_BotManager_1.0** z menu rozwijanego, a następnie wybierz polecenie **Recenzja + Utwórz**.

   ![Reguła ochrony botów](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować waf](waf-front-door-monitor.md).
