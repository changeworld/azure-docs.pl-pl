---
title: Privileged Identity Management zasobów Azure - MFA | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu włączyć uwierzytelnianie wieloskładnikowe dla zasobów usługi PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - role zasobów - MFA

PIM ról zasobów platformy Azure umożliwia zasobów Administratorzy i Administratorzy tożsamości chronić krytyczne infrastruktury platformy Azure z wiązaniem czasu członkostwa i dostępu just in time. Ponadto PIM zapewnia wymuszanie opcjonalne Azure Multi-Factor Authentication (MFA) dla dwóch różnych scenariuszy.

## <a name="require-mfa-to-activate"></a>Wymagane uwierzytelnianie MFA do aktywacji

Administratorzy zasobów mogą wymagać kwalifikujących się członkami roli kończą się pomyślnie usługi Azure MFA aktywować. Ten proces zapewnia, że jest aktywacja żądania użytkownika, który mówią, że są one z pewnością uzasadnione. Wymuszanie tej opcji chroni kluczowych zasobów w sytuacjach, gdy konto użytkownika zostało naruszone. 

Aby wymusić to wymaganie, zaznacz zasób z listy zarządzanych zasobów. Z [pulpitu nawigacyjnego przeglądu](pim-resource-roles-overview-dashboards.md), wybierz rolę z listy ról w prawym dolnym rogu ekranu.

Ponadto można uzyskać ustawień roli z albo "role" lub "Ustawienia roli" karty w menu nawigacji po lewej stronie.

>[!Note]
>Jeśli są wygaszone opcje dostępne w menu nawigacji po lewej stronie, aby zobaczyć transparent w górnej części strony, który stanowi "Masz odpowiednie role, które można aktywować" nie są aktywne administratora, a musi [aktywować](pim-resource-roles-activate-your-roles.md) przed kontynuowaniem.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Jeśli wyświetlanie członkostwo roli, wybierz "Ustawienia roli" na pasku w górnej części ekranu, aby otworzyć "szczegóły ustawień roli".

Kliknij przycisk **Edytuj** u góry, aby zmodyfikować ustawienia roli.

W sekcji obszarze **Aktywuj**, kliknij pole wyboru, aby **wymagają uwierzytelniania wieloskładnikowego, aby aktywować**, następnie kliknij przycisk Zapisz.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Uwierzytelniania MFA można wymagać przydziału

W niektórych przypadkach administrator zasobów może wykonać przypisania członka do roli na krótki czas trwania (jeden dzień na przykład) i musi przypisanej członków do żądania aktywacji. W tym scenariuszu PIM nie może wymusić uwierzytelnianie MFA, gdy element członkowski używa przypisania roli, ponieważ są one już aktywny w roli w czasie, które są przypisane.

Aby zapewnić, że administrator zasobów spełniających przydziału jest kto mówią, że są one, można wymusić uwierzytelnianie MFA na przypisanie.

Z tej samej roli ustawienie ekran szczegółów zaznacz pole "Wymagaj uwierzytelniania wieloskładnikowego na przypisanie".

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Kolejne kroki

[Wymagaj zatwierdzenia aktywacji](pim-resource-roles-approval-workflow.md)

[Korzystanie z dziennika inspekcji](pim-resource-roles-use-the-audit-log.md)



