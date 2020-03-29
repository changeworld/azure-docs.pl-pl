---
title: Przypisywanie ról usługi Azure AD w usłudze PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak przypisać role usługi Azure AD w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253275"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Dzięki usłudze Azure Active Directory (Azure AD) administrator globalny może tworzyć **stałe** przypisania ról administratora usługi Azure AD. Te przypisania ról można tworzyć za pomocą [portalu Azure](../users-groups-roles/directory-assign-admin-roles.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa zarządzania tożsamościami uprzywilejowanymi usługą Azure AD (PIM) umożliwia również administratorom ról uprzywilejowanych dokonywanie stałych przypisań ról administratora. Ponadto administratorzy ról uprzywilejowanych mogą sprawić, że użytkownicy **będą kwalifikować się do** ról administratora usługi Azure AD. Uprawniony administrator może aktywować rolę, gdy jej potrzebuje, a następnie wygasają, gdy już skończą.

## <a name="determine-your-version-of-pim"></a>Określanie wersji programu PIM

Począwszy od listopada 2019 r. część ról usługi Azure AD usługi Zarządzanie tożsamościami uprzywilejowanymi jest aktualizowana do nowej wersji, która odpowiada środowiskom ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmiany w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, które procedury, które należy wykonać w tym artykule, zależą od wersji zarządzania tożsamościami uprzywilejowanymi, które obecnie masz. Wykonaj kroki opisane w tej sekcji, aby ustalić, którą wersję zarządzania tożsamościami uprzywilejowanymi masz. Po poznaniu wersji zarządzania tożsamościami uprzywilejowanymi można wybrać procedury w tym artykule, które są zgodne z tą wersją.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest w roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**. Jeśli u góry strony przeglądu masz baner, postępuj zgodnie z instrukcjami na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami na karcie **Poprzednia wersja.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby użytkownik kwalifikował się do roli administratora usługi Azure AD.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) z użytkownikiem, który jest członkiem roli [administratora ról uprzywilejowanych.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Aby uzyskać informacje dotyczące udzielania innym administratorom dostępu do zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami, zobacz [Udzielanie dostępu innym administratorom w celu zarządzania zarządzaniem uprzywilejowanym zarządzaniem tożsamościami](pim-how-to-give-access-to-pim.md).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Role,** aby wyświetlić listę ról uprawnień usługi Azure AD.

    ![Role usługi Azure AD](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Wybierz **pozycję Dodaj członka,** aby otworzyć stronę **Nowe przypisanie.**

1. Wybierz **pozycję Wybierz rolę,** aby otworzyć stronę Wybierz rolę.

    ![Nowe okienko przydziału](./media/pim-how-to-add-role-to-user/select-role.png)

1. Wybierz rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz**.

1. Wybierz członka, do którego chcesz przypisać do roli, a następnie wybierz pozycję **Wybierz**.

1. Na liście **Typ przydziału** w okienku **Ustawienia członkostwa** wybierz pozycję **Kwalifikujące się** lub **Aktywne**.

    - **Kwalifikujące się** przypisania wymagają od członka roli wykonania akcji w celu użycia tej roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają od członka wykonywania żadnej akcji w celu użycia tej roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli przypisanie powinno być stałe (trwale kwalifikujące się lub przypisane na stałe), zaznacz pole wyboru **Trwale.**

    W zależności od ustawień roli pole wyboru może nie być wyświetlane lub może być niemodyfikowalne.

1. Aby określić określony czas trwania przydziału, wyczyść pole wyboru i zmodyfikuj pola daty i godziny rozpoczęcia i/lub zakończenia. Po zakończeniu wybierz pozycję **Gotowe**.

    ![Ustawienia członkostwa - data i godzina](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Aby utworzyć nowe przypisanie roli, wybierz pozycję **Dodaj**. Zostanie wyświetlone powiadomienie o stanie.

    ![Nowe przypisanie — powiadomienie](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Role,** aby wyświetlić listę ról usługi Azure AD.

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **Kwalifikujące się role** lub **Aktywne role.**

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Wybierz **pozycję Aktualizuj** lub **Usuń,** aby zaktualizować lub usunąć przypisanie roli.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Powod uczynienie użytkownika kwalifikującym się do roli

Wykonaj następujące kroki, aby użytkownik kwalifikował się do roli administratora usługi Azure AD.

1. Wybierz **role** lub **członków**.

    ![Role usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Wybierz **pozycję Dodaj członka,** aby otworzyć **otwieranie opcji Dodaj zarządzanych członków**.

1. Wybierz **pozycję Wybierz rolę**, wybierz rolę, którą chcesz zarządzać, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Wybierz **pozycję Wybierz członków**, wybierz użytkowników, których chcesz przypisać do roli, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W **obszarze Dodaj zarządzanych członków**wybierz przycisk **OK,** aby dodać użytkownika do roli.

1. Na liście ról wybierz przypisaną właśnie rolę, aby wyświetlić listę członków.

     Po przypisaniu roli wybrany użytkownik pojawi się na liście członków jako **kwalifikujący się do** roli.

    ![Użytkownik kwalifikujący się do roli](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik kwalifikuje się do roli, poinformuj go, że może ją aktywować zgodnie z instrukcjami w [obszarze Aktywuj moje role usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi.](pim-how-to-activate-role.md)

    Uprawnieni administratorzy są proszeni o zarejestrowanie się w celu korzystania z usługi Azure Multi-Factor Authentication (MFA) podczas aktywacji. Jeśli użytkownik nie może zarejestrować się w usłudze @outlook.comMFA lub używa konta Microsoft (na przykład), musisz uczynić go stałym we wszystkich swoich rolach.

## <a name="make-a-role-assignment-permanent"></a>Stałe przydzielenie roli

Domyślnie nowi użytkownicy *kwalifikują się* tylko do roli administratora usługi Azure AD. Wykonaj następujące kroki, jeśli chcesz, aby przypisanie roli było trwałe.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Lista członków](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz **rolę Kwalifikującą** się, którą chcesz na stałe wprowadzić.

1. Wybierz **pozycję Więcej,** a następnie wybierz pozycję **Zrób perm**.

    ![Stałe przypisywanie roli](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wymieniona jako **stała**.

    ![Lista członków ze stałą zmianą](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Można usunąć użytkowników z przypisań ról, ale upewnij się, że zawsze jest co najmniej jeden użytkownik, który jest stałym administratorem globalnym. Jeśli nie masz pewności, którzy użytkownicy nadal potrzebują swoich przypisań ról, możesz [rozpocząć przegląd dostępu do roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli administratora usługi Azure AD.

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Lista członków](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz przypisanie roli, które chcesz usunąć.

1. Wybierz **pozycję Więcej,** a następnie wybierz pozycję **Usuń**.

    ![Usuwanie roli](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. W wiadomości z prośbą o potwierdzenie wybierz pozycję **Tak**.

    ![Usuwanie roli](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostanie usunięte.

## <a name="authorization-error-when-assigning-roles"></a>Błąd autoryzacji podczas przypisywania ról

Jeśli niedawno włączono zarządzanie tożsamościami uprzywilejowanymi dla subskrypcji i podczas próby zakwalifikowania użytkownika do roli administratora usługi Azure AD, może to być spowodowane tym, że podmiot zabezpieczeń usługi MS-PIM nie ma jeszcze odpowiednich uprawnień. Podmiot usługi MS-PIM musi mieć rolę [Administratora dostępu użytkownika,](../../role-based-access-control/built-in-roles.md#user-access-administrator) aby przypisać role innym osobom. Zamiast czekać, aż do przypisania usługi MS-PIM roli Administrator dostępu użytkownika, można przypisać ją ręcznie.

Wykonaj następujące kroki, aby przypisać rolę Administratora dostępu użytkownika do jednostki usługi MS-PIM dla subskrypcji.

1. Zaloguj się do witryny Azure portal jako administrator globalny.

1. Wybierz **pozycję Wszystkie usługi,** a następnie **pozycję Subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**.

1. Wybierz **pozycję Przypisania ról,** aby wyświetlić bieżącą listę przypisań ról w zakresie subskrypcji.

   ![Blok kontroli dostępu (IAM) dla subskrypcji](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Sprawdź, czy podmiotowi usługi **MS-PIM** jest przypisana rola **Administratora dostępu użytkownika.**

1. Jeśli nie, wybierz pozycję **Dodaj przypisanie roli,** aby otworzyć okienko **Dodaj przypisanie roli.**

1. Z listy rozwijanej **Rola** wybierz rolę **Administratora dostępu użytkownika.**

1. Na liście **Wybierz** znajdź i wybierz jednostkę usługi **MS-PIM.**

   ![Okienko przypisywania ról — dodawanie uprawnień dla jednostki usługi MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Wybierz **pozycję Zapisz,** aby przypisać rolę.

   Po kilku chwilach podmiotu usługi MS-PIM jest przypisywana rola Administratora dostępu użytkownika w zakresie subskrypcji.

   ![Strona kontroli dostępu z przypisaniem roli administratora dostępu użytkownika dla jednostki usługi MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli administratora usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-change-default-settings.md)
- [Przypisywanie ról zasobów platformy Azure w zarządzania tożsamościami uprzywilejowanymi](pim-resource-roles-assign-roles.md)