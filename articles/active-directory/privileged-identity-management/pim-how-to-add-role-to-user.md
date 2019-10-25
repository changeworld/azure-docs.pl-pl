---
title: Przypisywanie ról usługi Azure AD w Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przypisywać role usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809199"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Przypisywanie ról usługi Azure AD w Privileged Identity Management

Za pomocą Azure Active Directory (Azure AD) administrator globalny może tworzyć **trwałe** przypisania roli administratora usługi Azure AD. Te przypisania ról można utworzyć za pomocą [Azure Portal](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia także administratorom ról uprzywilejowanych Tworzenie trwałych przypisań roli administratora. Ponadto administratorzy ról uprzywilejowanych mogą **wprowadzać użytkowników do** ról administratorów usługi Azure AD. Uprawniony administrator może aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasną po zakończeniu.

## <a name="make-a-user-eligible-for-a-role"></a>Przyznaj użytkownikowi uprawnienia do roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli administratora usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Aby uzyskać informacje na temat udzielania innym administratorom dostępu do zarządzania Privileged Identity Management, zobacz [udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otwórz **Azure AD Privileged Identity Management**.

    Jeśli jeszcze nie uruchomiono Privileged Identity Management w Azure Portal, wybierz pozycję [Rozpocznij korzystanie z Privileged Identity Management](pim-getting-started.md).

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz **role** lub **członków**.

    ![Role usługi Azure AD z opcjami menu z rolami i członkami](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Wybierz pozycję **Dodaj członka** , aby otworzyć aplet Dodaj elementy zarządzane.

1. Wybierz pozycję **Wybierz rolę**, wybierz rolę, którą chcesz zarządzać, a następnie wybierz pozycję **Wybierz**.

    ![Wybieranie okienka roli z listą ról usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Wybierz pozycję **Wybierz członków**, wybierz użytkowników, którym chcesz przypisać rolę, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz okienko członkowie, w którym można wybrać użytkownika](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W obszarze Dodaj zarządzane elementy członkowskie wybierz **przycisk OK** , aby dodać użytkownika do roli.

1. Na liście ról wybierz właśnie przypisaną rolę, aby wyświetlić listę członków.

     Po przypisaniu roli wybrany użytkownik zostanie wyświetlony na liście członków jako **uprawniony** do roli.

    ![Elementy członkowskie roli są wyświetlane wraz z ich stanem aktywacji](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik kwalifikuje się do roli, daj im znać, że mogą go aktywować zgodnie z instrukcjami w temacie [Aktywowanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-activate-role.md).

    Uprawnieni Administratorzy są proszeni o zarejestrowanie się w usłudze Azure Multi-Factor Authentication (MFA) podczas aktywacji. Jeśli użytkownik nie może zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego lub używa konto Microsoft (zazwyczaj @outlook.com), należy wprowadzić je jako trwałe we wszystkich rolach.

## <a name="make-a-role-assignment-permanent"></a>Tworzenie trwałego przypisania roli

Domyślnie nowi użytkownicy kwalifikują się tylko do roli administratora usługi Azure AD. Wykonaj te kroki, jeśli chcesz, aby przypisanie roli trwało.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Role usługi Azure AD — Lista członków pokazująca stan roli i aktywacji](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz **kwalifikującą się** rolę, która ma być trwała.

1. Wybierz pozycję **więcej** , a następnie wybierz pozycję **Utwórz uprawnienie**.

    ![Okienko zawierające listę użytkowników uprawnionych do roli z otwartymi opcjami menu Więcej](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wyświetlana jako **stała**.

    ![Lista członków pokazująca stan roli i aktywacji, który jest teraz trwały](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Możesz usunąć użytkowników z przypisań ról, ale upewnij się, że zawsze istnieje co najmniej jeden użytkownik, który jest stałym administratorem globalnym. Jeśli nie masz pewności, którzy użytkownicy nadal potrzebują przypisań ról, możesz [rozpocząć przegląd dostępu dla tej roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli administratora usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Role usługi Azure AD — Lista członków pokazująca rolę i aktywację](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz przypisanie roli, które chcesz usunąć.

1. Wybierz pozycję **więcej** , a następnie **Usuń**.

    ![W okienku zostanie wyświetlona lista użytkowników, którzy mają stałą rolę z otwartymi opcjami menu Więcej](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Gdy zostanie wyświetlony monit o potwierdzenie akcji, wybierz pozycję **tak**.

    ![Komunikat z pytaniem, czy chcesz usunąć członka z roli](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostanie usunięte.

## <a name="authorization-error-when-assigning-roles"></a>Błąd autoryzacji podczas przypisywania ról

Scenariusz: jako aktywny właściciel lub administrator dostępu użytkowników dla zasobu platformy Azure można zobaczyć zasób wewnątrz Privileged Identity Management ale nie może wykonać żadnych akcji, takich jak przypisanie kwalifikującego się przypisania lub wyświetlenie listy przypisań ról z Strona przeglądu zasobów. Każda z tych akcji powoduje błąd autoryzacji.

Aby przypisać role, nazwa główna usługi MS-PIM musi mieć przypisaną [rolę administratora dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) w kontroli dostępu opartej na rolach na potrzeby dostępu do zasobów platformy Azure (w przeciwieństwie do ról administracyjnych usługi Azure AD). Zamiast czekać, aż do MS-PIM zostanie przypisana rola administratora dostępu użytkowników, można przypisać ją ręcznie.

Poniższe kroki umożliwiają przypisanie roli administratora dostępu użytkownika do nazwy głównej usługi MS-PIM dla subskrypcji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny w organizacji usługi Azure AD.

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

- [Skonfiguruj ustawienia roli administratora usługi Azure AD w Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
