---
title: Przypisywanie ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przypisywać role usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c08f2dcf6df67401160775db60bf613d1c5241
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804465"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Przypisywanie ról usługi Azure AD w usłudze PIM

Za pomocą Azure Active Directory (Azure AD) administrator globalny może tworzyć **trwałe** przypisania roli administratora usługi Azure AD. Te przypisania ról można utworzyć za pomocą [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia także administratorom ról uprzywilejowanych Tworzenie trwałych przypisań roli administratora. Ponadto administratorzy ról uprzywilejowanych mogą **wprowadzać użytkowników do** ról administratorów usługi Azure AD. Uprawniony administrator może aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasną po zakończeniu.

## <a name="make-a-user-eligible-for-a-role"></a>Przyznaj użytkownikowi uprawnienia do roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli administratora usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Aby uzyskać informacje na temat udzielania innym administratorom dostępu do usługi PIM, zobacz [udzielanie dostępu innym administratorom w celu zarządzania usługą PIM](pim-how-to-give-access-to-pim.md).

1. Otwórz **Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie uruchomiono usługi PIM w Azure Portal, przejdź do pozycji [Rozpocznij korzystanie](pim-getting-started.md)z usługi PIM.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **role** lub **Członkowie**.

    ![Role usługi Azure AD z opcjami menu z rolami i członkami](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Kliknij przycisk **Dodaj członka** , aby otworzyć aplet Dodaj elementy zarządzane.

1. Kliknij pozycję **Wybierz rolę**, kliknij rolę, którą chcesz zarządzać, a następnie kliknij przycisk **Wybierz**.

    ![Wybieranie okienka roli z listą ról usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Kliknij pozycję **Wybierz członków**, wybierz użytkowników, którym chcesz przypisać rolę, a następnie kliknij przycisk **Wybierz**.

    ![Wybierz okienko członkowie, w którym można wybrać użytkownika](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W obszarze Dodawanie elementów członkowskich zarządzanych kliknij przycisk **OK** , aby dodać użytkownika do roli.

1. Na liście ról kliknij właśnie przypisaną rolę, aby wyświetlić listę członków.

     Po przypisaniu roli wybrany użytkownik zostanie wyświetlony na liście członków jako **uprawniony** do roli.

    ![Elementy członkowskie roli są wyświetlane wraz z ich stanem aktywacji](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik kwalifikuje się do roli, daj im znać, że mogą go aktywować zgodnie z instrukcjami w [sekcji aktywowanie ról usługi Azure AD w usłudze PIM](pim-how-to-activate-role.md).

    Uprawnieni Administratorzy są proszeni o zarejestrowanie się w usłudze Azure Multi-Factor Authentication (MFA) podczas aktywacji. Jeśli użytkownik nie może zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego lub korzysta z @outlook.comkonto Microsoft (zazwyczaj), należy je trwale we wszystkich rolach.

## <a name="make-a-role-assignment-permanent"></a>Tworzenie trwałego przypisania roli

Domyślnie nowi użytkownicy kwalifikują się tylko do roli administratora usługi Azure AD. Wykonaj te kroki, jeśli chcesz, aby przypisanie roli trwało.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Członkowie**.

    ![Role usługi Azure AD — Lista członków pokazująca stan roli i aktywacji](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij **kwalifikującą się** rolę, która ma być trwała.

1. Kliknij pozycję **więcej** , a następnie kliknij pozycję **Utwórz uprawnienie**.

    ![Okienko zawierające listę użytkowników uprawnionych do roli z otwartymi opcjami menu Więcej](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wyświetlana jako **stała**.

    ![Lista członków pokazująca stan roli i aktywacji, który jest teraz trwały](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Możesz usunąć użytkowników z przypisań ról, ale upewnij się, że zawsze istnieje co najmniej jeden użytkownik, który jest stałym administratorem globalnym. Jeśli nie masz pewności, którzy użytkownicy nadal potrzebują przypisań ról, możesz [rozpocząć przegląd dostępu dla tej roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli administratora usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Członkowie**.

    ![Role usługi Azure AD — Lista członków pokazująca rolę i aktywację](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Kliknij przypisanie roli, które chcesz usunąć.

1. Kliknij przycisk **więcej** , a następnie kliknij przycisk **Usuń**.

    ![W okienku zostanie wyświetlona lista użytkowników, którzy mają stałą rolę z otwartymi opcjami menu Więcej](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. W komunikacie z prośbą o potwierdzenie kliknij przycisk **tak**.

    ![Komunikat z pytaniem, czy chcesz usunąć członka z roli](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostanie usunięte.

## <a name="authorization-error-when-assigning-roles"></a>Błąd autoryzacji podczas przypisywania ról

Jeśli ostatnio włączono usługę PIM dla subskrypcji i podczas próby skorzystania z roli administratora usługi Azure AD wystąpi błąd autoryzacji, może to być spowodowane tym, że zasada usługi MS-PIM nie ma jeszcze odpowiednich uprawnień. Zasady usługi MS-PIM muszą mieć rolę [administrator dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) do przypisywania ról innym osobom. Zamiast czekać, aż do MS-PIM zostanie przypisana rola administratora dostępu użytkowników, można przypisać ją ręcznie.

Wykonaj następujące kroki, aby przypisać rolę administratora dostępu użytkownika do nazwy głównej usługi MS-PIM dla subskrypcji.

1. Zaloguj się do Azure Portal jako Administrator globalny.

1. Wybierz pozycję **wszystkie usługi** , a następnie pozycję **subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)** .

1. Wybierz **przypisania ról** , aby wyświetlić bieżącą listę przypisań ról w zakresie subskrypcji.

   ![Blok kontroli dostępu (IAM) dla subskrypcji](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Sprawdź, czy nazwa główna usługi **MS-PIM** jest przypisana do roli **administratora dostępu użytkownika** .

1. W przeciwnym razie wybierz pozycję **Dodaj przypisanie roli** , aby otworzyć okienko **Dodaj przypisanie roli** .

1. Z listy rozwijanej **rola** wybierz rolę **administrator dostępu użytkowników** .

1. Z listy **Wybierz** Znajdź i wybierz nazwę główną usługi **MS-PIM** .

   ![Dodawanie okienka przypisania roli — Dodawanie uprawnień dla nazwy głównej usługi MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Wybierz pozycję **Zapisz** , aby przypisać rolę.

   Po kilku chwilach nazwa główna usługi MS-PIM jest przypisana do roli administratora dostępu użytkownika w zakresie subskrypcji.

   ![Blok kontroli dostępu (IAM) przedstawiający przypisanie roli administratora dostępu użytkownika do MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli administratora usługi Azure AD w usłudze PIM](pim-how-to-change-default-settings.md)
- [Przypisywanie ról zasobów platformy Azure w usłudze PIM](pim-resource-roles-assign-roles.md)
