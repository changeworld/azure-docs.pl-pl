---
title: Ustawienia usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Konfigurowanie ustawień usługi Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703527"
---
# <a name="security-center-settings"></a>Ustawienia usługi Security Center
Ten artykuł zawiera omówienie ustawienia w usłudze Security Center.

W obszarze zasady zabezpieczeń można osiągnąć następujące ustawienia:

- **Zbieranie danych**: Określa aprowizacji agenta i [zbierania danych](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) ustawienia.
- **Zasady zabezpieczeń**: Określa, który kontroluje monitorów Centrum zabezpieczeń i zaleca zastosowanie. Możesz edytować [zasady zabezpieczeń](tutorial-security-policy.md) w usłudze Security Center. Można również użyć [usługi Azure Policy](tutorial-security-policy.md) do utworzenia nowych definicji, zdefiniowania dodatkowych zasad i przypisywania zasad w grupach zarządzania. 
- **Wiadomości e-mail z powiadomieniami**: Określa kontakty zabezpieczeń i [powiadomienia e-mail](security-center-provide-security-contact-details.md) ustawienia.
- **Warstwa cenowa**: Definiuje bezpłatna lub standardowa [wybór cen](security-center-pricing.md). Wybrana warstwa określa, które funkcje usługi Security Center są dostępne dla zasobów w zakresie. Można określić warstwę dla subskrypcji i obszarów roboczych.

> [!NOTE]
> Możesz ustawić wszystkie te na subskrypcję. W przypadku obszarów roboczych można ustawić tylko zbierania danych i warstwa cenowa.
>


## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Usługa Security Center korzysta z opartej na rolach kontrola dostępu (RBAC), który zapewnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Po otwarciu Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, które mają dostęp do. Oznacza to, że użytkownicy są przypisani do roli z *właściciela*, *Współautor*, lub *czytnika* subskrypcji zasób należy do. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- **Czytelnik zabezpieczeń**: Uprawnienia widok do usługi Security Center, który zawiera zalecenia, alerty, zasady i kondycję, ale nie mogą oni wprowadzić zmiany.
- **Administrator zabezpieczeń**: Te same prawa widok jako *Czytelnik zabezpieczeń*, i mogą również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule omówiono zasady zabezpieczeń w usłudze Azure Security Center. Aby dowiedzieć się więcej o usłudze Azure Security Center, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md): Informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md): Dowiedz się, jak zaleceń usługi Security Center pomóc w ochronie Twoich zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md): Dowiedz się, jak usługa Security Center zarządza i zabezpiecza dane.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.
