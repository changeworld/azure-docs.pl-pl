---
title: Konfigurowanie roli niestandardowej usługi Azure AD — zarządzanie tożsamościami uprzywilejowanymi (PIM)
description: Jak skonfigurować role niestandardowe usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f087ffe824a198c578f076857cca7fdf0f0a60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77498680"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurowanie ról niestandardowych usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Administrator ról uprzywilejowanych może zmienić ustawienia roli, które mają zastosowanie do użytkownika podczas aktywowania przypisania do roli niestandardowej i dla innych administratorów aplikacji, którzy przypisują role niestandardowe.

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogów podczas podglądu. Gdy możliwości są ogólnie dostępne, zarządzanie rolami odbędzie się w doświadczeniu wbudowanych ról. Jeśli widzisz następujący baner, te role powinny być zarządzane [w doświadczeniu wbudowanych ról](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Zaloguj się do [zarządzania tożsamościami uprzywilejowanymi](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w witrynie Azure portal przy za pomocą konta użytkownika przypisanego do roli administratora ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (Wersja zapoznawcza)**.

    ![Wybieranie w wersji zapoznawczej ról niestandardowych usługi Azure AD, aby wyświetlić kwalifikujące się przydziały ról](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Wybierz **pozycję Ustawienie,** aby otworzyć stronę **Ustawienia.** Wybierz rolę dla ustawień, które chcesz skonfigurować.
1. Wybierz **pozycję Edytuj,** aby otworzyć stronę **Ustawienia roli.**

    ![Otwieranie roli niestandardowej usługi Azure AD w celu edytowania ustawień](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Ustawienia roli

Istnieje kilka ustawień, które można skonfigurować.

### <a name="assignment-duration"></a>Czas trwania przydziału

Podczas konfigurowania ustawień roli można wybrać jedną z dwóch opcji czasu trwania przydziału dla każdego typu przydziału (kwalifikującego się lub aktywnego). Te opcje stają się domyślnym maksymalnym czasem trwania, gdy członek jest przypisany do roli w zarządzanie tożsamością uprzywilejowaną.

Możesz wybrać jedną z tych *kwalifikujących się* opcji czasu trwania przydziału.

- **Zezwalaj na stałe kwalifikujące się przydziały:** Administratorzy mogą przypisać stałe kwalifikujące się członkostwo.
- **Wygaśnie kwalifikujące się przypisanie po**: Administratorzy mogą wymagać, aby wszystkie kwalifikujące się przydziały miały określoną datę rozpoczęcia i zakończenia.

Można również wybrać jedną z tych *aktywnych* opcji czasu trwania przydziału:

- **Zezwalaj na stałe aktywne przypisanie:** Administratorzy mogą przypisać stałe aktywne członkostwo.
- **Wygaśnie aktywne przypisanie po**: Administratorzy mogą wymagać, aby wszystkie aktywne przydziały miały określoną datę rozpoczęcia i zakończenia.

### <a name="require-azure-multi-factor-authentication"></a>Wymaganie usługi Azure Multi-Factor Authentication

Zarządzanie tożsamościami uprzywilejowanymi zapewnia opcjonalne wymuszanie uwierzytelniania wieloskładnikowego platformy Azure dla dwóch różnych scenariuszy.

- **Wymagaj uwierzytelniania wieloskładnikowego przy aktywnym przypisaniu**

  Jeśli chcesz przypisać członka do roli tylko przez krótki czas (na przykład jeden dzień), może być zbyt wolno, aby wymagać od przypisanych członków żądania aktywacji. W tym scenariuszu zarządzanie tożsamościami uprzywilejowanymi nie można wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik aktywuje przypisanie roli, ponieważ są już aktywne w roli od momentu ich przypisania. Aby upewnić się, że administrator wypełniający przypisanie jest tym, kim się znajduje, zaznacz pole **Wymagaj uwierzytelniania wieloskładnikowego w aktywnym przypisaniu.**

- **Wymagaj uwierzytelniania wieloskładnikowego podczas aktywacji**

  Możesz wymagać kwalifikujących się użytkowników przypisanych do roli, aby zarejestrować się w usłudze Azure Multi-Factor Authentication, zanim będą mogli aktywować. Ten proces gwarantuje, że użytkownik, który żąda aktywacji jest tym, który mówi, że są z rozsądną pewnością. Wymuszanie tej opcji chroni role krytyczne w sytuacjach, gdy konto użytkownika mogło zostać naruszone. Aby wymagać od uprawnionego członka uruchomienia uwierzytelniania wieloskładnikowego platformy Azure przed aktywacją, wybierz pole **Wymagaj uwierzytelniania wieloskładnikowego w polu aktywacji.**

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie wieloskładnikowe i Zarządzanie tożsamościami uprzywilejowanymi](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Użyj **suwaka Maksymalny czas trwania aktywacji,** aby ustawić maksymalny czas w godzinach, przez który rola pozostaje aktywna przed jej wygaśnięciem. Ta wartość może pochodzić z, 1 i 24 godzin.

### <a name="require-justification"></a>Wymagaj uzasadnienia

Można wymagać, aby członkowie wprowadzać uzasadnienie w aktywnym przypisywaniu lub po ich aktywacji. Aby wymagać uzasadnienia, zaznacz pole wyboru **Wymagaj justowania na aktywnym przypisaniu** lub **Pole Wymagaj justowania w aktywacji.**

### <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia, aby aktywować

Jeśli chcesz wymagać zatwierdzenia, aby aktywować rolę, wykonaj następujące kroki.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia, aby aktywować.**
1. Wybierz **pozycję Wybierz osoby zatwierdzające,** aby otworzyć listę Wybierz członka lub **grupę.**

    ![Otwieranie roli niestandardowej usługi Azure AD w celu edytowania ustawień](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Zaznacz co najmniej jednego członka lub grupę, a następnie kliknij przycisk **Zaznacz**. Musisz wybrać co najmniej jedną osobę zatwierdzającej. Nie ma żadnych domyślnych osób zatwierdzających. Wybrane osoby zatwierdzające pojawią się na liście wybranych osób zatwierdzających.
1. Po określeniu ustawień roli wybierz pozycję **Aktualizuj,** aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

- [Aktywowanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-activate.md)
- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuwanie lub aktualizowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
