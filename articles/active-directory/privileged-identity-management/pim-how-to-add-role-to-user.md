---
title: Przypisywanie ról katalogu usługi Azure AD w usłudze PIM | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisywać ról katalogu usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 33bfe28bf612c47c9f42345dabccc017337c3d45
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190160"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>Przypisywanie ról katalogu usługi Azure AD w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD), ułatwia administratora globalnego **stałe** przypisań ról w katalogu. Te przypisania roli mogą być tworzone za pomocą [witryny Azure portal](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia również Administratorzy ról uprzywilejowanych zapewnienie katalogu trwałe przypisania ról. Ponadto administratorzy ról uprzywilejowanych uczynić użytkowników **kwalifikujących się** dla ról w katalogu. Administrator uprawniony można aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasają po one gotowe. Aby uzyskać informacje o rolach, którymi można zarządzać za pomocą usługi PIM, zobacz [ról katalogu usługi Azure AD, można zarządzać w usłudze PIM](pim-roles.md).

## <a name="make-a-user-eligible-for-a-role"></a>Ustaw użytkownika z uprawnieniami dla roli

Wykonaj następujące kroki, aby utworzyć użytkownika kwalifikuje się do roli katalogu usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

    Aby dowiedzieć się, jak udzielić innym dostęp administratora umożliwiający zarządzanie usługą PIM, zobacz [udzielić dostępu do innych administratorów do zarządzania usługą PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie rozpoczęto jeszcze PIM w witrynie Azure portal, przejdź do strony [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

1. Kliknij przycisk **ról katalogu usługi Azure AD**.

1. Kliknij przycisk **role** lub **członków**.

    ![Role w katalogach usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknij przycisk **Dodawanie elementu członkowskiego** aby otworzyć aplet Dodaj zarządzanych elementów członkowskich.

1. Kliknij przycisk **wybierz rolę**, kliknij rolę, którą chcesz zarządzać, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kliknij przycisk **Wybieranie elementów członkowskich**, wybierz użytkowników, o których chcesz przypisać do roli, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W oknie Dodaj zarządzanych elementów członkowskich, kliknij przycisk **OK** dodać użytkownika do roli.

1. Na liście ról kliknij rolę, którą przypisano do wyświetlania listy elementów członkowskich.

     Po przypisaniu roli użytkownika wybrania pojawi się na liście elementów członkowskich jako **kwalifikujących się** dla roli.

    ![Kwalifikuje się do roli użytkownika](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik jest uprawnieni do roli, daj znać, czy użytkownik może dokonać aktywacji go zgodnie z instrukcjami w [Uaktywnij Moje role katalogu usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md).

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

- [Konfigurowanie ustawień roli w katalogu usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
