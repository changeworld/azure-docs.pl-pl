---
title: Wymusić uwierzytelnianie wieloskładnikowe systemu Azure w ramach zasobów platformy Azure przy użyciu Privileged Identity Management | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano sposób włączania uwierzytelniania wieloskładnikowego dla zasobów usługi PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 21f6fef214f27630ff0eadc39e1e26c9c344f353
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444736"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Wymusić uwierzytelnianie wieloskładnikowe systemu Azure w ramach zasobów platformy Azure przy użyciu Privileged Identity Management

Privileged Identity Management (PIM) dla ról zasobów platformy Azure umożliwia administratorom zasobów oraz administratorów tożsamości w celu ochrony krytycznych infrastruktury platformy Azure przy użyciu czasowo związanych przynależności i dostęp just in time. Ponadto PIM udostępnia opcjonalne wymuszania uwierzytelnianie wieloskładnikowe Azure dla dwóch różnych scenariuszy.

## <a name="require-multi-factor-authentication-to-activate"></a>Wymagaj uwierzytelniania wieloskładnikowego aktywować

Administratorom zasobów mogą wymagać uprawnionych członków roli do uruchamiania usługi Azure Multi-Factor Authentication, zanim użytkownik może dokonać aktywacji. Ten proces zapewnia, że użytkownik, który żąda aktywacji jest kto mówią, że są one z pewnością uzasadnione. Wymuszanie ta opcja chroni zasoby o znaczeniu krytycznym w sytuacjach, gdy konto użytkownika może być zabezpieczenia mogły zostać naruszone. 

Aby wymusić to wymaganie, wybierz zasób z listy zarządzanych zasobów. Z [pulpit nawigacyjny przeglądu](pim-resource-roles-overview-dashboards.md), wybierz rolę z listy ról w prawej dolnej części ekranu.

Ponadto możesz pobrać ustawień roli z albo **role** lub **ustawień roli** kart w okienku po lewej stronie.

>[!Note]
>Jeśli zostanie wyświetlony Baner w górnej części strony, z informacją "Masz kwalifikujące się role, które można aktywować" Opcje w okienku po lewej stronie są wygaszone, nie jesteś administratorem active. Oznacza to, należy najpierw [aktywować](pim-resource-roles-activate-your-roles.md) przed kontynuowaniem.

!["Role" i "Ustawienia roli" karty ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Aby wyświetlić członkostwa w roli, wybierz **ustawień roli** na pasku w górnej części ekranu, aby otworzyć **szczegóły ustawień roli**.

Aby zmodyfikować ustawienia roli, wybierz **Edytuj** znajdujący się u góry.

W sekcji w obszarze **Aktywuj**, zaznacz pole wyboru, aby **Wymagaj uwierzytelniania wieloskładnikowego aktywacji**. Następnie wybierz pozycję **Zapisz**.

![Wymagaj usługi Multi-Factor Authentication przy uaktywnianiu](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Wymagaj uwierzytelniania wieloskładnikowego przy przypisaniu

W niektórych przypadkach administrator zasobów chcieć przypisać członka do roli przez krótki czas (jeden dzień, na przykład). W tym przypadku nie muszą przypisane elementy członkowskie żądanie aktywacji. W tym scenariuszu usługi PIM nie może wymusić uwierzytelnianie wieloskładnikowe, gdy element członkowski używa przypisania roli, ponieważ są one już aktywny w roli od chwili, które są przypisane.

Aby upewnij się, że administrator zasobów spełniających przypisania kto mówią, że są one, można wymusić uwierzytelnianie wieloskładnikowe przy przypisaniu.

Z tego samego ekranu szczegóły ustawienia roli, pole wyboru, aby **Wymagaj uwierzytelniania wieloskładnikowego na bezpośrednie przypisanie**.

![Wymagaj uwierzytelniania wieloskładnikowego na przypisania bezpośredniego](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Kolejne kroki

[Wymagaj zatwierdzenia do aktywowania](pim-resource-roles-approval-workflow.md)

[Korzystanie z dziennika inspekcji](pim-resource-roles-use-the-audit-log.md)



