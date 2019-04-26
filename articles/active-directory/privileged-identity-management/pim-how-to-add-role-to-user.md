---
title: Przypisz role usługi Azure AD w usłudze PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać role usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07259d90c7119dec4ca9139e10af2fb20a439425
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289471"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Przypisz role usługi Azure AD w usłudze PIM

Za pomocą usługi Azure Active Directory (Azure AD), ułatwia administratora globalnego **stałe** przypisania roli administratora usługi Azure AD. Te przypisania roli mogą być tworzone za pomocą [witryny Azure portal](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia również utwórz administratora trwałego przypisania ról uprzywilejowanych administratorów ról. Ponadto administratorzy ról uprzywilejowanych uczynić użytkowników **kwalifikujących się** dla ról administratora w usłudze Azure AD. Administrator uprawniony można aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasają po one gotowe.

## <a name="make-a-user-eligible-for-a-role"></a>Ustaw użytkownika z uprawnieniami dla roli

Wykonaj następujące kroki, aby utworzyć użytkownika kwalifikuje się do roli administratora usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest elementem członkowskim [Administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roli.

    Aby dowiedzieć się, jak udzielić innym dostęp administratora umożliwiający zarządzanie usługą PIM, zobacz [udzielić dostępu do innych administratorów do zarządzania usługą PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie rozpoczęto jeszcze PIM w witrynie Azure portal, przejdź do strony [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **role** lub **członków**.

    ![Role usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknij przycisk **Dodawanie elementu członkowskiego** aby otworzyć aplet Dodaj zarządzanych elementów członkowskich.

1. Kliknij przycisk **wybierz rolę**, kliknij rolę, którą chcesz zarządzać, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kliknij przycisk **Wybieranie elementów członkowskich**, wybierz użytkowników, o których chcesz przypisać do roli, a następnie kliknij przycisk **wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W oknie Dodaj zarządzanych elementów członkowskich, kliknij przycisk **OK** dodać użytkownika do roli.

1. Na liście ról kliknij rolę, którą przypisano do wyświetlania listy elementów członkowskich.

     Po przypisaniu roli użytkownika wybrania pojawi się na liście elementów członkowskich jako **kwalifikujących się** dla roli.

    ![Kwalifikuje się do roli użytkownika](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik jest uprawnieni do roli, daj znać, czy użytkownik może dokonać aktywacji go zgodnie z instrukcjami w [Uaktywnij Moje role usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md).

    Administratorzy uprawnieni są proszeni o rejestracji dla usługi Azure Multi-Factor Authentication (MFA) podczas aktywacji. Użytkownik nie może zarejestrować na potrzeby uwierzytelniania Wieloskładnikowego, czy jest używane konto Microsoft (zazwyczaj @outlook.com), należy wprowadzić je stałe w ich ról.

## <a name="make-a-role-assignment-permanent"></a>Były trwałe przypisania roli

Domyślnie nowi użytkownicy tylko kwalifikują się do roli administratora usługi Azure AD. Wykonaj następujące kroki, aby były trwałe przypisania roli.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **członków**.

    ![Lista elementów członkowskich](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij przycisk **kwalifikujących się** rolę, która ma być trwały.

1. Kliknij przycisk **więcej** a następnie kliknij przycisk **jako trwałą upewnij**.

    ![Były trwałe przypisania roli](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wyświetlany jako **stałe**.

    ![Lista elementów członkowskich z trwałych zmian](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Możesz usunąć użytkowników z przypisaniami ról, ale upewnij się, że zawsze jest co najmniej jednego użytkownika, który jest trwały administratora globalnego. Jeśli nie masz pewności, które użytkownicy nadal potrzebują swoje przypisania roli, możesz to zrobić [Rozpoczynanie przeglądu dostępu dla roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli administratora usługi Azure AD.

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **członków**.

    ![Lista elementów członkowskich](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij pozycję przypisania roli, którą chcesz usunąć.

1. Kliknij przycisk **więcej** a następnie kliknij przycisk **Usuń**.

    ![Usuń rolę](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Komunikat z prośbą o potwierdzenie kliknij **tak**.

    ![Usuń rolę](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostaną usunięte.

## <a name="authorization-error-when-assigning-roles"></a>Błąd autoryzacji podczas przypisywania ról

Jeśli wystąpi błąd autoryzacji, podczas próby nawiązania użytkownika kwalifikuje się do roli administratora usługi Azure AD PIM ostatnio włączony dla subskrypcji, może to być, ponieważ jednostki usługi MS PIM jeszcze nie ma odpowiednich uprawnień. Jednostki usługi MS PIM musi mieć [Administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) roli, aby przypisać role do innych użytkowników. Zamiast czekać, aż usługi PIM MS przypisany do roli Administrator dostępu użytkowników, można przypisać go ręcznie.

Wykonaj następujące kroki, aby przypisać rolę administratora dostępu użytkowników do jednostki usługi MS PIM w przypadku subskrypcji.

1. Zaloguj się do witryny Azure portal jako Administrator globalny.

1. Wybierz **wszystkich usług** i następnie **subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**.

1. Wybierz **przypisań ról** Aby wyświetlić bieżącą listę przypisań ról w zakresie subskrypcji.

   ![W bloku kontrola (IAM) dostępu do subskrypcji](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Sprawdź, czy **MS PIM** nazwy głównej usługi przypisano **Administrator dostępu użytkowników** roli.

1. Jeśli nie, wybierz **Dodaj przypisanie roli** otworzyć **Dodaj przypisanie roli** okienka.

1. W **roli** listy rozwijanej wybierz **Administrator dostępu użytkowników** roli.

1. W **wybierz** listy, Znajdź i wybierz **MS PIM** nazwy głównej usługi.

   ![Dodawanie uprawnień dla aplikacji PIM MS](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Wybierz **Zapisz** Aby przypisać rolę.

   Po kilku chwilach jednostki usługi MS PIM przypisany do roli Administrator dostępu użytkowników w zakresie subskrypcji.

   ![Rola Administrator dostępu użytkowników dla aplikacji PIM MS](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie ustawień roli administratora usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Przypisz role zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
