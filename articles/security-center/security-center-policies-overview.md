---
title: Ustawienia usługi Azure Security Center | Dokumentacja firmy Microsoft
description: Konfigurowanie ustawień usługi Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 2aa4545aa79260bea792392e1bebf4166253fc87
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52315719"
---
# <a name="security-center-settings"></a>Ustawienia usługi Security Center
Ten artykuł zawiera omówienie ustawienia w usłudze Security Center.

W obszarze zasady zabezpieczeń można osiągnąć następujące ustawienia:

- **Zbieranie danych**: Określa aprowizacji agenta i [zbierania danych](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) ustawienia.
- **Zasady zabezpieczeń**: Określa, który kontroluje monitorów Centrum zabezpieczeń i zaleca zastosowanie. Możesz edytować [zasady zabezpieczeń](security-center-azure-policy.md) w usłudze Security Center. Można również użyć [usługi Azure Policy](security-center-azure-policy.md) do utworzenia nowych definicji, zdefiniowania dodatkowych zasad i przypisywania zasad w grupach zarządzania. 
- **Wiadomości e-mail z powiadomieniami**: Określa kontakty zabezpieczeń i [powiadomienia e-mail](security-center-provide-security-contact-details.md) ustawienia.
- **Warstwa cenowa**: definiuje bezpłatna lub standardowa [wybór cen](security-center-pricing.md). Wybrana warstwa określa, które funkcje usługi Security Center są dostępne dla zasobów w zakresie. Można określić warstwę dla subskrypcji, grupy zasobów i obszarów roboczych.

> [!NOTE]
> Możesz ustawić wszystkie te na subskrypcję. W przypadku obszarów roboczych można ustawić tylko zbierania danych i warstwa cenowa. Dla grup zasobów można ustawić tylko warstwa cenowa.
>


## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Usługa Security Center korzysta z opartej na rolach kontrola dostępu (RBAC), który zapewnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Po otwarciu Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, które mają dostęp do. Oznacza to, że użytkownicy są przypisani do roli z *właściciela*, *Współautor*, lub *czytnika* do subskrypcji lub grupy zasobów, której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- **Czytelnik zabezpieczeń**: mają widoku praw do usługi Security Center, który zawiera zalecenia, alerty, zasady i kondycję, ale one nie może wprowadzać zmian.
- **Administrator zabezpieczeń**: mają te same prawa widok jako *Czytelnik zabezpieczeń*, i mogą również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.


## <a name="next-steps"></a>Kolejne kroki
W tym artykule omówiono zasady zabezpieczeń w usłudze Azure Security Center. Aby dowiedzieć się więcej o usłudze Azure Security Center, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md): informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center](security-center-recommendations.md): Dowiedz się, jak zaleceń usługi Security Center pomóc w ochronie Twoich zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
- [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md): Dowiedz się, jak usługa Security Center zarządza i zabezpiecza dane.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Uzyskaj najnowsze informacje o zabezpieczeniach platformy Azure i informacji.
