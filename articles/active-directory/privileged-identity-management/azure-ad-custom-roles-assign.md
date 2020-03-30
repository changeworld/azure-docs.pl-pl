---
title: Przypisywanie roli niestandardowej usługi Azure AD — zarządzanie tożsamościami uprzywilejowanymi (PIM)
description: Jak przypisać rolę niestandardową usługi Azure AD do zarządzania tożsamościami uprzywilejowanymi (PRZYDZIAŁ)
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
ms.openlocfilehash: 0303d37ef5bbbf266feb5456b0bc224ce272ee13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499246"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Przypisywanie roli niestandardowej usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

W tym artykule opisano, jak używać zarządzania tożsamościami uprzywilejowanymi (PIM) do tworzenia przypisania tylko w czasie i czasie do ról niestandardowych utworzonych do zarządzania aplikacjami w usłudze Azure Active Directory (Azure AD) środowiska administracyjnego.

- Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych do delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Role administratora niestandardowego w usłudze Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md).
- Jeśli nie używano jeszcze zarządzania tożsamościami uprzywilejowanymi, uzyskaj więcej informacji na [temat Rozpocznij korzystanie z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).
- Aby uzyskać informacje dotyczące udzielania innym administratorom dostępu do zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami, zobacz [Udzielanie dostępu innym administratorom w celu zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogów podczas podglądu. Gdy możliwości są ogólnie dostępne, zarządzanie rolami odbędzie się w doświadczeniu wbudowanych ról. Jeśli widzisz następujący baner, te role powinny być zarządzane [w doświadczeniu wbudowanych ról](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Przypisywanie roli

Zarządzanie tożsamościami uprzywilejowanymi może zarządzać rolami niestandardowymi, które można utworzyć w zarządzaniu aplikacjami usługi Azure Active Directory (Azure AD).  Poniższe kroki sprawiają, że kwalifikujące się przypisanie do niestandardowej roli katalogu.

1. Zaloguj się do [zarządzania tożsamościami uprzywilejowanymi](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w witrynie Azure portal przy za pomocą konta użytkownika przypisanego do roli administratora ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (Wersja zapoznawcza)**.

    ![Wybieranie w wersji zapoznawczej ról niestandardowych usługi Azure AD, aby wyświetlić kwalifikujące się przydziały ról](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Wybierz **pozycję Role,** aby wyświetlić listę ról niestandardowych dla aplikacji usługi Azure AD.

    ![Wybierz role zobacz listę kwalifikujących się przypisań ról](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Wybierz **pozycję Dodaj członka,** aby otworzyć stronę przydziału.
1. Aby ograniczyć zakres przypisania roli do pojedynczej aplikacji, wybierz **zakres,** aby określić zakres aplikacji.

    ![ograniczanie zakresu kwalifikujących się przypisań ról w usłudze Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Wybierz **pozycję Wybierz rolę,** aby otworzyć listę **Wybierz rolę.**

    ![wybierz kwalifikującą się rolę do przypisania użytkownikowi](./media/azure-ad-custom-roles-assign/select-role.png)

1. Wybierz rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz**. Zostanie otwarta lista **Wybierz członka.**

    ![wybierz użytkownika, któremu przypisujesz rolę](./media/azure-ad-custom-roles-assign/select-member.png)

1. Wybierz użytkownika, którego chcesz przypisać do roli, a następnie kliknij przycisk **Wybierz**. Zostanie otwarta lista **Ustawień członkostwa.**

    ![Ustawianie typu przypisania roli na kwalifikujący się lub aktywny](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na stronie **Ustawienia członkostwa** wybierz pozycję **Kwalifikujące się** lub **Aktywne:**

    - **Kwalifikujące się** przypisania wymagają od użytkownika przypisanego do roli wykonania akcji, zanim będzie mógł użyć tej roli. Akcje mogą obejmować przekazywanie sprawdzania uwierzytelniania wieloskładnikowego, dostarczanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.
    - **Aktywne** przypisania nie wymagają od przypisanego użytkownika wykonywania żadnej akcji w celu użycia tej roli. Aktywni użytkownicy mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli pole wyboru **Stałe** jest obecne i dostępne (w zależności od ustawień roli), można określić, czy przypisanie jest trwałe. Zaznacz to pole wyboru, aby przydział był trwale kwalifikowalne lub trwale przypisane. Wyczyść to pole wyboru, aby określić czas trwania przydziału.
1. Aby utworzyć nowe przypisanie roli, kliknij przycisk **Zapisz,** a następnie **dodaj**. Zostanie wyświetlone powiadomienie o stanie procesu przypisania.

Aby zweryfikować przypisanie roli, w otwartej roli wybierz pozycję **Przydział przypisz** > **Assign** i sprawdź, czy przypisanie roli jest poprawnie zidentyfikowane jako kwalifikujące się lub aktywne.

 ![Sprawdź, czy przypisanie roli jest widoczne jako kwalifikujące się lub aktywne](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Następne kroki

- [Aktywowanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuwanie lub aktualizowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurowanie przypisania niestandardowego zadania usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
