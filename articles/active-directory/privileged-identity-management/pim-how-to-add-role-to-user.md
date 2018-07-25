---
title: Przypisywanie ról katalogu użytkownikom za pomocą usługi Azure AD PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisywać ról katalogu użytkownikom za pomocą usługi Azure Active Directory Privileged Identity Management, jak i portalu Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1834addb4e51030afda43a2d7acad5d7ffc1889a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226700"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>Przypisywanie ról katalogu użytkownikom za pomocą usługi Azure AD PIM

Za pomocą usługi Azure Active Directory (Azure AD), ułatwia administratora globalnego **stałe** przypisań ról w katalogu. Te przypisania roli mogą być tworzone za pomocą [witryny Azure portal](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia również Administratorzy ról uprzywilejowanych zapewnienie katalogu trwałe przypisania ról. Ponadto administratorzy ról uprzywilejowanych uczynić użytkowników **kwalifikujących się** dla ról w katalogu. Administrator uprawniony można aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasają po one gotowe. Aby uzyskać informacje o rolach, którymi można zarządzać za pomocą usługi PIM, zobacz [ról w katalogu można zarządzać za pomocą usługi Azure AD PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Ustaw użytkownika z uprawnieniami dla roli

Wykonaj następujące kroki, aby utworzyć użytkownika kwalifikuje się do roli katalogu usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

    Aby dowiedzieć się, jak udzielić innej dostęp użytkownika do zarządzanie usługą PIM, zobacz [udzielanie dostępu do usługi PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

    Jeśli nie zostały jeszcze włączone PIM w witrynie Azure portal, przejdź do strony [Rozpoczynanie pracy z usługą Azure AD PIM](pim-getting-started.md).

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **roli (wersja zapoznawcza)** lub **członków**.

    ![Role w katalogach usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknij przycisk **Dodawanie elementu członkowskiego** aby otworzyć aplet Dodaj zarządzanych elementów członkowskich.

1. Kliknij przycisk **wybierz rolę**, kliknij rolę, którą chcesz zarządzać, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kliknij przycisk **Wybieranie elementów członkowskich**, wybierz użytkowników, o których chcesz przypisać do roli, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W oknie Dodaj zarządzanych elementów członkowskich, kliknij przycisk **OK** dodać użytkownika do roli.

     Po przypisaniu roli użytkownika wybrania pojawi się na liście elementów członkowskich jako **kwalifikujących się** dla roli.

    ![Kwalifikuje się do roli użytkownika](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik jest uprawnieni do roli, daj znać, czy użytkownik może dokonać aktywacji go zgodnie z instrukcjami w [jak aktywować lub dezaktywować rolę](pim-how-to-activate-role.md).

    Administratorzy uprawnieni są proszeni o rejestracji dla usługi Azure Multi-Factor Authentication (MFA) podczas aktywacji. Użytkownik nie może zarejestrować na potrzeby uwierzytelniania Wieloskładnikowego, czy jest używane konto Microsoft (zazwyczaj @outlook.com), należy wprowadzić je stałe w ich ról.

## <a name="make-a-role-assignment-permanent"></a>Były trwałe przypisania roli

Domyślnie nowym użytkownikom przysługuje tylko rolę katalogu. Wykonaj następujące kroki, aby były trwałe przypisania roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **członków**.

    ![Lista elementów członkowskich](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij przycisk **kwalifikujących się** rolę, która ma być trwały.

1. Kliknij przycisk **więcej** a następnie kliknij przycisk **jako trwałą upewnij**.

    ![Były trwałe przypisania roli](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wyświetlany jako **stałe**.

    ![Lista elementów członkowskich z trwałych zmian](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Możesz usunąć użytkowników z przypisaniami ról, ale upewnij się, że zawsze jest co najmniej jednego użytkownika, który jest trwały administratora globalnego. Jeśli nie masz pewności, które użytkownicy nadal potrzebują swoje przypisania roli, możesz to zrobić [Rozpoczynanie przeglądu dostępu dla roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z rolą katalogu.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **członków**.

    ![Lista elementów członkowskich](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij pozycję przypisania roli, którą chcesz usunąć.

1. Kliknij przycisk **więcej** a następnie kliknij przycisk **Usuń**.

    ![Usuń rolę](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Komunikat z prośbą o potwierdzenie kliknij **tak**.

    ![Usuń rolę](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostaną usunięte.

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
