---
title: Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować ustawienia roli zasobu platformy Azure w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d42c693fae6b538136d1e8c93094a0ea9e2077
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494871"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurowanie ustawień roli zasobów platformy Azure w usłudze PIM

Po skonfigurowaniu ustawienia roli zasobu platformy Azure, możesz zdefiniować ustawienia domyślne, które są stosowane do przypisania ról zasobów platformy Azure w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Poniższe procedury umożliwiają konfigurowanie przepływu pracy zatwierdzania, a następnie określ, kto może zatwierdzać lub odrzucać żądania.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia dla ról zasobów platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **zasobów platformy Azure**.

1. Kliknij zasób, który chcesz zarządzać, takich jak subskrypcję lub grupę zarządzania.

    ![Lista zasobów platformy Azure do zarządzania](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Kliknij przycisk **ustawień roli**.

    ![Ustawienia roli](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Kliknij rolę, którego ustawienia chcesz skonfigurować.

    ![Szczegóły ustawienia roli](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Kliknij przycisk **Edytuj** aby otworzyć okienko ustawienia roli.

    ![Edytowanie ustawień ról](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    W okienku ustawienia roli dla każdej roli istnieje kilka ustawień, które można skonfigurować.

## <a name="assignment-duration"></a>Czas trwania przypisania

Można wybierać spośród dwóch opcji czas trwania przypisania dla każdego typu przydziału (kwalifikujących się i aktywnych) podczas konfigurowania ustawień dla roli. Te opcje stają się domyślny maksymalny czas, gdy członek jest przypisany do roli w usłudze PIM.

Można wybrać jedną z tych **kwalifikujących się** opcje czas trwania przypisania:

| | |
| --- | --- |
| **Zezwalaj na przypisanie trwałego kwalifikowania się** | Administratorom zasobów można przypisywać trwałe kwalifikujące się członkostwa. |
| **Wygaśnięcie kwalifikującego się przypisania po** | Administratorom zasobów może wymagać, że wszystkie kwalifikujące się przypisania mają określoną datę początkową i końcową. |

Ponadto można wybrać jedną z tych **active** opcje czas trwania przypisania:

| | |
| --- | --- |
| **Zezwalaj na trwałe aktywne przypisanie** | Administratorom zasobów można przypisywać trwałe aktywne członkostwo. |
| **Wygaśnięcie aktywne przypisanie po** | Administratorom zasobów może wymagać, że wszystkie aktywne przypisania mają określoną datę początkową i końcową. |

> [!NOTE] 
> Wszystkie przydziały, które mają określonej daty zakończenia, można ją odnawiać każdorazowo administratorom zasobów. Ponadto członkowie mogą inicjować żądań samoobsługi, aby [rozszerzanie lub odnawianie przypisań ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

PIM udostępnia opcjonalne Wymuszanie usługi Azure Multi-Factor Authentication (MFA) dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisaniu

W niektórych przypadkach można przypisać członka do roli przez krótki czas (jeden dzień, na przykład). W tym przypadku nie muszą przypisanymi członkami żądanie aktywacji. W tym scenariuszu usługi PIM nie wymuszanie uwierzytelniania Wieloskładnikowego, gdy element członkowski używa przypisania roli, ponieważ są one już aktywny w roli od chwili, które są przypisane.

Aby upewnij się, że administrator zasobów spełniających przypisania kto mówią, że są one, można wymusić uwierzytelnianie wieloskładnikowe przy aktywnym przypisaniu, sprawdzając **Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisaniu** pole.

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj usługi Multi-Factor Authentication przy uaktywnianiu

Może wymagać uprawnionych członków roli do uruchamiania usługi MFA, zanim użytkownik może dokonać aktywacji. Ten proces zapewnia, że użytkownik, który żąda aktywacji jest kto mówią, że są one z pewnością uzasadnione. Wymuszanie ta opcja chroni zasoby o znaczeniu krytycznym w sytuacjach, gdy konto użytkownika może być zabezpieczenia mogły zostać naruszone.

Aby wymagać kwalifikującego się elementu członkowskiego uruchomić usługę MFA przed aktywacji, sprawdź **Wymagaj uwierzytelniania wieloskładnikowego aktywacji** pole.

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie wieloskładnikowe (MFA) i PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania uaktywniania

Użyj **maksymalny czas trwania aktywacji** suwak, aby ustawić maksymalny czas w godzinach, rola pozostaje aktywna, przed jego wygaśnięciem. Ta wartość może mieć wartość od 1 do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Może wymagać, że elementy członkowskie wprowadzić uzasadnienia przy aktywnym przypisaniu lub podczas aktywacji. Aby wymaga uzasadnienia, zapoznaj się z **Wymagaj uzasadnienia przy aktywnym przypisaniu** pole lub **Wymagaj uzasadnienia przy aktywacji** pole.

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia w celu aktywacji

Jeśli ma być wymagane zatwierdzenie, aby aktywować rolę, wykonaj następujące kroki.

1. Sprawdź **Wymagaj zatwierdzenia do aktywowania** pole wyboru.

1. Kliknij przycisk **wybierz osoby zatwierdzające** otworzyć wybierz panel lub grupę.

    ![Wybierz członka lub grupę](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wybierz co najmniej jeden element członkowski lub grupę, a następnie kliknij przycisk **wybierz**. Możesz dodać dowolną kombinację elementów członkowskich i grup. Należy wybrać co najmniej jedna osoba zatwierdzająca. Nie istnieją żadne osoby zatwierdzające domyślne.

    Wybrane opcje pojawią się na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkie Twoje ustawienia ról kliknij **aktualizacji** Aby zapisać zmiany.

## <a name="next-steps"></a>Kolejne kroki

- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
- [Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-configure-alerts.md)
