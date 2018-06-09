---
title: Wymusić uwierzytelnianie wieloskładnikowe Azure w zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: Ten dokument zawiera opis sposobu włączyć uwierzytelnianie wieloskładnikowe dla zasobów usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b4f3fb0b25787711294d09faf65c2856d57709bc
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234417"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Wymusić uwierzytelnianie wieloskładnikowe Azure w zasobów platformy Azure przy użyciu Privileged Identity Management

Uprzywilejowane tożsamości zarządzania (PIM) dla ról zasobów platformy Azure umożliwia zasobów Administratorzy i Administratorzy tożsamości chronić krytyczne infrastruktury platformy Azure z wiązaniem czasu członkostwa i dostępu just in time. Ponadto PIM zapewnia opcjonalne wymuszania uwierzytelnianie wieloskładnikowe Azure dla dwóch różnych scenariuszy.

## <a name="require-multi-factor-authentication-to-activate"></a>Wymagaj uwierzytelniania wieloskładnikowego aktywować

Administratorzy zasobów mogą wymagać kwalifikujących się członkami roli można uruchomić usługi Azure Multi-Factor Authentication, aby aktywować. Ten proces zapewnia, że użytkownik, który żąda jest aktywacji, który mówią, że są one z pewnością uzasadnione. Wymuszania tej opcji chroni kluczowych zasobów w sytuacjach, gdy konto użytkownika może zostały naruszone. 

Aby wymusić to wymaganie, zaznacz zasób z listy zarządzanych zasobów. Z [pulpitu nawigacyjnego przeglądu](pim-resource-roles-overview-dashboards.md), wybierz rolę z listy ról w prawej dolnej części ekranu.

Ponadto można pobrać ustawień ról z albo **ról** lub **ustawienia roli** karty w okienku po lewej stronie.

>[!Note]
>Jeśli widzisz Baner w górnej części strony, który stanowi "Masz odpowiednie role, które można aktywować" Opcje w okienku po lewej stronie są wygaszone, nie jesteś użytkownikiem z prawami administratora. Oznacza to, należy najpierw [aktywować](pim-resource-roles-activate-your-roles.md) przed kontynuowaniem.

!["Role" i "Ustawienia roli" karty ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Zaznacz, aby wyświetlić członkostwa roli **ustawienia roli** na pasku w górnej części ekranu, aby otworzyć **szczegóły ustawień roli**.

Aby zmodyfikować ustawienia ról, wybierz opcję **Edytuj** na górze.

W sekcji obszarze **Aktywuj**, zaznacz pole wyboru, aby **wymagają uwierzytelniania wieloskładnikowego na aktywacji**. Następnie wybierz pozycję **Zapisz**.

![Wymagaj usługi Multi-Factor Authentication przy uaktywnianiu](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Wymagaj uwierzytelniania wieloskładnikowego na przypisanie

W niektórych przypadkach administrator zasobów może być przypisanie członka do roli na krótki czas trwania (jeden dzień, na przykład). W takim przypadku nie muszą przypisanej członków do żądania aktywacji. W tym scenariuszu PIM nie może wymusić uwierzytelnianie wieloskładnikowe, gdy element członkowski używa przypisania roli, ponieważ są one już aktywny w roli w czasie, które są przypisane.

Do zapewnienia, że administrator zasobów spełniających przypisania kto mówią, że są one, można wymusić uwierzytelnianie wieloskładnikowe na przypisanie.

Z tej samej roli ustawienie ekran szczegółów zaznacz pole w celu **wymagają uwierzytelniania wieloskładnikowego na bezpośrednie przypisywanie**.

![Wymagaj uwierzytelniania wieloskładnikowego na bezpośrednie przypisywanie](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Kolejne kroki

[Wymagaj zatwierdzenia aktywacji](pim-resource-roles-approval-workflow.md)

[Korzystanie z dziennika inspekcji](pim-resource-roles-use-the-audit-log.md)



